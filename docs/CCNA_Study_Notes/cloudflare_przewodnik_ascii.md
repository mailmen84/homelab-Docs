# Cloudflare - kompletny przewodnik praktyczny

Wersja: ASCII-only, bez polskich znakow
Temat: Cloudflare, DNS, proxy, tunele, ochrona aplikacji, DDoS/DoS, Zero Trust, homelab

---

## 1. Cel tej notatki

Ten plik jest uporzadkowanym zapisem materialu z naszej sesji o Cloudflare.
Zawiera opis najwazniejszych funkcji Cloudflare, wyjasnienie jak dzialaja tunele,
jak konfigurowac je prawidlowo, jak myslec o ochronie przed DoS/DDoS oraz ktore
opcje Cloudflare sa najwazniejsze w praktyce.

Material jest napisany tak, aby laczyc jezyk techniczny z prostym wyjasnieniem.
Na koncu znajduje sie osobne podsumowanie: co konkretnie warto wykorzystac w homelabie.

---

## 2. Czym jest Cloudflare

Cloudflare to nie jest jedna usluga. To cala platforma do obslugi domen, DNS,
bezpieczenstwa, tuneli, ochrony aplikacji, przyspieszania stron, kontroli dostepu,
ochrony przed botami, hostingu prostych aplikacji i wielu innych rzeczy.

Najprosciej:

```text
Cloudflare = DNS + Proxy + Security + Zero Trust + Tunnels + CDN + Developer Platform
```

Typowy ruch bez Cloudflare wyglada tak:

```text
Uzytkownik -> publiczny IP serwera -> router/firewall -> aplikacja
```

Z Cloudflare:

```text
Uzytkownik -> Cloudflare -> Twoja aplikacja / serwer / chmura / tunel
```

Cloudflare moze byc uzywany dla:

```text
stron internetowych
sklepow internetowych
blogow
API
firmowych aplikacji
paneli administracyjnych
homelabu
serwerow VPS
aplikacji w Dockerze
aplikacji w Kubernetes
zdalnego dostepu do prywatnej sieci
ochrony loginow
ochrony formularzy
hostingu statycznych stron
serverless aplikacji
storage plikow
```

---

## 3. Najwazniejszy model dzialania Cloudflare

Cloudflare najczesciej dziala jako warstwa posrednia miedzy uzytkownikiem a serwerem.

Bez Cloudflare:

```text
Client -> Origin server
```

Z Cloudflare:

```text
Client -> Cloudflare Edge -> Origin server
```

Origin server to prawdziwy serwer, na ktorym dziala aplikacja. Moze to byc:

```text
VPS
serwer firmowy
serwer w domu
Kubernetes
Docker
hosting www
Nginx
Apache
IIS
aplikacja Node.js
aplikacja Python / Flask / FastAPI
```

Cloudflare pomaga w kilku obszarach:

```text
zarzadzanie DNS
ukrywanie prawdziwego IP serwera
szyfrowanie HTTPS
filtrowanie zlosliwego ruchu
cache i CDN
ochrona przed DDoS
ochrona przed botami
kontrola dostepu przez Zero Trust
publikowanie aplikacji bez otwierania portow przez Tunnel
```

---

## 4. Cloudflare DNS

### 4.1 Co to jest DNS

DNS tlumaczy nazwe domeny na adres IP.

Przyklad:

```text
example.com -> 104.21.10.15
app.example.com -> 192.0.2.10
```

Bez DNS uzytkownik musialby pamietac adres IP. Dzieki DNS wpisuje nazwe strony.

### 4.2 Najwazniejsze rekordy DNS

| Rekord | Do czego sluzy | Przyklad |
|---|---|---|
| A | wskazuje na adres IPv4 | app.example.com -> 1.2.3.4 |
| AAAA | wskazuje na adres IPv6 | app.example.com -> IPv6 |
| CNAME | alias do innej nazwy | www -> example.com |
| MX | obsluga poczty | Google Workspace, Proton, Microsoft 365 |
| TXT | weryfikacje, SPF, DKIM, DMARC | bezpieczenstwo poczty |
| SRV | specjalne uslugi | gry, VoIP, LDAP |
| NS | nameservery domeny | delegacja DNS |

### 4.3 Pomaranczowa i szara chmurka

W Cloudflare przy rekordach DNS zobaczysz status proxy.

Szara chmurka - DNS only:

```text
Uzytkownik -> bezposrednio do Twojego serwera
```

Cloudflare tylko odpowiada na zapytanie DNS. Nie filtruje ruchu webowego, nie robi WAF,
nie ukrywa IP origin servera.

Pomaranczowa chmurka - Proxied:

```text
Uzytkownik -> Cloudflare -> Twoj serwer
```

Cloudflare przejmuje ruch HTTP/HTTPS i moze stosowac:

```text
DDoS protection
WAF
SSL/TLS
cache
bot protection
rules
analytics
rate limiting
```

### 4.4 Kiedy uzywac Proxied

Uzywaj Proxied dla:

```text
stron www
aplikacji webowych
API HTTP/HTTPS
paneli, jezeli dodatkowo zabezpieczysz je Access lub VPN
```

Zostaw DNS only dla:

```text
rekordow MX
czesci rekordow TXT
niektorych weryfikacji domeny
uslug, ktore nie sa HTTP/HTTPS i nie maja obslugi przez Cloudflare proxy
```

---

## 5. Cloudflare Proxy

Cloudflare Proxy oznacza, ze ruch webowy przechodzi przez Cloudflare zanim trafi do origin servera.

Schemat:

```text
Client -> Cloudflare Edge -> Origin server
```

Proxy daje korzysci:

```text
ukrywa prawdziwy IP origin servera
filtruje zlosliwy ruch
przyspiesza strone przez cache
obsluguje SSL/TLS
pozwala uzywac regulek bezpieczenstwa
pomaga ograniczac boty i ataki
```

Wazne ograniczenie:

```text
Cloudflare proxy na standardowych planach dziala glownie dla ruchu HTTP/HTTPS.
Nie kazdy protokol mozna po prostu ukryc za pomaranczowa chmurka.
```

Dla innych scenariuszy istnieja osobne rozwiazania:

```text
Cloudflare Tunnel
Cloudflare Spectrum
Cloudflare Zero Trust
WARP / Cloudflare One Client
```

---

## 6. SSL/TLS

SSL/TLS odpowiada za szyfrowanie ruchu.

Schemat:

```text
Uzytkownik <- HTTPS -> Cloudflare <- HTTPS/HTTP -> Origin server
```

### 6.1 Najwazniejsze tryby SSL/TLS

| Tryb | Opis | Ocena |
|---|---|---|
| Off | brak szyfrowania | Nie uzywac |
| Flexible | HTTPS tylko miedzy uzytkownikiem a Cloudflare, dalej HTTP | Nie polecam |
| Full | HTTPS po obu stronach, ale certyfikat origin moze byc self-signed | Moze byc |
| Full Strict | HTTPS po obu stronach i certyfikat origin jest weryfikowany | Najlepsze |
| Automatic SSL/TLS | Cloudflare probuje dobrac ustawienie automatycznie | OK, ale lepiej rozumiec konfiguracje |

Najbezpieczniejszy standard:

```text
SSL/TLS mode: Full Strict
Always Use HTTPS: ON
Minimum TLS: 1.2 albo 1.3
```

### 6.2 Dlaczego Flexible SSL jest problemem

Flexible SSL wyglada tak:

```text
User -> HTTPS -> Cloudflare -> HTTP -> Origin server
```

Uzytkownik widzi klodke HTTPS w przegladarce, ale ruch od Cloudflare do serwera idzie HTTP.
To moze powodowac problemy z bezpieczenstwem, przekierowaniami i aplikacjami.

Lepszy model:

```text
User -> HTTPS -> Cloudflare -> HTTPS -> Origin server
```

### 6.3 Cloudflare Origin Certificate

Cloudflare moze wystawic certyfikat Origin Certificate. Jest on uzywany miedzy Cloudflare
a origin serverem. Przegladarka uzytkownika go bezposrednio nie widzi.

Mozliwe opcje certyfikatu po stronie origin:

```text
Let's Encrypt
Cloudflare Origin Certificate
certyfikat komercyjny
certyfikat z firmowego CA
```

---

## 7. CDN i Cache

CDN oznacza Content Delivery Network.

Cloudflare moze przechowywac kopie plikow na swoich serwerach edge na swiecie.
Dzieki temu uzytkownik pobiera pliki z blizszej lokalizacji, a origin server ma mniejsze obciazenie.

Dobre do cache:

```text
obrazy
CSS
JavaScript
fonty
pliki statyczne
downloady
publiczne assety strony
```

Niebezpieczne do cache bez ostroznosci:

```text
panel admina
koszyk sklepu
logowanie
API z prywatnymi danymi
Nextcloud
bankowosc
dynamiczne strony uzytkownika
```

Przyklad:

```text
/logo.png - mozna cache'owac
/login - raczej nie cache'owac
/api/user/profile - nie cache'owac bez pelnego zrozumienia
```

Zle ustawiony cache moze powodowac dziwne bledy:

```text
uzytkownik widzi stara wersje strony
sesje zachowuja sie niepoprawnie
aplikacja pokazuje nieaktualne dane
pliki po zmianie nadal laduja sie ze starej wersji
```

---

## 8. Cloudflare Tunnel

### 8.1 Co to jest Cloudflare Tunnel

Cloudflare Tunnel pozwala udostepnic lokalna lub prywatna aplikacje do Internetu bez otwierania portow
na routerze/firewallu.

Normalnie, aby wystawic aplikacje, robisz:

```text
Internet -> publiczny IP -> router/firewall -> port forwarding -> serwer
```

Przy Tunnel:

```text
cloudflared -> polaczenie wychodzace do Cloudflare
uzytkownik -> Cloudflare -> tunnel -> lokalna aplikacja
```

Najwazniejsza roznica:

```text
Nie musisz otwierac portu 80/443 na routerze.
Nie musisz miec publicznego IP na serwerze.
Nie wystawiasz origin servera bezposrednio do Internetu.
```

### 8.2 Do czego sluzy Tunnel

Tunnel jest dobry dla:

```text
firmowych aplikacji wewnetrznych
paneli administracyjnych
homelabu
aplikacji testowych
API developerskiego
serwerow bez publicznego IP
srodowisk za NAT-em
aplikacji w Dockerze
aplikacji w Kubernetes
```

### 8.3 Jak dziala Tunnel technicznie

Na serwerze instalujesz program:

```text
cloudflared
```

Ten program dziala jako connector.

Schemat:

```text
[local app] <- LAN/localhost -> [cloudflared] <- outbound tunnel -> [Cloudflare Edge] <- HTTPS -> [user]
```

Przyklad:

```text
Aplikacja lokalna: http://192.168.20.50:3000
Publiczny adres: https://app.example.com
```

Cloudflare Tunnel robi mapowanie:

```text
app.example.com -> http://192.168.20.50:3000
```

Jeden tunnel moze obslugiwac wiele aplikacji:

```text
app.example.com      -> http://192.168.1.10:3000
api.example.com      -> http://192.168.1.11:8000
grafana.example.com  -> http://192.168.1.12:3000
```

---

## 9. Dwa style konfiguracji Tunnel

### 9.1 Metoda 1 - konfiguracja przez Cloudflare Dashboard

To jest najlatwiejsza metoda.

Proces:

```text
Cloudflare Dashboard
-> Zero Trust
-> Networks
-> Tunnels
-> Create tunnel
-> wybierasz cloudflared
-> instalujesz connector
-> dodajesz Public Hostname
```

Przyklad Public Hostname:

```text
Subdomain: app
Domain: example.com
Type: HTTP
URL: 192.168.1.50:3000
```

Efekt:

```text
https://app.example.com -> tunnel -> http://192.168.1.50:3000
```

Ta metoda jest dobra, gdy chcesz zarzadzac wszystkim z panelu.

### 9.2 Metoda 2 - konfiguracja lokalna plikiem config.yml

Bardziej techniczna, ale bardzo dobra dla dokumentacji, automatyzacji i kontroli.

Przyklad:

```yaml
tunnel: company-apps
credentials-file: /etc/cloudflared/company-apps.json

ingress:
  - hostname: app.example.com
    service: http://192.168.1.50:3000

  - hostname: api.example.com
    service: http://192.168.1.51:8000

  - hostname: grafana.example.com
    service: http://192.168.1.52:3000

  - service: http_status:404
```

Ostatnia linia:

```yaml
- service: http_status:404
```

oznacza:

```text
Jesli request nie pasuje do zadnej zdefiniowanej aplikacji, zwroc 404.
```

---

## 10. Prawidlowa konfiguracja Tunnel

Przy kazdej aplikacji musisz znac 4 rzeczy:

```text
1. Jaka subdomena ma byc publiczna?
2. Gdzie lokalnie dziala aplikacja?
3. Czy aplikacja lokalnie uzywa HTTP czy HTTPS?
4. Czy aplikacja powinna byc chroniona przez Cloudflare Access?
```

Przyklad:

```text
Public hostname:
admin.example.com

Local service:
http://10.10.20.15:8080

Protection:
Cloudflare Access required
```

### 10.1 Najczestszy blad: zly protokol

Jesli aplikacja lokalnie dziala po HTTP:

```text
http://192.168.20.110:8096
```

to w Tunnel ustawiasz:

```text
HTTP -> 192.168.20.110:8096
```

Nie ustawiaj HTTPS, jezeli aplikacja lokalnie nie ma HTTPS.

Typowe bledy przy zlym protokole:

```text
502 Bad Gateway
525 SSL Handshake Failed
connection refused
service unavailable
```

### 10.2 Drugi blad: cloudflared nie ma dostepu do aplikacji

Jesli cloudflared dziala na jednej maszynie, a aplikacja na innej, musisz sprawdzic,
czy maszyna z cloudflared moze polaczyc sie z aplikacja.

Test:

```bash
curl -I http://192.168.1.50:3000
```

Jesli curl nie dziala lokalnie, Tunnel tez nie bedzie dzialal.

### 10.3 Trzeci blad: aplikacja slucha tylko na localhost

Jesli aplikacja dziala tylko na:

```text
127.0.0.1:3000
```

a cloudflared jest na innej maszynie, nie polaczy sie.

Aplikacja powinna sluchac na:

```text
0.0.0.0:3000
```

albo cloudflared musi dzialac na tej samej maszynie.

### 10.4 Czwarty blad: brak Access przed panelem admina

Nie wystawiaj paneli administracyjnych bez dodatkowego logowania.

Ryzykowne bez Access/VPN:

```text
proxmox.example.com
pfsense.example.com
truenas.example.com
grafana.example.com
admin.example.com
```

Dla takich paneli uzyj:

```text
Cloudflare Access
Tailscale
WireGuard
VPN
IP allowlist
MFA
```

---

## 11. Instalacja cloudflared na Ubuntu - ogolna logika

Aktualna instalacja zalezy od systemu i warto zawsze sprawdzic oficjalna dokumentacje Cloudflare.
Ogolna logika wyglada tak:

Aktualizacja systemu:

```bash
sudo apt update
```

Logowanie do Cloudflare:

```bash
cloudflared tunnel login
```

Tworzenie tunelu:

```bash
cloudflared tunnel create homelab-tunnel
```

Dodanie routingu DNS:

```bash
cloudflared tunnel route dns homelab-tunnel app.example.com
```

Uruchomienie testowe:

```bash
cloudflared tunnel run homelab-tunnel
```

Instalacja jako service:

```bash
sudo cloudflared service install
```

Sprawdzenie statusu:

```bash
systemctl status cloudflared
```

Restart:

```bash
sudo systemctl restart cloudflared
```

Logi:

```bash
journalctl -u cloudflared -f
```

---

## 12. Cloudflare Access

Cloudflare Access to bramka logowania przed aplikacja.

Bez Access:

```text
uzytkownik -> app.example.com -> aplikacja
```

Z Access:

```text
uzytkownik -> app.example.com -> Cloudflare login/MFA -> aplikacja
```

Access jest bardzo wazny dla:

```text
paneli admina
Grafany
wewnetrznych aplikacji firmowych
narzedzi developerskich
aplikacji HR/finance
staging environment
prywatnych API
```

Przykladowe polityki Access:

```text
Allow only konkretny email
Allow only konkretna domena firmowa
Allow przez Google login
Allow przez GitHub login
Allow przez Azure AD / Entra ID
Allow przez Okta
Allow przez one-time PIN
Allow przez service token dla maszyn
```

Przyklad:

```text
grafana.example.com
Policy: Allow only user@example.com
MFA: required
Session duration: 12 hours
```

Access nie zastapi hasla w aplikacji. Najlepszy model:

```text
Cloudflare Access login
+
normalne haslo aplikacji
+
MFA, jesli aplikacja wspiera
```

---

## 13. WAF - Web Application Firewall

WAF to firewall dla aplikacji webowych.

Zwykly firewall patrzy glownie na:

```text
IP
port
protokol
interface
VLAN
NAT
```

WAF patrzy wyzej, na poziomie HTTP:

```text
URL
sciezka
metoda HTTP
naglowki
kraj
user-agent
payload requestu
wzorce atakow
SQL injection
XSS
skanowanie podatnosci
brute force
```

Przyklad ataku:

```text
https://example.com/product?id=1' OR '1'='1
```

WAF moze rozpoznac, ze to wyglada jak SQL injection.

### 13.1 Przykladowe reguly WAF

Regula 1:

```text
Jesli kraj nie jest Ireland, Poland albo UK,
a hostname to admin.example.com,
to Block.
```

Regula 2:

```text
Jesli sciezka zawiera /wp-admin,
a strona nie uzywa WordPress,
to Block.
```

Regula 3:

```text
Jesli request trafia na /login z podejrzanego kraju,
to Managed Challenge.
```

### 13.2 WAF a pfSense

Cloudflare WAF i pfSense to nie to samo.

Cloudflare WAF chroni aplikacje webowe:

```text
HTTP/HTTPS
URL
login
API
payload
boty
ataki webowe
```

pfSense chroni siec lokalna:

```text
VLANy
routing
firewall miedzy podsieciami
NAT
VPN
DNS Resolver
reguly na interface'ach
```

Najlepszy model:

```text
Cloudflare = ochrona aplikacji publicznych
pfSense = ochrona sieci lokalnej
VPN/Tailscale = prywatna administracja
```

---

## 14. Rate Limiting

Rate limiting ogranicza liczbe requestow.

Przyklad:

```text
Jeden IP moze wykonac maksymalnie 10 prob logowania na minute.
Po przekroczeniu limitu: challenge albo block.
```

Do czego sluzy:

```text
ochrona loginow
ochrona API
ochrona formularzy
ochrona przed brute force
ochrona przed prostym DoS
ograniczenie scrapingu
ograniczenie kosztow backendu
```

Dobre miejsca na rate limiting:

```text
/login
/admin
/api/login
/api/auth
/wp-login.php
/xmlrpc.php
/password-reset
/contact-form
```

Przyklad reguly:

```text
If:
http.host eq "api.example.com"
and http.request.uri.path contains "/login"

Then:
limit 10 requests per 1 minute per IP
action: Managed Challenge
```

---

## 15. DoS i DDoS protection

### 15.1 DoS

DoS to atak z jednego zrodla albo malej liczby zrodel.

Przyklad:

```text
Jeden komputer wysyla tysiace requestow na sekunde.
```

### 15.2 DDoS

DDoS to atak rozproszony.

Przyklad:

```text
10 000 zainfekowanych urzadzen wysyla ruch do jednej strony.
```

### 15.3 Jak Cloudflare pomaga

Cloudflare pomaga, gdy ruch przechodzi przez Cloudflare:

```text
Proxied DNS
Cloudflare Tunnel
WAF
Rate Limiting
DDoS managed rules
Bot protection
Access
```

Najlepszy model ochrony:

```text
1. Nie wystawiaj origin servera bezposrednio.
2. Uzywaj Proxied DNS albo Tunnel.
3. Nie otwieraj niepotrzebnych portow.
4. Dodaj WAF rules.
5. Dodaj Rate Limiting.
6. Panele admina zabezpiecz Access/VPN.
7. Blokuj nietypowe kraje, jesli aplikacja ma lokalnych uzytkownikow.
8. Monitoruj Security Events.
```

### 15.4 I'm Under Attack Mode

Cloudflare ma tryb awaryjny, czesto nazywany I'm Under Attack Mode.
Uzywa sie go wtedy, gdy strona jest aktywnie atakowana i trzeba szybko podniesc poziom ochrony.

Nie jest to tryb, ktory zwykle wlacza sie na stale, bo moze utrudniac dostep prawdziwym uzytkownikom.

---

## 16. Bot Protection

Boty moga byc dobre albo zle.

Dobre boty:

```text
Googlebot
Bingbot
monitoring uptime
legalne integracje API
```

Zle boty:

```text
scraping
brute force
credential stuffing
spam
skanowanie podatnosci
falszywe rejestracje
```

Cloudflare ma rozne narzedzia:

```text
Bot Fight Mode
Super Bot Fight Mode
Managed Challenge
WAF rules
Rate Limiting
Turnstile
Bot score na wyzszych planach
```

Dla zwyklej strony mozna uzyc ostrzejszej ochrony.
Dla API, aplikacji mobilnych, Jellyfin albo specjalnych klientow trzeba uwazac, bo challenge moze zepsuc legalny ruch.

---

## 17. Turnstile

Turnstile to alternatywa dla CAPTCHA.

Zamiast klasycznych obrazkow typu "wybierz autobusy", Turnstile probuje potwierdzic,
ze uzytkownik jest prawdziwy, w mniej irytujacy sposob.

Dobre zastosowania:

```text
formularz logowania
formularz kontaktowy
rejestracja konta
reset hasla
komentarze
prosty endpoint API
```

Typowy schemat:

```text
1. Uzytkownik otwiera formularz.
2. Turnstile generuje token.
3. Formularz wysyla token do backendu.
4. Backend pyta Cloudflare, czy token jest poprawny.
5. Jesli tak, backend przyjmuje request.
```

---

## 18. Rules - reguly Cloudflare

Cloudflare ma kilka rodzajow regulek.

| Typ reguly | Do czego sluzy |
|---|---|
| WAF Custom Rules | bezpieczenstwo aplikacji |
| Rate Limiting Rules | limit requestow |
| Cache Rules | sterowanie cache |
| Redirect Rules | przekierowania |
| Transform Rules | modyfikacja request/response |
| Origin Rules | zmiana origin, portu, host header |
| Configuration Rules | inne ustawienia per hostname/path |

Przyklad Redirect Rule:

```text
http://example.com -> https://example.com
```

Przyklad Cache Rule:

```text
Cache all .jpg, .png, .css, .js for 1 month.
```

Przyklad WAF Custom Rule:

```text
Block requests to /admin from countries outside Ireland.
```

Przyklad Transform Rule:

```text
Dodaj security header do odpowiedzi.
```

---

## 19. Redirect Rules

Redirect Rules sluza do przekierowan.

Przyklady:

```text
http://example.com -> https://example.com
example.com -> www.example.com
old.example.com/page -> new.example.com/page
```

Dobre zastosowania:

```text
wymuszenie HTTPS
zmiana starej domeny na nowa
porzadkowanie adresow URL
przekierowanie z www na non-www albo odwrotnie
```

---

## 20. Transform Rules

Transform Rules pozwalaja modyfikowac request albo response.

Przyklady:

```text
dodanie albo usuniecie naglowka
zmiana URL path
normalizacja requestow
dodanie security headers
```

Przykladowe security headers:

```text
Strict-Transport-Security
X-Frame-Options
X-Content-Type-Options
Referrer-Policy
Content-Security-Policy
```

To jest bardziej zaawansowany temat. Na poczatku wazniejsze sa DNS, Tunnel, Access, WAF i Rate Limiting.

---

## 21. Origin Rules

Origin Rules pozwalaja zmienic, gdzie Cloudflare wysyla ruch.

Przyklad:

```text
Dla api.example.com uzyj origin server A.
Dla app.example.com uzyj origin server B.
Zmien port origin.
Zmien host header.
```

W prostych konfiguracjach czesto wystarcza Nginx Proxy Manager albo Cloudflare Tunnel.
Origin Rules przydaja sie w bardziej rozbudowanej infrastrukturze.

---

## 22. Configuration Rules

Configuration Rules pozwalaja zmieniac ustawienia Cloudflare zaleznie od hosta albo sciezki.

Przyklady:

```text
Dla admin.example.com ustaw wyzszy security level.
Dla static.example.com ustaw inne cache.
Dla api.example.com wylacz pewne optymalizacje.
```

---

## 23. Cloudflare Zero Trust

Zero Trust to wiekszy system bezpieczenstwa.

Obejmuje m.in.:

```text
Access
Gateway
Tunnel
WARP / Cloudflare One Client
Device posture
Service tokens
Private network access
Policies
Logs
```

Stara logika:

```text
Jestes w sieci firmowej = ufamy Ci.
```

Zero Trust:

```text
Kazdy request musi byc sprawdzony.
Kim jestes?
Z jakiego urzadzenia?
Czy masz MFA?
Czy urzadzenie jest bezpieczne?
Do jakiej aplikacji probujesz wejsc?
```

Zero Trust jest popularny w firmach, bo zastapil albo uzupelnil klasyczny VPN.

---

## 24. Cloudflare Gateway

Gateway sluzy do filtrowania ruchu uzytkownikow i urzadzen.

Moze filtrowac:

```text
DNS
HTTP
Network traffic
malware domains
phishing
kategorie stron
podejrzane aplikacje
niepozadany ruch
```

Przyklad firmowy:

```text
Laptop pracownika -> Cloudflare One Client -> Gateway -> Internet
```

Gateway moze blokowac:

```text
malware
phishing
adult content
gambling
unknown newly registered domains
command-and-control domains
```

W domu moze byc ciekawy, ale nie jest konieczny na start.

---

## 25. Cloudflare One Client / WARP

Cloudflare One Client, czesto kojarzony z WARP, to aplikacja instalowana na komputerze albo telefonie.

Moze robic kilka rzeczy:

```text
wysylac DNS przez Cloudflare
wysylac ruch przez Gateway
umozliwiac dostep do prywatnych aplikacji
zglaszac stan urzadzenia
sprawdzac device posture
```

Porownanie:

| Rozwiazanie | Najlepsze do |
|---|---|
| Cloudflare Tunnel | wystawianie aplikacji bez port forwarding |
| Cloudflare Access | logowanie/MFA przed aplikacja |
| Cloudflare Gateway | filtrowanie ruchu uzytkownikow |
| Cloudflare One Client/WARP | podlaczenie urzadzen do Cloudflare Zero Trust |
| Tailscale/WireGuard | prosty prywatny dostep do sieci/homelabu |

---

## 26. Cloudflare Workers

Workers to serverless platforma.

Czyli mozesz uruchamiac kod bez zarzadzania serwerem.

Przyklady uzycia:

```text
male API
proxy logic
redirect logic
autoryzacja requestow
modyfikacja response
integracja z R2/D1/KV
backend dla prostej aplikacji
```

Przyklad schematu:

```text
request -> Cloudflare Worker -> sprawdzenie tokena -> origin API
```

Dla nauki IT Workers sa ciekawe, bo ucza:

```text
HTTP
API
JavaScript/TypeScript
serverless
edge computing
security logic
```

---

## 27. Cloudflare Pages

Pages sluzy do hostowania stron statycznych i prostych aplikacji frontendowych.

Dobre do:

```text
portfolio
dokumentacja
blog
landing page
React/Vue/Svelte app
strona projektu
CV online
```

Typowy workflow:

```text
GitHub repo -> Cloudflare Pages -> automatyczny deploy
```

Dla nauki IT to bardzo dobra rzecz, bo mozesz miec:

```text
wlasna domene
GitHub
automatyczne deploymenty
HTTPS
globalny hosting
```

---

## 28. Cloudflare R2

R2 to object storage, podobny koncepcyjnie do Amazon S3.

Sluzy do przechowywania:

```text
obrazow
plikow
backupow aplikacyjnych
assetow strony
danych uzytkownikow
logow
plikow do pobrania
materialow AI/ML
```

Nie myl R2 z NAS-em.

```text
TrueNAS/NAS = lokalny storage w Twojej sieci
R2 = cloud object storage
```

R2 jest dobre jako zewnetrzne miejsce na wybrane pliki albo assety, ale nie jako pelny zamiennik domowego NAS.

---

## 29. Cloudflare Images, Stream i media

Cloudflare ma tez uslugi do mediow.

Images:

```text
przechowywanie obrazow
optymalizacja obrazow
zmiana rozmiaru
serwowanie obrazow przez CDN
```

Stream:

```text
hosting video
transkodowanie video
odtwarzanie video na stronach
```

To nie jest to samo co Jellyfin.

```text
Jellyfin = Twoj wlasny media server
Cloudflare Stream = platforma do hostingu video dla stron/aplikacji
```

---

## 30. Email Routing

Email Routing pozwala przekierowac poczte z wlasnej domeny na inny adres.

Przyklad:

```text
contact@example.com -> twojemail@gmail.com
admin@example.com -> twojemail@gmail.com
```

To jest dobre do odbierania poczty.

Wazne:

```text
Email Routing nie jest pelnym systemem do wysylania poczty.
```

Do wysylania profesjonalnej poczty potrzebujesz np.:

```text
Google Workspace
Microsoft 365
Proton Mail
Zoho
Mailgun
Amazon SES
SMTP provider
```

Dla domeny warto ustawic:

```text
SPF
DKIM
DMARC
```

To chroni przed podszywaniem sie pod Twoja domene.

---

## 31. Analytics i Security Events

Cloudflare pokazuje statystyki:

```text
ile bylo requestow
z jakich krajow
ile zablokowano
jakie byly ataki
jakie reguly zadzialaly
jaki byl traffic
cache hit ratio
DNS analytics
security events
```

To jest bardzo przydatne, bo widzisz, co realnie dzieje sie z domena.

Przyklad:

```text
Widzisz 500 requestow na /wp-login.php,
ale nie masz WordPressa.
```

Wtedy robisz WAF rule:

```text
Block /wp-login.php
```

---

## 32. Load Balancing

Load Balancing rozdziela ruch miedzy kilka serwerow.

Przyklad:

```text
app.example.com
-> server A
-> server B
-> server C
```

Dobre do:

```text
wysokiej dostepnosci
failover
wiekszego ruchu
aplikacji produkcyjnych
firmowych systemow
```

Jesli jeden serwer padnie, Cloudflare moze kierowac ruch na drugi.
Dla malej strony lub homelabu nie jest to pierwsza rzecz do konfiguracji, ale w firmach jest wazna.

---

## 33. Cloudflare dla API

Cloudflare jest bardzo przydatne dla API.

Mozesz uzyc:

```text
WAF
Rate Limiting
Access
API Shield
mTLS
schema validation
bot protection
logs
```

Przyklad API:

```text
api.example.com/login
api.example.com/users
api.example.com/orders
```

Co warto dodac:

```text
Rate limit na /login
Rate limit na /api
blokowanie podejrzanych krajow
token authentication w aplikacji
Cloudflare Access dla prywatnego API
```

Przyklad:

```text
/api/login -> 10 requests / minute / IP
/api/public -> 100 requests / minute / IP
/api/admin -> tylko Access albo VPN
```

---

## 34. Jak prawidlowo myslec o Cloudflare

Najlepiej traktowac Cloudflare jako kilka warstw.

### Warstwa 1 - domena i DNS

```text
Czy domena wskazuje tam, gdzie trzeba?
Czy rekord jest Proxied czy DNS only?
Czy poczta ma poprawne SPF/DKIM/DMARC?
```

### Warstwa 2 - transport i szyfrowanie

```text
Czy HTTPS dziala?
Czy SSL/TLS jest Full Strict?
Czy origin ma poprawny certyfikat?
```

### Warstwa 3 - aplikacja

```text
Czy aplikacja dziala lokalnie?
Czy Cloudflare moze sie do niej polaczyc?
Czy HTTP/HTTPS jest dobrze ustawione?
Czy nie cache'ujesz prywatnych danych?
```

### Warstwa 4 - bezpieczenstwo

```text
Czy WAF jest wlaczony?
Czy login ma rate limiting?
Czy panele admina maja Access?
Czy boty sa ograniczone?
```

### Warstwa 5 - monitoring

```text
Czy patrzysz w Security Events?
Czy widzisz blokowane requesty?
Czy nie blokujesz legalnych uzytkownikow?
```

---

## 35. Najczestsze bledy

### 35.1 Flexible SSL

Flexible SSL wyglada bezpiecznie, ale od Cloudflare do origin servera moze isc HTTP.

Zamiast tego uzyj:

```text
Full Strict
```

### 35.2 Otwarty origin IP

Jezeli masz pomaranczowa chmurke, ale publiczny IP serwera jest znany i port 443 jest otwarty,
ktos moze ominac Cloudflare.

Lepsze opcje:

```text
Tunnel
firewall allow only Cloudflare IPs
VPN
no public port forwarding
```

### 35.3 Brak Access przed panelem admina

Nie wystawiaj bezposrednio:

```text
grafana.example.com
proxmox.example.com
pfsense.example.com
admin.example.com
```

bez dodatkowej ochrony.

### 35.4 Zbyt agresywne cache

Nie uzywaj "Cache Everything" na dynamicznych aplikacjach bez dokladnego zrozumienia.

### 35.5 Zly protokol w Tunnel

Jesli aplikacja lokalnie dziala po HTTP, w Tunnel ustaw HTTP.
Jesli aplikacja lokalnie dziala po HTTPS, w Tunnel ustaw HTTPS.

### 35.6 Brak rate limiting na login

Kazdy publiczny login powinien miec przynajmniej podstawowy rate limit.

---

## 36. Przykladowa konfiguracja dla zwyklej strony internetowej

Domena:

```text
example.com
www.example.com
```

Ustawienia:

```text
DNS: Proxied
SSL/TLS: Full Strict
Always Use HTTPS: ON
WAF Managed Rules: ON
Cache static assets: ON
Rate limit /login: ON, jesli jest login
Bot protection: ON, ale testowac
Analytics: obserwowac
```

---

## 37. Przykladowa konfiguracja dla API

```text
api.example.com
```

Ustawienia:

```text
DNS: Proxied
SSL/TLS: Full Strict
WAF: ON
Rate limit: ON
Access: jesli API jest prywatne
Bot protection: ostroznie
Cache: zwykle OFF dla dynamicznego API
Logs: ON
```

---

## 38. Przykladowa konfiguracja dla aplikacji firmowej

```text
crm.company.com
admin.company.com
intranet.company.com
```

Ustawienia:

```text
Cloudflare Tunnel
Cloudflare Access
MFA
Identity provider, np. Google/Azure/Okta
Device posture
Gateway policies
WAF
Rate Limiting
Audit logs
```

Model:

```text
Nie ufamy samemu faktowi, ze ktos zna adres.
Najpierw tozsamosc, potem dostep.
```

---

## 39. Przykladowa konfiguracja dla projektu programistycznego

```text
frontend.example.com -> Cloudflare Pages
api.example.com -> Worker albo VPS za Cloudflare
files.example.com -> R2
```

Dobre do nauki:

```text
GitHub
CI/CD
DNS
HTTPS
API
serverless
object storage
security
```

---

## 40. Przykladowa konfiguracja dla Jellyfin

Jellyfin jest specyficzny, bo klienci TV i aplikacje mobilne czasem zle reaguja na agresywne challenge.

Model:

```text
jellyfin.example.com
-> Cloudflare Tunnel
-> http://192.168.20.110:8096
```

Zalecenia:

```text
SSL/TLS: Full Strict
WAF: lekki
Bot Fight Mode: ostroznie
Access: tylko jesli klienci dzialaja poprawnie z Access
Rate limiting: umiarkowany
Cache: raczej nie cache'owac streamow
```

Uwaga:

```text
Cloudflare nie zawsze jest idealne do duzego prywatnego streamingu video.
Dla dostepu rodzinnego czasem lepszy bedzie Tailscale, WireGuard albo VPN.
```

---

## 41. Przykladowa konfiguracja dla Nextcloud

Model:

```text
cloud.example.com
-> Cloudflare Tunnel
-> http://192.168.20.120:80
```

Zalecenia:

```text
SSL/TLS Full Strict
WAF Managed Rules
Rate limit na /login
nie cache'owac calej aplikacji
duzy upload moze wymagac dopracowania timeoutow i limitow
Access tylko jesli nie psuje dzialania klientow Nextcloud
```

---

## 42. Przykladowa konfiguracja dla Grafany

Model:

```text
grafana.example.com
-> Cloudflare Tunnel
-> http://192.168.20.130:3000
```

Zalecenia:

```text
Cloudflare Access: wymagany
Allow only your email
MFA: ON
Grafana local password: strong
Disable anonymous access
Rate limiting: ON
```

Grafana jest bardzo dobrym kandydatem do Cloudflare Access.

---

## 43. Przykladowa konfiguracja dla Proxmox

Technicznie mozna:

```text
proxmox.example.com
-> Cloudflare Tunnel
-> https://192.168.20.10:8006
```

Ale bezpieczniejsza rekomendacja:

```text
Nie wystawiaj Proxmox publicznie.
Uzywaj Tailscale, WireGuard albo VPN.
```

Jesli mimo wszystko przez Cloudflare:

```text
Access obowiazkowo
MFA obowiazkowo
tylko Twoj email
silne haslo Proxmox
WAF + rate limiting
nie publikowac bezposredniego IP
```

---

## 44. Najwazniejsze opcje Cloudflare - tabela zbiorcza

| Funkcja | Co to jest | Do czego sluzy |
|---|---|---|
| DNS | zarzadzanie rekordami domeny | kierowanie domen i subdomen |
| Proxy | ruch przechodzi przez Cloudflare | bezpieczenstwo, cache, ukrycie origin |
| SSL/TLS | szyfrowanie | HTTPS i ochrona polaczenia |
| CDN | globalne serwery edge | przyspieszenie stron |
| Cache | przechowywanie kopii plikow | mniejsze obciazenie origin |
| Tunnel | polaczenie outbound przez cloudflared | publikacja aplikacji bez port forwarding |
| Access | logowanie przed aplikacja | ochrona paneli i aplikacji prywatnych |
| WAF | firewall aplikacyjny | ochrona przed exploitami |
| Rate Limiting | limity requestow | ochrona loginow/API |
| DDoS Protection | ochrona przed duzym ruchem | odpornosc na ataki wolumetryczne |
| Bot Protection | wykrywanie botow | ochrona przed scrapingiem i brute force |
| Turnstile | CAPTCHA alternative | ochrona formularzy |
| Zero Trust | model dostepu identity-based | kontrola dostepu firmowego |
| Gateway | filtrowanie ruchu urzadzen | ochrona laptopow/sieci |
| WARP/One Client | klient na urzadzenia | Gateway, private access, posture |
| Workers | serverless kod | API, edge logic, automatyzacje |
| Pages | hosting statycznych stron | portfolio, dokumentacja, frontend |
| R2 | object storage | pliki, backupy, assety |
| Images | obrazki | optymalizacja i hosting obrazow |
| Stream | video | hosting i streaming video |
| Email Routing | przekierowanie maili | odbieranie poczty na domenie |
| Analytics | statystyki ruchu | monitoring i analiza |
| Load Balancing | rozdzielanie ruchu | failover i HA |

---

## 45. Co warto wykorzystac w homelabie

Dla homelabu nie trzeba uzywac wszystkiego naraz. Najlepiej zaczac od podstaw i dokladac kolejne elementy.

### 45.1 Cloudflare DNS

Warto uzyc do zarzadzania domena, np.:

```text
nodecrafts.org
grafana.nodecrafts.org
nextcloud.nodecrafts.org
jellyfin.nodecrafts.org
docs.nodecrafts.org
```

### 45.2 Cloudflare Tunnel

Bardzo warto.

Najwieksza korzysc:

```text
Nie otwierasz portow na routerze.
```

Dobre dla:

```text
Nextcloud
Grafana
male aplikacje testowe
dashboardy
projekty webowe
dokumentacja
```

### 45.3 Cloudflare Access

Bardzo warto dla paneli.

Uzyj dla:

```text
Grafana
wewnetrzne dashboardy
testowe aplikacje
admin panele
```

Dla bardzo wrazliwych rzeczy, jak:

```text
pfSense
Proxmox
TrueNAS
iLO/iDRAC
```

najbezpieczniej nadal uzywac:

```text
Tailscale / WireGuard / VPN
```

a nie wystawiac publicznie.

### 45.4 SSL/TLS Full Strict

Ustaw jako standard:

```text
SSL/TLS: Full Strict
Always Use HTTPS: ON
```

### 45.5 WAF

Warto uzyc podstawowych regul.

Przyklady:

```text
blokuj /wp-login.php, jesli nie masz WordPressa
blokuj kraje, z ktorych nie oczekujesz ruchu
challenge dla podejrzanych requestow
```

### 45.6 Rate Limiting

Warto dla:

```text
/login
/admin
/api
```

Przyklad:

```text
10 prob logowania na minute z jednego IP -> challenge/block
```

### 45.7 Nie wystawiac wszystkiego

Najwazniejsza zasada:

```text
Publiczne tylko to, co musi byc publiczne.
Administracja tylko przez VPN/Tailscale/Access.
```

### 45.8 Pages dla dokumentacji

Bardzo dobry pomysl dla nauki i portfolio.

Mozesz miec:

```text
GitHub repo z dokumentacja
Cloudflare Pages
docs.nodecrafts.org
```

### 45.9 R2 opcjonalnie

Mozesz uzyc pozniej do:

```text
backupow wybranych plikow
plikow statycznych
assetow strony
eksperymentow programistycznych
```

Nie jako zamiennik TrueNAS, tylko jako cloud object storage.

---

## 46. Najlepszy minimalny zestaw dla homelabu

Na start:

```text
Cloudflare DNS
Cloudflare Tunnel
Cloudflare Access
SSL/TLS Full Strict
WAF basic rules
Rate Limiting for login pages
Cloudflare Pages for documentation/portfolio
```

Nie zaczynalbym od:

```text
Load Balancing
Workers AI
zaawansowany Gateway
skomplikowane Transform Rules
pelne enterprise policies
```

To mozna dodac pozniej.

---

## 47. Prosta rekomendowana architektura homelab

Publikowanie aplikacji:

```text
Internet
   |
   v
Cloudflare DNS / Proxy / WAF / Access
   |
   v
Cloudflare Tunnel
   |
   v
cloudflared VM
   |
   v
LAN / VLAN services
   |
   +--> Nextcloud
   +--> Grafana
   +--> Jellyfin
   +--> Web apps
   +--> Documentation
```

Administracja:

```text
Laptop / phone
   |
   v
Tailscale / WireGuard
   |
   v
Proxmox / TrueNAS / pfSense / switches / iLO / iDRAC
```

---

## 48. Najwazniejsze podsumowanie

Cloudflare najlepiej rozumiec tak:

```text
DNS - gdzie domena wskazuje
Proxy - ruch przechodzi przez Cloudflare
SSL/TLS - szyfrowanie
CDN/Cache - przyspieszenie
WAF - ochrona aplikacji
Rate Limiting - ograniczenie naduzyc
DDoS Protection - ochrona przed duzym ruchem
Tunnel - aplikacje bez otwierania portow
Access - logowanie przed aplikacja
Zero Trust - kontrola dostepu wedlug tozsamosci
Workers/Pages/R2 - platforma developerska
```

Dla homelabu najwazniejsze:

```text
1. Uzyj Cloudflare DNS dla domeny.
2. Uzyj Tunnel zamiast port forwarding.
3. Uzyj Access dla paneli i dashboardow.
4. Nie wystawiaj pfSense/Proxmox/TrueNAS bezposrednio do Internetu.
5. Ustaw SSL/TLS Full Strict.
6. Dodaj WAF i rate limiting na loginy.
7. Do administracji uzywaj Tailscale/WireGuard.
8. Cloudflare Pages uzyj do dokumentacji i portfolio.
```

Najkrocej:

```text
Cloudflare do publikowania i ochrony aplikacji.
Tailscale/VPN do prywatnej administracji.
pfSense do kontroli sieci lokalnej.
```

---

## 49. Przydatne oficjalne zrodla

Cloudflare documentation main page:
https://developers.cloudflare.com/

Cloudflare DNS:
https://developers.cloudflare.com/dns/

Cloudflare Tunnel:
https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/

Cloudflare Access:
https://developers.cloudflare.com/cloudflare-one/policies/access/

Cloudflare WAF:
https://developers.cloudflare.com/waf/

Cloudflare Rate Limiting Rules:
https://developers.cloudflare.com/waf/rate-limiting-rules/

Cloudflare DDoS Protection:
https://developers.cloudflare.com/ddos-protection/

Cloudflare Workers:
https://developers.cloudflare.com/workers/

Cloudflare Pages:
https://developers.cloudflare.com/pages/

Cloudflare R2:
https://developers.cloudflare.com/r2/

