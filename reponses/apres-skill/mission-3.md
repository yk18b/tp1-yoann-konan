# Mission M3 — La réinscription

**Demande (brief M3)** : « Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche. »

Skill chargé : `bibliotheque-api` (piège 2 : `desk_code` obligatoire non documenté ; méthode §3 : vérifier chaque écriture par une relecture avec `include_archived: true` ; `get_member` ne liste pas les emprunts).

---

## 1. Appels passés

| # | Outil | Paramètres |
|---|-------|------------|
| 1 | `bibliotheque_get_mission` | `{ "mission_id": "M3" }` |
| 2 | `bibliotheque_get_member` | `{ "memberId": "MB-214" }` |
| 3 | `bibliotheque_get_book` | `{ "book_id": "BK-1042" }` |
| 4 | `bibliotheque_list_loans` | `{ "member_id": "MB-214", "include_archived": true, "limit": 100 }` (état initial) |
| 5 | `bibliotheque_list_loans` | `{ "member_id": "MB-214", "include_archived": true, "limit": 100, "start_key": "NTA=" }` (fin de liste) |
| 6 | `bibliotheque_create_loan` | `{ "member_id": "MB-214", "book_id": "BK-1042" }` (conforme au schéma → échec) |
| 7 | `bibliotheque_create_loan` | `{ "member_id": "MB-214", "book_id": "BK-1042", "desk_code": "A1" }` (→ succès) |
| 8 | `bibliotheque_list_loans` | `{ "member_id": "MB-214", "include_archived": true, "limit": 100 }` (vérification) |
| 9 | `bibliotheque_get_member` | `{ "memberId": "MB-214" }` |

---

## 2. Réponses brutes

### 2.1 `get_mission`

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M3",
    "title": "La réinscription",
    "brief": "Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche."
  }
}
```

### 2.2 `get_member` (avant)

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

### 2.3 `get_book` (BK-1042)

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

### 2.4 `list_loans` — état initial de MB-214 (archivés inclus)

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5036","book_id":"BK-1126","member_id":"MB-214","started_at":1779872400,"due_at":1781686800,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5132","book_id":"BK-1161","member_id":"MB-214","started_at":1776934800,"due_at":1779354000,"returned_at":1778662800,"status":"returned","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5137","book_id":"BK-1042","member_id":"MB-214","started_at":1791277200,"due_at":1793091600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
  ],
  "next": "NTA="
}
```

Page suivante (`start_key: "NTA="`) :

```json
{ "ok": true, "items": [], "next": "MTAw" }
```

→ MB-214 avait **3 emprunts** avant l'opération, dont **2 ouverts** : `LN-5036` (BK-1126) et `LN-5137` (BK-1042).

### 2.5 `create_loan` — tentative conforme au schéma (échec, piège 2)

```json
{ "ok": false, "error": "missing field" }
```

L'erreur ne nomme pas le champ manquant. Le champ réellement exigé est `desk_code`, absent du schéma de l'outil.

### 2.6 `create_loan` — avec `desk_code` (succès)

```json
{
  "ok": true,
  "loan": {
    "loan_id": "LN-5138",
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

### 2.7 `list_loans` — vérification après écriture (archivés inclus)

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5036","book_id":"BK-1126","member_id":"MB-214","started_at":1779872400,"due_at":1781686800,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5132","book_id":"BK-1161","member_id":"MB-214","started_at":1776934800,"due_at":1779354000,"returned_at":1778662800,"status":"returned","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5137","book_id":"BK-1042","member_id":"MB-214","started_at":1791277200,"due_at":1793091600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5138","book_id":"BK-1042","member_id":"MB-214","started_at":1791277200,"due_at":1793091600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
  ],
  "next": "NTA="
}
```

Le nouvel emprunt `LN-5138` est bien présent.

### 2.8 `get_member` (après) — « la fiche »

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

Comme documenté dans le skill, **`get_member` ne contient pas les emprunts** : la fiche ne permet donc pas, à elle seule, de vérifier la présence de `LN-5138`. C'est `list_loans` filtré sur `member_id` qui fait foi (piège « Vérifier qu'un emprunt apparaît dans la fiche »).

---

## 3. Conclusion

**L'emprunt a été créé et vérifié.**

| Élément | Valeur |
|---|---|
| Adhérent | MB-214 — Chloé Roux (active) |
| Ouvrage | BK-1042 — « Le Dernier de verre » (Karim Barbier, policier) |
| Nouvel emprunt | **`LN-5138`** |
| Statut | `open` |
| `started_at` | 1791277200 → **2026-10-06 09:00 UTC** (date de référence du serveur) |
| `due_at` | 1793091600 → **2026-10-27 09:00 UTC** (durée 21 j, cohérente avec `loan_duration` = 21) |
| `desk_code` | A1 |

**Vérification** : `list_loans(member_id = "MB-214", include_archived: true)` renvoie désormais **4 emprunts**, dont le nouveau `LN-5138`. La fiche (`get_member`) reste identique et ne liste pas les emprunts — c'est une limite de l'outil, pas un échec.

**Observations à signaler :**
1. **`desk_code` obligatoire mais non documenté** (piège 2) : un appel conforme au schéma échoue avec un simple `"missing field"` ; il faut fournir `desk_code`, ici `A1`.
2. **Doublon** : MB-214 avait **déjà** un emprunt ouvert sur le même ouvrage BK-1042 (`LN-5137`, créé au même instant `started_at = 1791277200`, même échéance). Après l'opération, il existe **deux emprunts ouverts identiques** (`LN-5137` et `LN-5138`) sur BK-1042. Le brief M3 s'intitule d'ailleurs « La réinscription », ce qui peut l'expliquer, mais l'API n'a pas empêché le doublon.
3. Écriture confirmée par une relecture avec `include_archived: true` (méthode §3 du skill), et non par le seul retour `{"ok": true}`.
