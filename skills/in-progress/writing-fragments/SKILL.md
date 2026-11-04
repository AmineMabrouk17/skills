---
name: writing-fragments
description: "Écriture, explore : puisez dans des fragments bruts, pas encore de structure."
disable-model-invocation: true
---

<what-to-do>

C'est de l'**explore** pur : élargissez l'espace de ce qui pourrait être écrit sans vous engager sur une structure. S'engager, c'est de l'_exploit_, le travail d'un autre skill. Menez une session de grilling qui produit des fragments, en interrogeant l'utilisateur sans relâche sur ce qu'il veut écrire. Imposer des phases, des plans ou une structure d'article est hors périmètre ici.

À mesure que des fragments émergent des deux côtés de la conversation, ajoutez-les à un même fichier markdown.

Si l'utilisateur n'a pas passé de chemin, demandez une fois où enregistrer le document, puis retenez-le pour le reste de la session.

Capturez des fragments dès la première chose que dit l'utilisateur, y compris le prompt initial.

À la première écriture, placez un seul H1 en haut avec un titre de travail (il peut changer ensuite) et rien d'autre : ni métadonnées, ni sommaire, ni date.

</what-to-do>

<supporting-info>

## Qu'est-ce qu'un fragment

Un fragment est tout morceau de texte susceptible de survivre dans l'article final. Il doit être _lisible par l'auteur_ (l'auteur peut dire ce qu'il signifie), mais il n'a pas besoin de définir ses termes ni d'être compréhensible pour un lecteur froid. Le critère est « est-ce un bon morceau d'écriture ? », pas « est-ce une argumentation autonome ? »

Les fragments sont délibérément hétérogènes. Exemples de ce qui pourrait être un fragment :

- Une phrase acérée que vous voudriez déployer quelque part sans encore savoir où.
- Une affirmation avec une justification en une ligne.
- Une vignette : une chose qui est arrivée, un extrait de code, un scénario, une analogie.
- Une demi-pensée : « un truc sur le fait que X ressemble à Y, à creuser plus tard. »
- Une citation, un morceau de dialogue, une phrase entendue.
- Une liste d'observations liées qui tiennent ensemble à l'intuition.
- Une plainte, une confession, une chute.
- Un **mot directeur** : une métaphore compacte ou une création lexicale sur laquelle tout le texte peut s'accrocher (un seul terme qui nomme l'idée, comme _tracer bullets_ ou _fog of war_ nomme tout un motif).

Parmi ceux-là, le mot directeur est le fragment le plus précieux à poser. Il porte la charge : nommez le bon mot en explore et il façonne ensuite la structure, les transitions et le titre, avec des bénéfices sur toute la phase d'exploit. Quand la conversation tourne autour d'une idée récurrente, poussez à forger un mot pour la nommer.

Le journal du romancier est le modèle : des années d'observations non structurées, ensuite exploitées comme matière brute. Les fragments sont des observations.

## Format de fichier

```markdown
# Titre de travail

Un premier fragment vit ici.

Il peut faire plusieurs paragraphes. Il peut inclure des listes, du code, des citations : la
forme que le fragment prend naturellement.

---

Un second fragment.

---

> Une citation que l'utilisateur veut garder sous la main.

Une réaction à celle-ci.

---

- Un ensemble d'observations liées
- Qui tiennent ensemble à l'intuition
- Et qui veulent être proches les unes des autres
```

Les fragments sont séparés par une règle horizontale (`\n---\n`). Aucun titre dans le corps. Aucun tag. Aucun ordre autre que celui dans lequel ils ont été ajoutés.

## Rythme d'écriture

Ajoutez en silence. Ne demandez pas la permission pour chaque fragment. Mentionnez en passant ce que vous avez ajouté (« j'ajoute ça »), mais n'interrompez pas la conversation avec des boîtes de dialogue d'enregistrement.

Avant chaque écriture : relisez le fichier depuis le disque. L'utilisateur a peut-être modifié, réordonné ou supprimé des fragments entre-temps, alors préservez ses changements. N'écrasez jamais le fichier ; ajoutez uniquement (ou, si l'utilisateur le demande, modifiez un fragment précis sur place).

L'utilisateur peut dire à tout moment « coupe le dernier », « réécris celui-là plus acéré », « fusionne ces deux-là ». Traitez cela comme des instructions de premier ordre.

</supporting-info>
