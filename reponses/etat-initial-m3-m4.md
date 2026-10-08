# État initial — M3 / M4

Lecture seule : aucune modification n'a été effectuée.

## 1. `bibliotheque_list_loans` — `member_id = MB-214`

2 emprunt(s). Appels :

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"member_id": "MB-214", "limit": 50}` | 2 | `NTA=` |
| 2 | `{"member_id": "MB-214", "limit": 50, "start_key": "NTA="}` | 0 | `MTAw` |

Tableau lisible :

| `loan_id` | `book_id` | `status` | `due_at` | `returned_at` |
|---|---|---|---|---|
| LN-5036 | BK-1126 | open | 2026-06-17 09:00 | null |
| LN-5132 | BK-1161 | returned | 2026-05-21 09:00 | 2026-05-13 09:00 |

Réponses brutes :

### MB-214 — page 1

```json
{"ok": true, "items": [{"loan_id": "LN-5036", "book_id": "BK-1126", "member_id": "MB-214", "started_at": 1779872400, "due_at": 1781686800, "returned_at": null, "status": "open", "archived": false, "desk_code": "A1"}, {"loan_id": "LN-5132", "book_id": "BK-1161", "member_id": "MB-214", "started_at": 1776934800, "due_at": 1779354000, "returned_at": 1778662800, "status": "returned", "archived": false, "desk_code": "A1"}], "next": "NTA="}
```

### MB-214 — page 2

```json
{"ok": true, "items": [], "next": "MTAw"}
```

## 2. `bibliotheque_list_loans` — `member_id = MB-202`

7 emprunt(s). Appels :

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"member_id": "MB-202", "limit": 50}` | 7 | `NTA=` |
| 2 | `{"member_id": "MB-202", "limit": 50, "start_key": "NTA="}` | 0 | `MTAw` |

Tableau lisible :

| `loan_id` | `book_id` | `status` | `due_at` | `returned_at` |
|---|---|---|---|---|
| LN-5038 | BK-1110 | returned | 2026-08-13 09:00 | 2026-08-13 09:00 |
| LN-5039 | BK-1159 | returned | 2026-10-05 09:00 | 2026-10-01 09:00 |
| LN-5060 | BK-1115 | open | 2026-06-13 09:00 | null |
| LN-5062 | BK-1025 | returned | 2026-05-17 09:00 | 2026-05-10 09:00 |
| LN-5095 | BK-1060 | returned | 2026-05-11 09:00 | 2026-05-04 09:00 |
| LN-5120 | BK-1040 | returned | 2026-09-29 09:00 | 2026-09-29 09:00 |
| LN-5134 | BK-1050 | returned | 2026-07-05 09:00 | 2026-07-02 09:00 |

Réponses brutes :

### MB-202 — page 1

```json
{"ok": true, "items": [{"loan_id": "LN-5038", "book_id": "BK-1110", "member_id": "MB-202", "started_at": 1785402000, "due_at": 1786611600, "returned_at": 1786611600, "status": "returned", "archived": false, "desk_code": "C3"}, {"loan_id": "LN-5039", "book_id": "BK-1159", "member_id": "MB-202", "started_at": 1788771600, "due_at": 1791190800, "returned_at": 1790845200, "status": "returned", "archived": false, "desk_code": "A1"}, {"loan_id": "LN-5060", "book_id": "BK-1115", "member_id": "MB-202", "started_at": 1778922000, "due_at": 1781341200, "returned_at": null, "status": "open", "archived": false, "desk_code": "A1"}, {"loan_id": "LN-5062", "book_id": "BK-1025", "member_id": "MB-202", "started_at": 1776589200, "due_at": 1779008400, "returned_at": 1778403600, "status": "returned", "archived": false, "desk_code": "C3"}, {"loan_id": "LN-5095", "book_id": "BK-1060", "member_id": "MB-202", "started_at": 1776070800, "due_at": 1778490000, "returned_at": 1777885200, "status": "returned", "archived": false, "desk_code": "C3"}, {"loan_id": "LN-5120", "book_id": "BK-1040", "member_id": "MB-202", "started_at": 1788858000, "due_at": 1790672400, "returned_at": 1790672400, "status": "returned", "archived": false, "desk_code": "A1"}, {"loan_id": "LN-5134", "book_id": "BK-1050", "member_id": "MB-202", "started_at": 1781427600, "due_at": 1783242000, "returned_at": 1782982800, "status": "returned", "archived": false, "desk_code": "C3"}], "next": "NTA="}
```

### MB-202 — page 2

```json
{"ok": true, "items": [], "next": "MTAw"}
```

## 3. `bibliotheque_get_book` — `book_id = BK-1042`

```json
{
  "ok": true,
  "book": {
    "book_id": "BK-1042",
    "title": "Le Dernier de verre",
    "author": "Karim Barbier",
    "genre": "policier",
    "copies": 4,
    "loan_duration": 21,
    "added_at": "2023-11-09T09:00:00.000Z",
    "archived": false
  }
}
```
