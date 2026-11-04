---
name: wizard
description: Générez un wizard bash interactif qui guide un humain à travers des étapes que lui seul peut accomplir. À utiliser pour provisionner une infrastructure, configurer des identifiants ou des secrets CI, parcourir un tableau de bord tiers inconnu, ou exécuter une migration ou un basculement ponctuel. N'invoquez pas ce skill pour des étapes que l'agent peut accomplir lui-même.
---

# Wizard

Un **wizard** est un script bash qui guide un humain, étape par étape, à travers une procédure manuelle pénible à faire à la main et pénible à réexpliquer à une IA à chaque fois. Il ouvre chaque URL, indique exactement quoi cliquer et copier, capture les valeurs, les écrit là où elles doivent aller (`.env`, secrets GitHub), confirme à chaque étape et montre combien d'étapes il reste. Il peut configurer des services tiers, exécuter une migration ponctuelle, ou faire passer le projet d'un état à un autre.

L'UX agréable est déjà résolue par [template.sh](template.sh) : progression étape par étape, portes de confirmation, ouverture d'URL multiplateforme (y compris WSL), saisie masquée des secrets, upserts idempotents dans `.env`, écritures `gh secret`/`gh variable`, et résumé final. **Votre travail consiste uniquement à cadrer la procédure et à rédiger ses étapes.** La bibliothèque au-dessus du marqueur `STAGES` est identique dans chaque wizard ; cette cohérence est le but : ne la modifiez jamais à la main.

Un wizard est éphémère par défaut : construit pour une seule exécution, enregistré dans un chemin scratch ou `scripts/`, supprimé lorsque le travail est fait. Ne le committez que si l'utilisateur veut un chemin de configuration reproductible qui doit vivre dans le repo.

## Processus

### 1. Cadrer la procédure

Déterminez chaque étape manuelle que l'humain doit accomplir et chaque valeur capturée en chemin. Lisez d'abord le repo, ne demandez pas à froid :

- Pour une configuration : `.env`, `.env.example`, `.env.*`, `README`, `docker-compose*`, la configuration du framework, et `.github/workflows/*` (chaque référence `secrets.*` / `vars.*` est une valeur que le wizard doit produire).
- Pour une migration ou une transition : l'état actuel, l'état cible, et les actions irréversibles entre les deux.

Montrez ensuite à l'utilisateur la liste ordonnée des étapes et les valeurs que chacune produit, et confirmez : il peut en ajouter, en retirer ou les réordonner.

**Terminé quand :** chaque étape est nommée dans l'ordre, et pour chaque valeur capturée vous savez (a) où l'humain l'obtient, (b) où elle est écrite (`.env`, un secret GitHub, les deux, ou nulle part ; certaines étapes sont de pures actions), et (c) si elle est secrète (saisie masquée) ou publique.

### 2. Cartographier le parcours de chaque étape

Pour chaque étape, écrivez le chemin précis qu'un humain suit : quelle URL ouvrir, quoi y faire, où une valeur est affichée, quelle variable elle remplit : par exemple « Dashboard → Developers → API keys → Reveal test key → copy ». Là où vous ne connaissez pas réellement l'UI actuelle ou la commande exacte, dites-le et demandez à l'utilisateur ou consultez la documentation : n'inventez jamais des étapes qui pourraient ne pas exister.

**Terminé quand :** chaque étape se traduit par des instructions concrètes qu'un inconnu pourrait suivre.

### 3. Rédiger le wizard

Copiez `template.sh` vers le chemin cible. Remplacez l'étape d'exemple par un `stage` par étape, dans l'ordre des dépendances. Utilisez les helpers de la bibliothèque : `stage`, `say`/`step`, `open_url`, `ask`/`ask_secret`, `write_env`, `set_secret`/`set_var`, `pause`/`confirm`. Définissez `TOTAL_STAGES` sur le nombre d'étapes que vous avez écrites.

Tenez la barre fixée par le modèle : ouvrez l'URL avant de demander sa valeur, utilisez `ask_secret` pour tout ce qui est secret, `write_env` pour chaque valeur persistée, `set_secret` uniquement pour les valeurs dont la CI a réellement besoin, et `confirm` avant toute action irréversible. Chaque `stage` efface l'écran pour que seule l'étape en cours soit visible : gardez une étape sur une seule tâche ciblée afin que rien de ce dont l'humain a besoin ne défile hors de vue. Ne touchez pas à la bibliothèque au-dessus du marqueur.

### 4. Vérifier et transmettre

- `bash -n <script>` ; exécutez `shellcheck` si disponible.
- `chmod +x <script>`.
- Ne l'exécutez pas de bout en bout vous-même : il ouvre des navigateurs et bloque sur une saisie humaine. Tracez-le statiquement à la place : chaque valeur de l'étape 1 est capturée et atterrit là où l'étape 1 l'a indiqué, et chaque nom de `set_secret` correspond exactement à une référence `secrets.*` dans la CI.
- Indiquez à l'utilisateur comment l'exécuter. S'il s'agit d'un chemin de configuration reproductible, committez-le et liez-le depuis le README afin que la personne suivante exécute le script au lieu de demander à une IA.
