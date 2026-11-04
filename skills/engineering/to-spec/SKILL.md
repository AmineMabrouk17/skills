---
name: to-spec
description: "Transformez la conversation en cours en une spec et publiez-la dans l'issue tracker du projet : pas d'entretien, seulement la synthèse de ce dont vous avez déjà discuté."
disable-model-invocation: true
---

Ce skill prend le contexte de la conversation en cours et votre compréhension de la base de code pour produire une spec. N'interviewez PAS l'utilisateur ; synthétisez simplement ce que vous savez déjà.

L'issue tracker et le vocabulaire des labels de triage devraient vous avoir été fournis. Sinon, demandez à l'utilisateur d'exécuter `/setup-matt-pocock-skills`.

## Processus

1. Explorez le repo pour comprendre l'état actuel de la base de code, si ce n'est pas déjà fait. Utilisez le vocabulaire du glossaire de domaine du projet tout au long de la spec, et respectez les ADR de la zone que vous touchez.

2. Esquissez les seams auxquels vous allez tester la fonctionnalité. Les seams existants doivent être préférés aux nouveaux. Utilisez le seam le plus haut possible. Si de nouveaux seams sont nécessaires, proposez-les au point le plus haut que vous pouvez. Moins il y a de seams dans la base de code, mieux c'est : le nombre idéal est un.

Vérifiez avec l'utilisateur que ces seams correspondent à ses attentes.

3. Rédigez la spec en utilisant le modèle ci-dessous, puis publiez-la dans l'issue tracker du projet. Appliquez le label de triage `ready-for-agent` : pas besoin de triage supplémentaire.

<spec-template>

## Énoncé du problème

Le problème auquel l'utilisateur fait face, du point de vue de l'utilisateur.

## Solution

La solution au problème, du point de vue de l'utilisateur.

## Récits utilisateur

Une liste LONGUE et numérotée de récits utilisateur. Chaque récit utilisateur doit être au format :

1. En tant que <acteur>, je veux <fonctionnalité>, afin de <bénéfice>

<user-story-example>
1. En tant que client d'une banque mobile, je veux voir le solde de mes comptes, afin de prendre de meilleures décisions concernant mes dépenses
</user-story-example>

Cette liste de récits utilisateur doit être extrêmement étendue et couvrir tous les aspects de la fonctionnalité.

## Décisions d'implémentation

Une liste des décisions d'implémentation qui ont été prises. Cela peut inclure :

- Les modules qui seront construits/modifiés
- Les interfaces de ces modules qui seront modifiées
- Les précisions techniques du développeur
- Les décisions d'architecture
- Les changements de schéma
- Les contrats d'API
- Les interactions spécifiques

N'incluez PAS de chemins de fichiers ni d'extraits de code spécifiques. Ils risquent de devenir obsolètes très vite.

Exception : si un prototype a produit un extrait qui encode une décision plus précisément que la prose ne le peut (machine à états, reducer, schéma, forme de type), intégrez-le dans la décision concernée et notez brièvement qu'il provient d'un prototype. Réduisez aux parties riches en décisions, pas une démo fonctionnelle, seulement les éléments importants.

## Décisions de test

Une liste des décisions de test qui ont été prises. Incluez :

- Une description de ce qui fait un bon test (tester uniquement le comportement externe, pas les détails d'implémentation)
- Quels modules seront testés
- L'art antérieur pour les tests (c'est-à-dire des types de tests similaires dans la base de code)

## Hors périmètre

Une description des éléments qui sont hors du périmètre de cette spec.

## Notes complémentaires

Toute note complémentaire sur la fonctionnalité.

</spec-template>
