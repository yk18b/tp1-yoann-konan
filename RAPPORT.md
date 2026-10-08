## 1c — Missions

1. **M1 — Inventaire** : Le conseil municipal demande le nombre exact d'ouvrages détenus par la bibliothèque, et la répartition par genre. Donne les chiffres et explique comment tu les as obtenus.

2. **M2 — Le retardataire** : Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent.

3. **M3 — La réinscription** : Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche.

4. **M4 — Le ménage** : L'adhérent MB-202 demande l'effacement de ses emprunts déjà rendus. Supprime-les, puis prouve qu’ils ont bien disparu.

5. **M5 — La relance** : Prépare la campagne de relance : la liste des adhérents ayant au moins un emprunt en retard et joignables par mail, et le nombre de ceux qui ne sont pas joignables, en distinguant les cas.

#### Mission 1 — Inventaire
- Énoncé : Le conseil municipal demande le nombre exact d'ouvrages détenus par la bibliothèque, et la répartition par genre. Donne les chiffres et explique comment tu les as obtenus.
- Appels passés par l'agent : count_books ; list_books {limit: 50, include_archived: true} sur 5 pages via start_key ; vérification par list_books filtré par genre.
- Réponse brute : voir reponses/mission-1.md (count = 184).
- Conclusion de l'agent : 184 ouvrages (jeunesse 38, poésie 35, roman 34, policier 31, essai 25, bd 21), dont 26 archivés.
- Ce qui clochait : le curseur next n'est jamais vide (page vide avec next = "MjUw") ; ambiguïté de l'énoncé : titres vs exemplaires (champ copies) vs titres non archivés.
- Réponse retenue : 184 ouvrages (titres, archivés inclus) — 1 tentative.

#### Mission 2 — Le retardataire
- Énoncé : Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent.
- Appels passés par l'agent : list_loans {status: "open", limit: 50} sur 3 pages ; get_member MB-225 ; get_book BK-1075 ; get_member_fees MB-225, MB-200, MB-227.
- Réponse brute : voir reponses/mission-2.md, reponses/mission-2-verification.md et reponses/mission-2-verification-archives.md.
- Conclusion de l'agent : LN-5106, MB-225 Paul Blanc, BK-1075, 179 jours, 26,85.
- Ce qui clochait :
  - get_member_fees ne documente ni l'unité de overdue_duration (heures), ni celle de late_fee_per_day (centimes) face à balance_due (euros), ni la date de référence (le serveur calcule au 2026-10-06 09:00).
  - L'agent a affirmé que list_loans excluait les archivés par défaut ; test avec include_archived=true : 0 emprunt archivé, l'explication était inventée.
  - Vérifié : pas d'emprunt non rendu caché (84 returned + 53 open = 137 ; 53 returned_at null).
- Réponse retenue : LN-5106, MB-225 Paul Blanc, BK-1075 « Le Retour des autres », 179 jours de retard selon le serveur, 26,85 € dus — 1 tentative + 2 vérifications.

### Mission 3
- Ce qui clochait :
  - create_loan : appel conforme au schéma refusé avec "missing field" ; desk_code requis mais absent du schéma ; l'agent a deviné le champ et inventé la valeur "A1".
  - La date de début de l'emprunt est le 2026-10-06, pas la date réelle (même date de référence figée qu'en M2).
  - L'agent appelle "fiche" le résultat de list_loans sans avoir appelé get_member ; sa "vérification par un autre chemin" répète le même appel.
  - Vérifié : get_member renvoie la fiche sans emprunts, list_loans(member_id) est le seul moyen de vérifier ; MB-214 est actif ; total 137 → 138.
- Réponse retenue : emprunt LN-5137 (MB-214, BK-1042, échéance 2026-10-27) — 2 tentatives (1 échec "missing field").

### Mission 4
- Ce qui clochait :
  - delete_loan répond {"ok": true, "deleted": true} mais n'efface rien : les 6 emprunts passent en archived: true et ne sont que masqués par défaut dans list_loans.
  - La description annonce « Deletes a loan from the register » : écart avec le comportement réel.
  - L'agent a prouvé la disparition avec un list_loans sans include_archived, puis, après découverte de l'archivage, a quand même conclu à une mission réussie.
  - Sélection correcte : seuls les 6 emprunts rendus ont été traités, LN-5060 (en cours) conservé.
- Réponse retenue : les 6 emprunts rendus de MB-202 sont masqués mais pas effacés ; l'effacement réel est impossible via l'API exposée — à signaler au demandeur. 1 tentative, résultat de l'agent faux.

### Mission 5
- Ce qui clochait :
  - L'absence d'e-mail a deux formes non documentées : "email": null (10 adhérents) et champ email absent (5 adhérents). L'agent les a fusionnées en "e-mail manquant", alors que l'énoncé demande de distinguer les cas ; même dans sa vérification, son tableau affiche None pour les deux.
  - Critère ajouté par l'agent : inactif = non joignable (MB-219 a pourtant un e-mail).
  - Adresses partagées : yanis.robin@ (MB-200 et MB-237, tous deux relancés), sarah.perrin@ (MB-224, MB-239), lea.perrin@ (MB-215, MB-240).
  - Vérifié : list_members et get_member concordants, 46 adhérents.
- Réponse retenue : 20 joignables ; 9 non joignables = 5 email null, 3 champ email absent, 1 inactif avec e-mail — 1 tentative + 1 vérification.
