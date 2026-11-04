# Tâches d'exemple

Des tâches regroupées par le problème qu'elles résolvent. Chacune nomme le déclencheur qui démarre ses exécutions, ce que fait une exécution, et ce qu'elle déclenche ensuite. La plupart des factories commencent par l'une d'elles et grandissent en enchaînant la suivante.

## Les tickets non triés s'accumulent

**Tâche d'exploration**
- Déclencheur : un ticket est créé, ou un humain lui pose un label.
- Fait : confronte le ticket à la base de code et le classe dans une catégorie (cas simple, besoin d'un humain, pas un bug).
- Déclenche : une exécution d'implémentation pour les cas simples. Tout le reste se termine avec le ticket labellisé comme en attente d'un humain.

## Les tickets prêts attendent que quelqu'un les construise

**Tâche d'implémentation**
- Déclencheur : un ticket est labellisé prêt, par un humain ou par une exécution d'exploration.
- Fait : construit le ticket jusqu'à ce que les vérifications automatisées passent, et ouvre une PR en brouillon.
- Déclenche : une exécution de revue automatisée.

## Les PR attendent une revue

**Tâche de revue automatisée**
- Déclencheur : une PR s'ouvre sans revue automatisée, ou un humain lui pose un label.
- Fait : examine le diff au regard des standards de la base de code et du ticket dont elle vient.
- Déclenche : une exécution de reprise en cas d'échec. En cas de succès, elle se termine, et la PR attend la revue humaine avant qu'un humain ne la merge.

## Les revues automatisées en échec restent sans correction

**Tâche de reprise**
- Déclencheur : une exécution de revue automatisée met une PR en échec.
- Fait : corrige uniquement ce que la revue automatisée a nommé, et pousse.
- Déclenche : une exécution de revue automatisée. Après un nombre défini d'échecs, elle se termine avec la PR labellisée comme en attente d'un humain.

## Les specs sont trop grosses pour une seule exécution

**Tâche de décomposition**
- Déclencheur : un ticket de spec reçoit un label.
- Fait : découpe la spec en tickets, chacun enregistrant les tickets dont il dépend.
- Déclenche : une exécution d'implémentation.

L'exécution d'implémentation pioche ici le prochain ticket dont toutes les dépendances sont fermées, et se redéclenche jusqu'à ce qu'il ne reste aucun ticket.

## Migrer vers une vérification automatisée plus stricte

Une nouvelle règle de lint (ou un réglage de types plus strict) est activée, mais seuls certains fichiers la respectent. La règle s'applique aux fichiers qui passent, et les autres restent sur une liste d'exceptions.

**Tâche ratchet**
- Déclencheur : une horloge quotidienne.
- Fait : retire un fichier de la liste d'exceptions, le corrige jusqu'à ce que la vérification plus stricte passe, et ouvre une PR.
- Déclenche : une exécution de revue automatisée. Une fois la liste d'exceptions vide, l'exécution ratchet n'a plus rien à prendre et la migration est terminée.

Une petite PR par jour garde la revue humaine bon marché, et la vérification ne fait que se durcir.

## Les utilisateurs signalent des bugs que personne ne lit

Un bouton de feedback dans l'application est une source de tickets : il crée chaque signalement comme un ticket déjà porteur du label explore. L'exécution d'exploration fait le reste.

## Les problèmes de production sont détectés trop tard

Un moniteur (requêtes lentes, taux d'erreur) est une source de tickets. Il crée un ticket par problème, et commente le ticket ouvert à la place quand il revoit le même problème. Après le merge d'une correction, il rouvre le ticket si le problème persiste.

## Les exécutions de la factory elle-même échouent

**Tâche de correction**
- Déclencheur : l'orchestrateur signale une exécution en échec.
- Fait : crée un ticket à partir du log de l'échec, et tente une correction sous forme de PR quand l'échec est corrigeable.
- Déclenche : une exécution de revue automatisée. Un échec dans le travail d'une exécution de correction ne déclenche rien, donc les corrections ne s'enchaînent jamais. Comme la correction modifie la factory elle-même, elle passe toujours par une revue humaine.

## La base de code dérive

**Tâche d'architecture**
- Déclencheur : une horloge nocturne.
- Fait : parcourt les changements récents à la recherche de candidats au refactoring et les regroupe dans un seul ticket.
- Déclenche : rien. Un humain choisit un candidat et lui pose un label, ce qui démarre la chaîne habituelle.

## Les mêmes erreurs reviennent

**Tâche rétro**
- Déclencheur : une PR est mergée, à raison d'une PR mergée sur dix.
- Fait : lit la PR, son ticket, ses revues automatisées et ses logs d'exécution, et propose des changements aux instructions de la factory elle-même sous forme de PR.
- Déclenche : une exécution de revue automatisée.
