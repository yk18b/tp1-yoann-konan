# Mission M2 — Vérification

## 1. `bibliotheque_list_loans` sans filtre de statut (toutes les pages jusqu'à une page vide)

137 emprunts récupérés au total sur 4 appel(s).

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"limit": 50}` | 50 | `NTA=` |
| 2 | `{"limit": 50, "start_key": "NTA="}` | 50 | `MTAw` |
| 3 | `{"limit": 50, "start_key": "MTAw"}` | 37 | `MTUw` |
| 4 | `{"limit": 50, "start_key": "MTUw"}` | 0 | `MjAw` |

Réponses brutes des pages : voir `reponses/m2-tous-les-emprunts.json` (objet `{ "ok": true, "items": [ … 137 emprunts … ] }`, toutes pages assemblées).

## 2. Valeurs distinctes de `status` et effectifs

| `status` | Nombre d'emprunts |
|---|---|
| returned | 84 |
| open | 53 |
| **Total** | **137** |

Valeurs distinctes : 'returned', 'open'.

## 3. Comptages `returned_at = null` et `archived = true`

- Emprunts avec `returned_at` à `null` (quel que soit le `status`) : **53**.

- Emprunts avec `archived` à `true` : **0** (la requête sans `include_archived` exclut les emprunts archivés par défaut).

## 4. Emprunt `returned_at = null` au `due_at` le plus ancien

- **LN-5106** — adhérent `MB-225`, ouvrage `BK-1075`, `status = open`, `due_at = 2026-04-10 09:00` (1775811600), `started_at = 2026-03-27 09:00`, `archived = False`.

Réponse brute de l'emprunt (extraite du jeu complet) :

```json
{
  "loan_id": "LN-5106",
  "book_id": "BK-1075",
  "member_id": "MB-225",
  "started_at": 1774602000,
  "due_at": 1775811600,
  "returned_at": null,
  "status": "open",
  "archived": false,
  "desk_code": "B2"
}
```

## 5. Descriptions et schémas d'outils (mot pour mot)

### `bibliotheque_list_loans`

Description complète :

> Lists the loans in registration order (by loan_id).

Schéma des paramètres (`inputSchema`) :

```json
{
  "type": "object",
  "properties": {
    "limit": {
      "type": "integer",
      "description": "Number of rows to return. Defaults to 20."
    },
    "start_key": {
      "type": "string",
      "description": "Opaque key returned as `next` by a previous call."
    },
    "member_id": {
      "type": "string",
      "description": "Restrict to one member."
    },
    "status": {
      "type": "string",
      "description": "Either \"open\" or \"returned\"."
    },
    "include_archived": {
      "type": "boolean",
      "description": "Include archived loans."
    }
  }
}
```

### `bibliotheque_get_member_fees`

Description complète :

> Returns what a member currently owes.

Schéma des paramètres (`inputSchema`) :

```json
{
  "type": "object",
  "properties": {
    "member_id": {
      "type": "string",
      "description": "Identifier, e.g. MB-214."
    }
  },
  "required": [
    "member_id"
  ]
}
```
