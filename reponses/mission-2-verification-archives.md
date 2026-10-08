# Mission M2 — Vérification avec archives

## 1. `bibliotheque_list_loans` avec `include_archived = true`, sans filtre de statut

137 emprunts récupérés au total sur 4 appel(s).

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"limit": 50, "include_archived": true}` | 50 | `NTA=` |
| 2 | `{"limit": 50, "include_archived": true, "start_key": "NTA="}` | 50 | `MTAw` |
| 3 | `{"limit": 50, "include_archived": true, "start_key": "MTAw"}` | 37 | `MTUw` |
| 4 | `{"limit": 50, "include_archived": true, "start_key": "MTUw"}` | 0 | `MjAw` |

Réponses brutes des pages : `reponses/m2-emprunts-avec-archives.json` (objet `{ "ok": true, "items": [ … 137 emprunts … ] }`).

## 2. Comptages

- **Nombre total d'emprunts : 137**.

- **Nombre d'emprunts archivés (`archived = true`) : 0**.

## 3. Détail des emprunts archivés

Aucun emprunt archivé.

## 4. Emprunt `returned_at = null` au `due_at` le plus ancien (archives comprises)

- **LN-5106** — adhérent `MB-225`, ouvrage `BK-1075`, `status = open`, `due_at = 2026-04-10 09:00` (1775811600), `returned_at = null`, `archived = False`.

Réponse brute :

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

Emprunts avec `returned_at = null` (archives comprises) : 53.
