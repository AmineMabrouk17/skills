# Runners

> Panorama de 2026-10, sans garantie d'actualité. Servez-vous-en pour vous orienter, puis recherchez vous-même les options actuelles : produits, prix, limites et bêtas changent vite.

Un runner est **jetable** : chaque exécution démarre sur une machine neuve et ne laisse rien derrière elle. Cinq types remplissent l'emplacement. Ils diffèrent surtout par qui possède la boucle d'agent et qui possède la machine :

| Type | Boucle d'agent | Machine | Remplit aussi |
| --- | --- | --- | --- |
| Runner de CI | la vôtre, dans le job de CI | celle du fournisseur de CI, ou la vôtre | orchestrateur, plateforme d'observabilité |
| API de microVM | la vôtre, dans l'orchestrateur ou sur le runner | celle du fournisseur | rien |
| Plateforme d'agent intégrée | celle du fournisseur | celle du fournisseur | une partie de l'orchestrateur, la plateforme d'observabilité |
| Worker fourni par l'utilisateur | celle du fournisseur | la vôtre | une partie de l'orchestrateur, la plateforme d'observabilité |
| VPS ou conteneurs maison | la vôtre | la vôtre | rien |

Le choix du runner se fait par exécution : un runner de CI bon marché pour les exécutions de revue et une microVM pour les exécutions d'implémentation est une conception normale.

## Runners de CI

Une VM ou un pod neuf par job de CI, démarré par le moteur de workflow de l'hébergeur git lui-même ; la CLI de l'agent est une étape du job. L'orchestrateur démarre une exécution avec un déclencheur de workflow (issue, PR, planification, dispatch manuel), et un groupe de concurrence empêche deux exécutions de travailler sur le même ticket. Les runners hébergés sont jetables par construction ; les runners auto-hébergés y arrivent par un mode éphémère, un pod par job, supprimé ensuite. Le coût est à la minute.

**Choisissez-le quand** l'hébergeur git est GitHub ou GitLab et que chaque déclencheur est un événement de l'hébergeur git ou une horloge : le fichier de workflow est l'orchestrateur et la page d'exécution est la plateforme d'observabilité, donc un produit remplit trois emplacements.

Points de départ :
- [GitHub Actions](https://docs.github.com/en/actions/reference/limits) : le choix par défaut pour une factory tout-GitHub ; [tarifs](https://docs.github.com/en/billing/reference/actions-runner-pricing).
- [Claude Code GitHub Action](https://code.claude.com/docs/en/github-actions) : Claude Code comme étape de workflow.
- [GitLab hosted runners](https://docs.gitlab.com/ci/runners/hosted_runners/linux/) : une VM éphémère par job.
- [Actions Runner Controller](https://docs.github.com/en/actions/concepts/runners/actions-runner-controller) : auto-hébergé, un pod éphémère par job sur le Kubernetes de l'utilisateur.

## API de microVM

Un appel SDK crée une microVM isolée (les fournisseurs disent « sandbox ») en une seconde environ ; l'orchestrateur y exécute des commandes, ou y démarre une CLI d'agent. Le coût est à la seconde de vCPU et de RAM. L'orchestrateur en crée une par exécution, la tue à la fin, et fixe un timeout comme filet de sécurité. Les fournisseurs vendent aussi de la persistance (pause/reprise, snapshots, disques) ; une factory l'utilise comme cache, et garde la mémoire entre les exécutions dans le ticket tracker, l'hébergeur git et l'orchestrateur.

**Choisissez-le quand** la factory a son propre orchestrateur (un serveur, une file) et veut beaucoup d'exécutions parallèles et éphémères sans machines à gérer. Comparez sur la durée maximale d'exécution, le plafond de concurrence, et si le temps passé à attendre le modèle est facturé : en 2026-10, Vercel ne facture que le CPU actif, donc le temps d'attente du modèle y est presque gratuit.

Points de départ : [E2B](https://e2b.dev/pricing), [Daytona](https://www.daytona.io/pricing), [Modal Sandboxes](https://modal.com/docs/guide/sandboxes), [Vercel Sandbox](https://vercel.com/docs/vercel-sandbox/pricing).

## Plateformes d'agent intégrées

Un agent de code hébergé, runner inclus : le fournisseur provisionne une VM ou un conteneur neuf par exécution, y fait tourner sa propre boucle d'agent, et pousse une branche ou une PR. Seule la branche survit. Les exécutions démarrent depuis une @-mention ou une assignation dans l'hébergeur git ou le ticket tracker, un message de chat, une planification ou un appel HTTP, donc la plateforme remplit une bonne partie de l'orchestrateur ; sa vue de transcription remplit la plateforme d'observabilité. Le coût est fondu dans un siège ou un abonnement, ou facturé à l'exécution.

**Choisissez-la quand** l'utilisateur veut le moins de pièces mobiles possible et se satisfait de l'agent de ce fournisseur. Confirmez que ses déclencheurs couvrent le ticket tracker de l'utilisateur.

Points de départ :
- [Claude Code cloud](https://code.claude.com/docs/en/claude-code-on-the-web), avec des [tâches planifiées](https://code.claude.com/docs/en/routines) pour les déclencheurs cron, API et événements GitHub.
- [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) : API d'abord ; créez une exécution contre un environnement et streamez les événements.
- [Codex cloud](https://developers.openai.com/codex/cloud) : un conteneur par tâche.
- [Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent) : assignez une issue à Copilot ; son runner est un runner GitHub Actions.

## Worker fourni par l'utilisateur

La plateforme intégrée garde sa boucle d'agent et sa file d'exécutions, mais les appels d'outil s'exécutent sur un worker que l'utilisateur héberge. Le worker interroge le fournisseur en sortie, réclame une exécution, l'exécute et sort : la même forme qu'un runner de CI auto-hébergé. La jetabilité est le travail de l'utilisateur : faites tourner le worker sous Kubernetes ou via une API de microVM pour que chaque exécution ait un disque neuf. Le coût est le siège ou l'usage du fournisseur plus les machines de l'utilisateur.

**Choisissez-le quand** l'utilisateur veut un agent intégré mais que les exécutions doivent atteindre un réseau privé, garder les données sur ses machines, ou utiliser du matériel spécifique.

Points de départ : [environnements auto-hébergés de Claude Code](https://code.claude.com/docs/en/self-hosted-environments), [pool auto-hébergé de Cursor](https://cursor.com/docs/cloud-agent/self-hosted-pool), [Copilot coding agent sur ARC](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-environment).

## VPS ou conteneurs maison

Une machine que l'utilisateur loue ou exploite, avec un conteneur (ou une machine) neuf par exécution : la machine peut vivre des mois, le conteneur vit une exécution. La durée maximale d'exécution est l'échéance que fixe l'orchestrateur. La jetabilité vient de `docker run --rm`, d'un Job Kubernetes avec `ttlSecondsAfterFinished: 0`, ou d'une machine détruite à la fin ; chaque cache est sur un chemin que l'exécution suivante traite comme facultatif. Le coût est mensuel fixe, ou à la seconde pendant l'exécution.

**Choisissez-le quand** les exécutions sont longues, régulières ou lourdes, que le coût horaire domine, ou qu'un serveur héberge déjà l'orchestrateur.

Points de départ : [Fly Machines](https://docs.fly.io/machines/overview) ([tarifs](https://docs.fly.io/about/pricing)), [Hetzner Cloud](https://www.hetzner.com/cloud/) plus Docker, [Kubernetes Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/).

## Approfondir les recherches

### Les questions qui décident de l'emplacement

Confrontez chaque candidat à ces questions, les exécutions de la factory sous les yeux :

1. **Comment l'orchestrateur en démarre-t-il une ?** Un déclencheur de workflow, un appel SDK, une @-mention, un appel HTTP, ou une file du fournisseur qu'un worker réclame. Cela correspond-il à l'orchestrateur que la conception nomme déjà ?
2. **Quelle est la durée maximale d'exécution, et le plafond de concurrence ?** Trouvez à la fois le timeout par défaut et le plafond, par offre, et ce qui se passe au plafond. Confrontez-les à l'exécution la plus longue et à la rafale la plus chargée de la conception.
3. **Quelle est la forme du coût ?** À la minute, à la seconde de vCPU et de RAM, au CPU actif seulement, à l'exécution, au siège, ou mensuel fixe. Le temps d'attente du modèle est-il facturé ? Les jetons de modèle sont-ils dans le prix ou en plus ?
4. **Comment la jetabilité est-elle obtenue ?** Neuf par défaut, un drapeau éphémère, ou le démontage par l'orchestrateur lui-même. Quelles fonctionnalités de persistance existent, et la conception peut-elle les cantonner à des caches ?
5. **À quoi une exécution peut-elle accéder ?** Accès au réseau privé, pare-feu sortant ou allowlist, comment les secrets arrivent au runner, et qui d'autre peut les lire.
6. **Quels autres emplacements remplit-il ?** Comptez les déclencheurs qu'il lit comme couverture d'orchestrateur, et sa page d'exécution ou sa transcription comme couverture de plateforme d'observabilité.

### Où vivent les réponses

- La **page des limites ou quotas** et la **page de tarifs** du fournisseur, plutôt que la page d'accueil.
- La **référence SDK ou API** de l'appel de création : ses paramètres de timeout, de cycle de vie et de réseau.
- La **doc sécurité ou réseau** pour les pare-feux, les allowlists et la gestion des secrets.
- Le **changelog ou les notes de version** pour le statut bêta, preview et GA, et pour le verrouillage par offre (Team, Enterprise).
- Pour les runners de CI, la **doc de facturation** de l'hébergeur git.

### Ce qui se périme le plus vite

- **Les prix et les quotas gratuits.** En 2026-10, GitHub avait annoncé une redevance à la minute pour les runners auto-hébergés, puis l'avait repoussée.
- **Le statut bêta, preview et early access**, et quelles offres débloquent une fonctionnalité. En 2026-10, Claude Managed Agents et les environnements auto-hébergés de Claude Code étaient tous deux en bêta.
- **Les timeouts par défaut et la durée maximale d'exécution** par offre.
- **Les intégrations de déclencheurs** des plateformes intégrées : quels ticket trackers et quels outils de chat démarrent une exécution.
- **Les temps de démarrage**, qui sont des affirmations de fournisseur ; mesurez-les s'ils comptent.

Les traits de type ci-dessus (qui possède la boucle, qui possède la machine, quels emplacements elle remplit) changent lentement ; fiez-vous à eux plutôt qu'à n'importe quel chiffre ici.
