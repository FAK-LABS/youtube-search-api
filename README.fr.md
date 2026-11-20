# YouTube Search API

Une API de recherche YouTube par [FAK LABS](https://faizankhichi.me/). Recherchez des vidéos, des chaînes, des playlists, des Shorts et des diffusions en direct sur YouTube — avec filtrage, tri et pagination complets. Aucune clé API requise.

> **Ce dépôt est uniquement de la documentation.** Il décrit le endpoint public et comment l'utiliser. Il ne contient aucun code.

## Lire dans votre langue

- [🇬🇧 English](README.md) · [🇧🇷 Português](README.pt.md) · [🇸🇦 العربية (Arabic)](README.ar.md) · [🇫🇷 Français](README.fr.md) · [🇪🇸 Español](README.es.md) · [🇵🇰 اردو (Urdu)](README.ur.md) · [🇹🇿 Kiswahili](README.sw.md) · [🇩🇪 Deutsch](README.de.md) · [🇮🇩 Bahasa Indonesia](README.id.md) · [🇮🇳 हिन्दी (Hindi)](README.hi.md) · [🇦🇫 پښتو (Pashto)](README.ps.md) · [🇮🇷 فارسی (Farsi)](README.fa.md) · [🇨🇳 简体中文 (Chinese)](README.zh.md)

## Endpoint

```
GET https://yt-api.faizankhichi.me/?q=<query>
```

Chaque requête renvoie du JSON propre. Le seul paramètre obligatoire est `q`.

## Démarrage rapide

```bash
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22"
```

```js
const res = await fetch("https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short");
const data = await res.json();
data.results.forEach(v => console.log(v.title, v.url));
```

## Paramètres

| Paramètre | Valeurs | Par défaut | Notes |
|---|---|---|---|
| `q` | texte de recherche (max. 200 caractères) | — | **Obligatoire.** |
| `type` | `video`, `channel`, `playlist`, `shorts`, `live`, `movie`, `show`, `all` | `video` | `all` renvoie des résultats mixtes vidéos / chaînes / playlists (les Shorts ne sont pas inclus — utilisez `type=shorts`). |
| `duration` | `short`, `medium`, `long`, `all` | `all` | short = moins de 4 min, medium = 4–20 min, long = plus de 20 min. Les diffusions en direct et à venir n'ont pas de durée, elles ne sont donc jamais renvoyées quand un filtre de durée est défini. |
| `uploaded` | `hour`, `today`, `week`, `month`, `year`, `all` | `all` | Fenêtre de date de mise en ligne. |
| `sort` | `relevance`, `upload_date`, `view_count`, `rating` | `relevance` | |
| `hl` | code de langue, ex. : `en`, `ur`, `es` | `en` | Langue de l'interface des résultats. |
| `gl` | code de région, ex. : `US`, `PK`, `ES` | `US` | Région pour laquelle les résultats sont localisés. |
| `maxResults` | 1–50 | `20` | Les valeurs au-dessus de 20 sont remplies automatiquement à partir des pages suivantes. |
| `pageToken` | jeton de continuation d'une réponse précédente | — | Récupère la page suivante. Passez le même `q` et les mêmes filtres avec le jeton. |
| `features` | séparés par des virgules : `hd`, `subtitles`, `cc`, `creative_commons`, `3d`, `live`, `purchased`, `4k`, `360`, `location`, `hdr`, `vr180` | — | À combiner avec n'importe quel autre filtre, ex. : `features=hd,subtitles`. |
| `suggest` | `1` | — | Renvoie des suggestions de recherche au lieu de résultats (voir ci-dessous). |

Les filtres se combinent librement — tous les paramètres fonctionnent ensemble :

```bash
# vidéos courtes mises en ligne cette semaine, les plus vues d'abord
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&type=video&duration=short&uploaded=week&sort=view_count"

# chaînes, en ourdou, pour le Pakistan
curl "https://yt-api.faizankhichi.me/?q=naat&type=channel&hl=ur&gl=PK"

# vidéos HD avec sous-titres
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&features=hd,subtitles"
```

## Réponse

Champs de premier niveau :

| Champ | Type | Description |
|---|---|---|
| `success` | boolean | `true` quand la requête a réussi. |
| `query` | string | La requête telle que reçue. |
| `count` | number | Nombre de résultats dans cette réponse. |
| `source` | string | `"youtubei"` (normal) ou `"html"` (chemin de secours). |
| `filters` | object | Les filtres appliqués, renvoyés tels quels : `type`, `duration`, `uploaded`, `sort`, `hl`, `gl`, `maxResults`, `features`. |
| `nextPageToken` | string or `null` | À passer comme `pageToken` pour récupérer la page suivante. `null` quand il n'y a plus de pages (les Shorts n'ont pas de page suivante — l'étagère est une seule page). |
| `results` | array | Les résultats (voir ci-dessous). |
| `suggestions` | array of strings | Uniquement quand `suggest=1` — suggestions de recherche pour la requête. |

### Exemple de réponse

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

## Champs des résultats

### Vidéo (`type=video`)

| Champ | Type | Description |
|---|---|---|
| `type` | string | `"video"`. |
| `id` | string | Identifiant de la vidéo YouTube. |
| `title` | string | Titre de la vidéo. |
| `description` | string | Extrait de la description. Peut être vide — toutes les vidéos n'en ont pas. |
| `channel.id` | string | Identifiant de la chaîne (`UC…`). |
| `channel.name` | string | Nom de la chaîne. |
| `channel.avatar` | string | URL de l'image d'avatar de la chaîne (vide si indisponible). |
| `thumbnails` | array | Toutes les tailles de miniatures renvoyées par YouTube : `{url, width, height}`. |
| `thumbnail` | string | URL de la plus grande miniature. |
| `duration` | string | Durée telle qu'affichée sur YouTube, ex. : `"8:00:00"`, `"3:24"`. Vide pour les diffusions en direct / à venir. |
| `durationSeconds` | number or `null` | Durée en secondes. `null` pour les diffusions en direct / à venir. |
| `views` | string | Nombre de vues tel qu'affiché, ex. : `"190,198 views"`. Vide quand YouTube n'en affiche aucun. |
| `publishedAt` | string | Date de publication relative telle qu'affichée, ex. : `"1 day ago"`, `"3 weeks ago"`. |
| `isLive` | boolean | `true` pour une diffusion en direct. |
| `isUpcoming` | boolean | `true` pour une diffusion programmée (à venir). |
| `hasCaptions` | boolean | `true` quand la vidéo a des sous-titres (CC). |
| `badges` | array of strings | Badges affichés par YouTube, ex. : `"New"`, `"LIVE"`, `"CC"`, `"4K"`. |
| `url` | string | URL de visionnage : `https://www.youtube.com/watch?v=<id>`. |

### Chaîne (`type=channel`)

| Champ | Type | Description |
|---|---|---|
| `type` | string | `"channel"`. |
| `id` | string | Identifiant de la chaîne (`UC…`). |
| `name` | string | Nom de la chaîne. |
| `handle` | string | Identifiant de la chaîne, ex. : `"@LofiGirl"`. Vide pour les chaînes sans identifiant (ex. : chaînes Thème générées automatiquement). |
| `subscribers` | string | Nombre d'abonnés tel qu'affiché, ex. : `"15.8M subscribers"`. Vide quand YouTube n'en affiche aucun. |
| `videoCount` | string | Nombre de vidéos tel qu'affiché (quand YouTube le fournit). |
| `avatar` | string | URL de l'image d'avatar de la chaîne. |
| `thumbnails` | array | Avatar dans toutes les tailles : `{url, width, height}`. |
| `verified` | boolean | `true` pour les chaînes vérifiées et les chaînes officielles d'artistes. |
| `description` | string | Extrait de la description de la chaîne (peut être vide). |
| `url` | string | URL de la chaîne : `https://www.youtube.com/channel/<id>`. |

### Playlist (`type=playlist`)

| Champ | Type | Description |
|---|---|---|
| `type` | string | `"playlist"`. |
| `id` | string | Identifiant de la playlist (`PL…`, `UU…`, `RD…`, …). |
| `title` | string | Titre de la playlist. |
| `description` | string | Description de la playlist (peut être vide). |
| `channel.id` | string | Identifiant de la chaîne propriétaire. |
| `channel.name` | string | Nom de la chaîne propriétaire. |
| `channel.handle` | string | Identifiant de la chaîne propriétaire (peut être vide). |
| `videoCount` | string | Nombre de vidéos tel qu'affiché, ex. : `"86 videos"`. |
| `videoCountNum` | number or `null` | Nombre de vidéos sous forme de nombre. |
| `thumbnails` | array | Toutes les tailles de miniatures : `{url, width, height}`. |
| `thumbnail` | string | URL de la plus grande miniature. |
| `url` | string | URL de la playlist : `https://www.youtube.com/playlist?list=<id>`. |

### Short (`type=shorts`)

| Champ | Type | Description |
|---|---|---|
| `type` | string | `"short"`. |
| `id` | string | Identifiant du Short. |
| `title` | string | Titre du Short. |
| `channel` | `null` | Les Shorts ne portent pas d'informations de chaîne dans la réponse de recherche. |
| `views` | string | Nombre de vues tel qu'affiché, ex. : `"50K views"`. |
| `thumbnails` | array | Tailles de miniatures : `{url, width, height}` (portrait, 405×720). |
| `thumbnail` | string | URL de la plus grande miniature. |
| `url` | string | URL du Short : `https://www.youtube.com/shorts/<id>`. |

### En direct (`type=live`)

Les résultats en direct sont des objets vidéo (`type=video`) avec `isLive: true`, sans `duration` / `durationSeconds`, et `views` indiquant le nombre de spectateurs actuels, ex. : `"1.2K watching"`.

## Pagination

```bash
# page 1
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5"
# → "nextPageToken": "ErsCEgoiRkFLI..."

# page 2 — même requête et mêmes filtres, plus le jeton
curl "https://yt-api.faizankhichi.me/?q=%22FAK%20LABS%22&maxResults=5&pageToken=ErsCEgoiRkFLI..."
```

Les pages ne répètent jamais les résultats. Demander `maxResults` au-dessus de 20 remplit la réponse à partir des pages suivantes automatiquement, donc une seule requête peut renvoyer jusqu'à 50 résultats.

## Suggestions de recherche

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

## Erreurs

| Statut | Signification |
|---|---|
| `400` | `q` manquant, ou valeur invalide pour `type`, `duration`, `uploaded`, `sort`, `features` ou `maxResults`. Le corps indique le problème. |
| `403` | Accès direct uniquement — l'endpoint refuse les requêtes relayées par un serveur ou un hébergeur gratuit. |
| `405` | Seul `GET` (et `OPTIONS` pour CORS) est pris en charge. |
| `502` | YouTube n'a pas répondu. Réessayez. |

Une requête sans résultat renvoie `200` avec `"success": true` et `"results": []` — ce n'est pas une erreur.

## Notes

- Pas de clé API, pas de connexion, pas d'en-tête de limite de débit — utilisez-le raisonnablement.
- `duration`, `views` et `publishedAt` sont les chaînes affichées par YouTube : ce sont donc des textes d'affichage, pas des nombres exacts. `durationSeconds` est la durée numérique quand elle est disponible.
- `uploaded=hour` utilise la propre fenêtre de la dernière heure de YouTube, qui est approximative — quelques éléments un peu plus anciens peuvent s'y glisser.
- Les résultats proviennent de la recherche YouTube ; ce que vous obtenez dépend de la requête, de la région (`gl`) et de la langue (`hl`).
- Le CORS est ouvert (`Access-Control-Allow-Origin: *`), donc l'endpoint fonctionne depuis n'importe quel site ou application.

## À propos

Conçu par [FAK LABS](https://faizankhichi.me/) — du logiciel sérieux, en production.
