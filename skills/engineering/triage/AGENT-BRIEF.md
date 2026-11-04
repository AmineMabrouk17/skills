# Rédiger des agent briefs

Un agent brief est un commentaire structuré publié sur une issue ou une PR GitHub lorsqu'elle passe à `ready-for-agent`. C'est la spécification faisant autorité à partir de laquelle un agent AFK travaillera. Le corps original et la discussion sont du contexte : l'agent brief est le contrat.

L'agent brief énonce **ce que l'agent doit faire**, ce qui s'étend aux deux surfaces : pour une issue, il s'agit de construire le changement à partir de rien ; pour une PR, il s'agit de ce qui reste à faire *au diff existant* : le terminer, combler les lacunes, répondre aux points de revue. Mêmes principes dans les deux cas ; l'exemple de PR ci-dessous montre la différence.

## Principes

### Durabilité plutôt que précision

L'issue peut rester dans `ready-for-agent` pendant des jours ou des semaines. La base de code changera entre-temps. Rédigez l'agent brief de façon à ce qu'il reste utile même lorsque des fichiers sont renommés, déplacés ou refactorisés.

- **Faites** : décrivez les interfaces, les types et les contrats comportementaux
- **Faites** : nommez les types, signatures de fonctions ou formes de configuration spécifiques que l'agent doit rechercher ou modifier
- **Ne faites pas** : référencez des chemins de fichiers, ils deviennent obsolètes
- **Ne faites pas** : référencez des numéros de ligne
- **Ne partez pas du principe** que la structure d'implémentation actuelle restera la même

### Comportemental, pas procédural

Décrivez **ce que** le système doit faire, pas **comment** l'implémenter. L'agent explorera la base de code à neuf et prendra ses propres décisions d'implémentation.

- **Bien :** « Le type `SkillConfig` devrait accepter un champ optionnel `schedule` de type `CronExpression` »
- **Mauvais :** « Ouvrez src/types/skill.ts et ajoutez un champ schedule à la ligne 42 »
- **Bien :** « Lorsqu'un utilisateur exécute `/triage` sans argument, il devrait voir un résumé des issues qui nécessitent attention »
- **Mauvais :** « Ajoutez une instruction switch dans la fonction de gestion principale »

### Critères d'acceptation complets

L'agent doit savoir quand il a terminé. Chaque agent brief doit avoir des critères d'acceptation concrets et testables. Chaque critère doit être vérifiable indépendamment.

- **Bien :** « Exécuter `gh issue list --label needs-triage` renvoie les issues qui sont passées par la classification initiale »
- **Mauvais :** « Le triage devrait fonctionner correctement »

### Limites de périmètre explicites

Indiquez ce qui est hors périmètre. Cela empêche l'agent de surenchérir ou de faire des suppositions sur des fonctionnalités adjacentes.

## Modèle

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** one-line description of what needs to happen

**Current behavior:**
Describe what happens now. For bugs, this is the broken behavior.
For enhancements, this is the status quo the feature builds on.

**Desired behavior:**
Describe what should happen after the agent's work is complete.
Be specific about edge cases and error conditions.

**Key interfaces:**
- `TypeName`: what needs to change and why
- `functionName()` return type: what it currently returns vs what it should return
- Config shape: any new configuration options needed

**Acceptance criteria:**
- [ ] Specific, testable criterion 1
- [ ] Specific, testable criterion 2
- [ ] Specific, testable criterion 3

**Out of scope:**
- Thing that should NOT be changed or addressed in this issue
- Adjacent feature that might seem related but is separate
```

## Exemples

### Bon agent brief (bug)

```markdown
## Agent Brief

**Category:** bug
**Summary:** Skill description truncation drops mid-word, producing broken output

**Current behavior:**
When a skill description exceeds 1024 characters, it is truncated at exactly
1024 characters regardless of word boundaries. This produces descriptions
that end mid-word (e.g. "Use when the user wants to confi").

**Desired behavior:**
Truncation should break at the last word boundary before 1024 characters
and append "..." to indicate truncation.

**Key interfaces:**
- The `SkillMetadata` type's `description` field: no type change needed,
  but the validation/processing logic that populates it needs to respect
  word boundaries
- Any function that reads SKILL.md frontmatter and extracts the description

**Acceptance criteria:**
- [ ] Descriptions under 1024 chars are unchanged
- [ ] Descriptions over 1024 chars are truncated at the last word boundary
      before 1024 chars
- [ ] Truncated descriptions end with "..."
- [ ] The total length including "..." does not exceed 1024 chars

**Out of scope:**
- Changing the 1024 char limit itself
- Multi-line description support
```

### Bon agent brief (amélioration)

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Add `.out-of-scope/` directory support for tracking rejected feature requests

**Current behavior:**
When a feature request is rejected, the issue is closed with a `wontfix` label
and a comment. There is no persistent record of the decision or reasoning.
Future similar requests require the maintainer to recall or search for the
prior discussion.

**Desired behavior:**
Rejected feature requests should be documented in `.out-of-scope/<concept>.md`
files that capture the decision, reasoning, and links to all issues that
requested the feature. When triaging new issues, these files should be
checked for matches.

**Key interfaces:**
- Markdown file format in `.out-of-scope/`: each file should have a
  `# Concept Name` heading, a `**Decision:**` line, a `**Reason:**` line,
  and a `**Prior requests:**` list with issue links
- The triage workflow should read all `.out-of-scope/*.md` files early
  and match incoming issues against them by concept similarity

**Acceptance criteria:**
- [ ] Closing a feature as wontfix creates/updates a file in `.out-of-scope/`
- [ ] The file includes the decision, reasoning, and link to the closed issue
- [ ] If a matching `.out-of-scope/` file already exists, the new issue is
      appended to its "Prior requests" list rather than creating a duplicate
- [ ] During triage, existing `.out-of-scope/` files are checked and surfaced
      when a new issue matches a prior rejection

**Out of scope:**
- Automated matching (human confirms the match)
- Reopening previously rejected features
- Bug reports (only enhancement rejections go to `.out-of-scope/`)
```

### Bon agent brief (PR)

Pour une PR, le « Current behavior » décrit l'état du diff, et l'agent brief demande à l'agent de le terminer ou de le corriger plutôt que de construire à partir de zéro.

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Finish the contributor's `--json` output flag for `triage list`

**Current behavior:**
The PR adds a `--json` flag that serializes the issue list to JSON. The happy
path works and the diff matches the project's command structure. Two gaps
remain: errors are still printed as human text (not JSON), and the new flag has
no test coverage.

**Desired behavior:**
With `--json`, all output (including errors) is well-formed JSON on stdout,
and the command's exit codes are unchanged. The existing human-readable output
is untouched when the flag is absent.

**Key interfaces:**
- The command's error path should emit `{ "error": string }` under `--json`
  instead of the plain-text error
- Reuse the existing serializer the PR already added; don't introduce a second

**Acceptance criteria:**
- [ ] `triage list --json` emits valid JSON for both success and error cases
- [ ] Exit codes match the non-JSON command
- [ ] A test covers the `--json` success output and one error case
- [ ] Default (non-JSON) output is byte-for-byte unchanged

**Out of scope:**
- Adding `--json` to any other command
- Changing the JSON shape of the success payload the PR already defined
```

### Mauvais agent brief

```markdown
## Agent Brief

**Summary:** Fix the triage bug

**What to do:**
The triage thing is broken. Look at the main file and fix it.
The function around line 150 has the issue.

**Files to change:**
- src/triage/handler.ts (line 150)
- src/types.ts (line 42)
```

C'est mauvais parce que :
- Pas de catégorie
- Description vague (« le truc de triage est cassé »)
- Références à des chemins de fichiers et des numéros de ligne qui deviendront obsolètes
- Pas de critères d'acceptation
- Pas de limites de périmètre
- Pas de description du comportement actuel par rapport au comportement souhaité
