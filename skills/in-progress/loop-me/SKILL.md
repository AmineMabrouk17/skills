---
name: loop-me
description: Interrogez-moi sans relâche sur les specs des workflows que je veux construire, dans cet espace de travail.
disable-model-invocation: true
argument-hint: "A workflow to design, or nothing to go find one"
---

Lancez une session `/grilling` avec état dont la seule sortie est des specs de **workflow**. Utilisez la discipline du grilling (sans relâche, une série de questions à la fois, une réponse recommandée attachée à chacune) orientée vers le vocabulaire et l'objectif ci-dessous. Créez, modifiez et supprimez les specs à mesure que le grilling tranche les choses.

## La lentille des boucles

Une **boucle** est un motif récurrent dans la vie de l'utilisateur : sa carrière, sa semaine, son matin, une activité répétée. Se représenter une vie comme des boucles imbriquées révèle à quel point ses activités sont réellement prévisibles, et c'est ce qui les rend dignes d'être **déléguées**. Utilisez la lentille pour trouver les boucles qui valent une spec, et proposez-en que l'utilisateur n'a pas remarquées.

Un **workflow** est la spec d'une boucle, rendue réelle. Vous exécutez un workflow sur une boucle : la boucle est son instanciation en cours. Les workflows vivent dans `workflows/*.md` et sont la source de vérité.

## Vocabulaire

Un langage partagé, mobilisé seulement quand un workflow le réclame : jamais une checklist. **N'imposez rien de structurel** : un workflow n'a besoin ni d'IA, ni de point de contrôle, ni de planification, sauf si le grilling montre le contraire.

- **Déclencheur** : ce qui lance chaque exécution, un **événement** (un nouvel email, une nouvelle issue) ou une **planification** (chaque matin). Le déclenchement par événement est généralement le plus efficace.
- **Point de contrôle** : un point avec un humain dans la boucle où l'on demande à l'utilisateur de vérifier ou de décider. Certains workflows n'en ont aucun et tournent de façon autonome ; d'autres n'utilisent aucune IA du tout.
- **Pousser à droite** : différez le point de contrôle aussi loin que possible. Faites le maximum de travail avant d'impliquer l'humain, pour qu'on ne lui demande qu'une fois, tard, avec tout préparé.
- **Brief** : ce que présente un point de contrôle, un résumé serré et prêt à décider (ce qui a été produit, pourquoi, et un lien vers l'actif lui-même), jamais la sortie brute. L'utilisateur lit un brief, pas un brouillon. La vitesse de revue est impérative.

## Critères d'achèvement

Une spec de workflow est terminée quand un agent implémenteur pourrait la construire sans poser une seule question. Interrogez jusqu'à ce point ; rien n'est terminé tant qu'une question subsiste.

## L'espace de travail

- `workflows/*.md` : une spec par workflow.
- `NOTES.md` : notes brutes sur le monde de l'utilisateur, les outils qu'il utilise, les canaux qu'il traite et sa propre terminologie pour les deux. Quand il est vide ou maigre, interrogez-le sur son monde avant de spécifier quoi que ce soit. Affinez les termes flous en termes canoniques à mesure qu'ils apparaissent, et consignez-les ici.
