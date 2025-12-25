# pendlers

**pendlers** är en enkel webbaserad pendlar-app som visar **aktuell avgångstid för ett specifikt tåg från en vald station**, med fokus på tydlighet, realtid och minimal komplexitet.

Appen använder **Trafikverkets öppna API** för tågdata och är tänkt som:

* ett personligt verktyg för daglig pendling
* ett tekniskt övningsprojekt (API, frontend, enkel backend-proxy)
* en grund för vidareutveckling (notiser, flera tåg, karta m.m.)

---

## Funktioner

* Visar **planerad avgångstid** och **prognos** (om försenad)
* Visar **“avgår om X minuter”**
* Visar **spårinformation** (när tillgängligt)
* Indikerar om tåget är **inställt**
* Uppdateras automatiskt (polling)
* Använder Trafikverkets officiella API (ingen scraping)

Exempelanvändning:

* Tåg **7401**
* Station **Härnösand (Hsd)**

---

## Datakälla

Appen hämtar data från Trafikverkets öppna API via Trafiklab:

* Endpoint:

  ```
  https://api.trafikinfo.trafikverket.se/v2/data.json
  ```
* Objekt: `TrainAnnouncement`
* Filter: station, tågnummer, avgång

API-nyckel krävs (se Installation).

---

## Arkitektur (översikt)

```
[ Browser / Frontend ]
          |
          |  GET /api/departure
          v
[ Backend / API-proxy ]
          |
          |  POST (XML)
          v
[ Trafikverkets API ]
```

Motiv:

* API-nyckeln hålls **utanför frontend**
* Enklare felsökning och framtida utbyggnad
* Möjligt att lägga cache, logik och validering i backend

---

## Installation

### Förutsättningar

* Node.js 18+ (eller motsvarande runtime)
* Ett konto på Trafiklab
* En Trafikverket API-nyckel

### Skaffa API-nyckel

1. Skapa konto på [https://www.trafiklab.se](https://www.trafiklab.se)
2. Skapa en API-nyckel
3. Spara nyckeln som miljövariabel

```bash
export TRV_API_KEY="din_api_nyckel"
```

---

## Kör lokalt

### Backend

```bash
npm install
npm start
```

Backend startar som standard på:

```
http://localhost:3000
```

Exempel:

```
GET /api/departure?train=7401&station=Hsd
```

---

### Frontend

Starta frontend (exempel):

```bash
npm run dev
```

Öppna i webbläsaren:

```
http://localhost:5173
```

---

## Konfiguration

| Parameter    | Beskrivning                     | Exempel |
| ------------ | ------------------------------- | ------- |
| train        | Tågnummer                       | 7401    |
| station      | Stationskod (LocationSignature) | Hsd     |
| pollInterval | Uppdateringsintervall (ms)      | 30000   |

Stationskoder följer Trafikverkets officiella signaturer.

---

## Exempel på API-anrop (curl)

```bash
curl -X POST "https://api.trafikinfo.trafikverket.se/v2/data.json" \
  -H "Content-Type: application/xml" \
  -d '<REQUEST>
        <LOGIN authenticationkey="DIN_API_NYCKEL" />
        <QUERY objecttype="TrainAnnouncement" schemaversion="1.9">
          <FILTER>
            <AND>
              <EQ name="AdvertisedTrainIdent" value="7401" />
              <EQ name="LocationSignature" value="Hsd" />
              <EQ name="ActivityType" value="Avgang" />
            </AND>
          </FILTER>
        </QUERY>
      </REQUEST>'
```

---

## Begränsningar

* API:t visar **aktuella och nära förestående händelser**, inte hela tidtabeller
* Ett tåg som redan avgått kan försvinna ur svaren
* Data uppdateras händelsebaserat av Trafikverket

---

## Möjlig vidareutveckling

* Flera favorit­tåg
* Push-notiser vid försening
* Karta (länk till Tågkartan)
* Offline-läge / cache
* Mobilanpassning / PWA
* Containerisering (Podman / Docker)
* Deployment till Kubernetes

---

## Licens

Bestäms av repoägaren.
Rekommendation för hobbyprojekt: MIT License.

---

## Namn

**pendlers**
Ett litet verktyg för en stor del av vardagen.

---
