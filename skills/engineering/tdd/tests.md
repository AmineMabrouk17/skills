# Bons et mauvais tests

## Bons tests

**Style intégration** : testez à travers de vraies interfaces, pas des mocks de parties internes.

```typescript
// GOOD: Tests observable behavior
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

Caractéristiques :

- Teste le comportement qui compte pour les utilisateurs/appelants
- N'utilise que l'API publique
- Survit aux refactorings internes
- Décrit QUOI, pas COMMENT
- Une seule assertion logique par test

## Mauvais tests

**Tests de détail d'implémentation** : couplés à la structure interne.

```typescript
// BAD: Tests implementation details
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

Signaux d'alerte :

- Mocker des collaborateurs internes
- Tester des méthodes privées
- Faire des assertions sur le nombre/ordre des appels
- Le test casse lors d'un refactoring sans changement de comportement
- Le nom du test décrit COMMENT, pas QUOI
- Vérifier par des moyens externes au lieu de l'interface

```typescript
// BAD: Bypasses interface to verify
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD: Verifies through interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**Tests tautologiques** : la valeur attendue reformule l'implémentation, donc le test passe par construction.

```typescript
// BAD: Expected value is recomputed the way the code computes it
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// GOOD: Expected value is an independent, known literal
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
