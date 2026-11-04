---
name: wayfinder
description: Planifiez un gros morceau de travail (plus qu'une session d'agent ne peut en contenir) comme une carte partagée de decision tickets sur votre issue tracker, et résolvez-les un par un jusqu'à ce que le chemin vers la destination soit clair.
disable-model-invocation: true
---

Une idée vague est arrivée, trop grande pour une seule session d'agent, et enveloppée de brouillard : le chemin d'ici à la **destination** n'est pas encore visible. Le wayfinding consiste à trouver ce chemin, pas à foncer vers la destination. Ce skill trace le chemin sous forme de **carte partagée** sur l'issue tracker du repo, puis traite ses **decision tickets** (des questions dont la résolution est une décision, pas des tranches de construction à exécuter) un par un jusqu'à ce que la route soit claire.

La destination varie selon l'effort, et la nommer est le premier acte du traçage : elle façonne chaque ticket. Cela peut être une spec à transmettre et à itérer, une décision à verrouiller avant le début de la planification, ou un changement réalisé sur place comme une migration de structure de données. La carte est agnostique du domaine : travail d'ingénierie, contenu de cours, tout ce qui correspond à la forme.

## Planifier, pas exécuter

Le wayfinder est de la **planification** par défaut : chaque ticket résout une décision, et la carte est terminée lorsque le chemin est clair, sans rien laisser à décider avant que quelqu'un aille faire la chose. L'envie de simplement faire le travail est généralement le signal que vous avez atteint le bord de la carte et qu'il est temps de transmettre. Un effort peut passer outre dans ses **Notes**, en portant l'exécution dans la carte elle-même, mais à défaut, produisez des décisions, pas des livrables.

## Désigner par le nom

Chaque carte et chaque ticket est une issue, donc elle a un **nom** : son titre. Dans tout ce que l'humain lit (narration, Decisions so far de la carte), désignez-la par ce nom, jamais par un id, un numéro ou un slug nu. Un mur de `#42, #43, #44` est illisible ; les noms se lisent d'un coup d'œil. L'id et l'URL ne disparaissent pas ; un nom enveloppe son lien, mais ils voyagent _à l'intérieur_ du nom, jamais à sa place.

## La carte

La carte est une issue unique sur l'issue tracker de ce repo, labellisée `wayfinder:map`, l'artefact canonique. Ses tickets sont des issues enfants de la carte.

La carte est un **index**, pas un stockage. Elle liste les décisions prises et pointe vers les tickets qui détiennent leur détail ; une décision vit à exactement un endroit, son ticket, donc la carte ne la reformule jamais, elle n'en donne que l'essentiel et le lien.

**L'endroit où vivent physiquement la carte, ses tickets enfants, le blocage et les requêtes de frontière dépend du tracker.** L'issue tracker devrait vous avoir été fourni. Sinon, demandez à l'utilisateur d'exécuter `/setup-matt-pocock-skills`. Consultez la section « Wayfinding operations » du doc du tracker pour savoir comment _ce_ repo les exprime. Si aucun tracker n'a été fourni, utilisez par défaut le tracker markdown local.

### Le corps de la carte

Toute la carte à basse résolution, chargée une fois par session. Les tickets ouverts ne sont **pas** listés : ce sont des issues enfants ouvertes, trouvées par requête.

```markdown
## Destination

<ce à quoi ressemble l'arrivée au bout de cette carte : la spec, la décision ou le changement vers lequel cet effort cherche son chemin. Une ou deux lignes ; chaque session s'y oriente avant de choisir un ticket.>

## Notes

<domaine ; skills que chaque session doit consulter ; préférences permanentes pour cet effort>

## Decisions so far

<!-- l'index : une ligne par ticket fermé, assez pour juger de la pertinence, puis zoomez le lien pour le détail que porte le ticket -->

- [<titre du ticket fermé>](link): <résumé en une ligne de la réponse>

## Not yet specified

<!-- voir « Fog of war » : brouillard dans le périmètre que vous ne pouvez pas encore transformer en ticket ; il en sort à mesure que la frontière avance -->

## Out of scope

<!-- voir « Hors périmètre » : travail jugé au-delà de la destination ; fermé, jamais promu -->
```

### Tickets

Chaque ticket est une **issue enfant** de la carte ; l'id d'issue du tracker est son identité. Son corps est la question, dimensionnée pour une session d'agent de 100K tokens :

```markdown
## Question

<la décision ou l'investigation que ce ticket résout>
```

Chaque ticket porte un label `wayfinder:<type>`, l'un de `research`, `prototype`, `grilling`, `task` (voir [Types de tickets](#types-de-tickets)). Les labels `wayfinder:` sont les seuls labels que portent une carte et ses tickets, jamais un label de triage comme `ready-for-agent` : ce sont des décisions, pas du travail d'implémentation.

Une session **revendique** un ticket en l'assignant au développeur qui pilote la carte, **d'abord**, avant tout travail, afin que les sessions concurrentes l'ignorent. Cet assigné _est_ la revendication : un ticket ouvert et non assigné est non revendiqué.

Le blocage utilise la relation de dépendance **native** du tracker : essentielle car elle rend la frontière _visuellement_ dans l'UI du tracker, si bien que l'humain voit ce qui est prenable sans ouvrir la carte. Seul un tracker dépourvu de blocage natif se rabat sur une convention dans le corps. Un ticket est **débloqué** lorsque tous les tickets qui le bloquent sont fermés ; la **frontière** est l'ensemble des enfants ouverts, débloqués et non revendiqués, le bord du connu.

La réponse ne fait pas partie du corps ; elle est enregistrée à la résolution (voir [Parcourir la carte](#parcourir-la-carte)). Les assets créés pendant la résolution d'un ticket sont liés depuis l'issue, pas collés dedans.

## Types de tickets

Chaque ticket est soit **HITL** (human in the loop, travaillé _avec_ un humain qui parle pour lui-même) soit **AFK**, piloté par l'agent seul. Un ticket HITL ne se résout que par cet échange en direct ; l'agent ne se substitue jamais à la part humaine (un agent de grilling qui répond à ses propres questions a enfreint cette règle).

- **Research** (AFK) : lire de la documentation, des API tierces ou des ressources locales comme des bases de connaissances pour faire remonter un fait dont une décision dépend. Résolu par un subagent qui appelle l'outil Skill avec « research ». À utiliser lorsque des connaissances extérieures au répertoire de travail actuel sont nécessaires.
- **Prototype** (HITL) : élever la fidélité de la discussion en fabriquant un artefact concret, bon marché et brut auquel réagir (un plan, une ébauche, un stub, ou du code UI/logique) en appelant l'outil Skill avec « prototype ». Lie le prototype comme asset. À utiliser lorsque « à quoi cela devrait ressembler » ou « comment cela devrait se comporter » est la question clé.
- **Grilling** (HITL) : conversation. Le cas par défaut. Appelez toujours l'outil Skill deux fois, pour « grilling » et « domain-modeling ».
- **Task** (HITL ou AFK) : travail manuel qui doit avoir lieu avant qu'une _décision_ puisse être prise : rien à décider, à prototyper ou à rechercher, mais la discussion est bloquée jusqu'à ce que ce soit fait. S'inscrire à un service pour que son API puisse être jugée, provisionner un accès, déplacer des données pour que leur forme soit visible. C'est le seul type qui _fait_ plutôt que décide, et il mérite sa place en débloquant une décision, pas en livrant la destination. L'agent le pilote seul quand il peut (AFK) ; sinon il remet à l'humain une checklist précise (HITL). Résolu lorsque le travail est fait ; la réponse enregistre ce qui a été fait et les faits qui en résultent (emplacement des identifiants, nouvelles URL, nombre de lignes) dont dépendent les tickets ultérieurs.

## Brouillard de guerre

La carte est _délibérément_ incomplète : ne tracez pas ce que vous ne pouvez pas encore voir. Au-delà des tickets actifs se trouve le **brouillard de guerre** : la vue floue des décisions et investigations dont vous devinez qu'elles arrivent mais que vous ne pouvez pas encore cerner, parce qu'elles dépendent de questions encore ouvertes. Résoudre un ticket dissipe le brouillard devant lui, faisant passer ce qui est désormais spécifiable en nouveaux tickets, un par un, jusqu'à ce que le chemin vers la destination soit clair et qu'il ne reste aucun ticket.

La section **Not yet specified** de la carte est l'endroit où cette vue floue est consignée : la question soupçonnée, la zone à revisiter plus tard. C'est la frontière non découverte _vers_ la destination : tout y est dans le périmètre, mais pas assez net pour en faire un ticket. Écrivez aussi librement ou aussi complètement que la vue le permet ; cela sert aussi de panneau indicateur pour les collaborateurs qui lisent où va l'effort.

**Brouillard ou ticket ?** Le test consiste à savoir si vous pouvez énoncer la question précisément maintenant, _pas_ si vous pouvez y répondre maintenant.

- **Ticket quand** la question est déjà nette, même si elle est bloquée et que vous ne pouvez pas encore agir dessus.
- **Not yet specified quand** vous ne pouvez pas encore la formuler aussi nettement. Ne pré-découpez pas le brouillard en morceaux de la taille d'un ticket : il est plus grossier qu'un ticket, et une parcelle peut se transformer en plusieurs tickets, ou en aucun, une fois que la frontière l'atteint.

**Not yet specified** exclut ce qui est déjà décidé (Decisions so far), ce qui est déjà un ticket actif, et ce qui est hors périmètre (la section suivante).

## Hors périmètre

Le brouillard ne s'accumule que _vers_ la destination. La destination fixe le périmètre, donc le travail au-delà est **hors périmètre** : ce n'est pas du brouillard, et cela n'a pas sa place dans **Not yet specified**. Il a sa propre section **Out of scope** sur la carte : le travail que vous avez consciemment exclu de _cet_ effort. C'est le périmètre, pas la netteté, qui le place ici.

Le travail hors périmètre ne se transforme jamais (la frontière s'arrête à la destination), donc il ne revient que si la destination est redessinée, et alors comme un nouvel effort, pas comme une reprise.

Exclure quelque chose du périmètre est un acte de cadrage, pas une étape sur la route. Lorsqu'un ticket déjà existant s'avère se situer au-delà de la destination (mal cadré pendant le traçage, ou révélé par une résolution), **fermez-le** (un ticket fermé est sans ambiguïté hors de la frontière) et laissez une ligne dans la section **Out of scope** : l'essentiel plus la raison pour laquelle il est hors périmètre, avec un lien vers le ticket fermé. Il reste hors de **Decisions so far**, qui enregistre la route réellement parcourue ; une limite de périmètre n'est pas une étape de celle-ci.

## Invocation

Deux modes. Dans les deux cas, **ne résolvez jamais plus d'un ticket par session**, à l'exception des tickets de research.

### Tracer la carte

L'utilisateur invoque avec une idée vague.

1. **Nommer la destination.** Appelez l'outil Skill deux fois, pour « grilling » et « domain-modeling », afin de cerner ce que cette carte cherche à rejoindre : la spec, la décision ou le changement. La destination fixe le périmètre, donc elle est réglée en premier.
2. **Cartographier la frontière.** Grillez à nouveau, **en largeur d'abord** cette fois : déployez-vous sur tout l'espace plutôt que profondément sur un seul fil, en faisant remonter les décisions ouvertes et aux premières étapes prenables maintenant. **Si cela ne fait remonter aucun brouillard** (le chemin vers la destination est déjà clair, le voyage entier assez petit pour une session), vous n'avez pas besoin de carte. Arrêtez et demandez à l'utilisateur comment il souhaite procéder.
3. **Créer la carte** (label `wayfinder:map`) : Destination et Notes remplies, Decisions so far vide, le brouillard esquissé dans **Not yet specified**.
4. **Créer les tickets que vous pouvez spécifier maintenant** comme issues enfants de la carte, puis câbler les arêtes de blocage dans une **seconde passe** (les issues ont besoin d'ids avant de pouvoir se référencer). Écrivez aussi les références croisées dans cette passe, avec de vrais ids : un espace réservé `#<n>` se lie automatiquement à une issue sans rapport. Le câblage les trie entre la frontière et les bloqués ; tout ce que vous ne pouvez pas encore spécifier reste dans le brouillard : la section **Not yet specified**.
5. **Lancer les subagents de research.** Pour chaque ticket `research` que vous venez de créer, lancez un subagent avec une fenêtre de contexte fraîche qui appelle l'outil Skill avec « research » pour le résoudre en parallèle, en capturant ses conclusions sur une branche jetable `research/<name>` avec un pointeur de contexte depuis le ticket. Poussez la branche mais n'ouvrez aucune PR : elle n'est jamais mergée.
6. Arrêtez : le traçage est le travail d'une session ; il ne résout rien de lui-même.

### Parcourir la carte

L'utilisateur invoque avec une carte (URL ou numéro). Un ticket est **facultatif** : sans ticket, c'est vous qui choisissez la prochaine décision, pas l'utilisateur.

1. Chargez la **carte** : la vue basse résolution, pas le corps de chaque ticket.
2. Choisissez le ticket. Si l'utilisateur en a nommé un, utilisez-le. Sinon, prenez le premier ticket de la frontière dans l'ordre. **Revendiquez-le** : assignez-le-vous avant tout travail.
3. Résolvez-le selon le type que nomme son label `wayfinder:<type>` (voir [Types de tickets](#types-de-tickets)). Lisez le label, pas seulement le corps : le corps n'énonce jamais le type. **Zoomez au besoin** : récupérez le corps complet de tout ticket lié ou fermé à la demande ; appelez l'outil Skill pour les skills nommés dans le bloc `## Notes`. En cas de doute, appelez l'outil Skill deux fois, pour « grilling » et « domain-modeling ».
4. Enregistrez la résolution : publiez la réponse comme **commentaire de résolution**, **fermez** l'issue, et **ajoutez un pointeur de contexte** aux Decisions so far de la carte.
5. Ajoutez les tickets nouvellement apparus (créer puis câbler) ; faites passer tout brouillard que la réponse a rendu spécifiable, en retirant chaque parcelle transformée de **Not yet specified** afin qu'elle ne vive que comme son nouveau ticket. Si la réponse révèle qu'un ticket (celui-ci ou un autre) se situe au-delà de la destination, **excluez-le du périmètre** plutôt que de le résoudre sur la route. Si la décision invalide d'autres parties de la carte, mettez à jour ou supprimez ces tickets.

L'utilisateur peut exécuter des tickets débloqués en parallèle, donc attendez-vous à ce que d'autres sessions modifient le tracker en même temps.
