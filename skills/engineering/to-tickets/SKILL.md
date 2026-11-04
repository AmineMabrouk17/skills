---
name: to-tickets
description: Découpez un plan, une spec ou la conversation en cours en un ensemble de tickets tracer-bullet, chacun déclarant ses arêtes de blocage, publiés dans le tracker configuré (arêtes sous forme de texte dans un fichier par ticket en local, ou liens de blocage natifs sur un vrai tracker).
disable-model-invocation: true
---

# Découper en tickets

Découpez un plan, une spec ou une conversation en un ensemble de **tickets** : des tranches verticales tracer-bullet, chacune déclarant les tickets qui la **bloquent**.

L'issue tracker et le vocabulaire des labels de triage devraient vous avoir été fournis. Sinon, demandez à l'utilisateur d'exécuter `/setup-matt-pocock-skills`.

## Processus

### 1. Rassembler le contexte

Travaillez à partir de ce qui est déjà dans le contexte de la conversation. Si l'utilisateur passe une référence (un chemin de spec, un numéro d'issue ou une URL) en argument, récupérez-la et lisez son corps complet et ses commentaires.

### 2. Explorer la base de code (facultatif)

Si vous n'avez pas déjà exploré la base de code, faites-le pour comprendre l'état actuel du code. Les titres et descriptions des tickets doivent utiliser le vocabulaire du glossaire de domaine du projet, et respecter les ADR de la zone que vous touchez.

Cherchez des occasions de prefactoriser le code pour faciliter l'implémentation. « Rendez le changement facile, puis faites le changement facile. »

### 3. Rédiger les tranches verticales

Découpez le travail en tickets **tracer bullet**.

<vertical-slice-rules>

- Chaque tranche traverse un chemin étroit mais COMPLET à travers chaque couche (schéma, API, UI, tests) : verticale, PAS une tranche horizontale d'une seule couche
- Une tranche terminée est démontrable ou vérifiable par elle-même
- Chaque tranche est dimensionnée pour tenir dans une seule fenêtre de contexte fraîche
- Tout prefactoring doit être fait d'abord

</vertical-slice-rules>

Donnez à chaque ticket ses **arêtes de blocage** : les autres tickets qui doivent être terminés avant qu'il puisse commencer. Un ticket sans bloqueur peut commencer immédiatement.

**Les refactorings larges sont l'exception au découpage vertical.** Un **refactoring large** est un changement mécanique (renommer une colonne, retyper un symbole partagé) dont le **rayon d'impact** s'étend sur toute la base de code, de sorte qu'une seule modification casse des milliers de sites d'appel d'un coup et qu'aucune tranche verticale ne peut se terminer au vert. Ne le forcez pas dans un tracer bullet ; séquencez-le en **étendre puis contracter**. D'abord étendre : ajoutez la nouvelle forme à côté de l'ancienne pour que rien ne casse. Puis migrez les sites d'appel par lots dimensionnés selon le rayon d'impact (par paquet, par répertoire), chaque lot étant son propre ticket bloqué par l'extension, en gardant la CI au vert lot après lot puisque l'ancienne forme existe encore. Enfin contractez : supprimez l'ancienne forme une fois qu'aucun appelant ne subsiste, dans un ticket bloqué par chaque lot de migration. Quand même les lots ne peuvent pas rester verts seuls, gardez la séquence mais laissez-les partager une branche d'intégration que tous bloquent un ticket final d'intégration et de vérification ; le vert n'est promis qu'à ce moment-là.

### 4. Interroger l'utilisateur

Présentez la décomposition proposée sous forme de liste numérotée. Pour chaque ticket, montrez :

- **Titre** : nom court et descriptif
- **Bloqué par** : quels autres tickets (le cas échéant) doivent être terminés d'abord
- **Ce qu'il livre** : le comportement de bout en bout que ce ticket rend fonctionnel

Demandez à l'utilisateur :

- La granularité vous semble-t-elle correcte ? (trop grossière / trop fine)
- Les arêtes de blocage sont-elles correctes : chaque ticket ne dépend-il que des tickets qui le conditionnent réellement ?
- Certains tickets devraient-ils être fusionnés ou découpés davantage ?

Itérez jusqu'à ce que l'utilisateur approuve la décomposition.

### 5. Publier les tickets dans le tracker configuré

Publiez les tickets approuvés. **Comment** cela se passe dépend du tracker configuré par `/setup-matt-pocock-skills` ; les tickets sont les mêmes dans les deux cas, seule la forme des arêtes de blocage change :

- **Fichiers locaux** → écrivez un fichier par ticket sous `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numérotés à partir de `01` dans l'ordre des dépendances (bloqueurs d'abord). Le « Bloqué par » de chaque fichier liste les numéros/titres dont il dépend. Utilisez le modèle de fichier par ticket ci-dessous : un ticket par fichier, jamais un fichier combiné unique.
- **Un vrai issue tracker (GitHub, Linear, …)** → publiez une issue par ticket dans l'ordre des dépendances (bloqueurs d'abord) afin que les arêtes de blocage de chaque ticket puissent référencer de vrais identifiants. Utilisez la relation de blocage native de la plateforme quand elle en a une ; sinon, définissez le « Bloqué par » de chaque ticket sur les issues bloquantes. Si la source était une issue existante, faites de chaque ticket une sous-issue (opération du doc du tracker). Appliquez le label de triage `ready-for-agent` sauf instruction contraire ; les tickets sont récupérables par un agent par construction.

Travaillez la **frontière** : tout ticket dont tous les bloqueurs sont terminés. Pour une chaîne purement linéaire, cela signifie de haut en bas.

Ne fermez ni ne modifiez aucune issue parente.

<local-ticket-template>

# <NN>: <Titre du ticket>

**Quoi construire :** le comportement de bout en bout que ce ticket rend fonctionnel, du point de vue de l'utilisateur, pas une liste d'implémentation couche par couche.

**Bloqué par :** les numéros/titres des tickets qui conditionnent celui-ci, ou « Aucun (peut commencer immédiatement) ».

**Statut :** ready-for-agent

- [ ] Critère d'acceptation 1
- [ ] Critère d'acceptation 2

</local-ticket-template>

<issue-template>

## Parent

Une référence à l'issue parente sur le tracker (si la source était une issue existante, sinon omettez cette section).

## Quoi construire

Le comportement de bout en bout que ce ticket rend fonctionnel, du point de vue de l'utilisateur, pas une implémentation couche par couche.

## Critères d'acceptation

- [ ] Critère 1
- [ ] Critère 2

## Bloqué par

- Une référence à chaque ticket bloquant, ou « Aucun (peut commencer immédiatement) ». Omettez cette section lorsque les bloqueurs ont été définis comme arêtes natives.

</issue-template>

Dans les deux formes, évitez les chemins de fichiers ou extraits de code spécifiques : ils deviennent obsolètes vite. Exception : si un prototype a produit un extrait qui encode une décision plus précisément que la prose ne le peut (machine à états, reducer, schéma, forme de type), intégrez-le et notez brièvement qu'il provient d'un prototype. Réduisez aux parties riches en décisions, pas une démo fonctionnelle, seulement les éléments importants.
