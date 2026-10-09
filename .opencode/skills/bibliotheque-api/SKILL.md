---
name: bibliotheque-api
description: "Règles de travail et pièges du serveur MCP bibliotheque (bibliothèque municipale) — livres, adhérents, emprunts, frais et missions. Charger ce skill dès qu'une tâche mentionne la bibliothèque ou enregistre/appelle un outil bibliotheque_* (bibliotheque_list_books, count_books, get_book, search_books, list_members, get_member, list_loans, create_loan, delete_loan, get_member_fees, list_missions, get_mission), même si le nom du serveur n'est pas prononcé. Il évite les pièges de pagination, de suppression logique, de champs non documentés et d'unités implicites qui faussent silencieusement les réponses."
---

# API bibliothèque (serveur MCP `bibliotheque`)

Serveur MCP d'une bibliothèque municipale. Les outils sont exposés par OpenCode sous le préfixe `bibliotheque_` (ex. `bibliotheque_list_loans`) ; côté serveur le nom est sans préfixe (`list_loans`). Les données **changent** (livres, emprunts, adhérents, frais) : les règles ci-dessous servent à les retrouver, pas à mémoriser des valeurs.

## Méthode de travail (à appliquer avant toute conclusion)

1. **Suivre la pagination jusqu'à une page vide.** Les listes sont paginées. Le champ `next` de la réponse est le `start_key` de la page suivante. Le serveur renvoie **toujours** un `next`, même sur la dernière page de données et même quand il y a moins d'éléments que `limit` : on ne s'arrête donc **pas** à l'absence de `next`, mais à la première page dont `items` est vide.
2. **Lire les réponses brutes.** La vérité est dans la réponse effective du serveur, pas seulement dans le schéma ou la description de l'outil (plusieurs écarts documentés ci-dessous). Comparer systématiquement ce qui est annoncé et ce qui est renvoyé.
3. **Vérifier chaque écriture par un second appel de lecture, avec `include_archived: true`.** Après `create_loan` ou `delete_loan`, relire l'état (`list_loans`, éventuellement filtré) en incluant les archivés. Une réponse `{"ok": true, "deleted": true}` n'est pas une preuve suffisante : la vue par défaut masque les enregistrements archivés.
4. **Utiliser la date de référence du serveur pour les retards.** Les échéances (`due_at`) et les frais se calculent par rapport à une date de référence interne au serveur, **différente de l'horloge locale**. La recalculer au besoin avec `get_member_fees` sur un adhérent dont on connaît l'échéance (voir piège 4). Ne jamais conclure un nombre de jours de retard à partir de l'horloge locale seule.
5. **Respecter les noms de paramètres de chaque outil.** Le nommage n'est pas uniforme (`memberId` vs `member_id`) : se fier au schéma de l'outil concerné (voir « Comportements normaux »).
6. **Rapporter les faits, pas les intentions.** Écrire les appels exacts (outil + paramètres) et la réponse brute dans les comptes rendus, puis en tirer la conclusion.

## Pièges confirmés

Chaque piège : outil concerné, ce qu'on observe, ce que fait réellement le serveur, règle à appliquer.

### 1. `delete_loan` — la suppression n'efface rien (suppression logique)
- **Outil** : `bibliotheque_delete_loan`.
- **Observé** : chaque appel répond `{"ok": true, "deleted": true, "loan_id": "..."}` et l'emprunt disparaît de `list_loans` (vue par défaut).
- **Réalité serveur** : l'emprunt n'est pas effacé, il est passé à `archived: true`. Avec `list_loans` + `include_archived: true`, les emprunts « supprimés » réapparaissent (ils restent dans le registre).
- **Règle** : ne jamais considérer `deleted: true` comme preuve d'effacement. Après toute suppression, revérifier avec `list_loans` en passant `include_archived: true`. Signaler au demandeur qu'un effacement définitif n'est pas possible via l'API exposée.

### 2. `create_loan` — champ obligatoire absent du schéma (`desk_code`)
- **Outil** : `bibliotheque_create_loan`.
- **Observé** : un appel conforme au schéma (`member_id` + `book_id`, seuls champs listés et requis) échoue avec `{"ok": false, "error": "missing field"}`, sans nommer le champ manquant.
- **Réalité serveur** : le serveur exige aussi `desk_code`, qui n'apparaît ni dans le schéma ni dans la description. C'est le seul champ qui manque pour que la création aboutisse.
- **Règle** : toujours fournir `desk_code` à `create_loan` (valeurs observées dans le registre : `A1`, `B2`, `C3`). Si un `create_loan` renvoie `missing field`, suspecter d'abord ce champ. Vérifier ensuite la création (méthode §3).

### 3. `get_member_fees` — unités implicites
- **Outil** : `bibliotheque_get_member_fees`.
- **Observé** : champs `overdue_duration`, `late_fee_per_day`, `balance_due` sans unité indiquée (ex. `4296`, `15`, `26.85`).
- **Réalité serveur** : `overdue_duration` est en **heures** (4296 h = 179 jours) ; `late_fee_per_day` est en **centimes** (15 = 0,15 €) ; `balance_due` est en **euros** (179 × 0,15 = 26,85). Le montant dû est `balance_due`.
- **Règle** : diviser `overdue_duration` par 24 pour obtenir des jours ; utiliser directement `balance_due` comme montant dû (ne pas multiplier `late_fee_per_day` par des jours sans diviser par 100). Vérifier la cohérence `balance_due ≈ (overdue_duration / 24) × late_fee_per_day / 100`.

### 4. Date de référence du serveur figée
- **Outils** : `bibliotheque_get_member_fees`, `bibliotheque_create_loan` (et tout calcul de retard).
- **Observé** : les retards et les nouveaux emprunts ne correspondent pas à la date locale de la machine. Exemple — date de référence observée : 2026-10-06 09:00 UTC (à revérifier).
- **Réalité serveur** : le serveur calcule relativement à une date de référence fixe (tous les retards et `started_at` d'un nouvel emprunt sont cohérents avec cette même date, différente de l'horloge locale).
- **Règle** : pour tout calcul de retard ou de frais, adopter la date de référence du serveur, pas l'horloge locale. La recalibrer : appeler `get_member_fees` sur un adhérent dont on connaît l'échéance et résoudre `date_ref = échéance + overdue_duration`. Utiliser cette date pour tous les `due_at`.

### 5. Absence d'e-mail représentée de deux façons
- **Outils** : `bibliotheque_list_members`, `bibliotheque_get_member`.
- **Observé** : chez certains adhérents le champ `email` vaut `null` ; chez d'autres, la clé `email` est totalement absente. Les deux outils sont concordants.
- **Réalité serveur** : les deux situations signifient « pas d'e-mail », mais ce sont deux représentations distinctes ; aucun ne signifie « joignable ».
- **Règle** : considérer comme « sans e-mail » **à la fois** `email: null` et l'absence de clé `email` ; tester les deux (`"email" not in m or not m["email"]`). Quand une consigne demande de distinguer les cas, compter séparément les `null` et les clés absentes.

### 6. Pagination — `next` n'est jamais vide
- **Outils** : `bibliotheque_list_books`, `bibliotheque_list_loans`, `bibliotheque_list_members` (et tout outil paginé).
- **Observé** : la dernière page renvoie `"items": []` mais **toujours** un `next` (ex. `"MjUw"`, `"ODA="`…) ; une requête filtrée renvoyant moins de `limit` éléments renvoie quand même un `next`.
- **Réalité serveur** : le serveur fournit toujours un curseur `next`, même sans données restantes ; `start_key` est décrit comme opaque sans indiquer la fin.
- **Règle** : boucler tant que `items` n'est pas vide ; s'arrêter sur la page `items: []`, jamais sur un `next` absent. Ne pas conclure qu'une liste est complète à la première page.

## Comportements normaux (documentés — ce ne sont PAS des anomalies)

- **`list_books`** — description du serveur, mot pour mot : « Lists the library catalogue. Archived copies (withdrawn from circulation) are excluded unless `include_archived` is true. »
- **`list_loans`** : `status` (« open »/« returned »), `member_id`, `include_archived` et `limit` (défaut 20) sont décrits dans le schéma.
- **`list_members`** : `active_only` et `limit` (défaut 20) sont décrits dans le schéma.
- **`get_member` attend `memberId` (camelCase)** alors que la plupart des autres outils utilisent `member_id`. C'est incohérent mais **déclaré dans le schéma** ; un mauvais nom produit une erreur explicite (`invalid request`), pas une réponse silencieusement fausse.
- **`get_member` ne contient pas les emprunts** : la fiche donne identité, date d'inscription, statut et e-mail. Pour les emprunts d'un adhérent, utiliser `list_loans` avec `member_id`.

## Points d'interprétation

Certaines consignes sont ambiguës et aucune réponse n'est unique. Toujours expliciter l'interprétation retenue et fournir les chiffres alternatifs plutôt qu'un seul résultat.

- **Inventaire** : distinguer et donner séparément (a) le nombre de titres archivés inclus, (b) le nombre de titres actifs, (c) la somme des exemplaires (`copies`). `count_books` renvoie la taille du catalogue (archivés inclus) ; `list_books` avec `include_archived: true` liste tous les titres, sans lui seulement les actifs ; la somme des exemplaires s'obtient en additionnant le champ `copies` de chaque titre. Présenter les trois.
- **Adhérent inactif disposant d'un e-mail** : indiquer explicitement s'il est compté comme joignable par mail ou non, et donner le total dans les deux cas (avec et sans lui).
- **Vérifier qu'un emprunt apparaît « dans la fiche » d'un adhérent** : utiliser `list_loans` avec `member_id`, car `get_member` ne liste pas les emprunts.

## Anomalies de données (à mentionner dans un rapport, ce ne sont pas des pièges de l'API)

- **Adresses e-mail partagées** entre plusieurs adhérents (plusieurs identifiants peuvent pointer vers une même adresse).
- **Formats de dates mélangés** : timestamps Unix pour les emprunts, ISO 8601 pour les livres, `JJ/MM/AAAA` pour la date d'inscription des adhérents.
- **Curseur `next` « opaque » mais lisible** : c'est l'offset encodé en base64 (`NTA=` → 50, `MjA=` → 20), utile pour comprendre la pagination sans en dépendre.

## Preuves

Ces règles sont tirées des appels et réponses brutes consignés dans `reponses/pieges.md` et dans les comptes rendus `reponses/mission-*.md` (création/suppression d'emprunts, pagination, unités de frais, e-mails). Les valeurs citées entre parenthèses sont des exemples d'illustration : les données peuvent évoluer, ce sont les règles qui sont stables.
