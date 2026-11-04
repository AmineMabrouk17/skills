---
name: retro
description: "Menez une rétrospective sur une session de codage."
disable-model-invocation: true
---

L'utilisateur a demandé une **rétrospective**. Vous suggérez des améliorations à l'**environnement** de l'agent de codage pour améliorer les exécutions futures.

## Étapes

1. Appelez l'outil Skill avec `writing-for-agents` pour obtenir le guide de style d'écriture.

2. Lisez les sources primaires de la session indiquée par l'utilisateur. Cela peut impliquer de fouiller les journaux de session sur cette machine. Si l'utilisateur ne précise pas de session, prenez celle en cours par défaut.

3. Cherchez des candidats d'amélioration dans ces catégories.

- **Navigation** : dans quelle mesure l'agent a-t-il trouvé facilement les bons fichiers ? Existe-t-il des dépendances cachées entre fichiers ? Un **pointeur de navigation** faciliterait-il les choses ? _À utiliser quand_ la session a mis longtemps à trouver une information.
- **Vérifications automatisées** : existe-t-il des vérifications automatisées qui pourraient attraper les erreurs commises par l'agent ? Linting, vérification de types, tests, linters de système de fichiers ? Lisez d'abord la commande de vérification du repo lui-même (ses `package.json`/scripts `lint`/`check` de l'outil de build, son workflow CI), de sorte qu'une vérification déjà existante mais non branchée ou silencieusement cassée constitue le constat, et non une réinvention. Un repo sans **garde-fou** (aucun hook pre-commit et aucune tâche CI lançant sa commande de lint/typecheck/test) est en soi un constat : un repo non linté est une occasion manquée permanente, pas un défaut neutre. _À utiliser quand_ l'agent a commis une erreur qu'une vérification automatisée aurait pu attraper, ou que le repo n'a aucun garde-fou.
- **Standards de codage** : faut-il donner une nouvelle règle à appliquer à l'**agent de revue** ? Faut-il supprimer ou clarifier une règle existante ? Classez d'abord la violation : une violation **mécanique** (un motif syntaxique figé, une API bannie, une forme d'import, une règle de placement de fichier) appelle une vérification déterministe, point final : une règle personnalisée dans le linter du repo, un nouveau hook pre-commit ou une nouvelle tâche CI, selon ce que le langage du repo et le garde-fou existant rendent le moins coûteux. Privilégiez par défaut la construction de la vérification plutôt que l'écriture de la règle. Réservez `CODING_STANDARDS.md` aux véritables **jugements** (cohérence entre fichiers, « correspond au style environnant », tout ce qu'aucun garde-fou ne saurait remplacer). _À utiliser quand_ l'agent de revue n'a pas réussi à attraper une erreur.
- **AGENTS.md global** : y a-t-il des instructions de pilotage qui devraient plutôt être déplacées vers les standards de codage (ou les vérifications automatisées) ? _À utiliser quand_ le fichier AGENTS.md est particulièrement volumineux, dans le repo OU dans la portée globale de l'utilisateur.
- **Économie d'outils** : l'agent a-t-il fait des appels d'outils coûteux qui pourraient être rationalisés ? Existe-t-il un outillage personnalisé (CLI, MCP) particulièrement inefficace en tokens ? _À utiliser quand_ l'agent a fait un appel d'outil coûteux.
- **Instructions inopérantes** : cherchez dans les fichiers de pilotage des instructions qui ne modifient pas le comportement de l'agent. _À utiliser quand_ les fichiers de pilotage sont volumineux et difficiles à manier.
- **Accès à l'information** : cherchez des occasions d'accroître l'accès de l'agent à l'information. Rediriger les journaux du serveur de développement, un accès en lecture seule aux services tiers. _À utiliser quand_ une information cruciale n'était pas disponible pour l'agent.

4. Présentez ces candidats à l'utilisateur, par ordre de gravité.

## Référence

### Implémentation vs revue

Rappelez-vous que tout travail passe par deux étapes : l'implémentation et la revue. L'agent d'implémentation subit la plus forte **pression de contexte**. Il est responsable de l'exploration, de l'écriture du code et du débogage des échecs.

L'agent de revue subit la plus faible pression de contexte : il reçoit un diff, donc aucune exploration n'est nécessaire. Il n'a souvent pas besoin d'écrire du code ni de déboguer.

Cela signifie que l'agent de revue devrait être chargé d'imposer les standards de codage, et non l'agent d'implémentation.

### Fichiers

Vous avez accès à plusieurs fichiers dans le repo :

- `CLAUDE.md`/`AGENTS.md` : ces fichiers sont poussés dans la fenêtre de contexte de tout agent travaillant dans ce repo. Ils doivent être utilisés avec une grande parcimonie, généralement uniquement pour des **pointeurs de navigation** vers d'autres fichiers.
- `CODING_STANDARDS.md` : ce fichier est lu pendant la revue, pas pendant l'implémentation. Ajoutez des **pointeurs de navigation** vers les dossiers de documentation si le fichier de standards dépasse 1,000 lignes.
- Docs : utilisez la documentation comme fichiers de référence, pointés par d'autres fichiers. Cherchez la documentation existante avant d'en écrire de nouvelles.
- Skills : utilisez les skills pour la documentation (puisque leur description entre dans la fenêtre de contexte de l'agent), ou pour les commandes invoquées par l'utilisateur. Suivez les conseils du skill `writing-for-agents`.
