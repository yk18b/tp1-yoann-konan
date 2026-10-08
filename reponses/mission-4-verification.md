# Mission M4 — Vérification

## 1. `bibliotheque_list_loans` — MB-202 avec `include_archived = true`, sans filtre de statut

7 emprunt(s) récupéré(s) sur 2 appel(s).

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"member_id": "MB-202", "include_archived": true, "limit": 50}` | 7 | `NTA=` |
| 2 | `{"member_id": "MB-202", "include_archived": true, "limit": 50, "start_key": "NTA="}` | 0 | `MTAw` |

| `loan_id` | `book_id` | `status` | `returned_at` | `archived` |
|---|---|---|---|---|
| LN-5038 | BK-1110 | returned | 2026-08-13 09:00 | True |
| LN-5039 | BK-1159 | returned | 2026-10-01 09:00 | True |
| LN-5060 | BK-1115 | open | null | False |
| LN-5062 | BK-1025 | returned | 2026-05-10 09:00 | True |
| LN-5095 | BK-1060 | returned | 2026-05-04 09:00 | True |
| LN-5120 | BK-1040 | returned | 2026-09-29 09:00 | True |
| LN-5134 | BK-1050 | returned | 2026-07-02 09:00 | True |

Réponses brutes :

### MB-202 (include_archived) — page 1

```json
{
  "ok": true,
  "items": [
    {
      "loan_id": "LN-5038",
      "book_id": "BK-1110",
      "member_id": "MB-202",
      "started_at": 1785402000,
      "due_at": 1786611600,
      "returned_at": 1786611600,
      "status": "returned",
      "archived": true,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5039",
      "book_id": "BK-1159",
      "member_id": "MB-202",
      "started_at": 1788771600,
      "due_at": 1791190800,
      "returned_at": 1790845200,
      "status": "returned",
      "archived": true,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5060",
      "book_id": "BK-1115",
      "member_id": "MB-202",
      "started_at": 1778922000,
      "due_at": 1781341200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5062",
      "book_id": "BK-1025",
      "member_id": "MB-202",
      "started_at": 1776589200,
      "due_at": 1779008400,
      "returned_at": 1778403600,
      "status": "returned",
      "archived": true,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5095",
      "book_id": "BK-1060",
      "member_id": "MB-202",
      "started_at": 1776070800,
      "due_at": 1778490000,
      "returned_at": 1777885200,
      "status": "returned",
      "archived": true,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5120",
      "book_id": "BK-1040",
      "member_id": "MB-202",
      "started_at": 1788858000,
      "due_at": 1790672400,
      "returned_at": 1790672400,
      "status": "returned",
      "archived": true,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5134",
      "book_id": "BK-1050",
      "member_id": "MB-202",
      "started_at": 1781427600,
      "due_at": 1783242000,
      "returned_at": 1782982800,
      "status": "returned",
      "archived": true,
      "desk_code": "C3"
    }
  ],
  "next": "NTA="
}
```

### MB-202 (include_archived) — page 2

```json
{
  "ok": true,
  "items": [],
  "next": "MTAw"
}
```

## 2. `bibliotheque_list_loans` sans aucun filtre

- **Sans `include_archived` (défaut) : 132 emprunts.**

- Avec `include_archived = true` : 138 emprunts, dont 6 archivés.


| Page (défaut) | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"limit": 50}` | 50 | `NTA=` |
| 2 | `{"limit": 50, "start_key": "NTA="}` | 50 | `MTAw` |
| 3 | `{"limit": 50, "start_key": "MTAw"}` | 32 | `MTUw` |
| 4 | `{"limit": 50, "start_key": "MTUw"}` | 0 | `MjAw` |

Autrement dit : 138 − 6 = 132 en vue par défaut, les 6 emprunts « supprimés » de M4 étant archivés.

## 3. `bibliotheque_delete_loan` — description et schéma (mot pour mot)

Description complète :

> Deletes a loan from the register.

Schéma des paramètres (`inputSchema`) :

```json
{
  "type": "object",
  "properties": {
    "loan_id": {
      "type": "string",
      "description": "Identifier, e.g. LN-5003."
    }
  },
  "required": [
    "loan_id"
  ]
}
```

## 4. Comparaison avec l'état initial (`etat-initial-m3-m4.md`)

| `loan_id` | `status` initial | `returned_at` initial | Archivé après M4 ? | Dans la vue par défaut ? |
|---|---|---|---|---|
| LN-5038 | returned | 1786611600 | True | non |
| LN-5039 | returned | 1790845200 | True | non |
| LN-5060 | open | null | False | oui |
| LN-5062 | returned | 1778403600 | True | non |
| LN-5095 | returned | 1777885200 | True | non |
| LN-5120 | returned | 1790672400 | True | non |
| LN-5134 | returned | 1782982800 | True | non |

- Emprunts rendus à l'état initial : 6 — LN-5038, LN-5039, LN-5062, LN-5095, LN-5120, LN-5134.

- Ces 6 emprunts ont `archived = true` après M4 mais sont absents de la vue par défaut.

- L'emprunt en cours `LN-5060` (`returned_at = null`) reste visible, `archived = false`.


**Conclusion de la vérification** : la fiche MB-202 en vue par défaut ne contient plus que `LN-5060` : les 6 emprunts rendus ont bien disparu de la fiche. Toutefois, `delete_loan` réalise une **suppression logique (archivage)** : avec `include_archived = true`, les 6 emprunts réapparaissent avec `archived = true` (total global 132 en vue par défaut, 138 archives comprises). La mission M4 est donc confirmée du point de vue de l'adhérent, tout en révélant que les emprunts sont archivés plutôt qu'effacés définitivement.
