# YouTube Search API

API pencarian YouTube dari [FAK LABS](https://faizankhichi.me/). Cari video, channel, playlist, Shorts, dan siaran langsung YouTube — dengan penyaringan, pengurutan, dan paginasi lengkap. Tidak perlu kunci API.

> **Repo ini hanya dokumentasi.** Repo ini menjelaskan endpoint publik dan cara menggunakannya. Repo ini tidak berisi kode.

## Baca dalam bahasa Anda

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Setiap permintaan mengembalikan JSON yang bersih. Satu-satunya parameter wajib adalah `q`.

## Mulai cepat

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Parameter

| Parameter | Nilai | Default | Catatan |
|---|---|---|---|
| `q` | teks pencarian (maks. 200 karakter) | — | **Wajib.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` mengembalikan hasil campuran video / channel / playlist (Shorts tidak termasuk — gunakan `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = di bawah 4 mnt, medium = 4–20 mnt, long = di atas 20 mnt. Siaran langsung dan mendatang tidak punya durasi, jadi tidak pernah dikembalikan saat filter durasi diatur. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Jendela tanggal unggah. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | kode bahasa, mis. `en`, `ur`, `es` | `en` | Bahasa antarmuka hasil. |
| `gl` | kode wilayah, mis. `US`, `PK`, `ES` | `US` | Wilayah tempat hasil dilokalkan. |
| `maxResults` | 1–50 | `20` | Nilai di atas 20 diisi otomatis dari halaman-halaman berikutnya. |
| `pageToken` | token lanjutan dari respons sebelumnya | — | Mengambil halaman berikutnya. Kirim `q` dan filter yang sama dengan token. |
| `features` | dipisah koma: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Gabungkan dengan filter lain apa pun, mis. `features=hd,subtitles`. |
| `suggest` | `1` | — | Mengembalikan saran pencarian, bukan hasil (lihat di bawah). |

Filter dapat digabung bebas — setiap parameter bekerja bersama:

```bash
# video pendek yang diunggah minggu ini, paling banyak ditonton dulu
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# channel, dalam bahasa Urdu, untuk Pakistan
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# video HD dengan subtitle
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Respons

Kolom tingkat atas:

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `success` | boolean | `true` saat permintaan berhasil. |
| `query` | string | Kueri seperti yang diterima. |
| `count` | number | Jumlah hasil dalam respons ini. |
| `source` | string | `"youtubei"` (normal) atau `"html"` (jalur cadangan). |
| `filters` | object | Filter yang diterapkan, dikembalikan apa adanya: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Kirim sebagai `pageToken` untuk mengambil halaman berikutnya. `null` saat tidak ada halaman lagi (Shorts tidak punya halaman berikutnya — raknya hanya satu halaman). |
| `results` | array | Hasil-hasilnya (lihat di bawah). |
| `suggestions` | array of strings | Hanya saat `suggest=1` — saran pencarian untuk kueri tersebut. |

### Contoh respons

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

## Kolom hasil

### Video (`type=video`)

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | Id video YouTube. |
| `title` | string | Judul video. |
| `description` | string | Cuplikan deskripsi. Bisa kosong — tidak semua video punya deskripsi. |
| `channel.id` | string | Id channel (`UC…`). |
| `channel.name` | string | Nama channel. |
| `channel.avatar` | string | URL gambar avatar channel (kosong jika tidak tersedia). |
| `thumbnails` | array | Semua ukuran thumbnail yang dikembalikan YouTube: `{url, width, height}`. |
| `thumbnail` | string | URL thumbnail terbesar. |
| `duration` | string | Durasi seperti yang ditampilkan di YouTube, mis. `"8:00:00"`, `"3:24"`. Kosong untuk siaran langsung / mendatang. |
| `durationSeconds` | number or `null` | Durasi dalam detik. `null` untuk siaran langsung / mendatang. |
| `views` | string | Jumlah penayangan seperti yang ditampilkan, mis. `"190,198 views"`. Kosong saat YouTube tidak menampilkan jumlah. |
| `publishedAt` | string | Waktu publikasi relatif seperti yang ditampilkan, mis. `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` untuk siaran langsung. |
| `isUpcoming` | boolean | `true` untuk siaran terjadwal (mendatang). |
| `hasCaptions` | boolean | `true` saat video punya teks (CC). |
| `badges` | array of strings | Lencana yang ditampilkan YouTube, mis. `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | URL tontonan: `https://www.youtube.com/watch?v=<id>`. |

### Channel (`type=channel`)

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Id channel (`UC…`). |
| `name` | string | Nama channel. |
| `handle` | string | Handle channel, mis. `"@LofiGirl"`. Kosong untuk channel tanpa handle (mis. channel Topik yang dibuat otomatis). |
| `subscribers` | string | Jumlah subscriber seperti yang ditampilkan, mis. `"15.8M subscribers"`. Kosong saat YouTube tidak menampilkan jumlah. |
| `videoCount` | string | Jumlah video seperti yang ditampilkan (saat YouTube menyediakannya). |
| `avatar` | string | URL gambar avatar channel. |
| `thumbnails` | array | Avatar dalam semua ukuran: `{url, width, height}`. |
| `verified` | boolean | `true` untuk channel terverifikasi dan channel artis resmi. |
| `description` | string | Cuplikan deskripsi channel (bisa kosong). |
| `url` | string | URL channel: `https://www.youtube.com/channel/<id>`. |

### Playlist (`type=playlist`)

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Id playlist (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Judul playlist. |
| `description` | string | Deskripsi playlist (bisa kosong). |
| `channel.id` | string | Id channel pemilik. |
| `channel.name` | string | Nama channel pemilik. |
| `channel.handle` | string | Handle channel pemilik (bisa kosong). |
| `videoCount` | string | Jumlah video seperti yang ditampilkan, mis. `"86 videos"`. |
| `videoCountNum` | number or `null` | Jumlah video sebagai angka. |
| `thumbnails` | array | Semua ukuran thumbnail: `{url, width, height}`. |
| `thumbnail` | string | URL thumbnail terbesar. |
| `url` | string | URL playlist: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Kolom | Tipe | Deskripsi |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Id Short. |
| `title` | string | Judul Short. |
| `channel` | `null` | Shorts tidak membawa info channel dalam respons pencarian. |
| `views` | string | Jumlah penayangan seperti yang ditampilkan, mis. `"50K views"`. |
| `thumbnails` | array | Ukuran thumbnail: `{url, width, height}` (potret, 405×720). |
| `thumbnail` | string | URL thumbnail terbesar. |
| `url` | string | URL Short: `https://www.youtube.com/shorts/<id>`. |

### Live (`type=live`)

Hasil live adalah objek video (`type=video`) dengan `isLive: true`, tanpa `duration` / `durationSeconds`, dan `views` menunjukkan jumlah penonton saat ini, mis. `"1.2K watching"`.

## Paginasi

```bash
# halaman 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# halaman 2 — kueri dan filter yang sama, ditambah token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

Halaman tidak pernah mengulang hasil. Meminta `maxResults` di atas 20 akan mengisi respons dari halaman-halaman berikutnya secara otomatis, jadi satu permintaan bisa mengembalikan hingga 50 hasil.

## Saran pencarian

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

## Error

| Status | Arti |
|---|---|
| `400` | `q` hilang, atau nilai tidak valid untuk `type`, `duration`, `uploaded`, `sort`, `features` atau `maxResults`. Body menyebutkan masalahnya. |
| `403` | Hanya akses langsung — endpoint menolak permintaan yang di-proxy melalui server atau host gratis. |
| `405` | Hanya `GET` (dan `OPTIONS` untuk CORS) yang didukung. |
| `502` | YouTube tidak menjawab. Coba lagi. |

Kueri tanpa kecocokan mengembalikan `200` dengan `"success": true` dan `"results": []` — bukan error.

## Catatan

- Tanpa kunci API, tanpa masuk, tanpa header batas laju — gunakan dengan wajar.
- `duration`, `views` dan `publishedAt` adalah string yang ditampilkan YouTube, jadi itu teks tampilan, bukan angka pasti. `durationSeconds` adalah durasi numerik saat tersedia.
- `uploaded=hour` memakai jendela satu jam terakhir milik YouTube sendiri, yang bersifat perkiraan — beberapa item yang sedikit lebih lama bisa masuk.
- Hasil berasal dari pencarian YouTube; yang Anda dapatkan tergantung pada kueri, wilayah (`gl`) dan bahasa (`hl`).
- CORS terbuka (`Access-Control-Allow-Origin: *`), jadi endpoint bekerja dari situs web atau aplikasi apa pun.

## Tentang

Dibuat oleh [FAK LABS](https://faizankhichi.me/) — perangkat lunak serius, berjalan di produksi.
