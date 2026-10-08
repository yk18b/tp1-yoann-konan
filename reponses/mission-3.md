# Mission M3 — La réinscription

Énoncé : Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche.

## Schéma de l'outil `create_loan`

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

## Appel 1 — `bibliotheque_create_loan` (tentative sans `desk_code`) — ÉCHEC

Paramètres :

```json
{"member_id": "MB-214", "book_id": "BK-1042"}
```

Réponse brute du serveur :

```json
{
  "ok": false,
  "error": "missing field"
}
```

Le schéma publié ne liste que `member_id` et `book_id`, mais l'implémentation exige un champ supplémentaire : `desk_code` (présent sur chaque emprunt du registre).

## Appel 2 — `bibliotheque_create_loan` (avec `desk_code`) — SUCCÈS

Paramètres :

```json
{"member_id": "MB-214", "book_id": "BK-1042", "desk_code": "A1"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "loan": {
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
}
```

## État de la fiche MB-214 AVANT (capturé dans `etat-initial-m3-m4.md`)

```json
{"ok": true, "items": [{"loan_id": "LN-5036", "book_id": "BK-1126", "member_id": "MB-214", "started_at": 1779872400, "due_at": 1781686800, "returned_at": null, "status": "open", "archived": false, "desk_code": "A1"}, {"loan_id": "LN-5132", "book_id": "BK-1161", "member_id": "MB-214", "started_at": 1776934800, "due_at": 1779354000, "returned_at": 1778662800, "status": "returned", "archived": false, "desk_code": "A1"}], "next": "NTA="}
```

## Appel 3 — `bibliotheque_list_loans` (member_id = MB-214, APRÈS, page 1)

Paramètres :

```json
{"member_id": "MB-214", "limit": 50}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [
    {
      "loan_id": "LN-5036",
      "book_id": "BK-1126",
      "member_id": "MB-214",
      "started_at": 1779872400,
      "due_at": 1781686800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5132",
      "book_id": "BK-1161",
      "member_id": "MB-214",
      "started_at": 1776934800,
      "due_at": 1779354000,
      "returned_at": 1778662800,
      "status": "returned",
      "archived": false,
      "desk_code": "A1"
    },
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
  ],
  "next": "NTA="
}
```

## Appel 4 — `bibliotheque_list_loans` (member_id = MB-214, APRÈS, page 2)

Paramètres :

```json
{"member_id": "MB-214", "limit": 50, "start_key": "NTA="}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [],
  "next": "MTAw"
}
```

## Conclusion finale

- L'emprunt a été créé : `{"loan_id": "LN-5137", "book_id": "BK-1042", "member_id": "MB-214", "started_at": 1791277200, "due_at": 1793091600, "returned_at": null, "status": "open", "archived": false, "desk_code": "A1"}`.

- Il apparaît bien dans la fiche de MB-214 (qui contient désormais 3 emprunts) :


| `loan_id` | `book_id` | `status` | `due_at` | `returned_at` | `desk_code` |
|---|---|---|---|---|---|
| LN-5036 | BK-1126 | open | 2026-06-17 09:00 | null | A1 |
| LN-5132 | BK-1161 | returned | 2026-05-21 09:00 | 2026-05-13 09:00 | A1 |
| LN-5137 | BK-1042 | open | 2026-10-27 09:00 | null | A1 |

**Vérification réussie : le nouvel emprunt `LN-5137` (MB-214, BK-1042) apparaît dans la fiche.**

---

## Vérification par un autre chemin (relecture de la fiche MB-214)

**Chemin alternatif** : rappel de `bibliotheque_list_loans` avec `member_id = MB-214`, sans filtre de statut, toutes les pages, puis comparaison avec l'état initial enregistré dans `reponses/etat-initial-m3-m4.md`.

### Appel V1 — `bibliotheque_list_loans` (page 1)

Paramètres :

```json
{"member_id": "MB-214", "limit": 50}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [
    {
      "loan_id": "LN-5036",
      "book_id": "BK-1126",
      "member_id": "MB-214",
      "started_at": 1779872400,
      "due_at": 1781686800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5132",
      "book_id": "BK-1161",
      "member_id": "MB-214",
      "started_at": 1776934800,
      "due_at": 1779354000,
      "returned_at": 1778662800,
      "status": "returned",
      "archived": false,
      "desk_code": "A1"
    },
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
  ],
  "next": "NTA="
}
```

### Appel V2 — `bibliotheque_list_loans` (page 2)

Paramètres :

```json
{"member_id": "MB-214", "limit": 50, "start_key": "NTA="}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [],
  "next": "MTAw"
}
```

### État initial (extrait de `etat-initial-m3-m4.md`)

| `loan_id` | `book_id` | `status` | `due_at` | `returned_at` | `desk_code` |
|---|---|---|---|---|---|
| LN-5036 | BK-1126 | open | 2026-06-17 09:00 | null | A1 |
| LN-5132 | BK-1161 | returned | 2026-05-21 09:00 | 2026-05-13 09:00 | A1 |

### État actuel

| `loan_id` | `book_id` | `status` | `due_at` | `returned_at` | `desk_code` |
|---|---|---|---|---|---|
| LN-5036 | BK-1126 | open | 2026-06-17 09:00 | null | A1 |
| LN-5132 | BK-1161 | returned | 2026-05-21 09:00 | 2026-05-13 09:00 | A1 |
| LN-5137 | BK-1042 | open | 2026-10-27 09:00 | null | A1 |

### Différences

- **Emprunts ajoutés (1) :**
  - **LN-5137** — `book_id = BK-1042`, `status = open`, `due_at = 2026-10-27 09:00`, `returned_at = null`, `desk_code = A1`.
- Aucun emprunt supprimé.
- Aucun emprunt existant modifié.

**Conclusion de la vérification** : par rapport à l'état initial (2 emprunts), la fiche en contient désormais 3. Le seul changement est l'ajout de **LN-5137** (MB-214 / BK-1042), les autres emprunts étant inchangés. La mission M3 est donc confirmée par ce second chemin.
