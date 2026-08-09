# shelly-elpris-dk

## DK spotpris-styring til Shelly-enheder

**shelly-elpris-dk** er et DK-only Shelly-script til styring af Shelly outputs baseret på danske timebaserede spotpriser, manuel dansk nettarif, energiafgift og moms.

Scriptet er beregnet til de danske prisområder **DK1** og **DK2** og bruger den danske spotpris-API fra **elprisenligenu.dk**. Denne version er begrænset til danske prisområder og danske opsætningsværdier.

[Licens: AGPL v3](https://www.gnu.org/licenses/agpl-3.0)

---

## Aktuel scriptversion

| Punkt | Værdi |
|---|---|
| Scriptnavn | `shelly-elpris-dk.js` |
| Runtime-version | `3.2.3_10` |
| Build-header | `shelly-elpris-dk 3.2.3 build 10` |
| Understøttede prisområder | `DK1`, `DK2` |
| Pris-API | `https://www.elprisenligenu.dk/api/v1/prices/` |
| Prisfelt | `DKK_per_kWh` |

---

## Hovedfunktioner

- **DK-only support** for de danske prisområder `DK1` og `DK2`.
- **Timebaserede spotpriser** fra `elprisenligenu.dk`.
- **Manuel Tarifmodel 3.0** med separate værdier for:
  - Lavlast: `00:00-06:00`
  - Højlast: `06:00-17:00`
  - Spidslast: `17:00-21:00`
  - Højlast: `21:00-24:00`
- **Manuel energiafgift** i `kr/kWh`.
- **Moms-håndtering** via særskilt momsprocentfelt.
- **Status UI**, der viser aktuel pris, spot-/gebyropdeling, Tarifmodel 3.0-værdier og energiafgift.
- **History UI** til logning af output-handlinger og statusændringer.
- **Setup UI** til region, tariffer, energiafgift, moms, outputs, fallback-timer, overrides og styringstilstand.
- **Help UI** med indbygget vejledning til de vigtigste opsætningsfelter.
- **DK-only logik** uden udenlandsk priszone-routing.

---

## Prisberegning

Scriptet beregner den effektive timepris ved at kombinere spotpris, manuel nettarif, energiafgift og moms.

```text
Effektiv pris = spotpris + Tarifmodel 3.0-nettarif + energiafgift + moms
```

Tarifperioden vælges ud fra tidspunktet på døgnet:

| Tidsperiode | Internt config-felt | Betydning |
|---|---|---|
| `00:00-06:00` | `night` | Lavlast |
| `06:00-17:00` | `day` | Højlast |
| `17:00-21:00` | `dayw` | Spidslast |
| `21:00-24:00` | `nightw` | Højlast |
| Alle timer | `tax` | Energiafgift |
| Alle timer | `vat` | Moms |

---

## Prisdatasource

DK-only versionen bruger:

```text
https://www.elprisenligenu.dk/api/v1/prices/
```

Scriptet forventer, at API’et returnerer timebaserede prisdata med feltet:

```text
DKK_per_kWh
```

Den nuværende implementering er timebaseret og forventer en normal 24-timers prisstruktur.

---

## Manuel nettarif og energiafgift

Scriptet henter ikke automatisk nettariffer fra DataHub eller fra danske netselskaber. Tarifferne skal indtastes manuelt i Setup UI’et.

Nyttig reference til at finde dit danske netselskab og nettarif:

```text
https://selectra.dk/netselskaber
```

Typiske perioder i Tarifmodel 3.0:

| Periode | Tidsrum |
|---|---|
| Lavlast | `00:00-06:00` |
| Højlast | `06:00-17:00` |
| Spidslast | `17:00-21:00` |
| Højlast | `21:00-24:00` |

Energiafgift indtastes i `kr/kWh`, for eksempel:

```text
0.90
```

Moms indtastes som procent, for eksempel:

```text
25
```

Hvis manuelt indtastede tariffer og afgifter allerede er inkl. moms, skal moms sættes til `0` for at undgå, at moms lægges på to gange.

---

## Understøttede Shelly-platforme

Scriptet er primært beregnet til Shelly Gen2-enheder og kan også fungere på udvalgte Gen3-/Gen4-enheder, hvis Matter er deaktiveret.

| Enhed | Gen2 | Gen3 | Gen4 |
|---|---:|---:|---:|
| Plus 1 | OK | Ikke testet | Ikke testet |
| Plus 1PM | OK | Ikke testet | OK |
| Plus 2PM | OK | Ikke testet | Ikke testet |
| Pro 2 | OK | Ikke testet | Ikke testet |
| Pro 3 | OK | Ikke testet | Ikke testet |
| Plug S | OK | OK | Ikke testet |
| Mini PM | Ikke testet | Viser ustabilitet | Ikke testet |

> Enhedskompatibilitet afhænger af firmwareversion, ledig script-hukommelse, aktiverede Shelly-funktioner og lokal konfiguration.

---

## Kom godt i gang

1. Tilslut Shelly-enheden til netværket.
2. Opdater Shelly-firmware til en nyere stabil version.
3. Sæt tidszone til:

```text
Europe/Copenhagen
```

4. Åbn Shelly Web UI.
5. Opret et nyt script.
6. Indsæt indholdet af:

```text
shelly-elpris-dk.js
```

7. Gem og start scriptet.
8. Åbn scriptets console og se efter output i stil med:

```text
elpris: v.3.2.3_10
elpris: URL http://<device-ip>/script/<script-id>
```

9. Åbn den viste URL i en browser.

---

## Indbygget webinterface

Shelly-enheden stiller et let HTTP UI til rådighed med fire faner.

| Fane | Beskrivelse |
|---|---|
| Status | Aktuel status, pris, tarif, energiafgift, output-state og aktiv logik |
| History | Loggede handlinger og output-ændringer |
| Setup | Region, Tarifmodel 3.0, energiafgift, moms, outputs, modes og overrides |
| Help | Indbygget vejledning til de vigtigste opsætningsfelter |

---

## Vigtige opsætningsfelter

### Region

Vælg `DK1` eller `DK2`.

### Tarifmodel 3.0

Indtast de aktuelle nettarifværdier i `kr/kWh` for de fire tarifperioder.

### Energiafgift

Indtast den aktuelle danske energiafgift i `kr/kWh`.

### VAT / moms

Indtast momsprocent. Brug `25` for normal dansk moms, hvis værdierne indtastes ekskl. moms.

### Mode

Scriptet understøtter flere styringstilstande:

- **Manual mode**: output styres manuelt.
- **Price limit**: output aktiveres, når den beregnede pris er under den valgte grænse.
- **Cheapest hours**: output aktiveres i de billigste timer i den valgte periode.

### Shelly outputs spec

Brug output `0` for et enkelt relæ, eller en kommasepareret liste som:

```text
0,1
```

### ON-time

Begrænser hvor længe output må være tændt inden for en time.

### Backup hours

Fallback-timer, der bruges hvis prisdata ikke er tilgængelige.

### Overrides

Faste timebaserede overrides kan tvinge output ON, OFF eller normal styring.

---

## KVS-konfiguration

Scriptet gemmer fælles konfiguration under:

```text
elpris
```

Instansspecifik konfiguration gemmes under:

```text
elpris-1
elpris-2
```

Ved opgradering fra ældre eller eksperimentelle builds anbefales det at rydde gamle KVS-nøgler før første start for at undgå forældede konfigurationsværdier.

---

## Noter og begrænsninger

- Scriptet er DK-only og understøtter kun `DK1` og `DK2`.
- Scriptet henter ikke danske nettariffer automatisk.
- Scriptet antager timebaseret pris- og styringslogik.
- Hvis spotpris-API’et ændrer format, kan scriptet kræve en opdatering.
- Hvis beregnede priser ser for høje eller lave ud, bør region, moms, nettarif og energiafgift kontrolleres først.

---

## Baggrund og credits

Videreudviklet af **DuckAvenger** så understøtter den de daske afgifter og nettariffer.

Udviklet og tilpasset af **Soviet9773Red** og bidragydere.

Baseret på idéer og arkitektur fra **shelly-porssisahko** af **Jussi Isotalo**.

Yderligere DK-specifik tilpasning og test: **S. Impagliazzo**.

---

## Repository

```text
https://github.com/DuckAvenger/shelly-elpris-dk
```

---

## Licens

Dette projekt er licenseret under GNU Affero General Public License v3.0.

```text
AGPL-3.0
```
