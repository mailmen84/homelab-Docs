# Storage Host Matrix

Krótki, operacyjny przegląd hostów storage bez potrzeby fizycznego dostępu do szafy.

## Cel
- Mieć jedno miejsce z aktualnym statusem hostów storage.
- Rozdzielić: co już wiemy vs co trzeba potwierdzić zdalnie.

## Hosty storage

| Host | Rola | IP | VLAN | MAC | Fizyczny port | Status |
|---|---|---:|---:|---|---|---|
| Dell R410-truenas (`truenas`) | Primary TrueNAS SCALE | `192.168.20.11` | 20 | `5c:ed:8c:1b:6e:6c` | 3 | active (doc refreshed from screenshots: TrueNAS 25.10.2.1, pool `tank` ONLINE) |
| Dell R730 (`truenas-r730`) | Secondary TrueNAS / planned PBS (TBC) | `192.168.20.12` | 20 | `00:0a:f7:86:6d:85` | TBD | active (doc refreshed from screenshots: TrueNAS 25.10.2 Goldeye, pool `tank` ONLINE, RAIDZ1+LOG mirror) |
| PC Workstation (`truenas-pc`) | Additional TrueNAS host | `192.168.20.13` | 20 | `04:d9:f5:0f:43:ca` | TBD | active |

## Co można uzupełnić zdalnie (bez fizycznego dostępu)

1. Wersja TrueNAS na każdym hoście.
2. Lista pooli i ich health.
3. Datasets i kluczowe SMB/NFS shares.
4. Snapshot tasks i replika (jeśli istnieją).
5. Potwierdzenie roli R730 (TrueNAS-only vs PBS target).

## Co wymaga fizycznego potwierdzenia później

1. Dokładne porty switcha dla R730 i `truenas-pc`.
2. Realna prędkość linku (1Gb/10Gb) dla każdego hosta.
3. Oznaczenia kabli i patch panel (jeśli jest).

## Następny krok dokumentacyjny
Po zebraniu danych zdalnie:
- zaktualizować `docs/10-hardware/servers/r410-truenas.md`
- zaktualizować `docs/10-hardware/servers/r730-truenas.md`
- zaktualizować `docs/10-hardware/servers/pc-truenas.md`
- doprecyzować `docs/30-storage/README.md` i runbook backupów.
