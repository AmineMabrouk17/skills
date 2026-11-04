# Format de MISSION.md

`MISSION.md` vit à la racine de l'espace de travail. Il consigne la _raison_ pour laquelle l'utilisateur apprend ce sujet. Chaque décision pédagogique (quoi enseigner ensuite, quelles ressources faire émerger, quels exercices concevoir) doit pouvoir se rattacher à ce document.

## Modèle

```md
# Mission : {Sujet}

## Pourquoi
{1 à 3 phrases. L'objectif concret et réel que l'utilisateur poursuit. Qu'est-ce qui change dans sa vie ou son travail quand il maîtrise ce skill ? Évitez les formulations abstraites du type « comprendre X » ; visez le résultat sous-jacent.}

## À quoi ressemble la réussite
- {Une chose précise et observable que l'utilisateur saura faire}
- {Une autre chose précise}
- {…}

## Contraintes
- {Temps, budget, engagements antérieurs, préférences d'apprentissage, tout ce qui borne l'approche}

## Hors périmètre
- {Sujets adjacents que l'utilisateur ne veut explicitement pas poursuivre pour l'instant, afin de protéger la zone de développement proximal}
```

## Règles

- **Une mission par espace de travail.** Si l'utilisateur veut apprendre deux choses sans rapport, cela fait deux espaces de travail.
- **Le concret plutôt que l'abstrait.** « Courir un semi-marathon d'ici octobre » vaut mieux que « être plus en forme ». « Livrer un CLI Rust à mon équipe » vaut mieux que « apprendre Rust ».
- **Recadrez le flou.** Si l'utilisateur n'arrive pas à formuler le pourquoi, interrogez-le avant d'écrire quoi que ce soit. Une mauvaise mission est pire que pas de mission.
- **Révisez quand la réalité change.** Les missions changent. Quand l'objectif de l'utilisateur évolue, mettez ce fichier à jour : ne laissez pas une mission obsolète orienter les sessions futures.
- **Restez court.** Si `MISSION.md` dépasse un écran, il a cessé d'être une boussole pour devenir un plan.
