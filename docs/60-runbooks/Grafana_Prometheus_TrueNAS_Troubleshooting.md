# Troubleshooting: Grafana, Prometheus i TrueNAS

## Cel instrukcji

Ta instrukcja służy do ustalenia, dlaczego Grafana nie pokazuje danych z TrueNAS. Wykonuj kroki po kolei i nie przechodź dalej, dopóki nie sprawdzisz wyniku bieżącego testu.

Konfiguracja opisana w instrukcji:

- TrueNAS: `192.168.20.12`
- `node_exporter`: port `9100`
- Prometheus: `192.168.20.52:9090`
- Grafana pobiera dane z Prometheusa

Przepływ danych:

```text
TrueNAS -> node_exporter:9100 -> Prometheus:9090 -> Grafana
```

> Grafana nie pobiera metryk bezpośrednio z TrueNAS. Najpierw należy znaleźć pierwszy niedziałający element w powyższym łańcuchu.

## 1. Sprawdź target w Prometheusie

Otwórz:

```text
http://192.168.20.52:9090/targets
```

Znajdź target `truenas`.

### Jeżeli status to `UP`

Połączenie `TrueNAS -> exporter -> Prometheus` działa. Przejdź do kroku 10 i sprawdź dane w Prometheusie, a następnie Grafanę.

### Jeżeli status to `DOWN`

Zapisz dokładną treść pola `Last Error`, a następnie przejdź do kroku 2.

Najczęstsze komunikaty:

| Komunikat | Prawdopodobna przyczyna |
|---|---|
| `connection refused` | TrueNAS odpowiada, ale nic nie słucha na porcie 9100 |
| `context deadline exceeded` | firewall, routing, VLAN albo timeout |
| `no route to host` | błędny adres IP, routing lub VLAN |
| `404 Not Found` | niewłaściwy port albo ścieżka metryk |
| `server returned HTTP status 401/403` | uwierzytelnienie lub reguły dostępu |

## 2. Sprawdź, czy Prometheus widzi TrueNAS w sieci

Na serwerze Prometheusa wykonaj:

```bash
ping -c 4 192.168.20.12
```

### Jeżeli ping działa

Przejdź do kroku 3.

### Jeżeli ping nie działa

Sprawdź kolejno:

1. Czy TrueNAS jest uruchomiony i GUI otwiera się pod właściwym adresem.
2. Aktualny adres IP TrueNAS: `ip addr` albo ustawienia sieciowe w GUI.
3. Czy Prometheus i TrueNAS znajdują się we właściwych VLAN-ach.
4. Routing między VLAN-ami.
5. Reguły firewalla na routerze/pfSense i TrueNAS.
6. Maskę podsieci, bramę oraz stan interfejsu sieciowego.

Po naprawie powtórz `ping`, a następnie przejdź do kroku 3.

> Brak odpowiedzi na ping nie zawsze oznacza brak łączności, ponieważ ICMP może być blokowany. Dlatego nawet wtedy warto wykonać test portu z kroku 3.

## 3. Sprawdź port 9100 z serwera Prometheusa

```bash
nc -zv 192.168.20.12 9100
```

Następnie:

```bash
curl -v --max-time 10 http://192.168.20.12:9100/metrics
```

### Jeżeli połączenie działa i `curl` zwraca metryki

Powinny pojawić się między innymi linie `# HELP`, `# TYPE` i metryki zaczynające się od `node_`. Exporter działa i jest osiągalny. Przejdź do kroku 8.

### Jeżeli pojawia się `Connection refused`

TrueNAS jest osiągalny, ale na porcie `9100` nic nie nasłuchuje. Przejdź do kroku 4.

### Jeżeli pojawia się timeout

Sprawdź firewall, VLAN i routing. Możliwe jest również, że exporter słucha tylko na `127.0.0.1`. Przejdź do kroku 4 i sprawdź adres nasłuchiwania.

### Jeżeli `nc` działa, ale `/metrics` nie zwraca metryk

Sprawdź odpowiedź HTTP z `curl -v`:

- `404` — prawdopodobnie na porcie działa inna usługa lub ustawiono złą ścieżkę;
- `401/403` — dostęp wymaga autoryzacji albo blokuje go proxy;
- pusta lub zerwana odpowiedź — sprawdź logi exportera w kroku 6;
- HTML zamiast metryk — port prowadzi do niewłaściwej usługi.

## 4. Sprawdź port lokalnie na TrueNAS

W TrueNAS Shell lub przez SSH wykonaj:

```bash
sudo ss -ltnp | grep ':9100'
```

### Jeżeli widzisz `0.0.0.0:9100` albo `[::]:9100`

Usługa słucha na interfejsach sieciowych. Sprawdź ją lokalnie:

```bash
curl --max-time 10 http://localhost:9100/metrics
curl --max-time 10 http://192.168.20.12:9100/metrics
```

- oba działają — wróć do firewalla/routingu i testu z serwera Prometheusa;
- `localhost` działa, ale adres TrueNAS nie — sprawdź adres bind/nasłuchiwania i firewall;
- oba nie działają mimo widocznego portu — sprawdź proces i logi w krokach 5–6.

### Jeżeli widzisz tylko `127.0.0.1:9100`

Exporter jest dostępny wyłącznie lokalnie. Zmień jego konfigurację lub konfigurację kontenera tak, aby udostępniał port w sieci, a potem powtórz test.

### Jeżeli polecenie nic nie zwraca

Na porcie `9100` nic nie działa. Przejdź do kroku 5.

## 5. Sprawdź proces i kontener node_exporter

```bash
ps aux | grep -i '[n]ode_exporter'
```

Sprawdź Dockera:

```bash
docker --version
sudo docker ps -a
sudo docker ps -a | grep -i exporter
```

Jeżeli `docker ps` zwraca błąd dostępu do `/var/run/docker.sock`, użyj `sudo docker ps`. Nie zmieniaj pochopnie uprawnień socketu Dockera.

### Exporter ma status `Up`

Przejdź do kroku 6 i sprawdź logi oraz mapowanie sieci/portu.

### Exporter ma status `Exited` lub `Restarting`

Przejdź do kroku 6. Nie twórz drugiego kontenera przed poznaniem przyczyny zatrzymania starego.

### Exportera nie ma

Prometheus nie ma skąd pobrać metryk. Przejdź do kroku 7.

## 6. Exporter istnieje, ale nie działa prawidłowo

Najpierw ustal dokładną nazwę kontenera:

```bash
sudo docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}' | grep -i exporter
```

Następnie, podstawiając właściwą nazwę:

```bash
sudo docker logs --tail 200 NAZWA_KONTENERA
sudo docker inspect NAZWA_KONTENERA
```

Sprawdź:

- komunikaty `permission denied`;
- błędną opcję uruchomienia;
- konflikt portu `address already in use`;
- brak wymaganych montowań hosta;
- czy kontener używa sieci hosta albo publikuje port `9100`;
- czy ma politykę automatycznego restartu.

### Jeżeli kontener jest zatrzymany, a logi nie pokazują błędu konfiguracji

```bash
sudo docker start NAZWA_KONTENERA
sudo docker ps | grep -i exporter
sudo docker logs --tail 50 NAZWA_KONTENERA
```

Następnie wróć do kroku 4.

### Jeżeli jest konflikt portu

```bash
sudo ss -ltnp | grep ':9100'
```

Ustal, co zajmuje port. Nie zatrzymuj procesu, dopóki nie potwierdzisz, czym jest. Exporter może też zostać wystawiony na innym wolnym porcie, ale ten sam port trzeba wtedy wpisać w Prometheusie.

### Jeżeli kontener działa, lecz nie udostępnia portu

Popraw konfigurację aplikacji/kontenera: zastosuj `host network` albo opublikuj port `9100:9100`, zależnie od sposobu wdrożenia. Na TrueNAS docelowo zarządzaj usługą przez **Apps -> Custom App**, aby konfiguracja była trwała i kontrolowana przez TrueNAS.

## 7. Exportera nie ma — test i instalacja

Najbezpieczniej skonfigurować go docelowo przez:

```text
TrueNAS -> Apps -> Custom App
```

Ręczne polecenie Docker może posłużyć jako test diagnostyczny:

```bash
sudo docker run -d \
  --name node-exporter-test \
  --network host \
  --pid host \
  -v /:/host:ro,rslave \
  prom/node-exporter:latest \
  --path.rootfs=/host
```

Po uruchomieniu sprawdź:

```bash
sudo docker ps | grep -i exporter
sudo docker logs --tail 100 node-exporter-test
sudo ss -ltnp | grep ':9100'
curl --max-time 10 http://localhost:9100/metrics
```

### Jeżeli test działa

Problem został potwierdzony: brakowało działającego exportera. Skonfiguruj trwałą aplikację w TrueNAS z automatycznym restartem, potem usuń testowy kontener dopiero po potwierdzeniu, że wersja docelowa działa. Nie uruchamiaj obu jednocześnie na porcie `9100`.

### Jeżeli pobranie obrazu nie działa

Sprawdź DNS i internet na TrueNAS:

```bash
getent hosts registry-1.docker.io
ping -c 2 1.1.1.1
```

- IP działa, ale nazwa nie — problem DNS;
- oba nie działają — brama, routing lub dostęp do internetu;
- rejestr odrzuca pobranie — sprawdź dokładny błąd, limity i konfigurację rejestru.

### Jeżeli kontener natychmiast się zatrzymuje

```bash
sudo docker ps -a | grep node-exporter-test
sudo docker logs node-exporter-test
```

Napraw błąd wskazany w logach. Najczęściej jest to niewłaściwa opcja, montowanie, uprawnienie albo konflikt nazwy/portu.

## 8. Sprawdź konfigurację Prometheusa

Na serwerze Prometheusa:

```bash
grep -n -A10 -B2 -i truenas /etc/prometheus/prometheus.yml
```

Oczekiwany fragment:

```yaml
- job_name: 'truenas'
  static_configs:
    - targets:
        - '192.168.20.12:9100'
```

Sprawdź składnię:

```bash
promtool check config /etc/prometheus/prometheus.yml
```

Jeżeli `promtool` nie jest w ścieżce, znajdź sposób uruchomienia właściwy dla instalacji Prometheusa, np. pełną ścieżkę albo polecenie wewnątrz jego kontenera.

### Jeżeli wynik to `SUCCESS`

Jeśli konfiguracja była zmieniana, przeładuj lub zrestartuj Prometheusa:

```bash
sudo systemctl restart prometheus
sudo systemctl status prometheus --no-pager
```

Jeżeli Prometheus działa w kontenerze, sprawdź i zrestartuj właściwy kontener zamiast usługi systemd.

### Jeżeli jest błąd składni

Popraw dokładnie wskazaną linię YAML. Zwróć uwagę na wcięcia i znaki `:`. Nie restartuj Prometheusa, dopóki `promtool check config` nie zakończy się sukcesem.

### Jeżeli plik wygląda poprawnie, ale targetu nie ma

Prometheus może używać innego pliku. Sprawdź parametry działającego procesu:

```bash
ps aux | grep '[p]rometheus'
```

Szukaj parametru `--config.file`. W przypadku kontenera sprawdź jego konfigurację i montowania.

## 9. Sprawdź usługę i logi Prometheusa

Dla instalacji systemowej:

```bash
sudo systemctl status prometheus --no-pager
sudo journalctl -u prometheus -n 200 --no-pager
```

Dla instalacji kontenerowej:

```bash
sudo docker ps -a | grep -i prometheus
sudo docker logs --tail 200 NAZWA_KONTENERA_PROMETHEUS
```

Jeżeli Prometheus nie działa, napraw błąd z logów, a następnie ponownie otwórz `/targets`.

## 10. Sprawdź dane bezpośrednio w Prometheusie

Otwórz:

```text
http://192.168.20.52:9090
```

Wykonaj query:

```promql
up{instance="192.168.20.12:9100"}
```

Interpretacja:

- wynik `1` — Prometheus pobiera dane;
- wynik `0` — target istnieje, ale ostatnie pobranie się nie udało; sprawdź `/targets`;
- brak wyniku — błędny `instance`, brak targetu albo Prometheus korzysta z innych etykiet.

Jeżeli nie znasz etykiet, wykonaj:

```promql
up
```

Sprawdź także obecność podstawowych metryk:

```promql
node_uname_info{instance="192.168.20.12:9100"}
```

```promql
node_cpu_seconds_total{instance="192.168.20.12:9100"}
```

```promql
node_memory_MemTotal_bytes{instance="192.168.20.12:9100"}
```

### Target jest `UP`, ale nie ma oczekiwanych metryk

Sprawdź stronę `/metrics` i logi exportera. Dashboard może oczekiwać innych nazw metryk, dodatkowych collectorów albo dodatkowego exportera.

## 11. Sprawdź źródło danych w Grafanie

W Grafanie przejdź do:

```text
Connections -> Data sources -> Prometheus
```

Sprawdź URL źródła i kliknij `Save & Test`.

Ważne: jeśli Grafana działa w kontenerze, `http://localhost:9090` oznacza kontener Grafany, a nie serwer Prometheusa. Użyj adresu osiągalnego z kontenera Grafany, np. `http://192.168.20.52:9090` albo właściwej nazwy usługi w tej samej sieci kontenerowej.

### Jeżeli test źródła nie przechodzi

Sprawdź z hosta lub kontenera Grafany:

```bash
curl --max-time 10 http://192.168.20.52:9090/-/ready
curl --max-time 10 http://192.168.20.52:9090/api/v1/query?query=up
```

Jeżeli Grafana działa w Dockerze:

```bash
sudo docker ps | grep -i grafana
sudo docker exec -it NAZWA_KONTENERA_GRAFANA sh
```

Wewnątrz kontenera wykonaj test dostępności Prometheusa, jeśli dostępny jest `wget` lub `curl`.

## 12. Sprawdź Grafana Explore

Przejdź do:

```text
Grafana -> Explore
```

Wybierz prawidłowe źródło Prometheus i uruchom:

```promql
up{instance="192.168.20.12:9100"}
```

### Explore pokazuje `1`, ale dashboard nadal ma `No data`

Połączenia działają. Problem leży w dashboardzie. Sprawdź kolejno:

1. Zakres czasu — ustaw np. `Last 1 hour`.
2. Czy dashboard używa właściwego datasource.
3. Zmienne dashboardu, zwłaszcza `job`, `instance`, `host` i `datasource`.
4. Query panelu w `Edit -> Query`.
5. Czy query filtruje po starej wartości IP, nazwie joba lub instance.
6. Czy dashboard oczekuje innego exportera albo innych nazw metryk.
7. `Query inspector`, aby zobaczyć rzeczywiste zapytanie i odpowiedź.
8. Odświeżenie dashboardu, wylogowanie/zalogowanie oraz restart Grafany dopiero po sprawdzeniu powyższych punktów.

## 13. Część paneli działa, a część pokazuje `No data`

Jeżeli CPU, RAM i sieć działają, ale brakuje temperatur, SMART albo ZFS, cały monitoring nie musi być uszkodzony.

Podstawowy `node_exporter` udostępnia głównie metryki systemu operacyjnego. Dashboard może dodatkowo wymagać:

- collectora ZFS;
- danych SMART;
- TrueNAS API;
- metryk temperatur sprzętu;
- osobnego exportera lub textfile collectora.

Otwórz edycję niedziałającego panelu, skopiuj nazwę metryki z query i wyszukaj ją bezpośrednio w Prometheusie. Jeśli metryka nie istnieje, należy włączyć odpowiedni collector/exporter albo dostosować dashboard do dostępnych danych.

## 14. Wszystko wygląda dobrze, ale nadal nie działa

Wykonaj tę procedurę, jeżeli port, target i datasource wydają się poprawne.

### A. Sprawdź właściwe adresy i aktualne IP

```bash
ip addr
ip route
```

Potwierdź, że dashboard i `prometheus.yml` nie używają starego adresu.

### B. Sprawdź czas na wszystkich hostach

```bash
date
timedatectl status
```

Duża różnica czasu może powodować, że próbki znajdują się poza zakresem dashboardu. Włącz synchronizację NTP i ustaw w Grafanie szerszy zakres czasu.

### C. Sprawdź, czy Prometheus zapisuje świeże próbki

```promql
time() - timestamp(up{instance="192.168.20.12:9100"})
```

Wynik powinien być zbliżony do interwału scrapowania, a nie do wielu minut lub godzin.

### D. Sprawdź etykiety, a nie zakładaj ich wartości

Wykonaj `up`, rozwiń serię i porównaj rzeczywiste `job` oraz `instance` z filtrami dashboardu.

### E. Sprawdź wiele źródeł Prometheus w Grafanie

Dashboard może używać innego datasource niż Explore. Sprawdź źródło na poziomie dashboardu, zmiennej i każdego niedziałającego panelu.

### F. Sprawdź logi Grafany

Instalacja systemowa:

```bash
sudo journalctl -u grafana-server -n 200 --no-pager
```

Instalacja kontenerowa:

```bash
sudo docker logs --tail 200 NAZWA_KONTENERA_GRAFANA
```

### G. Sprawdź reguły pośrednie

Jeżeli używasz reverse proxy, Cloudflare, pfSense lub niestandardowego DNS, przetestuj połączenie bezpośrednio po adresach LAN. Monitoring wewnętrzny nie powinien niepotrzebnie wychodzić przez publiczny proxy/tunnel.

### H. Sprawdź retencję i miejsce na dysku Prometheusa

```bash
df -h
df -i
```

Brak miejsca lub inode może uniemożliwiać poprawny zapis danych. Sprawdź również logi Prometheusa pod kątem błędów TSDB.

### I. Sprawdź duplikaty targetów i serii

W Prometheusie wykonaj:

```promql
count by (job, instance) (up)
```

Duplikaty lub kilka targetów z różnymi etykietami mogą powodować błędne wyniki zapytań dashboardu.

### J. Uruchamiaj ponownie od pierwszego potwierdzonego punktu

Po każdej zmianie powtórz pełny łańcuch:

```text
/metrics na TrueNAS
-> /metrics z serwera Prometheusa
-> Prometheus /targets = UP
-> Prometheus query up = 1
-> Grafana datasource test
-> Grafana Explore up = 1
-> dashboard
```

## 15. Szybka procedura diagnostyczna

1. Otwórz `http://192.168.20.52:9090/targets`.
2. Z serwera Prometheusa: `ping -c 4 192.168.20.12`.
3. `nc -zv 192.168.20.12 9100`.
4. `curl http://192.168.20.12:9100/metrics`.
5. Na TrueNAS: `sudo ss -ltnp | grep ':9100'`.
6. `sudo docker ps -a | grep -i exporter`.
7. Sprawdź logi exportera.
8. Sprawdź `prometheus.yml` i `promtool check config`.
9. W Prometheusie wykonaj `up{instance="192.168.20.12:9100"}`.
10. W Grafana Explore wykonaj to samo query.
11. Jeśli Explore działa, sprawdź czas, datasource, zmienne i query dashboardu.

## 16. Drzewo decyzji

```text
Grafana: No data
|
+-- Prometheus target DOWN
|   |
|   +-- ping/łączność nie działa -> IP, VLAN, routing, firewall
|   |
|   +-- port 9100: connection refused -> exporter nie działa lub nie istnieje
|   |
|   +-- port 9100: timeout -> firewall, routing albo zły bind
|   |
|   +-- /metrics działa -> konfiguracja lub działanie Prometheusa
|
+-- Prometheus target UP, query up = 1
    |
    +-- Grafana datasource test nie działa -> URL lub sieć Grafana-Prometheus
    |
    +-- Grafana Explore działa -> dashboard, query, zmienne lub zakres czasu
    |
    +-- tylko część paneli nie działa -> brak wymaganych metryk/collectorów
```

## 17. Diagnoza z opisanego przypadku

W sprawdzanym przypadku wyniki były następujące:

```text
Grafana: No data
Prometheus: target TrueNAS DOWN
ping 192.168.20.12: działa
curl i nc na porcie 9100: Connection refused
ss na TrueNAS: brak procesu na porcie 9100
ps: brak node_exporter
docker ps -a: brak kontenera node_exporter
```

Wniosek:

```text
node_exporter na TrueNAS nie był uruchomiony lub nie istniał.
```

Po uruchomieniu i trwałym skonfigurowaniu exportera należy potwierdzić kolejno: lokalne `/metrics`, dostęp z Prometheusa, target `UP`, query `up = 1`, Grafana Explore oraz dashboard.

## 18. Dobre praktyki na przyszłość

- Zarezerwuj stałe adresy IP/DHCP dla TrueNAS i Prometheusa.
- Nie używaj tagu obrazu `latest` w trwałej konfiguracji bez świadomej kontroli aktualizacji; przypnij sprawdzoną wersję.
- Ustaw automatyczny restart exportera i Prometheusa.
- Zapisz konfiguracje aplikacji oraz `prometheus.yml` w kopii zapasowej.
- Dodaj alert Prometheusa/Grafany dla `up == 0`.
- Po aktualizacji TrueNAS sprawdź Apps, port `9100` i targety Prometheusa.
- Nie uruchamiaj dwóch exporterów na tym samym porcie.
- Nie usuwaj kontenerów ani nie zmieniaj firewalla przed zapisaniem logów i potwierdzeniem przyczyny.
