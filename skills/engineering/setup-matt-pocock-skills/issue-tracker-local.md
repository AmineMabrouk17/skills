# Issue tracker : Markdown local

Les issues et les specs de ce repo sont des fichiers markdown dans `.scratch/`.

## Conventions

- Une fonctionnalité par répertoire : `.scratch/<feature-slug>/`
- La spec est `.scratch/<feature-slug>/spec.md`
- Les issues d'implémentation sont un fichier par ticket dans `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numérotés à partir de `01`, jamais un seul fichier de tickets combiné
- L'état de triage est consigné dans une ligne `Status:` près du haut de chaque fichier d'issue (voir `triage-labels.md` pour les chaînes de rôles)
- Les commentaires et l'historique de conversation s'ajoutent en bas du fichier sous un titre `## Comments`

## Quand un skill dit « publier dans l'issue tracker »

Créez un nouveau fichier sous `.scratch/<feature-slug>/` (en créant le répertoire si besoin).

## Quand un skill dit « récupérer le ticket pertinent »

Lisez le fichier au chemin référencé. Normalement, l'utilisateur passe directement le chemin ou le numéro de l'issue.

## Opérations de wayfinding

Utilisées par `/wayfinder`. La **map** est un fichier, avec un fichier **enfant** par ticket.

- **Map** : `.scratch/<effort>/map.md` (le corps Notes / Decisions-so-far / Fog).
- **Ticket enfant** : `.scratch/<effort>/issues/NN-<slug>.md`, numéroté à partir de `01`, avec la question dans le corps. Une ligne `Type:` consigne le type de ticket (`research`/`prototype`/`grilling`/`task`) ; une ligne `Status:` consigne `claimed`/`resolved`.
- **Blocage** : une ligne `Blocked by: NN, NN` près du haut. Un ticket est débloqué quand chaque fichier qu'il liste est `resolved`.
- **Frontière** : parcourez `.scratch/<effort>/issues/` pour trouver les fichiers ouverts, non bloqués et non réclamés ; le premier par numéro gagne.
- **Réclamer** : passez à `Status: claimed` et enregistrez avant tout travail.
- **Résoudre** : ajoutez la réponse sous un titre `## Answer`, passez à `Status: resolved`, puis ajoutez un pointeur de contexte (résumé + lien) aux Decisions-so-far de la map dans `map.md`.
