---
name: migrate-to-shoehorn
description: Migre les fichiers de test des assertions de type `as` vers @total-typescript/shoehorn. Utilisez-le quand l'utilisateur mentionne shoehorn, veut remplacer `as` dans les tests, ou a besoin de données de test partielles.
---

# Migrer vers Shoehorn

## Pourquoi shoehorn ?

`shoehorn` vous permet de passer des données partielles dans les tests tout en gardant TypeScript satisfait. Il remplace les assertions `as` par des alternatives sûres en matière de types.

**Uniquement pour le code de test.** N'utilisez jamais shoehorn dans du code de production.

Problèmes posés par `as` dans les tests :

- On nous apprend à ne pas l'utiliser
- Il faut spécifier manuellement le type cible
- Le double `as` (`as unknown as Type`) pour des données volontairement erronées

## Installation

```bash
npm i @total-typescript/shoehorn
```

## Schémas de migration

### Gros objets avec peu de propriétés utiles

Avant :

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

Après :

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

Avant :

```ts
getUser({ body: { id: "123" } } as Request);
```

Après :

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

Avant :

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

Après :

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## Quand utiliser chaque fonction

| Fonction        | Cas d'usage                                                          |
| --------------- | -------------------------------------------------------------------- |
| `fromPartial()` | Passer des données partielles qui passent quand même la vérification |
| `fromAny()`     | Passer des données volontairement erronées (garde l'autocomplétion)  |
| `fromExact()`   | Forcer un objet complet (à échanger avec fromPartial plus tard)      |

## Déroulé

1. **Recueillir les besoins** - demandez à l'utilisateur :
   - Quels fichiers de test ont des assertions `as` qui posent problème ?
   - A-t-il affaire à de gros objets dont seules certaines propriétés comptent ?
   - Doit-il passer des données volontairement erronées pour tester les erreurs ?

2. **Installer et migrer** :
   - [ ] Installer : `npm i @total-typescript/shoehorn`
   - [ ] Trouver les fichiers de test avec des assertions `as` : `grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] Remplacer `as Type` par `fromPartial()`
   - [ ] Remplacer `as unknown as Type` par `fromAny()`
   - [ ] Ajouter les imports depuis `@total-typescript/shoehorn`
   - [ ] Lancer la vérification de types pour valider
