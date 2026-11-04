---
name: setup-pre-commit
description: Met en place des hooks pre-commit Husky avec lint-staged (Prettier), la vérification de types et les tests dans le repo courant. Utilisez-le quand l'utilisateur veut ajouter des hooks pre-commit, configurer Husky, configurer lint-staged, ou ajouter formatage et vérification de types au moment du commit.
---

# Mettre en place les hooks pre-commit

## Ce que cette procédure installe

- Un hook pre-commit **Husky**
- **lint-staged** exécutant Prettier sur tous les fichiers indexés
- Une configuration **Prettier** (si elle manque)
- Les scripts **typecheck** et **test** dans le hook pre-commit

## Étapes

### 1. Détecter le gestionnaire de paquets

Cherchez `package-lock.json` (npm), `pnpm-lock.yaml` (pnpm), `yarn.lock` (yarn), `bun.lockb` (bun). Utilisez celui qui est présent. Par défaut, utilisez npm si c'est ambigu.

### 2. Installer les dépendances

Installez-les comme devDependencies :

```
husky lint-staged prettier
```

### 3. Initialiser Husky

```bash
npx husky init
```

Cela crée le répertoire `.husky/` et ajoute `prepare: "husky"` dans package.json.

### 4. Créer `.husky/pre-commit`

Écrivez ce fichier (pas besoin de shebang avec Husky v9+) :

```
npx lint-staged
npm run typecheck
npm run test
```

**Adaptez** : remplacez `npm` par le gestionnaire de paquets détecté. Si le repo n'a pas de script `typecheck` ou `test` dans package.json, omettez ces lignes et prévenez l'utilisateur.

### 5. Créer `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. Créer `.prettierrc` (si absent)

Ne le créez que s'il n'existe aucune configuration Prettier. Utilisez ces valeurs par défaut :

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. Vérifier

- [ ] `.husky/pre-commit` existe et est exécutable
- [ ] `.lintstagedrc` existe
- [ ] Le script `prepare` dans package.json vaut `"husky"`
- [ ] La configuration `prettier` existe
- [ ] Lancez `npx lint-staged` pour vérifier que cela fonctionne

### 8. Committer

Indexez tous les fichiers modifiés ou créés et committez avec le message : `Add pre-commit hooks (husky + lint-staged + prettier)`

Le commit passera par les nouveaux hooks pre-commit : un bon test de fumée pour vérifier que tout fonctionne.

## Remarques

- Husky v9+ n'a pas besoin de shebangs dans les fichiers de hook
- `prettier --ignore-unknown` ignore les fichiers que Prettier ne sait pas analyser (images, etc.)
- Le pre-commit lance d'abord lint-staged (rapide, uniquement les fichiers indexés), puis la vérification complète des types et les tests
