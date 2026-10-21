# Man-Beating Separation — Quel receveur gagne son duel en 1-contre-1 ?

## En bref (l'idée)
Se démarquer contre une couverture de **zone** est facile ; contre un **marquage individuel** (man-to-man), où un défenseur colle le receveur partout, c'est beaucoup plus dur. On mesure la **séparation** (distance du receveur visé à son défenseur le plus proche au moment où le ballon arrive) et on la **décompose man vs zone**. Le classement « Man-Beating Separation » isole les receveurs (WR / TE / RB) capables de se libérer **même en individuel** — l'information n°1 qu'un recruteur cherche, parce qu'elle prédit directement la réussite de l'action.

## L'insight (prouvé sur les données)
- **La séparation prédit la réussite** : de ~39 % de passes complétées à 0 yard de séparation, jusqu'à ~70 % à 5+ yards.
- **Se libérer est bien plus dur en individuel** : séparation moyenne de **2,84 yd en man** contre **4,94 yd en zone**, et la réussite chute de 65 % (zone) à 53 % (man).
- Un receveur qui garde de la séparation **contre le man-to-man** reste une option fiable même quand la défense lui met un homme dessus. Les stats de réception brutes mélangent man et zone ; notre métrique sépare ce talent.

## Qui l'utilise
- **Recruteur** : repérer le receveur qui gagne ses duels en solo (profil rare et recherché), et comparer deux joueurs à poste égal au-delà des réceptions.
- **Entraîneur** : savoir qui peut être visé en couverture serrée, et qui exploiter plutôt contre des zones.
- **Joueur** : objectiver sa valeur en individuel et revoir ses meilleurs duels.

## Ce que contient l'outil (site interactif)
- **🎬 Duels** : rejoue n'importe quel duel gagné en man-to-man (animation image par image, séparation affichée en direct), avec filtres par joueur / poste / séparation / touchdown.
- **🏆 Classement** : les receveurs triés par séparation en man-to-man (comparée à la zone).
- **👤 Fiche joueur** : dossier scout par joueur — scores man/zone, verdict automatique, et toutes ses actions connectées.
- **📊 Preuve & Analyse** : les graphiques qui démontrent l'insight (séparation → réussite, man vs zone, par poste).

## Comment lancer
Aucune dépendance à installer : tout est en **Python pur** (bibliothèque standard) + un site HTML/CSS/JS autonome.

1. (Optionnel) Régénérer les données depuis les CSV bruts :
   ```
   python generer_donnees.py
   ```
   Produit `resultats/data.json`. Le dépôt contient déjà ce fichier, cette étape est donc facultative.

2. Lancer le site (un petit serveur local est nécessaire car la page charge un JSON) :
   ```
   cd resultats
   python -m http.server 8000
   ```
   Puis ouvrir **http://localhost:8000/index.html** dans un navigateur.

> Astuce : si la commande `python` ouvre le Microsoft Store sous Windows, utilise `py` à la place.

## Méthode (résumé)
- Repère temporel : on s'ancre sur l'événement `pass_forward` puis on observe la scène ~0,8 s plus tard (8 frames à 10 fps), quand le ballon est en l'air.
- Cible = receveur le plus proche du ballon à cet instant ; séparation = distance à son défenseur le plus proche.
- Type de couverture (man / zone) fourni par PFF (`pff_passCoverageType`).
- Un joueur est classé à partir de 8 actions en man-to-man.

## Hypothèses & limites
- Le « défenseur direct » est approximé par le plus proche de la cible (les données ne donnent pas l'assignation de couverture joueur à joueur).
- L'instant d'observation (passe + 0,8 s) est un proxy du moment de réception, l'événement exact `pass_arrived` étant rare dans ce jeu de données.
- La séparation est mesurée au sol (x, y) ; la hauteur de passe n'est pas prise en compte.

## Données
NFL Next Gen Stats (tracking) + PFF (scouting), jeu de données du NFL Big Data Bowl.

## Structure du dépôt
```
Projet/
├── generer_donnees.py      # calcul de la métrique (Python pur)
├── README.md
└── resultats/
    ├── index.html          # site interactif
    ├── app.js              # logique (animation, classement, fiche joueur, analyse)
    └── data.json           # données pré-calculées (classement + actions + preuves)
nfl-big-data-bowl-regional-event-data/   # données brutes (fournies)
```
