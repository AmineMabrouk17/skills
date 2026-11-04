# Issue tracker : GitLab

Les issues et les specs de ce repo sont des issues GitLab. Utilisez la CLI [`glab`](https://gitlab.com/gitlab-org/cli) pour toutes les opérations.

## Conventions

- **Créer une issue** : `glab issue create --title "..." --description "..."`. Utilisez un heredoc pour les descriptions multi-lignes. Passez `--description -` pour ouvrir un éditeur.
- **Lire une issue** : `glab issue view <number> --comments`. Utilisez `-F json` pour une sortie exploitable par machine.
- **Lister les issues** : `glab issue list -O json` avec les filtres `--label` appropriés.
- **Commenter une issue** : `glab issue note <number> --message "..."`. GitLab appelle les commentaires des « notes ».
- **Ajouter / retirer des labels** : `glab issue update <number> --label "..."` / `--unlabel "..."`. Plusieurs labels peuvent être séparés par des virgules ou passés en répétant l'option.
- **Fermer** : `glab issue close <number>`. `glab issue close` n'accepte pas de commentaire de fermeture, publiez donc l'explication d'abord avec `glab issue note <number> --message "..."`, puis fermez.
- **Merge requests** : GitLab appelle les PR des « merge requests ». Utilisez `glab mr create`, `glab mr view`, `glab mr note`, etc., de la même forme que `gh pr ...` avec `mr` à la place de `pr` et `note`/`--message` à la place de `comment`/`--body`.

Déduisez le repo de `git remote -v` ; `glab` le fait automatiquement quand il est lancé dans un clone.

## Les merge requests comme surface de triage

**MRs as a request surface: no.** _(Passez à `yes` si ce repo traite les merge requests externes comme des demandes de fonctionnalité ; `/triage` lit ce flag.)_

Quand la valeur est `yes`, les MR passent par les mêmes labels et états que les issues, via les équivalents `glab mr` :

- **Lire une MR** : `glab mr view <number> --comments` et `glab mr diff <number>` pour le diff.
- **Lister les MR externes pour le triage** : `glab mr list -O json`, puis ne gardez que les MR dont l'auteur n'est ni membre ni propriétaire du projet (la MR d'un contributeur, pas le travail en cours d'un mainteneur).
- **Commenter / labelliser / fermer** : `glab mr note`, `glab mr update --label`/`--unlabel`, `glab mr close`.

Contrairement à GitHub, GitLab numérote les issues et les MR séparément, donc `#42` est sans ambiguïté une fois que vous savez de quelle surface le mainteneur parle.

## Quand un skill dit « publier dans l'issue tracker »

Créez une issue GitLab.

## Quand un skill dit « récupérer le ticket pertinent »

Lancez `glab issue view <number> --comments`.

## Opérations de wayfinding

Utilisées par `/wayfinder`. La **map** est une issue unique dont les issues **enfants** servent de tickets.

- **Map** : une issue unique labellisée `wayfinder:map`, qui porte le corps Notes / Decisions-so-far / Fog. `glab issue create --label wayfinder:map`. (Sur les offres GitLab qui ont des epics natifs, un epic peut porter la map à la place ; une issue labellisée fonctionne partout.)
- **Ticket enfant** : une issue portant `Part of #<map>` en haut de sa description, liée à la map (`glab issue create --linked-issues <map> --link-type relates_to`), avec les labels `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Une fois réclamé, le ticket est assigné au dev qui pilote.
- **Blocage** : le **lien de blocage natif** de GitLab, la représentation canonique et visible dans l'interface. Ajoutez-le avec l'action rapide `/blocked_by #<n>`, publiée comme note (`glab issue note <child> --message "/blocked_by #<blocker>"`). Les liens de blocage natifs sont une fonctionnalité Premium/Ultimate ; sur l'offre gratuite (ou là où ils ne sont pas disponibles), rabattez-vous sur une ligne `Blocked by: #<n>, #<n>` en haut de la description. Un ticket est débloqué quand tous ses bloqueurs sont fermés.
- **Requête de frontière** : listez les enfants de la map avec `glab api projects/:id/issues/<map>/links`, en gardant les issues ouvertes qui portent un label `wayfinder:<type>` ; écartez celles qui ont un bloqueur ouvert : un lien `blocked_by` natif vers une issue ouverte (`glab api projects/:id/issues/<child-iid>/links`), ou une issue ouverte dans la ligne `Blocked by`, ou une personne assignée ; le premier dans l'ordre de la map gagne.
- **Réclamer** : `glab issue update <n> --assignee @me`, la première écriture de la session.
- **Résoudre** : `glab issue note <n> --message "<answer>"`, puis `glab issue close <n>`, puis ajoutez un pointeur de contexte (résumé + lien) aux Decisions-so-far de la map.
