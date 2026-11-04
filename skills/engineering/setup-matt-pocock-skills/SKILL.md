---
name: setup-matt-pocock-skills
description: "Configurez ce repo pour les skills d'ingénierie : mettez en place son issue tracker, son vocabulaire de labels de triage et l'agencement de ses docs de domaine. À exécuter une fois avant la première utilisation des autres skills d'ingénierie."
disable-model-invocation: true
---

# Setup Matt Pocock's Skills

Échafaudez la configuration par repo que les skills d'ingénierie supposent :

- **Issue tracker** : où vivent les issues (GitHub par défaut ; le markdown local est aussi pris en charge d'emblée)
- **Labels de triage** : les chaînes utilisées pour les cinq rôles de triage canoniques
- **Docs de domaine** : où vivent `GLOSSARY.md` et les ADR, et les règles de consommation pour les lire

C'est un skill piloté par le prompt, pas un script déterministe. Explorez, présentez ce que vous avez trouvé, confirmez avec l'utilisateur, puis écrivez.

## Processus

### 1. Explorer

Examinez le repo actuel pour comprendre son état de départ. Lisez ce qui existe ; ne présumez rien :

- `git remote -v` et `.git/config` : est-ce un repo GitHub ? Lequel ?
- `AGENTS.md` et `CLAUDE.md` à la racine du repo : l'un des deux existe-t-il ? Y a-t-il déjà une section `## Agent skills` dans l'un d'eux ?
- `GLOSSARY.md` et `GLOSSARY-MAP.md` à la racine du repo, et les fichiers hérités `CONTEXT.md` / `CONTEXT-MAP.md`. Si un fichier hérité existe, proposez de le `git mv` vers son nom `GLOSSARY` et de réécrire ces noms dans `docs/agents/domain.md`.
- `docs/adr/` et tout répertoire `src/*/docs/adr/`
- `docs/agents/` : la sortie précédente de ce skill existe-t-elle déjà ?
- `.scratch/` : un signe qu'une convention d'issue tracker en markdown local est déjà en usage
- Le skill `triage` est-il installé ? (un dossier de skill `triage` à côté de celui-ci, ou `triage` dans vos skills disponibles.) Cela décide si la section B s'exécute du tout.
- Signaux de monorepo : un `pnpm-workspace.yaml`, un champ `workspaces` dans `package.json`, ou un `packages/*` peuplé avec son propre `src/`. Ils ne sont présents que dans un repo réellement gros et multi-paquets ; leur absence signifie mono-contexte, ce qui est le cas de presque tous les repos.

### 2. Présenter les constats et poser les questions

Résumez ce qui est présent et ce qui manque. Prenez ensuite les sections dans l'ordre. Une section, une réponse, puis la suivante.

Ouvrez chaque section par la réponse recommandée, pour que l'utilisateur puisse l'accepter d'un mot. Ne donnez une explication d'une ligne que lorsque le choix bifurque vraiment ; sautez entièrement la section quand l'exploration l'a déjà tranchée (section B quand `triage` n'est pas installé, section C quand il n'y a pas de monorepo).

**Section A : Issue tracker.**

> Explication : l'« issue tracker » est l'endroit où vivent les issues de ce repo. Des skills comme `to-tickets`, `triage` et `to-spec` y lisent et y écrivent. Ils ont besoin de savoir s'ils doivent appeler `gh issue create`, écrire un fichier markdown sous `.scratch/`, ou suivre un autre workflow que vous décrivez. Choisissez l'endroit où vous suivez réellement le travail de ce repo.

Posture par défaut : ces skills ont été conçus pour GitHub. Si un `git remote` pointe vers GitHub, proposez cela. Si un `git remote` pointe vers GitLab (`gitlab.com` ou un hôte auto-hébergé), proposez GitLab. Sinon (ou si l'utilisateur préfère), offrez :

- **GitHub** : les issues vivent dans les GitHub Issues du repo (utilise la CLI `gh`)
- **GitLab** : les issues vivent dans les GitLab Issues du repo (utilise la CLI [`glab`](https://gitlab.com/gitlab-org/cli))
- **Markdown local** : les issues vivent sous forme de fichiers sous `.scratch/<feature>/` dans ce repo (bien pour les projets solo ou les repos sans remote)
- **Autre** (Jira, Linear, etc.) : demandez à l'utilisateur de décrire le workflow en un paragraphe ; le skill l'enregistrera comme de la prose libre

Enregistrez le choix dans `docs/agents/issue-tracker.md`. Les templates GitHub et GitLab portent un flag « PRs as a request surface », par défaut **désactivé**. Laissez-le désactivé et ne le soulevez pas : un utilisateur qui veut des PR externes dans la file de triage pourra basculer le flag dans le fichier plus tard.

Un issue tracker GitHub ou GitLab a besoin d'un repo joignable. Quand l'utilisateur en choisit un et qu'aucun `git remote` ne pointe vers cet hôte, demandez-lui l'URL du repo et branchez-la (`git init` si nécessaire, `git remote add origin <url>`) avant d'aller plus loin. C'est fait quand `gh repo view` / `glab repo view` affiche le projet depuis ce répertoire ; sinon, arrêtez-vous et dites à l'utilisateur ce qui manque.

**Section B : Vocabulaire des labels de triage.** Sautez entièrement cette section si le skill `triage` n'est pas installé (l'exploration vous l'a dit), car un skill non installé n'a pas besoin de labels.

S'il est installé, posez exactement une question :

> Voulez-vous conserver les labels de triage par défaut ? (recommandé : **oui**)

Les valeurs par défaut sont les cinq rôles canoniques, chaque chaîne de label étant égale à son nom : `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. Sur **oui**, écrivez-les tels quels. Seulement si l'utilisateur dit non, généralement parce que son tracker utilise déjà d'autres noms (par ex. `bug:triage` pour `needs-triage`), recueillez les remplacements pour que `triage` applique les labels existants au lieu d'en créer des doublons.

**Section C : Docs de domaine.** Par défaut, partez sur **mono-contexte** (un `GLOSSARY.md` + `docs/adr/` à la racine du repo). Cela convient à presque tous les repos ; écrivez-le sans demander.

N'offrez **multi-contexte** (un `GLOSSARY-MAP.md` à la racine pointant vers des `GLOSSARY.md` par contexte) que si l'exploration a trouvé des signaux de monorepo. Confirmez alors l'agencement souhaité.

### 3. Confirmer et éditer

Montrez à l'utilisateur un brouillon de :

- Le bloc `## Agent skills` à ajouter à celui de `CLAUDE.md` / `AGENTS.md` qui est en cours d'édition (voir l'étape 4 pour les règles de sélection)
- Le contenu de `docs/agents/issue-tracker.md`, `docs/agents/domain.md` et `docs/agents/triage-labels.md` (le dernier seulement quand `triage` est installé)

Laissez-le corriger avant d'écrire.

### 4. Écrire

**Choisissez le fichier à éditer :**

- Si `CLAUDE.md` existe, éditez-le.
- Sinon, si `AGENTS.md` existe, éditez-le.
- Si aucun des deux n'existe, demandez à l'utilisateur lequel créer ; ne choisissez pas à sa place.

Ne créez jamais `AGENTS.md` quand `CLAUDE.md` existe déjà (ni l'inverse) ; éditez toujours celui qui est déjà là.

Si un bloc `## Agent skills` existe déjà dans le fichier choisi, mettez son contenu à jour sur place plutôt que d'ajouter un doublon. N'écrasez pas les modifications de l'utilisateur dans les sections environnantes.

Le bloc :

```markdown
## Agent skills

### Issue tracker

[résumé en une ligne de l'endroit où les issues sont suivies]. Voir `docs/agents/issue-tracker.md`.

### Triage labels

[résumé en une ligne du vocabulaire de labels]. Voir `docs/agents/triage-labels.md`.

### Domain docs

[résumé en une ligne de l'agencement : « mono-contexte » ou « multi-contexte »]. Voir `docs/agents/domain.md`.
```

N'incluez le sous-bloc `### Triage labels` et n'écrivez `docs/agents/triage-labels.md` que lorsque `triage` est installé et que la section B s'est exécutée. Sinon, les deux sont omis.

Quand la section B s'est exécutée sur GitHub ou GitLab, créez chaque label configuré qui manque au tracker (`gh label create` / `glab label create`).

Écrivez ensuite les fichiers de docs en partant des templates de départ de ce dossier de skill :

- [issue-tracker-github.md](./issue-tracker-github.md) : issue tracker GitHub
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) : issue tracker GitLab
- [issue-tracker-local.md](./issue-tracker-local.md) : issue tracker en markdown local
- [triage-labels.md](./triage-labels.md) : correspondance des labels (seulement si `triage` est installé)
- [domain.md](./domain.md) : règles de consommation des docs de domaine + agencement

Pour les issue trackers « autres », écrivez `docs/agents/issue-tracker.md` de zéro à partir de la description de l'utilisateur.

### 5. Terminer

Dites à l'utilisateur que la configuration est terminée et quels skills d'ingénierie vont désormais lire ces fichiers. Mentionnez qu'il pourra éditer directement `docs/agents/*.md` plus tard ; relancer ce skill n'est nécessaire que pour changer d'issue tracker ou repartir de zéro.
