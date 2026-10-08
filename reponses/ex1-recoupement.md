# Ex1 — Recoupement

## 1. `bibliotheque_count_books` — réponse brute

```json
{
  "ok": true,
  "count": 184
}
```

## 2. `bibliotheque_list_books` avec `include_archived = true` (toutes les pages)

Pagination suivie via le curseur `next` (4 pages, 50 par appel) :

| Page | Items reçus | Cumul | `next` |
|---|---|---|---|
| 1 | 50 | 50 | `NTA=` |
| 2 | 50 | 100 | `MTAw` |
| 3 | 50 | 150 | `MTUw` |
| 4 | 34 | 184 | `MjAw` |

## 3. Résultats

- **Nombre total de livres obtenus : 184**
- **Nombre de livres avec `archived = true` : 26**
- Identifiants uniques : 184 (aucun doublon)

## 4. Cohérence

`count_books` annonce `count = 184`, ce qui correspond exactement au nombre de livres obtenus avec `include_archived = true` (184). Les 26 livres archivés sont donc bien inclus dans ce total.
