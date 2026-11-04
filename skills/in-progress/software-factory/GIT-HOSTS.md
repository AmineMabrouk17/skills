# Hébergeurs git

> Panorama de 2026-10, sans garantie d'actualité. Servez-vous-en pour vous orienter, puis recherchez vous-même les options actuelles : produits, prix, limites et bêtas changent vite.

## Les types

### Plateformes tout-en-un

L'hébergeur git fourni avec un ticket tracker, un système de CI qui sert d'orchestrateur et des runners hébergés, le tout partageant un flux d'événements et un modèle de permissions. Un produit remplit l'hébergeur git, le ticket tracker, l'orchestrateur et le runner, et ses pages d'exécution de CI couvrent souvent aussi la plateforme d'observabilité. Chaque événement de PR, de revue, de label et de vérification est à la fois un webhook et un déclencheur natif, donc ce type a le catalogue de déclencheurs le plus riche.

**Choisissez-le quand** la factory démarre petit, que le code y vit déjà, ou que l'utilisateur veut une seule facture et un seul login. C'est la conception « All on GitHub » de [SKILL.md](SKILL.md). Les fournisseurs livrent aussi leurs propres agents, qui soit concurrencent les exécutions de l'utilisateur, soit s'insèrent comme l'une d'elles.

- [GitHub](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api) : le choix par défaut ; Issues, Actions, Apps et le plus grand écosystème d'intégrations.
- [GitLab](https://about.gitlab.com/pricing/) : issues, merge requests, CI/CD et runners dans un seul produit, en SaaS ou auto-hébergé.

### Hébergeurs git de suite

Un hébergeur git vendu comme un module d'une suite plus large, où le ticket tracker est un produit frère (Jira, Azure Boards) relié par des liens et des webhooks. Les événements du tracker arrivent sur un second flux, donc l'orchestrateur joint deux sources d'événements.

**Choisissez-le quand** l'organisation fait déjà tourner la suite et que ses humains vivent dans Jira ou Azure Boards. La factory hérite de ce tracker comme interface humaine.

- [Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/api-request-limits/) : l'hébergeur git d'Atlassian, associé à Jira et Bitbucket Pipelines.
- [Azure DevOps (Azure Repos)](https://learn.microsoft.com/en-us/azure/devops/integrate/concepts/rate-limits) : Repos, Boards, Pipelines et identités Entra dans une seule organisation, donc il remplit les quatre emplacements.
- [AWS CodeCommit](https://aws.amazon.com/blogs/devops/aws-codecommit-returns-to-general-availability/) : un hébergeur git nu avec des identités IAM ; associez-le à un tracker et une CI externes.

### Hébergeurs git auto-hébergés

Un hébergeur git que l'utilisateur fait tourner sur sa propre infrastructure, souvent à côté de l'orchestrateur et des runners sur le même réseau. L'exploitant fixe les limites de débit, donc le plafond, c'est le matériel. L'utilisateur assume les mises à jour, les sauvegardes et la disponibilité de chaque emplacement rempli.

**Choisissez-le quand** le code doit rester sur site ou en air-gap, que l'utilisateur veut zéro coût par siège, ou qu'un budget d'API SaaS bride une factory chargée. Forgejo et Gitea conviennent aux petites équipes et regroupent les issues avec une CI compatible GitHub Actions ; GitLab auto-hébergé et GitHub Enterprise Server conviennent aux organisations et conservent leurs Apps et comptes de service.

- [Forgejo](https://forgejo.org/2026-10-release-v17-0/) : gouverné par la communauté, léger, avec Forgejo Actions.
- [Gitea](https://about.gitea.com/) : léger, avec Gitea Actions ; aussi vendu en managé.
- [GitLab auto-hébergé](https://about.gitlab.com/install/) et [GitHub Enterprise Server](https://docs.github.com/en/enterprise-server@latest) : les plateformes complètes sur l'infrastructure de l'utilisateur.

### Hébergeurs git orientés revue et décentralisés

Des hébergeurs git dont le modèle de revue s'écarte de la forme branche-et-PR : des patchsets par commit avec des labels votés, ou des dépôts fédérés sur des nœuds opérés par les utilisateurs. L'outillage de factory suppose le plus souvent des PR, alors attendez-vous à écrire davantage de glu. Dans Gerrit, une exécution de vérification automatisée vote `Verified` depuis un compte bot et un humain vote `Code-Review`.

**Choisissez-le quand** l'équipe de l'utilisateur révise déjà de cette façon, ou privilégie la fédération sur l'écosystème.

- [Gerrit](https://www.gerritcodereview.com/) : revue par patchset avec votes de labels ; le modèle d'Android et de Chromium.
- [Tangled](https://blog.tangled.org/ci/) : hébergement git sur l'AT Protocol avec des PR empilées et son propre runner de CI.

### Hébergeurs git natifs pour agents

De nouveaux hébergeurs git construits autour des agents comme auteurs principaux : débit de commits élevé, lectures côté serveur à la place des clones, et attribution par agent. Le type entier est jeune (en 2026-10), donc les barrières et les limites de débit peuvent ne pas être publiées : demandez au fournisseur.

**Choisissez-le quand** la factory lance beaucoup d'exécutions concurrentes sur un grand dépôt et que le temps de clone ou les limites d'API dominent. Testez-en un sur un miroir d'un seul dépôt, en gardant GitHub comme repli.

- [Cursor Origin](https://cursor.com/origin) : de l'équipe Graphite, avec synchronisation GitHub et webhooks signés de PR, de revue et de push.
- [GitLab next-gen SCM](https://about.gitlab.com/blog/gitlab-next-gen-scm/) : backend GitLab reconstruit, avec des API d'agent sans clone.

### Stockage git pour les exécutions

Des dépôts git sans interface, pensés API d'abord, créés par milliers : branches, commits et diffs, sans PR, revues ni barrières. Ils remplissent la moitié de l'emplacement, avec des jetons éphémères par dépôt frappés à chaque exécution.

**Choisissez-le quand** les exécutions ont besoin de dépôts de travail ou de forks bon marché par exécution (un fork par ticket, l'état de travail d'un agent) et que la PR atterrit toujours sur un hébergeur git complet parmi les types ci-dessus.

- [Cloudflare Artifacts](https://www.cloudflare.com/product/artifacts/) : stockage compatible git sur Workers ; forkez une base par tâche.
- [Pierre code.storage](https://code.storage/docs) : API git en marque blanche avec des branches éphémères.

## Approfondir les recherches

Confrontez chaque candidat à ces questions :

1. **Déclencheurs** : quels événements (PR ouverte, push, revue soumise, label ajouté, vérification terminée) atteignent l'orchestrateur sous forme de webhooks, et les livraisons sont-elles signées ?
2. **Identité** : une exécution peut-elle agir avec une identité de bot ou d'app dédiée ? Cela garde les exécutions attribuables, permet à une exécution de revue automatisée d'approuver une PR écrite par une autre identité, et décide si les pushs d'une exécution déclenchent l'exécution suivante.
3. **Barrières** : les vérifications requises, les approbations requises et la protection de branche sont-elles disponibles sur l'offre et la visibilité de dépôt de l'utilisateur, et un bot peut-il figurer sur une liste de contournement ?
4. **Limites de débit** : quel budget d'API chaque exécution sur chaque runner partage-t-elle, par quelle unité (installation, jeton, utilisateur, workspace), et comment évolue-t-il ?
5. **Emplacements remplis** : lesquels parmi le ticket tracker, l'orchestrateur, le runner et la plateforme d'observabilité le même produit couvre-t-il ?

Cherchez les réponses dans les sources primaires : la référence de l'API REST et des webhooks, la page des limites de débit, la page de tarifs et de comparaison des offres, la doc des jetons d'accès ou des apps, et le changelog ou les notes de version.

Ce qui se périme le plus vite : le statut bêta et preview (tout le type natif pour agents et celui du stockage git), les chiffres de limites de débit, l'offre qui débloque les barrières, et la disponibilité après acquisitions, fermetures et réouvertures. En 2026-10, par exemple, AWS CodeCommit avait rouvert aux nouveaux clients et Bitbucket Data Center n'était vendu que sous licence hybride avec Cloud.

## Pièges durables

Vérifiez que chacun tient toujours avant que la conception s'appuie dessus.

- **Le `GITHUB_TOKEN` de GitHub coupe les chaînes.** Les événements causés par le jeton Actions intégré ne démarrent aucune nouvelle exécution de workflow (sauf `workflow_dispatch` et `repository_dispatch`), donc une exécution qui pousse avec lui arrête silencieusement l'exécution suivante. Donnez aux exécutions une identité d'app GitHub dès le premier jour.
- **Les barrières sont souvent derrière une offre payante.** En 2026-10, les rulesets GitHub sur les dépôts privés exigent Pro, Team ou Enterprise, les règles d'approbation GitLab exigent Premium, et les merge checks Bitbucket imposés exigent Premium.
- **Les limites de débit sont par identité, et le type de bot les change.** En 2026-10, une installation d'app GitHub obtient 5 000 requêtes/heure contre 1 000 pour `GITHUB_TOKEN` ; les jetons utilisateur Bitbucket restent au taux de base tandis que les jetons d'accès montent en charge ; Azure DevOps augmente le budget d'un bot avec son niveau d'accès.
- **Les trackers de suite impliquent deux flux d'événements.** L'orchestrateur joint lui-même les événements de l'hébergeur git et du ticket tracker.
