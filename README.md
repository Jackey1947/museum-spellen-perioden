# Vestingmuseum Maastricht — De Vesting door de Eeuwen

Een interactief "wiel van de geschiedenis" voor het Vestingmuseum Maastricht. De bezoeker draait het wiel en stopt bij een van de zes historische perioden uit de vestinggeschiedenis van Maastricht. Het zijpaneel toont dan de belangrijkste feiten en sleutelfiguren van die periode.

## Probeer het uit

**[Open de live versie →](https://jackey1947.github.io/museum-spellen-perioden/)**

Werkt direct in elke moderne browser, ook op mobiel.

## Lokaal draaien

Eén bestand, geen build, geen dependencies:

```sh
git clone https://github.com/Jackey1947/museum-spellen-perioden.git
cd museum-spellen-perioden
open index.html
```

## De zes perioden

| # | Periode | Tijdvak |
|---|---|---|
| 1 | Romeinse Tijd | ca. 50 – 400 n.Chr. |
| 2 | Vroege Middeleeuwen | ca. 400 – 1229 |
| 3 | Middeleeuwen — Stadsmuren | ca. 1229 – 1500 |
| 4 | Bourgondisch & Spaans Bestuur | ca. 1500 – 1632 |
| 5 | Republiek & Vestinggordel | ca. 1632 – 1794 |
| 6 | Frans Bestuur & Opheffing | 1794 – 1867 |

## Techniek

Eén `index.html` met inline CSS, inline JavaScript en een inline SVG-wiel. Geen framework, geen buildstap.
