# Mission M1 — Inventaire

**Demande (brief M1)** : « Le conseil municipal demande le nombre exact d'ouvrages détenus par la bibliothèque, et la répartition par genre. Donne les chiffres et explique comment tu les as obtenus. »

Skill chargé : `bibliotheque-api` (règles de pagination, de suppression logique, unités, etc.).

---

## 1. Appels passés

| # | Outil | Paramètres |
|---|-------|------------|
| 1 | `bibliotheque_list_missions` | (aucun) |
| 2 | `bibliotheque_get_mission` | `{ "mission_id": "M1" }` |
| 3 | `bibliotheque_count_books` | (aucun) |
| 4 | `bibliotheque_list_books` | `{ "include_archived": true, "limit": 100 }` |
| 5 | `bibliotheque_list_books` | `{ "include_archived": true, "limit": 100, "start_key": "NTA=" }` |
| 6 | `bibliotheque_list_books` | `{ "include_archived": true, "limit": 100, "start_key": "MTAw" }` |
| 7 | `bibliotheque_list_books` | `{ "include_archived": true, "limit": 100, "start_key": "MTUw" }` |
| 8 | `bibliotheque_list_books` | `{ "include_archived": true, "limit": 100, "start_key": "MjAw" }` (page de contrôle de fin) |
| 9 | `bibliotheque_list_books` | `{ "genre": "roman", "include_archived": true, "limit": 100 }` |
| 10 | `bibliotheque_list_books` | `{ "genre": "policier", "include_archived": true, "limit": 100 }` |
| 11 | `bibliotheque_list_books` | `{ "genre": "jeunesse", "include_archived": true, "limit": 100 }` |
| 12 | `bibliotheque_list_books` | `{ "genre": "essai", "include_archived": true, "limit": 100 }` |
| 13 | `bibliotheque_list_books` | `{ "genre": "bd", "include_archived": true, "limit": 100 }` |
| 14 | `bibliotheque_list_books` | `{ "genre": "poésie", "include_archived": true, "limit": 100 }` |

**Note de pagination** : bien que `limit` soit fixé à 100, le serveur plafonne chaque page à 50 éléments. Le champ `next` est **toujours** renvoyé, même sur la dernière page de données **et** sur la page vide. Conformément au skill (piège 6), je ne me suis pas arrêté à l'absence de `next` mais à la première page dont `items` est vide.

---

## 2. Réponses brutes

### 2.1 `list_missions`

```json
{
  "ok": true,
  "items": [
    { "mission_id": "M1", "title": "Inventaire" },
    { "mission_id": "M2", "title": "Le retardataire" },
    { "mission_id": "M3", "title": "La réinscription" },
    { "mission_id": "M4", "title": "Le ménage" },
    { "mission_id": "M5", "title": "La relance" }
  ]
}
```

### 2.2 `get_mission`

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M1",
    "title": "Inventaire",
    "brief": "Le conseil municipal demande le nombre exact d'ouvrages détenus par la bibliothèque, et la répartition par genre. Donne les chiffres et explique comment tu les as obtenus."
  }
}
```

### 2.3 `count_books`

```json
{ "ok": true, "count": 184 }
```

### 2.4 Catalogue complet paginé (`list_books`, archivés inclus)

Pagination brute (tronquée aux métadonnées ; voir §2.5 pour le contenu intégral item par item) :

```json
{ "items": [ 50 éléments, BK-1000 .. BK-1049 ], "next": "NTA=" }      // NTA=  -> offset 50
{ "items": [ 50 éléments, BK-1050 .. BK-1099 ], "next": "MTAw" }     // MTAw  -> offset 100
{ "items": [ 50 éléments, BK-1100 .. BK-1149 ], "next": "MTUw" }     // MTUw  -> offset 150
{ "items": [ 34 éléments, BK-1150 .. BK-1183 ], "next": "MjAw" }     // MjAw  -> offset 200
{ "items": [],                                   "next": "MjUw" }    // page vide -> fin de liste
```

Total titres = 50 + 50 + 50 + 34 = **184**. Concordance exacte avec `count_books` (184).
(Le curseur `next` est l'offset encodé en base64 : `NTA=`=50, `MTAw`=100, `MTUw`=150, `MjAw`=200.)

### 2.5 Répartition par genre — contenu brut (JSON compact, aucune valeur modifiée)

#### `list_books({ "genre": "roman", "include_archived": true })`

```json
[
{"book_id":"BK-1012","title":"Le Retour des autres","author":"Léa Mercier","genre":"roman","copies":2,"loan_duration":21,"added_at":"2024-07-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1025","title":"Les Mains au loin","author":"Manon Guerin","genre":"roman","copies":2,"loan_duration":28,"added_at":"2024-05-12T09:00:00.000Z","archived":false},
{"book_id":"BK-1026","title":"Les Mains au loin","author":"Léa Guerin","genre":"roman","copies":4,"loan_duration":21,"added_at":"2020-11-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1027","title":"La Maison sans fin","author":"Camille Robin","genre":"roman","copies":1,"loan_duration":14,"added_at":"2024-10-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1037","title":"Les Ombres de Marseille","author":"Thomas Mercier","genre":"roman","copies":3,"loan_duration":14,"added_at":"2024-06-10T09:00:00.000Z","archived":false},
{"book_id":"BK-1049","title":"Les Mains du fleuve","author":"Paul Perrin","genre":"roman","copies":2,"loan_duration":14,"added_at":"2025-01-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1053","title":"La Nuit des autres","author":"Antoine Bernard","genre":"roman","copies":3,"loan_duration":21,"added_at":"2022-01-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1059","title":"Une Histoire du fleuve","author":"Paul Marchand","genre":"roman","copies":1,"loan_duration":21,"added_at":"2022-08-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1061","title":"La Traversée en hiver","author":"Hugo Girard","genre":"roman","copies":1,"loan_duration":14,"added_at":"2025-12-31T09:00:00.000Z","archived":false},
{"book_id":"BK-1064","title":"Les Heures au loin","author":"Chloé Dumas","genre":"roman","copies":3,"loan_duration":14,"added_at":"2023-02-27T09:00:00.000Z","archived":true},
{"book_id":"BK-1068","title":"Le Dernier des autres (tome 2)","author":"Fatou Noël","genre":"roman","copies":3,"loan_duration":14,"added_at":"2025-02-06T09:00:00.000Z","archived":true},
{"book_id":"BK-1072","title":"La Maison du Nord","author":"Thomas Lefèvre","genre":"roman","copies":3,"loan_duration":14,"added_at":"2021-09-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1081","title":"Les Mains de verre","author":"Chloé Lefèvre","genre":"roman","copies":1,"loan_duration":14,"added_at":"2021-12-16T09:00:00.000Z","archived":true},
{"book_id":"BK-1088","title":"Le Retour de septembre","author":"Fatou Robin","genre":"roman","copies":1,"loan_duration":21,"added_at":"2025-04-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1092","title":"Le Retour oubliées","author":"Inès Fontaine","genre":"roman","copies":1,"loan_duration":21,"added_at":"2023-12-13T09:00:00.000Z","archived":false},
{"book_id":"BK-1093","title":"Le Dernier du Nord","author":"Julien Roux","genre":"roman","copies":2,"loan_duration":14,"added_at":"2020-10-26T09:00:00.000Z","archived":false},
{"book_id":"BK-1099","title":"Le Dernier du libraire","author":"Nora Leroy","genre":"roman","copies":2,"loan_duration":21,"added_at":"2025-07-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1101","title":"Le Dernier de pierre","author":"Yanis Dumas","genre":"roman","copies":2,"loan_duration":28,"added_at":"2023-12-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1102","title":"Les Racines du dimanche (tome 2)","author":"Fatou Perrin","genre":"roman","copies":4,"loan_duration":21,"added_at":"2023-01-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1118","title":"Le Dernier de septembre","author":"Yanis Blanc","genre":"roman","copies":3,"loan_duration":28,"added_at":"2021-08-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1122","title":"Le Chant au loin","author":"Inès Bernard","genre":"roman","copies":2,"loan_duration":28,"added_at":"2023-05-31T09:00:00.000Z","archived":false},
{"book_id":"BK-1123","title":"Le Dernier oubliées","author":"Hugo Mercier","genre":"roman","copies":4,"loan_duration":28,"added_at":"2024-11-20T09:00:00.000Z","archived":false},
{"book_id":"BK-1127","title":"La Frontière oubliées","author":"Lucas Moreau","genre":"roman","copies":1,"loan_duration":28,"added_at":"2021-03-10T09:00:00.000Z","archived":false},
{"book_id":"BK-1133","title":"La Traversée du fleuve","author":"Paul Mercier","genre":"roman","copies":2,"loan_duration":28,"added_at":"2026-03-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1140","title":"Les Fenêtres perdues","author":"Yanis Guerin","genre":"roman","copies":4,"loan_duration":14,"added_at":"2022-05-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1148","title":"La Mémoire du dimanche","author":"Emma Mercier","genre":"roman","copies":4,"loan_duration":28,"added_at":"2024-02-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1149","title":"Le Dernier du Nord","author":"Léa Barbier","genre":"roman","copies":1,"loan_duration":28,"added_at":"2022-08-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1157","title":"Une Histoire au loin","author":"Thomas Robin","genre":"roman","copies":4,"loan_duration":21,"added_at":"2023-03-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1160","title":"Le Silence des collines","author":"Camille Lefèvre","genre":"roman","copies":3,"loan_duration":21,"added_at":"2025-09-12T09:00:00.000Z","archived":false},
{"book_id":"BK-1162","title":"Le Chant au loin","author":"Inès Mercier","genre":"roman","copies":2,"loan_duration":21,"added_at":"2025-11-11T09:00:00.000Z","archived":false},
{"book_id":"BK-1167","title":"Un Été du libraire","author":"Manon Guerin","genre":"roman","copies":2,"loan_duration":14,"added_at":"2025-04-10T09:00:00.000Z","archived":false},
{"book_id":"BK-1173","title":"La Nuit du fleuve","author":"Thomas Perrin","genre":"roman","copies":1,"loan_duration":21,"added_at":"2021-12-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1177","title":"Le Chant du dimanche","author":"Nora Blanc","genre":"roman","copies":3,"loan_duration":14,"added_at":"2026-07-20T09:00:00.000Z","archived":true},
{"book_id":"BK-1178","title":"Un Été de verre","author":"Paul Girard","genre":"roman","copies":4,"loan_duration":14,"added_at":"2024-01-14T09:00:00.000Z","archived":false}
]
// next: "NTA=" (mais items non vide -> une seule page ; le serveur renvoie nonetheless next)
```

#### `list_books({ "genre": "policier", "include_archived": true })`

```json
[
{"book_id":"BK-1001","title":"Le Chant du fleuve","author":"Karim Marchand","genre":"policier","copies":3,"loan_duration":28,"added_at":"2021-01-13T09:00:00.000Z","archived":false},
{"book_id":"BK-1005","title":"Un Été perdues","author":"Sarah Lefèvre","genre":"policier","copies":4,"loan_duration":28,"added_at":"2026-02-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1006","title":"Un Été du Nord","author":"Antoine Marchand","genre":"policier","copies":2,"loan_duration":28,"added_at":"2026-03-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1010","title":"Les Racines de pierre","author":"Thomas Guerin","genre":"policier","copies":4,"loan_duration":21,"added_at":"2021-09-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1014","title":"Le Chant au loin","author":"Mehdi Mercier","genre":"policier","copies":1,"loan_duration":28,"added_at":"2025-09-20T09:00:00.000Z","archived":false},
{"book_id":"BK-1019","title":"La Maison de Marseille","author":"Emma Mercier","genre":"policier","copies":3,"loan_duration":14,"added_at":"2026-08-12T09:00:00.000Z","archived":true},
{"book_id":"BK-1028","title":"Le Retour de verre","author":"Paul Roux","genre":"policier","copies":4,"loan_duration":28,"added_at":"2022-09-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1030","title":"La Mémoire du Nord","author":"Léa Barbier","genre":"policier","copies":4,"loan_duration":14,"added_at":"2022-01-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1035","title":"Le Voyage sans fin","author":"Paul Mercier","genre":"policier","copies":3,"loan_duration":21,"added_at":"2021-10-31T09:00:00.000Z","archived":false},
{"book_id":"BK-1042","title":"Le Dernier de verre","author":"Karim Barbier","genre":"policier","copies":4,"loan_duration":21,"added_at":"2023-11-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1056","title":"Le Voyage de pierre","author":"Mehdi Leroy","genre":"policier","copies":3,"loan_duration":21,"added_at":"2024-10-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1070","title":"Le Jardin du fleuve","author":"Léa Noël","genre":"policier","copies":1,"loan_duration":14,"added_at":"2026-06-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1071","title":"Le Voyage oubliées","author":"Inès Moreau","genre":"policier","copies":4,"loan_duration":28,"added_at":"2025-04-16T09:00:00.000Z","archived":false},
{"book_id":"BK-1091","title":"Une Histoire des vivants","author":"Thomas Moreau","genre":"policier","copies":2,"loan_duration":21,"added_at":"2023-10-12T09:00:00.000Z","archived":false},
{"book_id":"BK-1095","title":"Les Ombres de Marseille","author":"Nora Fontaine","genre":"policier","copies":4,"loan_duration":21,"added_at":"2022-01-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1097","title":"Le Chant du libraire","author":"Fatou Guerin","genre":"policier","copies":1,"loan_duration":28,"added_at":"2023-06-16T09:00:00.000Z","archived":false},
{"book_id":"BK-1104","title":"Le Voyage des vivants","author":"Manon Barbier","genre":"policier","copies":2,"loan_duration":28,"added_at":"2024-10-07T09:00:00.000Z","archived":true},
{"book_id":"BK-1109","title":"Le Retour perdues","author":"Thomas Perrin","genre":"policier","copies":2,"loan_duration":21,"added_at":"2024-08-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1119","title":"Une Histoire des autres (tome 2)","author":"Emma Barbier","genre":"policier","copies":1,"loan_duration":28,"added_at":"2021-11-02T09:00:00.000Z","archived":false},
{"book_id":"BK-1131","title":"La Nuit perdues","author":"Manon Perrin","genre":"policier","copies":1,"loan_duration":28,"added_at":"2021-01-11T09:00:00.000Z","archived":true},
{"book_id":"BK-1138","title":"Les Heures du libraire","author":"Mehdi Dumas","genre":"policier","copies":3,"loan_duration":14,"added_at":"2025-10-20T09:00:00.000Z","archived":false},
{"book_id":"BK-1143","title":"Un Été du dimanche","author":"Chloé Bernard","genre":"policier","copies":3,"loan_duration":14,"added_at":"2021-05-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1146","title":"Un Été des vivants","author":"Paul Fontaine","genre":"policier","copies":4,"loan_duration":14,"added_at":"2022-01-30T09:00:00.000Z","archived":false},
{"book_id":"BK-1153","title":"Les Mains perdues (tome 2)","author":"Yanis Blanc","genre":"policier","copies":2,"loan_duration":28,"added_at":"2025-11-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1155","title":"La Nuit oubliées","author":"Julien Marchand","genre":"policier","copies":4,"loan_duration":21,"added_at":"2024-07-25T09:00:00.000Z","archived":false},
{"book_id":"BK-1158","title":"Les Ombres du dimanche","author":"Lucas Roux","genre":"policier","copies":1,"loan_duration":28,"added_at":"2020-12-16T09:00:00.000Z","archived":true},
{"book_id":"BK-1163","title":"Les Ombres du libraire","author":"Fatou Girard","genre":"policier","copies":4,"loan_duration":28,"added_at":"2023-01-31T09:00:00.000Z","archived":false},
{"book_id":"BK-1168","title":"Un Été au loin","author":"Antoine Roux","genre":"policier","copies":4,"loan_duration":28,"added_at":"2026-02-16T09:00:00.000Z","archived":false},
{"book_id":"BK-1170","title":"Les Heures en hiver (tome 2)","author":"Paul Lefèvre","genre":"policier","copies":1,"loan_duration":21,"added_at":"2023-04-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1171","title":"Les Mains sans fin","author":"Yanis Marchand","genre":"policier","copies":4,"loan_duration":21,"added_at":"2023-08-23T09:00:00.000Z","archived":false},
{"book_id":"BK-1182","title":"Les Ombres de Marseille","author":"Emma Roux","genre":"policier","copies":3,"loan_duration":28,"added_at":"2026-03-28T09:00:00.000Z","archived":false}
]
// next: "NTA="
```

#### `list_books({ "genre": "jeunesse", "include_archived": true })`

```json
[
{"book_id":"BK-1004","title":"La Maison de pierre","author":"Léa Girard","genre":"jeunesse","copies":4,"loan_duration":21,"added_at":"2020-12-16T09:00:00.000Z","archived":true},
{"book_id":"BK-1013","title":"La Mémoire de septembre","author":"Yanis Moreau","genre":"jeunesse","copies":3,"loan_duration":21,"added_at":"2022-05-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1015","title":"La Nuit en hiver","author":"Sarah Bernard","genre":"jeunesse","copies":4,"loan_duration":28,"added_at":"2022-04-11T09:00:00.000Z","archived":false},
{"book_id":"BK-1016","title":"Les Racines oubliées","author":"Hugo Bernard","genre":"jeunesse","copies":3,"loan_duration":21,"added_at":"2024-02-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1021","title":"La Nuit en hiver","author":"Sarah Blanc","genre":"jeunesse","copies":4,"loan_duration":14,"added_at":"2025-10-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1024","title":"La Maison de verre","author":"Paul Noël","genre":"jeunesse","copies":3,"loan_duration":21,"added_at":"2026-09-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1038","title":"La Nuit oubliées","author":"Mehdi Guerin","genre":"jeunesse","copies":2,"loan_duration":21,"added_at":"2023-08-27T09:00:00.000Z","archived":true},
{"book_id":"BK-1040","title":"La Mémoire de septembre","author":"Chloé Bernard","genre":"jeunesse","copies":4,"loan_duration":21,"added_at":"2023-05-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1046","title":"Les Racines du dimanche","author":"Emma Barbier","genre":"jeunesse","copies":2,"loan_duration":21,"added_at":"2021-03-29T09:00:00.000Z","archived":false},
{"book_id":"BK-1047","title":"Une Histoire en hiver","author":"Yanis Marchand","genre":"jeunesse","copies":2,"loan_duration":21,"added_at":"2021-07-26T09:00:00.000Z","archived":true},
{"book_id":"BK-1050","title":"Le Silence oubliées","author":"Julien Blanc","genre":"jeunesse","copies":4,"loan_duration":21,"added_at":"2022-01-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1051","title":"Le Chant de Marseille (tome 2)","author":"Antoine Perrin","genre":"jeunesse","copies":4,"loan_duration":21,"added_at":"2022-04-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1052","title":"La Traversée sans fin","author":"Hugo Dumas","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2025-02-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1057","title":"Les Racines de verre","author":"Léa Perrin","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2024-06-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1060","title":"La Nuit de verre","author":"Inès Marchand","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2021-01-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1063","title":"Un Été oubliées","author":"Manon Guerin","genre":"jeunesse","copies":4,"loan_duration":21,"added_at":"2022-04-04T09:00:00.000Z","archived":true},
{"book_id":"BK-1073","title":"La Traversée oubliées","author":"Lucas Bernard","genre":"jeunesse","copies":2,"loan_duration":21,"added_at":"2021-10-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1077","title":"La Traversée du libraire","author":"Mehdi Fontaine","genre":"jeunesse","copies":1,"loan_duration":14,"added_at":"2023-08-20T09:00:00.000Z","archived":false},
{"book_id":"BK-1078","title":"Un Été du fleuve","author":"Thomas Leroy","genre":"jeunesse","copies":3,"loan_duration":21,"added_at":"2023-02-26T09:00:00.000Z","archived":false},
{"book_id":"BK-1089","title":"Un Été oubliées","author":"Inès Barbier","genre":"jeunesse","copies":1,"loan_duration":14,"added_at":"2023-06-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1094","title":"Le Retour de pierre","author":"Thomas Roux","genre":"jeunesse","copies":1,"loan_duration":28,"added_at":"2021-02-24T09:00:00.000Z","archived":false},
{"book_id":"BK-1096","title":"La Frontière au loin","author":"Manon Dumas","genre":"jeunesse","copies":3,"loan_duration":21,"added_at":"2020-10-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1098","title":"La Frontière du fleuve","author":"Yanis Barbier","genre":"jeunesse","copies":1,"loan_duration":14,"added_at":"2024-07-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1105","title":"Le Chant perdues","author":"Nora Roux","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2023-06-13T09:00:00.000Z","archived":true},
{"book_id":"BK-1110","title":"Le Jardin de pierre","author":"Nora Mercier","genre":"jeunesse","copies":3,"loan_duration":14,"added_at":"2026-07-19T09:00:00.000Z","archived":false},
{"book_id":"BK-1112","title":"La Maison oubliées","author":"Léa Mercier","genre":"jeunesse","copies":4,"loan_duration":14,"added_at":"2026-01-26T09:00:00.000Z","archived":false},
{"book_id":"BK-1116","title":"La Mémoire du dimanche","author":"Fatou Leroy","genre":"jeunesse","copies":4,"loan_duration":14,"added_at":"2026-09-23T09:00:00.000Z","archived":false},
{"book_id":"BK-1120","title":"Le Voyage du dimanche","author":"Paul Mercier","genre":"jeunesse","copies":1,"loan_duration":14,"added_at":"2023-01-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1124","title":"La Traversée oubliées","author":"Emma Marchand","genre":"jeunesse","copies":4,"loan_duration":28,"added_at":"2023-06-10T09:00:00.000Z","archived":true},
{"book_id":"BK-1134","title":"Un Été de verre","author":"Julien Marchand","genre":"jeunesse","copies":4,"loan_duration":14,"added_at":"2022-07-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1136","title":"La Traversée des vivants (tome 2)","author":"Nora Marchand","genre":"jeunesse","copies":1,"loan_duration":28,"added_at":"2025-12-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1139","title":"Les Racines de pierre","author":"Manon Blanc","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2025-01-14T09:00:00.000Z","archived":true},
{"book_id":"BK-1141","title":"Les Fenêtres sans fin","author":"Yanis Noël","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2026-04-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1145","title":"Le Silence du libraire","author":"Léa Blanc","genre":"jeunesse","copies":3,"loan_duration":28,"added_at":"2024-09-15T09:00:00.000Z","archived":true},
{"book_id":"BK-1150","title":"Le Silence de verre","author":"Fatou Mercier","genre":"jeunesse","copies":1,"loan_duration":28,"added_at":"2025-01-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1152","title":"Le Dernier des collines","author":"Thomas Mercier","genre":"jeunesse","copies":2,"loan_duration":21,"added_at":"2024-07-07T09:00:00.000Z","archived":false},
{"book_id":"BK-1156","title":"Un Été de pierre","author":"Léa Robin","genre":"jeunesse","copies":4,"loan_duration":28,"added_at":"2025-08-12T09:00:00.000Z","archived":false},
{"book_id":"BK-1165","title":"La Maison du dimanche","author":"Nora Barbier","genre":"jeunesse","copies":3,"loan_duration":14,"added_at":"2026-08-25T09:00:00.000Z","archived":false}
]
// next: "NTA="
```

#### `list_books({ "genre": "essai", "include_archived": true })`

```json
[
{"book_id":"BK-1002","title":"Les Racines des vivants","author":"Karim Mercier","genre":"essai","copies":4,"loan_duration":14,"added_at":"2024-02-02T09:00:00.000Z","archived":true},
{"book_id":"BK-1020","title":"Le Chant du fleuve","author":"Yanis Guerin","genre":"essai","copies":2,"loan_duration":21,"added_at":"2022-09-23T09:00:00.000Z","archived":false},
{"book_id":"BK-1029","title":"La Frontière du libraire","author":"Antoine Dumas","genre":"essai","copies":4,"loan_duration":14,"added_at":"2025-11-04T09:00:00.000Z","archived":false},
{"book_id":"BK-1033","title":"Le Retour de septembre","author":"Antoine Lefèvre","genre":"essai","copies":1,"loan_duration":21,"added_at":"2022-04-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1039","title":"Un Été sans fin","author":"Léa Lefèvre","genre":"essai","copies":4,"loan_duration":21,"added_at":"2024-03-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1043","title":"Les Mains de septembre","author":"Antoine Robin","genre":"essai","copies":2,"loan_duration":14,"added_at":"2022-05-13T09:00:00.000Z","archived":false},
{"book_id":"BK-1045","title":"Une Histoire sans fin","author":"Thomas Roux","genre":"essai","copies":2,"loan_duration":14,"added_at":"2023-09-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1048","title":"Une Histoire en hiver","author":"Léa Robin","genre":"essai","copies":2,"loan_duration":28,"added_at":"2024-06-30T09:00:00.000Z","archived":false},
{"book_id":"BK-1055","title":"La Mémoire perdues","author":"Antoine Bernard","genre":"essai","copies":2,"loan_duration":14,"added_at":"2021-08-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1058","title":"Les Ombres du Nord","author":"Antoine Barbier","genre":"essai","copies":2,"loan_duration":21,"added_at":"2025-05-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1062","title":"La Maison du fleuve","author":"Camille Perrin","genre":"essai","copies":4,"loan_duration":28,"added_at":"2021-06-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1067","title":"Le Jardin de septembre","author":"Manon Guerin","genre":"essai","copies":4,"loan_duration":14,"added_at":"2021-10-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1084","title":"La Maison perdues","author":"Chloé Bernard","genre":"essai","copies":2,"loan_duration":28,"added_at":"2023-02-21T09:00:00.000Z","archived":false},
{"book_id":"BK-1086","title":"Le Chant de verre","author":"Manon Blanc","genre":"essai","copies":1,"loan_duration":14,"added_at":"2026-04-20T09:00:00.000Z","archived":false},
{"book_id":"BK-1108","title":"Une Histoire de Marseille","author":"Antoine Lefèvre","genre":"essai","copies":4,"loan_duration":14,"added_at":"2022-03-29T09:00:00.000Z","archived":false},
{"book_id":"BK-1111","title":"Le Chant en hiver","author":"Thomas Mercier","genre":"essai","copies":1,"loan_duration":14,"added_at":"2026-02-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1113","title":"Une Histoire du Nord","author":"Fatou Lefèvre","genre":"essai","copies":3,"loan_duration":21,"added_at":"2021-03-01T09:00:00.000Z","archived":false},
{"book_id":"BK-1114","title":"Les Racines des autres","author":"Fatou Robin","genre":"essai","copies":4,"loan_duration":14,"added_at":"2022-08-07T09:00:00.000Z","archived":false},
{"book_id":"BK-1117","title":"Les Mains oubliées","author":"Julien Robin","genre":"essai","copies":4,"loan_duration":14,"added_at":"2021-05-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1126","title":"Les Mains oubliées","author":"Sarah Dumas","genre":"essai","copies":4,"loan_duration":21,"added_at":"2023-10-25T09:00:00.000Z","archived":false},
{"book_id":"BK-1144","title":"Le Voyage perdues","author":"Hugo Dumas","genre":"essai","copies":3,"loan_duration":14,"added_at":"2022-05-25T09:00:00.000Z","archived":false},
{"book_id":"BK-1164","title":"Le Jardin de verre","author":"Léa Perrin","genre":"essai","copies":3,"loan_duration":21,"added_at":"2023-05-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1169","title":"La Nuit du dimanche","author":"Mehdi Robin","genre":"essai","copies":4,"loan_duration":14,"added_at":"2024-12-21T09:00:00.000Z","archived":true},
{"book_id":"BK-1176","title":"Une Histoire perdues","author":"Chloé Leroy","genre":"essai","copies":4,"loan_duration":21,"added_at":"2021-07-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1180","title":"Le Silence des vivants","author":"Léa Lefèvre","genre":"essai","copies":3,"loan_duration":21,"added_at":"2023-11-02T09:00:00.000Z","archived":false}
]
// next: "NTA="
```

#### `list_books({ "genre": "bd", "include_archived": true })`

```json
[
{"book_id":"BK-1003","title":"La Mémoire au loin","author":"Lucas Noël","genre":"bd","copies":4,"loan_duration":21,"added_at":"2023-06-09T09:00:00.000Z","archived":true},
{"book_id":"BK-1011","title":"Un Été en hiver","author":"Mehdi Fontaine","genre":"bd","copies":1,"loan_duration":14,"added_at":"2022-03-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1017","title":"Le Chant oubliées (tome 2)","author":"Manon Leroy","genre":"bd","copies":3,"loan_duration":28,"added_at":"2025-09-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1023","title":"Le Voyage des autres","author":"Mehdi Perrin","genre":"bd","copies":2,"loan_duration":21,"added_at":"2022-02-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1054","title":"La Traversée des autres","author":"Chloé Fontaine","genre":"bd","copies":4,"loan_duration":21,"added_at":"2024-09-11T09:00:00.000Z","archived":false},
{"book_id":"BK-1065","title":"Un Été de Marseille","author":"Antoine Roux","genre":"bd","copies":1,"loan_duration":21,"added_at":"2026-05-28T09:00:00.000Z","archived":false},
{"book_id":"BK-1066","title":"La Frontière en hiver","author":"Mehdi Dumas","genre":"bd","copies":3,"loan_duration":21,"added_at":"2026-07-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1079","title":"Le Voyage du fleuve","author":"Thomas Roux","genre":"bd","copies":2,"loan_duration":14,"added_at":"2025-11-26T09:00:00.000Z","archived":false},
{"book_id":"BK-1080","title":"Le Voyage perdues","author":"Mehdi Mercier","genre":"bd","copies":3,"loan_duration":14,"added_at":"2025-08-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1082","title":"Le Silence de verre","author":"Emma Lefèvre","genre":"bd","copies":4,"loan_duration":28,"added_at":"2026-01-02T09:00:00.000Z","archived":false},
{"book_id":"BK-1083","title":"Les Racines du dimanche","author":"Sarah Perrin","genre":"bd","copies":3,"loan_duration":14,"added_at":"2025-08-23T09:00:00.000Z","archived":false},
{"book_id":"BK-1087","title":"Les Ombres perdues","author":"Yanis Bernard","genre":"bd","copies":2,"loan_duration":14,"added_at":"2024-03-02T09:00:00.000Z","archived":false},
{"book_id":"BK-1115","title":"Un Été du Nord","author":"Mehdi Robin","genre":"bd","copies":3,"loan_duration":28,"added_at":"2024-01-12T09:00:00.000Z","archived":false},
{"book_id":"BK-1125","title":"Le Silence des vivants","author":"Chloé Leroy","genre":"bd","copies":2,"loan_duration":28,"added_at":"2024-12-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1128","title":"Le Dernier de verre","author":"Emma Roux","genre":"bd","copies":2,"loan_duration":14,"added_at":"2025-07-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1135","title":"Le Jardin du fleuve","author":"Paul Guerin","genre":"bd","copies":2,"loan_duration":21,"added_at":"2023-01-03T09:00:00.000Z","archived":false},
{"book_id":"BK-1137","title":"La Traversée oubliées","author":"Camille Blanc","genre":"bd","copies":4,"loan_duration":21,"added_at":"2024-03-07T09:00:00.000Z","archived":false},
{"book_id":"BK-1142","title":"La Traversée au loin","author":"Mehdi Dumas","genre":"bd","copies":2,"loan_duration":14,"added_at":"2021-07-01T09:00:00.000Z","archived":false},
{"book_id":"BK-1147","title":"Le Dernier au loin","author":"Nora Blanc","genre":"bd","copies":1,"loan_duration":14,"added_at":"2022-10-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1154","title":"Les Heures de verre","author":"Antoine Robin","genre":"bd","copies":4,"loan_duration":14,"added_at":"2021-01-30T09:00:00.000Z","archived":true},
{"book_id":"BK-1166","title":"Une Histoire des autres","author":"Karim Noël","genre":"bd","copies":4,"loan_duration":28,"added_at":"2024-10-21T09:00:00.000Z","archived":false}
]
// next: "NTA="
```

#### `list_books({ "genre": "poésie", "include_archived": true })`

```json
[
{"book_id":"BK-1000","title":"Le Voyage du fleuve (tome 2)","author":"Hugo Perrin","genre":"poésie","copies":4,"loan_duration":14,"added_at":"2023-04-17T09:00:00.000Z","archived":false},
{"book_id":"BK-1007","title":"Les Ombres du fleuve","author":"Paul Guerin","genre":"poésie","copies":4,"loan_duration":21,"added_at":"2026-05-11T09:00:00.000Z","archived":false},
{"book_id":"BK-1008","title":"Les Heures de pierre","author":"Emma Bernard","genre":"poésie","copies":2,"loan_duration":21,"added_at":"2024-08-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1009","title":"Le Voyage des autres","author":"Karim Fontaine","genre":"poésie","copies":4,"loan_duration":21,"added_at":"2024-06-26T09:00:00.000Z","archived":false},
{"book_id":"BK-1018","title":"Le Jardin de verre","author":"Léa Roux","genre":"poésie","copies":4,"loan_duration":14,"added_at":"2022-06-27T09:00:00.000Z","archived":true},
{"book_id":"BK-1022","title":"La Maison de septembre","author":"Emma Marchand","genre":"poésie","copies":2,"loan_duration":28,"added_at":"2025-09-07T09:00:00.000Z","archived":false},
{"book_id":"BK-1031","title":"La Frontière sans fin","author":"Thomas Dumas","genre":"poésie","copies":3,"loan_duration":28,"added_at":"2025-03-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1032","title":"Un Été perdues","author":"Hugo Blanc","genre":"poésie","copies":3,"loan_duration":14,"added_at":"2023-11-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1034","title":"Un Été au loin (tome 2)","author":"Inès Bernard","genre":"poésie","copies":3,"loan_duration":21,"added_at":"2022-08-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1036","title":"Le Dernier de Marseille","author":"Léa Mercier","genre":"poésie","copies":1,"loan_duration":14,"added_at":"2024-05-02T09:00:00.000Z","archived":false},
{"book_id":"BK-1041","title":"La Maison de pierre","author":"Léa Guerin","genre":"poésie","copies":3,"loan_duration":14,"added_at":"2022-08-27T09:00:00.000Z","archived":true},
{"book_id":"BK-1044","title":"Une Histoire du dimanche","author":"Manon Robin","genre":"poésie","copies":1,"loan_duration":28,"added_at":"2025-12-27T09:00:00.000Z","archived":false},
{"book_id":"BK-1069","title":"Les Mains de pierre","author":"Emma Mercier","genre":"poésie","copies":2,"loan_duration":21,"added_at":"2023-03-17T09:00:00.000Z","archived":true},
{"book_id":"BK-1074","title":"Les Fenêtres du dimanche","author":"Julien Guerin","genre":"poésie","copies":2,"loan_duration":14,"added_at":"2023-07-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1075","title":"Le Retour des autres","author":"Yanis Perrin","genre":"poésie","copies":3,"loan_duration":14,"added_at":"2026-04-18T09:00:00.000Z","archived":false},
{"book_id":"BK-1076","title":"Les Heures du Nord","author":"Julien Leroy","genre":"poésie","copies":1,"loan_duration":21,"added_at":"2023-03-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1085","title":"Les Heures perdues (tome 2)","author":"Fatou Blanc","genre":"poésie","copies":4,"loan_duration":21,"added_at":"2025-06-16T09:00:00.000Z","archived":true},
{"book_id":"BK-1090","title":"Les Ombres de Marseille","author":"Thomas Leroy","genre":"poésie","copies":4,"loan_duration":21,"added_at":"2024-08-19T09:00:00.000Z","archived":false},
{"book_id":"BK-1100","title":"Les Heures du libraire","author":"Antoine Roux","genre":"poésie","copies":3,"loan_duration":28,"added_at":"2024-02-06T09:00:00.000Z","archived":false},
{"book_id":"BK-1103","title":"La Mémoire de septembre","author":"Julien Robin","genre":"poésie","copies":2,"loan_duration":14,"added_at":"2025-11-09T09:00:00.000Z","archived":false},
{"book_id":"BK-1106","title":"Les Heures du fleuve","author":"Yanis Marchand","genre":"poésie","copies":2,"loan_duration":21,"added_at":"2021-08-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1107","title":"Un Été sans fin","author":"Thomas Robin","genre":"poésie","copies":1,"loan_duration":14,"added_at":"2025-11-02T09:00:00.000Z","archived":false},
{"book_id":"BK-1121","title":"Les Mains du fleuve","author":"Emma Blanc","genre":"poésie","copies":4,"loan_duration":14,"added_at":"2021-11-05T09:00:00.000Z","archived":false},
{"book_id":"BK-1129","title":"Les Racines des autres","author":"Yanis Leroy","genre":"poésie","copies":4,"loan_duration":28,"added_at":"2022-08-21T09:00:00.000Z","archived":false},
{"book_id":"BK-1130","title":"La Nuit sans fin","author":"Julien Guerin","genre":"poésie","copies":1,"loan_duration":28,"added_at":"2024-01-15T09:00:00.000Z","archived":false},
{"book_id":"BK-1132","title":"Les Mains du Nord","author":"Lucas Fontaine","genre":"poésie","copies":2,"loan_duration":28,"added_at":"2021-06-08T09:00:00.000Z","archived":false},
{"book_id":"BK-1151","title":"Un Été du fleuve","author":"Yanis Marchand","genre":"poésie","copies":1,"loan_duration":21,"added_at":"2023-08-10T09:00:00.000Z","archived":false},
{"book_id":"BK-1159","title":"Le Chant de Marseille","author":"Inès Bernard","genre":"poésie","copies":4,"loan_duration":28,"added_at":"2020-12-22T09:00:00.000Z","archived":false},
{"book_id":"BK-1161","title":"Le Jardin du Nord","author":"Julien Noël","genre":"poésie","copies":3,"loan_duration":28,"added_at":"2024-12-29T09:00:00.000Z","archived":false},
{"book_id":"BK-1172","title":"La Maison au loin","author":"Camille Perrin","genre":"poésie","copies":2,"loan_duration":14,"added_at":"2024-05-29T09:00:00.000Z","archived":true},
{"book_id":"BK-1174","title":"Le Voyage de pierre","author":"Thomas Bernard","genre":"poésie","copies":2,"loan_duration":28,"added_at":"2024-11-28T09:00:00.000Z","archived":true},
{"book_id":"BK-1175","title":"Le Jardin des vivants","author":"Nora Girard","genre":"poésie","copies":2,"loan_duration":28,"added_at":"2026-06-19T09:00:00.000Z","archived":false},
{"book_id":"BK-1179","title":"Le Chant en hiver","author":"Hugo Marchand","genre":"poésie","copies":2,"loan_duration":28,"added_at":"2026-05-01T09:00:00.000Z","archived":false},
{"book_id":"BK-1181","title":"Les Racines du Nord","author":"Léa Dumas","genre":"poésie","copies":1,"loan_duration":14,"added_at":"2026-09-25T09:00:00.000Z","archived":false},
{"book_id":"BK-1183","title":"Le Chant de septembre","author":"Chloé Guerin","genre":"poésie","copies":1,"loan_duration":14,"added_at":"2024-12-08T09:00:00.000Z","archived":false}
]
// next: "NTA="
```

---

## 3. Dépouillement

Méthode : addition des éléments de chaque réponse ci-dessus.

### 3.1 Nombre de titres par genre

| Genre | Titres actifs | Titres archivés | Total titres |
|-------|--------------:|----------------:|-------------:|
| jeunesse | 30 | 8 | 38 |
| poésie | 29 | 6 | 35 |
| roman | 30 | 4 | 34 |
| policier | 27 | 4 | 31 |
| essai | 23 | 2 | 25 |
| bd | 19 | 2 | 21 |
| **Total** | **158** | **26** | **184** |

### 3.2 Exemplaires (champ `copies`)

| Genre | Exemplaires actifs | Exemplaires archivés | Total exemplaires |
|-------|-------------------:|---------------------:|------------------:|
| jeunesse | 82 | 25 | 107 |
| poésie | 70 | 17 | 87 |
| roman | 71 | 10 | 81 |
| policier | 79 | 7 | 86 |
| essai | 65 | 8 | 73 |
| bd | 48 | 8 | 56 |
| **Total** | **415** | **75** | **490** |

### 3.3 Contrôles de cohérence
- Somme des titres par genre = 34 + 31 + 38 + 25 + 21 + 35 = **184** = `count_books.count`.
- Somme des titres via pagination complète = 50 + 50 + 50 + 34 = **184**.
- Somme des exemplaires par genre = 81 + 86 + 107 + 73 + 56 + 87 = **490**.
- Somme actifs + archivés : titres 158 + 26 = 184 ; exemplaires 415 + 75 = 490.

---

## 4. Conclusion

La bibliothèque détient **184 titres** au catalogue (copies archivées incluses), soit **490 exemplaires physiques**. Comme certains titres sont archivés (« retirés de la circulation » selon la description de `list_books`), je donne les deux lectures :

- **Ouvrages réellement en circulation** : **158 titres** pour **415 exemplaires**.
- **Ensemble du catalogue, archivés compris** : **184 titres** pour **490 exemplaires**.

Répartition par genre (titres / exemplaires) :

| Genre | Titres (total) | Exemplaires (total) | Titres en circulation | Exemplaires en circulation |
|-------|---------------:|--------------------:|----------------------:|---------------------------:|
| jeunesse | 38 | 107 | 30 | 82 |
| poésie | 35 | 87 | 29 | 70 |
| roman | 34 | 81 | 30 | 71 |
| policier | 31 | 86 | 27 | 79 |
| essai | 25 | 73 | 23 | 65 |
| bd | 21 | 56 | 19 | 48 |
| **Total** | **184** | **490** | **158** | **415** |

**Comment ces chiffres ont été obtenus** :
1. `count_books` a renvoyé `count = 184` (taille du catalogue, archivés inclus) — contrôle indépendant.
2. J'ai parcouru **tout** le catalogue avec `list_books(include_archived: true)`, en suivant le champ `next` jusqu'à la **première page vide** (`items: []`), conformément au piège 6 du skill (le serveur renvoie toujours un `next`, y compris sur la dernière page et sur la page vide). Cela donne 184 titres.
3. J'ai ensuite appelé `list_books` une fois **par genre** avec `include_archived: true` pour obtenir la répartition. La somme des 6 genres (38+35+34+31+25+21) redonne exactement 184.
4. Pour chaque titre j'ai additionné le champ `copies` (exemplaires par titre) et distingué `archived: true` (retiré de la circulation) de `archived: false`. Totaux : 490 exemplaires, dont 75 dans des titres archivés.

**Interprétation retenue** : la question « nombre exact d'ouvrages détenus » est ambiguë entre *titres* et *exemplaires*. J'ai donc présenté les deux, ainsi que la distinction actifs/archivés (piège de la suppression logique) plutôt qu'un chiffre unique. Si le conseil entend « ouvrages disponibles à la lecture », le chiffre à retenir est **158 titres / 415 exemplaires** ; s'il entend « patrimoine totalement détenu », c'est **184 titres / 490 exemplaires**.
