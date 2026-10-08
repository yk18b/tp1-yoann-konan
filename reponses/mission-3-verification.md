# Mission M3 — Vérification

## 1. Descriptions et schémas (mot pour mot)

### `bibliotheque_create_loan`

Description complète :

> Registers a new loan for a member.

Schéma des paramètres (`inputSchema`) :

```json
{
  "type": "object",
  "properties": {
    "member_id": {
      "type": "string",
      "description": "Borrower identifier."
    },
    "book_id": {
      "type": "string",
      "description": "Borrowed book identifier."
    }
  },
  "required": [
    "member_id",
    "book_id"
  ]
}
```

### `bibliotheque_get_member`

Description complète :

> Returns one member record.

Schéma des paramètres (`inputSchema`) :

```json
{
  "type": "object",
  "properties": {
    "memberId": {
      "type": "string",
      "description": "Identifier, e.g. MB-214."
    }
  },
  "required": [
    "memberId"
  ]
}
```

## 2. `bibliotheque_get_member` pour MB-214 (deux noms de paramètre)

### Appel 2a — paramètre `member_id`

Paramètres :

```json
{"member_id": "MB-214"}
```

Réponse brute :

```json
{
  "ok": false,
  "error": "invalid request"
}
```

### Appel 2b — paramètre `memberId`

Paramètres :

```json
{"memberId": "MB-214"}
```

Réponse brute :

```json
{
  "ok": true,
  "member": {
    "member_id": "MB-214",
    "first_name": "Chloé",
    "last_name": "Roux",
    "joined_at": "09/07/2023",
    "active": true,
    "email": "chloe.roux@example.org"
  }
}
```

Le schéma officiel de `get_member` attend **`memberId`** (camelCase) : l'appel 2b est donc le seul correct, tandis que l'appel 2a renvoie une erreur de champ manquant.

## 3. `bibliotheque_list_loans` sans filtre de membre ni de statut (toutes les pages)

138 emprunts au total sur 4 appel(s).

| Page | Paramètres | Items | `next` |
|---|---|---|---|
| 1 | `{"limit": 50}` | 50 | `NTA=` |
| 2 | `{"limit": 50, "start_key": "NTA="}` | 50 | `MTAw` |
| 3 | `{"limit": 50, "start_key": "MTAw"}` | 38 | `MTUw` |
| 4 | `{"limit": 50, "start_key": "MTUw"}` | 0 | `MjAw` |

- **LN-5137 apparaît bien** dans la liste globale :

```json
{
  "loan_id": "LN-5137",
  "book_id": "BK-1042",
  "member_id": "MB-214",
  "started_at": 1791277200,
  "due_at": 1793091600,
  "returned_at": null,
  "status": "open",
  "archived": false,
  "desk_code": "A1"
}
```

- **Nombre total d'emprunts : 138** (contre 137 avant la création → +1).

## 4. `bibliotheque_get_member_fees` pour MB-214

Paramètres :

```json
{"member_id": "MB-214"}
```

Réponse brute :

```json
{
  "ok": true,
  "member_id": "MB-214",
  "open_loans": 2,
  "overdue_duration": 2664,
  "late_fee_per_day": 15,
  "balance_due": 16.65
}
```

## Conclusion

- `get_member` exige `memberId` (camelCase) ; `member_id` échoue.

- `LN-5137` est présent dans le registre global ; total = 138 emprunts (+1 vs 137).

- MB-214 présente des frais de retard (voir réponse 4).
