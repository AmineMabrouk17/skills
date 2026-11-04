---
name: improve-codebase-architecture
description: Analysez une base de code pour repérer des occasions d'approfondissement, présentez-les dans un rapport HTML visuel, puis passez au crible celle que vous choisissez.
disable-model-invocation: true
---

# Améliorer l'architecture d'une base de code

Faites émerger les frictions architecturales et proposez des **occasions d'approfondissement** : des refactorings qui transforment des modules superficiels en modules profonds. L'objectif est la testabilité et la navigabilité par l'IA.

Cette commande est _éclairée_ par le modèle de domaine du projet et s'appuie sur un vocabulaire de conception partagé :

- Appelez l'outil Skill avec « codebase-design » pour le vocabulaire d'architecture (**module**, **interface**, **profondeur**, **seam**, **adaptateur**, **levier**, **localité**) et ses principes (le test de suppression, « l'interface est la surface de test », « un adaptateur = seam hypothétique, deux = seam réel »). Utilisez ces termes exactement dans chaque suggestion, et ne dérivez pas vers « composant », « service », « API » ou « frontière ».
- Le langage de domaine dans `GLOSSARY.md` donne des noms aux bons seams ; les ADR dans `docs/adr/` consignent des décisions que cette commande ne doit pas remettre en cause.

## Processus

### 1. Explorer

**Délimitez avant de balayer : YAGNI.** Approfondir un module est rentable parce que cela rend ses changements futurs plus faciles, donc accordez un poids supplémentaire aux parties de la base de code qui ont changé récemment. Décidez *où* regarder avant de regarder :

- Si l'utilisateur a nommé une direction (un module, un sous-système, un point de douleur), prenez-la et sautez l'inférence ci-dessous.
- Sinon, remontez une bonne partie de l'historique des commits (`git log --oneline`) pour trouver les points chauds de la base de code, les fichiers et zones qui reviennent sans cesse, et laissez ces chemins attirer votre attention en premier. Si les changements sont éparpillés sans point chaud évident, élargissez le filet.

Lisez d'abord le glossaire de domaine du projet (`GLOSSARY.md`) et les ADR de la zone que vous touchez.

Ensuite, lancez un subagent avec une fenêtre de contexte neuve pour parcourir la base de code. Ne suivez pas d'heuristiques rigides ; explorez de façon organique et notez où vous rencontrez de la friction :

- Où comprendre un concept oblige-t-il à rebondir entre beaucoup de petits modules ?
- Où les modules sont-ils **superficiels**, avec une interface presque aussi complexe que l'implémentation ?
- Où des fonctions pures ont-elles été extraites juste pour la testabilité, alors que les vrais bugs se cachent dans la façon dont elles sont appelées (pas de **localité**) ?
- Où des modules étroitement couplés fuient-ils à travers leurs seams ?
- Quelles parties de la base de code sont non testées, ou difficiles à tester à travers leur interface actuelle ?

Appliquez le **test de suppression** à tout ce que vous soupçonnez d'être superficiel : le supprimer concentrerait-il la complexité, ou la déplacerait-il simplement ? Un « oui, cela concentre » est le signal que vous voulez.

### 2. Présenter les candidats dans un rapport HTML

Écrivez un fichier HTML autonome dans le répertoire temporaire du système pour que rien n'atterrisse dans le repo. Résolvez le répertoire temporaire à partir de `$TMPDIR`, avec repli sur `/tmp` (ou `%TEMP%` sous Windows), et écrivez dans `<tmpdir>/architecture-review-<timestamp>.html` pour que chaque exécution obtienne un fichier neuf. Ouvrez-le pour l'utilisateur (`xdg-open <path>` sous Linux, `open <path>` sous macOS, `start <path>` sous Windows) et donnez-lui le chemin absolu.

Le rapport utilise **Tailwind via CDN** pour la mise en page et le style, et **Mermaid via CDN** pour les diagrammes là où un graphe/flux/séquence communique la structure de façon fiable. Mélangez Mermaid avec des visuels CSS/SVG faits main : utilisez Mermaid quand les relations sont en forme de graphe (graphes d'appels, dépendances, séquences), et des divs/SVG construits à la main quand vous voulez quelque chose de plus éditorial (diagrammes de masse, coupes transversales, animations d'effondrement). Chaque candidat reçoit une **visualisation avant/après**. Soyez visuel.

Pour chaque candidat, rendez une carte avec :

- **Fichiers** : quels fichiers/modules sont concernés
- **Problème** : pourquoi l'architecture actuelle crée de la friction
- **Solution** : description en français simple de ce qui changerait
- **Bénéfices** : expliqués en termes de localité et de levier, et comment les tests s'amélioreraient
- **Diagramme Avant / Après** : côte à côte, dessiné sur mesure, illustrant la superficialité et l'approfondissement
- **Force de la recommandation** : l'une de `Strong`, `Worth exploring`, `Speculative`, rendue en badge

Terminez le rapport par une section **Recommandation principale** : quel candidat vous attaqueriez en premier et pourquoi.

**Utilisez le vocabulaire de GLOSSARY.md pour le domaine, et le vocabulaire de `/codebase-design` pour l'architecture.** Si `GLOSSARY.md` définit « Order », parlez du « module d'admission des commandes », pas du « FooBarHandler », et pas du « service Order ».

**Conflits d'ADR** : si un candidat contredit un ADR existant, ne le signalez que quand la friction est assez réelle pour justifier de revisiter l'ADR. Marquez-le clairement dans la carte (ex. un encadré d'avertissement : _« contredit l'ADR-0007, mais vaut la peine d'être rouvert parce que… »_). Ne listez pas tous les refactorings théoriques qu'un ADR interdit.

Voir [HTML-REPORT.md](HTML-REPORT.md) pour l'échafaudage HTML complet, les motifs de diagramme et les conseils de style.

Ne proposez PAS encore d'interfaces. Une fois le fichier écrit, demandez à l'utilisateur : « Lequel de ceux-là aimeriez-vous explorer ? »

### 3. Boucle de grillage

Une fois que l'utilisateur a choisi un candidat, appelez l'outil Skill avec « grilling » pour parcourir l'arbre de décision avec lui : contraintes, dépendances, la forme du module approfondi, ce qui se trouve derrière le seam, quels tests survivent.

Les effets de bord se produisent en ligne au fil des décisions ; appelez l'outil Skill avec « domain-modeling » pour garder le modèle de domaine à jour en cours de route :

- **Vous nommez un module approfondi d'après un concept absent de `GLOSSARY.md` ?** Ajoutez le terme à `GLOSSARY.md`. Créez le fichier paresseusement s'il n'existe pas.
- **Vous affinez un terme flou pendant la conversation ?** Mettez à jour `GLOSSARY.md` sur-le-champ.
- **L'utilisateur rejette le candidat avec une raison porteuse ?** Proposez un ADR, formulé ainsi : _« Voulez-vous que je consigne cela dans un ADR pour que les futures revues d'architecture ne le resuggèrent pas ? »_ Ne le proposez que si la raison serait réellement nécessaire à un futur explorateur pour éviter de resuggérer la même chose ; sautez les raisons éphémères (« pas le moment ») et celles qui vont de soi.
- **Vous voulez explorer des interfaces alternatives pour le module approfondi ?** Appelez l'outil Skill avec « codebase-design » et utilisez son motif de sous-agents parallèles « design-it-twice ».
