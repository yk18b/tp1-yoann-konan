# Mission M2 — Le retardataire

Énoncé : Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent.

## Appel 1 — `bibliotheque_list_loans` (status=open, page 1)

Paramètres :

```json
{"status": "open", "limit": 50}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [
    {
      "loan_id": "LN-5004",
      "book_id": "BK-1090",
      "member_id": "MB-203",
      "started_at": 1790154000,
      "due_at": 1791968400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5006",
      "book_id": "BK-1133",
      "member_id": "MB-216",
      "started_at": 1779094800,
      "due_at": 1781514000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5008",
      "book_id": "BK-1040",
      "member_id": "MB-201",
      "started_at": 1787907600,
      "due_at": 1789722000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5009",
      "book_id": "BK-1159",
      "member_id": "MB-245",
      "started_at": 1790413200,
      "due_at": 1792832400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5012",
      "book_id": "BK-1083",
      "member_id": "MB-235",
      "started_at": 1780736400,
      "due_at": 1781946000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5013",
      "book_id": "BK-1027",
      "member_id": "MB-234",
      "started_at": 1776243600,
      "due_at": 1777453200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5014",
      "book_id": "BK-1175",
      "member_id": "MB-239",
      "started_at": 1782118800,
      "due_at": 1784538000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5015",
      "book_id": "BK-1103",
      "member_id": "MB-221",
      "started_at": 1776675600,
      "due_at": 1777885200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5016",
      "book_id": "BK-1135",
      "member_id": "MB-234",
      "started_at": 1780822800,
      "due_at": 1782637200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5020",
      "book_id": "BK-1016",
      "member_id": "MB-216",
      "started_at": 1774947600,
      "due_at": 1776762000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5022",
      "book_id": "BK-1112",
      "member_id": "MB-234",
      "started_at": 1789981200,
      "due_at": 1791190800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5023",
      "book_id": "BK-1035",
      "member_id": "MB-237",
      "started_at": 1776243600,
      "due_at": 1778058000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5024",
      "book_id": "BK-1012",
      "member_id": "MB-232",
      "started_at": 1774861200,
      "due_at": 1776675600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5025",
      "book_id": "BK-1151",
      "member_id": "MB-212",
      "started_at": 1785834000,
      "due_at": 1787648400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5026",
      "book_id": "BK-1053",
      "member_id": "MB-242",
      "started_at": 1784106000,
      "due_at": 1785920400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5034",
      "book_id": "BK-1022",
      "member_id": "MB-200",
      "started_at": 1788426000,
      "due_at": 1790845200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
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
      "loan_id": "LN-5045",
      "book_id": "BK-1090",
      "member_id": "MB-204",
      "started_at": 1782291600,
      "due_at": 1784106000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5054",
      "book_id": "BK-1151",
      "member_id": "MB-232",
      "started_at": 1783501200,
      "due_at": 1785315600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5055",
      "book_id": "BK-1141",
      "member_id": "MB-219",
      "started_at": 1790758800,
      "due_at": 1793178000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5057",
      "book_id": "BK-1111",
      "member_id": "MB-239",
      "started_at": 1783414800,
      "due_at": 1784624400,
      "returned_at": null,
      "status": "open",
      "archived": false,
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
      "loan_id": "LN-5065",
      "book_id": "BK-1060",
      "member_id": "MB-210",
      "started_at": 1786179600,
      "due_at": 1788598800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5066",
      "book_id": "BK-1112",
      "member_id": "MB-231",
      "started_at": 1779267600,
      "due_at": 1780477200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5068",
      "book_id": "BK-1097",
      "member_id": "MB-226",
      "started_at": 1779440400,
      "due_at": 1781859600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5074",
      "book_id": "BK-1180",
      "member_id": "MB-230",
      "started_at": 1781686800,
      "due_at": 1783501200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5075",
      "book_id": "BK-1128",
      "member_id": "MB-210",
      "started_at": 1784451600,
      "due_at": 1785661200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5077",
      "book_id": "BK-1076",
      "member_id": "MB-234",
      "started_at": 1776330000,
      "due_at": 1778144400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5078",
      "book_id": "BK-1012",
      "member_id": "MB-209",
      "started_at": 1790845200,
      "due_at": 1792659600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5082",
      "book_id": "BK-1122",
      "member_id": "MB-227",
      "started_at": 1788771600,
      "due_at": 1791190800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5084",
      "book_id": "BK-1016",
      "member_id": "MB-208",
      "started_at": 1783674000,
      "due_at": 1785488400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5085",
      "book_id": "BK-1167",
      "member_id": "MB-203",
      "started_at": 1781514000,
      "due_at": 1782723600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5086",
      "book_id": "BK-1056",
      "member_id": "MB-241",
      "started_at": 1779440400,
      "due_at": 1781254800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5088",
      "book_id": "BK-1013",
      "member_id": "MB-219",
      "started_at": 1780995600,
      "due_at": 1782810000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5091",
      "book_id": "BK-1099",
      "member_id": "MB-210",
      "started_at": 1784365200,
      "due_at": 1786179600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5094",
      "book_id": "BK-1132",
      "member_id": "MB-230",
      "started_at": 1787648400,
      "due_at": 1790067600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5097",
      "book_id": "BK-1060",
      "member_id": "MB-240",
      "started_at": 1787734800,
      "due_at": 1790154000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5098",
      "book_id": "BK-1083",
      "member_id": "MB-206",
      "started_at": 1779094800,
      "due_at": 1780304400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5099",
      "book_id": "BK-1171",
      "member_id": "MB-210",
      "started_at": 1780736400,
      "due_at": 1782550800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5100",
      "book_id": "BK-1012",
      "member_id": "MB-228",
      "started_at": 1783501200,
      "due_at": 1785315600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
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
    },
    {
      "loan_id": "LN-5107",
      "book_id": "BK-1005",
      "member_id": "MB-241",
      "started_at": 1776934800,
      "due_at": 1779354000,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5109",
      "book_id": "BK-1183",
      "member_id": "MB-221",
      "started_at": 1782205200,
      "due_at": 1783414800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5111",
      "book_id": "BK-1027",
      "member_id": "MB-221",
      "started_at": 1776762000,
      "due_at": 1777971600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5114",
      "book_id": "BK-1030",
      "member_id": "MB-239",
      "started_at": 1789117200,
      "due_at": 1790326800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5115",
      "book_id": "BK-1057",
      "member_id": "MB-239",
      "started_at": 1775293200,
      "due_at": 1777712400,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5118",
      "book_id": "BK-1113",
      "member_id": "MB-242",
      "started_at": 1775552400,
      "due_at": 1777366800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5121",
      "book_id": "BK-1132",
      "member_id": "MB-228",
      "started_at": 1782378000,
      "due_at": 1784797200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    },
    {
      "loan_id": "LN-5125",
      "book_id": "BK-1077",
      "member_id": "MB-219",
      "started_at": 1786957200,
      "due_at": 1788166800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5127",
      "book_id": "BK-1016",
      "member_id": "MB-237",
      "started_at": 1775552400,
      "due_at": 1777366800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    }
  ],
  "next": "NTA="
}
```

## Appel 2 — `bibliotheque_list_loans` (status=open, page 2)

Paramètres :

```json
{"status": "open", "limit": 50, "start_key": "NTA="}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [
    {
      "loan_id": "LN-5129",
      "book_id": "BK-1020",
      "member_id": "MB-207",
      "started_at": 1775725200,
      "due_at": 1777539600,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "A1"
    },
    {
      "loan_id": "LN-5131",
      "book_id": "BK-1164",
      "member_id": "MB-217",
      "started_at": 1785920400,
      "due_at": 1787734800,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "C3"
    },
    {
      "loan_id": "LN-5133",
      "book_id": "BK-1100",
      "member_id": "MB-239",
      "started_at": 1783242000,
      "due_at": 1785661200,
      "returned_at": null,
      "status": "open",
      "archived": false,
      "desk_code": "B2"
    }
  ],
  "next": "MTAw"
}
```

## Appel 3 — `bibliotheque_list_loans` (status=open, page 3)

Paramètres :

```json
{"status": "open", "limit": 50, "start_key": "MTAw"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [],
  "next": "MTUw"
}
```

## Appel 4 — `bibliotheque_get_member`

Paramètres :

```json
{"memberId": "MB-225"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "member": {
    "member_id": "MB-225",
    "first_name": "Paul",
    "last_name": "Blanc",
    "joined_at": "17/04/2024",
    "active": true,
    "email": "paul.blanc@example.org"
  }
}
```

## Appel 5 — `bibliotheque_get_book`

Paramètres :

```json
{"book_id": "BK-1075"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "book": {
    "book_id": "BK-1075",
    "title": "Le Retour des autres",
    "author": "Yanis Perrin",
    "genre": "poésie",
    "copies": 3,
    "loan_duration": 14,
    "added_at": "2026-04-18T09:00:00.000Z",
    "archived": false
  }
}
```

## Appel 6 — `bibliotheque_get_member_fees`

Paramètres :

```json
{"member_id": "MB-225"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "member_id": "MB-225",
  "open_loans": 1,
  "overdue_duration": 4296,
  "late_fee_per_day": 15,
  "balance_due": 26.85
}
```

## Appel 7 — `bibliotheque_get_member_fees`

Paramètres :

```json
{"member_id": "MB-200"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "member_id": "MB-200",
  "open_loans": 1,
  "overdue_duration": 120,
  "late_fee_per_day": 15,
  "balance_due": 0.75
}
```

## Appel 8 — `bibliotheque_get_member_fees`

Paramètres :

```json
{"member_id": "MB-227"}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "member_id": "MB-227",
  "open_loans": 1,
  "overdue_duration": 24,
  "late_fee_per_day": 15,
  "balance_due": 0.15
}
```

## Conclusion finale

- **Adhérent : MB-225 — Paul Blanc** (membre actif, paul.blanc@example.org).

- **Ouvrage : BK-1075** — « Le Retour des autres » (Yanis Perrin, poésie).

- **Emprunt : LN-5106**, échéance le 2026-04-10 09:00 (démarré le 2026-03-27 09:00).

- **Retard exact : 179 jours** (date de référence du serveur : 2026-10-06 09:00).

> Remarque : le serveur retient une date de référence au 2026-10-06 09:00 UTC (vérifiée via `get_member_fees`). Au 2026-10-09 (date locale de la machine) l'écart calendaire serait de 182 jours ; l'écart pénalisable retenu par le serveur, cohérent avec le montant dû, est de 179 jours.

- **Montant dû par l'adhérent : 26.85** (15 centimes/jour × 179 jours = 26.85 ; `late_fee_per_day = 15`, `balance_due = 26.85`).

### Méthode

1. `bibliotheque_list_loans` avec `status = "open"` (toutes les pages) donne tous les emprunts en cours ; l'emprunt dont l'échéance `due_at` est la plus ancienne est `LN-5106` (MB-225 / BK-1075).

2. `bibliotheque_get_member` et `bibliotheque_get_book` identifient l'adhérent et l'ouvrage.

3. `bibliotheque_get_member_fees` donne le montant dû et la durée de retard `overdue_duration = 4296` (en heures) → 179 jours, cohérent avec l'écart d'échéance.

4. La date de référence du serveur (2026-10-06 09:00 UTC) est calibrée avec les appels `get_member_fees` sur MB-200 (échéance 01/10 → 120 h = 5 j) et MB-227 (échéance 05/10 → 24 h = 1 j).

### Classement des 5 emprunts les plus en retard

| Rang | Emprunt | Adhérent | Ouvrage | Échéance | Retard (j) |
|---|---|---|---|---|---|
| 1 | LN-5106 | MB-225 | BK-1075 | 2026-04-10 09:00 | 179 |
| 2 | LN-5024 | MB-232 | BK-1012 | 2026-04-20 09:00 | 169 |
| 3 | LN-5020 | MB-216 | BK-1016 | 2026-04-21 09:00 | 168 |
| 4 | LN-5118 | MB-242 | BK-1113 | 2026-04-28 09:00 | 161 |
| 5 | LN-5127 | MB-237 | BK-1016 | 2026-04-28 09:00 | 161 |