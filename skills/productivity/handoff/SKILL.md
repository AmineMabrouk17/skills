---
name: handoff
description: Compactez la conversation actuelle en un document de passation pour qu'un autre agent reprenne le travail.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Rédigez un document de passation résumant la conversation actuelle afin qu'un agent frais puisse poursuivre le travail. Enregistrez-le dans le répertoire temporaire du système d'exploitation de l'utilisateur (`$TMPDIR`, sinon `/tmp` ; `%TEMP%` sous Windows) - pas dans l'espace de travail actuel.

Incluez une section « skills suggérés » dans le document, en nommant les skills pour lesquels le prochain agent devrait appeler l'outil Skill.

Ne dupliquez pas le contenu déjà capturé dans d'autres artefacts (specs, plans, ADR, issues, commits, diffs). Référencez-les par chemin ou URL à la place.

Masquez toute information sensible, comme les clés d'API, les mots de passe ou les informations personnelles identifiantes.

Si l'utilisateur a passé des arguments, traitez-les comme une description de ce sur quoi la prochaine session se concentrera et adaptez le document en conséquence.
