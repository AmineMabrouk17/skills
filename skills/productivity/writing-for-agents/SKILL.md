---
name: writing-for-agents
description: Écrire des documents pour les agents. À utiliser quand vous créez ou modifiez des skills, ou quand vous modifiez AGENTS.md ou CLAUDE.md.
---

Référence pour écrire tout document qu'un agent consomme : un skill, un `AGENTS.md` / `CLAUDE.md`, un doc atteint par un pointeur. L'emballage diffère ; l'écriture non : les mêmes leviers rendent chacun prévisible, puisque l'agent suit le même _processus_ à chaque exécution plutôt que de produire la même sortie.

Quand le document que vous écrivez est un skill, lisez [`SKILL-MECHANICS.md`](SKILL-MECHANICS.md) pour le frontmatter, le choix d'invocation et les skills routeurs.

## Pointeurs de contexte

Un **pointeur de contexte** est une référence tenue dans le contexte de l'agent, qui nomme une matière hors contexte et encode la condition pour l'atteindre. La description d'un skill en est un ; une ligne de `AGENTS.md` qui nomme un doc est le même objet. C'est la _formulation_ du pointeur, pas sa cible, qui décide quand l'agent atteint la matière, et avec quelle fiabilité. Une cible indispensable derrière un pointeur faiblement formulé est un bug de variance : affûtez d'abord la formulation, et ne placez la matière dans le corps que si l'affûtage échoue.

Un pointeur remplit deux fonctions : énoncer ce qu'est la matière, et lister les **branches** qui doivent déclencher l'accès à cette matière (une branche est un cas distinct que le document traite, de sorte que des exécutions différentes empruntent des chemins différents à travers lui). Chaque mot d'un pointeur toujours chargé coûte à chaque tour, il supporte donc un élagage encore plus dur que le corps :

- **Placez le mot directeur en tête** : le pointeur est là où il fait son travail de déclenchement.
- **Un déclencheur par branche.** Les synonymes qui renomment une seule branche sont une branche écrite deux fois ; fusionnez-les et ne gardez que les branches réellement distinctes.
- **Coupez l'identité que le corps porte déjà.**

## Les deux charges

Chaque document et chaque pointeur que vous ajoutez dépense l'un de deux budgets :

- La **charge de contexte** est le coût de la matière toujours chargée sur la fenêtre de l'agent : une ligne d'`AGENTS.md`, une description de skill, tout ce qui siège dans le contexte à chaque tour, dépensant des tokens et de l'attention qu'il se déclenche ou non.
- La **charge cognitive** est le coût pour l'humain : quels documents existent et quand recourir à chacun. L'humain est l'index. Ce n'est pas un coût à minimiser : c'est le prix de l'action humaine ; dépensez-la là où le jugement humain compte, retirez-la là où il ne compte pas.

La matière atteinte uniquement par un pointeur échappe à la charge de contexte au prix de la ligne du pointeur lui-même ; la matière sans aucun pointeur repose entièrement sur la charge cognitive.

## Hiérarchie de l'information

Un document est construit à partir de deux types de contenu : les **étapes** (les actions ordonnées que l'agent exécute) et la **référence** (définitions, règles, faits consultés à la demande). Les deux se mêlent librement : tout en étapes (une recette), tout en référence (les règles d'une revue, ce skill), ou les deux. La décision centrale est l'endroit où chaque pièce se situe sur la **hiérarchie de l'information**, une échelle classée par l'immédiateté du besoin qu'a l'agent de la matière :

1. **L'étape dans le fichier** est le niveau primaire : ce que l'agent fait, dans l'ordre.
2. **La référence dans le fichier** se consulte à la demande. Souvent un ensemble de pairs légitimement à plat (chaque règle d'une revue sur la même marche), ce qui est un agencement correct, pas une odeur.
3. **La référence divulguée** est repoussée dans un fichier séparé, atteint par un pointeur de contexte, chargé seulement quand le pointeur se déclenche. Elle va d'un fichier frère dans le même dossier jusqu'à une référence entièrement externe, qui vit n'importe où et que n'importe quel document peut viser.

Poussez trop peu vers le bas et le sommet gonfle ; poussez trop et vous cachez de la matière dont l'agent a réellement besoin. Cette tension, c'est toute la décision.

La **divulgation progressive** est le mouvement vers le bas de l'échelle (hors du fichier principal, derrière un pointeur) pour que le sommet reste lisible. Ce n'est pas d'abord une optimisation de tokens : c'est ainsi que la hiérarchie est protégée. Le branchement est le test de divulgation le plus net : mettez dans le corps ce dont chaque branche a besoin, et poussez derrière un pointeur ce que seules certaines branches atteignent. Quand un document a des étapes, une référence dans le fichier qui devrait être divulguée les enterre et transforme l'attention qu'on leur porte en pile ou face : un levier de variance, pas seulement de lisibilité.

La **co-localisation** est le pendant intra-fichier : là où l'échelle décide _à quelle profondeur_ une pièce se situe, la co-localisation décide _ce qui se trouve à côté d'elle_ une fois sur place. Gardez la définition d'un concept, ses règles et ses mises en garde sous un même titre plutôt que dispersées, de sorte que lire une partie amène ses voisines avec elle. Le test : le document doit se lire comme une documentation écrite pour l'agent. La matière groupée se lit ainsi ; la matière dispersée non. (À distinguer de la duplication : celle-ci répète un sens à deux endroits ; la dispersion fragmente un sens à travers beaucoup d'endroits.)

L'**étalement** est le mode d'échec ici : un document simplement trop long, même quand chaque ligne est vivante et unique. L'attention s'amincit sur l'excédent, et chaque ligne supplémentaire est une ligne de plus à garder pertinente. Le remède est l'échelle : divulguez la référence derrière des pointeurs, et scindez par branche ou par séquence pour que chaque chemin ne porte que ce dont il a besoin.

## Étapes et critères d'achèvement

Chaque étape se termine sur un **critère d'achèvement**, la condition qui dit à l'agent que le travail est fait. Deux propriétés en font un levier :

- La **clarté** : l'agent peut-il distinguer fait de non-fait ? Une borne vague (« compréhension atteinte ») invite à l'**achèvement prématuré** : terminer l'étape avant qu'elle ne soit réellement faite, l'attention glissant vers _être fait_. Les étapes encore visibles devant (les **étapes post-achèvement**) fournissent la traction ; la clarté du critère est la résistance. Défendez dans l'ordre : **affûtez d'abord la borne** (local et bon marché) ; seulement si elle est irréductiblement floue _et_ que vous observez la précipitation, cachez les étapes suivantes en scindant la séquence. Le masquage ne fonctionne qu'à travers une vraie frontière de contexte (une passation ou un dispatch de subagent ; un appel dans le même contexte laisse les étapes suivantes en place et ne dégage rien).
- L'**exigence** : combien elle requiert. « Chaque modèle modifié a été pris en compte » force un travail minutieux là où « produis une liste de changements » ne le fait pas. L'exigence pilote le **travail d'investigation** (le creusage que l'agent fait dans le travail, latent dans la formulation plutôt qu'écrit comme sa propre étape), et elle n'est pas liée à l'étape : « chaque règle appliquée » lie un corps de référence à plat tout comme « chaque étape faite » lie une séquence, ce qui fait qu'un document tout en référence porte encore une barre d'exhaustivité.

Les critères les plus forts sont à la fois vérifiables et exhaustifs.

## Quand scinder

Scinder un document en deux dépense l'une des deux charges, donc ne scindez que lorsque la coupe le mérite :

- **Par séquence** : scindez une suite d'étapes là où les étapes post-achèvement tentent l'agent de précipiter celle qui est devant lui. Les garder hors de vue pousse à plus de travail d'investigation sur la tâche en cours. Méfiez-vous de l'inverse : fusionner des séquences expose les étapes ultérieures de chaque étape à ce qui suit, invitant à l'achèvement prématuré.
- **Par invocation**, propre aux skills : voir [`SKILL-MECHANICS.md`](SKILL-MECHANICS.md).

## Mots directeurs

Un **mot directeur** est un concept compact qui vit déjà dans le pré-entraînement du modèle et avec lequel l'agent pense pendant qu'il exécute le document (_lesson_, _fog of war_, _tracer bullets_). Répété comme un token, jamais comme une phrase, il accumule une définition distribuée et ancre toute une région de comportement avec le moins de tokens possible, en recrutant des priors que le modèle détient déjà. Forger votre propre mot marche si vous le définissez clairement, mais un mot inventé ne recrute aucun prior : vous payez en tokens de définition ce qu'un mot pré-entraîné donne gratuitement ; cherchez d'abord un mot existant.

Il ancre deux fois. Dans le corps, l'_exécution_ : l'agent atteint le même comportement chaque fois que le mot apparaît, et au sein d'une référence à plat il concentre l'attention sur une classe de choses à chercher. Dans un pointeur, l'_invocation_ : quand le même mot vit dans vos prompts, vos docs et votre base de code, l'agent relie ce langage partagé à la matière et l'atteint plus fiablement.

Traquez les occasions de refactoriser avec des mots directeurs. Une triade épelée à trois endroits, un pointeur qui dépense une phrase à désigner une idée. Chacun est un passage qui demande à se replier en un seul token :

- « fast, deterministic, low-overhead » → _tight_ (une boucle _tight_).
- « a loop you believe in » → _red_, transformant une porte floue en un état observable binaire (la boucle devient _red_ sur le bug, ou elle ne le devient pas).

Vous gagnez deux fois : moins de tokens, et un crochet plus affûté auquel l'agent accroche sa réflexion. Supposez que tout document porte des reformulations que les mots directeurs peuvent retirer. Allez les trouver.

La **négation** est le mode d'échec voisin de ce levier : diriger par l'interdiction traîne le comportement interdit dans le contexte et le rend _plus_ disponible, pas moins. _Don't think of an elephant_, et l'éléphant est tout ce qui reste ; la négation est un modificateur faible que le concept fortement activé déborde, si bien que l'interdiction se lit à moitié comme l'instruction de faire la chose. Formulez le **positif** : énoncez le comportement cible (« écris des commentaires d'une ligne ») pour que l'interdit ne soit jamais prononcé. Une interdiction mérite sa place seulement comme garde-fou dur que vous ne pouvez pas formuler positivement ; même là, associez-la à la cible positive pour que l'attention atterrisse sur ce qu'il faut faire.

## Élagage

- Gardez chaque sens dans une **source unique de vérité** : un endroit qui fait autorité, pour que changer le comportement soit une modification en un seul endroit. La **duplication** (le même sens à plus d'un endroit) coûte en maintenance et en tokens, et gonfle la proéminence d'un sens sur l'échelle au-delà de son rang réel. (C'est l'inverse accidentel d'un mot directeur, qui répète un token exprès, jamais le sens.)
- L'**environnement** est aussi une source de vérité (les scripts de `package.json`, les fichiers de config, l'agencement des répertoires, la sortie de `--help`), et un document qui le reformule est un **cache** : une copie d'une recherche, qui ne mérite sa charge que lorsque la recherche est coûteuse. Mettez en cache ce que l'agent ne peut pas trouver en regardant : la convention non écrite, la raison derrière un choix, le piège qu'aucune config n'avoue. Laissez à l'environnement les recherches d'un fichier et d'une commande, là où elles ne peuvent pas devenir obsolètes.
- Vérifiez chaque ligne sous l'angle de la **pertinence** : concerne-t-elle encore ce que fait le document ? Une ligne perd sa pertinence en ne concernant jamais la tâche (pure exposition, ou une branche qui devrait être divulguée) ou en devenant obsolète à mesure que le comportement ou le monde qu'elle décrit change. Des documents plus courts sont plus faciles à garder pertinents. Sans discipline d'élagage, le destin par défaut est le **sédiment** : des couches obsolètes qui se déposent parce qu'ajouter semble sûr et retirer semble risqué, jusqu'à ce qu'il faille carotter à travers pour retrouver ce qui est encore vivant.
- Traquez les **no-ops** phrase par phrase : une instruction que le modèle obéit déjà par défaut paie une charge pour ne rien dire. Le test (change-t-elle le comportement par rapport au défaut ?) est relatif au modèle, pas au lecteur : deux personnes qui ne sont pas d'accord sur un no-op ne sont pas d'accord sur le défaut, et tranchent en exécutant le document, pas par débat. Quand une phrase échoue, supprimez la phrase entière plutôt que d'en rogner des mots. Le test note aussi les mots directeurs : un mot trop faible pour battre le défaut (_be thorough_ quand l'agent est déjà à peu près minutieux) est un no-op, et le correctif est un mot plus fort (_relentless_), pas une autre technique.
