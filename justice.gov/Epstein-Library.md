# Justice.gov – Multimedia Search API

## Méthode

`GET`

## Auth

Aucune ❌

## Description

Retourne les documents multimédias publics indexés par **justice.gov** (PDF, fichiers, pièces jointes, documents judiciaires, etc.) correspondant à des mots-clés.

Les résultats sont fournis au format **JSON** avec métadonnées complètes :

- Nom du fichier & URL directe
- Type MIME & taille
- Découpage (chunking) et pages PDF concernées
- Score de pertinence (Elasticsearch)
- Extraits de contenu avec mots-clés surligués (highlight)
- Infos de stockage back-end (S3)

---

## Endpoint principal

```
https://www.justice.gov/multimedia-search
```

---

## Paramètres

| Nom    | Type   | Description                    |
|--------|--------|--------------------------------|
| keys   | string | Mots-clés de recherche         |
| page   | int    | Numéro de page (pagination)    |

---

## Exemple de requête

```
https://www.justice.gov/multimedia-search?keys=M&page=1
```

---

## Exemple de réponse (complet)

```json
{
  "hits": {
    "total": {
      "value": 88129,
      "relation": "gte"
    },
    "max_score": 6.780701,
    "hits": [
      {
        "_source": {
          "documentId": "caaa7e6b",
          "ORIGIN_FILE_NAME": "EFTA02344444.pdf",
          "ORIGIN_FILE_URI": "https://www.justice.gov/epstein/files/DataSet 11/EFTA02344444.pdf",
          "contentType": "application/pdf",
          "fileSize": 1161738,
          "totalWords": 2328,
          "totalCharacters": 18427,
          "processedAt": "2026-01-30T10:22:33Z",
          "indexedAt": "2026-01-30T10:22:34Z",
          "source": "document-indexing-pipeline",

          // Chunking (découpage pour l'indexation)
          "chunkIndex": 0,
          "totalChunks": 3,
          "charStart": 0,
          "charEnd": 7634,
          "chunkSize": 7634,
          "isChunked": true,

          // Pages PDF concernées par ce chunk
          "startPage": 1,
          "endPage": 29,

          // Stockage back-end (S3)
          "bucket": "amazon-s3x-sync",
          "key": "DataSet 11/EFTA02344444.pdf"
        },
        "fields": {
          "ORIGIN_FILE_NAME": ["EFTA02344444.pdf"]
        },
        "highlight": {
          "content": [
            "extrait 1 avec <em>mot-clé</em> surligné...",
            "extrait 2 avec <em>mot-clé</em> surligné..."
          ]
        }
      }
    ]
  }
}
```

---

## Champs importants

### Niveau racine

| Champ                          | Description                                          |
|--------------------------------|------------------------------------------------------|
| `hits.total.value`             | Nombre total de résultats                            |
| `hits.total.relation`          | `gte` = résultat approximate (≥)                     |
| `hits.max_score`               | Score de pertinence le plus élevé (Elasticsearch)    |
| `hits.hits[]`                  | Liste des documents trouvés                          |

### `_source` — Métadonnées du fichier

| Champ                          | Description                                          |
|--------------------------------|------------------------------------------------------|
| `documentId`                   | Identifiant unique du document                       |
| `ORIGIN_FILE_NAME`             | Nom du fichier original                              |
| `ORIGIN_FILE_URI`              | URL directe de téléchargement                        |
| `contentType`                  | Type MIME (pdf, image, etc.)                         |
| `fileSize`                     | Taille en octets                                     |
| `totalWords`                   | Nombre total de mots dans le fichier                 |
| `totalCharacters`              | Nombre total de caractères dans le fichier           |
| `processedAt`                  | Date de traitement du fichier                        |
| `indexedAt`                    | Date d'indexation dans le moteur de recherche        |
| `source`                       | Pipeline d'origine (`document-indexing-pipeline`)    |
|--------------------------------|------------------------------------------------------|

### `_source` — Chunking (découpage)

| Champ                          | Description                                          |
|--------------------------------|------------------------------------------------------|
| `isChunked`                    | `true` si le fichier est découpé en plusieurs chunks |
| `totalChunks`                  | Nombre total de chunks pour ce fichier               |
| `chunkIndex`                   | Index du chunk actuel (commence à 0)                 |
| `chunkSize`                    | Taille de ce chunk en caractères                     |
| `charStart`                    | Position de début du chunk dans le fichier           |
| `charEnd`                      | Position de fin du chunk dans le fichier             |
| `startPage`                    | Première page PDF concernée par ce chunk             |
| `endPage`                      | Dernière page PDF concernée par ce chunk             |
|--------------------------------|------------------------------------------------------|

### `_source` — Stockage back-end

| Champ                          | Description                                          |
|--------------------------------|------------------------------------------------------|
| `bucket`                       | Nom du bucket S3 Amazon (`amazon-s3x-sync`)          |
| `key`                          | Chemin du fichier dans le bucket S3                  |
|--------------------------------|------------------------------------------------------|

### Autres champs

| Champ                          | Description                                          |
|--------------------------------|------------------------------------------------------|
| `fields.ORIGIN_FILE_NAME`      | Doublon du nom de fichier (champ indexé)             |
| `highlight.content[]`          | Extraits de texte avec les mots-clés dans `<em>`     |
|--------------------------------|------------------------------------------------------|

---

## Comportement

- Pagination via `page`
- Recherche plein texte
- Retour JSON (type Elasticsearch / OpenSearch)
- Les grands fichiers sont découpés en **chunks** pour l'indexation — chaque chunk est un résultat séparé
- Les mots-clés sont surligués dans `highlight.content` via des balises `<em>...</em>`
- `max_score` permet de trier par pertinence
- URLs directes téléchargeables
- Aucun header ni token requis

---

## Liens utiles

**Page suivante :**

```
https://www.justice.gov/multimedia-search?keys={query}&page=2
```

**Téléchargement direct d'un fichier :**

```
https://www.justice.gov/epstein/files/DataSet 11/EFTA02344444.pdf
```
