# Pièges candidats (à confirmer avec preuve)

## list_books / list_loans — curseur next jamais vide
- Observé : page vide renvoyée avec next = "MjUw" ; requêtes par genre < 50 résultats avec next = "NTA=".
- Preuve : reponses/mission-1.md, appel 6.
- Statut : à vérifier dans la description complète de l'outil.

## get_member_fees — unités
- Observé : overdue_duration 4296 (heures ?), late_fee_per_day 15 (centimes ?), balance_due 26.85 (euros ?).
- Preuve : reponses/mission-2.md, appel 6.
- Statut : à vérifier dans la description.

## get_member_fees — date de référence
- Observé : retards calculés comme si on était le 2026-10-06 09:00, pas à la date réelle.
- Preuve : reponses/mission-2.md, appels 6 à 8.
- Statut : à vérifier.

## list_loans — filtre status
- Observé : l'agent n'a regardé que status=open.
- Statut : à vérifier (autres statuts ? returned_at null avec un autre statut ?).

## Non-pièges (documentés, ne pas signaler)
- list_books exclut les archivés par défaut : écrit dans sa description.

## create_loan — champ obligatoire non documenté (CONFIRMÉ)
- Observé : appel avec member_id + book_id (conforme au schéma) → {"ok": false, "error": "missing field"}.
- Réalité : le serveur exige desk_code, absent du schéma ; l'erreur ne nomme pas le champ.
- Règle : toujours fournir desk_code (A1, B2 ou C3 observés).
- Preuve : reponses/mission-3.md, appels 1 et 2 + schéma.
