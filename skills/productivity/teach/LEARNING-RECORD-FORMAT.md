# Format des learning records

Les learning records vivent dans `./learning-records/` et utilisent une numérotation séquentielle : `0001-slug.md`, `0002-slug.md`, etc. Créez le répertoire paresseusement : seulement quand le premier record est écrit.

Ils sont l'équivalent pédagogique des ADR : ils capturent des leçons non évidentes, des insights clés et des connaissances préalables déclarées qui orienteront les sessions futures. Ils servent à calculer la zone de développement proximal.

## Modèle

```md
# {Short title of what was learned or established}

{1-3 sentences: what was learned (or what prior knowledge was established), and why it matters for future sessions.}
```

C'est tout le format. Un learning record peut tenir en un seul paragraphe. La valeur est de consigner _que_ ce point est désormais connu et _pourquoi_ cela change ce qu'il faut enseigner ensuite, pas de remplir des sections.

## Sections facultatives

Ne les incluez que lorsqu'elles apportent une valeur réelle. La plupart des records n'en auront pas besoin.

- **Status** en frontmatter (`active | superseded by LR-NNNN`) : utile quand une compréhension antérieure s'avère fausse et se voit remplacée.
- **Preuves** : comment l'utilisateur a démontré la compréhension (une question à laquelle il a répondu, un exercice terminé, une expérience antérieure citée). Utile quand l'affirmation pourrait être réexaminée.
- **Implications** : ce que cela débloque ou exclut pour les sessions futures. À consigner quand ce n'est pas évident.

## Numérotation

Parcourez `./learning-records/` pour trouver le plus grand numéro existant et incrémentez-le de un.

## Quand écrire un learning record

Écrivez-en un dès que l'une de ces conditions est vraie :

1. **L'utilisateur a démontré une compréhension réelle de quelque chose de non trivial** : pas seulement une exposition, mais la preuve qu'il sait employer le concept correctement. Cela fixe un nouveau plancher pour ce qu'il faut enseigner ensuite.
2. **L'utilisateur a déclaré des connaissances préalables** : « Je connais déjà X. » Consignez-le pour que les sessions futures ne le réenseignent pas. Consignez aussi la _profondeur_ revendiquée.
3. **Une idée fausse a été corrigée** : l'utilisateur croyait auparavant quelque chose de faux et voit désormais pourquoi. Ce sont des records de grande valeur : ils prédisent les futurs points de blocage sur des sujets liés.
4. **La mission a changé en réponse à l'apprentissage** : l'utilisateur a découvert qu'il tenait à autre chose que ce qu'il pensait. Faites un lien croisé vers [[MISSION.md]] et mettez-le à jour.

### Ce qui ne compte _pas_

- Une matière qui a simplement été couverte. Couvrir n'est pas apprendre. Attendez des preuves.
- Tout ce qui est déjà consigné de façon concise dans [[GLOSSARY.md]] comme définition d'un terme. Ne dupliquez pas.
- Des journaux d'activité session par session. Les learning records ne sont pas un journal : ce sont des insights d'une qualité qui permet de décider.

## Remplacement

Quand un record ultérieur contredit un record antérieur (la compréhension de l'utilisateur s'est approfondie ou corrigée), marquez l'ancien record `Status: superseded by LR-NNNN` plutôt que de le supprimer. L'histoire de l'évolution de la compréhension est elle-même un signal utile.
