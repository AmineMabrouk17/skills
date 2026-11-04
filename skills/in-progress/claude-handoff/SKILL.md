---
name: claude-handoff
description: Passer la conversation en cours à un agent en arrière-plan neuf qui reprend le travail immédiatement.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Écrivez un résumé de passation de la conversation en cours pour qu'un agent neuf puisse continuer le travail. Enregistrez-le dans le répertoire temporaire du système d'exploitation de l'utilisateur, puis lancez un agent en arrière-plan amorcé avec ce résumé comme prompt : `claude --bg --name "<descriptive name>" -- "$(cat <summary file>)"`. Passer le fichier évite que le shell exécute les backticks ou développe les `$` du résumé. Il démarre dans le répertoire de travail courant et rend la main immédiatement ; l'utilisateur le gère avec `claude agents`.

Passez toujours `-n`/`--name` avec un nom descriptif (par exemple `--name "Fix login bug"`) ; il définit le nom affiché dans la liste des jobs, le sélecteur de session et le titre du terminal.

Incluez une section « skills suggérés » dans le résumé, en nommant les skills pour lesquels le prochain agent devra appeler l'outil Skill.

Ne dupliquez pas le contenu déjà consigné dans d'autres artefacts (specs, plans, ADR, issues, commits, diffs). Référencez-les par chemin ou par URL.

Caviardez toute information sensible, comme les clés d'API, les mots de passe ou les informations personnelles identifiables, puisque le résumé devient le prompt de l'agent.

Si l'utilisateur a passé des arguments, traitez-les comme une description de ce sur quoi la prochaine session portera et adaptez le résumé en conséquence.
