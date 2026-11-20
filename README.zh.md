# YouTube Search API

[FAK LABS](https://faizankhichi.me/) 的 YouTube 搜索 API。搜索 YouTube 视频、频道、播放列表、Shorts 和直播——支持完整的筛选、排序和分页。无需 API 密钥。

> **本仓库仅为文档。** 它介绍公共 endpoint 及其使用方法。不包含任何代码。

## 用您的语言阅读

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

每个请求都返回干净的 JSON。唯一必填参数是 `q`。

## 快速开始

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## 参数

| 参数 | 取值 | 默认值 | 说明 |
|---|---|---|---|
| `q` | 搜索文本（最多 200 个字符） | — | **必填。** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` 返回视频 / 频道 / 播放列表的混合结果（不含 Shorts——请用 `type=shorts`）。 |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = 4 分钟以下，medium = 4–20 分钟，long = 20 分钟以上。直播和预定直播没有时长，因此设置时长筛选时永远不会返回。 |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | 上传日期范围。 |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | 语言代码，例如 `en`、`ur`、`es` | `en` | 结果的界面语言。 |
| `gl` | 地区代码，例如 `US`、`PK`、`ES` | `US` | 结果本地化的地区。 |
| `maxResults` | 1–50 | `20` | 超过 20 的值会自动从后续页面补齐。 |
| `pageToken` | 上一次响应中的 continuation token | — | 获取下一页。连同 token 一起传入相同的 `q` 和筛选条件。 |
| `features` | 逗号分隔：`hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | 可与任何其他筛选条件组合，例如 `features=hd,subtitles`。 |
| `suggest` | `1` | — | 返回搜索建议而非结果（见下文）。 |

筛选条件可自由组合——每个参数都可与其他参数一起使用：

```bash
# 本周上传的短视频，按观看量从高到低
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# 频道，乌尔都语，面向巴基斯坦
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# 带字幕的 HD 视频
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## 响应

顶级字段：

| 字段 | 类型 | 说明 |
|---|---|---|
| `success` | boolean | 请求成功时为 `true`。 |
| `query` | string | 收到的查询原文。 |
| `count` | number | 本次响应中的结果数量。 |
| `source` | string | `"youtubei"`（正常）或 `"html"`（备用路径）。 |
| `filters` | object | 已应用的筛选条件，原样返回：`type`、`duration`、`uploaded`、`sort`、`hl`、`gl`、`maxResults`、`features`。 |
| `nextPageToken` | string or `null` | 作为 `pageToken` 传入以获取下一页。没有更多页面时为 `null`（Shorts 没有下一页——货架只有一页）。 |
| `results` | array | 结果（见下文）。 |
| `suggestions` | array of strings | 仅当 `suggest=1` 时——该查询的搜索建议。 |

### 响应示例

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

## 结果字段

### 视频 (`type=video`)

| 字段 | 类型 | 说明 |
|---|---|---|
| `type` | string | `"video"`。 |
| `id` | string | YouTube 视频 ID。 |
| `title` | string | 视频标题。 |
| `description` | string | 描述摘要。可能为空——并非每个视频都有描述。 |
| `channel.id` | string | 频道 ID（`UC…`）。 |
| `channel.name` | string | 频道名称。 |
| `channel.avatar` | string | 频道头像图片 URL（不可用时为空）。 |
| `thumbnails` | array | YouTube 返回的全部缩略图尺寸：`{url, width, height}`。 |
| `thumbnail` | string | 最大缩略图的 URL。 |
| `duration` | string | YouTube 上显示的时长，例如 `"8:00:00"`、`"3:24"`。直播 / 预定直播为空。 |
| `durationSeconds` | number or `null` | 以秒为单位的时长。直播 / 预定直播为 `null`。 |
| `views` | string | 显示的观看次数，例如 `"190,198 views"`。YouTube 未显示时为空。 |
| `publishedAt` | string | 显示的相对发布时间，例如 `"1 day ago"`、`"3 weeks ago"`。 |
| `isLive` | boolean | 直播时为 `true`。 |
| `isUpcoming` | boolean | 已安排（即将开始）的直播为 `true`。 |
| `hasCaptions` | boolean | 视频有字幕（CC）时为 `true`。 |
| `badges` | array of strings | YouTube 显示的徽章，例如 `"New"`、`"LIVE"`、`"CC"`、`"4K"`。 |
| `url` | string | 观看链接：`https://www.youtube.com/watch?v=<id>`。 |

### 频道 (`type=channel`)

| 字段 | 类型 | 说明 |
|---|---|---|
| `type` | string | `"channel"`。 |
| `id` | string | 频道 ID（`UC…`）。 |
| `name` | string | 频道名称。 |
| `handle` | string | 频道 handle，例如 `"@LofiGirl"`。无 handle 的频道为空（例如自动生成的 Topic 频道）。 |
| `subscribers` | string | 显示的订阅者数量，例如 `"15.8M subscribers"`。YouTube 未显示时为空。 |
| `videoCount` | string | 显示的视频数量（YouTube 提供时）。 |
| `avatar` | string | 频道头像图片 URL。 |
| `thumbnails` | array | 全部尺寸的头像：`{url, width, height}`。 |
| `verified` | boolean | 已验证频道和官方艺人频道为 `true`。 |
| `description` | string | 频道描述摘要（可能为空）。 |
| `url` | string | 频道链接：`https://www.youtube.com/channel/<id>`。 |

### 播放列表 (`type=playlist`)

| 字段 | 类型 | 说明 |
|---|---|---|
| `type` | string | `"playlist"`。 |
| `id` | string | 播放列表 ID（`PL…`、`UU…`、`RD…` 等）。 |
| `title` | string | 播放列表标题。 |
| `description` | string | 播放列表描述（可能为空）。 |
| `channel.id` | string | 所属频道 ID。 |
| `channel.name` | string | 所属频道名称。 |
| `channel.handle` | string | 所属频道 handle（可能为空）。 |
| `videoCount` | string | 显示的视频数量，例如 `"86 videos"`。 |
| `videoCountNum` | number or `null` | 数字形式的视频数量。 |
| `thumbnails` | array | 全部缩略图尺寸：`{url, width, height}`。 |
| `thumbnail` | string | 最大缩略图的 URL。 |
| `url` | string | 播放列表链接：`https://www.youtube.com/playlist?list=<id>`。 |

### Short (`type=shorts`)

| 字段 | 类型 | 说明 |
|---|---|---|
| `type` | string | `"short"`。 |
| `id` | string | Short ID。 |
| `title` | string | Short 标题。 |
| `channel` | `null` | Shorts 在搜索响应中不带频道信息。 |
| `views` | string | 显示的观看次数，例如 `"50K views"`。 |
| `thumbnails` | array | 缩略图尺寸：`{url, width, height}`（竖屏，405×720）。 |
| `thumbnail` | string | 最大缩略图的 URL。 |
| `url` | string | Short 链接：`https://www.youtube.com/shorts/<id>`。 |

### 直播 (`type=live`)

直播结果是视频对象（`type=video`），`isLive: true`，没有 `duration` / `durationSeconds`，`views` 显示当前观看人数，例如 `"1.2K watching"`。

## 分页

```bash
# 第 1 页
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# 第 2 页——相同的查询和筛选条件，加上 token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

页面从不重复结果。`maxResults` 超过 20 时，响应会自动从后续页面补齐，因此一次请求最多可返回 50 条结果。

## 搜索建议

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

## 错误

| 状态 | 含义 |
|---|---|
| `400` | 缺少 `q`，或 `type`、`duration`、`uploaded`、`sort`、`features`、`maxResults` 的值无效。响应体中会说明问题。 |
| `403` | 仅限直接访问——endpoint 拒绝经服务器或免费主机代理的请求。 |
| `405` | 仅支持 `GET`（CORS 的 `OPTIONS` 除外）。 |
| `502` | YouTube 未响应。请重试。 |

无匹配的查询返回 `200`，其中 `"success": true` 且 `"results": []`——这不是错误。

## 说明

- 无 API 密钥、无需登录、无速率限制响应头——请合理使用。
- `duration`、`views` 和 `publishedAt` 是 YouTube 显示的字符串，因此是展示文本而非精确数字。`durationSeconds` 是有时可用的数字时长。
- `uploaded=hour` 使用 YouTube 自身的过去一小时窗口，该窗口是近似的——少数稍旧的条目可能会混入。
- 结果来自 YouTube 搜索；得到什么取决于查询、地区（`gl`）和语言（`hl`）。
- CORS 已开放（`Access-Control-Allow-Origin: *`），因此 endpoint 可在任何网站或应用中使用。

## 关于

由 [FAK LABS](https://faizankhichi.me/) 打造——严肃的软件，正在生产环境中运行。
