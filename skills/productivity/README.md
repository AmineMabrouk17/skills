# Productivité

Outils de workflow généraux, non spécifiques au code.

## Invoqués par l'utilisateur

Atteignables uniquement quand vous les tapez (Claude Code : `disable-model-invocation: true` ; Codex : `policy.allow_implicit_invocation: false` dans `agents/openai.yaml`).

- **[grill-me](./grill-me/SKILL.md)** : Soyez interrogé sans relâche sur un plan ou une conception jusqu'à ce que chaque branche de l'arbre de conception soit résolue.
- **[handoff](./handoff/SKILL.md)** : Compactez la conversation actuelle en un document de passation pour qu'un autre agent puisse poursuivre le travail.
- **[teach](./teach/SKILL.md)** : Enseignez à l'utilisateur un nouveau skill ou concept sur plusieurs sessions, en utilisant le répertoire courant comme espace de travail d'enseignement avec état.
- **[to-questionnaire](./to-questionnaire/SKILL.md)** : Transformez une décision que vous ne pouvez pas trancher seul en un questionnaire Markdown pour la seule personne qui le peut (rempli en asynchrone, ou ensemble lors d'une réunion).
- **[wait-what](./wait-what/SKILL.md)** : Déclenchez ceci dès qu'un message ne passe pas. L'agent le reformule avec le contexte qui vous manque, en anglais simple, en utilisant le vocabulaire de votre `GLOSSARY.md`.

## Invoqués par le modèle

Atteignables par le modèle ou l'utilisateur (formulations déclencheuses riches pour que le modèle puisse y recourir).

- **[grilling](./grilling/SKILL.md)** : Interrogez l'utilisateur sans relâche sur un plan, une décision ou une idée jusqu'à ce que chaque branche de l'arbre de conception soit résolue.
- **[writing-for-agents](./writing-for-agents/SKILL.md)** : Écrire des documents pour les agents : skills, AGENTS.md/CLAUDE.md, et tout document qu'un agent atteint par un pointeur.
