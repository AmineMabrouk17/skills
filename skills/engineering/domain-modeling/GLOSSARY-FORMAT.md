# Format de GLOSSARY.md

## Structure

```md
# {Context Name}

{One or two sentence description of what this context is and why it exists.}

## Language

**Order**:
{A one or two sentence description of the term}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account
```

## Règles

- **Ayez des partis pris.** Quand plusieurs mots existent pour un même concept, choisissez le meilleur et listez les autres sous `_Avoid_`.
- **Gardez des définitions serrées.** Une ou deux phrases au maximum. Définissez ce que c'est, pas ce que ça fait.
- **N'incluez que les termes propres au contexte de ce projet.** Les concepts généraux de programmation (délais d'attente, types d'erreur, motifs utilitaires) n'ont pas leur place même si le projet les utilise intensivement. Avant d'ajouter un terme, demandez-vous : est-ce un concept propre à ce contexte ou un concept général de programmation ? Seul le premier a sa place.
- **Groupez les termes sous des sous-titres** quand des regroupements naturels émergent. Si tous les termes appartiennent à une seule zone cohérente, une liste plate convient.

## Repos à contexte unique ou multiple

**Contexte unique (la plupart des repos) :** un seul `GLOSSARY.md` à la racine du repo.

**Contextes multiples :** un `GLOSSARY-MAP.md` à la racine du repo liste les contextes, où ils vivent et comment ils se rapportent les uns aux autres :

```md
# Glossary Map

## Contexts

- [Ordering](./src/ordering/GLOSSARY.md): receives and tracks customer orders
- [Billing](./src/billing/GLOSSARY.md): generates invoices and processes payments
- [Fulfillment](./src/fulfillment/GLOSSARY.md): manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

Le skill déduit quelle structure s'applique :

- Si `GLOSSARY-MAP.md` existe, lisez-le pour trouver les contextes
- Si seul un `GLOSSARY.md` racine existe, contexte unique
- Si aucun des deux n'existe, créez un `GLOSSARY.md` racine paresseusement quand le premier terme est résolu

Quand plusieurs contextes existent, déduisez de quel contexte relève le sujet en cours. Si c'est flou, demandez.
