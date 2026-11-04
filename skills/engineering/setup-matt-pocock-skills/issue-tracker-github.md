# Issue tracker : GitHub

Les issues et les specs de ce repo sont des issues GitHub. Utilisez la CLI `gh` pour toutes les opérations.

## Conventions

- **Créer une issue** : `gh issue create --title "..." --body "..."`. Utilisez un heredoc pour les corps multi-lignes.
- **Lire une issue** : `gh issue view <number> --json number,title,body,labels,comments`.
- **Lister les issues** : `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` avec les filtres `--label` et `--state` appropriés.
- **Faire d'une issue une sub-issue d'un parent** : `gh issue create --parent <parent> ...`, ou ensuite `gh issue edit <parent> --add-sub-issue <child>` (`gh` 2.94 et ultérieur). `gh` plus ancien : `gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>` (id de base de données, comme dans **Blocage** ci-dessous). Sans sub-issues, placez `Part of #<parent>` en haut du corps de l'issue enfant.
- **Blocage** : les **dépendances d'issues natives** de GitHub, la représentation canonique et visible dans l'interface. Ajoutez une arête avec `gh issue create --blocked-by <n>,<n> ...` ou `gh issue edit <child> --add-blocked-by <blocker>` (`gh` 2.94 et ultérieur). `gh` plus ancien : `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`, où `<blocker-db-id>` est l'**id de base de données** numérique du bloqueur (`gh api repos/<owner>/<repo>/issues/<n> --jq .id`, _pas_ le `#number` ni le `node_id`). Là où les dépendances ne sont pas disponibles, rabattez-vous sur une ligne `Blocked by: #<n>, #<n>` en haut du corps de l'issue enfant. Un ticket est débloqué quand tous ses bloqueurs sont fermés.
- **Compter les bloqueurs ouverts** : `gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by` (bloqueurs ouverts uniquement ; 0 signifie que le ticket est démarrable). `gh issue view <n> --json blockedBy` liste aussi les bloqueurs fermés, filtrez donc : `--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`.
- **Commenter une issue** : `gh issue comment <number> --body "..."`
- **Ajouter / retirer des labels** : `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Fermer** : publiez l'explication avec `gh issue comment <number> --body "..."`, puis `gh issue close <number>`. Sur une issue déjà fermée, `gh issue close --comment` abandonne le commentaire et sort quand même avec le code 0.

Déduisez le repo de `git remote -v` ; `gh` le fait automatiquement quand il est lancé dans un clone.

Ne créez des issues que dans le repo de l'utilisateur. Ne créez jamais d'issue dans `mattpocock/skills` (le repo d'où viennent ces skills) sauf si l'utilisateur en est propriétaire. Dans un fork, `gh` peut choisir par défaut le parent upstream, donc avant le premier `gh issue create` d'une session, vérifiez la cible avec `gh repo view --json nameWithOwner` et confirmez-la avec l'utilisateur si ce n'est pas son repo.

## Les pull requests comme surface de triage

**PRs as a request surface: no.** _(Passez à `yes` si ce repo traite les pull requests externes comme des demandes de fonctionnalité ; `/triage` lit ce flag.)_

Quand la valeur est `yes`, les PR passent par les mêmes labels et états que les issues, via les équivalents `gh pr` :

- **Lire une PR** : `gh pr view <number>`, puis `gh pr view <number> --comments` (hors TTY, `--comments` n'affiche que les commentaires, pas le corps), et `gh pr diff <number>` pour le diff.
- **Lister les PR externes pour le triage** : `gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`.
- **Commenter / labelliser / fermer** : `gh pr comment`, `gh pr edit --add-label`/`--remove-label`, `gh pr close`.

GitHub partage un seul espace de numéros entre issues et PR, donc un `#42` seul peut désigner l'un ou l'autre : résolvez avec `gh pr view 42` et rabattez-vous sur `gh issue view 42`.

## Quand un skill dit « publier dans l'issue tracker »

Créez une issue GitHub.

## Quand un skill dit « récupérer le ticket pertinent »

Lisez-le comme dans **Lire une issue** ci-dessus.

## Opérations de wayfinding

Utilisées par `/wayfinder`. La **map** est une issue unique dont les issues **enfants** servent de tickets.

- **Map** : une issue unique labellisée `wayfinder:map`, qui porte le corps Notes / Decisions-so-far / Fog. `gh issue create --label wayfinder:map`.
- **Ticket enfant** : une issue liée à la map comme sub-issue GitHub (voir **Faire d'une issue une sub-issue d'un parent**). Là où les sub-issues ne sont pas activées, ajoutez l'enfant à une liste de tâches dans le corps de la map et placez `Part of #<map>` en haut du corps de l'enfant. Labels : `wayfinder:<type>` (`research`/`prototype`/`grilling`/`task`). Une fois réclamé, le ticket est assigné au dev qui pilote.
- **Blocage** : voir **Blocage** dans Conventions.
- **Requête de frontière** : listez les enfants ouverts de la map (`gh issue list --state open`, limité aux sub-issues / à la liste de tâches de la map), écartez ceux qui ont un bloqueur ouvert (voir **Compter les bloqueurs ouverts**, ou une issue ouverte dans la ligne `Blocked by`) ou une personne assignée ; le premier dans l'ordre de la map gagne.
- **Réclamer** : `gh issue edit <n> --add-assignee @me`, la première écriture de la session.
- **Résoudre** : `gh issue comment <n> --body "<answer>"`, puis `gh issue close <n>`, puis ajoutez un pointeur de contexte (résumé + lien) aux Decisions-so-far de la map.
