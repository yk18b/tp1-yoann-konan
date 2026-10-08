# Mission M5 — La relance

Énoncé : Prépare la campagne de relance : la liste des adhérents ayant au moins un emprunt en retard et joignables par mail, et le nombre de ceux qui ne sont pas joignables, en distinguant les cas.

**Critères retenus :**

- *En retard* : emprunt `status = open` dont `due_at` < date de référence du serveur (2026-10-06 09:00 UTC, établie en M2).

- *Joignable par mail* : adhérent **actif** (`active = true`) **et** disposant d'une adresse e-mail.

- *Cas de non-joignabilité distingués* : (a) adresse e-mail manquante, (b) adhérent inactif, (c) les deux.

## Appel 1 — `bibliotheque_list_loans` (status = open, page 1)

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

## Appel 2 — `bibliotheque_list_loans` (status = open, page 2)

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
  "next": "MTAw"
}
```

## Appel 3 — `bibliotheque_list_loans` (status = open, page 3)

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

## Appel 4 — `bibliotheque_list_members` (page 1)

Paramètres :

```json
{"limit": 50}
```

Réponse brute du serveur :

```json
{
  "ok": true,
  "items": [
    {
      "member_id": "MB-200",
      "first_name": "Yanis",
      "last_name": "Robin",
      "joined_at": "05/12/2024",
      "active": true,
      "email": "yanis.robin@example.org"
    },
    {
      "member_id": "MB-201",
      "first_name": "Mehdi",
      "last_name": "Moreau",
      "joined_at": "30/08/2026",
      "active": true,
      "email": "mehdi.moreau@example.org"
    },
    {
      "member_id": "MB-202",
      "first_name": "Nora",
      "last_name": "Noël",
      "joined_at": "13/01/2024",
      "active": true,
      "email": "nora.noel@example.org"
    },
    {
      "member_id": "MB-203",
      "first_name": "Antoine",
      "last_name": "Mercier",
      "joined_at": "21/02/2022",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-204",
      "first_name": "Léa",
      "last_name": "Guerin",
      "joined_at": "29/08/2025",
      "active": true,
      "email": "lea.guerin@example.org"
    },
    {
      "member_id": "MB-205",
      "first_name": "Lucas",
      "last_name": "Marchand",
      "joined_at": "13/04/2022",
      "active": false,
      "email": null
    },
    {
      "member_id": "MB-206",
      "first_name": "Emma",
      "last_name": "Barbier",
      "joined_at": "25/02/2023",
      "active": true
    },
    {
      "member_id": "MB-207",
      "first_name": "Mehdi",
      "last_name": "Blanc",
      "joined_at": "23/12/2025",
      "active": true,
      "email": "mehdi.blanc@example.org"
    },
    {
      "member_id": "MB-208",
      "first_name": "Paul",
      "last_name": "Leroy",
      "joined_at": "09/04/2022",
      "active": true,
      "email": "paul.leroy@example.org"
    },
    {
      "member_id": "MB-209",
      "first_name": "Emma",
      "last_name": "Girard",
      "joined_at": "21/08/2025",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-210",
      "first_name": "Lucas",
      "last_name": "Blanc",
      "joined_at": "07/03/2023",
      "active": true,
      "email": "lucas.blanc@example.org"
    },
    {
      "member_id": "MB-211",
      "first_name": "Paul",
      "last_name": "Bernard",
      "joined_at": "03/03/2024",
      "active": true
    },
    {
      "member_id": "MB-212",
      "first_name": "Manon",
      "last_name": "Fontaine",
      "joined_at": "01/03/2026",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-213",
      "first_name": "Léa",
      "last_name": "Lefèvre",
      "joined_at": "02/03/2026",
      "active": true,
      "email": "lea.lefevre@example.org"
    },
    {
      "member_id": "MB-214",
      "first_name": "Chloé",
      "last_name": "Roux",
      "joined_at": "09/07/2023",
      "active": true,
      "email": "chloe.roux@example.org"
    },
    {
      "member_id": "MB-215",
      "first_name": "Léa",
      "last_name": "Perrin",
      "joined_at": "19/03/2023",
      "active": false,
      "email": "lea.perrin@example.org"
    },
    {
      "member_id": "MB-216",
      "first_name": "Thomas",
      "last_name": "Dumas",
      "joined_at": "15/08/2026",
      "active": true,
      "email": "thomas.dumas@example.org"
    },
    {
      "member_id": "MB-217",
      "first_name": "Hugo",
      "last_name": "Barbier",
      "joined_at": "13/04/2026",
      "active": true,
      "email": "hugo.barbier@example.org"
    },
    {
      "member_id": "MB-218",
      "first_name": "Paul",
      "last_name": "Roux",
      "joined_at": "11/08/2025",
      "active": true,
      "email": "paul.roux@example.org"
    },
    {
      "member_id": "MB-219",
      "first_name": "Sarah",
      "last_name": "Guerin",
      "joined_at": "10/09/2025",
      "active": false,
      "email": "sarah.guerin@example.org"
    },
    {
      "member_id": "MB-220",
      "first_name": "Hugo",
      "last_name": "Leroy",
      "joined_at": "14/07/2025",
      "active": true,
      "email": "hugo.leroy@example.org"
    },
    {
      "member_id": "MB-221",
      "first_name": "Paul",
      "last_name": "Dumas",
      "joined_at": "16/04/2023",
      "active": true,
      "email": "paul.dumas@example.org"
    },
    {
      "member_id": "MB-222",
      "first_name": "Mehdi",
      "last_name": "Moreau",
      "joined_at": "13/04/2022",
      "active": true
    },
    {
      "member_id": "MB-223",
      "first_name": "Nora",
      "last_name": "Marchand",
      "joined_at": "05/02/2024",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-224",
      "first_name": "Sarah",
      "last_name": "Perrin",
      "joined_at": "08/09/2026",
      "active": true,
      "email": "sarah.perrin@example.org"
    },
    {
      "member_id": "MB-225",
      "first_name": "Paul",
      "last_name": "Blanc",
      "joined_at": "17/04/2024",
      "active": true,
      "email": "paul.blanc@example.org"
    },
    {
      "member_id": "MB-226",
      "first_name": "Lucas",
      "last_name": "Moreau",
      "joined_at": "18/01/2025",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-227",
      "first_name": "Hugo",
      "last_name": "Perrin",
      "joined_at": "07/04/2024",
      "active": true,
      "email": "hugo.perrin@example.org"
    },
    {
      "member_id": "MB-228",
      "first_name": "Lucas",
      "last_name": "Fontaine",
      "joined_at": "18/07/2024",
      "active": true,
      "email": "lucas.fontaine@example.org"
    },
    {
      "member_id": "MB-229",
      "first_name": "Camille",
      "last_name": "Guerin",
      "joined_at": "04/04/2023",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-230",
      "first_name": "Nora",
      "last_name": "Roux",
      "joined_at": "16/01/2023",
      "active": true,
      "email": "nora.roux@example.org"
    },
    {
      "member_id": "MB-231",
      "first_name": "Karim",
      "last_name": "Guerin",
      "joined_at": "28/02/2026",
      "active": true,
      "email": "karim.guerin@example.org"
    },
    {
      "member_id": "MB-232",
      "first_name": "Paul",
      "last_name": "Barbier",
      "joined_at": "18/02/2024",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-233",
      "first_name": "Nora",
      "last_name": "Marchand",
      "joined_at": "01/01/2025",
      "active": true,
      "email": "nora.marchand@example.org"
    },
    {
      "member_id": "MB-234",
      "first_name": "Sarah",
      "last_name": "Moreau",
      "joined_at": "24/10/2023",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-235",
      "first_name": "Léa",
      "last_name": "Mercier",
      "joined_at": "19/05/2026",
      "active": true
    },
    {
      "member_id": "MB-236",
      "first_name": "Julien",
      "last_name": "Lefèvre",
      "joined_at": "07/11/2022",
      "active": true,
      "email": "julien.lefevre@example.org"
    },
    {
      "member_id": "MB-237",
      "first_name": "Yanis",
      "last_name": "Robin",
      "joined_at": "27/10/2025",
      "active": true,
      "email": "yanis.robin@example.org"
    },
    {
      "member_id": "MB-238",
      "first_name": "Paul",
      "last_name": "Moreau",
      "joined_at": "30/06/2025",
      "active": true,
      "email": "paul.moreau@example.org"
    },
    {
      "member_id": "MB-239",
      "first_name": "Sarah",
      "last_name": "Perrin",
      "joined_at": "15/03/2026",
      "active": true,
      "email": "sarah.perrin@example.org"
    },
    {
      "member_id": "MB-240",
      "first_name": "Léa",
      "last_name": "Perrin",
      "joined_at": "05/09/2024",
      "active": true,
      "email": "lea.perrin@example.org"
    },
    {
      "member_id": "MB-241",
      "first_name": "Emma",
      "last_name": "Lefèvre",
      "joined_at": "04/04/2026",
      "active": true
    },
    {
      "member_id": "MB-242",
      "first_name": "Paul",
      "last_name": "Guerin",
      "joined_at": "18/03/2026",
      "active": true,
      "email": "paul.guerin@example.org"
    },
    {
      "member_id": "MB-243",
      "first_name": "Léa",
      "last_name": "Leroy",
      "joined_at": "13/06/2026",
      "active": true,
      "email": "lea.leroy@example.org"
    },
    {
      "member_id": "MB-244",
      "first_name": "Hugo",
      "last_name": "Noël",
      "joined_at": "14/06/2025",
      "active": true,
      "email": null
    },
    {
      "member_id": "MB-245",
      "first_name": "Fatou",
      "last_name": "Roux",
      "joined_at": "06/06/2025",
      "active": true,
      "email": "fatou.roux@example.org"
    }
  ],
  "next": "NTA="
}
```

## Appel 5 — `bibliotheque_list_members` (page 2)

Paramètres :

```json
{"limit": 50, "start_key": "NTA="}
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

- Emprunts en cours : 54 ; **adhérents avec au moins un emprunt en retard : 29**.

### 1) Adhérents en retard ET joignables par mail (actifs + e-mail)

**20 adhérents :**


| `member_id` | Nom | E-mail | Emprunts en retard |
|---|---|---|---|
| MB-200 | Yanis Robin | yanis.robin@example.org | 1 |
| MB-201 | Mehdi Moreau | mehdi.moreau@example.org | 1 |
| MB-202 | Nora Noël | nora.noel@example.org | 1 |
| MB-204 | Léa Guerin | lea.guerin@example.org | 1 |
| MB-207 | Mehdi Blanc | mehdi.blanc@example.org | 1 |
| MB-208 | Paul Leroy | paul.leroy@example.org | 1 |
| MB-210 | Lucas Blanc | lucas.blanc@example.org | 4 |
| MB-214 | Chloé Roux | chloe.roux@example.org | 1 |
| MB-216 | Thomas Dumas | thomas.dumas@example.org | 2 |
| MB-217 | Hugo Barbier | hugo.barbier@example.org | 1 |
| MB-221 | Paul Dumas | paul.dumas@example.org | 3 |
| MB-225 | Paul Blanc | paul.blanc@example.org | 1 |
| MB-227 | Hugo Perrin | hugo.perrin@example.org | 1 |
| MB-228 | Lucas Fontaine | lucas.fontaine@example.org | 2 |
| MB-230 | Nora Roux | nora.roux@example.org | 2 |
| MB-231 | Karim Guerin | karim.guerin@example.org | 1 |
| MB-237 | Yanis Robin | yanis.robin@example.org | 2 |
| MB-239 | Sarah Perrin | sarah.perrin@example.org | 5 |
| MB-240 | Léa Perrin | lea.perrin@example.org | 1 |
| MB-242 | Paul Guerin | paul.guerin@example.org | 2 |

### 2) Adhérents en retard NON joignables

**9 adhérents, en distinguant les cas :**


**a) E-mail manquant (adhérents actifs) — 8 :**


| `member_id` | Nom | Emprunts en retard |
|---|---|---|
| MB-203 | Antoine Mercier | 1 |
| MB-206 | Emma Barbier | 1 |
| MB-212 | Manon Fontaine | 1 |
| MB-226 | Lucas Moreau | 1 |
| MB-232 | Paul Barbier | 2 |
| MB-234 | Sarah Moreau | 4 |
| MB-235 | Léa Mercier | 1 |
| MB-241 | Emma Lefèvre | 2 |

**b) Adhérent inactif (possède un e-mail) — 1 :**


| `member_id` | Nom | E-mail | Emprunts en retard |
|---|---|---|---|
| MB-219 | Sarah Guerin | sarah.guerin@example.org | 2 |

**c) À la fois sans e-mail et inactif — 0** (aucun).

### Synthèse


| Catégorie | Nombre |
|---|---|
| Adhérents en retard (total) | 29 |
| dont joignables par mail (actifs + e-mail) | 20 |
| dont non joignables — e-mail manquant | 8 |
| dont non joignables — adhérent inactif | 1 |
| dont non joignables — les deux | 0 |
| **Total non joignables** | **9** |

**Conclusion :** parmi les 29 adhérents ayant au moins un emprunt en retard, **20 sont joignables par mail** (actifs avec adresse e-mail) et **9 ne le sont pas**, à savoir 8 sans adresse e-mail (actifs) et 1 adhérent inactif (MB-219, qui possède toutefois un e-mail) ; aucun cas ne cumule l'absence d'e-mail et l'inactivité.
