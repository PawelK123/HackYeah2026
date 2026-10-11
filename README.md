# ♿ Ścieżka dla Ciebie

**Ścieżka dla Ciebie** to aplikacja webowa do planowania tras pieszych i sprawdzania dostępności miejsc w Krakowie dla osób poruszających się na wózkach inwalidzkich oraz rodziców z wózkami dziecięcymi.

Projekt powstał w ramach **HackYeah 2026** w kategorii **Kraków bez barier oraz Smart City**.

## 👥 Zespół projektowy

- Michał — [@micchask](https://github.com/micchask)
- Ignacy — [@Bleklajtnik](https://github.com/Bleklajtnik)
- Paweł — [@PawelK123](https://github.com/PawelK123)
- Gustaw — [@gustawito](https://github.com/gustawito)
- Miłosz — [@mwiktorowicz](https://github.com/mwiktorowicz)

## 🎥 Materiały projektowe

- **Film prezentujący projekt:** [Zobacz na YouTube](https://www.youtube.com/watch?v=BnIoNk-IPlw)
- **Prezentacja projektu:** [Otwórz prezentację PDF](https://cdn.discordapp.com/attachments/1555890962394513501/1556224968625496084/HackYeah20202620Pitch20Deck_10MB.pdf?backend=b2&ex=6acc9d71&is=6acb4bf1&hm=5a477ab73b7c0296d8190f97a7e20e7fd10c5bcf9993a57851f0f1f690531fd8&)

---

*Tworzymy Kraków bardziej dostępny dla wszystkich.*

## Problem

Popularne mapy liczą najkrótszą trasę pieszą i nie sprawdzają, czy da się nią przejechać na wózku.
Na trasie Rynek → Wawel najkrótszy wariant ma **1,07 km, 2 odcinki schodów i 764 m bruku**.
Trasa bez schodów istnieje (**1,70 km, 222 m bruku**), ale nikt jej nie pokazuje.

Dane o dostępności są rozproszone (OpenStreetMap, deklaracje dostępności w BIP, wiedza mieszkańców),
niepełne i rzadko potwierdzane. Brak informacji bywa traktowany jak „dostępne”, a bariery chwilowe
(niedziałająca winda, remont chodnika) nie trafiają nigdzie.

## Rozwiązanie

- **Routing pod potrzeby użytkownika.** Graf chodników z OSM; koszt odcinka zależy od schodów,
  krawężników, nachylenia i nawierzchni. Za wysoki krawężnik lub schody bez rampy = odcinek
  nieprzejezdny dla danego profilu.
- **Profile jako preferencje, nie diagnozy.** Wózek, wózek dziecięcy, senior, turysta, bez profilu.
  Każdy profil to zestaw ustawień, które można zmienić. Nie pytamy o niepełnosprawność.
- **Warianty tras z wyjaśnieniem.** Najbardziej dostępna, najkrótsza, kompromis; porównanie
  (długość, czas, schody, bruk, wynik dostępności) i opis krok po kroku.
- **Pochodzenie każdej informacji.** Każda cecha miejsca i odcinka ma źródło, datę pobrania
  lub potwierdzenia, pewność i status: potwierdzone, niepotwierdzone, sprzeczne, nieaktualne.
- **Zgłoszenia mieszkańców.** Anonimowe zgłoszenie bariery, potwierdzanie przez innych,
  a potwierdzona bariera zmienia trasę.
- **Widok dla miasta.** Dashboard najczęstszych problemów i mapa braków danych.

## Dane

| Źródło | Co daje | Zakres w obszarze demo |
|---|---|---|
| OpenStreetMap (Overpass) | chodniki, schody, nawierzchnia, krawężniki, miejsca, ławki, przewijaki | 7 515 odcinków, 9 108 miejsc, 1 393 ławki |
| NMT GUGiK 1 m | nachylenie odcinków | 5 308 odcinków |
| Deklaracje dostępności (BIP) | windy, toalety, wejścia w instytucjach | 32 instytucje |
| Zgłoszenia użytkowników | bariery chwilowe | na bieżąco |
| Dane przykładowe | parkingi OzN, wydarzenia | oznaczone w aplikacji |

Obszar demo: Stare Miasto, Wawel, Kazimierz. Dane są zapisane w repo, aplikacja działa
bez dostępu do zewnętrznych API (`make seed`). Szczegóły: [`docs/data-sources.md`](docs/data-sources.md).

## Wyniki

- Trasa Rynek → Wawel dla wózka: **0 schodów, wynik dostępności 70/100**, wobec 2 odcinków schodów
  na trasie najkrótszej.
- Dane sprzeczne pokazujemy wprost: Bazylika Mariacka – OSM „dostępne”, dane ręczne „częściowo”,
  status „źródła się nie zgadzają”.
- Brak danych nigdy nie jest „dostępne”; odcinek bez informacji o nawierzchni dostaje niższą
  pewność i ostrzeżenie.
- Awaria źródła (np. Overpass) nie wyłącza aplikacji – działa na ostatnim snapshocie.
- Dostępność samej aplikacji (WCAG 2.2 AA): **axe 0 problemów** w 14 widokach, tryb jasny i ciemny;
  **Lighthouse 100/100**.
- **182 testy backendu i 156 frontendu**, CI na każdym PR.

## Uruchomienie

    docker compose up --build -d
    make seed

Aplikacja: http://localhost:5173 · API: http://localhost:8000/docs

**Wersja online:** Obecnie niedostępna (instrukcja wdrożenia: [`docs/deploy-render.md`](docs/deploy-render.md)).

Bez `make`: [`docs/development.md`](docs/development.md). Scenariusz demo (3 min):
[`docs/demo-scenario.md`](docs/demo-scenario.md).

## Architektura

Pozyskiwanie danych (providery, snapshot, normalizacja) jest oddzielone od prezentacji
(API FastAPI + frontend React/MapLibre). Nowe źródło danych = nowa klasa providera.
Nowe miasto = plik `cities/<miasto>.yaml` i `make seed-refresh`.
Szczegóły: [`docs/architecture.md`](docs/architecture.md).

Stack: React, TypeScript, MapLibre · FastAPI, osmnx, networkx · PostgreSQL + PostGIS · Docker Compose.

## Odniesienie do wyzwań

**Kraków bez barier.** Grupa docelowa: osoby na wózkach i rodzice z wózkami. Prototyp pokazuje
schody, krawężniki, nachylenie, nawierzchnię, toalety i miejsca odpoczynku ze źródłem, datą
i pewnością. Windy, szerokość wejść i progi – dane częściowe (tylko tam, gdzie podaje je źródło).
Nie wymaga systemów UMK ani ręcznego utrzymywania bazy przez miasto.
Model biznesowy: [`docs/business-model.md`](docs/business-model.md) · bezpieczeństwo i prywatność:
[`docs/security-privacy.md`](docs/security-privacy.md) · utrzymanie danych:
[`docs/data-operations.md`](docs/data-operations.md).

**Smart City.** Rozwiązanie łączy planowanie podróży, dostępność przestrzeni, komunikację mieszkańców
z miastem (zgłoszenia) i wykorzystanie danych miejskich do decyzji (dashboard, mapa braków danych).

## Ograniczenia i dalsze prace

- Obszar demo ograniczony do Starego Miasta, Wawelu i Kazimierza.
- Parkingi OzN i wydarzenia – dane przykładowe.
- Moderacja zgłoszeń bez logowania (tylko przez API).
- Dalsze prace: paszport dostępności miejsca z danymi o drzwiach i progach, prowadzenie
  do dostępnego wejścia, tryb „samochód + wózek” z parkingami OzN, komunikacja miejska
  (GTFS), kolejne miasta.

## Licencje

Dane: OpenStreetMap (ODbL, wymagana atrybucja), NMT GUGiK, deklaracje dostępności (informacja
publiczna). Podkłady mapy: OSM, Esri World Imagery, CARTO – na warunkach dostawców.
Geokoder: Photon (komoot).

## Praca na hackathonie i AI

Kod powstał w trakcie HackYeah (pierwszy commit 3.10.2026, 12:09). Przy kodzie i dokumentacji
korzystaliśmy z asystenta AI (Claude Code); zmiany przechodziły review i testy CI.

## Dla deweloperów

[`docs/development.md`](docs/development.md)
