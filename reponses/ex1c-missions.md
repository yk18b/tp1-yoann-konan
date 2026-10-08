# Ex1c — Missions

## `bibliotheque_list_missions` — réponse brute

```json
{
  "ok": true,
  "items": [
    {
      "mission_id": "M1",
      "title": "Inventaire"
    },
    {
      "mission_id": "M2",
      "title": "Le retardataire"
    },
    {
      "mission_id": "M3",
      "title": "La réinscription"
    },
    {
      "mission_id": "M4",
      "title": "Le ménage"
    },
    {
      "mission_id": "M5",
      "title": "La relance"
    }
  ]
}
```

## M1 — Inventaire

Réponse brute de `bibliotheque_get_mission` :

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

Énoncé exact :

> Le conseil municipal demande le nombre exact d'ouvrages détenus par la bibliothèque, et la répartition par genre. Donne les chiffres et explique comment tu les as obtenus.

## M2 — Le retardataire

Réponse brute de `bibliotheque_get_mission` :

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

Énoncé exact :

> Identifie l'emprunt le plus en retard actuellement : quel adhérent, quel ouvrage, et combien de jours de retard exactement. Donne aussi le montant dû par cet adhérent.

## M3 — La réinscription

Réponse brute de `bibliotheque_get_mission` :

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M3",
    "title": "La réinscription",
    "brief": "Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche."
  }
}
```

Énoncé exact :

> Enregistre un nouvel emprunt pour l'adhérent MB-214 sur l'ouvrage BK-1042, puis vérifie que l'emprunt apparaît bien dans sa fiche.

## M4 — Le ménage

Réponse brute de `bibliotheque_get_mission` :

```json
{
  "ok": true,
  "mission": {
    "mission_id": "M4",
    "title": "Le ménage",
    "brief": "L'adhérent MB-202 demande l'effacement de ses emprunts déjà rendus. Supprime-les, puis prouve qu’ils ont bien disparu."
  }
}
```

Énoncé exact :

> L'adhérent MB-202 demande l'effacement de ses emprunts déjà rendus. Supprime-les, puis prouve qu’ils ont bien disparu.

## M5 — La relance

Réponse brute de `bibliotheque_get_mission` :

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

Énoncé exact :

> Prépare la campagne de relance : la liste des adhérents ayant au moins un emprunt en retard et joignables par mail, et le nombre de ceux qui ne sont pas joignables, en distinguant les cas.
