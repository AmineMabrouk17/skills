---
name: ask-matt
description: Demandez quel skill ou quel flux correspond à votre situation. Un routeur sur les skills de ce repo.
disable-model-invocation: true
---

# Demandez à Matt

Vous ne vous souvenez pas de chaque skill, alors demandez.

Avant d'affirmer ce que fait un skill ou de recommander de sauter une étape, lisez le SKILL.md de ce skill : les résumés ici servent seulement à s'orienter.

Un **flux** est un chemin à travers les skills. La plupart des chemins suivent un **flux principal**, et deux **voies d'accès** s'y fondent. Tout le reste est autonome, ou une couche de vocabulaire qui court en dessous.

## Le flux principal : idée → livraison

La route que suit la majorité du travail. Vous avez une idée et vous voulez la voir construite.

1. **`/grill-with-docs`** affine l'idée par entretien. Commencez ici dès que vous **travaillez dans un répertoire de travail** : il est à état, et retient ce qu'il apprend dans `GLOSSARY.md` et les ADR. (Pas de répertoire de travail ? Utilisez plutôt `/grill-me`, couvert dans Autonome. Les deux exécutent la même primitive `/grilling` ; `grill-with-docs` est celui qui laisse une trace écrite, ce qui en fait le meilleur des deux dès qu'il y a un repo où la laisser.)
2. **Bifurcation : pouvez-vous régler chaque question en conversation ?** Si une question demande une réponse exécutable (état, logique métier, une UI que vous devez voir), faites un détour par un prototype, relié par **`/handoff`** dans les deux sens (un prototype vit dans son propre répertoire, ce qui est exactement la raison d'être de `/handoff` ; voir Frontières de phase) :
   - **`/handoff`** sortant, puis ouvrez une session fraîche sur ce fichier,
   - **`/prototype`** pour répondre à la question avec du code jetable,
   - **`/handoff`** de retour avec ce que vous avez appris, et référencez-le depuis le fil d'idée d'origine.
3. **Bifurcation : est-ce une construction sur plusieurs sessions ?**
   - **Oui** → **`/to-spec`** (transformer le fil en spec), puis **`/to-tickets`** pour le découper en tickets tracer-bullet, chacun déclarant ses **arêtes de blocage**. Ensuite, travaillez les tickets de l'une de deux façons :
     - **`/implement`** par ticket, en faisant un `/clear` du contexte entre chacun. Sur un tracker local, c'est un fichier par ticket sous `.scratch/<feature>/issues/`, travaillé à la main en commençant par les bloqueurs ; sur un vrai tracker, les arêtes deviennent des liens de blocage natifs, donc tout ticket dont les bloqueurs sont faits peut être pris. Chaque ticket est autonome, donc le contexte du dernier est jetable.
     - **`/implement-spec`** pour toute la spec en une seule passe. Il lit les tickets comme un **graphe de tâches**, fait tourner des subagents implémenteurs sur la **frontière** prête en parallèle, et pose tout sur une seule **branche d'intégration**. Prenez-le quand vous préférez orchestrer la construction plutôt que piloter chaque ticket vous-même.
   - **Non** → **`/implement`** ici même, dans la même fenêtre de contexte.

   Dans les deux cas, le code se construit en pilotant **`/tdd`** (une tranche red-green à la fois) et se termine par **`/code-review`**, une revue à deux axes (Standards + Spec) du diff. `/implement` exécute les deux par ticket ; les implémenteurs de `/implement-spec` pilotent chacun `/tdd`, et il exécute un `/code-review` sur la branche d'intégration. Prenez **`/tdd`** seul quand vous voulez juste construire un comportement concret en test-first sans spec complète, et **`/code-review`** seul dès que vous voulez revoir une branche ou une PR par rapport à un point fixe.

   Quand le travail monte en pull request, **`/pr`** façonne le corps : le plus petit visuel qui montre le changement, des preuves avant/après que ça marche, et un verdict porte à sens unique ou porte à double sens. Il est invoqué par le modèle, donc l'agent y recourt dès qu'il écrit une PR.

4. **`/retro`** ferme la boucle. Après une construction, et surtout une qui a mal tourné, il revient sur la session et suggère des changements à l'**environnement** de l'agent, pas au code : des pointeurs de navigation, des vérifications automatisées, les standards de code que `/code-review` applique, des fichiers de pilotage, de l'outillage. Les erreurs mécaniques deviennent des vérifications déterministes ; les jugements deviennent des standards de code. La construction suivante part alors d'un meilleur environnement.

### Hygiène du contexte

Gardez les étapes 1–3 dans **une seule fenêtre de contexte ininterrompue** (ne compactez ni ne videz avant `/to-tickets`) pour que le grilling, la spec et les tickets s'appuient tous sur la même réflexion. Chaque `/implement` démarre ensuite à neuf, en travaillant depuis le ticket. Lancez `/retro` dans la session sur laquelle il revient, avant de vider ; après avoir vidé, pointez-le plutôt vers le journal de cette session.

La limite ici est la **[smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)** : la fenêtre (~150k tokens sur les modèles de pointe) dans laquelle le modèle raisonne encore avec acuité. Si une session s'en approche avant `/to-tickets`, ne forcez pas en mode dégradé ; faites un `/compact` à la frontière de phase la plus proche et continuez (voir Frontières de phase).

## Voies d'accès

Une situation de départ qui génère du travail, puis se fond dans le flux principal.

- **Des bugs et des demandes qui s'accumulent** → **`/triage`**. Il fait passer les issues par les rôles de triage et produit des issues prêtes pour un agent, que **`/implement`** reprend ensuite.

  Le triage ne sert qu'aux issues que **vous n'avez pas créées** : rapports de bug, demandes de fonctionnalités entrantes, tout ce qui arrive brut. Les tickets que `/to-tickets` a produits sont déjà prêts pour un agent, donc **ne les triez pas**.

- **Quelque chose est cassé** → **`/diagnosing-bugs`**. Pour les cas difficiles : le bug qui résiste au premier regard, le flake intermittent, la régression qui s'est glissée entre deux états connus comme bons. Il refuse de théoriser tant qu'il n'a pas une **boucle de rétroaction serrée** (une commande qui passe déjà au rouge sur *ce* bug), puis corrige avec un test de régression. Une fois le correctif en place, lancez **`/retro`** dans la même session pour demander ce qui aurait empêché le bug ; quand la vraie conclusion est qu'il n'y a pas de bon seam pour le verrouiller, c'est un travail pour **`/improve-codebase-architecture`**.

- **Un effort énorme et brumeux : un projet greenfield ou une grosse construction de fonctionnalité, trop gros pour une session** → **`/wayfinder`**, le flux le plus exigeant cognitivement ici. Quand le chemin d'ici à la destination n'est pas encore visible, il trace une **carte partagée** de **decision tickets** sur l'issue tracker et les résout un par un, en produisant des **décisions, pas des livrables**, jusqu'à repousser le brouillard et dégager la voie. Là où **`/grill-with-docs`** affine une idée que vous pouvez tenir en une session, wayfinder est pour l'idée que vous ne pouvez pas tenir, et il est plus lent et plus dense, donc gardez-le exactement pour ça, jamais pour une fonctionnalité bien cadrée.

  Quand la carte s'éclaircit, **il passe la main, il ne construit pas** : fondez-vous dans le flux principal à **`/to-spec`**, qui replie les décisions liées de la carte en un plan constructible, puis `/to-tickets` et `/implement` comme d'habitude. Boucler la carte directement dans `/implement` saute ce repli et jette le détail lié, donc allez directement à `/implement` seulement quand l'effort s'est révélé vraiment petit.

## Santé de la base de code

Pas du travail de fonctionnalité, juste de l'entretien.

- **`/improve-codebase-architecture`** se lance dès que vous avez un moment pour garder la base de code bonne à l'usage des agents. Il fait remonter des **opportunités d'approfondissement** ; en choisir une _génère une idée_ que vous pouvez emmener dans le flux principal à `/grill-with-docs`. C'est l'étude qui trouve les candidats ; **`/codebase-design`** (ci-dessous) est l'établi sur lequel vous concevez celui qui est choisi.

## Vocabulaire en dessous

Deux références invoquées par le modèle qui courent *sous* les autres skills, chacune source unique de vérité pour son vocabulaire. Recourez-y directement quand le problème tient aux **mots** et non au processus ; ou laissez les skills ci-dessus les mobiliser.

- **`/domain-modeling`** : affiner le langage *du domaine* du projet : contester un terme flou, résoudre un mot surchargé (« account » qui fait trois métiers), consigner une décision difficile à inverser comme ADR. C'est la discipline active que `/grill-with-docs` pilote pour garder `GLOSSARY.md` propre.
- **`/codebase-design`** est le vocabulaire du module profond (module, interface, profondeur, seam, adaptateur, levier, localité) pour concevoir la *forme* d'un module : beaucoup de comportement derrière une petite interface à un seam propre. `/tdd` et `/improve-codebase-architecture` le parlent tous les deux.

## Frontières de phase

Une **phase** est un bloc de travail à l'intérieur d'une session : le grilling, l'implémentation, la QA. À la **frontière** entre deux d'entre elles, vous avez cinq options, et choisir entre elles est la décision la plus floue de toute cette carte :

- **Continue** : rester sur place. Ne coûte rien, ne perd rien.
- **`/clear`** : vider la fenêtre, quand rien ici ne compte pour la suite.
- **`/handoff`** écrit un fichier markdown portable. Étroit : seulement pour un **nouveau harnais**, un **nouveau répertoire**, un **collègue**, ou bifurquer une tâche annexe **en milieu de phase**. Ce qu'il achète, c'est la portabilité.
- **Subagent** : envoyer une tâche bien cadrée dans sa propre fenêtre et récupérer un rapport.
- **`/compact`** compresse ce contexte et amorce une session fraîche avec. Le **défaut**, en bas de l'arbre plutôt que le premier réflexe.

Lisez [PHASE-BOUNDARIES.md](PHASE-BOUNDARIES.md) pour l'arbre ordonné : les cinq questions, le raisonnement derrière chaque branche, et pourquoi le coût de la source primaire fait de **Continue** la première à écarter. Prenez la décision **à** une frontière ; en milieu de phase, continuez ou découpez le reste en subagents.

## Autonome

Entièrement hors du flux principal.

- **`/grill-me`** : le même entretien implacable que `/grill-with-docs`, mais **sans état** : il n'enregistre rien localement et ne construit aucun `GLOSSARY.md`. Recourez-y quand vous **ne travaillez pas dans un répertoire de travail** (affiner un plan, une conception, un texte, tout ce qui n'a pas de repo en dessous). Si vous êtes dans un répertoire de travail, utilisez plutôt `/grill-with-docs` : il exécute le même entretien et laisse une trace écrite, donc il est strictement le meilleur.
- **`/grilling`** est la primitive d'entretien elle-même : des tours, la frontière, les faits sont le travail de l'agent et les décisions sont les vôtres. `/grill-me` et `/grill-with-docs` sont les deux entrées nommées, et `/triage`, `/wayfinder` et `/improve-codebase-architecture` l'exécutent tous en interne. Recourez-y directement seulement quand vous voulez l'entretien sans emballage autour.
- **`/prototype`** est un petit programme jetable qui répond à une question de conception : ce modèle d'état est-il juste, ou à quoi cette UI devrait-elle ressembler. Jetable est une contrainte sur la façon d'écrire le code, pas une promesse de le détruire : la réponse se replie dans le vrai code, et le prototype lui-même est conservé comme **source primaire** sur une branche `prototype/<name>` issue de main, pointée depuis l'issue d'implémentation. C'est le détour de l'étape 2 du flux principal, mais recourez-y chaque fois qu'une question de conception est difficile à trancher sur le papier.
- **`/research`** : déléguez le travail de lecture à un **agent en arrière-plan** : il enquête sur une question face à des **sources primaires**, puis laisse un fichier Markdown cité dans le repo. Continuez à travailler pendant qu'il lit. Le fichier qu'il produit est à emmener *dans* le flux principal à `/grill-with-docs`, car la recherche nourrit la réflexion plutôt que de la remplacer.
- **`/to-questionnaire`** intervient quand ce qui vous bloque n'est ni dans votre tête ni dans la base de code, mais dans **celle de quelqu'un d'autre**, et il lui écrit un questionnaire à remplir. C'est l'inverse de `/grill-me` : au lieu de vous interroger sur le sujet, il vous interroge sur l'**envoi** (à qui ça va, ce qu'il vous faut en retour) et vise les questions sur l'écart. Ce qui revient est de la matière pour `/grill-with-docs` ou `/to-spec`.
- **`/wizard`** est pour les étapes que seul un **humain** peut faire : provisionner de l'infrastructure, configurer des identifiants ou des secrets CI, cliquer dans un tableau de bord tiers inconnu, exécuter une migration ou un basculement ponctuel. Il génère un script bash interactif qui ouvre chaque URL, capture chaque valeur et l'écrit dans `.env` et les secrets GitHub, pour que la procédure cesse d'être quelque chose que vous réexpliquez à un agent à chaque fois. Invoqué par le modèle, donc l'agent y recourt dès qu'il heurte un mur que seul vous pouvez franchir. Si l'agent pouvait simplement le faire lui-même, il devrait ; ceci est pour les cas où un humain est vraiment dans la boucle.
- **`/wait-what`** est le correctif pour un message qui n'a pas atterri. Utilisez-le en cours de conversation, dans n'importe quel autre skill, et l'agent reformule ce qu'il vient de dire avec le contexte qui vous manquait, en anglais simple, avec le vocabulaire de `GLOSSARY.md`. Il agit après coup ; `/grill-with-docs` est le remède en amont, parce qu'un langage partagé convenu tôt est ce qui empêche le jargon d'arriver tout court.
- **`/teach`** : apprendre un concept sur plusieurs sessions, en utilisant le répertoire courant comme espace de travail à état.
- **`/writing-for-agents`** est la référence pour écrire des documents que les agents consomment : skills, AGENTS.md, docs pointées.

## Prérequis

**`/setup-matt-pocock-skills`** : à lancer avant votre premier flux d'ingénierie pour configurer l'issue tracker, les labels de triage et l'agencement des docs que les autres skills supposent. Les issue trackers personnalisés fonctionnent aussi.
