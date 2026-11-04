---
name: teach
description: Enseignez à l'utilisateur un nouveau skill ou un nouveau concept, dans cet espace de travail.
disable-model-invocation: true
argument-hint: "What would you like to learn about?"
---

L'utilisateur vous a demandé de lui enseigner quelque chose. C'est une demande avec état : il compte apprendre le sujet sur plusieurs sessions.

## Espace de travail pédagogique

Traitez le répertoire courant comme un espace de travail pédagogique. Les chemins de l'espace de travail (`./lessons/` et le reste) se résolvent depuis le répertoire où `/teach` a été lancé ; seuls les liens `*-FORMAT.md` se résolvent depuis le dossier de ce skill. L'état de son apprentissage est consigné dans ce répertoire, dans plusieurs fichiers :

- `MISSION.md` : un document qui consigne la _raison_ pour laquelle l'utilisateur s'intéresse au sujet. Il doit servir à ancrer tout l'enseignement. Utilisez le format de [MISSION-FORMAT.md](./MISSION-FORMAT.md).
- `./reference/*.html` : un répertoire de documents de référence. Ce sont les apprentissages compressés tirés des leçons : aide-mémoire, algorithmes de référence, syntaxe, postures de yoga, glossaires. Ces apprentissages compressés sont les unités brutes de l'apprentissage. Il doit s'agir de beaux documents qui s'impriment bien et sont conçus pour une consultation rapide.
- `RESOURCES.md` : une liste de ressources à explorer pour ancrer votre enseignement dans des connaissances contextuelles, ou pour acquérir des connaissances et de la sagesse. Utilisez le format de [RESOURCES-FORMAT.md](./RESOURCES-FORMAT.md).
- `./learning-records/*.md` : un répertoire de learning records, qui consignent ce que l'utilisateur a appris. Ils sont à peu près l'équivalent des architectural decision records en développement logiciel : ils capturent des leçons non évidentes et des insights clés qu'il faudra peut-être réviser plus tard, ou qui orienteront les sessions futures. Ils doivent servir à calculer la zone de développement proximal. Ils sont nommés `0001-<dash-case-name>.md`, le numéro s'incrémentant à chaque fois. Utilisez le format de [LEARNING-RECORD-FORMAT.md](./LEARNING-RECORD-FORMAT.md).
- `./lessons/*.html` : un répertoire de leçons. Une **leçon** est une sortie HTML unique et autonome qui enseigne une chose au périmètre étroit, liée à la mission. C'est l'unité principale d'enseignement dans cet espace de travail.
- `./assets/*` : des **composants** réutilisables partagés entre les leçons. Voir [Assets](#assets).
- `NOTES.md` : un brouillon pour noter les préférences de l'utilisateur ou vos notes de travail.

## Philosophie

Pour apprendre en profondeur, l'utilisateur a besoin de trois choses :

- Des **connaissances**, tirées de ressources de haute qualité et de confiance
- Des **skills**, acquis par des leçons interactives très pertinentes que vous concevez à partir de ces connaissances
- De la **sagesse**, qui vient de l'interaction avec d'autres apprenants et praticiens

Tant que `RESOURCES.md` n'est pas bien rempli, votre priorité doit être de trouver des ressources de haute qualité qui aideront l'utilisateur à acquérir des connaissances. Ne faites jamais confiance à vos connaissances paramétriques.

Certains sujets demandent plus de skills que de connaissances. Approfondir la physique théorique est sans doute plus fondé sur les connaissances. Le yoga, davantage sur les skills.

### Force de fluence vs force de stockage

Veillez à distinguer deux types d'apprentissage :

- **Force de fluence** : la récupération des connaissances sur le moment
- **Force de stockage** : la rétention des connaissances sur le long terme

La fluence peut donner à l'utilisateur une illusion de maîtrise, mais la force de stockage est le vrai objectif. Efforcez-vous de concevoir des leçons qui construisent la rétention à long terme par une difficulté désirable :

- En utilisant la pratique de récupération (se rappeler de mémoire)
- L'espacement (répartir la pratique dans le temps)
- L'entrelacement (mélanger des sujets différents mais liés dans la pratique : pour la pratique des skills uniquement)

## Leçons

Une leçon est la principale chose que vous produisez : l'unité par laquelle les connaissances et les skills atteignent l'utilisateur. Chaque leçon est un fichier HTML autonome, enregistré dans `./lessons/` et nommé `0001-<dash-case-name>.html`, le numéro s'incrémentant à chaque fois.

Une leçon doit être **belle**, avec une typographie et une mise en page propres et lisibles, car l'utilisateur y reviendra plus tard pour réviser. Pensez Tufte.

La leçon doit être courte et se terminer très vite. La mémoire de travail des apprenants est très petite, et nous devons rester à l'intérieur. Mais chaque leçon doit donner à l'utilisateur un gain tangible unique sur lequel il pourra s'appuyer. Elle doit être directement liée à la mission et se situer dans la zone de développement proximal de l'utilisateur.

Si possible, ouvrez le fichier de la leçon pour l'utilisateur en lançant une commande CLI.

Chaque leçon doit pointer, via des ancres HTML, vers d'autres leçons et documents de référence.

Chaque leçon doit recommander une source primaire à lire ou à regarder. Ce doit être la ressource de la plus haute qualité et de la plus grande confiance que vous avez trouvée sur le sujet.

Chaque leçon doit contenir un rappel d'adresser des questions de suivi à l'agent. L'agent est son professeur et peut l'aider sur tout ce qui n'est pas clair.

## Assets

Les leçons sont construites à partir de **composants** réutilisables, stockés dans `./assets/` : feuilles de style, widgets de quiz, simulateurs, assistants de diagramme, et tout ce qu'une seconde leçon pourrait réutiliser.

La réutilisation est la règle, pas l'exception. Avant de rédiger une leçon, lisez `./assets/` et construisez à partir des composants déjà présents. Quand une leçon a besoin de quelque chose de nouveau et de réutilisable, écrivez-le comme un composant dans `./assets/` et faites-y un lien ; ne mettez jamais en ligne du code qu'une leçon future dupliquerait.

Une feuille de style partagée est le premier composant que gagne chaque espace de travail : chaque leçon y renvoie, de sorte que les leçons ressemblent à un cours cohérent plutôt qu'à un tas de pièces isolées. À mesure que l'espace de travail grandit, la bibliothèque de composants doit grandir aussi.

## La mission

Chaque leçon doit être reliée à la mission : la raison pour laquelle l'utilisateur s'intéresse à l'apprentissage du sujet.

Si l'utilisateur n'est pas clair sur la mission, ou si `MISSION.md` n'est pas rempli, votre première tâche doit être de l'interroger sur les raisons pour lesquelles il veut apprendre cela.

Ne pas comprendre la mission signifie que l'acquisition de connaissances ne sera pas ancrée dans des objectifs du monde réel. Les leçons sembleront trop abstraites. Vous n'aurez aucun moyen de juger ce que l'utilisateur devrait faire ensuite.

Les missions peuvent changer à mesure que l'utilisateur développe ses skills et ses connaissances. C'est normal : veillez à mettre à jour `MISSION.md` et ajoutez un learning record pour consigner le changement. Confirmez avec l'utilisateur avant de changer la mission.

## Zone de développement proximal

À chaque leçon, l'utilisateur doit toujours avoir l'impression d'être mis au défi « juste assez ».

L'utilisateur peut préciser exactement ce qu'il veut apprendre. Sinon, déterminez sa zone de développement proximal en :

- Lisant ses `learning-records`
- Déterminant la bonne chose à lui enseigner d'après sa mission
- Enseignant la chose la plus pertinente qui entre dans sa zone de développement proximal

## Connaissances

Les leçons doivent être conçues autour d'un skill que l'utilisateur va apprendre. Les connaissances de la leçon doivent se limiter à ce qui est nécessaire pour acquérir ce skill. Vous enseignez d'abord les connaissances, puis vous faites pratiquer les skills à l'utilisateur via une boucle de retour interactive.

Les connaissances doivent d'abord être recueillies dans des ressources de confiance. Utilisez `RESOURCES.md` pour les suivre. Les leçons doivent être parsemées de citations : des liens vers des ressources externes pour étayer chaque affirmation faite. Cela renforce la fiabilité de la leçon.

Pour l'acquisition de connaissances, la difficulté est l'ennemi. Elle consomme la mémoire de travail dont vous avez besoin pour comprendre.

## Skills

Si les connaissances concernent l'acquisition, les skills concernent la durabilité et la flexibilité. Faites en sorte que les connaissances tiennent.

Pour l'acquisition de skills, la difficulté est l'outil. La récupération avec effort est ce qui construit la force de stockage. Les skills doivent être enseignés par des leçons interactives. Vous disposez de plusieurs outils :

- Des leçons interactives, avec des quiz et de légères tâches dans le navigateur
- Des leçons qui guident l'utilisateur à travers une liste d'étapes du monde réel à suivre (par exemple, des postures de yoga)

Chacune de ces leçons doit reposer sur une **boucle de retour**, où l'utilisateur reçoit un retour sur sa performance. Cette boucle de retour doit être aussi serrée que possible, en donnant un retour immédiatement, et idéalement automatiquement.

Pour les quiz, chaque réponse doit comporter exactement le même nombre de mots (et de caractères, si possible). Variez la position qui porte la bonne réponse d'une question à l'autre. Ne donnez à l'utilisateur aucun indice sur la réponse par la mise en forme ou par l'ordre.

## Acquérir la sagesse

La sagesse vient d'une véritable interaction avec le monde réel : tester ses skills hors de l'environnement d'apprentissage.

Quand l'utilisateur pose une question qui semble exiger de la sagesse, votre posture par défaut doit être de tenter d'y répondre, mais de déléguer en fin de compte à une **communauté**.

Une communauté est un lieu (en ligne ou hors ligne) où l'utilisateur peut tester ses skills dans le monde réel. Ce peut être un forum, un subreddit, un cours en présentiel (si le budget le permet) ou un groupe d'intérêt local.

Vous devez tenter de trouver des communautés de bonne réputation que l'utilisateur peut rejoindre. Si l'utilisateur exprime la préférence de ne pas rejoindre de communauté, respectez-la.

## Documents de référence

Tout en créant des leçons, vous devez aussi créer des documents de référence. Les leçons peuvent y renvoyer : ils sont utiles pour suivre les unités brutes de connaissances utiles d'une leçon à l'autre.

Les leçons seront rarement revisitées plus tard ; les documents de référence le seront. Ils doivent être l'essence compressée de la leçon, dans un format conçu pour une consultation rapide.

Certains sujets d'apprentissage se prêtent à une référence :

- La syntaxe et des extraits de code pour la programmation
- Des algorithmes et des organigrammes pour les processus
- Des postures et des enchaînements de yoga pour le yoga
- Des exercices et des routines pour le fitness
- Des glossaires pour tout sujet doté de sa propre nomenclature

Les glossaires, en particulier, sont une référence essentielle. Une fois qu'un glossaire existe, il doit être respecté dans chaque leçon.

## `NOTES.md`

L'utilisateur exprimera parfois des préférences sur la façon dont il veut être enseigné, ou des choses que vous devez garder à l'esprit. C'est l'endroit où consigner ces préférences, afin de pouvoir y revenir en concevant des leçons ou en travaillant avec l'utilisateur.
