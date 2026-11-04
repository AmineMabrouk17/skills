# Format du rapport HTML

La revue architecturale est rendue sous la forme d'un seul fichier HTML autonome, dans le répertoire temporaire du système. Tailwind et Mermaid viennent tous deux de CDN. Mermaid gère de manière fiable les diagrammes en forme de graphe ; des divs construits à la main et du SVG inline gèrent les visuels plus éditoriaux (diagrammes de masse, coupes transversales). Mélangez les deux : ne vous appuyez pas sur Mermaid pour tout, cela finirait par paraître générique.

## Échafaudage

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review for {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## En-tête

Nom du repo, date, et une légende compacte : boîte pleine = module, ligne pointillée = seam, flèche rouge = fuite, boîte sombre épaisse = module profond. Pas de paragraphe d'introduction. On passe directement aux candidats.

## Carte de candidat

Ce sont les diagrammes qui portent le poids. La prose est rare, simple, et emploie les termes du glossaire (du skill `/codebase-design`) sans cérémonie.

Chaque candidat est un `<article>` :

- **Titre** : court, il nomme l'approfondissement (ex. « Effondrer le pipeline d'admission des commandes »).
- **Ligne de badges** : force de la recommandation (`Strong` = émeraude, `Worth exploring` = ambre, `Speculative` = ardoise), plus un tag pour la catégorie de dépendance (`in-process`, `local-substitutable`, `ports & adapters`, `mock`).
- **Fichiers** : liste en monospace, `font-mono text-sm`.
- **Diagramme Avant / Après** : la pièce maîtresse. Deux colonnes, côte à côte. Voir les motifs ci-dessous.
- **Problème** : une phrase. Ce qui fait mal.
- **Solution** : une phrase. Ce qui change.
- **Gains** : des puces, ≤6 mots chacune. Ex. « Les tests touchent une seule interface », « La logique de tarification cesse de fuir », « Supprimer 4 wrappers superficiels ».
- **Encadré ADR** (si applicable) : une ligne dans une boîte teintée ambre.

Pas de paragraphes d'explication. Si le diagramme a besoin d'un paragraphe pour être compris, redessinez le diagramme.

## Motifs de diagramme

Choisissez le motif qui convient au candidat. Mélangez-les. Ne faites pas en sorte que tous les diagrammes se ressemblent. La variété fait partie de l'objectif.

### Graphe Mermaid (la bête de somme pour les dépendances / le flux d'appels)

Utilisez un `flowchart` ou un `graph` Mermaid quand le point est « X appelle Y qui appelle Z, et regardez le désordre ». Enveloppez-le dans une carte stylée avec Tailwind pour qu'il ne donne pas l'impression d'avoir été parachuté. Appliquez les styles avec classDef pour colorer en rouge les arêtes de fuite et en sombre le module profond. Les diagrammes de séquence marchent bien pour « avant : 6 allers-retours ; après : 1 ».

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### Boîtes et flèches construites à la main (quand la mise en page de Mermaid vous résiste)

Des modules en `<div>` avec bordures et labels. Des flèches en éléments SVG inline `<line>` ou `<path>` positionnés en absolu au-dessus d'un conteneur relatif. Recourez-y quand vous voulez que le diagramme « après » ressemble à un module profond à grosse bordure avec des entrailles grisées, car Mermaid ne rendra pas cela avec le bon poids.

### Coupe transversale (bonne pour la superficialité en couches)

Empilez des bandes horizontales (`h-12 border-l-4`) pour montrer les couches qu'un appel traverse. Avant : 6 fines couches qui ne font rien. Après : 1 bande épaisse étiquetée avec la responsabilité consolidée.

### Diagramme de masse (bon pour « interface aussi large que l'implémentation »)

Deux rectangles par module : un pour la surface de l'interface, un pour l'implémentation. Avant : le rectangle de l'interface est presque aussi haut que celui de l'implémentation (superficiel). Après : le rectangle de l'interface est court, celui de l'implémentation est haut (profond).

### Effondrement du graphe d'appels

Avant : un arbre d'appels de fonctions rendu en boîtes imbriquées. Après : le même arbre effondré en une seule boîte, avec les appels devenus internes montrés en fondu à l'intérieur.

## Conseils de style

- Penchez pour l'éditorial, pas pour le tableau de bord d'entreprise. De l'espace généreux. Serif en option pour les titres (`font-serif` s'accorde bien avec stone/slate).
- De la couleur avec parcimonie : un accent (émeraude ou indigo) plus du rouge pour la fuite et de l'ambre pour les avertissements.
- Gardez les diagrammes à environ 320px de haut pour que l'avant/après tienne confortablement côte à côte sans défilement.
- Utilisez `text-xs uppercase tracking-wider` pour les labels de module dans les diagrammes, pour qu'ils se lisent comme un schéma, pas comme une UI.
- Les seuls scripts sont le CDN Tailwind et l'import ESM de Mermaid. Le rapport est statique par ailleurs : pas de code applicatif, pas d'interactivité au-delà du rendu propre de Mermaid.

## Section de recommandation principale

Une carte plus grande. Nom du candidat, une phrase sur le pourquoi, lien d'ancre vers sa carte. C'est tout.

## Ton

Un français simple et concis, mais les noms et verbes architecturaux viennent directement du skill `/codebase-design`. La concision n'est pas une excuse pour dériver.

**Utilisez exactement :** module, interface, implémentation, profondeur, profond, superficiel, seam, adaptateur, levier, localité.

**Ne substituez jamais :** composant, service, unité (pour module) · API, signature (pour interface) · frontière (pour seam) · couche, wrapper (pour module, quand vous voulez dire module).

**Formulations qui collent au style :**

- « Le module d'admission des commandes est superficiel : l'interface correspond presque à l'implémentation. »
- « La tarification fuit à travers le seam. »
- « Approfondissez : une interface, un seul endroit à tester. »
- « Deux adaptateurs justifient le seam : HTTP en prod, en mémoire dans les tests. »

**Les puces de gains** nomment le gain avec les termes du glossaire : *« localité : les bugs se concentrent dans un module »*, *« levier : une interface, N sites d'appel »*, *« l'interface rétrécit ; l'implémentation absorbe les wrappers »*. N'écrivez pas *« plus facile à maintenir »* ni *« code plus propre »*, car ces termes ne sont pas dans le glossaire et ne méritent pas leur place.

Pas de précautions oratoires, pas de délayage, pas de « il convient de noter que… ». Si une phrase pourrait être une puce, faites-en une puce. Si une puce pourrait être supprimée, supprimez-la. Si un terme n'est pas dans le glossaire du skill `/codebase-design`, cherchez-en un qui y soit avant d'en inventer un nouveau.
