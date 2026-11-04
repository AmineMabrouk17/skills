---
name: setup-ts-deep-modules
description: Brancher dependency-cruiser sur un repo TypeScript pour que chaque package soit un module profond, avec l'implémentation cachée dans des sous-dossiers et accessible uniquement via ses fichiers points d'entrée. Invoqué par l'utilisateur.
disable-model-invocation: true
---

# Mise en place des modules profonds TS

Faites de chaque package de ce repo un **module profond** : beaucoup de comportement derrière une petite interface. La surface publique d'un package, ce sont ses **points d'entrée** (les fichiers à la racine du package), et tout ce qui est dans ses sous-dossiers est caché. Ce skill installe [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) et les règles qui font des points d'entrée le seul chemin d'accès, puis prouve que les règles mordent.

Pour le vocabulaire (module profond, interface, seam, profondeur), appelez l'outil Skill avec « codebase-design » et utilisez son langage tout du long.

## La forme imposée

```
src/packages/
  <name>/
    index.ts        ← an entry point (public). Import this from outside.
    client.ts       ← another entry point. Packages may expose SEVERAL.
    lib/            ← implementation: hidden from outside, free to import each other.
    tests/          ← co-located tests + fixtures (a subfolder, so private).
```

La surface publique, ce sont les **fichiers racine** du package, pas un `index.ts` désigné. Par convention, l'implémentation vit dans `lib/` et les tests dans `tests/`, ce qui donne à chaque package la même forme à deux dossiers. La règle elle-même est générale, cependant : *tout* ce qui est dans *n'importe quel* sous-dossier est privé, donc vous n'étendez jamais la config pour ajouter un dossier.

Quatre règles, toutes en `error` :

1. **Frontière des points d'entrée** : le code hors d'un package (code applicatif ou autre package) ne peut importer que les points d'entrée de ce package (ses fichiers racine), jamais rien dans ses sous-dossiers.
2. **Liberté intra-package** : les fichiers d'un package s'importent librement entre eux.
3. **Tests via les points d'entrée** : les fichiers sous `<pkg>/tests/` peuvent importer les points d'entrée de n'importe quel package et leurs propres fixtures `tests/`, mais jamais les internes d'un sous-dossier d'un package (pas même les leurs). Les tests d'intégration entre packages sont acceptables ; les imports profonds ne le sont pas.
4. **Pas de cycles** : aucun cycle de dépendances.

**Des points d'entrée, pas un barrel.** Comme la surface publique est *chaque* fichier racine, un package peut exposer plusieurs petits points d'entrée (`index.ts`, `client.ts`, `server.ts`) au lieu de tout faire passer par un seul `index.ts` géant. Les fichiers barrel qui réexportent tout un sous-arbre sont déconseillés ; gardez des points d'entrée petits et cachez l'implémentation dans des sous-dossiers.

Le layering (quels packages peuvent dépendre de quels autres) est une préoccupation *différente* et reste sous forme de stub commenté dans la config, à remplir par ce repo.

## Étapes

### 1. Détecter l'environnement

- **Gestionnaire de packages** : `pnpm-lock.yaml` → pnpm, `yarn.lock` → yarn, `bun.lockb` → bun, sinon npm. Utilisez-le pour chaque commande ci-dessous (`pnpm`/`yarn`/`npm run`/`bunx`).
- **Racine des packages** : si `src/` existe, utilisez `src/packages`, sinon `packages`. Confirmez le choix avec l'utilisateur si le repo a déjà une convention évidente différente.
- **Config existante** : cherchez un fichier `.dependency-cruiser.*`. S'il en existe un, ne l'écrasez **pas** : fusionnez-y les quatre règles et les options, et dites à l'utilisateur ce que vous avez ajouté.

**Terminé quand :** le gestionnaire de packages, la racine des packages et le statut de la config existante sont tous connus.

### 2. Installer dependency-cruiser

Installez `dependency-cruiser` comme devDependency avec le gestionnaire de packages détecté.

**Terminé quand :** `dependency-cruiser` est dans `devDependencies`.

### 3. Écrire la config

Copiez [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) à la racine du repo sous le nom `.dependency-cruiser.cjs`. Définissez `PACKAGES_ROOT` avec la racine détectée à l'étape 1. Les règles sont basées sur la profondeur des chemins et indépendantes des extensions, donc rien d'autre n'a besoin d'être adapté.

**Terminé quand :** `.dependency-cruiser.cjs` existe avec le bon `PACKAGES_ROOT`, et les quatre règles d'interdiction sont présentes.

### 4. Le brancher dans les vérifications

- Ajoutez un script `lint:boundaries` : `depcruise <packages-root>` (ou `depcruise src`).
- Intégrez-le à la commande de vérification globale du repo, celle qui exécute déjà le typecheck (par exemple un script `check` / `ci` / `validate`). Ne touchez **pas** à `tsconfig` et n'ajoutez pas d'alias de chemins.
- S'il n'y a pas de script global, ajoutez `lint:boundaries` et dites à l'utilisateur de l'inclure dans la CI.

**Terminé quand :** `lint:boundaries` existe et s'exécute dans la même commande que le typecheck.

### 5. Échafauder le package d'exemple

Créez un `<packages-root>/example/` commité comme modèle à copier :

- `index.ts` est un point d'entrée. Exportez une fonction qui délègue à un fichier interne (pour que le package soit visiblement *profond*, pas un simple passe-plat).
- `lib/impl.ts` : un fichier interne dans un **sous-dossier**, importé par `index.ts`, non accessible depuis l'extérieur.
- `tests/example.test.ts` importe **uniquement** `../index` (un point d'entrée) et vérifie la fonction publique.

Dites à l'utilisateur qu'il s'agit d'un modèle de départ à copier ou à supprimer.

**Terminé quand :** le package d'exemple existe, expose son comportement via un point d'entrée racine et cache `impl` dans un sous-dossier.

### 6. Prouver que les règles mordent

C'est le critère d'achèvement de tout le skill : une config qui n'échoue pas sur une violation ne vaut rien.

1. Lancez `lint:boundaries`. Elle doit **passer** sur l'exemple propre.
2. Ajoutez temporairement un import profond dans `tests/example.test.ts` (par exemple `import { thing } from "../lib/impl"`). Relancez `lint:boundaries` ; elle doit **échouer** avec `tests-through-entrypoints`.
3. Annulez l'import profond. Relancez une fois ; elle doit **passer**.

**Terminé quand :** vous avez observé un succès, puis un échec sur l'import profond, puis un succès à nouveau. Si l'étape 2 n'échoue pas, les règles ne sont pas branchées correctement : corrigez avant de finir.

### 7. Documenter la convention

Écrivez un `README.md` **dans le dossier des packages** (`<packages-root>/README.md`, à côté des packages qu'il régit) couvrant : la disposition `src/packages/<name>/` (points d'entrée à la racine, `lib/` pour l'implémentation, `tests/` pour les tests), « importez uniquement via les points d'entrée d'un package (ses fichiers racine) », et comment lancer `lint:boundaries`. **Déconseillez les fichiers barrel** explicitement : exposez plusieurs petits points d'entrée au lieu de réexporter tout un sous-arbre via un seul index. Limitez-vous à l'extrait à copier plus les quatre règles, un paragraphe chacune.

Ajoutez ensuite un **pointeur de contexte** vers ce fichier depuis le fichier d'instructions pour agents du repo (`CLAUDE.md` s'il existe, sinon `AGENTS.md`, en créant `AGENTS.md` si ni l'un ni l'autre n'existe). Une ligne suffit, par exemple `Packages are deep modules: see [src/packages/README.md](./src/packages/README.md) before adding or importing one.`. C'est ce qui fait qu'un agent découvre la règle de frontière au lieu de trébucher dessus.

**Terminé quand :** `<packages-root>/README.md` existe et déconseille les barrels, et le `CLAUDE.md`/`AGENTS.md` du repo pointe vers lui.

## Notes

- Les rétroréférences `$1` de la config (le group matching de dependency-cruiser) sont ce qui permet à un package d'atteindre ses propres internes alors que les extérieurs ne le peuvent pas. Ne les aplatissez pas en règles séparées par package.
- Public ou privé se décide par la **profondeur** : les fichiers racine d'un package sont des points d'entrée ; tout ce qui est dans un sous-dossier est privé. Les sous-dossiers conventionnels sont `lib/` (implémentation) et `tests/`, mais la règle ne les code pas en dur : n'importe quel sous-dossier est privé, donc un nouveau dossier ne demande jamais de changer la config. Ajouter un point d'entrée revient à ajouter un fichier racine (pas de barrel).
- Les packages sont **à plat** : un seul niveau d'enfants directs sous la racine. Les internes d'un package peuvent s'imbriquer aussi profondément que vous voulez ; un package ne peut pas contenir un autre package.
- Utilisez `.cjs` (et non `.js`) pour que le `module.exports` de la config fonctionne même dans les repos `"type": "module"`.
