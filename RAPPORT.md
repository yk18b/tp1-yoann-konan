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
- Réponse brute : voir reponses/mission-2.md (get_member_fees MB-225 : overdue_duration 4296, late_fee_per_day 15, balance_due 26.85).
- Conclusion de l'agent : LN-5106, MB-225 Paul Blanc, BK-1075, 179 jours, 26,85.
- Ce qui clochait (à confirmer) : filtre status=open peut exclure des emprunts non rendus ; unités devinées (overdue_duration en heures ? late_fee_per_day en centimes, balance_due en euros) ; date de référence du serveur = 6 octobre au lieu de la date réelle ; formats de dates mélangés.
- Réponse retenue : en attente de vérification.
