# Mission M2 — Le retardataire

**Demande (brief M2)** : « Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent. »

Skill chargé : `bibliotheque-api` (pagination, date de référence du serveur figée, unités des frais).

---

## 1. Appels passés

| # | Outil | Paramètres |
|---|-------|------------|
| 1 | `bibliotheque_get_mission` | `{ "mission_id": "M2" }` |
| 2 | `bibliotheque_list_loans` | `{ "include_archived": true, "limit": 100 }` |
| 3 | `bibliotheque_list_loans` | `{ "include_archived": true, "limit": 100, "start_key": "NTA=" }` |
| 4 | `bibliotheque_list_loans` | `{ "include_archived": true, "limit": 100, "start_key": "MTAw" }` |
| 5 | `bibliotheque_list_loans` | `{ "include_archived": true, "limit": 100, "start_key": "MTUw" }` (page de contrôle de fin) |
| 6 | `bibliotheque_get_member_fees` | `{ "member_id": "MB-225" }` |
| 7 | `bibliotheque_get_member` | `{ "memberId": "MB-225" }` |
| 8 | `bibliotheque_get_book` | `{ "book_id": "BK-1075" }` |

**Note** : j'ai inclus les emprunts archivés (`include_archived: true`) pour ne manquer aucun enregistrement (piège 1 : la suppression est logique). Le catalogue d'emprunts contient **138** lignes (50 + 50 + 38).

---

## 2. Réponses brutes

### 2.1 `get_mission`

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M2",
    "title": "Le retardataire",
    "brief": "Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent."
  }
}
```

### 2.2 `list_loans` — page 1 (`limit: 100`, `include_archived: true`)

```json
{
"items": [
{"loan_id":"LN-5000","book_id":"BK-1030","member_id":"MB-234","started_at":1777798800,"due_at":1779008400,"returned_at":1778835600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5001","book_id":"BK-1076","member_id":"MB-201","started_at":1779786000,"due_at":1781600400,"returned_at":1781254800,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5002","book_id":"BK-1055","member_id":"MB-235","started_at":1780131600,"due_at":1781341200,"returned_at":1781341200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5003","book_id":"BK-1112","member_id":"MB-213","started_at":1790672400,"due_at":1791882000,"returned_at":1791104400,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5004","book_id":"BK-1090","member_id":"MB-203","started_at":1790154000,"due_at":1791968400,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5005","book_id":"BK-1120","member_id":"MB-224","started_at":1789635600,"due_at":1790845200,"returned_at":1790067600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5006","book_id":"BK-1133","member_id":"MB-216","started_at":1779094800,"due_at":1781514000,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5007","book_id":"BK-1054","member_id":"MB-217","started_at":1775984400,"due_at":1777798800,"returned_at":1777366800,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5008","book_id":"BK-1040","member_id":"MB-201","started_at":1787907600,"due_at":1789722000,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5009","book_id":"BK-1159","member_id":"MB-245","started_at":1790413200,"due_at":1792832400,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5010","book_id":"BK-1148","member_id":"MB-211","started_at":1782205200,"due_at":1784624400,"returned_at":1784019600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5011","book_id":"BK-1056","member_id":"MB-232","started_at":1776502800,"due_at":1778317200,"returned_at":1778058000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5012","book_id":"BK-1083","member_id":"MB-235","started_at":1780736400,"due_at":1781946000,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5013","book_id":"BK-1027","member_id":"MB-234","started_at":1776243600,"due_at":1777453200,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5014","book_id":"BK-1175","member_id":"MB-239","started_at":1782118800,"due_at":1784538000,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5015","book_id":"BK-1103","member_id":"MB-221","started_at":1776675600,"due_at":1777885200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5016","book_id":"BK-1135","member_id":"MB-234","started_at":1780822800,"due_at":1782637200,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5017","book_id":"BK-1121","member_id":"MB-230","started_at":1778576400,"due_at":1779786000,"returned_at":1779267600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5018","book_id":"BK-1032","member_id":"MB-217","started_at":1783933200,"due_at":1785142800,"returned_at":1784797200,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5019","book_id":"BK-1142","member_id":"MB-245","started_at":1788166800,"due_at":1789376400,"returned_at":1788685200,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5020","book_id":"BK-1016","member_id":"MB-216","started_at":1774947600,"due_at":1776762000,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5021","book_id":"BK-1053","member_id":"MB-231","started_at":1786266000,"due_at":1788080400,"returned_at":1787821200,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5022","book_id":"BK-1112","member_id":"MB-234","started_at":1789981200,"due_at":1791190800,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5023","book_id":"BK-1035","member_id":"MB-237","started_at":1776243600,"due_at":1778058000,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5024","book_id":"BK-1012","member_id":"MB-232","started_at":1774861200,"due_at":1776675600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5025","book_id":"BK-1151","member_id":"MB-212","started_at":1785834000,"due_at":1787648400,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5026","book_id":"BK-1053","member_id":"MB-242","started_at":1784106000,"due_at":1785920400,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5027","book_id":"BK-1182","member_id":"MB-222","started_at":1790758800,"due_at":1793178000,"returned_at":1792486800,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5028","book_id":"BK-1001","member_id":"MB-224","started_at":1790154000,"due_at":1792573200,"returned_at":1792486800,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5029","book_id":"BK-1088","member_id":"MB-216","started_at":1777626000,"due_at":1779440400,"returned_at":1779354000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5030","book_id":"BK-1095","member_id":"MB-233","started_at":1780995600,"due_at":1782810000,"returned_at":1782205200,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5031","book_id":"BK-1008","member_id":"MB-239","started_at":1784797200,"due_at":1786611600,"returned_at":1786093200,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5032","book_id":"BK-1110","member_id":"MB-227","started_at":1786957200,"due_at":1788166800,"returned_at":1788166800,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5033","book_id":"BK-1054","member_id":"MB-204","started_at":1789462800,"due_at":1791277200,"returned_at":1790931600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5034","book_id":"BK-1022","member_id":"MB-200","started_at":1788426000,"due_at":1790845200,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5035","book_id":"BK-1126","member_id":"MB-223","started_at":1783242000,"due_at":1785056400,"returned_at":1784710800,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5036","book_id":"BK-1126","member_id":"MB-214","started_at":1779872400,"due_at":1781686800,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5037","book_id":"BK-1061","member_id":"MB-236","started_at":1777107600,"due_at":1778317200,"returned_at":1778058000,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5038","book_id":"BK-1110","member_id":"MB-202","started_at":1785402000,"due_at":1786611600,"returned_at":1786611600,"status":"returned","archived":true,"desk_code":"C3"},
{"loan_id":"LN-5039","book_id":"BK-1159","member_id":"MB-202","started_at":1788771600,"due_at":1791190800,"returned_at":1790845200,"status":"returned","archived":true,"desk_code":"A1"},
{"loan_id":"LN-5040","book_id":"BK-1025","member_id":"MB-224","started_at":1781168400,"due_at":1783587600,"returned_at":1782896400,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5041","book_id":"BK-1160","member_id":"MB-232","started_at":1787994000,"due_at":1789808400,"returned_at":1789635600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5042","book_id":"BK-1166","member_id":"MB-222","started_at":1774861200,"due_at":1777280400,"returned_at":1777194000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5043","book_id":"BK-1167","member_id":"MB-215","started_at":1781773200,"due_at":1782982800,"returned_at":1782464400,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5044","book_id":"BK-1050","member_id":"MB-217","started_at":1783069200,"due_at":1784883600,"returned_at":1784451600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5045","book_id":"BK-1090","member_id":"MB-204","started_at":1782291600,"due_at":1784106000,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5046","book_id":"BK-1040","member_id":"MB-204","started_at":1781341200,"due_at":1783155600,"returned_at":1782723600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5047","book_id":"BK-1170","member_id":"MB-210","started_at":1776070800,"due_at":1777885200,"returned_at":1777626000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5048","book_id":"BK-1109","member_id":"MB-239","started_at":1780304400,"due_at":1782118800,"returned_at":1781427600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5049","book_id":"BK-1043","member_id":"MB-225","started_at":1786352400,"due_at":1787562000,"returned_at":1786870800,"status":"returned","archived":false,"desk_code":"B2"}
],
"next": "NTA="
}
```

### 2.3 `list_loans` — page 2 (`start_key: "NTA="`)

```json
{
"items": [
{"loan_id":"LN-5050","book_id":"BK-1046","member_id":"MB-218","started_at":1782032400,"due_at":1783846800,"returned_at":1783242000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5051","book_id":"BK-1011","member_id":"MB-230","started_at":1787216400,"due_at":1788426000,"returned_at":1788166800,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5052","book_id":"BK-1130","member_id":"MB-210","started_at":1788771600,"due_at":1791190800,"returned_at":1790586000,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5053","book_id":"BK-1073","member_id":"MB-232","started_at":1790758800,"due_at":1792573200,"returned_at":1792573200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5054","book_id":"BK-1151","member_id":"MB-232","started_at":1783501200,"due_at":1785315600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5055","book_id":"BK-1141","member_id":"MB-219","started_at":1790758800,"due_at":1793178000,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5056","book_id":"BK-1017","member_id":"MB-220","started_at":1785056400,"due_at":1787475600,"returned_at":1786957200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5057","book_id":"BK-1111","member_id":"MB-239","started_at":1783414800,"due_at":1784624400,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5058","book_id":"BK-1051","member_id":"MB-216","started_at":1790413200,"due_at":1792227600,"returned_at":1792141200,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5059","book_id":"BK-1077","member_id":"MB-242","started_at":1786611600,"due_at":1787821200,"returned_at":1787648400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5060","book_id":"BK-1115","member_id":"MB-202","started_at":1778922000,"due_at":1781341200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5061","book_id":"BK-1065","member_id":"MB-206","started_at":1778230800,"due_at":1780045200,"returned_at":1779786000,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5062","book_id":"BK-1025","member_id":"MB-202","started_at":1776589200,"due_at":1779008400,"returned_at":1778403600,"status":"returned","archived":true,"desk_code":"C3"},
{"loan_id":"LN-5063","book_id":"BK-1021","member_id":"MB-216","started_at":1784451600,"due_at":1785661200,"returned_at":1785488400,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5064","book_id":"BK-1167","member_id":"MB-210","started_at":1787734800,"due_at":1788944400,"returned_at":1788512400,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5065","book_id":"BK-1060","member_id":"MB-210","started_at":1786179600,"due_at":1788598800,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5066","book_id":"BK-1112","member_id":"MB-231","started_at":1779267600,"due_at":1780477200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5067","book_id":"BK-1056","member_id":"MB-209","started_at":1786352400,"due_at":1788166800,"returned_at":1787907600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5068","book_id":"BK-1097","member_id":"MB-226","started_at":1779440400,"due_at":1781859600,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5069","book_id":"BK-1023","member_id":"MB-203","started_at":1778230800,"due_at":1780045200,"returned_at":1779267600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5070","book_id":"BK-1128","member_id":"MB-225","started_at":1779699600,"due_at":1780909200,"returned_at":1780563600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5071","book_id":"BK-1075","member_id":"MB-237","started_at":1788858000,"due_at":1790067600,"returned_at":1789981200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5072","book_id":"BK-1028","member_id":"MB-201","started_at":1775984400,"due_at":1778403600,"returned_at":1778058000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5073","book_id":"BK-1000","member_id":"MB-240","started_at":1782291600,"due_at":1783501200,"returned_at":1783155600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5074","book_id":"BK-1180","member_id":"MB-230","started_at":1781686800,"due_at":1783501200,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5075","book_id":"BK-1128","member_id":"MB-210","started_at":1784451600,"due_at":1785661200,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5076","book_id":"BK-1053","member_id":"MB-237","started_at":1788858000,"due_at":1790672400,"returned_at":1789894800,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5077","book_id":"BK-1076","member_id":"MB-234","started_at":1776330000,"due_at":1778144400,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5078","book_id":"BK-1012","member_id":"MB-209","started_at":1790845200,"due_at":1792659600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5079","book_id":"BK-1160","member_id":"MB-222","started_at":1775034000,"due_at":1776848400,"returned_at":1776243600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5080","book_id":"BK-1142","member_id":"MB-244","started_at":1778922000,"due_at":1780131600,"returned_at":1779872400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5081","book_id":"BK-1072","member_id":"MB-237","started_at":1779699600,"due_at":1780909200,"returned_at":1780304400,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5082","book_id":"BK-1122","member_id":"MB-227","started_at":1788771600,"due_at":1791190800,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5083","book_id":"BK-1156","member_id":"MB-211","started_at":1777626000,"due_at":1780045200,"returned_at":1779354000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5084","book_id":"BK-1016","member_id":"MB-208","started_at":1783674000,"due_at":1785488400,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5085","book_id":"BK-1167","member_id":"MB-203","started_at":1781514000,"due_at":1782723600,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5086","book_id":"BK-1056","member_id":"MB-241","started_at":1779440400,"due_at":1781254800,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5087","book_id":"BK-1056","member_id":"MB-200","started_at":1788685200,"due_at":1790499600,"returned_at":1790240400,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5088","book_id":"BK-1013","member_id":"MB-219","started_at":1780995600,"due_at":1782810000,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5089","book_id":"BK-1057","member_id":"MB-235","started_at":1778317200,"due_at":1780736400,"returned_at":1780304400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5090","book_id":"BK-1091","member_id":"MB-238","started_at":1778403600,"due_at":1780218000,"returned_at":1780131600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5091","book_id":"BK-1099","member_id":"MB-210","started_at":1784365200,"due_at":1786179600,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5092","book_id":"BK-1015","member_id":"MB-216","started_at":1791277200,"due_at":1793696400,"returned_at":1793696400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5093","book_id":"BK-1176","member_id":"MB-218","started_at":1778662800,"due_at":1780477200,"returned_at":1779786000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5094","book_id":"BK-1132","member_id":"MB-230","started_at":1787648400,"due_at":1790067600,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5095","book_id":"BK-1060","member_id":"MB-202","started_at":1776070800,"due_at":1778490000,"returned_at":1777885200,"status":"returned","archived":true,"desk_code":"C3"},
{"loan_id":"LN-5096","book_id":"BK-1096","member_id":"MB-239","started_at":1780650000,"due_at":1782464400,"returned_at":1781859600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5097","book_id":"BK-1060","member_id":"MB-240","started_at":1787734800,"due_at":1790154000,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5098","book_id":"BK-1083","member_id":"MB-206","started_at":1779094800,"due_at":1780304400,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5099","book_id":"BK-1171","member_id":"MB-210","started_at":1780736400,"due_at":1782550800,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
],
"next": "MTAw"
}
```

### 2.4 `list_loans` — page 3 (`start_key: "MTAw"`)

```json
{
"items": [
{"loan_id":"LN-5100","book_id":"BK-1012","member_id":"MB-228","started_at":1783501200,"due_at":1785315600,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5101","book_id":"BK-1099","member_id":"MB-200","started_at":1778835600,"due_at":1780650000,"returned_at":1780477200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5102","book_id":"BK-1175","member_id":"MB-205","started_at":1786870800,"due_at":1789290000,"returned_at":1789203600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5103","book_id":"BK-1126","member_id":"MB-213","started_at":1787734800,"due_at":1789549200,"returned_at":1789462800,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5104","book_id":"BK-1126","member_id":"MB-213","started_at":1783846800,"due_at":1785661200,"returned_at":1784883600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5105","book_id":"BK-1123","member_id":"MB-225","started_at":1786438800,"due_at":1788858000,"returned_at":1788426000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5106","book_id":"BK-1075","member_id":"MB-225","started_at":1774602000,"due_at":1775811600,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5107","book_id":"BK-1005","member_id":"MB-241","started_at":1776934800,"due_at":1779354000,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5108","book_id":"BK-1070","member_id":"MB-203","started_at":1777366800,"due_at":1778576400,"returned_at":1777885200,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5109","book_id":"BK-1183","member_id":"MB-221","started_at":1782205200,"due_at":1783414800,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5110","book_id":"BK-1074","member_id":"MB-219","started_at":1780045200,"due_at":1781254800,"returned_at":1780995600,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5111","book_id":"BK-1027","member_id":"MB-221","started_at":1776762000,"due_at":1777971600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5112","book_id":"BK-1043","member_id":"MB-234","started_at":1785488400,"due_at":1786698000,"returned_at":1786611600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5113","book_id":"BK-1021","member_id":"MB-233","started_at":1786179600,"due_at":1787389200,"returned_at":1787302800,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5114","book_id":"BK-1030","member_id":"MB-239","started_at":1789117200,"due_at":1790326800,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5115","book_id":"BK-1057","member_id":"MB-239","started_at":1775293200,"due_at":1777712400,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5116","book_id":"BK-1044","member_id":"MB-244","started_at":1786179600,"due_at":1788598800,"returned_at":1788512400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5117","book_id":"BK-1039","member_id":"MB-228","started_at":1778576400,"due_at":1780390800,"returned_at":1780131600,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5118","book_id":"BK-1113","member_id":"MB-242","started_at":1775552400,"due_at":1777366800,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5119","book_id":"BK-1099","member_id":"MB-236","started_at":1789117200,"due_at":1790931600,"returned_at":1790845200,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5120","book_id":"BK-1040","member_id":"MB-202","started_at":1788858000,"due_at":1790672400,"returned_at":1790672400,"status":"returned","archived":true,"desk_code":"A1"},
{"loan_id":"LN-5121","book_id":"BK-1132","member_id":"MB-228","started_at":1782378000,"due_at":1784797200,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5122","book_id":"BK-1005","member_id":"MB-213","started_at":1776330000,"due_at":1778749200,"returned_at":1778490000,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5123","book_id":"BK-1034","member_id":"MB-237","started_at":1777798800,"due_at":1779613200,"returned_at":1778922000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5124","book_id":"BK-1161","member_id":"MB-212","started_at":1775120400,"due_at":1777539600,"returned_at":1777021200,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5125","book_id":"BK-1077","member_id":"MB-219","started_at":1786957200,"due_at":1788166800,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5126","book_id":"BK-1053","member_id":"MB-204","started_at":1786438800,"due_at":1788253200,"returned_at":1787734800,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5127","book_id":"BK-1016","member_id":"MB-237","started_at":1775552400,"due_at":1777366800,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5128","book_id":"BK-1016","member_id":"MB-225","started_at":1786438800,"due_at":1788253200,"returned_at":1787648400,"status":"returned","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5129","book_id":"BK-1020","member_id":"MB-207","started_at":1775725200,"due_at":1777539600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5130","book_id":"BK-1013","member_id":"MB-239","started_at":1780736400,"due_at":1782550800,"returned_at":1781946000,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5131","book_id":"BK-1164","member_id":"MB-217","started_at":1785920400,"due_at":1787734800,"returned_at":null,"status":"open","archived":false,"desk_code":"C3"},
{"loan_id":"LN-5132","book_id":"BK-1161","member_id":"MB-214","started_at":1776934800,"due_at":1779354000,"returned_at":1778662800,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5133","book_id":"BK-1100","member_id":"MB-239","started_at":1783242000,"due_at":1785661200,"returned_at":null,"status":"open","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5134","book_id":"BK-1050","member_id":"MB-202","started_at":1781427600,"due_at":1783242000,"returned_at":1782982800,"status":"returned","archived":true,"desk_code":"C3"},
{"loan_id":"LN-5135","book_id":"BK-1162","member_id":"MB-229","started_at":1778576400,"due_at":1780390800,"returned_at":1779786000,"status":"returned","archived":false,"desk_code":"A1"},
{"loan_id":"LN-5136","book_id":"BK-1129","member_id":"MB-230","started_at":1778749200,"due_at":1781168400,"returned_at":1780995600,"status":"returned","archived":false,"desk_code":"B2"},
{"loan_id":"LN-5137","book_id":"BK-1042","member_id":"MB-214","started_at":1791277200,"due_at":1793091600,"returned_at":null,"status":"open","archived":false,"desk_code":"A1"}
],
"next": "MTUw"
}
```

### 2.5 `list_loans` — page 4 (`start_key: "MTUw"`, contrôle de fin)

```json
{ "items": [], "next": "MjAw" }
```

Total emprunts = 50 + 50 + 38 = **138**. Page vide atteinte → fin de liste (piège 6).

### 2.6 `get_member_fees` (MB-225)

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

### 2.7 `get_member` (MB-225)

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

### 2.8 `get_book` (BK-1075)

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

---

## 3. Analyse

### 3.1 Date de référence du serveur (piège 4)

MB-225 n'a **qu'un seul** emprunt ouvert, `LN-5106`, dont on connaît l'échéance `due_at = 1775811600`. `get_member_fees` renvoie `overdue_duration = 4296` **heures**.

```
date_ref = due_at + overdue_duration × 3600
         = 1775811600 + 4296 × 3600
         = 1775811600 + 15465600
         = 1791277200  →  2026-10-06 09:00 UTC
```

La date de référence du serveur est donc **2026-10-06 09:00 UTC**, différente de l'horloge locale. Tous les calculs de retard ci-dessous l'utilisent.

### 3.2 Emprunt le plus en retard

Parmi les emprunts **ouverts** (`status: "open"`, `archived: false`), je recherche le `due_at` minimal (échéance la plus ancienne). Les trois échéances les plus anciennes sont :

| loan_id | member_id | book_id | due_at | date d'échéance | retard (jours) |
|---|---|---|---|---|---|
| **LN-5106** | **MB-225** | **BK-1075** | **1775811600** | 2026-04-10 09:00 UTC | **179** |
| LN-5024 | MB-232 | BK-1012 | 1776675600 | 2026-04-20 09:00 UTC | 169 |
| LN-5020 | MB-216 | BK-1016 | 1776762000 | 2026-04-21 09:00 UTC | 168 |

`LN-5106` est donc l'emprunt le plus en retard.

### 3.3 Calcul exact

```
retard = (date_ref − due_at) / 86400
       = (1791277200 − 1775811600) / 86400
       = 15465600 / 86400
       = 179 jours exactement
```

### 3.4 Montant dû (piège 3)

`get_member_fees(MB-225)` :
- `overdue_duration = 4296` **heures** = 4296 / 24 = **179 jours** ;
- `late_fee_per_day = 15` **centimes** = 0,15 € ;
- `balance_due = 26,85 €`.

Contrôle : 179 × 0,15 = **26,85 €** — cohérent. Comme MB-225 n'a qu'un emprunt ouvert, ce montant correspond intégralement à `LN-5106`.

---

## 4. Conclusion

**L'emprunt le plus en retard actuellement est `LN-5106`.**

| Élément | Valeur |
|---|---|
| Adhérent | **MB-225 — Paul Blanc** (adhérent actif, inscrit le 17/04/2024, `paul.blanc@example.org`) |
| Ouvrage | **BK-1075 — « Le Retour des autres »**, de Yanis Perrin (poésie) |
| Début d'emprunt | 2026-03-27 09:00 UTC (`started_at = 1774602000`) |
| Échéance | **2026-04-10 09:00 UTC** (`due_at = 1775811600`) |
| Date de référence serveur | 2026-10-06 09:00 UTC |
| **Retard exact** | **179 jours** (4296 heures) |
| **Montant dû** | **26,85 €** |

**Comment ces chiffres ont été obtenus :**
1. J'ai parcouru **tous** les emprunts avec `list_loans(include_archived: true)`, en suivant `next` jusqu'à la page vide (138 emprunts) — l'archivage est logique, il fallait tout voir.
2. J'ai isolé les emprunts `status: "open"` et `archived: false`, puis retenu celui dont le `due_at` est le plus petit : `LN-5106`.
3. J'ai recalibré la **date de référence du serveur** (distincte de l'horloge locale) grâce à `get_member_fees` sur MB-225, qui n'a qu'un seul emprunt ouvert : `date_ref = due_at + overdue_duration = 1791277200` (2026-10-06 09:00 UTC).
4. Retard = `(date_ref − due_at)/86400` = **179 jours**, confirmé par `overdue_duration` = 4296 h.
5. Montant dû = `balance_due` = **26,85 €** (à prendre tel quel : `overdue_duration` est en heures, `late_fee_per_day` en centimes).
