# Approfondissement

Comment approfondir sans risque un groupe de modules superficiels, compte tenu de ses dépendances. Suppose le vocabulaire de [SKILL.md](SKILL.md) : **module**, **interface**, **seam**, **adaptateur**.

## Catégories de dépendances

Lorsque vous évaluez un candidat à l'approfondissement, classez ses dépendances. La catégorie détermine comment le module approfondi est testé à travers son seam.

### 1. Dans le processus

Calcul pur, état en mémoire, aucune entrée-sortie. Toujours approfondissable : fusionnez les modules et testez directement à travers la nouvelle interface. Aucun adaptateur nécessaire.

### 2. Substitutable localement

Dépendances qui ont des substituts locaux pour les tests (PGLite pour Postgres, système de fichiers en mémoire). Approfondissable si le substitut existe. Le module approfondi est testé avec le substitut exécuté dans la suite de tests. Le seam est interne ; aucun port à l'interface externe du module.

### 3. Distant mais possédé (Ports et adaptateurs)

Vos propres services à travers une frontière réseau (microservices, API internes). Définissez un **port** (interface) au niveau du seam. Le module profond possède la logique ; le transport est injecté comme **adaptateur**. Les tests utilisent un adaptateur en mémoire. La production utilise un adaptateur HTTP/gRPC/file d'attente.

Forme de la recommandation : *« Définissez un port au niveau du seam, implémentez un adaptateur HTTP pour la production et un adaptateur en mémoire pour les tests, afin que la logique réside dans un seul module profond même s'il est déployé à travers un réseau. »*

### 4. Vraiment externe (mock)

Services tiers (Stripe, Twilio, etc.) que vous ne contrôlez pas. Le module approfondi prend la dépendance externe comme port injecté ; les tests fournissent un adaptateur mock.

## Discipline des seams

- **Un adaptateur signifie un seam hypothétique. Deux adaptateurs signifient un seam réel.** N'introduisez pas de port tant qu'au moins deux adaptateurs ne sont pas justifiés (typiquement production + test). Un seam à un seul adaptateur n'est qu'une indirection.
- **Seams internes et seams externes.** Un module profond peut avoir des seams internes (privés à son implémentation, utilisés par ses propres tests) ainsi que le seam externe à son interface. N'exposez pas les seams internes à travers l'interface simplement parce que les tests les utilisent.

## Stratégie de test : remplacer, pas empiler

- Les anciens tests unitaires sur des modules superficiels deviennent du gaspillage dès que des tests existent à l'interface du module approfondi ; supprimez-les.
- Écrivez de nouveaux tests à l'interface du module approfondi. L'**interface est la surface de test**.
- Les tests portent sur des résultats observables à travers l'interface, pas sur l'état interne.
- Les tests doivent survivre aux refactorings internes, car ils décrivent le comportement, pas l'implémentation. Si un test doit changer quand l'implémentation change, c'est qu'il teste au-delà de l'interface.
