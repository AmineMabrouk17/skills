# Prototype d'UI

Générez **plusieurs variations d'UI radicalement différentes** sur une seule route, commutables depuis une barre flottante en bas. L'utilisateur bascule entre les variantes dans le navigateur, en choisit une (ou vole des morceaux à chacune), puis jette le reste.

Si la question porte sur la logique/l'état plutôt que sur l'apparence de quelque chose, c'est la mauvaise branche. Utilisez [LOGIC.md](LOGIC.md).

## Quand c'est la bonne forme

- « À quoi cette page devrait-elle ressembler ? »
- « Je veux voir quelques options pour ce tableau de bord avant de m'engager. »
- « Essaie une mise en page différente pour l'écran de réglages. »
- Chaque fois que l'utilisateur passerait sinon une journée à choisir mentalement entre trois maquettes floues.

## Deux sous-formes : préférez fortement la sous-forme A

Un prototype d'UI est bien plus facile à juger quand il **touche au reste de l'application** : vrai en-tête, vraie barre latérale, vraies données, vraie densité. Une route jetable toute seule est un vide : chaque variante paraît correcte isolément. Prenez la sous-forme A par défaut chaque fois qu'une page existante plausible peut héberger les variantes. Ne recourez à la sous-forme B que si le prototype n'a réellement pas de foyer proche.

### Sous-forme A : ajustement à une page existante (préférée)

La route existe déjà. Les variantes sont rendues **sur la même route**, contrôlées par un paramètre de recherche d'URL `?variant=`. Le chargement de données, les paramètres et l'authentification existants restent tous. Seul le rendu permute. C'est le défaut ; choisissez-le sauf raison précise de ne pas le faire.

Si le prototype concerne quelque chose qui n'a pas encore de page mais qui *vivrait naturellement dans une page* (une nouvelle section du tableau de bord, une nouvelle carte sur l'écran de réglages, une nouvelle étape d'un flux existant), c'est encore la sous-forme A. Montez les variantes à l'intérieur de la page hôte.

### Sous-forme B : une nouvelle page (en dernier recours)

Ne l'utilisez que quand la chose prototypée n'a réellement aucune page existante où vivre (par ex. une surface entièrement nouvelle de premier niveau, ou un flux qui ne peut pas être intégré ailleurs de façon sensée).

Créez une **route jetable** en suivant la convention de routage que le projet utilise déjà. N'inventez pas une nouvelle structure de premier niveau. Nommez-la pour qu'elle soit manifestement un prototype (par ex. incluez le mot `prototype` dans le chemin ou le nom de fichier). Même motif `?variant=`.

Avant de vous engager sur la sous-forme B, vérifiez : n'y a-t-il vraiment aucune page existante où cela pourrait s'intégrer ? Une route vide cache des problèmes de conception qu'une route peuplée révélerait.

Dans les deux sous-formes, la barre flottante en bas est identique.

## Processus

### 1. Énoncer la question et choisir N

Prenez **3 variantes** par défaut. Au-delà de 5, elles cessent d'être radicalement différentes et deviennent du bruit, donc plafonnez là.

Notez le plan en une ligne, à l'emplacement du prototype ou dans un commentaire en haut de fichier :

> « Trois variantes de la page de réglages, commutables via `?variant=`, sur la route `/settings` existante. »

Cela marche que l'utilisateur soit là pour objecter ou non.

### 2. Générer des variantes radicalement différentes

Esquissez chaque variante. Tenez chacune à :

- La raison d'être de la page et les données auxquelles elle a accès.
- La bibliothèque de composants / le système de style du projet (TailwindCSS, shadcn, MUI, CSS simple, peu importe).
- Un nom de composant exporté clair, par ex. `VariantA`, `VariantB`, `VariantC`.

Les variantes doivent être **structurellement différentes** : mise en page différente, hiérarchie de l'information différente, affordance principale différente, pas seulement des couleurs différentes. Trois grilles de cartes à peine retouchées, ce n'est pas un prototype d'UI, c'est du papier peint. Si deux esquisses se ressemblent trop, refaites-en une avec la consigne explicite « n'utilise pas de grille de cartes ».

### 3. Les câbler ensemble

Créez un composant de commutation unique sur la route :

```tsx
// pseudo-code, adapt to the project's framework
const variant = searchParams.get('variant') ?? 'A';
return (
  <>
    {variant === 'A' && <VariantA {...data} />}
    {variant === 'B' && <VariantB {...data} />}
    {variant === 'C' && <VariantC {...data} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </>
);
```

Pour la sous-forme A (page existante) : gardez tout le chargement de données existant au-dessus du commutateur ; seul le sous-arbre rendu change selon la variante.

Pour la sous-forme B (nouvelle page) : la route jetable sous `/prototype/<name>` monte le même commutateur.

### 4. Construire le commutateur flottant

Une petite barre en position fixe, en bas au centre de l'écran, avec trois pièces :

- **Flèche gauche** : passe à la variante précédente (en boucle).
- **Label de variante** : montre la clé de la variante courante et, si la variante exporte un nom, ce nom aussi. Par ex. `B (Sidebar layout)`.
- **Flèche droite** : passe à la suivante (en boucle).

Comportement :

- Cliquer sur une flèche met à jour le paramètre de recherche d'URL (utilisez le routeur du framework, par ex. `router.replace` sur Next, `navigate` sur React Router, etc.) pour que la variante soit partageable et stable au rechargement.
- Clavier : les touches fléchées `←` et `→` font aussi défiler. N'interceptez pas les touches fléchées quand un `<input>`, un `<textarea>` ou un `[contenteditable]` a le focus.
- Visuellement distinct de la page (par ex. pilule à fort contraste, ombre subtile) pour qu'il ne fasse manifestement pas partie du design évalué.
- Masqué dans les builds de production : conditionnez-le sur `process.env.NODE_ENV !== 'production'` ou une vérification équivalente, pour qu'un merge de prototype égaré ne puisse pas livrer la barre aux utilisateurs.

Placez le commutateur dans un composant partagé unique pour que les deux sous-formes puissent le réutiliser. Localisez-le là où vit l'UI partagée dans le projet.

### 5. Transmettez-le

Communiquez l'URL (et les clés `?variant=`). L'utilisateur basculera quand il y arrivera. Le retour intéressant est généralement **« je veux l'en-tête de B avec la barre latérale de C »**, qui est le design qu'il veut réellement.

### 6. Consigner la réponse et nettoyer

Une fois qu'une variante a gagné, consignez la réponse (quelle variante et pourquoi), puis consignez le prototype comme le décrit le [SKILL](SKILL.md). Intégrez la gagnante au vrai code et déplacez le reste vers la branche jetable, pas dans `main` :

- **Sous-forme A** : intégrez la gagnante à la page existante ; retirez de `main` les variantes perdantes et le commutateur.
- **Sous-forme B** : promouvez la variante gagnante en vraie route ; retirez de `main` la route jetable et le commutateur.

L'ensemble complet des variantes est la source primaire, donc il atterrit sur la branche jetable, pas à la poubelle, car des composants de variante et un commutateur laissés dans la branche principale pourrissent vite et embrouillent le prochain lecteur.

## Anti-motifs

- **Des variantes qui ne diffèrent que par la couleur ou le texte.** C'est une retouche, pas un prototype. De vraies variantes sont en désaccord sur la structure.
- **Partager trop de code entre variantes.** Un `<Header>` partagé va bien ; un `<Layout>` partagé tue l'intérêt. Chaque variante doit être libre de jeter la mise en page.
- **Câbler des variantes à de vraies mutations.** Les prototypes en lecture seule sont très bien. Si une variante doit muter, pointez-la vers un stub : la question est « à quoi cela devrait-il ressembler », pas « est-ce que le backend marche ».
- **Promouvoir le prototype directement en production.** Le code des variantes a été écrit sous contraintes de prototype (pas de tests, gestion d'erreurs minimale). Réécrivez-le correctement quand vous l'intégrez.
