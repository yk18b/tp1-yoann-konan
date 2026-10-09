# Pièges de l'API bibliothèque

Chaque entrée : outil concerné, ce qu'on observe, ce que fait réellement le serveur, règle à appliquer, preuve.

---

## Pièges confirmés

### 1. `delete_loan` — la suppression n'efface rien
- **Outil** : `bibliotheque_delete_loan`
- **Observé** : chaque appel répond `{"ok": true, "deleted": true, "loan_id": "..."}` et l'emprunt disparaît de `list_loans`.
- **Réalité** : le serveur n'efface pas l'emprunt, il le passe en `archived: true`. Avec `list_loans` + `include_archived: true`, les emprunts « supprimés » sont toujours présents (138 emprunts au total, dont 6 archivés après M4).
- **Écart avec la description** : l'outil annonce « Deletes a loan from the register » et répond `deleted: true`, alors que l'emprunt reste dans le registre.
- **Règle** : ne jamais considérer `deleted: true` comme une preuve. Vérifier avec `list_loans` en passant `include_archived: true` (la vue par défaut masque les archivés). Signaler au demandeur qu'un effacement réel est impossible via l'API exposée.
- **Preuve** : `reponses/mission-4.md` (appels 3 à 10) et `reponses/mission-4-verification.md` (sections 1 et 2) ; captures `captures/m4-piege-delete-archive.png`.

### 2. `create_loan` — champ obligatoire absent du schéma
- **Outil** : `bibliotheque_create_loan`
- **Observé** : un appel conforme au schéma (`member_id` + `book_id`, les deux seuls champs listés et requis) échoue avec `{"ok": false, "error": "missing field"}`.
- **Réalité** : le serveur exige un troisième champ, `desk_code`, qui n'apparaît pas dans le schéma ; le message d'erreur ne nomme pas le champ manquant.
- **Règle** : toujours fournir `desk_code` à `create_loan`. Valeurs observées dans le registre : `A1`, `B2`, `C3`.
- **Preuve** : `reponses/mission-3.md` (schéma publié, appel 1 en échec, appel 2 en succès) ; capture `captures/m3-piege-desk-code.png`.

### 3. `get_member_fees` — unités non documentées
- **Outil** : `bibliotheque_get_member_fees`
- **Observé** : `overdue_duration: 4296`, `late_fee_per_day: 15`, `balance_due: 26.85` (MB-225).
- **Réalité** : `overdue_duration` est en **heures** (4296 h = 179 jours), `late_fee_per_day` est en **centimes** (15 = 0,15 €), `balance_due` est en **euros** (179 × 0,15 = 26,85). Aucune de ces unités n'est indiquée dans la description ni dans le schéma.
- **Règle** : diviser `overdue_duration` par 24 pour obtenir des jours ; ne jamais multiplier `late_fee_per_day` par des jours pour obtenir des euros sans diviser par 100 ; utiliser `balance_due` comme montant dû.
- **Preuve** : `reponses/mission-2.md` (appels 6 à 8 : MB-225, MB-200, MB-227) et `reponses/mission-3-verification.md` (section 4 : MB-214, 2664 h = 111 j, 16,65 €).

### 4. Date de référence du serveur figée
- **Outils** : `bibliotheque_get_member_fees`, `bibliotheque_create_loan`
- **Observé** : les retards et les nouveaux emprunts ne correspondent pas à la date réelle (9 octobre 2026).
- **Réalité** : le serveur calcule comme si l'on était le **2026-10-06 09:00 UTC** : retards de MB-200 (5 j), MB-227 (1 j), MB-225 (179 j), MB-214 (111 j) tous cohérents avec cette date ; l'emprunt LN-5137 créé le 9 octobre a `started_at` = 2026-10-06 09:00. Ce n'est documenté nulle part.
- **Règle** : pour tout calcul de retard, utiliser la date de référence du serveur (2026-10-06 09:00 UTC), pas l'horloge locale ; la recalibrer si besoin avec `get_member_fees` sur un adhérent dont on connaît l'échéance.
- **Preuve** : `reponses/mission-2.md` (appels 6 à 8), `reponses/mission-3.md` (appel 2, `started_at: 1791277200`), `reponses/mission-3-verification.md` (section 4).

### 5. Absence d'e-mail représentée de deux façons
- **Outils** : `bibliotheque_list_members`, `bibliotheque_get_member`
- **Observé** : certains adhérents ont `"email": null`, d'autres n'ont pas du tout de champ `email`.
- **Réalité** : sur 46 adhérents, 31 ont un e-mail, 10 ont `"email": null` (MB-203, 205, 209, 212, 223, 226, 229, 232, 234, 244) et 5 n'ont pas de clé `email` (MB-206, 211, 222, 235, 241). Les deux outils sont concordants. Rien n'est documenté.
- **Règle** : considérer comme « sans e-mail » à la fois la valeur `null` et l'absence de clé, mais les compter séparément quand on doit distinguer les cas. Ne pas tester seulement l'un des deux.
- **Preuve** : `reponses/mission-5.md` (appel 4) et `reponses/mission-5-verification.md` (sections 2 et 4).

### 6. Pagination — `next` n'est jamais vide
- **Outils** : `bibliotheque_list_books`, `bibliotheque_list_loans`, `bibliotheque_list_members`
- **Observé** : la dernière page renvoie `"items": []` mais toujours un curseur (`"next": "MjUw"`, `"ODA="`…) ; une requête filtrée qui renvoie moins de `limit` résultats renvoie quand même `next`.
- **Réalité** : le serveur fournit toujours un `next`, même quand il n'y a plus de données. Le schéma décrit `start_key` comme une clé opaque sans préciser comment détecter la fin.
- **Règle** : suivre `next` jusqu'à obtenir une page avec `items` vide ; ne pas s'arrêter à la première page, ne pas attendre un `next` absent.
- **Preuve** : `reponses/mission-1.md` (appel 6 : `items: []`, `next: "MjUw"`), `reponses/mission-5-verification.md` (section 3, page 4).

### 7. `limit` plafonné silencieusement à 50
- **Outils** : `bibliotheque_list_loans` (et probablement les autres outils paginés).
- **Observé** : un appel avec `limit: 100` renvoie 50 éléments et un `next`.
- **Réalité serveur** : le serveur plafonne la taille de page à 50 sans erreur ni avertissement ; le schéma n'indique aucun maximum.
- **Règle** : ne jamais supposer qu'une page contient `limit` éléments ; toujours suivre `next` jusqu'à une page vide, quel que soit le `limit` demandé.
- **Preuve** : `reponses/apres-skill/mission-2.md`, appels 2 à 5 (`limit: 100` → pages de 50, 50, 38, puis vide).

---

## Non-pièges (comportement documenté — ne pas signaler)

- **`list_books` exclut les archivés par défaut** : écrit dans sa description (« Archived copies are excluded unless include_archived is true »). L'écart entre `count_books` (184) et `list_books` par défaut (158) s'explique entièrement : 158 + 26 archivés = 184.
- **`list_loans`** : les paramètres `include_archived`, `status` (« open » ou « returned ») et `member_id` sont décrits dans le schéma ; `limit` vaut 20 par défaut (indiqué).
- **`list_members`** : `active_only` est décrit dans le schéma ; `limit` vaut 20 par défaut (indiqué).
- **`get_member` attend `memberId` (camelCase)** alors que les autres outils utilisent `member_id` : c'est incohérent mais déclaré dans le schéma, et un mauvais nom provoque une erreur explicite (`invalid request`), pas une réponse silencieusement fausse.
- **`get_member` ne contient pas les emprunts** : la fiche renvoie identité, date d'inscription, statut et e-mail ; pour les emprunts d'un adhérent, utiliser `list_loans` avec `member_id`.

---

## Anomalies de données (pas des pièges de l'API, à mentionner dans le rapport)

- **Adresses e-mail partagées** entre adhérents : `yanis.robin@example.org` (MB-200 et MB-237, tous deux en retard), `sarah.perrin@example.org` (MB-224 et MB-239), `lea.perrin@example.org` (MB-215 et MB-240).
- **Formats de dates mélangés** : timestamps Unix pour les emprunts, ISO 8601 pour les livres, `JJ/MM/AAAA` pour `joined_at` des adhérents.
- **Curseur « opaque » lisible** : `next` est simplement l'offset encodé en base64 (`NTA=` → 50, `MjA=` → 20).


