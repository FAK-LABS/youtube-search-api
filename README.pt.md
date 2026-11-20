# YouTube Search API

Uma API de pesquisa do YouTube da [FAK LABS](https://faizankhichi.me/). Pesquise vídeos, canais, playlists, Shorts e transmissões ao vivo do YouTube — com filtragem, ordenação e paginação completas. Não é necessária chave de API.

> **Este repositório é apenas documentação.** Descreve o endpoint público e como usá-lo. Não contém código.

## Leia em seu idioma

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Cada requisição retorna JSON limpo. O único parâmetro obrigatório é `q`.

## Início rápido

```bash
curl "https://yt-api.faizankhichi.me/?q=lofi"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Parâmetros

| Parâmetro | Valores | Padrão | Notas |
|---|---|---|---|
| `q` | texto da pesquisa (máx. 200 caracteres) | — | **Obrigatório.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` retorna resultados mistos de vídeos / canais / playlists (Shorts não estão incluídos — use `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = menos de 4 min, medium = 4–20 min, long = mais de 20 min. Transmissões ao vivo e futuras não têm duração, então nunca são retornadas quando um filtro de duração é definido. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Janela de data de envio. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | código de idioma, ex.: `en`, `ur`, `es` | `en` | Idioma da interface dos resultados. |
| `gl` | código de região, ex.: `US`, `PK`, `ES` | `US` | Região para a qual os resultados são localizados. |
| `maxResults` | 1–50 | `20` | Valores acima de 20 são preenchidos automaticamente a partir de outras páginas. |
| `pageToken` | token de continuação de uma resposta anterior | — | Busca a próxima página. Passe o mesmo `q` e filtros com o token. |
| `features` | separados por vírgula: `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | Combine com qualquer outro filtro, ex.: `features=hd,subtitles`. |
| `suggest` | `1` | — | Retorna sugestões de pesquisa em vez de resultados (veja abaixo). |

Os filtros combinam livremente — todos os parâmetros funcionam juntos:

```bash
# vídeos curtos enviados esta semana, mais vistos primeiro
curl "https://yt-api.faizankhichi.me/?q=lofi&type=video&duration=short&uploaded=week&sort=view_count"

# canais, em urdu, para o Paquistão
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# vídeos em HD com legendas
curl "https://yt-api.faizankhichi.me/?q=lofi&features=hd,subtitles"
```

## Resposta

Campos de nível superior:

| Campo | Tipo | Descrição |
|---|---|---|
| `success` | boolean | `true` quando a requisição funcionou. |
| `query` | string | A consulta como recebida. |
| `count` | number | Número de resultados nesta resposta. |
| `source` | string | `"youtubei"` (normal) ou `"html"` (caminho alternativo). |
| `filters` | object | Os filtros aplicados, devolvidos: `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | Passe como `pageToken` para buscar a próxima página. `null` quando não há mais páginas (Shorts não têm próxima página — a prateleira é uma única página). |
| `results` | array | Os resultados (veja abaixo). |
| `suggestions` | array of strings | Somente quando `suggest=1` — sugestões de pesquisa para a consulta. |

### Exemplo de resposta

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

## Campos dos resultados

### Vídeo (`type=video`)

| Campo | Tipo | Descrição |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | Id do vídeo no YouTube. |
| `title` | string | Título do vídeo. |
| `description` | string | Trecho da descrição. Pode estar vazio — nem todo vídeo tem uma. |
| `channel.id` | string | Id do canal (`UC…`). |
| `channel.name` | string | Nome do canal. |
| `channel.avatar` | string | URL da imagem do avatar do canal (vazia quando indisponível). |
| `thumbnails` | array | Todos os tamanhos de miniatura que o YouTube retorna: `{url, width, height}`. |
| `thumbnail` | string | URL da maior miniatura. |
| `duration` | string | Duração como mostrada no YouTube, ex.: `"8:00:00"`, `"3:24"`. Vazia para transmissões ao vivo / futuras. |
| `durationSeconds` | number or `null` | Duração em segundos. `null` para transmissões ao vivo / futuras. |
| `views` | string | Contagem de visualizações como mostrada, ex.: `"190,198 views"`. Vazia quando o YouTube não mostra nenhuma. |
| `publishedAt` | string | Tempo relativo de publicação como mostrado, ex.: `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` para uma transmissão ao vivo. |
| `isUpcoming` | boolean | `true` para uma transmissão agendada (futura). |
| `hasCaptions` | boolean | `true` quando o vídeo tem legendas (CC). |
| `badges` | array of strings | Selos que o YouTube mostra, ex.: `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | URL de exibição: `https://www.youtube.com/watch?v=<id>`. |

### Canal (`type=channel`)

| Campo | Tipo | Descrição |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Id do canal (`UC…`). |
| `name` | string | Nome do canal. |
| `handle` | string | Identificador do canal, ex.: `"@LofiGirl"`. Vazio para canais sem um (ex.: canais de Tópico gerados automaticamente). |
| `subscribers` | string | Contagem de inscritos como mostrada, ex.: `"15.8M subscribers"`. Vazia quando o YouTube não mostra nenhuma. |
| `videoCount` | string | Contagem de vídeos como mostrada (quando o YouTube a fornece). |
| `avatar` | string | URL da imagem do avatar do canal. |
| `thumbnails` | array | Avatar em todos os tamanhos: `{url, width, height}`. |
| `verified` | boolean | `true` para canais verificados e canais oficiais de artista. |
| `description` | string | Trecho da descrição do canal (pode estar vazio). |
| `url` | string | URL do canal: `https://www.youtube.com/channel/<id>`. |

### Playlist (`type=playlist`)

| Campo | Tipo | Descrição |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Id da playlist (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Título da playlist. |
| `description` | string | Descrição da playlist (pode estar vazia). |
| `channel.id` | string | Id do canal proprietário. |
| `channel.name` | string | Nome do canal proprietário. |
| `channel.handle` | string | Identificador do canal proprietário (pode estar vazio). |
| `videoCount` | string | Contagem de vídeos como mostrada, ex.: `"86 videos"`. |
| `videoCountNum` | number or `null` | Contagem de vídeos como número. |
| `thumbnails` | array | Todos os tamanhos de miniatura: `{url, width, height}`. |
| `thumbnail` | string | URL da maior miniatura. |
| `url` | string | URL da playlist: `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Campo | Tipo | Descrição |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Id do Short. |
| `title` | string | Título do Short. |
| `channel` | `null` | Shorts não trazem informações de canal na resposta de pesquisa. |
| `views` | string | Contagem de visualizações como mostrada, ex.: `"50K views"`. |
| `thumbnails` | array | Tamanhos de miniatura: `{url, width, height}` (retrato, 405×720). |
| `thumbnail` | string | URL da maior miniatura. |
| `url` | string | URL do Short: `https://www.youtube.com/shorts/<id>`. |

### Ao vivo (`type=live`)

Os resultados ao vivo são objetos de vídeo (`type=video`) com `isLive: true`, sem `duration` / `durationSeconds`, e `views` mostrando a contagem atual de espectadores, ex.: `"1.2K watching"`.

## Paginação

```bash
# página 1
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5"
# → "nextPageToken": "EpkDEgRsb2Zp..."

# página 2 — mesma consulta e filtros, mais o token
curl "https://yt-api.faizankhichi.me/?q=lofi&maxResults=5&pageToken=EpkDEgRsb2Zp..."
```

As páginas nunca repetem resultados. Pedir `maxResults` acima de 20 preenche a resposta a partir de outras páginas automaticamente, então uma requisição pode retornar até 50 resultados.

## Sugestões de pesquisa

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

## Erros

| Status | Significado |
|---|---|
| `400` | Falta o `q`, ou um valor inválido para `type`, `duration`, `uploaded`, `sort`, `features` ou `maxResults`. O corpo indica o problema. |
| `403` | Apenas acesso direto — o endpoint recusa requisições feitas por proxy através de um servidor ou host gratuito. |
| `405` | Apenas `GET` (e `OPTIONS` para CORS) é suportado. |
| `502` | O YouTube não respondeu. Tente novamente. |

Uma consulta sem resultados retorna `200` com `"success": true` e `"results": []` — não é um erro.

## Notas

- Sem chave de API, sem login, sem cabeçalho de limite de taxa — use com bom senso.
- `duration`, `views` e `publishedAt` são as strings que o YouTube mostra, portanto são texto de exibição, não números exatos. `durationSeconds` é a duração numérica quando disponível.
- `uploaded=hour` usa a própria janela da última hora do YouTube, que é aproximada — alguns itens um pouco mais antigos podem aparecer.
- Os resultados vêm da pesquisa do YouTube; o que você recebe depende da consulta, da região (`gl`) e do idioma (`hl`).
- O CORS é aberto (`Access-Control-Allow-Origin: *`), então o endpoint funciona de qualquer site ou aplicativo.

## Sobre

Feito por [FAK LABS](https://faizankhichi.me/) — software sério, rodando em produção.
