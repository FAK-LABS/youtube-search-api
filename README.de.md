# YouTube Search API

Eine YouTube-Such-API von [FAK LABS](https://faizankhichi.me/). Durchsuchen Sie YouTube-Videos, Kanäle, Playlists, Shorts und Livestreams — mit vollständigem Filtern, Sortieren und Paginierung. Kein API-Schlüssel erforderlich.

> **Dieses Repo ist nur Dokumentation.** Es beschreibt den öffentlichen Endpoint und seine Verwendung. Es enthält keinen Code.

## In Ihrer Sprache lesen

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Jede Anfrage liefert sauberes JSON zurück. Der einzige erforderliche Parameter ist `q`.

## Schnellstart

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Parameter

| Parameter | Werte | Standard | Hinweise |
|---|---|---|---|
| `q` | Suchtext (max. 200 Zeichen) | — | **Erforderlich.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` liefert gemischte Video- / Kanal- / Playlist-Ergebnisse (Shorts sind nicht enthalten — verwenden Sie `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = unter 4 Min., medium = 4–20 Min., long = über 20 Min. Live- und kommende Streams haben keine Dauer und werden daher nie zurückgegeben, wenn ein Dauerfilter gesetzt ist. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Zeitfenster des Upload-Datums. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | Sprachcode, z. B. `en`, `ur`, `es` | `en` | Sprache der Benutzeroberfläche der Ergebnisse. |
| `gl` | Regionscode, z. B. `US`, `PK`, `ES` | `US` | Region, für die die Ergebnisse lokalisiert sind. |
| `maxResults` | 1–50 | `20` | Werte über 20 werden automatisch von weiteren Seiten aufgefüllt. |
| `pageToken` | Fortsetzungstoken aus einer vorherigen Antwort | — | Ruft die nächste Seite ab. Übergeben Sie dasselbe `q` und dieselben Filter mit dem Token. |
| `features` | kommagetrennt: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Mit jedem anderen Filter kombinierbar, z. B. `features=hd,subtitles`. |
| `suggest` | `1` | — | Gibt Suchvorschläge statt Ergebnissen zurück (siehe unten). |

Filter lassen sich frei kombinieren — jeder Parameter funktioniert mit jedem anderen:

```bash
# kurze Videos, diese Woche hochgeladen, meistgesehen zuerst
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# Kanäle, auf Urdu, für Pakistan
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# HD-Videos mit Untertiteln
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Antwort

Felder der obersten Ebene:

| Feld | Typ | Beschreibung |
|---|---|---|
| `success` | boolean | `true`, wenn die Anfrage funktioniert hat. |
| `query` | string | Die Abfrage wie empfangen. |
| `count` | number | Anzahl der Ergebnisse in dieser Antwort. |
| `source` | string | `"youtubei"` (normal) oder `"html"` (Ersatzweg). |
| `filters` | object | Die angewendeten Filter, zurückgegeben: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Als `pageToken` übergeben, um die nächste Seite abzurufen. `null`, wenn es keine weiteren Seiten gibt (Shorts haben keine nächste Seite — das Regal ist eine Seite). |
| `results` | array | Die Ergebnisse (siehe unten). |
| `suggestions` | array of strings | Nur bei `suggest=1` — Suchvorschläge für die Abfrage. |

### Beispielantwort

`GET https://yt-api.faizankhichi.me/?q=lofi&maxResults=2`

```json
{
  "success": true,
  "query": "lofi",
  "count": 2,
  "source": "youtubei",
  "filters": {
    "type": "video",
    "duration": "all",
    "uploaded": "all",
    "sort": "relevance",
    "hl": "en",
    "gl": "US",
    "maxResults": 2,
    "features": []
  },
  "nextPageToken": "EpkDEgRsb2ZpGpADRWdJUUFV...",
  "results": [
    {
      "type": "video",
      "id": "nx32Ue0AAA0",
      "title": "a rainy night 🌧️ lofi rain for deep focus",
      "description": "",
      "channel": {
        "id": "UCSJ4gkVC6NrvII8umztf0Ow",
        "name": "Lofi Girl",
        "avatar": "https://yt3.ggpht.com/REcZL_LGu0DaDaC-G7VIbN0uE_5l-i_dw6ZvOETRnY3pRhUmfkOq6C1MLephPZkV4VSzQAhEi_4=s88-c-k-c0x00ffffff-no-rj"
      },
      "thumbnails": [
        { "url": "https://i.ytimg.com/vi/nx32Ue0AAA0/hq720.jpg?sqp=-oaymwEcCOgCEMoBSFXyq4qpAw4IARUAAIhCGAFwAcABBg==&rs=AOn4CLCWXzhp-Yo5_hpKK838kAJD9LFfIA", "width": 360, "height": 202 },
        { "url": "https://i.ytimg.com/vi/nx32Ue0AAA0/hq720.jpg?sqp=-oaymwEcCNAFEJQDSFXyq4qpAw4IARUAAIhCGAFwAcABBg==&rs=AOn4CLD6NBjFWdI7sdBoFySfs_k0X7JWvw", "width": 720, "height": 404 }
      ],
      "thumbnail": "https://i.ytimg.com/vi/nx32Ue0AAA0/hq720.jpg?sqp=-oaymwEcCNAFEJQDSFXyq4qpAw4IARUAAIhCGAFwAcABBg==&rs=AOn4CLD6NBjFWdI7sdBoFySfs_k0X7JWvw",
      "duration": "8:00:00",
      "durationSeconds": 28800,
      "views": "190,198 views",
      "publishedAt": "1 day ago",
      "isLive": false,
      "isUpcoming": false,
      "hasCaptions": false,
      "badges": ["New"],
      "url": "https://www.youtube.com/watch?v=nx32Ue0AAA0"
    }
  ]
}
```

## Ergebnisfelder

### Video (`type=video`)

| Feld | Typ | Beschreibung |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | YouTube-Video-ID. |
| `title` | string | Videotitel. |
| `description` | string | Beschreibungsausschnitt. Kann leer sein — nicht jedes Video hat eine Beschreibung. |
| `channel.id` | string | Kanal-ID (`UC…`). |
| `channel.name` | string | Kanalname. |
| `channel.avatar` | string | URL des Kanal-Avatarbilds (leer, wenn nicht verfügbar). |
| `thumbnails` | array | Alle von YouTube zurückgegebenen Thumbnail-Größen: `{url, width, height}`. |
| `thumbnail` | string | URL des größten Thumbnails. |
| `duration` | string | Länge wie auf YouTube angezeigt, z. B. `"8:00:00"`, `"3:24"`. Leer für Live- / kommende Streams. |
| `durationSeconds` | number or `null` | Länge in Sekunden. `null` für Live- / kommende Streams. |
| `views` | string | Aufrufzahl wie angezeigt, z. B. `"190,198 views"`. Leer, wenn YouTube keine anzeigt. |
| `publishedAt` | string | Relative Veröffentlichungszeit wie angezeigt, z. B. `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` für einen Livestream. |
| `isUpcoming` | boolean | `true` für einen geplanten (kommenden) Stream. |
| `hasCaptions` | boolean | `true`, wenn das Video Untertitel (CC) hat. |
| `badges` | array of strings | Von YouTube angezeigte Badges, z. B. `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | Wiedergabe-URL: `https://www.youtube.com/watch?v=<id>`. |

### Kanal (`type=channel`)

| Feld | Typ | Beschreibung |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Kanal-ID (`UC…`). |
| `name` | string | Kanalname. |
| `handle` | string | Kanal-Handle, z. B. `"@LofiGirl"`. Leer für Kanäle ohne Handle (z. B. automatisch erzeugte Themenkanäle). |
| `subscribers` | string | Abonnentenzahl wie angezeigt, z. B. `"15.8M subscribers"`. Leer, wenn YouTube keine anzeigt. |
| `videoCount` | string | Videoanzahl wie angezeigt (wenn YouTube sie liefert). |
| `avatar` | string | URL des Kanal-Avatarbilds. |
| `thumbnails` | array | Avatar in allen Größen: `{url, width, height}`. |
| `verified` | boolean | `true` für verifizierte Kanäle und offizielle Künstlerkanäle. |
| `description` | string | Beschreibungsausschnitt des Kanals (kann leer sein). |
| `url` | string | Kanal-URL: `https://www.youtube.com/channel/<id>`. |

### Playlist (`type=playlist`)

| Feld | Typ | Beschreibung |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Playlist-ID (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Titel der Playlist. |
| `description` | string | Beschreibung der Playlist (kann leer sein). |
| `channel.id` | string | ID des besitzenden Kanals. |
| `channel.name` | string | Name des besitzenden Kanals. |
| `channel.handle` | string | Handle des besitzenden Kanals (kann leer sein). |
| `videoCount` | string | Videoanzahl wie angezeigt, z. B. `"86 videos"`. |
| `videoCountNum` | number or `null` | Videoanzahl als Zahl. |
| `thumbnails` | array | Alle Thumbnail-Größen: `{url, width, height}`. |
| `thumbnail` | string | URL des größten Thumbnails. |
| `url` | string | Playlist-URL: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Feld | Typ | Beschreibung |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Short-ID. |
| `title` | string | Titel des Shorts. |
| `channel` | `null` | Shorts enthalten in der Suchantwort keine Kanalinformationen. |
| `views` | string | Aufrufzahl wie angezeigt, z. B. `"50K views"`. |
| `thumbnails` | array | Thumbnail-Größen: `{url, width, height}` (Hochformat, 405×720). |
| `thumbnail` | string | URL des größten Thumbnails. |
| `url` | string | Shorts-URL: `https://www.youtube.com/shorts/<id>`. |

### Live (`type=live`)

Live-Ergebnisse sind Videoobjekte (`type=video`) mit `isLive: true`, ohne `duration` / `durationSeconds`, und `views` zeigt die aktuelle Zuschauerzahl, z. B. `"1.2K watching"`.

## Paginierung

```bash
# Seite 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# Seite 2 — gleiche Abfrage und Filter, plus Token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

Seiten wiederholen nie Ergebnisse. Eine `maxResults`-Angabe über 20 füllt die Antwort automatisch von weiteren Seiten auf, sodass eine Anfrage bis zu 50 Ergebnisse liefern kann.

## Suchvorschläge

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi&suggest=1"
```

```json
{
  "success": true,
  "query": "lofi",
  "suggestions": ["lofi", "lofi coffee shop", "lofi fireplace", "..."]
}
```

## Fehler

| Status | Bedeutung |
|---|---|
| `400` | `q` fehlt oder ein ungültiger Wert für `type`, `duration`, `uploaded`, `sort`, `features` oder `maxResults`. Der Body nennt das Problem. |
| `403` | Nur Direktzugriff — der Endpoint lehnt Anfragen ab, die über einen Server oder einen kostenlosen Host per Proxy laufen. |
| `405` | Nur `GET` (und `OPTIONS` für CORS) wird unterstützt. |
| `502` | YouTube hat nicht geantwortet. Erneut versuchen. |

Eine Abfrage ohne Treffer liefert `200` mit `"success": true` und `"results": []` — kein Fehler.

## Hinweise

- Kein API-Schlüssel, keine Anmeldung, kein Rate-Limit-Header — vernünftig verwenden.
- `duration`, `views` und `publishedAt` sind die von YouTube angezeigten Zeichenketten, also Anzeigetext, keine exakten Zahlen. `durationSeconds` ist die numerische Dauer, wenn verfügbar.
- `uploaded=hour` verwendet YouTubes eigenes Fenster der letzten Stunde, das ungefähr ist — ein paar etwas ältere Einträge können durchrutschen.
- Ergebnisse stammen aus der YouTube-Suche; was Sie erhalten, hängt von der Abfrage, der Region (`gl`) und der Sprache (`hl`) ab.
- CORS ist offen (`Access-Control-Allow-Origin: *`), der Endpoint funktioniert also von jeder Website oder App aus.

## Über

Erstellt von [FAK LABS](https://faizankhichi.me/) — ernsthafte Software, im Produktivbetrieb.
