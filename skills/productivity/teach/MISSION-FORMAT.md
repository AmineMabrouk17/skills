# Format de MISSION.md

`MISSION.md` vit à la racine de l'espace de travail. Il consigne la _raison_ pour laquelle l'utilisateur apprend ce sujet. Chaque décision pédagogique (quoi enseigner ensuite, quelles ressources faire émerger, quels exercices concevoir) doit pouvoir se rattacher à ce document.

## Modèle

```md
# Mission: {Topic}

## Why
{1-3 sentences. The concrete real-world goal the user is chasing. What changes in their life or work when they have this skill? Avoid abstract framings like "to understand X"; push for the underlying outcome.}

## Success looks like
- {A specific, observable thing the user will be able to do}
- {Another specific thing}
- {…}

## Constraints
- {Time, budget, prior commitments, learning preferences, anything that bounds the approach}

## Out of scope
- {Adjacent topics the user explicitly does not want to chase right now, protecting the zone of proximal development}
```

## Règles

- **Une mission par espace de travail.** Si l'utilisateur veut apprendre deux choses sans rapport, cela fait deux espaces de travail.
- **Le concret plutôt que l'abstrait.** « Courir un semi-marathon d'ici octobre » vaut mieux que « être plus en forme ». « Livrer un CLI Rust à mon équipe » vaut mieux que « apprendre Rust ».
- **Recadrez le flou.** Si l'utilisateur n'arrive pas à formuler le pourquoi, interrogez-le avant d'écrire quoi que ce soit. Une mauvaise mission est pire que pas de mission.
- **Révisez quand la réalité change.** Les missions changent. Quand l'objectif de l'utilisateur évolue, mettez ce fichier à jour : ne laissez pas une mission obsolète orienter les sessions futures.
- **Restez court.** Si `MISSION.md` dépasse un écran, il a cessé d'être une boussole pour devenir un plan.
