---
name: codebase-design
description: Vocabulaire partagé pour concevoir des modules profonds. À utiliser quand l'utilisateur veut concevoir ou améliorer l'interface d'un module, trouver des occasions d'approfondissement, décider où placer un seam, rendre le code plus testable ou plus navigable par l'IA, ou quand un autre skill a besoin du vocabulaire des modules profonds.
---

# Conception de base de code

Concevez des **modules profonds** : beaucoup de comportement derrière une petite interface, placés sur un seam net, testables à travers cette interface. Employez ce vocabulaire et ces principes partout où du code est conçu ou restructuré. L'objectif : du levier pour les appelants, de la localité pour les mainteneurs et de la testabilité pour tout le monde.

## Glossaire

Employez ces termes exactement : ne les remplacez pas par « composant », « service », « API » ou « frontière ». Un vocabulaire cohérent est tout l'enjeu.

**Module** : tout ce qui a une interface et une implémentation. Délibérément agnostique à l'échelle : une fonction, une classe, un paquet ou une tranche qui traverse plusieurs niveaux. _À éviter_ : unité, composant, service.

**Interface** : tout ce qu'un appelant doit savoir pour utiliser le module correctement : la signature de type, mais aussi les invariants, les contraintes d'ordre, les modes d'erreur, la configuration requise et les caractéristiques de performance. _À éviter_ : API, signature (trop étroit, ces termes ne désignent que la surface au niveau des types).

**Implémentation** : ce qu'il y a à l'intérieur d'un module, son corps de code. À distinguer de l'**Adaptateur** : une chose peut être un petit adaptateur avec une grosse implémentation (un repo Postgres) ou un gros adaptateur avec une petite implémentation (un faux en mémoire). Employez « adaptateur » quand le seam est le sujet, « implémentation » sinon.

**Profondeur** : le levier au niveau de l'interface. La quantité de comportement qu'un appelant (ou un test) peut exercer par unité d'interface qu'il doit apprendre. Un module est **profond** quand beaucoup de comportement se trouve derrière une petite interface, **superficiel** quand l'interface est presque aussi complexe que l'implémentation.

**Seam** _(Michael Feathers)_ : un endroit où vous pouvez modifier le comportement sans éditer à cet endroit ; l'*emplacement* où vit l'interface d'un module. L'endroit où placer le seam est une décision de conception à part entière, distincte de ce qui se trouve derrière. _À éviter_ : frontière (surchargée par le contexte borné du DDD).

**Adaptateur** : une chose concrète qui satisfait une interface sur un seam. Décrit le *rôle* (la place qu'il occupe), pas la substance (ce qu'il y a dedans).

**Levier** : ce que les appelants gagnent de la profondeur. Plus de capacité par unité d'interface apprise. Une seule implémentation rentabilise N sites d'appel et M tests.

**Localité** : ce que les mainteneurs gagnent de la profondeur. Le changement, les bugs, la connaissance et la vérification se concentrent en un seul endroit au lieu de se disperser entre les appelants. Corrigé une fois, corrigé partout.

## Profond ou superficiel

**Module profond** = petite interface + beaucoup d'implémentation :

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│                     │
│  Deep Implementation│  ← Complex logic hidden
│                     │
└─────────────────────┘
```

**Module superficiel** = grande interface + peu d'implémentation (à éviter) :

```
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, complex params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

Lorsque vous concevez une interface, demandez-vous :

- Puis-je réduire le nombre de méthodes ?
- Puis-je simplifier les paramètres ?
- Puis-je cacher davantage de complexité à l'intérieur ?

## Principes

- **La profondeur est une propriété de l'interface, pas de l'implémentation.** Un module profond peut être composé en interne de petites parties mockables et interchangeables ; elles ne font simplement pas partie de l'interface. Un module peut avoir des **seams internes** (privés à son implémentation, utilisés par ses propres tests) ainsi que le **seam externe** à son interface.
- **Le test de suppression.** Imaginez supprimer le module. Si la complexité disparaît, c'était un simple passe-plat. Si la complexité réapparaît chez N appelants, il méritait sa place.
- **L'interface est la surface de test.** Les appelants et les tests franchissent le même seam. Si vous voulez tester *au-delà* de l'interface, le module a probablement la mauvaise forme.
- **Un adaptateur signifie un seam hypothétique. Deux adaptateurs signifient un seam réel.** N'introduisez pas de seam si rien ne varie réellement à travers lui.

## Concevoir pour la testabilité

De bonnes interfaces rendent les tests naturels :

1. **Acceptez les dépendances, ne les créez pas.**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **Retournez des résultats, ne produisez pas d'effets de bord.**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **Petite surface d'exposition.** Moins de méthodes = moins de tests nécessaires. Moins de paramètres = une mise en place de test plus simple.

## Relations

- Un **Module** a exactement une **Interface** (la surface qu'il présente aux appelants et aux tests).
- La **Profondeur** est une propriété d'un **Module**, mesurée par rapport à son **Interface**.
- Un **Seam** est l'endroit où vit l'**Interface** d'un **Module**.
- Un **Adaptateur** se place sur un **Seam** et satisfait l'**Interface**.
- La **Profondeur** produit du **Levier** pour les appelants et de la **Localité** pour les mainteneurs.

## Cadrages rejetés

- **La profondeur comme rapport entre lignes d'implémentation et lignes d'interface** (Ousterhout) : récompense le remplissage de l'implémentation. Nous utilisons plutôt la profondeur comme levier.
- **« Interface » comme mot-clé `interface` de TypeScript ou comme méthodes publiques d'une classe** : trop étroit ; ici, l'interface inclut tout fait qu'un appelant doit connaître.
- **« Frontière »** : surchargé par le contexte borné du DDD. Dites **seam** ou **interface**.

## Pour aller plus loin

- **Approfondir un groupe de modules compte tenu de ses dépendances**, voir [DEEPENING.md](DEEPENING.md) : catégories de dépendances, discipline des seams et tests par remplacement plutôt que par empilement.
- **Explorer des interfaces alternatives**, voir [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md) : lancez des subagents parallèles pour concevoir l'interface de plusieurs façons radicalement différentes, puis comparez sur la profondeur, la localité et le placement du seam.
