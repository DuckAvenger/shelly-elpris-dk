shelly-elpris-dk
DK spotpris-styring til Shelly-enheder
shelly-elpris-dk er et DK-only Shelly-script til styring af Shelly outputs baseret på danske timebaserede elpriser, inkl. nettarif, energiafgift og moms.
Scriptet er beregnet til de danske prisområder DK1 og DK2 og henter priser fra elpriser.org. Denne version er begrænset til danske prisområder og danske opsætningsværdier.
Licens: AGPL v3
Aktuel scriptversion
<table>
<tr>
<th>
Punkt
</th>
<th>
Værdi
</th>
</tr>
<tr>
<td>
Scriptnavn
</td>
<td>
shelly-elpris-dk.js
</td>
</tr>
<tr>
<td>
Runtime-version
</td>
<td>
3.3.0\_BRKDWN
</td>
</tr>
<tr>
<td>
Understøttede prisområder
</td>
<td>
DK1, DK2
</td>
</tr>
<tr>
<td>
Pris-API
</td>
<td>
https://elpriser.org/api
</td>
</tr>
</table>
Hovedfunktioner
DK-only support for de danske prisområder DK1 og DK2.
Automatisk netselskabs-opslag: vælg netselskab i en dropdown (filtreret efter region), og GLN/EAN-nummer samt dækningsområde vises automatisk.
Fuldt automatisk prisberegning inkl. nettarif, energiafgift og moms, hentet direkte fra elpriser.org — ingen manuel indtastning af tariffer eller afgifter.
Prisopdeling på Status-siden: samlet pris, ren spotpris, nettarif og energiafgift vises hver for sig (alt inkl. 25% moms).
Status UI, der viser aktuel pris, prisopdeling, output-tilstand og årsagen til den aktuelle styring.
History UI til logning af output-handlinger og statusændringer.
Setup UI til region, netselskab, outputs, fallback-timer, undtagelser og styringstilstand.
Help UI med indbygget vejledning til de vigtigste opsætningsfelter.
Dansk brugerflade i hele det indbyggede webinterface.
DK-only logik uden udenlandsk priszone-routing.
Prisberegning
Scriptet henter den fuldt beregnede timepris direkte fra elpriser.org, allerede inkl. nettarif, energiafgift og moms:
```
GET https://elpriser.org/api/prices?area=<DK1|DK2>&mode=net_inkl_alt&gln=<netselskabets GLN>&date=<YYYY-MM-DD>
```
Hvis intet netselskab er valgt, falder scriptet tilbage til den rene spotpris (uden tarif/afgift) fra den samme API.
Til Status-sidens prisopdeling hentes desuden, ca. én gang i timen, to supplerende opslag:
<table>
<tr>
<th>
Felt
</th>
<th>
Kilde (mode)
</th>
<th>
Beregning
</th>
</tr>
<tr>
<td>
Spotpris nu
</td>
<td>
spot_inkl
</td>
<td>
Direkte fra API'et (inkl. moms)
</td>
</tr>
<tr>
<td>
Nettarif
</td>
<td>
net_inkl_tarif
</td>
<td>
net_inkl_tarif − Spotpris nu
</td>
</tr>
<tr>
<td>
Energiafgift
</td>
<td>
net_inkl_alt
</td>
<td>
Pris nu − net_inkl_tarif
</td>
</tr>
</table>
Alle priser er inkl. 25% dansk moms.
Prisdatasource
DK-only versionen bruger:
https://elpriser.org/api
Scriptet forventer, at API'et returnerer timebaserede prisdata for et helt døgn (24 timer) i JSON-format.
Netselskab og GLN
Scriptet henter automatisk nettariffer og energiafgift — der er intet behov for manuel indtastning. Vælg blot dit netselskab i Setup UI'et; listen filtreres automatisk ud fra valgt region (DK1/DK2), og GLN/EAN-nummeret bruges internt til at hente den korrekte pris fra elpriser.org.
Understøttede Shelly-platforme
Scriptet er primært beregnet til Shelly Gen2-enheder og kan også fungere på udvalgte Gen3-/Gen4-enheder, hvis Matter er deaktiveret.
<table>
<tr>
<th>
Enhed
</th>
<th>
Gen2
</th>
<th>
Gen3
</th>
<th>
Gen4
</th>
</tr>
<tr>
<td>
Plus 1
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
<td>
Ikke testet
</td>
</tr>
<tr>
<td>
Plus 1PM
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
<td>
OK
</td>
</tr>
<tr>
<td>
Plus 2PM
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
<td>
Ikke testet
</td>
</tr>
<tr>
<td>
Pro 2
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
<td>
Ikke testet
</td>
</tr>
<tr>
<td>
Pro 3
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
<td>
Ikke testet
</td>
</tr>
<tr>
<td>
Plug S
</td>
<td>
OK
</td>
<td>
OK
</td>
<td>
Ikke testet
</td>
</tr>
<tr>
<td>
Mini PM
</td>
<td>
Ikke testet
</td>
<td>
Viser ustabilitet
</td>
<td>
Ikke testet
</td>
</tr>
</table>
Enhedskompatibilitet afhænger af firmwareversion, ledig script-hukommelse, aktiverede Shelly-funktioner og lokal konfiguration.
Kom godt i gang
Tilslut Shelly-enheden til netværket.
Opdater Shelly-firmware til en nyere stabil version.
Sæt tidszone til: Europe/Copenhagen
Åbn Shelly Web UI.
Opret et nyt script.
Indsæt indholdet af: shelly-elpris-dk.js
Gem scriptet, og genstart det eksplicit (Stop → Start) — kun at gemme filen er ikke nok til at indlæse en opdateret version.
Åbn scriptets console og se efter output i stil med:
`elpris: v.3.3.0_BRKDWN`
`elpris: URL http://<device-ip>/script/<script-id>`
Åbn den viste URL i en browser.
Gå til fanen Opsætning, vælg Region og Netselskab, og gem.
Indbygget webinterface
Shelly-enheden stiller et let HTTP UI til rådighed med fire faner (på dansk).
<table>
<tr>
<th>
Fane
</th>
<th>
Beskrivelse
</th>
</tr>
<tr>
<td>
Status
</td>
<td>
Aktuel pris (samlet, spot, nettarif, energiafgift), output-tilstand og aktiv logik
</td>
</tr>
<tr>
<td>
Historik
</td>
<td>
Loggede handlinger og output-ændringer
</td>
</tr>
<tr>
<td>
Opsætning
</td>
<td>
Region, netselskab, outputs, tilstande og undtagelser
</td>
</tr>
<tr>
<td>
Hjælp
</td>
<td>
Indbygget vejledning til de vigtigste opsætningsfelter
</td>
</tr>
</table>
Vigtige opsætningsfelter
Region
Vælg DK1 eller DK2.
Netselskab
Vælg dit netselskab i dropdown-listen (filtreret efter region). GLN/EAN-nummer og dækningsområde vises automatisk. Priser (inkl. nettarif, energiafgift og moms) hentes derefter automatisk — ingen manuel indtastning nødvendig.
Tilstand
Scriptet understøtter flere styringstilstande:
Manuel: output styres manuelt.
Prisgrænse: output aktiveres, når prisen er under den valgte grænse.
Billigste tid: output aktiveres i de billigste timer i den valgte periode.
Shelly outputs
Brug output 0 for et enkelt relæ, eller en kommasepareret liste som: 0,1
ON-tid
Begrænser hvor længe output må være tændt inden for en time.
Backup-timer
Fallback-timer, der bruges hvis prisdata ikke er tilgængelige.
Undtagelser
Faste timebaserede undtagelser kan tvinge output TIL, FRA eller normal styring.
KVS-konfiguration
Scriptet gemmer fælles konfiguration under: `elpris`
Instansspecifik konfiguration gemmes under: `elpris-1`, `elpris-2`
Konfigurationen gemmes via en POST-forespørgsel til enhedens `/rpc/KVS.Set` (med automatisk fallback til GET samt et kort automatisk gentagelsesforsøg, hvis enheden svarer langsomt). Ved opgradering fra ældre eller eksperimentelle builds anbefales det at rydde gamle KVS-nøgler (`http://<device-ip>/rpc/KVS.Delete?key="elpris"`) før første start, hvis der opstår problemer med at gemme konfigurationen, samt genstarte scriptet eksplicit (Stop → Start).
Noter og begrænsninger
Scriptet er DK-only og understøtter kun DK1 og DK2.
Scriptet henter automatisk nettarif og energiafgift via elpriser.org — der er ikke længere en manuel fallback-model.
Scriptet antager timebaseret pris- og styringslogik.
Hvis elpriser.org's API ændrer format, kan scriptet kræve en opdatering.
Hvis beregnede priser ser for høje eller lave ud, bør region og valgt netselskab kontrolleres først.
KVS-lageret på enheden har en grænse på ca. 253 bytes pr. nøgle — brug korte konfigurationsnavne.
Baggrund og credits
DK-versionen er inspireret af Alexander og Jussi Isotalo, og er fuldstændig omskrevet af DuckAvenger.
Baseret oprindeligt på idéer og arkitektur fra shelly-porssisahko af Jussi Isotalo.
Repository
https://github.com/DuckAvenger/shelly-elpris-dk
Licens
Dette projekt er licenseret under GNU Affero General Public License v3.0. AGPL-3.0
