# Ticket trackers

> Panorama de 2026-10, sans garantie d'actualité. Servez-vous-en pour vous orienter, puis recherchez vous-même les options actuelles : produits, prix, limites et bêtas changent vite.

Le ticket tracker est l'interface humaine de la factory. Chaque type ci-dessous est un point de départ : lisez de quoi il s'agit et quand une factory le choisit, puis confrontez les exemples aux questions de [Approfondir les recherches](#approfondir-les-recherches).

## Les types

### 1. Tracker de l'hébergeur git

Le tracker intégré à l'hébergeur git, pour que tickets, branches et PR partagent un produit, un modèle de permissions et une identité. Une factory le choisit quand elle est petite, que l'équipe vit déjà sur l'hébergeur git, ou que le dépôt est public. C'est la conception « All on GitHub » : la CI de l'hébergeur git remplit l'emplacement orchestrateur sans glu. Les labels sont le routeur fiable, parce que les statuts vivent sur un tableau séparé. Les développeurs externes créent leurs tickets là où est le code ; les non-développeurs ont besoin d'un compte, donc un bouton de feedback passe par l'API. Les hébergeurs git auto-hébergés emportent leur tracker avec eux.

- [GitHub Issues](https://docs.github.com/en/copilot/concepts/agents/about-third-party-agents) : le choix par défaut ; assignation à un agent pour Copilot, Claude et Codex.
- [GitLab Issues](https://about.gitlab.com/blog/how-to-use-a-work-item-created-trigger/) : des flux Duo Agent Platform déclenchés depuis les work items.
- [Forgejo](https://forgejo.org/2026-10-release-v17-0/) : auto-hébergé, compatible Actions.

À vérifier : quelles fonctionnalités d'assignation à un agent sont encore en preview et quelles offres les incluent ; si un agent natif lit les commentaires ultérieurs ou seulement le ticket au moment de l'assignation (en 2026-10, Copilot ne le lit qu'à l'assignation).

### 2. Tracker de dev natif pour agents

Un tracker SaaS autonome pour les équipes logiciel qui traite les agents comme des assignés de première classe, avec une API d'agent et son propre agent de code. Une factory le choisit quand les équipes produit, support ou design planifient le travail, que l'équipe y vit déjà, ou qu'elle veut des vues de triage et de planification soignées. La conception « Linear tickets ». Les statuts et les workflows sont de première classe, donc « passé à Ready » est un déclencheur propre. Les agents apparaissent comme des utilisateurs d'application à qui un humain délègue, et les exécutions peuvent diffuser leur progression dans le ticket. Les agents natifs y sont les plus profonds de tous les types, donc l'un d'eux peut aussi remplir en partie les emplacements orchestrateur et runner.

- [Linear](https://linear.app/developers/agents) : le tracker le plus façonné pour les agents.
- [Jira](https://support.atlassian.com/jira-software-cloud/docs/collaborate-on-work-items-with-ai-agents/) : des agents déclenchés par statut ; convient à une organisation déjà sur Atlassian.
- [Shortcut](https://www.shortcut.com/help/integrations/using-ai-agents) : assignez une story ou mentionnez un agent dans un commentaire.
- [YouTrack](https://www.jetbrains.com/youtrack/) : des workflows as code ; s'auto-héberge.

À vérifier : le statut de l'API d'agent (celle de Linear était en developer preview en 2026-10) ; comment les exécutions d'agent natif sont mesurées et facturées ; le coût du siège agent ; les délais d'accusé de réception des webhooks d'agent ; quelle offre débloque les formulaires destinés aux clients ; les dates de fin de vie des éditions auto-hébergées.

### 3. Tracker de dev open source

Un tracker façon Linear que vous pouvez faire tourner sur votre propre infrastructure, avec une édition cloud à côté. Une factory le choisit quand les données restent à la maison (air-gap, secteur régulé) ou que l'utilisateur veut lire et patcher le code du tracker tout en l'achetant. Le routage par états, labels et propriétés personnalisées fonctionne à peu près comme au type 2 ; les agents natifs sont plus jeunes.

- [Plane](https://developers.plane.so/dev-tools/agents/overview) : façon Linear ; utilisateurs bot par app ; les agents répondent en commentaires.
- [Huly](https://github.com/hcengineering/huly-selfhost) : espace de travail tout-en-un avec synchronisation GitHub bidirectionnelle ; une pile lourde à exploiter.
- [OpenProject](https://www.openproject.org/) : gestion de projet classique et mature.

À vérifier : le statut des fonctionnalités d'agent (celles de Plane étaient en bêta en 2026-10) ; quelle édition porte les rôles invité et les formulaires de collecte ; la licence ; le coût d'exploitation de la pile auto-hébergée.

### 4. Outil de travail généraliste

Un outil de gestion du travail ou de docs dont la base de données ou la liste de tâches fait aussi office de tracker. Une factory le choisit quand il sert une équipe non technique qui y vit déjà, ou que les tickets sont surtout du contenu, de l'ops ou de la recherche plutôt que du code. Les formulaires sont sa force : quiconque a un lien crée un ticket. Le routage passe par des propriétés de base de données flexibles, donc chaque factory invente son propre schéma. Le contexte développeur (diffs, PR) est à un clic sur l'hébergeur git. SaaS uniquement.

- [Notion](https://www.notion.com/help/custom-agents) : la base de données comme tracker ; les agents se déclenchent sur les changements de base.
- [Asana](https://asana.com/product/ai/ai-teammates) : les AI Teammates prennent les tâches assignées.
- [ClickUp](https://clickup.com/brain/cloud-agents) : les Cloud Agents renvoient une PR dans le fil de la tâche.
- [Trello](https://developer.atlassian.com/cloud/trello/guides/rest-api/webhooks/) : tableaux et listes ; webhooks plus automatisation Butler.

À vérifier : la latence et la granularité des webhooks (en 2026-10, Notion groupait les mises à jour de contenu, qui arrivaient une ou deux minutes en retard) ; quelle offre inclut les agents.

### 5. Tracker natif du dépôt

Des tickets stockés comme des fichiers dans le dépôt (JSONL, Markdown), édités par des agents via une CLI ou un serveur MCP et mergés comme du code. Une factory le choisit quand un développeur pilote des agents en local et veut le plan dans git, ou comme mémoire de travail d'une exécution à côté d'un vrai tracker. Un push vers l'hébergeur git est le seul déclencheur, donc l'orchestrateur calcule le diff des fichiers pour trouver ce qui a changé. L'identité est celle de l'hébergeur git. La création externe passe par un commit ou une PR, la plus faible de tous les types, donc une factory migre vers un tracker d'hébergeur git dès que d'autres créent des tickets.

- [Beads](https://github.com/steveyegge/beads) : du JSONL avec suivi des dépendances, pour qu'une exécution pioche le prochain ticket débloqué.
- [Backlog.md](https://github.com/MrLesk/Backlog.md) : des tâches Markdown avec une vue Kanban et un serveur MCP.

### 6. Construire le sien

L'application propre de la factory garde les tickets dans sa propre base de données, généralement à côté de l'orchestrateur. La conception « Own tracker ». Une factory le choisit quand le tracker *est* le produit (une plateforme interne, une usine de support client) ou qu'aucun modèle sur étagère ne convient. Les déclencheurs deviennent des appels de fonction ; chaque trait des questions ci-dessous devient du code à écrire et à maintenir, d'où la préférence du skill pour l'achat. Une base [Airtable](https://airtable.com/developers/web/api/webhooks-overview) avec des webhooks est un entre-deux à moitié construit.

## Approfondir les recherches

Confrontez chaque candidat à ces questions. Elles décident de l'emplacement :

- **Déclencheurs** : quels changements de ticket (créé, labellisé, statut déplacé, assigné, commenté, mentionné) émettent un webhook ou un événement que l'orchestrateur peut transformer en exécution ? À quelle vitesse et avec quelle granularité ?
- **Routage** : quels labels, statuts ou champs une exécution peut-elle lire pour connaître son genre (explore, implement, rework) ?
- **Fil** : un humain peut-il répondre à un agent en commentaires, et cette réponse déclenche-t-elle l'exécution suivante ?
- **Identité** : existe-t-il une API plus une identité de bot, pour que les exécutions agissent en leur nom et que les humains distinguent l'agent du collègue ? Le bot coûte-t-il un siège ?
- **Agents natifs** : un ticket peut-il être assigné directement à un agent ? Quels agents, sur quelle offre, mesurés comment ? Que lit-il : le ticket à l'assignation, ou tout le fil ?
- **Création externe** : comment une source de tickets ou un client crée-t-il des tickets : formulaires, email, chat, API ? Quelle offre le débloque ?
- **Hébergement** : SaaS uniquement, ou auto-hébergé, et sous quelle licence ?

Les réponses vivent dans les sources primaires : la référence de l'API et des webhooks (listes d'événements, payloads, règles de livraison et de retry), la doc développeur de l'agent ou de l'app, la page de tarifs et le tableau de comparaison des offres, et le changelog ou les notes de version. Privilégiez-les aux billets de blog et aux sites de comparaison.

Ce qui se périme d'abord : les fonctionnalités d'agent et leur statut preview ou bêta, la mesure des agents et le prix des crédits, le verrouillage des agents et des formulaires par offre, les limites de débit, et les acquisitions, fermetures et dates de fin de vie des éditions auto-hébergées. Les listes d'événements de webhook et les modèles de routage changent lentement.

## Pièges durables

Vérifiez que chacun tient toujours avant de vous appuyer dessus :

- **Le piège du `GITHUB_TOKEN` de GitHub** : les changements faits avec le `GITHUB_TOKEN` par défaut ne déclenchent aucune nouvelle exécution de workflow. Une exécution qui labellise ou commente un ticket pour déclencher l'exécution suivante agit via un jeton d'app GitHub.
- **Le contexte au moment de l'assignation** : certains agents natifs ne lisent le ticket qu'à l'assignation, donc le contexte ultérieur va sur la PR.
- **Les webhooks d'agent attendent un accusé de réception rapide** : l'orchestrateur accuse réception tout de suite et démarre l'exécution ensuite.
- **Les webhooks groupés des outils de travail généralistes** arrivent en retard ; une factory qui a besoin d'exécutions rapides déclenche sur les changements de propriété ou choisit un autre type.
