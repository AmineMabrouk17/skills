---
name: software-factory
description: Langage commun pour concevoir une software factory : les tâches qu'elle exécute et les services auxquels elle se branche.
disable-model-invocation: true
---

Une **software factory** est un système d'agents qui transforme des tickets, des PR et des planifications en code mergé, déployé sur une infrastructure distante choisie par l'utilisateur. Ce skill est le langage pour en concevoir une. Utilisez ces termes, et seulement ceux-ci, quand vous discutez d'une factory ou la dessinez au tableau ; quand l'utilisateur emploie un mot de la liste _À éviter_, nommez le terme canonique.

## Tâches et exécutions

**Tâche** :
Un type de travail que la factory exécute, défini une fois et exécuté de nombreuses fois : revue, exploration, implémentation, ratchet. Une tâche peut porter sur un ticket, une PR, ou la base de code dans son ensemble.
_À éviter_ : job, workflow, action

**Déclencheur** :
L'événement qui démarre une exécution : un changement dans le ticket tracker ou l'hébergeur git, une horloge dans l'orchestrateur, ou une autre exécution.

**Exécution** :
Une exécution d'une tâche sur un runner, de son déclencheur à sa fin, traitée comme une boîte noire. Une exécution en déclenche d'autres, soit directement, par la logique de l'orchestrateur, soit en modifiant le ticket tracker ou l'hébergeur git. Une exécution n'attend jamais un humain : elle se termine, et le changement ultérieur de l'humain dans le ticket tracker ou l'hébergeur git déclenche une nouvelle exécution.
_À éviter_ : session

**Runner** :
La machine jetable sur laquelle s'exécute une exécution. Rien de ce qu'elle porte ne survit à l'exécution, même quand la machine elle-même est durable : la seule mémoire qu'une exécution garde des exécutions précédentes, ce sont le ticket tracker, l'hébergeur git et l'orchestrateur.
_À éviter_ : serveur, hôte, machine, compute, sandbox

## Services

**Ticket tracker** :
Là où vivent les tickets, et l'interface humaine de la factory : les humains planifient le travail en créant des tickets, parlent aux agents dans leurs commentaires, et leurs changements de tickets déclenchent des exécutions.
_À éviter_ : issue tracker, gestionnaire de tickets, tableau de projet

**Source de tickets** :
Tout ce qui, hors de la factory, crée des tickets dans le ticket tracker : un bouton de feedback, un moniteur, un traqueur d'erreurs, une boîte de réception.
_À éviter_ : source de données, source de déclencheur, collecte

**Hébergeur git** :
Là où vivent les branches, les PR et les revues.
_À éviter_ : hébergeur de code, forge, repo

**Orchestrateur** :
Le composant qui reçoit les déclencheurs, démarre des exécutions sur les runners, et enregistre quelles exécutions sont en file, en cours ou terminées.
_À éviter_ : répartiteur, ordonnanceur, control plane

**Plateforme d'observabilité** :
Là où les runners envoient logs, traces et statut, pour qu'un humain voie ce qui s'est passé dans chaque exécution. Facultative : le produit qui remplit l'emplacement runner ou orchestrateur en montre souvent assez à lui seul. Une exécution ne prend jamais de décision à partir d'elle tant que quelque chose ne l'écrit pas dans le ticket tracker ou l'hébergeur git.
_À éviter_ : observabilité, logs, supervision, tableau de bord

## Concevoir une factory

Une conception a deux moitiés : les **services**, chacun un emplacement rempli par un produit, et les **tâches** dont les exécutions font avancer le travail à travers eux.

Partez du problème de l'utilisateur, pas de la factory : des tickets non triés, des PR en attente de revue automatisée, une vérification automatisée plus stricte vers laquelle migrer, des bugs que personne ne lit. Quand l'utilisateur nomme un problème ou ne sait pas quoi construire, lisez [EXAMPLE-TASKS.md](EXAMPLE-TASKS.md) pour les tâches qui résolvent les plus courants.

### Démarrer lentement

Chaque déclencheur est un accélérateur. Des accélérateurs sans freins font un canon à slop. Il y a trois freins :

- **Vérifications automatisées** : des barrières déterministes (types, lint, tests) que chaque exécution doit franchir.
- **Revue automatisée** : une exécution séparée qui lit le diff à froid au regard des standards de la base de code et du ticket, quand il y en a un.
- **Revue humaine** : un humain lit la PR avant son merge ; ce qu'elle attrape devient de nouvelles vérifications automatisées et une revue automatisée plus affûtée.

Ajoutez un déclencheur automatisé à la fois, en le limitant au début (un rapport de bug sur dix, une horloge une fois par jour), avec une revue humaine sur chaque PR. Ouvrez les vannes à mesure que les freins gagnent la confiance, et plafonnez chaque boucle d'exécutions à quelques essais avant un humain.

### Remplir chaque emplacement

Chaque service est un emplacement. Un produit peut remplir plusieurs emplacements, et un serveur peut héberger plusieurs services. Trois conceptions, côte à côte :

| Service                | All on GitHub                   | Linear tickets                 | Own tracker              |
| ---------------------- | ------------------------------- | ------------------------------ | ------------------------ |
| Ticket tracker         | GitHub Issues                   | Linear                         | application maison       |
| Source de tickets      | bouton de feedback              | bouton de feedback, moniteur   | bouton de feedback, moniteur |
| Hébergeur git          | GitHub                          | GitHub                         | GitHub                   |
| Orchestrateur          | GitHub Actions                  | serveur maison + base de données | application maison     |
| Runner                 | Actions runner                  | Actions, E2B, Daytona ou un VPS | application maison, ou sandboxes |
| Plateforme d'observabilité | aucune : les pages d'exécution d'Actions suffisent | une plateforme de traçage LLM | application maison |

Quand l'utilisateur choisit un produit pour un emplacement, lisez le panorama de cet emplacement pour vous orienter, puis recherchez vous-même les options actuelles ; chaque panorama regroupe les options par type et liste les questions qui décident de l'emplacement :

- [TICKET-TRACKERS.md](TICKET-TRACKERS.md)
- [GIT-HOSTS.md](GIT-HOSTS.md)
- [RUNNERS.md](RUNNERS.md)
- [OBSERVABILITY-PLATFORMS.md](OBSERVABILITY-PLATFORMS.md)

Achetez les services et construisez les tâches : les tâches portent la conception de l'utilisateur, les services sont génériques. Privilégiez un ticket tracker sur étagère : il est robuste, les gens le connaissent déjà, et les utilisateurs externes y entrent facilement.

### Interroger chaque tâche

Pour chaque tâche, répondez :

1. Qu'est-ce qui la déclenche ?
2. Que fait-elle, et quels services lit-elle et écrit-elle ?
3. Que déclenche-t-elle ensuite ?
4. Où un humain reprend-il la main ?
5. Qu'est-ce qui empêche deux exécutions de faire le même travail ?

Pour chaque source de tickets, répondez : qu'est-ce qui l'empêche de créer deux fois le même ticket ?

Une conception est terminée quand chaque emplacement nomme un produit (ou, pour la plateforme d'observabilité, une absence assumée) et que chaque tâche et chaque source de tickets a une réponse à chaque question.
