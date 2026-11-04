---
name: grilling
description: Interrogez l'utilisateur sans relâche sur un plan, une décision ou une idée. À utiliser quand l'utilisateur veut mettre sa réflexion à l'épreuve, ou emploie l'une des phrases déclencheuses « grill ».
---

Interrogez l'utilisateur sans relâche jusqu'à ce que vous atteigniez une compréhension partagée. Cartographiez cela comme un **arbre de conception** : chaque décision se ramifie en décisions qui en dépendent.

Parcourez l'arbre en **tours**. La **frontière** est l'ensemble des décisions dont les prérequis sont déjà réglés : les questions que vous pouvez poser _maintenant_ sans deviner des réponses que vous n'avez pas encore entendues. Posez toute la frontière en un seul tour : numérotez chaque question et donnez votre réponse recommandée. Attendez ensuite les réponses de l'utilisateur avant le tour suivant.

Formatez un tour comme ceci :

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Formulez chaque question de sorte que « oui » accepte votre réponse recommandée.

Chaque tour au cours duquel l'utilisateur répond remodèle l'arbre : les décisions réglées poussent la frontière vers l'extérieur et débloquent les questions qui en dépendaient. Recalculez la frontière et posez le tour suivant. Une question dont la réponse dépend d'une autre question encore ouverte dans ce tour appartient à un tour _ultérieur_, pas à celui-ci.

Trouver des _faits_ est votre travail, jamais celui de l'utilisateur. Quand une question de la frontière a besoin d'un fait venant de l'environnement (système de fichiers, outils, etc.), envoyez un sub-agent avec une fenêtre de contexte neuve pour le trouver ; ne demandez pas à l'utilisateur quelque chose que vous pourriez chercher vous-même. Ne restez pas bloqué dessus : une exploration en cours est un prérequis non réglé, donc seules les questions en aval attendent le rapport du sub-agent ; posez le reste de la frontière maintenant. Les _décisions_ appartiennent à l'utilisateur : soumettez-les-lui une par une et attendez.

La session est terminée quand la frontière est vide : chaque branche de l'arbre de conception visitée, rien laissé implicitement supposé. N'agissez pas dessus avant que l'utilisateur confirme que vous avez atteint une compréhension partagée.
