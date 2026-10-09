# Mission M5 — La relance

**Demande (brief M5)** : « Prépare la campagne de relance : la liste des adhérents ayant au moins un emprunt en retard et joignables par mail, et le nombre de ceux qui ne sont pas joignables, en distinguant les cas. »

Skill chargé : `bibliotheque-api` (piège 4 : date de référence serveur figée ; piège 5 : absence d'e-mail sous deux formes — `null` vs clé absente ; piège 6 : pagination ; point « adhérent inactif disposant d'un e-mail »).

---

## 1. Appels passés

| # | Outil | Paramètres |
|---|-------|------------|
| 1 | `bibliotheque_get_mission` | `{ "mission_id": "M5" }` |
| 2 | `bibliotheque_list_members` | `{ "limit": 100 }` |
| 3 | `bibliotheque_list_members` | `{ "limit": 100, "start_key": "NTA=" }` (fin de liste) |
| 4 | `bibliotheque_list_loans` | `{ "status": "open", "limit": 100 }` |
| 5 | `bibliotheque_list_loans` | `{ "status": "open", "limit": 100, "start_key": "NTA=" }` |
| 6 | `bibliotheque_get_member_fees` | `{ "member_id": "MB-225" }` (recalibrage de la date de référence) |

Pagination (piège 6) : `list_members` s'arrête à une page vide après 46 adhérents ; `list_loans(status: "open")` s'arrête après 55 emprunts ouverts (50 + 5).

---

## 2. Réponses brutes

### 2.1 `get_mission`

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M5",
    "title": "La relance",
    "brief": "Prépare la campagne de relance : la liste des adhérents ayant au moins un emprunt en retard et joignables par mail, et le nombre de ceux qui ne sont pas joignables, en distinguant les cas."
  }
}
```

### 2.2 `list_members` — page 1 (46 adhérents ; clé `email` omise quand absente)

```json
{
"items": [
{"member_id":"MB-200","first_name":"Yanis","last_name":"Robin","joined_at":"05/12/2024","active":true,"email":"yanis.robin@example.org"},
{"member_id":"MB-201","first_name":"Mehdi","last_name":"Moreau","joined_at":"30/08/2026","active":true,"email":"mehdi.moreau@example.org"},
{"member_id":"MB-202","first_name":"Nora","last_name":"Noël","joined_at":"13/01/2024","active":true,"email":"nora.noel@example.org"},
{"member_id":"MB-203","first_name":"Antoine","last_name":"Mercier","joined_at":"21/02/2022","active":true,"email":null},
{"member_id":"MB-204","first_name":"Léa","last_name":"Guerin","joined_at":"29/08/2025","active":true,"email":"lea.guerin@example.org"},
{"member_id":"MB-205","first_name":"Lucas","last_name":"Marchand","joined_at":"13/04/2022","active":false,"email":null},
{"member_id":"MB-206","first_name":"Emma","last_name":"Barbier","joined_at":"25/02/2023","active":true},
{"member_id":"MB-207","first_name":"Mehdi","last_name":"Blanc","joined_at":"23/12/2025","active":true,"email":"mehdi.blanc@example.org"},
{"member_id":"MB-208","first_name":"Paul","last_name":"Leroy","joined_at":"09/04/2022","active":true,"email":"paul.leroy@example.org"},
{"member_id":"MB-209","first_name":"Emma","last_name":"Girard","joined_at":"21/08/2025","active":true,"email":null},
{"member_id":"MB-210","first_name":"Lucas","last_name":"Blanc","joined_at":"07/03/2023","active":true,"email":"lucas.blanc@example.org"},
{"member_id":"MB-211","first_name":"Paul","last_name":"Bernard","joined_at":"03/03/2024","active":true},
{"member_id":"MB-212","first_name":"Manon","last_name":"Fontaine","joined_at":"01/03/2026","active":true,"email":null},
{"member_id":"MB-213","first_name":"Léa","last_name":"Lefèvre","joined_at":"02/03/2026","active":true,"email":"lea.lefevre@example.org"},
{"member_id":"MB-214","first_name":"Chloé","last_name":"Roux","joined_at":"09/07/2023","active":true,"email":"chloe.roux@example.org"},
{"member_id":"MB-215","first_name":"Léa","last_name":"Perrin","joined_at":"19/03/2023","active":false,"email":"lea.perrin@example.org"},
{"member_id":"MB-216","first_name":"Thomas","last_name":"Dumas","joined_at":"15/08/2026","active":true,"email":"thomas.dumas@example.org"},
{"member_id":"MB-217","first_name":"Hugo","last_name":"Barbier","joined_at":"13/04/2026","active":true,"email":"hugo.barbier@example.org"},
{"member_id":"MB-218","first_name":"Paul","last_name":"Roux","joined_at":"11/08/2025","active":true,"email":"paul.roux@example.org"},
{"member_id":"MB-219","first_name":"Sarah","last_name":"Guerin","joined_at":"10/09/2025","active":false,"email":"sarah.guerin@example.org"},
{"member_id":"MB-220","first_name":"Hugo","last_name":"Leroy","joined_at":"14/07/2025","active":true,"email":"hugo.leroy@example.org"},
{"member_id":"MB-221","first_name":"Paul","last_name":"Dumas","joined_at":"16/04/2023","active":true,"email":"paul.dumas@example.org"},
{"member_id":"MB-222","first_name":"Mehdi","last_name":"Moreau","joined_at":"13/04/2022","active":true},
{"member_id":"MB-223","first_name":"Nora","last_name":"Marchand","joined_at":"05/02/2024","active":true,"email":null},
{"member_id":"MB-224","first_name":"Sarah","last_name":"Perrin","joined_at":"08/09/2026","active":true,"email":"sarah.perrin@example.org"},
{"member_id":"MB-225","first_name":"Paul","last_name":"Blanc","joined_at":"17/04/2024","active":true,"email":"paul.blanc@example.org"},
{"member_id":"MB-226","first_name":"Lucas","last_name":"Moreau","joined_at":"18/01/2025","active":true,"email":null},
{"member_id":"MB-227","first_name":"Hugo","last_name":"Perrin","joined_at":"07/04/2024","active":true,"email":"hugo.perrin@example.org"},
{"member_id":"MB-228","first_name":"Lucas","last_name":"Fontaine","joined_at":"18/07/2024","active":true,"email":"lucas.fontaine@example.org"},
{"member_id":"MB-229","first_name":"Camille","last_name":"Guerin","joined_at":"04/04/2023","active":true,"email":null},
{"member_id":"MB-230","first_name":"Nora","last_name":"Roux","joined_at":"16/01/2023","active":true,"email":"nora.roux@example.org"},
{"member_id":"MB-231","first_name":"Karim","last_name":"Guerin","joined_at":"28/02/2026","active":true,"email":"karim.guerin@example.org"},
{"member_id":"MB-232","first_name":"Paul","last_name":"Barbier","joined_at":"18/02/2024","active":true,"email":null},
{"member_id":"MB-233","first_name":"Nora","last_name":"Marchand","joined_at":"01/01/2025","active":true,"email":"nora.marchand@example.org"},
{"member_id":"MB-234","first_name":"Sarah","last_name":"Moreau","joined_at":"24/10/2023","active":true,"email":null},
{"member_id":"MB-235","first_name":"Léa","last_name":"Mercier","joined_at":"19/05/2026","active":true},
{"member_id":"MB-236","first_name":"Julien","last_name":"Lefèvre","joined_at":"07/11/2022","active":true,"email":"julien.lefevre@example.org"},
{"member_id":"MB-237","first_name":"Yanis","last_name":"Robin","joined_at":"27/10/2025","active":true,"email":"yanis.robin@example.org"},
{"member_id":"MB-238","first_name":"Paul","last_name":"Moreau","joined_at":"30/06/2025","active":true,"email":"paul.moreau@example.org"},
{"member_id":"MB-239","first_name":"Sarah","last_name":"Perrin","joined_at":"15/03/2026","active":true,"email":"sarah.perrin@example.org"},
{"member_id":"MB-240","first_name":"Léa","last_name":"Perrin","joined_at":"05/09/2024","active":true,"email":"lea.perrin@example.org"},
{"member_id":"MB-241","first_name":"Emma","last_name":"Lefèvre","joined_at":"04/04/2026","active":true},
{"member_id":"MB-242","first_name":"Paul","last_name":"Guerin","joined_at":"18/03/2026","active":true,"email":"paul.guerin@example.org"},
{"member_id":"MB-243","first_name":"Léa","last_name":"Leroy","joined_at":"13/06/2026","active":true,"email":"lea.leroy@example.org"},
{"member_id":"MB-244","first_name":"Hugo","last_name":"Noël","joined_at":"14/06/2025","active":true,"email":null},
{"member_id":"MB-245","first_name":"Fatou","last_name":"Roux","joined_at":"06/06/2025","active":true,"email":"fatou.roux@example.org"}
],
"next": "NTA="
}
```

Page 2 (`start_key: "NTA="`) : `{ "ok": true, "items": [], "next": "MTAw" }` → **46 adhérents** au total.

### 2.3 `list_loans(status: "open")` — page 1

> Champs pertinents seuls affichés (`loan_id`, `member_id`, `due_at`) pour la lisibilité ; les objets complets (`book_id`, `started_at`, `status`, `archived`, `desk_code`) sont dans `mission-2.md`.

```json
{
"items": [
{"loan_id":"LN-5004","member_id":"MB-203","due_at":1791968400},
{"loan_id":"LN-5006","member_id":"MB-216","due_at":1781514000},
{"loan_id":"LN-5008","member_id":"MB-201","due_at":1789722000},
{"loan_id":"LN-5009","member_id":"MB-245","due_at":1792832400},
{"loan_id":"LN-5012","member_id":"MB-235","due_at":1781946000},
{"loan_id":"LN-5013","member_id":"MB-234","due_at":1777453200},
{"loan_id":"LN-5014","member_id":"MB-239","due_at":1784538000},
{"loan_id":"LN-5015","member_id":"MB-221","due_at":1777885200},
{"loan_id":"LN-5016","member_id":"MB-234","due_at":1782637200},
{"loan_id":"LN-5020","member_id":"MB-216","due_at":1776762000},
{"loan_id":"LN-5022","member_id":"MB-234","due_at":1791190800},
{"loan_id":"LN-5023","member_id":"MB-237","due_at":1778058000},
{"loan_id":"LN-5024","member_id":"MB-232","due_at":1776675600},
{"loan_id":"LN-5025","member_id":"MB-212","due_at":1787648400},
{"loan_id":"LN-5026","member_id":"MB-242","due_at":1785920400},
{"loan_id":"LN-5034","member_id":"MB-200","due_at":1790845200},
{"loan_id":"LN-5036","member_id":"MB-214","due_at":1781686800},
{"loan_id":"LN-5045","member_id":"MB-204","due_at":1784106000},
{"loan_id":"LN-5054","member_id":"MB-232","due_at":1785315600},
{"loan_id":"LN-5055","member_id":"MB-219","due_at":1793178000},
{"loan_id":"LN-5057","member_id":"MB-239","due_at":1784624400},
{"loan_id":"LN-5060","member_id":"MB-202","due_at":1781341200},
{"loan_id":"LN-5065","member_id":"MB-210","due_at":1788598800},
{"loan_id":"LN-5066","member_id":"MB-231","due_at":1780477200},
{"loan_id":"LN-5068","member_id":"MB-226","due_at":1781859600},
{"loan_id":"LN-5074","member_id":"MB-230","due_at":1783501200},
{"loan_id":"LN-5075","member_id":"MB-210","due_at":1785661200},
{"loan_id":"LN-5077","member_id":"MB-234","due_at":1778144400},
{"loan_id":"LN-5078","member_id":"MB-209","due_at":1792659600},
{"loan_id":"LN-5082","member_id":"MB-227","due_at":1791190800},
{"loan_id":"LN-5084","member_id":"MB-208","due_at":1785488400},
{"loan_id":"LN-5085","member_id":"MB-203","due_at":1782723600},
{"loan_id":"LN-5086","member_id":"MB-241","due_at":1781254800},
{"loan_id":"LN-5088","member_id":"MB-219","due_at":1782810000},
{"loan_id":"LN-5091","member_id":"MB-210","due_at":1786179600},
{"loan_id":"LN-5094","member_id":"MB-230","due_at":1790067600},
{"loan_id":"LN-5097","member_id":"MB-240","due_at":1790154000},
{"loan_id":"LN-5098","member_id":"MB-206","due_at":1780304400},
{"loan_id":"LN-5099","member_id":"MB-210","due_at":1782550800},
{"loan_id":"LN-5100","member_id":"MB-228","due_at":1785315600},
{"loan_id":"LN-5106","member_id":"MB-225","due_at":1775811600},
{"loan_id":"LN-5107","member_id":"MB-241","due_at":1779354000},
{"loan_id":"LN-5109","member_id":"MB-221","due_at":1783414800},
{"loan_id":"LN-5111","member_id":"MB-221","due_at":1777971600},
{"loan_id":"LN-5114","member_id":"MB-239","due_at":1790326800},
{"loan_id":"LN-5115","member_id":"MB-239","due_at":1777712400},
{"loan_id":"LN-5118","member_id":"MB-242","due_at":1777366800},
{"loan_id":"LN-5121","member_id":"MB-228","due_at":1784797200},
{"loan_id":"LN-5125","member_id":"MB-219","due_at":1788166800},
{"loan_id":"LN-5127","member_id":"MB-237","due_at":1777366800}
],
"next": "NTA="
}
```

### 2.4 `list_loans(status: "open")` — page 2 (`start_key: "NTA="`)

```json
{
"items": [
{"loan_id":"LN-5129","member_id":"MB-207","due_at":1777539600},
{"loan_id":"LN-5131","member_id":"MB-217","due_at":1787734800},
{"loan_id":"LN-5133","member_id":"MB-239","due_at":1785661200},
{"loan_id":"LN-5137","member_id":"MB-214","due_at":1793091600},
{"loan_id":"LN-5138","member_id":"MB-214","due_at":1793091600}
],
"next": "MTAw"
}
```

Total emprunts ouverts = 50 + 5 = **55**.

### 2.5 `get_member_fees` (MB-225) — recalibrage de la date de référence

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

MB-225 n'a qu'un emprunt ouvert (`LN-5106`, `due_at = 1775811600`), donc :
`date_ref = 1775811600 + 4296 × 3600 = 1791277200` → **2026-10-06 09:00 UTC** (date de référence du serveur, piège 4).

---

## 3. Dépouillement

**Critère de retard** : `status = "open"` **et** `due_at < date_ref` (1791277200).

- 55 emprunts ouverts ; **29 adhérents** ont au moins un emprunt en retard.
- Correspondance adhérents/e-mails : 46 fiches. Absence d'e-mail sous deux formes (piège 5) : `email: null` **ou** clé `email` absente.

### 3.1 Joignables par mail (e-mail présent)

| Adhérent | Actif | Emprunts en retard | E-mail |
|---|---|---|---|
| MB-200 Yanis Robin | oui | 1 | yanis.robin@example.org |
| MB-201 Mehdi Moreau | oui | 1 | mehdi.moreau@example.org |
| MB-202 Nora Noël | oui | 1 | nora.noel@example.org |
| MB-204 Léa Guerin | oui | 1 | lea.guerin@example.org |
| MB-207 Mehdi Blanc | oui | 1 | mehdi.blanc@example.org |
| MB-208 Paul Leroy | oui | 1 | paul.leroy@example.org |
| MB-210 Lucas Blanc | oui | 4 | lucas.blanc@example.org |
| MB-214 Chloé Roux | oui | 1 | chloe.roux@example.org |
| MB-216 Thomas Dumas | oui | 2 | thomas.dumas@example.org |
| MB-217 Hugo Barbier | oui | 1 | hugo.barbier@example.org |
| **MB-219 Sarah Guerin** | **non** | 2 | sarah.guerin@example.org |
| MB-221 Paul Dumas | oui | 3 | paul.dumas@example.org |
| MB-225 Paul Blanc | oui | 1 | paul.blanc@example.org |
| MB-227 Hugo Perrin | oui | 1 | hugo.perrin@example.org |
| MB-228 Lucas Fontaine | oui | 2 | lucas.fontaine@example.org |
| MB-230 Nora Roux | oui | 2 | nora.roux@example.org |
| MB-231 Karim Guerin | oui | 1 | karim.guerin@example.org |
| MB-237 Yanis Robin | oui | 2 | yanis.robin@example.org |
| MB-239 Sarah Perrin | oui | 5 | sarah.perrin@example.org |
| MB-240 Léa Perrin | oui | 1 | lea.perrin@example.org |
| MB-242 Paul Guerin | oui | 2 | paul.guerin@example.org |

### 3.2 Non joignables (aucun e-mail), cas distingués

| Adhérent | Actif | Emprunts en retard | Forme de l'absence |
|---|---|---|---|
| MB-203 Antoine Mercier | oui | 1 | `email: null` |
| MB-212 Manon Fontaine | oui | 1 | `email: null` |
| MB-226 Lucas Moreau | oui | 1 | `email: null` |
| MB-232 Paul Barbier | oui | 2 | `email: null` |
| MB-234 Sarah Moreau | oui | 4 | `email: null` |
| MB-206 Emma Barbier | oui | 1 | **clé `email` absente** |
| MB-235 Léa Mercier | oui | 1 | **clé `email` absente** |
| MB-241 Emma Lefèvre | oui | 2 | **clé `email` absente** |

### 3.3 Totaux

| Catégorie | Nombre |
|---|---|
| Adhérents avec ≥ 1 emprunt en retard | **29** |
| Joignables par mail (au moins un e-mail) | **21** |
| ↳ dont actifs uniquement | 20 |
| ↳ dont inactifs (MB-219) | 1 |
| Non joignables (aucun e-mail) | **8** |
| ↳ `email: null` | 5 |
| ↳ clé `email` absente | 3 |
| ↳ actifs / inactifs parmi les non joignables | 8 / 0 |

---

## 4. Conclusion

**Liste des adhérents en retard ET joignables par mail (21)** — voir tableau 3.1.
Cible d'envoi : MB-200, MB-201, MB-202, MB-204, MB-207, MB-208, MB-210, MB-214, MB-216, MB-217, **MB-219**, MB-221, MB-225, MB-227, MB-228, MB-230, MB-231, MB-237, MB-239, MB-240, MB-242.

**Non joignables : 8 adhérents**, tous actifs, en distinguant les deux formes d'absence :
- **`email: null` (5)** : MB-203, MB-212, MB-226, MB-232, MB-234 ;
- **clé `email` absente (3)** : MB-206, MB-235, MB-241.

**Comment ces chiffres ont été obtenus :**
1. J'ai parcouru **tous** les adhérents (`list_members`) jusqu'à la page vide : 46 fiches, en relevant `active` et l'e-mail (en traitant `null` **et** l'absence de clé comme « pas d'e-mail »).
2. J'ai parcouru **tous** les emprunts ouverts (`list_loans(status: "open")`) jusqu'à la page vide : 55 emprunts.
3. J'ai recalibré la **date de référence du serveur** via `get_member_fees(MB-225)` : `date_ref = 1791277200` = **2026-10-06 09:00 UTC**. Un emprunt est en retard si `due_at < date_ref`.
4. J'ai regroupé les emprunts en retard par `member_id` (29 adhérents), puis croisé avec la présence d'e-mail.
5. **Totaux** : **21 joignables** (20 actifs + 1 inactif, MB-219) et **8 non joignables** (5 `null` + 3 clés absentes).

**Points d'interprétation / à signaler :**
- **Adhérent inactif joignable (MB-219 Sarah Guerin, 2 retards)** : je l'ai **compté comme joignable** (e-mail présent) → total **21**. Si la campagne ne cible que les adhérents actifs, le total tombe à **20** (MB-219 exclu). Les 8 non joignables sont, eux, tous actifs.
- **Anomalie — adresses partagées** : MB-200 et MB-237 partagent `yanis.robin@example.org` ; MB-224 et MB-239 partagent `sarah.perrin@example.org` ; MB-215 et MB-240 partagent `lea.perrin@example.org`. Lors d'un envoi, plusieurs adhérents peuvent être atteints à la même adresse (à ne pas dédoublonner sans arbitrage).
- Aucun cas de clé `email` renseignée mais vide ; l'absence est toujours soit `null`, soit une clé manquante (piège 5).
