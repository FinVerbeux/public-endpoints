
## Endpoint principal

[https://ws-public.interpol.int/notices/v1/red](https://ws-public.interpol.int/notices/v1/red)

## Paramètres
```
|--------------|------|----------------------|
| Nom          | Type | Description          |
|--------------|------|----------------------|
| page         | int  | Numéro de page       |
| resultPerPage| int  | Résultats par page   |
|--------------|------|----------------------|
```
## Exemple

[https://ws-public.interpol.int/notices/v1/red?page=2&resultPerPage=20](https://ws-public.interpol.int/notices/v1/red?page=2&resultPerPage=20)

## Exemple de réponse (simplifié)

```json
{
  "total": 6469,
  "query": {
    "page": 2,
    "resultPerPage": 20
  },
  "_embedded": {
    "notices": [
      {
        "entity_id": "2025/95737",
        "forename": "ISHMEL SHAVAUGHNE",
        "name": "SWEENEY",
        "nationalities": ["US"]
      }
    ]
  }
}
```

## Liens utiles

* **Détails notice** :

```
https://ws-public.interpol.int/notices/v1/red/{id}
```

* **Images** :

```
https://ws-public.interpol.int/notices/v1/red/{id}/images
```
