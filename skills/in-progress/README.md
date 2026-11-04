# En cours

Bêta. Ces skills sont publics à dessein : essayez-les et dites-moi ce qui casse. Ils sont exclus du plugin et du README de premier niveau jusqu'à ce qu'ils passent dans un bucket stable, ils n'ont pas de page de docs, et ils peuvent changer ou disparaître sans prévenir.

Le plugin ne vous les fournira pas. Installez-en un directement :

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

- **[loop-me](./loop-me/SKILL.md)** : Interrogez-vous jusqu'à obtenir des specs de workflow implémentables, sur plusieurs sessions, en utilisant le répertoire courant comme espace de travail avec état. Invoqué par l'utilisateur.
- **[writing-beats](./writing-beats/SKILL.md)** : Façonnez un article comme un parcours de beats, à la manière d'un livre dont vous êtes le héros. Choisissez un beat de départ, n'écrivez que ce beat, puis basculez vers le suivant, jusqu'à ce que l'article atteigne une fin naturelle.
- **[writing-fragments](./writing-fragments/SKILL.md)** : Session de grilling qui vous extrait des fragments (des pépites d'écriture hétérogènes) et les ajoute à un document unique comme matière première pour un futur article.
- **[writing-shape](./writing-shape/SKILL.md)** : Prenez un fichier markdown de matière première et façonnez-le en article paragraphe par paragraphe, en argumentant les choix de format à chaque étape.
- **[claude-handoff](./claude-handoff/SKILL.md)** : Passez la conversation en cours à un agent en arrière-plan neuf qui reprend le travail immédiatement, amorcé par un résumé de passation via `claude --bg`. Invoqué par l'utilisateur.
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)** : Branchez dependency-cruiser sur un repo TypeScript pour que chaque package soit un module profond : implémentation cachée dans des sous-dossiers, accessible uniquement via ses fichiers points d'entrée, et tests qui l'exercent à travers eux. Invoqué par l'utilisateur.
- **[software-factory](./software-factory/SKILL.md)** : Langage partagé pour concevoir une usine logicielle : les tâches qu'elle exécute et les services auxquels elle se branche. Appelez-le en parallèle d'une session de grilling. Invoqué par l'utilisateur.
