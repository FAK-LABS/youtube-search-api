# YouTube Search API

Una API de búsqueda de YouTube de [FAK LABS](https://faizankhichi.me/). Busca vídeos, canales, listas de reproducción, Shorts y transmisiones en vivo de YouTube — con filtrado, orden y paginación completos. No se necesita clave de API.

> **Este repositorio es solo documentación.** Describe el endpoint público y cómo usarlo. No contiene código.

## Leer en tu idioma

- [English](README.md) · [Português](README.pt.md) · [العربية](README.ar.md) · [Français](README.fr.md) · [Español](README.es.md) · [اردو](README.ur.md) · [Kiswahili](README.sw.md) · [Deutsch](README.de.md) · [Bahasa Indonesia](README.id.md) · [हिन्दी](README.hi.md) · [پښتو](README.ps.md) · [فارسی](README.fa.md) · [简体中文](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Cada solicitud devuelve JSON limpio. El único parámetro obligatorio es `q`.

## Inicio rápido

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Parámetros

| Parámetro | Valores | Predeterminado | Notas |
|---|---|---|---|
| `q` | texto de búsqueda (máx. 200 caracteres) | — | **Obligatorio.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` devuelve resultados mixtos de vídeos / canales / listas de reproducción (los Shorts no están incluidos — usa `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = menos de 4 min, medium = 4–20 min, long = más de 20 min. Las transmisiones en vivo y próximas no tienen duración, así que nunca se devuelven cuando se define un filtro de duración. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Ventana de fecha de subida. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | código de idioma, ej.: `en`, `ur`, `es` | `en` | Idioma de la interfaz de los resultados. |
| `gl` | código de región, ej.: `US`, `PK`, `ES` | `US` | Región para la que se localizan los resultados. |
| `maxResults` | 1–50 | `20` | Los valores por encima de 20 se completan automáticamente desde otras páginas. |
| `pageToken` | token de continuación de una respuesta anterior | — | Obtiene la siguiente página. Pasa el mismo `q` y filtros con el token. |
| `features` | separados por comas: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Combina con cualquier otro filtro, ej.: `features=hd,subtitles`. |
| `suggest` | `1` | — | Devuelve sugerencias de búsqueda en lugar de resultados (ver abajo). |

Los filtros se combinan libremente — todos los parámetros funcionan juntos:

```bash
# vídeos cortos subidos esta semana, los más vistos primero
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# canales, en urdu, para Pakistán
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# vídeos en HD con subtítulos
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Respuesta

Campos de nivel superior:

| Campo | Tipo | Descripción |
|---|---|---|
| `success` | boolean | `true` cuando la solicitud funcionó. |
| `query` | string | La consulta tal como se recibió. |
| `count` | number | Número de resultados en esta respuesta. |
| `source` | string | `"youtubei"` (normal) o `"html"` (ruta alternativa). |
| `filters` | object | Los filtros aplicados, devueltos tal cual: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Pásalo como `pageToken` para obtener la siguiente página. `null` cuando no hay más páginas (los Shorts no tienen página siguiente — el estante es una sola página). |
| `results` | array | Los resultados (ver abajo). |
| `suggestions` | array of strings | Solo cuando `suggest=1` — sugerencias de búsqueda para la consulta. |

### Ejemplo de respuesta

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

## Campos de los resultados

### Vídeo (`type=video`)

| Campo | Tipo | Descripción |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | Id del vídeo de YouTube. |
| `title` | string | Título del vídeo. |
| `description` | string | Fragmento de la descripción. Puede estar vacío — no todos los vídeos tienen uno. |
| `channel.id` | string | Id del canal (`UC…`). |
| `channel.name` | string | Nombre del canal. |
| `channel.avatar` | string | URL de la imagen de avatar del canal (vacía cuando no está disponible). |
| `thumbnails` | array | Todos los tamaños de miniatura que devuelve YouTube: `{url, width, height}`. |
| `thumbnail` | string | URL de la miniatura más grande. |
| `duration` | string | Duración como se muestra en YouTube, ej.: `"8:00:00"`, `"3:24"`. Vacía para transmisiones en vivo / próximas. |
| `durationSeconds` | number or `null` | Duración en segundos. `null` para transmisiones en vivo / próximas. |
| `views` | string | Cantidad de vistas como se muestra, ej.: `"190,198 views"`. Vacía cuando YouTube no muestra ninguna. |
| `publishedAt` | string | Tiempo relativo de publicación como se muestra, ej.: `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` para una transmisión en vivo. |
| `isUpcoming` | boolean | `true` para una transmisión programada (próxima). |
| `hasCaptions` | boolean | `true` cuando el vídeo tiene subtítulos (CC). |
| `badges` | array of strings | Insignias que muestra YouTube, ej.: `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | URL para ver: `https://www.youtube.com/watch?v=<id>`. |

### Canal (`type=channel`)

| Campo | Tipo | Descripción |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Id del canal (`UC…`). |
| `name` | string | Nombre del canal. |
| `handle` | string | Identificador del canal, ej.: `"@LofiGirl"`. Vacío para canales sin uno (ej.: canales de Tema generados automáticamente). |
| `subscribers` | string | Cantidad de suscriptores como se muestra, ej.: `"15.8M subscribers"`. Vacía cuando YouTube no muestra ninguna. |
| `videoCount` | string | Cantidad de vídeos como se muestra (cuando YouTube la proporciona). |
| `avatar` | string | URL de la imagen de avatar del canal. |
| `thumbnails` | array | Avatar en todos los tamaños: `{url, width, height}`. |
| `verified` | boolean | `true` para canales verificados y canales oficiales de artistas. |
| `description` | string | Fragmento de la descripción del canal (puede estar vacío). |
| `url` | string | URL del canal: `https://www.youtube.com/channel/<id>`. |

### Lista de reproducción (`type=playlist`)

| Campo | Tipo | Descripción |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Id de la lista (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Título de la lista. |
| `description` | string | Descripción de la lista (puede estar vacía). |
| `channel.id` | string | Id del canal propietario. |
| `channel.name` | string | Nombre del canal propietario. |
| `channel.handle` | string | Identificador del canal propietario (puede estar vacío). |
| `videoCount` | string | Cantidad de vídeos como se muestra, ej.: `"86 videos"`. |
| `videoCountNum` | number or `null` | Cantidad de vídeos como número. |
| `thumbnails` | array | Todos los tamaños de miniatura: `{url, width, height}`. |
| `thumbnail` | string | URL de la miniatura más grande. |
| `url` | string | URL de la lista: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Campo | Tipo | Descripción |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Id del Short. |
| `title` | string | Título del Short. |
| `channel` | `null` | Los Shorts no llevan información del canal en la respuesta de búsqueda. |
| `views` | string | Cantidad de vistas como se muestra, ej.: `"50K views"`. |
| `thumbnails` | array | Tamaños de miniatura: `{url, width, height}` (vertical, 405×720). |
| `thumbnail` | string | URL de la miniatura más grande. |
| `url` | string | URL del Short: `https://www.youtube.com/shorts/<id>`. |

### En vivo (`type=live`)

Los resultados en vivo son objetos de vídeo (`type=video`) con `isLive: true`, sin `duration` / `durationSeconds`, y `views` muestra la cantidad actual de espectadores, ej.: `"1.2K watching"`.

## Paginación

```bash
# página 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# página 2 — misma consulta y filtros, más el token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

Las páginas nunca repiten resultados. Pedir `maxResults` por encima de 20 completa la respuesta desde otras páginas automáticamente, así que una sola solicitud puede devolver hasta 50 resultados.

## Sugerencias de búsqueda

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

## Errores

| Estado | Significado |
|---|---|
| `400` | Falta `q`, o un valor no válido para `type`, `duration`, `uploaded`, `sort`, `features` o `maxResults`. El cuerpo indica el problema. |
| `403` | Solo acceso directo — el endpoint rechaza solicitudes enviadas por proxy a través de un servidor o un host gratuito. |
| `405` | Solo se admite `GET` (y `OPTIONS` para CORS). |
| `502` | YouTube no respondió. Reintenta. |

Una consulta sin coincidencias devuelve `200` con `"success": true` y `"results": []` — no es un error.

## Notas

- Sin clave de API, sin inicio de sesión, sin encabezado de límite de velocidad — úsalo con moderación.
- `duration`, `views` y `publishedAt` son las cadenas que muestra YouTube, así que son texto de visualización, no números exactos. `durationSeconds` es la duración numérica cuando está disponible.
- `uploaded=hour` usa la propia ventana de la última hora de YouTube, que es aproximada — algunos elementos un poco más antiguos pueden colarse.
- Los resultados vienen de la búsqueda de YouTube; lo que obtienes depende de la consulta, la región (`gl`) y el idioma (`hl`).
- CORS está abierto (`Access-Control-Allow-Origin: *`), así que el endpoint funciona desde cualquier sitio web o aplicación.

## Acerca de

Creado por [FAK LABS](https://faizankhichi.me/) — software serio, en producción.
