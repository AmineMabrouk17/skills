# Prototype de logique

Un seul fichier HTML autonome (une **démo partageable**) qui permet à quiconque de piloter un modèle d'état en cliquant sur des boutons. Utilisez ceci quand la question porte sur la **logique métier, les transitions d'état ou la forme des données** : le genre de chose qui paraît raisonnable sur le papier mais ne se sent fausse qu'une fois poussée à travers de vrais cas.

Comme c'est un seul fichier, sans rien à installer, vous pouvez le confier à une personne non développeuse (un designer, un PM, un expert du domaine) et la laisser ressentir le modèle par elle-même. Il parle donc son langage, pas celui du code.

## Quand c'est la bonne forme

- « Je ne suis pas sûr que cette machine à états gère le cas limite où X puis Y. »
- « Ce modèle de données me permet-il vraiment de représenter le cas où… »
- « Je veux sentir à quoi l'API devrait ressembler avant de l'écrire. »
- Tout cas où quelqu'un veut **appuyer sur des boutons et voir l'état changer**.

Si la question est « à quoi cela devrait-il ressembler », c'est la mauvaise branche. Utilisez [UI.md](UI.md).

## Processus

### 1. Énoncer la question

Avant d'écrire du code, notez quel modèle d'état et quelle question vous prototypez. Un paragraphe, en haut de la démo (dans une intro visible, pas seulement un commentaire). Un prototype de logique qui répond à la mauvaise question est un pur gâchis, donc rendez la question explicite pour qu'elle puisse être vérifiée plus tard, que l'utilisateur regarde maintenant ou y revienne plus tard sans être devant l'écran.

### 2. Isoler la logique dans un module portable

Placez la logique réelle (la partie qui répond à la question) dans un seul bloc `<script>` écrit comme un petit module pur qu'on pourrait extraire et déposer ensuite dans la vraie base de code. La page autour est jetable ; ce module ne l'est pas.

La bonne forme dépend de la question :

- **Un reducer pur** : `(state, action) => state`. Bon quand les actions sont des événements discrets et que l'état est une valeur unique.
- **Une machine à états** : des états et des transitions explicites. Bon quand « quelles actions sont même légales en ce moment » fait partie de la question.
- **Un petit ensemble de fonctions pures** sur un type de données simple. Bon quand il n'y a pas d'état courant implicite, seulement des transformations.
- **Une classe ou un module avec une surface de méthodes claire** quand la logique possède réellement un état interne persistant.

Choisissez la forme qui colle le mieux à la question posée, *pas* celle qui est la plus facile à brancher à une page. Gardez-la pure : pas de DOM, pas de `document`, pas de gestionnaires de boutons qui y entrent. La page l'appelle ; rien ne circule dans l'autre sens. C'est ce qui rend le prototype utile au-delà de sa propre durée de vie : une fois la question résolue, le reducer / la machine / l'ensemble de fonctions validé s'extrait tout seul vers le vrai module.

### 3. Construire le fichier HTML partageable

Un seul fichier, HTML/CSS/JS simple : pas de framework, pas de bundler, pas de serveur, tout en ligne pour qu'il s'ouvre par double-clic et survive à un envoi par e-mail. N'importe qui devrait pouvoir le faire tourner en l'ouvrant.

Écrivez-le pour une personne non développeuse. Chaque label est en **langage de domaine**, pas en code : les boutons et l'état se lisent comme le métier, pas comme le reducer. Expliquez en mots simples ce qui se passe.

Agencez-le selon une hiérarchie propre, de haut en bas :

1. **Titre et explication en une ligne** de ce que cette démo permet d'explorer (la question de l'étape 1).
2. **État courant** : l'état pertinent complet, rendu sous forme de panneau lisible (champs étiquetés, pas un vidage JSON brut), re-rendu après chaque clic pour que le changement soit visible. Là où cela aide une personne non développeuse à suivre, signalez ce qui vient de changer.
3. **Boutons de jeu libre** : un bouton par action, toujours disponible, pour que quiconque puisse manipuler le modèle dans n'importe quel ordre. Chaque clic déclenche son action et re-rend l'état.
4. **Parcours guidés** : un ensemble de **scénarios**, un par onglet. Chaque onglet contient une courte description du scénario en langage simple (la situation qu'il installe et ce qu'il faut observer) et, en dessous, les **boutons à presser** dans l'ordre pour ce scénario. Chaque étape est un vrai bouton : cliquer dessus effectue cette action et passe à l'étape suivante. Démarrer un parcours réinitialise vers un état initial connu pour que le scénario se déroule de la même façon à chaque fois.

Choisissez des scénarios qui montrent les cas gênants, ceux difficiles à raisonner sur le papier : le chemin nominal, un cas limite délicat, une tentative de quelque chose qui devrait être illégal.

Gardez-le beau mais sobre : typographie propre, espacement généreux, une seule couleur d'accent. Pas d'animations, pas de gadgets : rien qui concurrence l'état et les boutons.

### 4. Transmettez-le

Envoyez-leur le fichier, ou ouvrez-le pour eux. Ils cliqueront dans les parcours et en jeu libre quand ils y arriveront ; les moments intéressants sont quand ils disent « attends, ça ne devrait pas être possible » ou « tiens, je pensais que X serait différent » ; ce sont les bugs de l'_idée_, et c'est tout l'intérêt. S'ils veulent de nouvelles actions ou un nouveau scénario, ajoutez-les. Les prototypes évoluent.

### 5. Consigner la réponse et le prototype

Une fois que le prototype a répondu à sa question, consignez la réponse, puis consignez le prototype comme le décrit le [SKILL](SKILL.md). La correspondance propre à la logique : le reducer / la machine / l'ensemble de fonctions validé s'extrait vers le vrai module (la décision, absorbée) ; la coquille HTML suit vers la branche jetable qui conserve le prototype comme source primaire, et comme c'est un fichier autonome, il y reste trivialement re-exécutable.

## Anti-motifs

- **N'ajoutez pas de tests.** Un prototype qui a besoin de tests n'est plus un prototype.
- **Ne le branchez pas à la vraie base de données.** Utilisez un état en mémoire, sauf si la question porte spécifiquement sur la persistance.
- **Ne généralisez pas.** Pas de « et si on voulait supporter X plus tard ». Le prototype répond à une seule question.
- **Ne mélangez pas la logique et la page.** Si le module pur référence le DOM, `document` ou des gestionnaires de boutons, il n'est plus extractible. Gardez la page comme une fine coquille au-dessus d'un module pur.
- **Ne recourez pas à un framework, un bundler ou un serveur.** Un seul fichier que le destinataire double-clique ; une application React ou un serveur de dev tue le côté « partageable ».
- **Ne livrez pas la coquille HTML en production.** La page est optimisée pour être cliquée à la main. Le module de logique derrière est la partie qui vaut la peine d'être gardée.
