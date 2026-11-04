# Frontières de phase

Une **phase** est un bloc de travail à l'intérieur d'une session : le grilling, l'implémentation, la QA. La définition est floue à dessein : une phase se termine quand vous pensez *« ok, on a fini avec ça »*.

La **frontière de phase** est l'écart entre deux phases, et c'est le seul endroit où cette décision a sa place. En milieu de phase, il n'y a pas de décision à prendre : continuer, ou découper le travail restant en subagents. Compacter en milieu de phase fait perdre le fil à l'agent.

## Les cinq options

| Option       | Ce que ça fait                                                    |
| ------------ | ----------------------------------------------------------------- |
| **Continue** | Rester dans la session. Aucun changement de contexte.             |
| **`/clear`** | Vider la fenêtre de contexte et repartir de zéro.                 |
| **`/handoff`** | Écrire un fichier markdown portable et amorcer une session n'importe où avec lui. |
| **Subagent** | Envoyer la tâche dans sa propre fenêtre de contexte et récupérer un rapport. |
| **`/compact`** | Compresser ce contexte et amorcer une session fraîche avec le résumé. |

## L'arbre

Descendez de haut en bas à la frontière. Le premier **oui** l'emporte.

**1. Pouvez-vous continuer dans cette session ?** Deux choses rendent la réponse positive : la phase suivante a besoin de cette phase comme **source primaire**, ou il vous reste assez de [smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone) (~150k tokens) pour que la phase suivante tienne. Grilling → implémentation est le oui standard : l'implémentation veut le raisonnement mot pour mot, pas un résumé. Continue ne coûte rien et ne perd rien, donc écartez-la avant tout le reste.

**2. Le contexte est-il sans rapport avec ce qui vient ensuite ?** Tout ce qui est dans cette session (l'exploration, les décisions, les impasses) est-il jetable ? Si oui, **`/clear`**. C'est le coup le moins cher du plateau : il ne prend aucun temps et rend toute la fenêtre. `/clear` n'est pas non plus définitif : l'ancienne session reste reprenable.

Le coût d'une erreur ici est à sens unique. Videz un contexte *pertinent* et vous perdez le **pourquoi** derrière ce que vous avez construit, et aucune relecture du diff ne vous le rendra.

**3. Avez-vous besoin de passer la main ?** `/handoff` est étroit. Vous n'en avez besoin que lorsque vous :

- passez à un **nouveau harnais** (Claude → Codex),
- déménagez vers un **nouveau répertoire** ou repo,
- envoyez le travail à un **collègue**,
- ou bifurquez une tâche annexe trouvée **en milieu de phase** sans faire dérailler ce que vous faites.

Cette liste est toute la clause. Ce que `/handoff` achète, c'est la **portabilité** : un fichier qui voyage. Si rien ne voyage, vous n'en avez pas besoin.

**4. La tâche peut-elle être faite AFK ?** Est-elle assez bien cadrée pour tourner sans vous au clavier, sans pilotage ? Alors envoyez-la à un **subagent** et laissez cette session intacte. La revue automatisée est le cas standard : l'agent lit le diff et fait un rapport, et vous n'êtes pas nécessaire pendant ce temps.

**5. Sinon, `/compact`.** Contexte pertinent, même harnais, même répertoire, et vous devez rester dans la boucle : c'est là que l'arbre atterrit, et il y atterrit souvent. Passez-lui une instruction (`/compact we're going to QA this area`) pour que le résumé conserve ce dont la phase suivante a besoin.

`/compact` est le **défaut, pas le premier réflexe**. Il se trouve en bas parce que les quatre questions au-dessus sont toutes moins chères ou plus précises. Le mode de défaillance quand on commence ici, c'est une session fraîche qui se trompe avec assurance sur une décision que le résumé a aplatie.

## Sources primaires et secondaires

Chaque mouvement sauf **Continue** transforme une **source primaire** en **source secondaire** : la session telle qu'elle s'est déroulée, remplacée par un résumé. L'échange a toujours la même forme :

| Source                            | Information | Bruit    | Marge de manœuvre |
| --------------------------------- | ----------- | -------- | ----------------- |
| Primaire (Continue)               | Complète    | Beaucoup | Peu               |
| Secondaire (`/compact`, `/handoff`) | Avec perte  | Moins    | Beaucoup          |

C'est pourquoi la question 1 vient en premier. Vous ne payez la perte que quand rester coûte plus que ça ne rapporte.

## Ce sont des jugements

Les questions ne sont pas objectives : chacune comporte une part de goût, et la même frontière peut pencher dans deux sens selon le jour. La valeur est de les poser **dans l'ordre**, à la frontière plutôt qu'au milieu du travail.
