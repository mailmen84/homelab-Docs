# Plan działania — Audyt i uporządkowanie Homelabu

**Wersja:** 0.6
**Ostatnia aktualizacja:** 2026-08-25
**Repo:** https://github.com/mailmen84/homelab-Docs (PUBLICZNE)

---

## 0. Zasady pracy

1. **Maksymalnie 3 kroki na raz**, potem potwierdzenie „czy wykonane poprawnie?".
2. **Bez zgadywania** — brak informacji = pytanie. Nieznane = `???`.
3. Jeśli istnieje lepsza droga — propozycja + uzasadnienie, decyzja po Twojej stronie.
4. Ten plik = stan projektu. Aktualizowany po każdym etapie.
5. Nic nie usuwamy bez potwierdzenia.
6. **Dane z repo traktujemy jako NIEPOTWIERDZONE** do czasu weryfikacji na żywym sprzęcie.
7. **Każda naprawa = osobny dokument troubleshooting** w `docs/60-runbooks/`. Zapisujemy: objaw, diagnozę, co sprawdzone, co zadziałało, jak zweryfikowano. Cel: gdy problem wróci, jest gotowa ścieżka.
8. **Kolejność projektu (decyzja właściciela):** najpierw **kompletna i poprawna dokumentacja**, dopiero potem naprawy i dokańczanie projektów.

**Legenda:** `[ ]` do zrobienia · `[~]` w trakcie · `[x]` zrobione · `[!]` problem · `[?]` decyzja
**Wiarygodność danych:** `POTWIERDZONE` (sprawdzone na sprzęcie) · `Z-REPO` (tylko z dokumentacji) · `SPRZECZNE`

---

## 1. Cel projektu

- Zinwentaryzować wszystkie serwery, urządzenia i usługi.
- Naprawić niedziałające serwisy.
- Dokończyć rozpoczęte, nieukończone projekty.
- Doprowadzić repo GitHub do stanu zgodnego z rzeczywistością.

---

## 2. RYZYKA — do decyzji na starcie

| # | Ryzyko | Opis | Status |
|---|---|---|---|
| R1 | **BRAK BACKUPÓW** | Fizycznie nie istnieją żadne kopie zapasowe. Każda naprawa wykonywana jest bez siatki bezpieczeństwa. | `[!]` |
| R2 | **Repo publiczne** | W publicznym repo są adresy MAC, plan VLAN, mapa portów, IP. Nie są to sekrety, ale to pełna mapa sieci. Do decyzji: zostawić publiczne czy przełączyć na prywatne. | `[?]` |
| R3 | **Dokumentacja rozjechana z rzeczywistością** | Repo opisuje stan, który już nie obowiązuje (patrz sekcja 3). | `[!]` |

**Rekomendacja:** przed Fazą 6 (naprawy) minimum = kopia konfiguracji: backup pfSense (XML), `vzdump` kluczowych VM, eksport konfiguracji TrueNAS.

---

## 3. SPRZECZNOŚCI repo vs. rzeczywistość (stan na 2026-08-25)

| # | Repo mówi | Rzeczywistość (od właściciela) | Do zrobienia |
|---|---|---|---|
| S1 | R410 = TrueNAS, **primary storage** | R410 **wycofany** z roli NAS | ROZSTRZYGNIĘTE — do poprawy w repo |
| S2 | R730 = TrueNAS „PBS to be confirmed" | R730 = **jedyny i główny NAS** | ROZSTRZYGNIĘTE — do poprawy w repo. Osobno: czy PBS powstał? |
| S3 | R320 = „role TBD" | R320 **wyłączony** | ROZSTRZYGNIĘTE — do poprawy w repo, decyzja co dalej z maszyną |
| S4 | 3 hosty TrueNAS (R410, R730, truenas-pc) | **tylko R730** | ROZSTRZYGNIĘTE — usunąć R410 i truenas-pc z aktywnych |
| S7 | R730 ma `192.168.20.12`, R410 ma `.11` | R730 ma **`192.168.20.11`** | Potwierdzić czy `.12` jest zwolnione |
| S8 | Brak pfSense na liście urządzeń | pfSense = **osobny PC z 2× NIC** | ROZSTRZYGNIĘTE — dodać do repo |
| S9 | Repo zna 10 VM (100–109) | Realnie **13 VM** (100–112) | Dodać 110 Kali, 111 NetMap, 112 UbuntuLerningDesktop |
| S10 | Prometheus/Grafana „planowane na Raspberry Pi" | Działają jako **VM na Proxmox** | Poprawić w repo |
| S11 | Proxmox VE 9.2.2 | **PVE 9.2.4** | ROZSTRZYGNIĘTE — poprawić w repo |
| S5 | pfSense = brama `192.168.20.1` | **Nie ma go na liście urządzeń** — nie wiadomo na czym stoi | Ustalić hosta pfSense |
| S6 | Mapa portów switcha ma 6 pozycji | Brakuje R730, Raspberry Pi, Optiplex, Cisco switch | Zrobić pełną mapę portów |

---

## 4. Rejestr sprzętu (v0.2 — dane `Z-REPO`, niezweryfikowane)

| # | Nazwa | Sprzęt | IP | Rola wg repo | Stan rzeczywisty | Wiarygodność |
|---|---|---|---|---|---|---|
| 1 | proxmox | HP DL380 Gen9 | 192.168.20.10 | Proxmox VE 9.2.2, główny compute | działa | Z-REPO |
| 2 | truenas-r730 | Dell R730 | **192.168.20.11** | TrueNAS Scale | **jedyny aktywny NAS** | POTWIERDZONE |
| 3 | ~~truenas-r410~~ | Dell R410 | — | TrueNAS (primary) | **WYCOFANY** | POTWIERDZONE |
| 4 | ~~R320~~ | Dell R320 | — | role TBD | **WYŁĄCZONY** | POTWIERDZONE |
| 5 | ~~truenas-pc~~ | PC Workstation | — | TrueNAS Scale | **NIEAKTYWNY** | POTWIERDZONE |
| 6 | pihole | Raspberry Pi 3+ | 192.168.10.2 | Pi-hole DNS | działa | Z-REPO |
| 7 | tailscale | Dell Optiplex | 192.168.10.130 | Tailscale subnet router | ??? | Z-REPO |
| 8 | pfSense | **osobny PC, 2× NIC** | 192.168.20.1 | brama + DHCP | działa | POTWIERDZONE |
| 9 | switch core | Buffalo BS-MP2012 | ??? | switch zarządzalny | ??? | Z-REPO |
| 10 | switch 2 | Cisco (model ???) | ??? | VLAN-capable | ??? | Z-REPO |
| 11 | NAS 2 | Buffalo TS-XL | ??? | drugi NAS | ??? | Z-REPO |
| 12 | UPS / drukarki / IoT / kamery | ??? | ??? | — | ??? | brak danych |

---

## 5. Sieć (wg repo)

| Podsieć | VLAN | Przeznaczenie |
|---|---|---|
| 192.168.10.0/24 | 10 | Infrastruktura (Pi-hole, Tailscale) |
| 192.168.20.0/24 | 20 | Serwery, VM, usługi |
| 192.168.30.0/24 | 30 | Zarządzanie sprzętem (iDRAC/iLO, IoT) |
| — | 40/50/60 | **planowane, nie wdrożone** |

Plan adresacji w `.20.x`: `.1` pfSense · `.2–.9` rezerwa · `.10–.49` serwery fizyczne · `.50–.99` VM-y · `.100–.200` DHCP · `.201–.254` rezerwa
Historycznie: płaska sieć `192.168.8.0/24` — część stron w repo nadal ją wymienia.

---

## 6. Rejestr VM na Proxmox — `POTWIERDZONE` (`qm list`, 2026-08-25)

| VMID | Nazwa | Status | RAM (MB) | Dysk (GB) | IP wg repo | Rola | W repo? |
|---|---|---|---|---|---|---|---|
| 100 | Ngnix | stopped | 2 048 | 32 | .20.55 | Reverse proxy — **NIEDOKOŃCZONY** `[!]` | tak |
| 101 | ollama-ai-01 | stopped | **262 144** | 500 | .20.57 | AI / LLM | tak |
| 102 | grafana | **running** | 4 096 | 40 | .20.54 | Dashboardy | tak |
| 103 | Immich | **running** | 16 384 | 50 | .20.53 | Zdjęcia | tak |
| 104 | Unifi | stopped | 4 096 | 72 | .20.59 | UniFi Controller | tak |
| 105 | Prometheus | **running** | 4 096 | 100 | .20.52 | Metryki | tak |
| 106 | fitnessapp | stopped | 8 192 | 140 | .20.50 | Aplikacja dietetyczna | tak |
| 107 | fitnessapp-db | stopped | 16 384 | 250 | .20.51 | Baza fitnessapp | tak |
| 108 | openclaw | stopped | 16 384 | 100 | .20.56 | ??? | tak (bez roli) |
| 109 | Wazuh | **running** | 16 384 | 200 | .20.58 | SIEM | tak |
| 110 | Kali | stopped | 8 192 | 50 | **???** | Nauka / laboratorium | **NIE** |
| 111 | NetMap | **running** | 4 096 | 32 | **???** | Monitoring sieci i VM | **NIE** |
| 112 | UbuntuLerningDesktop | stopped | 8 192 | 60 | **???** | Nauka / desktop | **NIE** |

**Podsumowanie:** 13 VM (repo znało 10). Running: 5 (grafana, Immich, Prometheus, Wazuh, NetMap). Stopped: 8.
**Ważne:** „stopped" ≠ „zepsute". Wg właściciela maszyny są po prostu wyłączone. Które są celowo wyłączone, a które to niedokończone projekty — do ustalenia.

**Obserwacje techniczne:**
- Suma przydzielonego RAM ≈ **362 GB** przy **512 GB** fizycznie w DL380 → zapas ~150 GB, brak overcommitu. `[x]` zamknięte.
- Suma dysków bootowalnych ≈ **1 626 GB**.
- `NetMap` ma PID 4719, pozostałe działające ~241–242 tys. → NetMap działa od dawna, reszta uruchomiona niedawno.
- `Ngnix` (reverse proxy) jest **zatrzymany** — czyli usługi nie są obecnie publikowane przez proxy.
- Prometheus i Grafana działają **jako VM**, a repo mówiło o nich jako „planowanych na Raspberry Pi" — do poprawy w repo.
- **Brak kontenerów LXC** — `pct list` zwraca pustą listę. `[x]` zamknięte.
- Nadal brak spisu **kontenerów Docker wewnątrz VM**.

**Wersja Proxmox `POTWIERDZONE`:** `pve-manager/9.2.4`, jądro `7.0.14-4-pve`. Repo podaje 9.2.2 → do poprawy.

**Stan usług wg właściciela:** wszystkie VM poza `100 Ngnix` działają poprawnie. **Nginx nigdy nie został do końca skonfigurowany** — to pierwszy potwierdzony niedokończony projekt.

---

## 6b. Projekty do dokończenia i naprawy (rejestr)

Kolejność realizacji: **dopiero po zamknięciu dokumentacji.**

| # | Projekt | Typ | Opis / stan | Priorytet | Status |
|---|---|---|---|---|---|
| P1 | **Nginx (VM 100)** | NIEDOKOŃCZONY | Nigdy nie skonfigurowany do końca. Cel wg pamięci właściciela (**niepotwierdzony**): reverse proxy, żeby wchodzić na usługi po nazwie w domenie zamiast po IP. Do ustalenia: domena, lista usług, czy HTTPS/certyfikaty. | średni | `[ ]` |
| P2 | **Prometheus / Grafana — brak danych z TrueNAS** | NAPRAWA | Po migracji NAS na R730 monitoring nie pokazuje danych z TrueNAS. **Hipoteza do weryfikacji:** cel scrape'owania w Prometheusie wskazuje na stary adres/hosta (R410 `.11` → R730 przejął `.11`, wcześniej `.12`) albo wyłączony eksporter po stronie TrueNAS. | wysoki | `[ ]` |
| P3 | **Ansible** | NIEDOKOŃCZONY | Rozpoczęty, nieukończony. Zakres do ustalenia. | średni | `[ ]` |
| P4 | **Suricata (IDS/IPS)** | NIEDOKOŃCZONY | Rozpoczęty, nieukończony. Do ustalenia: gdzie miała działać (pfSense? osobna VM?) i w jakim trybie. | średni | `[ ]` |
| P5 | *(rezerwa — kolejne projekty do dopisania po powrocie do domu)* | — | — | — | `[ ]` |

---

## 6c. Szablon dokumentu troubleshooting

Każda naprawa trafia do `docs/60-runbooks/RB-<numer>-<nazwa>.md` w tej strukturze:

```
# RB-001 — <krótki tytuł>
Data: | Dotyczy: <host/usługa> | Status: rozwiązane / obejście / otwarte

## 1. Objaw
Co dokładnie nie działało, od kiedy, co widać w UI/logach.

## 2. Środowisko
Wersje, IP, hosty, powiązane usługi.

## 3. Co sprawdzono
| # | Sprawdzenie | Komenda | Wynik |

## 4. Przyczyna
Ustalona przyczyna źródłowa (albo: nieustalona).

## 5. Rozwiązanie
Kroki naprawcze wraz z komendami.

## 6. Weryfikacja
Jak potwierdzono, że działa.

## 7. Jak cofnąć
## 8. Zapobieganie / wnioski
```

**Uwaga:** treść samego runbooka (`docs/60-runbooks/*.md`) pisana jest **po angielsku**, zgodnie z `CONVENTIONS.md` — ten szablon jest tylko roboczą notatką w PLAN.md.

---

## 7. Fazy projektu

- **Faza 1 — Weryfikacja sprzętu** `[~]` — potwierdzić co faktycznie żyje, wypełnić luki z sekcji 4.
- **Faza 2 — Sieć** `[ ]` — ustalić hosta pfSense, pełna mapa portów obu switchy, weryfikacja VLAN.
- **Faza 3 — Usługi** `[ ]` — realny spis VM/LXC/Docker/apek TrueNAS, porty, do czego służą.
- **Faza 4 — Ocena stanu** `[ ]` — każda usługa: `OK` / `BROKEN` / `UNFINISHED` / `UNUSED` / `UNKNOWN`.
- **Faza 5 — Backup + priorytety** `[ ]` — najpierw kopie, potem kolejność napraw.
- **Faza 6 — Naprawy i dokańczanie** `[ ]` — wg rejestru w sekcji 6b, każda pozycja z runbookiem wg szablonu 6c.
- **Faza 7 — Aktualizacja repo** `[ ]`.
- **Faza 8 — Higiena** `[ ]` — monitoring, aktualizacje, zarządzanie sekretami.

---

## 8. Zmiany na produkcji — „jak cofnąć"

| Data | Co zmienione | Gdzie | Jak cofnąć |
|---|---|---|---|
| — | — | — | — |

---

## 9. Backlog

| # | Zadanie | Typ | Priorytet | Status |
|---|---|---|---|---|
| 1 | Ustalić na czym działa pfSense | LUKA | wysoki | `[x]` |
| 2 | Zweryfikować VM na Proxmox (`qm list`) | INWENTARYZACJA | wysoki | `[x]` |
| 2a | Ustalić role VM 110/111/112 | INWENTARYZACJA | wysoki | `[x]` |
| 2a2 | Ustalić **IP** dla VM 110/111/112 | INWENTARYZACJA | średni | `[ ]` odłożone (praca z telefonu) |
| 2b | Podzielić zatrzymane VM na celowe vs zepsute | OCENA | wysoki | `[x]` — tylko Nginx niedokończony |
| 2c | Spis LXC | INWENTARYZACJA | wysoki | `[x]` — brak LXC |
| 2c2 | Spis kontenerów Docker wewnątrz VM | INWENTARYZACJA | średni | `[ ]` |
| 2d | Potwierdzić wersję Proxmox | DOKUMENTACJA | średni | `[x]` — 9.2.4 |
| 2e | RAM DL380 | RYZYKO | średni | `[x]` — 512 GB, OK |
| 2f | Projekty do naprawy/dokończenia → przeniesione do sekcji 6b | — | — | `[~]` |
| 2g | Uzgodnić szablon runbooka i założyć `docs/60-runbooks/` | PROCEDURA | wysoki | `[~]` propozycja gotowa |
| 3 | Potwierdzić stan R730 (pule, datasety, czy migracja skończona) | INWENTARYZACJA | wysoki | `[ ]` |
| 3a | Poprawić w repo: jeden TrueNAS = R730 @ .11; usunąć R410, R320, truenas-pc | DOKUMENTACJA | wysoki | `[~]` |
| 3b | Ustalić czy `192.168.20.12` i `.13` są zwolnione w pfSense | SIEĆ | średni | `[ ]` |
| 4 | Stworzyć pierwszy realny backup | RYZYKO | wysoki | `[ ]` |
| 5 | Ustalić los R410 i R320 | DECYZJA | średni | `[ ]` |
| 6 | Zaktualizować repo o sprzeczności S1–S6 | DOKUMENTACJA | średni | `[ ]` |
| 7 | Sprawdzić otwarte Issue #1 w repo | DOKUMENTACJA | niski | `[ ]` |
| 8 | Decyzja: repo publiczne czy prywatne | DECYZJA | średni | `[ ]` |

---

## 10. Dziennik sesji

| Data | Co zrobiono | Następny krok |
|---|---|---|
| 2026-08-25 | Utworzenie planu, ustalenie procedury | — |
| 2026-08-25 | Przegląd repo, wykrycie 6 sprzeczności, wstępny rejestr sprzętu i VM | Weryfikacja na żywym sprzęcie (Faza 1) |
| 2026-08-25 | Ustalono zasadę runbooków po każdej naprawie + kolejność: dokumentacja przed naprawami. Zarejestrowano projekty P1–P4 (Nginx, Prometheus/Grafana↔TrueNAS, Ansible, Suricata). Aktualizacja `homelab-summary.md` **wstrzymana** do czasu spisania pozostałych usług | Właściciel dośle listę pozostałych działających rzeczy spoza repo |
| 2026-08-25 | `pveversion` = 9.2.4, `pct list` pusty (brak LXC). DL380 = 512 GB RAM. Jedyny niedokończony projekt na Proxmoxie: Nginx | Inwentaryzacja TrueNAS R730 + sieci |
| 2026-08-25 | `qm list`: 13 VM zamiast 10. pfSense = osobny PC z 2× NIC. Wykryte S8–S11 | Ustalić role nowych VM + LXC/Docker |
| 2026-08-25 | Rozstrzygnięte S1–S4: jedyny TrueNAS to R730 @ 192.168.20.11. R410/R320/truenas-pc wycofane. Ustalono, że statusy „needs config" w repo = niedokończona dokumentacja, nie awarie | Potwierdzić hosta pfSense + realny stan VM |
| 2026-08-25 | Claude Code: sprawdzono klon repo, remote, uprawnienia push (branch `claude/new-session-xm3ca7` utworzony na GitHubie). Dodano `PLAN.md` i `CLAUDE.md` do repo (po polsku, dokumenty robocze) | Zbierać od właściciela listę pozostałych działających usług spoza repo, potem aktualizacja `homelab-summary.md` i reszty dokumentacji (po angielsku) |

---

## 11. Pytania otwarte

1. ~~Na czym działa pfSense?~~ — **ODPOWIEDŹ: osobny PC z dwiema kartami sieciowymi**
2. Czy `192.168.20.12` i `.13` są zwolnione po wycofanych maszynach?
3. Czy Buffalo TS-XL, Cisco switch, Optiplex/Tailscale nadal są w użyciu?
4. Czy są urządzenia spoza serwerowni (IoT, kamery, drukarki, smart home)?
5. Czy PBS (Proxmox Backup Server) faktycznie powstał na R730?
6. Jak wprowadzamy zmiany do repo — ja przygotowuję gotowe pliki .md, Ty commitujesz?
7. ~~Role VM 110/111/112~~ — **ODPOWIEDŹ: Kali i Ubuntu = nauka, NetMap = monitoring sieci i VM.** IP nadal do uzupełnienia.
8. ~~Które VM są zepsute?~~ — **ODPOWIEDŹ: tylko Nginx niedokończony, reszta działa.**
9. ~~Wersja Proxmox?~~ — **ODPOWIEDŹ: PVE 9.2.4**
10. Jaki jest model/specyfikacja PC z pfSense?
11. Jakie pule/datasety/udziały ma TrueNAS R730?
12. Co dokładnie ma robić Nginx — jakie usługi ma publikować i pod jakimi domenami? (właściciel nie pamięta — do odtworzenia z konfiguracji na VM 100)
13. Jaka domena jest używana w homelabie?
14. Gdzie miała działać Suricata i w jakim zakresie?
15. Co miał obejmować Ansible — jakie hosty i jakie zadania?
16. Jakie jeszcze działające usługi istnieją, których nie ma w repo?
