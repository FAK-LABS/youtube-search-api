# YouTube Search API

API ya kutafuta YouTube kutoka [FAK LABS](https://faizankhichi.me/). Tafuta video, chaneli, orodha za kucheza, Shorts na matangazo ya moja kwa moja ya YouTube — kwa kuchuja, kupanga na kurasa kamili. Hakuna ufunguo wa API unaohitajika.

> **Repo hii ni nyaraka tu.** Inaelezea endpoint ya umma na jinsi ya kuitumia. Haina code.

## Soma kwa lugha yako

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Kila ombi linarudisha JSON safi. Kigezo pekee cha lazima ni `q`.

## Anza haraka

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Vigezo

| Kigezo | Thamani | Chaguo-msingi | Maelezo |
|---|---|---|---|
| `q` | maandishi ya utafutaji (herufi 200 kwa juu) | — | **Lazima.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` inarudisha matokeo mchanganyiko ya video / chaneli / orodha za kucheza (Shorts hazijumuishwi — tumia `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = chini ya dakika 4, medium = dakika 4–20, long = zaidi ya dakika 20. Matangazo ya moja kwa moja na yajayo hayana muda, kwa hivyo hayarudishwi kamwe wakati kichujio cha muda kimewekwa. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Dirisha la tarehe ya kupakia. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | msimbo wa lugha, mf. `en`, `ur`, `es` | `en` | Lugha ya kiolesura cha matokeo. |
| `gl` | msimbo wa eneo, mf. `US`, `PK`, `ES` | `US` | Eneo ambalo matokeo yanalengwa. |
| `maxResults` | 1–50 | `20` | Thamani zaidi ya 20 hujazwa kiotomatiki kutoka kurasa zinazofuata. |
| `pageToken` | token ya muendelezo kutoka jibu la awali | — | Hupata ukurasa unaofuata. Pitisha `q` na vichujio vile vile pamoja na token. |
| `features` | zilizotenganishwa kwa koma: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Changanya na kichujio kingine chochote, mf. `features=hd,subtitles`. |
| `suggest` | `1` | — | Hunarudisha mapendekezo ya utafutaji badala ya matokeo (tazama hapa chini). |

Vichujio huchanganyika kwa uhuru — kila kigezo hufanya kazi pamoja na kingine:

```bash
# video fupi zilizopakiwa wiki hii, zilizotazamwa zaidi kwanza
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# chaneli, kwa Kiurdu, za Pakistan
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# video za HD zenye manukuu
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Jibu

Sehemu za juu:

| Sehemu | Aina | Maelezo |
|---|---|---|
| `success` | boolean | `true` ombi lilipofanikiwa. |
| `query` | string | Utafutaji kama ulivyopokelewa. |
| `count` | number | Idadi ya matokeo katika jibu hili. |
| `source` | string | `"youtubei"` (kawaida) au `"html"` (njia mbadala). |
| `filters` | object | Vichujio vilivyotumika, vimerudishwa kama vilivyo: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Pitisha kama `pageToken` kupata ukurasa unaofuata. `null` wakati hakuna kurasa zaidi (Shorts hazina ukurasa unaofuata — rafu ni ukurasa mmoja). |
| `results` | array | Matokeo (tazama hapa chini). |
| `suggestions` | array of strings | Tu wakati `suggest=1` — mapendekezo ya utafutaji kwa utafutaji huo. |

### Mfano wa jibu

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

## Sehemu za matokeo

### Video (`type=video`)

| Sehemu | Aina | Maelezo |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | Id ya video ya YouTube. |
| `title` | string | Kichwa cha video. |
| `description` | string | Dondoo la maelezo. Linaweza kuwa tupu — si kila video ina moja. |
| `channel.id` | string | Id ya chaneli (`UC…`). |
| `channel.name` | string | Jina la chaneli. |
| `channel.avatar` | string | URL ya picha ya avatar ya chaneli (tupu isipopatikana). |
| `thumbnails` | array | Ukubwa wote wa vijipicha unaorudishwa na YouTube: `{url, width, height}`. |
| `thumbnail` | string | URL ya kijipicha kikubwa zaidi. |
| `duration` | string | Urefu kama unavyoonyeshwa kwenye YouTube, mf. `"8:00:00"`, `"3:24"`. Tupu kwa matangazo ya moja kwa moja / yajayo. |
| `durationSeconds` | number or `null` | Urefu kwa sekunde. `null` kwa matangazo ya moja kwa moja / yajayo. |
| `views` | string | Idadi ya watazamaji kama inavyoonyeshwa, mf. `"190,198 views"`. Tupu wakati YouTube haionyeshi idadi. |
| `publishedAt` | string | Wakati wa kuchapisha kama unavyoonyeshwa, mf. `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` kwa tangazo la moja kwa moja. |
| `isUpcoming` | boolean | `true` kwa tangazo lililoratibiwa (lijalo). |
| `hasCaptions` | boolean | `true` wakati video ina manukuu (CC). |
| `badges` | array of strings | Beji zinazoonyeshwa na YouTube, mf. `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | URL ya kutazama: `https://www.youtube.com/watch?v=<id>`. |

### Chaneli (`type=channel`)

| Sehemu | Aina | Maelezo |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Id ya chaneli (`UC…`). |
| `name` | string | Jina la chaneli. |
| `handle` | string | Handle ya chaneli, mf. `"@LofiGirl"`. Tupu kwa chaneli zisizo na moja (mf. chaneli za Topic zinazozalishwa kiotomatiki). |
| `subscribers` | string | Idadi ya waliojiandikisha kama inavyoonyeshwa, mf. `"15.8M subscribers"`. Tupu wakati YouTube haionyeshi idadi. |
| `videoCount` | string | Idadi ya video kama inavyoonyeshwa (wakati YouTube inatoa). |
| `avatar` | string | URL ya picha ya avatar ya chaneli. |
| `thumbnails` | array | Avatar katika ukubwa wote: `{url, width, height}`. |
| `verified` | boolean | `true` kwa chaneli zilizothibitishwa na chaneli rasmi za wasanii. |
| `description` | string | Dondoo la maelezo ya chaneli (linaweza kuwa tupu). |
| `url` | string | URL ya chaneli: `https://www.youtube.com/channel/<id>`. |

### Orodha ya kucheza (`type=playlist`)

| Sehemu | Aina | Maelezo |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Id ya orodha ya kucheza (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Kichwa cha orodha ya kucheza. |
| `description` | string | Maelezo ya orodha ya kucheza (yanaweza kuwa tupu). |
| `channel.id` | string | Id ya chaneli mwenye orodha. |
| `channel.name` | string | Jina la chaneli mwenye orodha. |
| `channel.handle` | string | Handle ya chaneli mwenye orodha (inaweza kuwa tupu). |
| `videoCount` | string | Idadi ya video kama inavyoonyeshwa, mf. `"86 videos"`. |
| `videoCountNum` | number or `null` | Idadi ya video kama nambari. |
| `thumbnails` | array | Ukubwa wote wa vijipicha: `{url, width, height}`. |
| `thumbnail` | string | URL ya kijipicha kikubwa zaidi. |
| `url` | string | URL ya orodha ya kucheza: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Sehemu | Aina | Maelezo |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Id ya Short. |
| `title` | string | Kichwa cha Short. |
| `channel` | `null` | Shorts hazibebi taarifa za chaneli katika jibu la utafutaji. |
| `views` | string | Idadi ya watazamaji kama inavyoonyeshwa, mf. `"50K views"`. |
| `thumbnails` | array | Ukubwa wa vijipicha: `{url, width, height}` (wima, 405×720). |
| `thumbnail` | string | URL ya kijipicha kikubwa zaidi. |
| `url` | string | URL ya Short: `https://www.youtube.com/shorts/<id>`. |

### Moja kwa moja (`type=live`)

Matokeo ya moja kwa moja ni vitu vya video (`type=video`) vyenye `isLive: true`, bila `duration` / `durationSeconds`, na `views` inaonyesha idadi ya watazamaji wa sasa, mf. `"1.2K watching"`.

## Kurasa

```bash
# ukurasa 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# ukurasa 2 — utafutaji na vichujio vile vile, pamoja na token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

Kurasa hazirudii matokeo kamwe. Kuomba `maxResults` zaidi ya 20 hujaza jibu kutoka kurasa zinazofuata kiotomatiki, kwa hivyo ombi moja linaweza kurudisha hadi matokeo 50.

## Mapendekezo ya utafutaji

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

## Makosa

| Hali | Maana |
|---|---|
| `400` | `q` haipo, au thamani batili kwa `type`, `duration`, `uploaded`, `sort`, `features` au `maxResults`. Mwili unataja tatizo. |
| `403` | Ufikiaji wa moja kwa moja tu — endpoint inakataa maombi yaliyopitishwa kupitia seva au mwenyeji wa bure. |
| `405` | `GET` tu (na `OPTIONS` kwa CORS) ndiyo inayoungwa mkono. |
| `502` | YouTube haikujibu. Jaribu tena. |

Utafutaji usio na matokeo unarudisha `200` na `"success": true` na `"results": []` — si kosa.

## Maelezo

- Hakuna ufunguo wa API, hakuna kuingia, hakuna kichwa cha kikomo cha kiwango — tumia kwa busara.
- `duration`, `views` na `publishedAt` ni mifuatano inayoonyeshwa na YouTube, kwa hivyo ni maandishi ya kuonyesha, si nambari kamili. `durationSeconds` ni muda wa nambari wakati unapatikana.
- `uploaded=hour` hutumia dirisha la saa iliyopita la YouTube, ambalo ni la kukadiria — vitu vichache vilivyozeeka kidogo vinaweza kuingia.
- Matokeo yanatoka kwenye utafutaji wa YouTube; unachopata kinategemea utafutaji, eneo (`gl`) na lugha (`hl`).
- CORS iko wazi (`Access-Control-Allow-Origin: *`), kwa hivyo endpoint inafanya kazi kutoka tovuti au programu yoyote.

## Kuhusu

Imejengwa na [FAK LABS](https://faizankhichi.me/) — programu nzito, inayoendesha uzalishaji.
