---
name: git-guardrails-claude-code
description: Configure des hooks Claude Code pour bloquer les commandes git dangereuses (push, reset --hard, clean, branch -D, etc.) avant leur exécution. Utilisez-le quand l'utilisateur veut empêcher des opérations git destructrices, ajouter des hooks de sécurité git, ou bloquer git push/reset dans Claude Code.
---

# Mettre en place les garde-fous Git

Met en place un hook PreToolUse qui intercepte et bloque les commandes git dangereuses avant que Claude ne les exécute.

## Ce qui est bloqué

- `git push` (toutes les variantes, y compris `--force`)
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

Lorsqu'une commande est bloquée, Claude voit un message lui indiquant qu'il n'a pas l'autorisation d'accéder à ces commandes.

## Étapes

### 1. Demander la portée

Demandez à l'utilisateur : installer pour **ce projet uniquement** (`.claude/settings.json`) ou **tous les projets** (`~/.claude/settings.json`) ?

### 2. Copier le script du hook

Le script fourni se trouve ici : [scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

Copiez-le à l'emplacement cible selon la portée :

- **Projet** : `.claude/hooks/block-dangerous-git.sh`
- **Global** : `~/.claude/hooks/block-dangerous-git.sh`

Rendez-le exécutable avec `chmod +x`.

### 3. Ajouter le hook dans la configuration

Ajoutez-le au fichier de configuration approprié :

**Projet** (`.claude/settings.json`) :

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**Global** (`~/.claude/settings.json`) :

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

Si le fichier de configuration existe déjà, fusionnez le hook dans le tableau `hooks.PreToolUse` existant. N'écrasez pas les autres réglages.

### 4. Demander la personnalisation

Demandez si l'utilisateur veut ajouter ou retirer des motifs de la liste bloquée. Modifiez le script copié en conséquence.

### 5. Vérifier

Lancez un test rapide :

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

Le script doit se terminer avec le code 2 et afficher un message BLOCKED sur stderr.
