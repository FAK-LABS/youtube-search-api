# YouTube Search API

واجهة برمجة تطبيقات للبحث في YouTube من [FAK LABS](https://faizankhichi.me/). ابحث في فيديوهات YouTube وقنواته وقوائم التشغيل وShorts والبث المباشر — مع فلترة وترتيب وترقيم صفحات كاملة. لا حاجة إلى مفتاح API.

> **هذا المستودع للتوثيق فقط.** يصف نقطة النهاية العامة وكيفية استخدامها. ولا يحتوي على أي كود.

## اقرأ بلغتك

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## نقطة النهاية

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

كل طلب يعيد JSON نظيفًا. المعامل الوحيد المطلوب هو `q`.

## البدء السريع

```bash
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## المعاملات

| المعامل | القيم | الافتراضي | ملاحظات |
|---|---|---|---|
| `q` | نص البحث (بحد أقصى 200 حرف) | — | **مطلوب.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` يعيد نتائج مختلطة من الفيديوهات / القنوات / قوائم التشغيل (لا تشمل Shorts — استخدم `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = أقل من 4 دقائق، medium = 4–20 دقيقة، long = أكثر من 20 دقيقة. البث المباشر والقادم ليس له مدة، لذا لا يظهر أبدًا عند تعيين فلتر المدة. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | نافذة تاريخ الرفع. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | رمز اللغة، مثال: `en`، `ur`، `es` | `en` | لغة واجهة النتائج. |
| `gl` | رمز المنطقة، مثال: `US`، `PK`، `ES` | `US` | المنطقة التي تُخصَّص النتائج لها. |
| `maxResults` | 1–50 | `20` | القيم فوق 20 تُملأ تلقائيًا من صفحات أخرى. |
| `pageToken` | رمز المتابعة من استجابة سابقة | — | يجلب الصفحة التالية. مرّر نفس `q` والفلاتر مع الرمز. |
| `features` | مفصولة بفواصل: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | ادمج مع أي فلتر آخر، مثال: `features=hd,subtitles`. |
| `suggest` | `1` | — | يعيد اقتراحات بحث بدلًا من النتائج (انظر أدناه). |

تندمج الفلاتر بحرية — كل معامل يعمل مع الآخر:

```bash
# فيديوهات قصيرة رُفعت هذا الأسبوع، الأكثر مشاهدة أولًا
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short&uploaded=week&sort=view_count"

# قنوات، باللغة الأردية، لباكستان
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# فيديوهات عالية الدقة مع ترجمة
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&features=hd,subtitles"
```

## الاستجابة

الحقول العليا:

| الحقل | النوع | الوصف |
|---|---|---|
| `success` | boolean | `true` عند نجاح الطلب. |
| `query` | string | الاستعلام كما ورد. |
| `count` | number | عدد النتائج في هذه الاستجابة. |
| `source` | string | `"youtubei"` (عادي) أو `"html"` (المسار البديل). |
| `filters` | object | الفلاتر المطبقة، تُعاد كما هي: `type`، `duration`، `uploaded`، `sort`، `hl`، `gl`، `maxResults`، `features`. |
| `nextPageToken` | string or `null` | مرّره كـ `pageToken` لجلب الصفحة التالية. `null` عند عدم وجود صفحات أخرى (Shorts ليس لها صفحة تالية — الرف صفحة واحدة). |
| `results` | array | النتائج (انظر أدناه). |
| `suggestions` | array of strings | فقط عند `suggest=1` — اقتراحات بحث للاستعلام. |

### مثال على الاستجابة

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

## حقول النتائج

### فيديو (`type=video`)

| الحقل | النوع | الوصف |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | معرّف فيديو YouTube. |
| `title` | string | عنوان الفيديو. |
| `description` | string | مقتطف من الوصف. قد يكون فارغًا — ليس كل فيديو يحمل وصفًا. |
| `channel.id` | string | معرّف القناة (`UC…`). |
| `channel.name` | string | اسم القناة. |
| `channel.avatar` | string | رابط صورة الصورة الرمزية للقناة (فارغ عند عدم توفره). |
| `thumbnails` | array | جميع أحجام الصور المصغرة التي يعيدها YouTube: `{url, width, height}`. |
| `thumbnail` | string | رابط أكبر صورة مصغرة. |
| `duration` | string | المدة كما تظهر على YouTube، مثال: `"8:00:00"`، `"3:24"`. فارغة للبث المباشر / القادم. |
| `durationSeconds` | number or `null` | المدة بالثواني. `null` للبث المباشر / القادم. |
| `views` | string | عدد المشاهدات كما يظهر، مثال: `"190,198 views"`. فارغ عندما لا يعرض YouTube عددًا. |
| `publishedAt` | string | وقت النشر النسبي كما يظهر، مثال: `"1 day ago"`، `"3 weeks ago"`. |
| `isLive` | boolean | `true` للبث المباشر. |
| `isUpcoming` | boolean | `true` للبث المجدول (القادم). |
| `hasCaptions` | boolean | `true` عندما يحتوي الفيديو على ترجمة (CC). |
| `badges` | array of strings | الشارات التي يعرضها YouTube، مثال: `"New"`، `"LIVE"`، `"CC"`، `"4K"`. |
| `url` | string | رابط المشاهدة: `https://www.youtube.com/watch?v=<id>`. |

### قناة (`type=channel`)

| الحقل | النوع | الوصف |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | معرّف القناة (`UC…`). |
| `name` | string | اسم القناة. |
| `handle` | string | مُعرِّف القناة، مثال: `"@LofiGirl"`. فارغ للقنوات التي ليس لها معرّف (مثل قنوات المواضيع المُنشأة تلقائيًا). |
| `subscribers` | string | عدد المشتركين كما يظهر، مثال: `"15.8M subscribers"`. فارغ عندما لا يعرض YouTube عددًا. |
| `videoCount` | string | عدد الفيديوهات كما يظهر (عندما يقدمه YouTube). |
| `avatar` | string | رابط صورة الصورة الرمزية للقناة. |
| `thumbnails` | array | الصورة الرمزية بجميع الأحجام: `{url, width, height}`. |
| `verified` | boolean | `true` للقنوات الموثقة وقنوات الفنانين الرسمية. |
| `description` | string | مقتطف من وصف القناة (قد يكون فارغًا). |
| `url` | string | رابط القناة: `https://www.youtube.com/channel/<id>`. |

### قائمة تشغيل (`type=playlist`)

| الحقل | النوع | الوصف |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | معرّف قائمة التشغيل (`PL…`، `UU…`، `RD…`، …). |
| `title` | string | عنوان قائمة التشغيل. |
| `description` | string | وصف قائمة التشغيل (قد يكون فارغًا). |
| `channel.id` | string | معرّف القناة المالكة. |
| `channel.name` | string | اسم القناة المالكة. |
| `channel.handle` | string | مُعرِّف القناة المالكة (قد يكون فارغًا). |
| `videoCount` | string | عدد الفيديوهات كما يظهر، مثال: `"86 videos"`. |
| `videoCountNum` | number or `null` | عدد الفيديوهات كرقم. |
| `thumbnails` | array | جميع أحجام الصور المصغرة: `{url, width, height}`. |
| `thumbnail` | string | رابط أكبر صورة مصغرة. |
| `url` | string | رابط قائمة التشغيل: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| الحقل | النوع | الوصف |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | معرّف Short. |
| `title` | string | عنوان Short. |
| `channel` | `null` | Shorts لا تحمل معلومات القناة في استجابة البحث. |
| `views` | string | عدد المشاهدات كما يظهر، مثال: `"50K views"`. |
| `thumbnails` | array | أحجام الصور المصغرة: `{url, width, height}` (عمودي، 405×720). |
| `thumbnail` | string | رابط أكبر صورة مصغرة. |
| `url` | string | رابط Short: `https://www.youtube.com/shorts/<id>`. |

### مباشر (`type=live`)

نتائج البث المباشر هي كائنات فيديو (`type=video`) مع `isLive: true`، بلا `duration` / `durationSeconds`، و`views` تعرض عدد المشاهدين الحالي، مثال: `"1.2K watching"`.

## ترقيم الصفحات

```bash
# الصفحة 1
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5"
# → "nextPageToken": "ErsCEgoiRkFLI..."

# الصفحة 2 — نفس الاستعلام والفلاتر، مع الرمز
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5&pageToken=ErsCEgoiRkFLI..."
```

الصفحات لا تكرر النتائج أبدًا. طلب `maxResults` فوق 20 يملأ الاستجابة من صفحات أخرى تلقائيًا، لذا يمكن لطلب واحد أن يعيد ما يصل إلى 50 نتيجة.

## اقتراحات البحث

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

## الأخطاء

| الحالة | المعنى |
|---|---|
| `400` | غياب `q`، أو قيمة غير صالحة لـ `type` أو `duration` أو `uploaded` أو `sort` أو `features` أو `maxResults`. الجسم يسمّي المشكلة. |
| `403` | الوصول المباشر فقط — نقطة النهاية ترفض الطلبات المُمرَّرة عبر خادم أو مضيف مجاني. |
| `405` | يُدعم `GET` فقط (و`OPTIONS` لـ CORS). |
| `502` | لم يجب YouTube. أعد المحاولة. |

الاستعلام الذي لا يطابق شيئًا يعيد `200` مع `"success": true` و`"results": []` — وليس خطأ.

## ملاحظات

- لا مفتاح API، لا تسجيل دخول، لا ترويسة حد معدل — استخدمه بعقلانية.
- `duration` و`views` و`publishedAt` هي النصوص التي يعرضها YouTube، فهي نص عرض وليست أرقامًا دقيقة. `durationSeconds` هو المدة الرقمية عند توفرها.
- `uploaded=hour` يستخدم نافذة الساعة الأخيرة الخاصة بـ YouTube، وهي تقريبية — قد تتسرب بعض العناصر الأقدم قليلًا.
- النتائج تأتي من بحث YouTube؛ ما تحصل عليه يعتمد على الاستعلام والمنطقة (`gl`) واللغة (`hl`).
- CORS مفتوح (`Access-Control-Allow-Origin: *`)، لذا تعمل نقطة النهاية من أي موقع أو تطبيق.

## حول

بُني بواسطة [FAK LABS](https://faizankhichi.me/) — برمجيات جادة، تعمل في الإنتاج.
