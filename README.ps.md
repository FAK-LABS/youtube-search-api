# YouTube Search API

د [FAK LABS](https://faizankhichi.me/) یوټیوب لټون API۔ د یوټیوب ویډیوګانې، چینلونه، پلیلیستونه، Shorts او ژوندۍ خپرونې ولټوئ — د بشپړ فلټر کولو، ترتیبولو او پاڼو کولو سره۔ API کیلي ته اړتیا نشته۔

> **دا ریپو یوازې اسناد دي۔** دا عامه endpoint او د هغې د کارولو طریقه تشریح کوي۔ په کې هیڅ کوډ نشته۔

## په خپله ژبه یې ولولئ

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

هره غوښتنه پاک JSON بېرته راکوي۔ یوازې اړین پارامیټر `q` دی۔

## چټک پیل

```bash
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## پارامیټرونه

| پارامیټر | ارزښتونه | ډیفالټ | یادښتونه |
|---|---|---|---|
| `q` | د لټون متن (تر 200 تورو پورې) | — | **اړین۔** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` د ویډیو / چینل / پلیلیست ګډې پایلې راکوي (Shorts پکې نشته — `type=shorts` وکاروئ)۔ |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = له 4 دقیقو کم، medium = 4–20 دقیقې، long = له 20 دقیقو زیات۔ ژوندۍ او راتلونکې خپرونې موده نه لري، نو کله چې د مودې فلټر وټاکل شي، هېڅکله نه راځي۔ |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | د اپلوډ نېټې کړکۍ۔ |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | د ژبې کوډ، لکه `en`، `ur`، `es` | `en` | د پایلو د انټرفیس ژبه۔ |
| `gl` | د سیمې کوډ، لکه `US`، `PK`، `ES` | `US` | هغه سیمه چې پایلې ورته ځايي شوې دي۔ |
| `maxResults` | 1–50 | `20` | له 20 څخه پورته ارزښتونه په اتوماتيک ډول له راتلونکو پاڼو ډکیږي۔ |
| `pageToken` | له پخواني ځواب څخه د دوام ټوکن | — | راتلونکې پاڼه راوړي۔ له ټوکن سره هماغه `q` او فلټرونه ولېږئ۔ |
| `features` | په کوما جلا شوي: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | له بل هر فلټر سره یې یوځای کړئ، لکه `features=hd,subtitles`۔ |
| `suggest` | `1` | — | د پایلو پر ځای د لټون وړاندیزونه راکوي (لاندې وګورئ)۔ |

فلټرونه په ازاده توګه یوځای کیږي — هر پارامیټر له بل سره کار کوي:

```bash
# لنډې ویډیوګانې چې په دې اونۍ کې اپلوډ شوې، تر ټولو زیاتې کتل شوې لومړی
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short&uploaded=week&sort=view_count"

# چینلونه، په اردو کې، د پاکستان لپاره
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# د سبټایټلونو سره HD ویډیوګانې
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&features=hd,subtitles"
```

## ځواب

د پورتنۍ کچې ساحې:

| ساحه | ډول | تشریح |
|---|---|---|
| `success` | boolean | کله چې غوښتنه بریالۍ شي `true`۔ |
| `query` | string | لټون لکه څنګه چې ترلاسه شو۔ |
| `count` | number | په دې ځواب کې د پایلو شمېر۔ |
| `source` | string | `"youtubei"` (عادي) یا `"html"` (بدیله لار)۔ |
| `filters` | object | کارول شوي فلټرونه، بېرته ښودل شوي: `type`، `duration`، `uploaded`، `sort`، `hl`، `gl`، `maxResults`، `features`۔ |
| `nextPageToken` | string or `null` | د راتلونکې پاڼې لپاره یې د `pageToken` په توګه ولېږئ۔ کله چې نورې پاڼې نه وي `null` (Shorts راتلونکې پاڼه نه لري — المارۍ یوه پاڼه ده)۔ |
| `results` | array | پایلې (لاندې وګورئ)۔ |
| `suggestions` | array of strings | یوازې کله چې `suggest=1` — د لټون لپاره د لټون وړاندیزونه۔ |

### د ځواب بېلګه

`GET https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=2`

```json
{
  "success": true,
  "query": "\"FAK LABS\"",
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
  "nextPageToken": "ErsCEgoiRkFLIExBQlMiGqwCRWdJUUFVZ...",
  "results": [
    {
      "type": "video",
      "id": "PlQvsgrxoEo",
      "title": "Faizan Ahmad Khichi website Studio Free website hosting free website templates Easy to use #FAK",
      "description": "",
      "channel": {
        "id": "UCBfjw4aRQSbtHMz5zBGHSjA",
        "name": "Faizan Ahmad Khichi | FAK LABS",
        "avatar": "https://yt3.ggpht.com/WzEKmlgEaE6PC-ic8a_xgF5zIAlZd37WjHq5apzlNgEMH6zdZvPQVI8cm8AcbQ4lAcrhNWss=s68-c-k-c0x00ffffff-no-rj"
      },
      "thumbnails": [
        { "url": "https://i.ytimg.com/vi/PlQvsgrxoEo/hqdefault.jpg?sqp=-oaymwEcCOADEI4CSFXyq4qpAw4IARUAAIhCGAFwAcABBg==&rs=AOn4CLCg2EtEoJ3ga8IhiF_TslOFfdv2Vg", "width": 480, "height": 270 }
      ],
      "thumbnail": "https://i.ytimg.com/vi/PlQvsgrxoEo/hqdefault.jpg?sqp=-oaymwEcCOADEI4CSFXyq4qpAw4IARUAAIhCGAFwAcABBg==&rs=AOn4CLCg2EtEoJ3ga8IhiF_TslOFfdv2Vg",
      "duration": "8:49",
      "durationSeconds": 529,
      "views": "115 views",
      "publishedAt": "1 year ago",
      "isLive": false,
      "isUpcoming": false,
      "hasCaptions": false,
      "badges": [],
      "url": "https://www.youtube.com/watch?v=PlQvsgrxoEo"
    }
  ]
}
```

## د پایلو ساحې

### ویډیو (`type=video`)

| ساحه | ډول | تشریح |
|---|---|---|
| `type` | string | `"video"`۔ |
| `id` | string | د یوټیوب ویډیو id۔ |
| `title` | string | د ویډیو سرلیک۔ |
| `description` | string | د تشریح ټوټه۔ کېدای شي تشه وي — هره ویډیو تشریح نه لري۔ |
| `channel.id` | string | د چینل id (`UC…`)۔ |
| `channel.name` | string | د چینل نوم۔ |
| `channel.avatar` | string | د چینل اوتار انځور URL (کله چې شتون ونه لري تش وي)۔ |
| `thumbnails` | array | د یوټیوب له خوا راستانه شوي ټول thumbnail اندازې: `{url, width, height}`۔ |
| `thumbnail` | string | د تر ټولو لوی thumbnail URL۔ |
| `duration` | string | موده لکه څنګه چې په یوټیوب کې ښودل کیږي، لکه `"8:00:00"`، `"3:24"`۔ د ژوندۍ / راتلونکې خپرونې لپاره تشه۔ |
| `durationSeconds` | number or `null` | په ثانیو کې موده۔ د ژوندۍ / راتلونکې خپرونې لپاره `null`۔ |
| `views` | string | د کتنو شمېر لکه څنګه چې ښودل کیږي، لکه `"190,198 views"`۔ کله چې یوټیوب هېڅ نه ښيي تشه۔ |
| `publishedAt` | string | د خپرولو اړوند وخت لکه څنګه چې ښودل کیږي، لکه `"1 day ago"`، `"3 weeks ago"`۔ |
| `isLive` | boolean | د ژوندۍ خپرونې لپاره `true`۔ |
| `isUpcoming` | boolean | د مهالوېش شوې (راتلونکې) خپرونې لپاره `true`۔ |
| `hasCaptions` | boolean | کله چې ویډیو کیپشن (CC) ولري `true`۔ |
| `badges` | array of strings | هغه بیجونه چې یوټیوب یې ښيي، لکه `"New"`، `"LIVE"`، `"CC"`، `"4K"`۔ |
| `url` | string | د کتلو URL: `https://www.youtube.com/watch?v=<id>`۔ |

### چینل (`type=channel`)

| ساحه | ډول | تشریح |
|---|---|---|
| `type` | string | `"channel"`۔ |
| `id` | string | د چینل id (`UC…`)۔ |
| `name` | string | د چینل نوم۔ |
| `handle` | string | د چینل handle، لکه `"@LofiGirl"`۔ د هغو چینلونو لپاره تشه چې handle نه لري (لکه په اتوماتيک ډول جوړ شوي Topic چینلونه)۔ |
| `subscribers` | string | د ګډونوالو شمېر لکه څنګه چې ښودل کیږي، لکه `"15.8M subscribers"`۔ کله چې یوټیوب هېڅ نه ښيي تشه۔ |
| `videoCount` | string | د ویډیوګانو شمېر لکه څنګه چې ښودل کیږي (کله چې یوټیوب یې ورکوي)۔ |
| `avatar` | string | د چینل اوتار انځور URL۔ |
| `thumbnails` | array | په ټولو اندازو کې اوتار: `{url, width, height}`۔ |
| `verified` | boolean | د تایید شویو چینلونو او رسمي هنرمند چینلونو لپاره `true`۔ |
| `description` | string | د چینل تشریح ټوټه (کېدای شي تشه وي)۔ |
| `url` | string | د چینل URL: `https://www.youtube.com/channel/<id>`۔ |

### پلیلیست (`type=playlist`)

| ساحه | ډول | تشریح |
|---|---|---|
| `type` | string | `"playlist"`۔ |
| `id` | string | د پلیلیست id (`PL…`، `UU…`، `RD…`، …)۔ |
| `title` | string | د پلیلیست سرلیک۔ |
| `description` | string | د پلیلیست تشریح (کېدای شي تشه وي)۔ |
| `channel.id` | string | د مالک چینل id۔ |
| `channel.name` | string | د مالک چینل نوم۔ |
| `channel.handle` | string | د مالک چینل handle (کېدای شي تشه وي)۔ |
| `videoCount` | string | د ویډیوګانو شمېر لکه څنګه چې ښودل کیږي، لکه `"86 videos"`۔ |
| `videoCountNum` | number or `null` | د ویډیوګانو شمېر په شمېره کې۔ |
| `thumbnails` | array | ټول thumbnail اندازې: `{url, width, height}`۔ |
| `thumbnail` | string | د تر ټولو لوی thumbnail URL۔ |
| `url` | string | د پلیلیست URL: `https://www.youtube.com/playlist?list=<id>`۔ |

### Short (`type=shorts`)

| ساحه | ډول | تشریح |
|---|---|---|
| `type` | string | `"short"`۔ |
| `id` | string | د Short id۔ |
| `title` | string | د Short سرلیک۔ |
| `channel` | `null` | Shorts د لټون په ځواب کې د چینل معلومات نه لري۔ |
| `views` | string | د کتنو شمېر لکه څنګه چې ښودل کیږي، لکه `"50K views"`۔ |
| `thumbnails` | array | د thumbnail اندازې: `{url, width, height}` (عمودي، 405×720)۔ |
| `thumbnail` | string | د تر ټولو لوی thumbnail URL۔ |
| `url` | string | د Short URL: `https://www.youtube.com/shorts/<id>`۔ |

### ژوندۍ (`type=live`)

ژوندۍ پایلې د ویډیو توکي دي (`type=video`) چې `isLive: true` لري، `duration` / `durationSeconds` نه لري، او `views` د اوسنیو کتونکو شمېر ښيي، لکه `"1.2K watching"`۔

## پاڼې کول

```bash
# پاڼه 1
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5"
# → "nextPageToken": "ErsCEgoiRkFLI..."

# پاڼه 2 — هماغه لټون او فلټرونه، له ټوکن سره
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5&pageToken=ErsCEgoiRkFLI..."
```

پاڼې هېڅکله پایلې نه تکراروي۔ له 20 څخه پورته `maxResults` غوښتل ځواب په اتوماتيک ډول له راتلونکو پاڼو ډکوي، نو یوه غوښتنه کولی شي تر 50 پورې پایلې ورکړي۔

## د لټون وړاندیزونه

```bash
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&suggest=1"
```

```json
{
  "success": true,
  "query": "\"FAK LABS\"",
  "suggestions": []
}
```

## تېروتنې

| حالت | مانا |
|---|---|
| `400` | `q` نشته، یا د `type`، `duration`، `uploaded`، `sort`، `features` یا `maxResults` ناسم ارزښت دی۔ body ستونزه په ګوته کوي۔ |
| `403` | یوازې مستقیم لاسرسی — endpoint هغه غوښتنې ردوي چې د سرور یا وړیا کوربه له لارې پراکسي شوي وي۔ |
| `405` | یوازې `GET` (او د CORS لپاره `OPTIONS`) ملاتړ کیږي۔ |
| `502` | یوټیوب ځواب ورنه کړ۔ بیا هڅه وکړئ۔ |

بې سمونې لټون `200` راکوي چې `"success": true` او `"results": []` لري — دا تېروتنه نه ده۔

## یادښتونه

- هېڅ API کیلي نشته، هېڅ ننوتل نشته، هېڅ rate-limit سرلیک نشته — په هوښیارۍ سره یې وکاروئ۔
- `duration`، `views` او `publishedAt` هغه تارونه دي چې یوټیوب یې ښيي، نو دا د ښودلو متن دی، دقیق شمېرې نه دي۔ `durationSeconds` کله چې شتون ولري عددي موده ده۔
- `uploaded=hour` د یوټیوب خپله د تېر ساعت کړکۍ کاروي، چې تقریبي ده — یو څو لږ زړې توکي کېدای شي ورننوځي۔
- پایلې د یوټیوب له لټون څخه راځي؛ هغه څه چې تاسو یې ترلاسه کوئ په لټون، سیمه (`gl`) او ژبه (`hl`) پورې اړه لري۔
- CORS خلاص دی (`Access-Control-Allow-Origin: *`)، نو endpoint له هرې ویب پاڼې یا اپلیکېشن څخه کار کوي۔

## په اړه

د [FAK LABS](https://faizankhichi.me/) لخوا جوړ شوی — جدي سافټویر، په تولید کې چلېږي۔
