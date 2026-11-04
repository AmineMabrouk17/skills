---
name: code-review
description: "Passez en revue les changements depuis un point fixe (commit, branch, tag ou merge-base) selon deux axes : Standards (le code suit-il les standards de code documentés de ce repo ?) et Spec (le code correspond-il à ce que l'issue/spec d'origine demandait ?). Exécute les deux revues dans des subagents parallèles et les rapporte côte à côte. À utiliser quand l'utilisateur veut revoir une branche, une PR, des changements en cours, ou demande de « revoir depuis X »."
---

Revue à deux axes du diff entre `HEAD` et un point fixe fourni par l'utilisateur :

- **Standards** : le code est-il conforme aux standards de code documentés de ce repo ?
- **Spec** : le code implémente-t-il fidèlement l'issue / la spec d'origine ?

Les deux axes tournent en **subagents parallèles** pour ne pas polluer mutuellement leur contexte, puis ce skill agrège leurs conclusions. Démarrez chaque subagent avec une fenêtre de contexte neuve.

L'issue tracker aurait dû vous être fourni. Sinon, dites à l'utilisateur de lancer `/setup-matt-pocock-skills`.

## Processus

### 1. Fixer le point fixe

Ce que l'utilisateur a dit est le point fixe (un SHA de commit, un nom de branche, un tag, `main`, `HEAD~5`, etc.). S'il n'en a pas précisé, demandez-le.

Capturez la commande de diff une fois : `git diff <fixed-point>...HEAD` (trois points, donc la comparaison se fait contre le merge-base). Notez aussi la liste des commits via `git log <fixed-point>..HEAD --oneline`.

Avant d'aller plus loin, confirmez que le point fixe se résout (`git rev-parse <fixed-point>`) et que le diff n'est pas vide. Une mauvaise ref ou un diff vide doit échouer ici, pas à l'intérieur de deux subagents parallèles.

### 2. Identifier la source de la spec

Cherchez la spec d'origine, dans cet ordre :

1. Les références d'issue dans les messages de commit (`#123`, `Closes #45`, GitLab `!67`, etc.), récupérées via le workflow décrit dans le doc du tracker.
2. Un chemin que l'utilisateur a passé en argument.
3. Un fichier de spec sous `docs/`, `specs/` ou `.scratch/` correspondant au nom de branche ou à la fonctionnalité.
4. Si rien n'est trouvé, demandez à l'utilisateur où est la spec. S'il dit qu'il n'y en a pas, le subagent **Spec** sautera et rapportera « aucune spec disponible ».

### 3. Identifier les sources des standards

Cherchez dans le repo chaque fichier qui documente comment le code doit être écrit. Quand `CODING_STANDARDS.md` ou `CONTRIBUTING.md` existe, il doit être dans la liste.

En plus de ce que le repo documente, l'axe Standards porte toujours le **référentiel de smells** ci-dessous : un ensemble fixe de code smells de Fowler (_Refactoring_, ch. 3) qui s'applique même quand un repo ne documente rien. Deux règles l'encadrent :

- **Le repo prime.** Un standard documenté du repo gagne toujours ; là où il approuve quelque chose que le référentiel signalerait, taisez le smell.
- **Toujours un jugement.** Chaque smell est une heuristique étiquetée (« possible Feature Envy »), jamais une violation dure. Comme tout standard ici, sautez tout ce que l'outillage applique déjà.

Chaque smell se lit *ce que c'est* → *comment corriger* ; confrontez-le au diff :

- **Mysterious Name** : une fonction, variable ou type dont le nom ne révèle pas ce qu'elle fait ou contient. → renommez-la ; si aucun nom honnête ne vient, la conception est trouble.
- **Duplicated Code** : la même forme de logique apparaît dans plus d'un hunk ou fichier du changement. → extrayez la forme partagée, appelez-la depuis les deux.
- **Feature Envy** : une méthode qui plonge dans les données d'un autre objet plus que dans les siennes. → déplacez la méthode sur les données qu'elle envie.
- **Data Clumps** : les mêmes quelques champs ou paramètres continuent de voyager ensemble (un type qui demande à naître). → regroupez-les dans un seul type, passez celui-ci.
- **Primitive Obsession** : une primitive ou une chaîne qui tient lieu d'un concept de domaine méritant son propre type. → donnez au concept son propre petit type.
- **Repeated Switches** : la même cascade de `switch`/`if` sur le même type se répète dans le changement. → remplacez par du polymorphisme, ou une map que les deux sites partagent.
- **Shotgun Surgery** : un seul changement logique force des éditions dispersées dans beaucoup de fichiers du diff. → rassemblez ce qui change ensemble dans un seul module.
- **Divergent Change** : un fichier ou module est édité pour plusieurs raisons sans rapport. → découpez pour que chaque module change pour une seule raison.
- **Speculative Generality** : abstraction, paramètres ou hooks ajoutés pour des besoins que la spec n'a pas. → supprimez-les ; ré-inlinez jusqu'à ce qu'un vrai besoin apparaisse.
- **Message Chains** : une longue navigation `a.b().c().d()` dont l'appelant ne devrait pas dépendre. → cachez la promenade derrière une méthode sur le premier objet.
- **Middle Man** : une classe ou fonction qui ne fait surtout que déléguer plus loin. → coupez-la, appelez directement la vraie cible.
- **Refused Bequest** : une sous-classe ou un implémenteur qui ignore ou redéfinit la plupart de ce qu'il hérite. → abandonnez l'héritage, utilisez la composition.

### 4. Lancer les deux subagents en parallèle

Émettez les deux appels de subagent ensemble, au premier plan, et agrègez les rapports qu'ils renvoient.

Le **prompt du subagent Standards** doit inclure :

- La commande de diff complète et la liste des commits.
- La liste des fichiers sources de standards trouvés à l'étape 3, **plus le référentiel de smells de l'étape 3** collé en entier (le subagent n'y a aucun autre accès).
- Le brief : « Rapportez, par fichier/hunk le cas échéant, (a) chaque endroit où le diff viole un standard documenté : citez le standard (fichier + la règle) ; et (b) tout smell du référentiel que vous repérez : nommez-le et citez le hunk. Distinguez les violations dures des jugements : les manquements à un standard documenté peuvent être durs, mais les smells du référentiel sont toujours des jugements, et un standard documenté du repo prime sur le référentiel. Sautez tout ce que l'outillage applique. Moins de 400 mots. »

Le **prompt du subagent Spec** doit inclure :

- La commande de diff et la liste des commits.
- Le chemin ou le contenu récupéré de la spec.
- Le brief : « Rapportez : (a) les exigences demandées par la spec qui manquent ou sont partielles ; (b) le comportement présent dans le diff qui n'était pas demandé (dérive du périmètre) ; (c) les exigences qui semblent implémentées mais où l'implémentation semble fausse. Citez la ligne de spec pour chaque constat. Moins de 400 mots. »

Si la spec manque, sautez le subagent Spec et notez-le dans le rapport final.

### 5. Agréger

Présentez les deux rapports sous les titres `## Standards` et `## Spec`, mot pour mot ou légèrement nettoyés. Ne **fusionnez ni ne reclassez** les constats, car les deux axes sont délibérément séparés (voir _Pourquoi deux axes_).

Terminez par un résumé d'une ligne : total des constats par axe, et le pire problème _dans chaque axe_ (s'il y en a). Ne choisissez pas un seul vainqueur entre les axes : c'est exactement le reclassement que la séparation existe pour empêcher.

## Pourquoi deux axes

Un changement peut passer un axe et échouer sur l'autre :

- Du code qui suit chaque standard mais implémente la mauvaise chose → **Standards passe, Spec échoue.**
- Du code qui fait exactement ce que l'issue demandait mais casse les conventions du projet → **Spec passe, Standards échoue.**

Les rapporter séparément empêche un axe de masquer l'autre.
