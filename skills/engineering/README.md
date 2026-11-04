# Ingénierie

Les skills que j'utilise au quotidien pour le travail sur du code.

## Invoqués par l'utilisateur

Accessibles uniquement quand vous les tapez (Claude Code : `disable-model-invocation: true` ; Codex : `policy.allow_implicit_invocation: false` dans `agents/openai.yaml`).

- **[ask-matt](./ask-matt/SKILL.md)** : Demandez quel skill ou quel flux correspond à votre situation. Un routeur vers les skills invoqués par l'utilisateur de ce repo.
- **[grill-with-docs](./grill-with-docs/SKILL.md)** : Une session de grilling qui construit aussi le modèle de domaine de votre projet, affine la terminologie et met à jour `GLOSSARY.md` et les ADR au fil de l'eau.
- **[triage](./triage/SKILL.md)** : Fait avancer les issues dans une machine à états de rôles de triage.
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)** : Analyse une base de code pour trouver des opportunités d'approfondissement, les présente dans un rapport HTML visuel, puis mène une session de grilling sur celle que vous choisissez.
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)** : Configure ce repo pour les skills d'ingénierie (issue tracker, labels de triage, organisation des docs de domaine). À lancer une fois par repo.
- **[to-spec](./to-spec/SKILL.md)** : Transforme la conversation en cours en spec et la publie dans l'issue tracker.
- **[to-tickets](./to-tickets/SKILL.md)** : Découpe n'importe quel plan, spec ou conversation en un ensemble de tickets tracer-bullet, chacun déclarant ses dépendances bloquantes, que ce soit sous forme de texte dans un fichier local ou de liens de blocage natifs sur un vrai tracker.
- **[implement](./implement/SKILL.md)** : Réalise le travail décrit par une spec ou un ensemble de tickets, en pilotant `/tdd` aux seams convenus à l'avance et en terminant par `/code-review` avant de committer.
- **[implement-spec](./implement-spec/SKILL.md)** : Implémente une spec entière sur une seule branche d'intégration. Traite les tickets comme un graphe de tâches, en lançant des subagents implémenteurs sur la frontière prête pour une concurrence maximale, puis termine par `/code-review`.
- **[wayfinder](./wayfinder/SKILL.md)** : Planifie un gros morceau de travail (plus qu'une seule session d'agent ne peut en contenir) sous forme de carte partagée de decision tickets sur l'issue tracker, résolus un par un jusqu'à ce que le chemin vers la destination soit clair.
- **[retro](./retro/SKILL.md)** : Propose des améliorations à l'environnement de l'agent de code (navigation, vérifications automatisées, standards de code, fichiers de pilotage, outillage) après une session, en commençant par les plus importantes.

## Invoqués par le modèle

Accessibles par le modèle ou par l'utilisateur (formulations de déclenchement riches pour que le modèle puisse les solliciter).

- **[prototype](./prototype/SKILL.md)** : Construisez un prototype jetable pour répondre à une question de conception : un seul fichier HTML partageable pour l'état/la logique, ou plusieurs variantes d'interface activables.

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)** : Boucle de diagnostic disciplinée pour les bugs difficiles et les régressions de performance : construire une boucle de rétroaction qui passe au rouge sur ce bug → minimiser → formuler des hypothèses → instrumenter → corriger → tester la non-régression.
- **[research](./research/SKILL.md)** : Enquête sur une question à partir de sources primaires dignes de confiance et consigne les résultats dans un fichier Markdown cité dans le repo, exécuté comme agent en arrière-plan.
- **[tdd](./tdd/SKILL.md)** : Développement piloté par les tests avec une boucle red-green-refactor. Construit des fonctionnalités ou corrige des bugs une tranche verticale à la fois.
- **[domain-modeling](./domain-modeling/SKILL.md)** : Construit et affine activement le modèle de domaine d'un projet en remettant en question les termes, en le mettant à l'épreuve avec des scénarios et en mettant à jour `GLOSSARY.md` et les ADR au fil de l'eau.
- **[codebase-design](./codebase-design/SKILL.md)** : Discipline et vocabulaire partagés pour concevoir des modules profonds : petites interfaces, seams propres, testables à travers l'interface.
- **[code-review](./code-review/SKILL.md)** : Revue du diff depuis un point fixe selon deux axes : **Standards** (le code suit-il les standards de code du repo, plus une base de référence des code smells de Fowler ?) et **Spec** (implémente-t-il fidèlement l'issue/la spec d'origine ?), menée par des subagents en parallèle.
- **[pr](./pr/SKILL.md)** : La forme que doit prendre le corps d'une pull request : un résumé sous la forme du plus petit visuel qui rend le changement clair, des preuves avant/après que ça fonctionne, et un verdict sur le danger du merge (porte à sens unique ou à double sens, plus le rayon d'impact).
- **[wizard](./wizard/SKILL.md)** : Génère un assistant bash interactif qui guide une personne à travers les étapes qu'elle seule peut accomplir : provisionner de l'infrastructure, configurer des identifiants ou des secrets CI, parcourir un tableau de bord tiers inconnu, ou exécuter une migration ou un basculement ponctuel.
