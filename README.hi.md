# YouTube Search API

[FAK LABS](https://faizankhichi.me/) द्वारा YouTube खोज API। YouTube वीडियो, चैनल, प्लेलिस्ट, Shorts और लाइव स्ट्रीम खोजें — पूर्ण फ़िल्टरिंग, सॉर्टिंग और पेजिनेशन के साथ। API key की आवश्यकता नहीं है।

> **यह रेपो केवल दस्तावेज़ीकरण है।** यह सार्वजनिक endpoint और इसके उपयोग का तरीका बताता है। इसमें कोई कोड नहीं है।

## अपनी भाषा में पढ़ें

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

हर अनुरोध साफ़ JSON लौटाता है। एकमात्र आवश्यक पैरामीटर `q` है।

## त्वरित शुरुआत

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## पैरामीटर

| पैरामीटर | मान | डिफ़ॉल्ट | नोट्स |
|---|---|---|---|
| `q` | खोज पाठ (अधिकतम 200 अक्षर) | — | **आवश्यक।** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` वीडियो / चैनल / प्लेलिस्ट के मिश्रित परिणाम देता है (Shorts शामिल नहीं हैं — `type=shorts` उपयोग करें)। |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = 4 मिनट से कम, medium = 4–20 मिनट, long = 20 मिनट से अधिक। लाइव और आगामी स्ट्रीम की कोई अवधि नहीं होती, इसलिए अवधि फ़िल्टर लगाने पर वे कभी नहीं लौटाए जाते। |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | अपलोड तिथि की समय-सीमा। |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | भाषा कोड, जैसे `en`, `ur`, `es` | `en` | परिणामों की इंटरफ़ेस भाषा। |
| `gl` | क्षेत्र कोड, जैसे `US`, `PK`, `ES` | `US` | वह क्षेत्र जिसके लिए परिणाम स्थानीयकृत हैं। |
| `maxResults` | 1–50 | `20` | 20 से ऊपर के मान आगे के पृष्ठों से अपने आप भर दिए जाते हैं। |
| `pageToken` | पिछले उत्तर से continuation टोकन | — | अगला पृष्ठ लाता है। टोकन के साथ वही `q` और फ़िल्टर भेजें। |
| `features` | अल्पविराम से अलग: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | किसी भी अन्य फ़िल्टर के साथ मिलाएँ, जैसे `features=hd,subtitles`। |
| `suggest` | `1` | — | परिणामों के बजाय खोज सुझाव देता है (नीचे देखें)। |

फ़िल्टर स्वतंत्र रूप से मिलते हैं — हर पैरामीटर दूसरे के साथ काम करता है:

```bash
# इस सप्ताह अपलोड किए गए छोटे वीडियो, सबसे ज़्यादा देखे गए पहले
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# चैनल, उर्दू में, पाकिस्तान के लिए
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# सबटाइटल वाले HD वीडियो
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## उत्तर

शीर्ष-स्तरीय फ़ील्ड:

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `success` | boolean | अनुरोध सफल होने पर `true`। |
| `query` | string | प्राप्त हुई खोज जैसी थी वैसी। |
| `count` | number | इस उत्तर में परिणामों की संख्या। |
| `source` | string | `"youtubei"` (सामान्य) या `"html"` (वैकल्पिक रास्ता)। |
| `filters` | object | लगाए गए फ़िल्टर, वापस दिखाए गए: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`। |
| `nextPageToken` | string or `null` | अगला पृष्ठ लाने के लिए `pageToken` के रूप में भेजें। और पृष्ठ न होने पर `null` (Shorts का अगला पृष्ठ नहीं होता — शेल्फ़ एक ही पृष्ठ है)। |
| `results` | array | परिणाम (नीचे देखें)। |
| `suggestions` | array of strings | केवल जब `suggest=1` — खोज के लिए खोज सुझाव। |

### उत्तर का उदाहरण

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

## परिणाम फ़ील्ड

### वीडियो (`type=video`)

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `type` | string | `"video"`। |
| `id` | string | YouTube वीडियो id। |
| `title` | string | वीडियो का शीर्षक। |
| `description` | string | विवरण का अंश। खाली हो सकता है — हर वीडियो का विवरण नहीं होता। |
| `channel.id` | string | चैनल id (`UC…`)। |
| `channel.name` | string | चैनल का नाम। |
| `channel.avatar` | string | चैनल अवतार छवि का URL (उपलब्ध न होने पर खाली)। |
| `thumbnails` | array | YouTube द्वारा लौटाए गए सभी थंबनेल आकार: `{url, width, height}`। |
| `thumbnail` | string | सबसे बड़े थंबनेल का URL। |
| `duration` | string | अवधि जैसी YouTube पर दिखाई जाती है, जैसे `"8:00:00"`, `"3:24"`। लाइव / आगामी स्ट्रीम के लिए खाली। |
| `durationSeconds` | number or `null` | सेकंड में अवधि। लाइव / आगामी स्ट्रीम के लिए `null`। |
| `views` | string | व्यू संख्या जैसी दिखाई जाती है, जैसे `"190,198 views"`। YouTube कुछ न दिखाए तो खाली। |
| `publishedAt` | string | सापेक्ष प्रकाशन समय जैसा दिखाया जाता है, जैसे `"1 day ago"`, `"3 weeks ago"`। |
| `isLive` | boolean | लाइव स्ट्रीम के लिए `true`। |
| `isUpcoming` | boolean | निर्धारित (आगामी) स्ट्रीम के लिए `true`। |
| `hasCaptions` | boolean | वीडियो में कैप्शन (CC) होने पर `true`। |
| `badges` | array of strings | YouTube द्वारा दिखाए गए बैज, जैसे `"New"`, `"LIVE"`, `"CC"`, `"4K"`। |
| `url` | string | देखने का URL: `https://www.youtube.com/watch?v=<id>`। |

### चैनल (`type=channel`)

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `type` | string | `"channel"`। |
| `id` | string | चैनल id (`UC…`)। |
| `name` | string | चैनल का नाम। |
| `handle` | string | चैनल हैंडल, जैसे `"@LofiGirl"`। बिना हैंडल वाले चैनलों के लिए खाली (जैसे अपने आप बने Topic चैनल)। |
| `subscribers` | string | सब्सक्राइबर संख्या जैसी दिखाई जाती है, जैसे `"15.8M subscribers"`। YouTube कुछ न दिखाए तो खाली। |
| `videoCount` | string | वीडियो संख्या जैसी दिखाई जाती है (जब YouTube देता है)। |
| `avatar` | string | चैनल अवतार छवि का URL। |
| `thumbnails` | array | सभी आकारों में अवतार: `{url, width, height}`। |
| `verified` | boolean | सत्यापित चैनलों और आधिकारिक कलाकार चैनलों के लिए `true`। |
| `description` | string | चैनल विवरण का अंश (खाली हो सकता है)। |
| `url` | string | चैनल URL: `https://www.youtube.com/channel/<id>`। |

### प्लेलिस्ट (`type=playlist`)

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `type` | string | `"playlist"`। |
| `id` | string | प्लेलिस्ट id (`PL…`, `UU…`, `RD…`, …)। |
| `title` | string | प्लेलिस्ट का शीर्षक। |
| `description` | string | प्लेलिस्ट का विवरण (खाली हो सकता है)। |
| `channel.id` | string | स्वामी चैनल की id। |
| `channel.name` | string | स्वामी चैनल का नाम। |
| `channel.handle` | string | स्वामी चैनल का हैंडल (खाली हो सकता है)। |
| `videoCount` | string | वीडियो संख्या जैसी दिखाई जाती है, जैसे `"86 videos"`। |
| `videoCountNum` | number or `null` | संख्या के रूप में वीडियो संख्या। |
| `thumbnails` | array | सभी थंबनेल आकार: `{url, width, height}`। |
| `thumbnail` | string | सबसे बड़े थंबनेल का URL। |
| `url` | string | प्लेलिस्ट URL: `https://www.youtube.com/playlist?list=<id>`। |

### Short (`type=shorts`)

| फ़ील्ड | प्रकार | विवरण |
|---|---|---|
| `type` | string | `"short"`। |
| `id` | string | Short की id। |
| `title` | string | Short का शीर्षक। |
| `channel` | `null` | Shorts खोज उत्तर में चैनल जानकारी नहीं रखते। |
| `views` | string | व्यू संख्या जैसी दिखाई जाती है, जैसे `"50K views"`। |
| `thumbnails` | array | थंबनेल आकार: `{url, width, height}` (पोर्ट्रेट, 405×720)। |
| `thumbnail` | string | सबसे बड़े थंबनेल का URL। |
| `url` | string | Short URL: `https://www.youtube.com/shorts/<id>`। |

### लाइव (`type=live`)

लाइव परिणाम वीडियो ऑब्जेक्ट हैं (`type=video`) जिनमें `isLive: true` होता है, `duration` / `durationSeconds` नहीं होते, और `views` वर्तमान दर्शक संख्या दिखाता है, जैसे `"1.2K watching"`।

## पेजिनेशन

```bash
# पृष्ठ 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# पृष्ठ 2 — वही खोज और फ़िल्टर, टोकन के साथ
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

पृष्ठ कभी परिणाम नहीं दोहराते। `maxResults` 20 से ऊपर माँगने पर उत्तर आगे के पृष्ठों से अपने आप भर जाता है, इसलिए एक अनुरोध 50 तक परिणाम दे सकता है।

## खोज सुझाव

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

## त्रुटियाँ

| स्थिति | अर्थ |
|---|---|
| `400` | `q` गुम है, या `type`, `duration`, `uploaded`, `sort`, `features` या `maxResults` का अमान्य मान है। body समस्या बताता है। |
| `403` | केवल सीधी पहुँच — endpoint सर्वर या मुफ़्त होस्ट के माध्यम से प्रॉक्सी किए गए अनुरोधों को अस्वीकार करता है। |
| `405` | केवल `GET` (और CORS के लिए `OPTIONS`) समर्थित है। |
| `502` | YouTube ने उत्तर नहीं दिया। पुनः प्रयास करें। |

बिना मिलान वाली खोज `200` देती है जिसमें `"success": true` और `"results": []` होते हैं — यह त्रुटि नहीं है।

## नोट्स

- कोई API key नहीं, कोई साइन-इन नहीं, कोई rate-limit हेडर नहीं — समझदारी से उपयोग करें।
- `duration`, `views` और `publishedAt` वे स्ट्रिंग हैं जो YouTube दिखाता है, इसलिए वे प्रदर्शन पाठ हैं, सटीक संख्याएँ नहीं। `durationSeconds` उपलब्ध होने पर संख्यात्मक अवधि है।
- `uploaded=hour` YouTube की अपनी पिछले घंटे की विंडो उपयोग करता है, जो अनुमानित है — कुछ थोड़ी पुरानी चीज़ें आ सकती हैं।
- परिणाम YouTube की खोज से आते हैं; आपको क्या मिलता है यह खोज, क्षेत्र (`gl`) और भाषा (`hl`) पर निर्भर करता है।
- CORS खुला है (`Access-Control-Allow-Origin: *`), इसलिए endpoint किसी भी वेबसाइट या ऐप से काम करता है।

## के बारे में

[FAK LABS](https://faizankhichi.me/) द्वारा निर्मित — गंभीर सॉफ़्टवेयर, प्रोडक्शन में चल रहा है।
