# YouTube Search API

A YouTube search API by [FAK LABS](https://faizankhichi.me/). Search YouTube videos, channels, playlists, Shorts and live streams — with full filtering, sorting and pagination. No API key needed.

> **This repo is documentation only.** It describes the public endpoint and how to use it. It contains no code.

## Read this in your language

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Every request returns clean JSON. The only required parameter is `q`.

## Quick start

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Parameters

| Parameter | Values | Default | Notes |
|---|---|---|---|
| `q` | search text (max 200 chars) | — | **Required.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` returns mixed video / channel / playlist results (Shorts are not included — use `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = under 4 min, medium = 4–20 min, long = over 20 min. Live and upcoming streams have no duration, so they are never returned when a duration filter is set. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Upload date window. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | language code, e.g. `en`, `ur`, `es` | `en` | Interface language of the results. |
| `gl` | region code, e.g. `US`, `PK`, `ES` | `US` | Region the results are localised for. |
| `maxResults` | 1–50 | `20` | Values above 20 are filled automatically from further pages. |
| `pageToken` | continuation token from a previous response | — | Fetch the next page. Pass the same `q` and filters with the token. |
| `features` | comma-separated: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Combine with any other filter, e.g. `features=hd,subtitles`. |
| `suggest` | `1` | — | Returns search suggestions instead of results (see below). |

Filters combine freely — every parameter works together:

```bash
# short videos uploaded this week, most viewed first
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# channels, in Urdu, for Pakistan
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# HD videos with subtitles
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Response

Top-level fields:

| Field | Type | Description |
|---|---|---|
| `success` | boolean | `true` when the request worked. |
| `query` | string | The query as received. |
| `count` | number | Number of results in this response. |
| `source` | string | `"youtubei"` (normal) or `"html"` (fallback path). |
| `filters` | object | The filters that were applied, echoed back: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Pass as `pageToken` to fetch the next page. `null` when there are no more pages (Shorts have no next page — the shelf is one page). |
| `results` | array | The results (see below). |
| `suggestions` | array of strings | Only when `suggest=1` — search suggestions for the query. |

### Example response

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

## Result fields

### Video (`type=video`)

| Field | Type | Description |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | YouTube video id. |
| `title` | string | Video title. |
| `description` | string | Description snippet. May be empty — not every video carries one. |
| `channel.id` | string | Channel id (`UC…`). |
| `channel.name` | string | Channel name. |
| `channel.avatar` | string | Channel avatar image URL (empty when unavailable). |
| `thumbnails` | array | All thumbnail sizes YouTube returns: `{url, width, height}`. |
| `thumbnail` | string | Largest thumbnail URL. |
| `duration` | string | Length as shown on YouTube, e.g. `"8:00:00"`, `"3:24"`. Empty for live / upcoming streams. |
| `durationSeconds` | number or `null` | Length in seconds. `null` for live / upcoming streams. |
| `views` | string | View count as shown, e.g. `"190,198 views"`. Empty when YouTube shows none. |
| `publishedAt` | string | Relative publish time as shown, e.g. `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` for a live stream. |
| `isUpcoming` | boolean | `true` for a scheduled (upcoming) stream. |
| `hasCaptions` | boolean | `true` when the video has captions (CC). |
| `badges` | array of strings | Badges YouTube shows, e.g. `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | Watch URL: `https://www.youtube.com/watch?v=<id>`. |

### Channel (`type=channel`)

| Field | Type | Description |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Channel id (`UC…`). |
| `name` | string | Channel name. |
| `handle` | string | Channel handle, e.g. `"@LofiGirl"`. Empty for channels without one (e.g. auto-generated Topic channels). |
| `subscribers` | string | Subscriber count as shown, e.g. `"15.8M subscribers"`. Empty when YouTube shows none. |
| `videoCount` | string | Video count as shown (when YouTube provides it). |
| `avatar` | string | Channel avatar image URL. |
| `thumbnails` | array | Avatar in all sizes: `{url, width, height}`. |
| `verified` | boolean | `true` for verified channels and official artist channels. |
| `description` | string | Channel description snippet (may be empty). |
| `url` | string | Channel URL: `https://www.youtube.com/channel/<id>`. |

### Playlist (`type=playlist`)

| Field | Type | Description |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Playlist id (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Playlist title. |
| `description` | string | Playlist description (may be empty). |
| `channel.id` | string | Owning channel id. |
| `channel.name` | string | Owning channel name. |
| `channel.handle` | string | Owning channel handle (may be empty). |
| `videoCount` | string | Video count as shown, e.g. `"86 videos"`. |
| `videoCountNum` | number or `null` | Video count as a number. |
| `thumbnails` | array | All thumbnail sizes: `{url, width, height}`. |
| `thumbnail` | string | Largest thumbnail URL. |
| `url` | string | Playlist URL: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Field | Type | Description |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Short video id. |
| `title` | string | Short title. |
| `channel` | `null` | Shorts do not carry channel info in the search response. |
| `views` | string | View count as shown, e.g. `"50K views"`. |
| `thumbnails` | array | Thumbnail sizes: `{url, width, height}` (portrait, 405×720). |
| `thumbnail` | string | Largest thumbnail URL. |
| `url` | string | Shorts URL: `https://www.youtube.com/shorts/<id>`. |

### Live (`type=live`)

Live results are video objects (`type=video`) with `isLive: true`, no `duration` / `durationSeconds`, and `views` showing the current watcher count, e.g. `"1.2K watching"`.

## Pagination

```bash
# page 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# page 2 — same query and filters, plus the token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

Pages never repeat results. Asking for `maxResults` above 20 fills the response from further pages automatically, so one request can return up to 50 results.

## Search suggestions

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

## Errors

| Status | Meaning |
|---|---|
| `400` | Missing `q`, or an invalid value for `type`, `duration`, `uploaded`, `sort`, `features` or `maxResults`. The body names the problem. |
| `403` | Direct access only — the endpoint refuses requests proxied through a server or free host. |
| `405` | Only `GET` (and `OPTIONS` for CORS) is supported. |
| `502` | YouTube did not answer. Retry. |

A query with no matches returns `200` with `"success": true` and `"results": []` — not an error.

## Notes

- No API key, no sign-in, no rate-limit header — use it reasonably.
- `duration`, `views` and `publishedAt` are the strings YouTube shows, so they are display text, not exact numbers. `durationSeconds` is the numeric duration when available.
- `uploaded=hour` uses YouTube's own last-hour window, which is approximate — a few slightly older items can slip in.
- Results come from YouTube's search; what you get depends on the query, region (`gl`) and language (`hl`).
- CORS is open (`Access-Control-Allow-Origin: *`), so the endpoint works from any website or app.

## About

Built by [FAK LABS](https://faizankhichi.me/) — serious software, running in production.
