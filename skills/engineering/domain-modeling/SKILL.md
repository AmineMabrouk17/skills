---
name: domain-modeling
description: Construire et affiner le modèle de domaine d'un projet. À utiliser lors de discussions sur la terminologie de la base de code, lors de la rédaction ou de la modification d'un GLOSSARY.md, ou lors de l'enregistrement ou de la modification d'un ADR.
---

# Modélisation du domaine

Construisez et affinez activement le modèle de domaine du projet pendant que vous concevez. C'est la discipline *active* : contester les termes, inventer des scénarios de cas limite et écrire le glossaire et les décisions au moment même où ils se cristallisent. (Lire simplement `GLOSSARY.md` pour le vocabulaire n'est pas ce skill : c'est une habitude d'une ligne que tout skill peut avoir. Ce skill sert quand vous modifiez le modèle, pas seulement quand vous le consommez.)

## Structure des fichiers

La plupart des repos ont un seul contexte :

```
/
├── GLOSSARY.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

Si un `GLOSSARY-MAP.md` existe à la racine, le repo a plusieurs contextes. La carte indique où vit chacun :

```
/
├── GLOSSARY-MAP.md
├── docs/
│   └── adr/                          ← system-wide decisions
├── src/
│   ├── ordering/
│   │   ├── GLOSSARY.md
│   │   └── docs/adr/                 ← context-specific decisions
│   └── billing/
│       ├── GLOSSARY.md
│       └── docs/adr/
```

Créez les fichiers paresseusement : seulement quand vous avez quelque chose à écrire. Si aucun `GLOSSARY.md` n'existe, créez-en un quand le premier terme est résolu. Si aucun `docs/adr/` n'existe, créez-le quand le premier ADR est nécessaire.

## Pendant la session

### Contester au regard du glossaire

Quand l'utilisateur emploie un terme qui entre en conflit avec le langage existant dans `GLOSSARY.md`, signalez-le immédiatement. « Votre glossaire définit “annulation” comme X, mais vous semblez vouloir dire Y. De quoi s'agit-il ? »

### Affiner le langage flou

Quand l'utilisateur emploie des termes vagues ou surchargés, proposez un terme canonique précis. « Vous dites “compte” : voulez-vous dire le Client ou l'Utilisateur ? Ce sont deux choses différentes. »

### Discuter de scénarios concrets

Lorsque des relations de domaine sont en discussion, mettez-les à l'épreuve avec des scénarios précis. Inventez des scénarios qui sondent les cas limites et forcent l'utilisateur à être précis sur les frontières entre les concepts.

### Recouper avec le code

Quand l'utilisateur énonce comment quelque chose fonctionne, vérifiez si le code est d'accord. Si vous trouvez une contradiction, exposez-la : « Votre code annule des Commandes entières, mais vous venez de dire qu'une annulation partielle est possible. Laquelle est correcte ? »

### Mettre à jour GLOSSARY.md au fil de l'eau

Quand un terme est résolu, mettez à jour `GLOSSARY.md` sur-le-champ. Ne les groupez pas : capturez-les au moment où ils surviennent. Utilisez le format de [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md).

`GLOSSARY.md` doit être totalement dépourvu de détails d'implémentation. Ne traitez pas `GLOSSARY.md` comme une spec, un brouillon ou un dépôt de décisions d'implémentation. C'est un glossaire et rien d'autre.

### Proposer des ADR avec parcimonie

Ne proposez de créer un ADR que lorsque les trois conditions suivantes sont réunies :

1. **Difficile à inverser** : le coût de changer d'avis plus tard est significatif
2. **Surprenant sans contexte** : un lecteur futur se demandera « pourquoi ont-ils fait ça comme ça ? »
3. **Le résultat d'un vrai compromis** : il existait de véritables alternatives et vous en avez choisi une pour des raisons précises

Si l'une des trois manque, sautez l'ADR. Utilisez le format de [ADR-FORMAT.md](./ADR-FORMAT.md).
