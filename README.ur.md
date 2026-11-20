# YouTube Search API

[FAK LABS](https://faizankhichi.me/) کی YouTube سرچ API۔ YouTube کی ویڈیوز، چینلز، پلے لسٹس، Shorts اور لائیو سٹریمز تلاش کریں — مکمل فلٹرنگ، ترتیب اور صفحہ بندی کے ساتھ۔ API کی (key) کی ضرورت نہیں۔

> **یہ ریپو صرف دستاویزات کے لیے ہے۔** یہ عوامی endpoint اور اس کے استعمال کا طریقہ بتاتا ہے۔ اس میں کوئی کوڈ نہیں ہے۔

## اپنی زبان میں پڑھیں

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

ہر درخواست صاف JSON واپس کرتی ہے۔ واحد لازمی پیرامیٹر `q` ہے۔

## فوری آغاز

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## پیرامیٹرز

| پیرامیٹر | اقدار | ڈیفالٹ | نوٹس |
|---|---|---|---|
| `q` | تلاش کا متن (زیادہ سے زیادہ 200 حروف) | — | **لازمی۔** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` ویڈیو / چینل / پلے لسٹ کے ملے جلے نتائج دیتا ہے (Shorts شامل نہیں — `type=shorts` استعمال کریں)۔ |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = 4 منٹ سے کم، medium = 4–20 منٹ، long = 20 منٹ سے زیادہ۔ لائیو اور آنے والی سٹریمز کی کوئی مدت نہیں ہوتی، اس لیے مدت کا فلٹر لگانے پر وہ کبھی واپس نہیں آتیں۔ |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | اپ لوڈ کی تاریخ کی حد۔ |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | زبان کا کوڈ، مثلاً `en`، `ur`، `es` | `en` | نتائج کی انٹرفیس زبان۔ |
| `gl` | علاقے کا کوڈ، مثلاً `US`، `PK`، `ES` | `US` | وہ علاقہ جس کے لیے نتائج مقامی کیے گئے ہوں۔ |
| `maxResults` | 1–50 | `20` | 20 سے زیادہ اقدار اگلے صفحات سے خود بخود پوری کر دی جاتی ہیں۔ |
| `pageToken` | پچھلی جواب سے continuation ٹوکن | — | اگلا صفحہ لاتا ہے۔ ٹوکن کے ساتھ وہی `q` اور فلٹرز بھیجیں۔ |
| `features` | کوما سے الگ: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | کسی بھی دوسرے فلٹر کے ساتھ ملائیں، مثلاً `features=hd,subtitles`۔ |
| `suggest` | `1` | — | نتائج کے بجائے سرچ تجاویز دیتا ہے (نیچے دیکھیں)۔ |

فلٹرز آزادانہ ملتے ہیں — ہر پیرامیٹر دوسرے کے ساتھ کام کرتا ہے:

```bash
# اس ہفتے اپ لوڈ شدہ مختصر ویڈیوز، سب سے زیادہ دیکھی گئی پہلے
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# چینلز، اردو میں، پاکستان کے لیے
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# سب ٹائٹلز والی HD ویڈیوز
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## جواب

اعلیٰ سطح کے فیلڈز:

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `success` | boolean | درخواست کامیاب ہونے پر `true`۔ |
| `query` | string | موصول شدہ تلاش جیسی تھی ویسی۔ |
| `count` | number | اس جواب میں نتائج کی تعداد۔ |
| `source` | string | `"youtubei"` (عام) یا `"html"` (متبادل راستہ)۔ |
| `filters` | object | لگائے گئے فلٹرز، واپس دکھائے گئے: `type`، `duration`، `uploaded`، `sort`، `hl`، `gl`، `maxResults`، `features`۔ |
| `nextPageToken` | string or `null` | اگلا صفحہ لانے کے لیے `pageToken` کے طور پر بھیجیں۔ مزید صفحات نہ ہونے پر `null` (Shorts کا اگلا صفحہ نہیں ہوتا — شیلف ایک ہی صفحہ ہے)۔ |
| `results` | array | نتائج (نیچے دیکھیں)۔ |
| `suggestions` | array of strings | صرف جب `suggest=1` — تلاش کے لیے سرچ تجاویز۔ |

### جواب کی مثال

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

## نتائج کے فیلڈز

### ویڈیو (`type=video`)

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `type` | string | `"video"`۔ |
| `id` | string | YouTube ویڈیو کی id۔ |
| `title` | string | ویڈیو کا عنوان۔ |
| `description` | string | تفصیل کا اقتباس۔ خالی ہو سکتا ہے — ہر ویڈیو کی تفصیل نہیں ہوتی۔ |
| `channel.id` | string | چینل کی id (`UC…`)۔ |
| `channel.name` | string | چینل کا نام۔ |
| `channel.avatar` | string | چینل کی اوتار تصویر کا URL (دستیاب نہ ہونے پر خالی)۔ |
| `thumbnails` | array | YouTube کی واپس کی گئی تمام تھمب نیل سائز: `{url, width, height}`۔ |
| `thumbnail` | string | سب سے بڑے تھمب نیل کا URL۔ |
| `duration` | string | مدت جیسا YouTube پر دکھائی جاتی ہے، مثلاً `"8:00:00"`، `"3:24"`۔ لائیو / آنے والی سٹریمز کے لیے خالی۔ |
| `durationSeconds` | number or `null` | سیکنڈوں میں مدت۔ لائیو / آنے والی سٹریمز کے لیے `null`۔ |
| `views` | string | ویوز کی تعداد جیسی دکھائی جاتی ہے، مثلاً `"190,198 views"`۔ YouTube کچھ نہ دکھائے تو خالی۔ |
| `publishedAt` | string | اشاعت کا متعلقہ وقت جیسا دکھایا جاتا ہے، مثلاً `"1 day ago"`، `"3 weeks ago"`۔ |
| `isLive` | boolean | لائیو سٹریم کے لیے `true`۔ |
| `isUpcoming` | boolean | شیڈول شدہ (آنے والی) سٹریم کے لیے `true`۔ |
| `hasCaptions` | boolean | ویڈیو میں کیپشن (CC) ہونے پر `true`۔ |
| `badges` | array of strings | YouTube کے دکھائے گئے بیجز، مثلاً `"New"`، `"LIVE"`، `"CC"`، `"4K"`۔ |
| `url` | string | دیکھنے کا URL: `https://www.youtube.com/watch?v=<id>`۔ |

### چینل (`type=channel`)

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `type` | string | `"channel"`۔ |
| `id` | string | چینل کی id (`UC…`)۔ |
| `name` | string | چینل کا نام۔ |
| `handle` | string | چینل کا handle، مثلاً `"@LofiGirl"`۔ جن چینلز کا handle نہ ہو ان کے لیے خالی (مثلاً خود بخود بنے Topic چینلز)۔ |
| `subscribers` | string | سبسکرائبرز کی تعداد جیسی دکھائی جاتی ہے، مثلاً `"15.8M subscribers"`۔ YouTube کچھ نہ دکھائے تو خالی۔ |
| `videoCount` | string | ویڈیوز کی تعداد جیسی دکھائی جاتی ہے (جب YouTube دے)۔ |
| `avatar` | string | چینل کی اوتار تصویر کا URL۔ |
| `thumbnails` | array | تمام سائز میں اوتار: `{url, width, height}`۔ |
| `verified` | boolean | تصدیق شدہ چینلز اور آفیشل آرٹسٹ چینلز کے لیے `true`۔ |
| `description` | string | چینل کی تفصیل کا اقتباس (خالی ہو سکتا ہے)۔ |
| `url` | string | چینل کا URL: `https://www.youtube.com/channel/<id>`۔ |

### پلے لسٹ (`type=playlist`)

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `type` | string | `"playlist"`۔ |
| `id` | string | پلے لسٹ کی id (`PL…`، `UU…`، `RD…`، …)۔ |
| `title` | string | پلے لسٹ کا عنوان۔ |
| `description` | string | پلے لسٹ کی تفصیل (خالی ہو سکتی ہے)۔ |
| `channel.id` | string | مالک چینل کی id۔ |
| `channel.name` | string | مالک چینل کا نام۔ |
| `channel.handle` | string | مالک چینل کا handle (خالی ہو سکتا ہے)۔ |
| `videoCount` | string | ویڈیوز کی تعداد جیسی دکھائی جاتی ہے، مثلاً `"86 videos"`۔ |
| `videoCountNum` | number or `null` | ویڈیوز کی تعداد بطور عدد۔ |
| `thumbnails` | array | تمام تھمب نیل سائز: `{url, width, height}`۔ |
| `thumbnail` | string | سب سے بڑے تھمب نیل کا URL۔ |
| `url` | string | پلے لسٹ کا URL: `https://www.youtube.com/playlist?list=<id>`۔ |

### Short (`type=shorts`)

| فیلڈ | قسم | تفصیل |
|---|---|---|
| `type` | string | `"short"`۔ |
| `id` | string | Short کی id۔ |
| `title` | string | Short کا عنوان۔ |
| `channel` | `null` | Shorts سرچ جواب میں چینل کی معلومات نہیں رکھتے۔ |
| `views` | string | ویوز کی تعداد جیسی دکھائی جاتی ہے، مثلاً `"50K views"`۔ |
| `thumbnails` | array | تھمب نیل سائز: `{url, width, height}` (عمودی، 405×720)۔ |
| `thumbnail` | string | سب سے بڑے تھمب نیل کا URL۔ |
| `url` | string | Short کا URL: `https://www.youtube.com/shorts/<id>`۔ |

### لائیو (`type=live`)

لائیو نتائج ویڈیو آبجیکٹس ہیں (`type=video`) جن میں `isLive: true` ہوتا ہے، `duration` / `durationSeconds` نہیں ہوتے، اور `views` دیکھنے والوں کی موجودہ تعداد دکھاتا ہے، مثلاً `"1.2K watching"`۔

## صفحہ بندی

```bash
# صفحہ 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# صفحہ 2 — وہی تلاش اور فلٹرز، ٹوکن کے ساتھ
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

صفحات کبھی نتائج دہراتے نہیں۔ `maxResults` 20 سے زیادہ مانگنے پر جواب اگلے صفحات سے خود بخود بھر جاتا ہے، اس لیے ایک درخواست 50 تک نتائج دے سکتی ہے۔

## سرچ تجاویز

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

## غلطیاں

| حیثیت | مطلب |
|---|---|
| `400` | `q` غائب ہے، یا `type`، `duration`، `uploaded`، `sort`، `features` یا `maxResults` کی غلط قدر ہے۔ body مسئلہ بتاتا ہے۔ |
| `403` | صرف براہ راست رسائی — endpoint سرور یا مفت ہوسٹ کے ذریعے پراکسی شدہ درخواستوں کو مسترد کرتا ہے۔ |
| `405` | صرف `GET` (اور CORS کے لیے `OPTIONS`) سپورٹ ہے۔ |
| `502` | YouTube نے جواب نہیں دیا۔ دوبارہ کوشش کریں۔ |

بغیر میچ والی تلاش `200` دیتی ہے جس میں `"success": true` اور `"results": []` ہوتے ہیں — یہ غلطی نہیں ہے۔

## نوٹس

- کوئی API کی (key) نہیں، کوئی سائن ان نہیں، کوئی rate-limit ہیڈر نہیں — سمجھداری سے استعمال کریں۔
- `duration`، `views` اور `publishedAt` وہ سٹرنگز ہیں جو YouTube دکھاتا ہے، اس لیے یہ ڈسپلے متن ہیں، درست اعداد نہیں۔ `durationSeconds` دستیاب ہونے پر عددی مدت ہے۔
- `uploaded=hour` YouTube کی اپنی پچھلے گھنٹے کی ونڈو استعمال کرتا ہے، جو تقریبی ہے — کچھ تھوڑی پرانی اشیاء آ سکتی ہیں۔
- نتائج YouTube کی سرچ سے آتے ہیں؛ آپ کو کیا ملتا ہے اس کا انحصار تلاش، علاقے (`gl`) اور زبان (`hl`) پر ہے۔
- CORS کھلا ہے (`Access-Control-Allow-Origin: *`)، اس لیے endpoint کسی بھی ویب سائٹ یا ایپ سے کام کرتا ہے۔

## کے بارے میں

[FAK LABS](https://faizankhichi.me/) کی طرف سے بنایا گیا — سنجیدہ سافٹ ویئر، پروڈکشن میں چل رہا ہے۔
