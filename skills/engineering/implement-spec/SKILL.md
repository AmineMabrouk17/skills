---
name: implement-spec
description: "Implémentez en code le résultat de /to-spec et /to-tickets."
disable-model-invocation: true
---

Une spec vous a été fournie. Des tickets devraient y être associés, décrivant comment implémenter la spec.

L'issue tracker devrait vous avoir été fourni. Sinon, demandez à l'utilisateur de lancer `/setup-matt-pocock-skills`.

L'objectif est que toute la spec soit implémentée sur une seule **branch d'intégration**, avec chaque ticket résolu de la manière dont l'issue tracker clôt le travail.

Les tickets ne sont pas une liste d'étapes. Ils forment un **graphe de tâches** avec des relations de blocage entre eux. Cela signifie qu'il existe toujours une **frontière** de tickets prêts à être pris.

Démarrez chaque subagent avec une fenêtre de contexte neuve.

La communication vers et depuis les subagents doit être rare. Communiquez principalement via des **pointeurs de contexte** : vers la spec, les tickets, les notes de recherche et les commits précédents. Ne dupliquez pas les informations déjà disponibles via les pointeurs.

Les **subagents implémenteurs** doivent être lancés en arrière-plan lorsque c'est possible, pour une concurrence maximale.

## Étapes

1. Lisez la spec et les tickets pour comprendre le graphe de tâches.

2. (facultatif) Utilisez un **subagent d'exploration** pour mener toute exploration exigée par les tickets : fichiers pertinents de la base de code ou documentation externe. Assurez-vous que le subagent d'exploration peut enregistrer des fichiers : il doit sauvegarder ses notes markdown dans un répertoire hors du repo, accessible à tous les futurs subagents. Cela permet aux **subagents implémenteurs** de se concentrer sur l'implémentation plutôt que sur l'exploration.

3. Créez la branch d'intégration. Si l'issue tracker clôt le travail via des PR, ou si l'utilisateur en demande une, ouvrez une PR en brouillon après le premier merge de l'étape 5 (une branch sans commit en avance sur main ne peut pas en ouvrir une), marquée comme fermant la spec et les tickets.

4. Utilisez un **subagent implémenteur** neuf pour chaque ticket, chacun dans son propre worktree sur sa propre branch. Chaque subagent implémenteur :
   - confirme que son worktree est basé sur la branch d'intégration avant de commencer, et s'y réinitialise sinon ;
   - appelle l'outil Skill avec `tdd` pour construire le ticket ;
   - merge la pointe de la branch d'intégration dans sa propre branch avant de signaler qu'il a terminé

5. Dès qu'un **subagent implémenteur** a terminé, mergez son travail dans la branch d'intégration avec un **subagent de merge**.

6. Si cela modifie la **frontière** des tickets disponibles, lancez d'autres **subagents implémenteurs** pour travailler sur les nouveaux tickets. Cela permet une concurrence maximale.

7. Une fois tous les tickets terminés, appelez l'outil Skill avec `code-review` sur la branch d'intégration. Corrigez tous les problèmes soulevés par la revue de code dans un seul **subagent implémenteur** neuf.

8. Si une PR en brouillon existe, marquez-la comme prête pour la revue. Sinon, résolvez chaque ticket de la manière dont l'issue tracker clôt le travail, et indiquez la branch d'intégration.

9. Nettoyez tous les worktrees des **subagents implémenteurs**.
