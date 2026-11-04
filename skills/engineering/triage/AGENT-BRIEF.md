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

**Catégorie :** bug / enhancement
**Résumé :** description en une ligne de ce qui doit se passer

**Comportement actuel :**
Décrivez ce qui se passe maintenant. Pour les bugs, c'est le comportement cassé.
Pour les améliorations, c'est le statu quo sur lequel la fonctionnalité se construit.

**Comportement souhaité :**
Décrivez ce qui devrait se passer une fois le travail de l'agent terminé.
Soyez précis sur les cas limites et les conditions d'erreur.

**Interfaces clés :**
- `TypeName` : ce qui doit changer et pourquoi
- type de retour de `functionName()` : ce qu'elle renvoie actuellement vs ce qu'elle devrait renvoyer
- forme de la configuration : toute nouvelle option de configuration nécessaire

**Critères d'acceptation :**
- [ ] Critère 1 spécifique et testable
- [ ] Critère 2 spécifique et testable
- [ ] Critère 3 spécifique et testable

**Hors périmètre :**
- Chose qui ne doit PAS être modifiée ou traitée dans cette issue
- Fonctionnalité adjacente qui pourrait sembler liée mais qui est distincte
```

## Exemples

### Bon agent brief (bug)

```markdown
## Agent Brief

**Catégorie :** bug
**Résumé :** la troncature de la description de skill coupe au milieu d'un mot et produit une sortie cassée

**Comportement actuel :**
Lorsqu'une description de skill dépasse 1024 caractères, elle est tronquée à
exactement 1024 caractères sans tenir compte des limites de mots. Cela produit
des descriptions qui se terminent au milieu d'un mot (par exemple « Use when the user wants to confi »).

**Comportement souhaité :**
La troncature devrait couper à la dernière limite de mot avant 1024 caractères
et ajouter "..." pour indiquer la troncature.

**Interfaces clés :**
- Le champ `description` du type `SkillMetadata` : aucun changement de type
  nécessaire, mais la logique de validation/traitement qui le remplit doit
  respecter les limites de mots
- Toute fonction qui lit le frontmatter de SKILL.md et en extrait la description

**Critères d'acceptation :**
- [ ] Les descriptions de moins de 1024 caractères sont inchangées
- [ ] Les descriptions de plus de 1024 caractères sont tronquées à la dernière
      limite de mot avant 1024 caractères
- [ ] Les descriptions tronquées se terminent par "..."
- [ ] La longueur totale, "..." compris, ne dépasse pas 1024 caractères

**Hors périmètre :**
- Changer la limite de 1024 caractères elle-même
- Prise en charge des descriptions multilignes
```

### Bon agent brief (amélioration)

```markdown
## Agent Brief

**Catégorie :** enhancement
**Résumé :** ajouter la prise en charge d'un répertoire `.out-of-scope/` pour suivre les demandes de fonctionnalités rejetées

**Comportement actuel :**
Lorsqu'une demande de fonctionnalité est rejetée, l'issue est fermée avec un label `wontfix`
et un commentaire. Il n'existe aucun enregistrement persistant de la décision ni du raisonnement.
Les demandes similaires futures obligent le mainteneur à se souvenir ou à rechercher la
discussion antérieure.

**Comportement souhaité :**
Les demandes de fonctionnalités rejetées devraient être documentées dans des fichiers
`.out-of-scope/<concept>.md` qui capturent la décision, le raisonnement et des liens vers
toutes les issues qui ont demandé la fonctionnalité. Lors du triage de nouvelles issues,
ces fichiers devraient être vérifiés pour trouver des correspondances.

**Interfaces clés :**
- Format de fichier Markdown dans `.out-of-scope/` : chaque fichier devrait avoir un
  titre `# Concept Name`, une ligne `**Decision:**`, une ligne `**Reason:**`
  et une liste `**Prior requests:**` avec des liens vers les issues
- Le workflow de triage devrait lire tous les fichiers `.out-of-scope/*.md` tôt
  et faire correspondre les issues entrantes par similarité de concept

**Critères d'acceptation :**
- [ ] Fermer une fonctionnalité comme wontfix crée/met à jour un fichier dans `.out-of-scope/`
- [ ] Le fichier inclut la décision, le raisonnement et un lien vers l'issue fermée
- [ ] Si un fichier `.out-of-scope/` correspondant existe déjà, la nouvelle issue est
      ajoutée à sa liste « Prior requests » plutôt que de créer un doublon
- [ ] Pendant le triage, les fichiers `.out-of-scope/` existants sont vérifiés et remontés
      lorsqu'une nouvelle issue correspond à un rejet antérieur

**Hors périmètre :**
- Correspondance automatisée (un humain confirme la correspondance)
- Rouvrir des fonctionnalités précédemment rejetées
- Rapports de bug (seuls les rejets d'amélioration vont dans `.out-of-scope/`)
```

### Bon agent brief (PR)

Pour une PR, le « Current behavior » décrit l'état du diff, et l'agent brief demande à l'agent de le terminer ou de le corriger plutôt que de construire à partir de zéro.

```markdown
## Agent Brief

**Catégorie :** enhancement
**Résumé :** terminer le flag de sortie `--json` du contributeur pour `triage list`

**Comportement actuel :**
La PR ajoute un flag `--json` qui sérialise la liste des issues en JSON. Le chemin
nominal fonctionne et le diff correspond à la structure de commandes du projet. Deux lacunes
subsistent : les erreurs sont encore imprimées en texte humain (pas en JSON), et le nouveau flag n'a
aucune couverture de test.

**Comportement souhaité :**
Avec `--json`, toute la sortie (y compris les erreurs) est du JSON bien formé sur stdout,
et les codes de sortie de la commande sont inchangés. La sortie lisible par un humain existante
reste intacte lorsque le flag est absent.

**Interfaces clés :**
- Le chemin d'erreur de la commande devrait émettre `{ "error": string }` sous `--json`
  au lieu de l'erreur en texte brut
- Réutilisez le sérialiseur existant que la PR a déjà ajouté ; n'en introduisez pas un second

**Critères d'acceptation :**
- [ ] `triage list --json` émet du JSON valide pour les cas de succès comme d'erreur
- [ ] Les codes de sortie correspondent à ceux de la commande non-JSON
- [ ] Un test couvre la sortie de succès `--json` et un cas d'erreur
- [ ] La sortie par défaut (non-JSON) est identique octet pour octet

**Hors périmètre :**
- Ajouter `--json` à toute autre commande
- Changer la forme JSON de la charge utile de succès déjà définie par la PR
```

### Mauvais agent brief

```markdown
## Agent Brief

**Résumé :** corriger le bug de triage

**Quoi faire :**
Le truc de triage est cassé. Regardez le fichier principal et corrigez-le.
La fonction vers la ligne 150 a le problème.

**Fichiers à modifier :**
- src/triage/handler.ts (ligne 150)
- src/types.ts (ligne 42)
```

C'est mauvais parce que :
- Pas de catégorie
- Description vague (« le truc de triage est cassé »)
- Références à des chemins de fichiers et des numéros de ligne qui deviendront obsolètes
- Pas de critères d'acceptation
- Pas de limites de périmètre
- Pas de description du comportement actuel par rapport au comportement souhaité
