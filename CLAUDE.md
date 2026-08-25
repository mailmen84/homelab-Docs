# CLAUDE.md — kontekst projektu Homelab

Ten plik jest przekazaniem kontekstu z rozmowy prowadzonej w aplikacji Claude (2026-08-25) do Claude Code.
**Przeczytaj go w całości przed pierwszą odpowiedzią.** Szczegółowy, żywy plan jest w `PLAN.md` — to on jest źródłem prawdy o postępach.

---

## 1. Kim jest użytkownik i czego chce

Właściciel homelabu (GitHub: `mailmen84`). Repo dokumentacji: https://github.com/mailmen84/homelab-Docs (**publiczne**).

Cele projektu:
1. Zinwentaryzować wszystkie serwery, urządzenia i usługi w homelabie.
2. Doprowadzić repo do stanu zgodnego z rzeczywistością — repo **nigdy nie zostało dokończone** i zawiera nieaktualne dane.
3. Dokończyć rozpoczęte, nieukończone projekty.
4. Naprawić niedziałające usługi.

**Kolejność ustalona przez właściciela: NAJPIERW kompletna i poprawna dokumentacja, DOPIERO POTEM naprawy.**

---

## 2. ZASADY PRACY — obowiązkowe

1. **Maksymalnie 3 kroki na raz.** Po nich zapytaj: „czy wszystko wykonane poprawnie?". Nie rozpisuj pełnych, wieloetapowych instrukcji — to powoduje bałagan, gdy użytkownik utknie w połowie.
2. **Nigdy nie zgaduj.** Brak informacji = pytanie. Nieznane wartości zapisuj jako `???`. Rozróżniaj dane `POTWIERDZONE` (sprawdzone na sprzęcie) od `Z-REPO` (tylko z dokumentacji, niezweryfikowane).
3. **Nie pytaj dwa razy o to samo.**
4. **Jeśli widzisz lepszą drogę do celu — zaproponuj ją i uzasadnij.** Decyzja należy do właściciela.
5. **Każda naprawa = osobny dokument troubleshooting** w `docs/60-runbooks/` (szablon w sekcji 6c pliku `PLAN.md`). Cel: gdy problem wróci, jest gotowa ścieżka.
6. **Nic nie usuwamy bez potwierdzenia** — kontenery, VM, dyski, wpisy DNS, pliki w repo.
7. **Zero sekretów w repo.** Hasła, tokeny, klucze, configi VPN — nigdy.
8. Właściciel **często pracuje z telefonu** — wtedy proponuj zadania nadające się do wykonania z małego ekranu i nie każ mu przepisywać długich outputów.
9. `PLAN.md` aktualizuj po każdym ustaleniu. Wpisuj zmiany do dziennika sesji w sekcji 10.
10. **Język repo:** `PLAN.md` i `CLAUDE.md` — po polsku (dokumenty robocze projektu). **Cała reszta repo** (dokumentacja w `docs/`, runbooki troubleshooting, README, konwencje) — **po angielsku**, zgodnie z `CONVENTIONS.md`.

---

## 3. Stan faktyczny — POTWIERDZONE (2026-08-25)

### Sprzęt aktywny
| Nazwa | Sprzęt | IP | Rola |
|---|---|---|---|
| proxmox | HP DL380 Gen9, **512 GB RAM** | 192.168.20.10 | Proxmox VE **9.2.4** (jądro 7.0.14-4-pve) |
| truenas-r730 | Dell R730 | **192.168.20.11** | **Jedyny aktywny NAS** |
| pfSense | **osobny PC z 2× NIC** | 192.168.20.1 | Brama + DHCP |
| pihole | Raspberry Pi 3+ | 192.168.10.2 | Pi-hole DNS |
| tailscale | Dell Optiplex | 192.168.10.130 | Tailscale subnet router (do weryfikacji) |
| switch core | Buffalo BS-MP2012 | ??? | switch zarządzalny |
| switch 2 | Cisco (model ???) | ??? | do weryfikacji |
| NAS 2 | Buffalo TS-XL | ??? | do weryfikacji |

### Sprzęt WYCOFANY (repo nadal go wymienia jako aktywny — do usunięcia z dokumentacji)
- **Dell R410** — był głównym TrueNAS, wycofany, dane przeniesione na R730.
- **Dell R320** — wyłączony.
- **truenas-pc (PC Workstation, .20.13)** — nieaktywny.

### Sieć
- `192.168.10.0/24` VLAN 10 — infrastruktura (Pi-hole, Tailscale)
- `192.168.20.0/24` VLAN 20 — serwery, VM, usługi
- `192.168.30.0/24` VLAN 30 — zarządzanie sprzętem (iDRAC/iLO, IoT)
- VLAN 40/50/60 — **planowane, niewdrożone**
- Adresacja `.20.x`: `.1` pfSense · `.10–.49` serwery fizyczne · `.50–.99` VM · `.100–.200` DHCP

### Maszyny wirtualne — `qm list` z 2026-08-25 (13 VM, brak kontenerów LXC)
| VMID | Nazwa | Status | RAM MB | Dysk GB | IP wg repo | Rola |
|---|---|---|---|---|---|---|
| 100 | Ngnix | stopped | 2 048 | 32 | .20.55 | Reverse proxy — **NIEDOKOŃCZONY** |
| 101 | ollama-ai-01 | stopped | 262 144 | 500 | .20.57 | AI / LLM |
| 102 | grafana | running | 4 096 | 40 | .20.54 | Dashboardy |
| 103 | Immich | running | 16 384 | 50 | .20.53 | Zdjęcia |
| 104 | Unifi | stopped | 4 096 | 72 | .20.59 | UniFi Controller |
| 105 | Prometheus | running | 4 096 | 100 | .20.52 | Metryki |
| 106 | fitnessapp | stopped | 8 192 | 140 | .20.50 | Aplikacja dietetyczna |
| 107 | fitnessapp-db | stopped | 16 384 | 250 | .20.51 | Baza fitnessapp |
| 108 | openclaw | stopped | 16 384 | 100 | .20.56 | rola nieopisana |
| 110 | Kali | stopped | 8 192 | 50 | ??? | nauka / laboratorium |
| 109 | Wazuh | running | 16 384 | 200 | .20.58 | SIEM |
| 111 | NetMap | running | 4 096 | 32 | ??? | monitoring sieci i VM |
| 112 | UbuntuLerningDesktop | stopped | 8 192 | 60 | ??? | nauka / desktop |

**Ważne:** status `stopped` **nie oznacza awarii** — te maszyny są po prostu wyłączone i wg właściciela działają poprawnie. Jedyny niedziałający/niedokończony element na Proxmoxie to **Nginx (VM 100)**.

Suma przydzielonego RAM ≈ 362 GB przy 512 GB fizycznych — brak overcommitu.

---

## 4. Rozjazdy repo ↔ rzeczywistość (do poprawienia w dokumentacji)

| # | Repo mówi | Jest naprawdę |
|---|---|---|
| S1 | R410 = TrueNAS primary storage | R410 wycofany |
| S2 | R730 = TrueNAS „PBS tbc" | R730 = jedyny i główny NAS |
| S3 | R320 = „role TBD" | R320 wyłączony |
| S4 | 3 hosty TrueNAS | tylko jeden — R730 |
| S7 | R730 ma `.12`, R410 ma `.11` | **R730 ma `.11`** — przejął adres po R410 |
| S8 | brak pfSense na liście urządzeń | pfSense = osobny PC z 2× NIC |
| S9 | 10 VM (100–109) | **13 VM** (100–112) |
| S10 | Prometheus/Grafana „planowane na Raspberry Pi" | działają jako VM na Proxmoxie |
| S11 | Proxmox VE 9.2.2 | **PVE 9.2.4** |

Dodatkowo: repo wspomina historyczną płaską sieć `192.168.8.0/24` — pozostałości do wyczyszczenia.

---

## 5. Projekty do dokończenia / naprawy (realizacja PO domknięciu dokumentacji)

| # | Projekt | Typ | Stan |
|---|---|---|---|
| P1 | **Nginx (VM 100)** | niedokończony | Cel wg pamięci właściciela (**niepotwierdzony**): reverse proxy, żeby wchodzić na usługi po nazwie w domenie zamiast po IP. Właściciel nie pamięta szczegółów → **odtworzyć z `/etc/nginx/` na VM 100, nie zgadywać.** Do ustalenia: domena, lista usług, HTTPS. |
| P2 | **Prometheus/Grafana nie pokazuje danych z TrueNAS** | naprawa | Problem pojawił się **po migracji NAS na R730**. **Hipoteza do weryfikacji, nie diagnoza:** target w Prometheusie wskazuje na stary adres/hosta (R410 miał `.11`, R730 przejął `.11`, wcześniej miał `.12`), albo eksporter po stronie TrueNAS jest wyłączony. Priorytet wysoki. |
| P3 | **Ansible** | niedokończony | Zakres do ustalenia. |
| P4 | **Suricata** | niedokończony | Do ustalenia: gdzie miała działać (pfSense? osobna VM?) i w jakim trybie. |

Właściciel zapowiedział, że takich projektów jest więcej — lista będzie uzupełniana.

---

## 6. RYZYKA

| # | Ryzyko | Opis |
|---|---|---|
| R1 | **BRAK BACKUPÓW** | Fizycznie nie istnieją żadne kopie zapasowe. **Przed jakąkolwiek naprawą zrób minimum: backup konfiguracji pfSense (XML), `vzdump` kluczowych VM, eksport konfiguracji TrueNAS.** |
| R2 | **Repo jest publiczne** | Zawiera adresy MAC, plan VLAN, mapę portów, adresację. To nie są sekrety, ale to pełna mapa sieci. Decyzja publiczne/prywatne — otwarta. |
| R3 | Dokumentacja rozjechana z rzeczywistością | Patrz sekcja 4. |

---

## 7. Pytania otwarte

1. IP dla VM 110 (Kali), 111 (NetMap), 112 (UbuntuLerningDesktop).
2. Czy `192.168.20.12` i `.13` są zwolnione w pfSense po wycofanych maszynach?
3. Czy static lease w pfSense dla `.11` wskazuje na MAC R730 (`00:0a:f7:86:6d:85`)?
4. Pule, datasety i udziały na TrueNAS R730.
5. Czy PBS (Proxmox Backup Server) faktycznie powstał?
6. Kontenery Docker wewnątrz VM — brak jakiegokolwiek spisu.
7. Czy Buffalo TS-XL, Cisco switch i Optiplex/Tailscale nadal są w użyciu?
8. Urządzenia spoza serwerowni: IoT, kamery, drukarki, smart home?
9. Pełna mapa portów obu switchy (obecna w repo ma tylko 6 pozycji i brakuje w niej R730, Raspberry Pi, Optiplex).
10. Model/specyfikacja PC z pfSense.
11. Jaka domena jest używana w homelabie?
12. Treść otwartego Issue #1 w repo.
13. **Jakie jeszcze działające usługi istnieją, których nie ma w repo** — właściciel zapowiedział, że jest ich kilka i poda je po kolei.

---

## 8. Gdzie skończyliśmy i co dalej

**2026-08-25, Claude Code:** repo sklonowane, `origin` OK, uprawnienia do push potwierdzone (branch `claude/new-session-xm3ca7` utworzony na GitHubie, wcześniej istniał tylko lokalnie). `PLAN.md` i `CLAUDE.md` dodane do repo (po polsku — patrz zasada językowa w sekcji 2 pkt 10).

Plan na najbliższą sesję:
1. Właściciel podaje po kolei pozostałe działające usługi/urządzenia — Ty aktualizujesz `PLAN.md` na bieżąco.
2. Gdy lista będzie kompletna → przygotować komplet poprawionych plików do repo (`homelab-summary.md`, `inventory.md`, `network-summary.md`, `vm-inventory.md`) — **po angielsku**, zgodnie z `CONVENTIONS.md`.
3. Dopiero potem: backup (R1), następnie projekty P2 → P1 → P3/P4, każdy z runbookiem (treść runbooka po angielsku).
