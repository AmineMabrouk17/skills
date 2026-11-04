---
name: tdd
description: Développement piloté par les tests. À utiliser quand l'utilisateur veut construire des fonctionnalités ou corriger des bugs en commençant par les tests, mentionne « red-green-refactor », ou veut des tests d'intégration.
---

# Développement piloté par les tests (TDD)

TDD est la boucle red → green. Ce skill est la référence qui fait de cette boucle un producteur de tests qui valent la peine d'être conservés : ce qu'est un bon test, où vont les tests, les anti-patrons et les règles de la boucle. Chaque section s'applique à chaque cycle : consultez-les avant et pendant la boucle, pas après.

Quand vous explorez la base de code, lisez `GLOSSARY.md` (s'il existe) pour que les noms de tests et le vocabulaire d'interface correspondent à la langue métier du projet, et respectez les ADR de la zone que vous touchez.

## Ce qu'est un bon test

Les tests vérifient le comportement à travers les interfaces publiques, pas les détails d'implémentation. Le code peut changer entièrement ; les tests ne doivent pas changer. Un bon test se lit comme une spécification : « user can checkout with valid cart » vous dit exactement quelle capacité existe, et il survit aux refactorings parce qu'il ne se soucie pas de la structure interne.

Voir [tests.md](tests.md) pour des exemples et [mocking.md](mocking.md) pour les recommandations sur le mocking.

## Les seams : où vont les tests

Un **seam** est la frontière publique au niveau de laquelle vous testez : l'interface où vous observez le comportement sans regarder à l'intérieur. Les tests vivent aux seams, jamais contre les internes.

**Ne testez qu'aux seams convenus à l'avance.** Avant d'écrire le moindre test, notez les seams à tester et confirmez-les avec l'utilisateur. Aucun test n'est écrit sur un seam non confirmé. Vous ne pouvez pas tout tester : convenir des seams en amont est donc la façon de concentrer l'effort de test sur les chemins critiques et la logique complexe plutôt que sur chaque cas limite.

Demandez : « Quelle est l'interface publique, et quels seams devons-nous tester ? » Ajoutez pour chaque seam proposé une note d'une ligne sur ce qu'il attrape et ce qu'il laisse passer.

Quand la forme de cette interface est elle-même en question (quelle est la profondeur du module, où se situe le seam, ce que l'interface doit exposer), appelez l'outil Skill avec « codebase-design » pour le vocabulaire. C'est la source partagée des termes module, interface, depth, seam, adapter, leverage et locality, et c'est une référence à consulter, pas une session à lancer.

## Anti-patrons

- **Couplé à l'implémentation** : utilise des mocks sur des collaborateurs internes, teste des méthodes privées ou vérifie via un canal latéral (interroger la base de données au lieu d'utiliser l'interface). Le signe : le test casse quand vous refactorisez alors que le comportement n'a pas changé.
- **Tautologique** : l'assertion recalcule la valeur attendue de la même façon que le code (`expect(add(a, b)).toBe(a + b)`, un snapshot dérivé à la main de la même manière, une constante comparée à elle-même), donc elle passe par construction et ne peut jamais être en désaccord avec le code. Les valeurs attendues doivent venir d'une source de vérité indépendante : un littéral connu comme correct, un exemple travaillé, la spec.
- **Découpage horizontal** : écrire tous les tests d'abord, puis toute l'implémentation. Les tests en masse vérifient un comportement _imaginé_ : vous testez la _forme_ des choses plutôt que le comportement visible par l'utilisateur, les tests deviennent insensibles aux vrais changements, et vous vous engagez sur la structure des tests avant de comprendre l'implémentation. Travaillez plutôt en **tranches verticales** : un test → une implémentation → on recommence, chaque test étant une **tracer bullet** qui répond à ce que le cycle précédent vous a appris.

## Règles de la boucle

- **Red avant green.** Écrivez d'abord le test qui échoue, puis seulement le code nécessaire pour le faire passer. N'anticipez pas les tests futurs et n'ajoutez pas de fonctionnalités spéculatives.
- **Une tranche à la fois.** Un seam, un test, une implémentation minimale par cycle.
- **Le refactoring ne fait pas partie de la boucle.** Il appartient à l'étape de revue (voir le skill `code-review`), pas au cycle d'implémentation red → green.
