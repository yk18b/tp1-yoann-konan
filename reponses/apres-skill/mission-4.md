# Mission M4 — Le ménage

**Demande (brief M4)** : « L'adhérent MB-202 demande l'effacement de ses emprunts déjà rendus. Supprime-les, puis prouve qu'ils ont bien disparu. »

Skill chargé : `bibliotheque-api` (piège 1 : `delete_loan` = **suppression logique** ; méthode §3 : vérifier toute écriture par une relecture avec `include_archived: true` ; piège 6 : pagination).

---

## 1. Appels passés

| # | Outil | Paramètres |
|---|-------|------------|
| 1 | `bibliotheque_get_mission` | `{ "mission_id": "M4" }` |
| 2 | `bibliotheque_list_loans` | `{ "member_id": "MB-202", "include_archived": true, "limit": 100 }` (état initial, archivés inclus) |
| 3 | `bibliotheque_list_loans` | `{ "member_id": "MB-202", "include_archived": true, "limit": 100, "start_key": "NTA=" }` (fin de liste) |
| 4 | `bibliotheque_list_loans` | `{ "member_id": "MB-202", "limit": 100 }` (vue par défaut, avant) |
| 5-10 | `bibliotheque_delete_loan` | `{ "loan_id": "LN-5038" }`, `LN-5039`, `LN-5062`, `LN-5095`, `LN-5120`, `LN-5134` |
| 11 | `bibliotheque_list_loans` | `{ "member_id": "MB-202", "limit": 100 }` (vue par défaut, après) |
| 12 | `bibliotheque_list_loans` | `{ "member_id": "MB-202", "include_archived": true, "limit": 100 }` (vue complète, après) |

---

## 2. Réponses brutes

### 2.1 `get_mission`

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M4",
    "title": "Le ménage",
    "brief": "L'adhérent MB-202 demande l'effacement de ses emprunts déjà rendus. Supprime-les, puis prouve qu’ils ont bien disparu."
  }
}
```

### 2.2 État initial de MB-202 — `list_loans` archivés inclus

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5038","book_id":"BK-1110","member_id":"MB-202","started_at":1785402000,"due_at":1786611600,"returned_at":1786611600,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5039","book_id":"BK-1159","member_id":"MB-202","started_at":1788771600,"due_at":1791190800,"returned_at":1790845200,"status":"returned","archived":true,"desk_code":"A1"},
    {"loan_id":"LN-5060","book_id":"BK-1115","member_id":"MB-202","started_at":1778922000,"due_at":1781341200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5062","book_id":"BK-1025","member_id":"MB-202","started_at":1776589200,"due_at":1779008400,"returned_at":1778403600,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5095","book_id":"BK-1060","member_id":"MB-202","started_at":1776070800,"due_at":1778490000,"returned_at":1777885200,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5120","book_id":"BK-1040","member_id":"MB-202","started_at":1788858000,"due_at":1790672400,"returned_at":1790672400,"status":"returned","archived":true,"desk_code":"A1"},
    {"loan_id":"LN-5134","book_id":"BK-1050","member_id":"MB-202","started_at":1781427600,"due_at":1783242000,"returned_at":1782982800,"status":"returned","archived":true,"desk_code":"C3"}
  ],
  "next": "NTA="
}
```

Page suivante (`start_key: "NTA="`) → `{ "ok": true, "items": [], "next": "MTAw" }` : la liste s'arrête à **7 emprunts**.

**Constat important dès l'état initial** : les **6 emprunts rendus** de MB-202 (`LN-5038`, `LN-5039`, `LN-5062`, `LN-5095`, `LN-5120`, `LN-5134`) sont **déjà `archived: true`** — ils avaient donc déjà été « supprimés » (logiquement) avant cette mission. Seul `LN-5060` (ouvert, non rendu) est actif.

### 2.3 Vue par défaut de MB-202 (avant opération)

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5060","book_id":"BK-1115","member_id":"MB-202","started_at":1778922000,"due_at":1781341200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
  ],
  "next": "NTA="
}
```

La vue par défaut ne montrait déjà qu'`LN-5060` : les 6 rendus étaient déjà masqués.

### 2.4 Suppressions — `delete_loan`

```json
{ "ok": true, "deleted": true, "loan_id": "LN-5038" }
{ "ok": true, "deleted": true, "loan_id": "LN-5039" }
{ "ok": true, "deleted": true, "loan_id": "LN-5062" }
{ "ok": true, "deleted": true, "loan_id": "LN-5095" }
{ "ok": true, "deleted": true, "loan_id": "LN-5120" }
{ "ok": true, "deleted": true, "loan_id": "LN-5134" }
```

### 2.5 Vue par défaut de MB-202 (après opération)

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5060","book_id":"BK-1115","member_id":"MB-202","started_at":1778922000,"due_at":1781341200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
  ],
  "next": "NTA="
}
```

### 2.6 Vue complète de MB-202, archivés inclus (après opération)

```json
{
  "ok": true,
  "items": [
    {"loan_id":"LN-5038","book_id":"BK-1110","member_id":"MB-202","started_at":1785402000,"due_at":1786611600,"returned_at":1786611600,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5039","book_id":"BK-1159","member_id":"MB-202","started_at":1788771600,"due_at":1791190800,"returned_at":1790845200,"status":"returned","archived":true,"desk_code":"A1"},
    {"loan_id":"LN-5060","book_id":"BK-1115","member_id":"MB-202","started_at":1778922000,"due_at":1781341200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
    {"loan_id":"LN-5062","book_id":"BK-1025","member_id":"MB-202","started_at":1776589200,"due_at":1779008400,"returned_at":1778403600,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5095","book_id":"BK-1060","member_id":"MB-202","started_at":1776070800,"due_at":1778490000,"returned_at":1777885200,"status":"returned","archived":true,"desk_code":"C3"},
    {"loan_id":"LN-5120","book_id":"BK-1040","member_id":"MB-202","started_at":1788858000,"due_at":1790672400,"returned_at":1790672400,"status":"returned","archived":true,"desk_code":"A1"},
    {"loan_id":"LN-5134","book_id":"BK-1050","member_id":"MB-202","started_at":1781427600,"due_at":1783242000,"returned_at":1782982800,"status":"returned","archived":true,"desk_code":"C3"}
  ],
  "next": "NTA="
}
```

---

## 3. Analyse

| Vue | Avant | Après |
|---|---|---|
| `list_loans` par défaut (MB-202) | 1 emprunt (LN-5060) | 1 emprunt (LN-5060) |
| `list_loans` archivés inclus (MB-202) | 7 emprunts | 7 emprunts |
| Emprunts rendus visibles en 2.6 | — | 6, tous `archived: true` |

1. **Les 6 rendus étaient déjà archivés** avant l'opération (constat 2.2). Les 6 `delete_loan` ont donc renvoyé `{"deleted": true}` sans modifier l'état observable : ils étaient déjà invisibles dans la vue par défaut.
2. **La disparition apparente est prouvée** : dans la vue par défaut (2.5), seuls subsistent les emprunts non archivés, soit `LN-5060`. Les 6 rendus n'y figurent plus.
3. **Mais ils n'ont pas été effacés** : avec `include_archived: true` (2.6), les 6 rendus réapparaissent intacts, avec `archived: true`. C'est une **suppression logique**, pas un effacement définitif.
4. `{"ok": true, "deleted": true}` n'est **pas** une preuve d'effacement (piège 1) : c'est la relecture avec archivés qui révèle la vérité.

---

## 4. Conclusion

J'ai appelé `delete_loan` sur les **6 emprunts rendus** de MB-202 : `LN-5038`, `LN-5039`, `LN-5062`, `LN-5095`, `LN-5120`, `LN-5134`. Chaque appel a répondu `{"ok": true, "deleted": true}`.

**Preuve de disparition (vue par défaut)** : `list_loans(member_id = "MB-202")` ne renvoie plus que l'emprunt ouvert `LN-5060`. Les 6 emprunts rendus ont bien disparu de la vue courante.

**Mais** — et c'est le point à remonter au demandeur — `list_loans(member_id = "MB-202", include_archived: true)` les renvoie **tous les 6**, avec `archived: true`. L'API **n'effaçe pas** les emprunts : elle les archive. On peut seulement les **masquer** de la vue par défaut.

À signaler également : les 6 emprunts rendus étaient **déjà archivés avant** cette mission ; les suppressions n'ont donc rien changé de plus. Une **suppression définitive n'est pas possible** via l'API exposée. Si l'adhérent exige un effacement réel (droit à l'effacement), il faut passer par un traitement hors API (administration de la base) — la demande ne peut pas être satisfaite au sens strict avec les outils disponibles.
