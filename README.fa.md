# YouTube Search API

ای‌پی‌آی جستجوی یوتیوب از [FAK LABS](https://faizankhichi.me/). ویدیوها، کانال‌ها، فهرست‌های پخش، Shorts و پخش‌های زنده یوتیوب را جستجو کنید — با فیلتر کامل، مرتب‌سازی و صفحه‌بندی. نیازی به کلید API نیست.

> **این مخزن فقط مستندات است.** نقطه پایانی عمومی و نحوه استفاده از آن را توصیف می‌کند. هیچ کدی در آن نیست.

## به زبان خود بخوانید

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

هر درخواست JSON تمیز برمی‌گرداند. تنها پارامتر الزامی `q` است.

## شروع سریع

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## پارامترها

| پارامتر | مقدارها | پیش‌فرض | نکته‌ها |
|---|---|---|---|
| `q` | متن جستجو (حداکثر ۲۰۰ نویسه) | — | **الزامی.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` نتایج ترکیبی ویدیو / کانال / فهرست پخش برمی‌گرداند (Shorts شامل نیست — از `type=shorts` استفاده کنید). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = کمتر از ۴ دقیقه، medium = ۴ تا ۲۰ دقیقه، long = بیش از ۲۰ دقیقه. پخش‌های زنده و آینده مدت ندارند، پس وقتی فیلتر مدت تنظیم شود هرگز برنمی‌گردند. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | بازه تاریخ بارگذاری. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | کد زبان، مثلاً `en`، `ur`، `es` | `en` | زبان رابط نتایج. |
| `gl` | کد منطقه، مثلاً `US`، `PK`، `ES` | `US` | منطقه‌ای که نتایج برای آن بومی‌سازی شده‌اند. |
| `maxResults` | 1–50 | `20` | مقدارهای بالای ۲۰ به‌طور خودکار از صفحه‌های بعدی پر می‌شوند. |
| `pageToken` | توکن ادامه از پاسخ قبلی | — | صفحه بعد را می‌گیرد. همان `q` و فیلترها را با توکن بفرستید. |
| `features` | با کاما جدا شده: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | با هر فیلتر دیگری ترکیب کنید، مثلاً `features=hd,subtitles`. |
| `suggest` | `1` | — | به‌جای نتایج، پیشنهادهای جستجو برمی‌گرداند (پایین را ببینید). |

فیلترها آزادانه ترکیب می‌شوند — هر پارامتر با دیگری کار می‌کند:

```bash
# ویدیوهای کوتاه بارگذاری‌شده این هفته، پربازدیدترین اول
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# کانال‌ها، به اردو، برای پاکستان
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# ویدیوهای HD با زیرنویس
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## پاسخ

فیلدهای سطح بالا:

| فیلد | نوع | توضیح |
|---|---|---|
| `success` | boolean | وقتی درخواست موفق بود `true`. |
| `query` | string | جستجو همان‌طور که دریافت شد. |
| `count` | number | تعداد نتایج در این پاسخ. |
| `source` | string | `"youtubei"` (معمولی) یا `"html"` (مسیر جایگزین). |
| `filters` | object | فیلترهای اعمال‌شده، بازگردانده شده: `type`، `duration`، `uploaded`، `sort`، `hl`، `gl`، `maxResults`، `features`. |
| `nextPageToken` | string or `null` | به‌عنوان `pageToken` بفرستید تا صفحه بعد را بگیرید. وقتی صفحه دیگری نیست `null` (Shorts صفحه بعد ندارند — قفسه یک صفحه است). |
| `results` | array | نتایج (پایین را ببینید). |
| `suggestions` | array of strings | فقط وقتی `suggest=1` — پیشنهادهای جستجو برای جستجو. |

### نمونه پاسخ

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

## فیلدهای نتیجه

### ویدیو (`type=video`)

| فیلد | نوع | توضیح |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | شناسه ویدیوی یوتیوب. |
| `title` | string | عنوان ویدیو. |
| `description` | string | گزیده توضیحات. ممکن است خالی باشد — هر ویدیویی توضیح ندارد. |
| `channel.id` | string | شناسه کانال (`UC…`). |
| `channel.name` | string | نام کانال. |
| `channel.avatar` | string | نشانی تصویر آواتار کانال (وقتی در دسترس نیست خالی است). |
| `thumbnails` | array | همه اندازه‌های بندانگشتی که یوتیوب برمی‌گرداند: `{url, width, height}`. |
| `thumbnail` | string | نشانی بزرگ‌ترین بندانگشتی. |
| `duration` | string | مدت همان‌طور که در یوتیوب نشان داده می‌شود، مثلاً `"8:00:00"`، `"3:24"`. برای پخش‌های زنده / آینده خالی است. |
| `durationSeconds` | number or `null` | مدت به ثانیه. برای پخش‌های زنده / آینده `null`. |
| `views` | string | شمار بازدید همان‌طور که نشان داده می‌شود، مثلاً `"190,198 views"`. وقتی یوتیوب چیزی نشان نمی‌دهد خالی است. |
| `publishedAt` | string | زمان انتشار نسبی همان‌طور که نشان داده می‌شود، مثلاً `"1 day ago"`، `"3 weeks ago"`. |
| `isLive` | boolean | برای پخش زنده `true`. |
| `isUpcoming` | boolean | برای پخش برنامه‌ریزی‌شده (آینده) `true`. |
| `hasCaptions` | boolean | وقتی ویدیو زیرنویس (CC) دارد `true`. |
| `badges` | array of strings | نشان‌هایی که یوتیوب نشان می‌دهد، مثلاً `"New"`، `"LIVE"`، `"CC"`، `"4K"`. |
| `url` | string | نشانی تماشا: `https://www.youtube.com/watch?v=<id>`. |

### کانال (`type=channel`)

| فیلد | نوع | توضیح |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | شناسه کانال (`UC…`). |
| `name` | string | نام کانال. |
| `handle` | string | هندل کانال، مثلاً `"@LofiGirl"`. برای کانال‌های بدون هندل خالی است (مثلاً کانال‌های Topic ساخته‌شده خودکار). |
| `subscribers` | string | شمار دنبال‌کنندگان همان‌طور که نشان داده می‌شود، مثلاً `"15.8M subscribers"`. وقتی یوتیوب چیزی نشان نمی‌دهد خالی است. |
| `videoCount` | string | شمار ویدیوها همان‌طور که نشان داده می‌شود (وقتی یوتیوب ارائه می‌دهد). |
| `avatar` | string | نشانی تصویر آواتار کانال. |
| `thumbnails` | array | آواتار در همه اندازه‌ها: `{url, width, height}`. |
| `verified` | boolean | برای کانال‌های تأییدشده و کانال‌های رسمی هنرمند `true`. |
| `description` | string | گزیده توضیحات کانال (ممکن است خالی باشد). |
| `url` | string | نشانی کانال: `https://www.youtube.com/channel/<id>`. |

### فهرست پخش (`type=playlist`)

| فیلد | نوع | توضیح |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | شناسه فهرست پخش (`PL…`، `UU…`، `RD…`، …). |
| `title` | string | عنوان فهرست پخش. |
| `description` | string | توضیح فهرست پخش (ممکن است خالی باشد). |
| `channel.id` | string | شناسه کانال مالک. |
| `channel.name` | string | نام کانال مالک. |
| `channel.handle` | string | هندل کانال مالک (ممکن است خالی باشد). |
| `videoCount` | string | شمار ویدیوها همان‌طور که نشان داده می‌شود، مثلاً `"86 videos"`. |
| `videoCountNum` | number or `null` | شمار ویدیوها به‌صورت عدد. |
| `thumbnails` | array | همه اندازه‌های بندانگشتی: `{url, width, height}`. |
| `thumbnail` | string | نشانی بزرگ‌ترین بندانگشتی. |
| `url` | string | نشانی فهرست پخش: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| فیلد | نوع | توضیح |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | شناسه Short. |
| `title` | string | عنوان Short. |
| `channel` | `null` | Shorts در پاسخ جستجو اطلاعات کانال ندارند. |
| `views` | string | شمار بازدید همان‌طور که نشان داده می‌شود، مثلاً `"50K views"`. |
| `thumbnails` | array | اندازه‌های بندانگشتی: `{url, width, height}` (عمودی، 405×720). |
| `thumbnail` | string | نشانی بزرگ‌ترین بندانگشتی. |
| `url` | string | نشانی Short: `https://www.youtube.com/shorts/<id>`. |

### زنده (`type=live`)

نتایج زنده اشیای ویدیو هستند (`type=video`) با `isLive: true`، بدون `duration` / `durationSeconds`، و `views` شمار بینندگان فعلی را نشان می‌دهد، مثلاً `"1.2K watching"`.

## صفحه‌بندی

```bash
# صفحه ۱
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# صفحه ۲ — همان جستجو و فیلترها، با توکن
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

صفحه‌ها هرگز نتایج را تکرار نمی‌کنند. درخواست `maxResults` بالای ۲۰ به‌طور خودکار از صفحه‌های بعدی پر می‌شود، پس یک درخواست می‌تواند تا ۵۰ نتیجه برگرداند.

## پیشنهادهای جستجو

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

## خطاها

| وضعیت | معنی |
|---|---|
| `400` | `q` نیست، یا مقدار نامعتبر برای `type`، `duration`، `uploaded`، `sort`، `features` یا `maxResults`. بدنه مشکل را نام می‌برد. |
| `403` | فقط دسترسی مستقیم — نقطه پایانی درخواست‌هایی را که از طریق سرور یا میزبان رایگان پروکسی شده‌اند رد می‌کند. |
| `405` | فقط `GET` (و `OPTIONS` برای CORS) پشتیبانی می‌شود. |
| `502` | یوتیوب پاسخ نداد. دوباره تلاش کنید. |

جستجویی بدون تطابق `200` برمی‌گرداند با `"success": true` و `"results": []` — خطا نیست.

## نکته‌ها

- بدون کلید API، بدون ورود، بدون سربرگ محدودیت نرخ — معقول استفاده کنید.
- `duration`، `views` و `publishedAt` رشته‌هایی هستند که یوتیوب نشان می‌دهد، پس متن نمایشی‌اند، نه اعداد دقیق. `durationSeconds` وقتی در دسترس است مدت عددی است.
- `uploaded=hour` از پنجره ساعت گذشته خود یوتیوب استفاده می‌کند که تقریبی است — چند مورد کمی قدیمی‌تر ممکن است وارد شوند.
- نتایج از جستجوی یوتیوب می‌آیند؛ آنچه می‌گیرید به جستجو، منطقه (`gl`) و زبان (`hl`) بستگی دارد.
- CORS باز است (`Access-Control-Allow-Origin: *`)، پس نقطه پایانی از هر وب‌سایت یا برنامه‌ای کار می‌کند.

## درباره

ساخته‌شده توسط [FAK LABS](https://faizankhichi.me/) — نرم‌افزار جدی، در حال اجرا در تولید.
