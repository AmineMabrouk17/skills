---
name: scaffold-exercises
description: Crée des arborescences d'exercices avec sections, énoncés, solutions et explications qui passent le lint. Utilisez-le quand l'utilisateur veut générer la structure d'exercices, créer des stubs d'exercices, ou préparer une nouvelle section de cours.
---

# Générer la structure des exercices

Créez des arborescences d'exercices qui passent `pnpm ai-hero-cli internal lint`, puis committez avec `git commit`.

## Nommage des répertoires

- **Sections** : `XX-section-name/` dans `exercises/` (par exemple, `01-retrieval-skill-building`)
- **Exercices** : `XX.YY-exercise-name/` dans une section (par exemple, `01.03-retrieval-with-bm25`)
- Numéro de section = `XX`, numéro d'exercice = `XX.YY`
- Les noms sont en dash-case (minuscules, tirets)

## Variantes d'exercice

Chaque exercice doit avoir au moins un de ces sous-dossiers :

- `problem/` - espace de travail de l'étudiant avec des TODO
- `solution/` - implémentation de référence
- `explainer/` - contenu conceptuel, sans TODO

Lorsque vous créez des stubs, utilisez `explainer/` par défaut, sauf si le plan indique autre chose.

## Fichiers requis

Chaque sous-dossier (`problem/`, `solution/`, `explainer/`) doit avoir un `readme.md` qui :

- N'est **pas vide** (il doit avoir du vrai contenu, même une seule ligne de titre suffit)
- N'a pas de liens cassés

Lorsque vous créez des stubs, écrivez un readme minimal avec un titre et une description :

```md
# Titre de l'exercice

Description ici
```

Si le sous-dossier contient du code, il lui faut aussi un `main.ts` (>1 ligne). Mais pour des stubs, un exercice avec seulement un readme suffit.

## Déroulé

1. **Analyser le plan** - extraire les noms de sections, les noms d'exercices et les types de variantes
2. **Créer les répertoires** - `mkdir -p` pour chaque chemin
3. **Créer les readmes de stub** - un `readme.md` par dossier de variante, avec un titre
4. **Lancer le lint** - `pnpm ai-hero-cli internal lint` pour valider
5. **Corriger les erreurs** - itérer jusqu'à ce que le lint passe

## Récapitulatif des règles de lint

Le linter (`pnpm ai-hero-cli internal lint`) vérifie que :

- Chaque exercice a des sous-dossiers (`problem/`, `solution/`, `explainer/`)
- Au moins un de `problem/`, `explainer/` ou `explainer.1/` existe
- `readme.md` existe et n'est pas vide dans le sous-dossier principal
- Aucun fichier `.gitkeep`
- Aucun fichier `speaker-notes.md`
- Aucun lien cassé dans les readmes
- Aucune commande `pnpm run exercise` dans les readmes
- `main.ts` est requis dans chaque sous-dossier, sauf s'il n'y a qu'un readme

## Déplacer et renommer des exercices

Lorsque vous renumérotez ou déplacez des exercices :

1. Utilisez `git mv` (pas `mv`) pour renommer les répertoires - cela préserve l'historique git
2. Mettez à jour le préfixe numérique pour maintenir l'ordre
3. Relancez le lint après les déplacements

Exemple :

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## Exemple : créer des stubs à partir d'un plan

Étant donné un plan comme :

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

Créez :

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

Puis créez les readmes de stub :

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
