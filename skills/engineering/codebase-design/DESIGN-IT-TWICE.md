# Concevoir deux fois

Lorsque l'utilisateur veut explorer des interfaces alternatives pour un candidat à l'approfondissement choisi, utilisez ce modèle de subagents parallèles. Inspiré de « Design It Twice » (Ousterhout) : votre première idée n'est probablement pas la meilleure.

Reprend le vocabulaire de [SKILL.md](SKILL.md) : **module**, **interface**, **seam**, **adaptateur**, **levier**.

## Processus

### 1. Cadrer l'espace du problème

Avant de lancer les subagents, écrivez une explication destinée à l'utilisateur sur l'espace du problème pour le candidat choisi :

- Les contraintes que toute nouvelle interface devrait satisfaire
- Les dépendances sur lesquelles elle s'appuierait, et dans quelle catégorie elles tombent (voir [DEEPENING.md](DEEPENING.md))
- Une esquisse de code approximative et illustrative pour ancrer les contraintes, pas une proposition, juste un moyen de rendre les contraintes concrètes

Montrez-la à l'utilisateur, puis passez immédiatement à l'étape 2. L'utilisateur lit et réfléchit pendant que les subagents travaillent en parallèle.

### 2. Lancer les subagents

Lancez 3 subagents ou plus en parallèle, chacun avec une fenêtre de contexte neuve. Chacun doit produire une interface **radicalement différente** pour le module approfondi.

Donnez à chaque subagent un brief technique distinct (chemins de fichiers, détails de couplage, catégorie de dépendance issue de [DEEPENING.md](DEEPENING.md), ce qui se trouve derrière le seam). Le brief est indépendant de l'explication de l'espace du problème destinée à l'utilisateur à l'étape 1. Donnez à chaque agent une contrainte de conception différente :

- Agent 1 : « Minimisez l'interface : visez 1 à 3 points d'entrée au maximum. Maximisez le levier par point d'entrée. »
- Agent 2 : « Maximisez la flexibilité : prenez en charge de nombreux cas d'usage et l'extension. »
- Agent 3 : « Optimisez pour l'appelant le plus courant : rendez le cas par défaut trivial. »
- Agent 4 (le cas échéant) : « Concevez autour des ports et adaptateurs pour les dépendances qui traversent le seam. »

Incluez dans le brief le vocabulaire de [SKILL.md](SKILL.md) et celui de GLOSSARY.md afin que chaque subagent nomme les choses de façon cohérente avec le langage de l'architecture et le langage de domaine du projet.

Chaque subagent produit :

1. Interface (types, méthodes, paramètres, plus les invariants, l'ordre, les modes d'erreur)
2. Un exemple d'usage montrant comment les appelants l'utilisent
3. Ce que l'implémentation cache derrière le seam
4. Stratégie de dépendances et adaptateurs (voir [DEEPENING.md](DEEPENING.md))
5. Compromis : où le levier est élevé, où il est mince

### 3. Présenter et comparer

Présentez les conceptions séquentiellement pour que l'utilisateur puisse absorber chacune, puis comparez-les en prose. Mettez en contraste la **profondeur** (le levier au niveau de l'interface), la **localité** (où le changement se concentre) et le **placement du seam**.

Après la comparaison, donnez votre propre recommandation : quelle conception vous semble la plus solide et pourquoi. Si des éléments de conceptions différentes se combineraient bien, proposez un hybride. Ayez un avis tranché : l'utilisateur veut une lecture nette, pas un menu.
