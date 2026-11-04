# Quand utiliser le mock

Utilisez le mock uniquement aux **frontières du système** :

- API externes (paiement, e-mail, etc.)
- Bases de données (parfois, préférez une base de test)
- Temps/aléatoire
- Système de fichiers (parfois)

N'utilisez pas de mock pour :

- Vos propres classes/modules
- Les collaborateurs internes
- Tout ce que vous contrôlez

## Concevoir pour le mocking

Aux frontières du système, concevez des interfaces faciles à mocker :

**1. Utilisez l'injection de dépendances**

Passez les dépendances externes en paramètre plutôt que de les créer à l'intérieur :

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. Préférez les interfaces de style SDK aux fetchers génériques**

Créez des fonctions spécifiques pour chaque opération externe au lieu d'une seule fonction générique avec de la logique conditionnelle :

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

L'approche SDK signifie :
- Chaque mock renvoie une forme spécifique
- Pas de logique conditionnelle dans la configuration du test
- Il est plus facile de voir quels endpoints un test exerce
- Sûreté de typage par endpoint
