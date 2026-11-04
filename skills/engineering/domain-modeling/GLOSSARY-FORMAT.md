# Format de GLOSSARY.md

## Structure

```md
# {Nom du contexte}

{Description en une ou deux phrases de ce qu'est ce contexte et pourquoi il existe.}

## Langage

**Commande :**
{Description en une ou deux phrases du terme}
_Avoid_ : Achat, transaction

**Facture :**
Une demande de paiement envoyée à un client après livraison.
_Avoid_ : Note, demande de paiement

**Client :**
Une personne ou une organisation qui passe des commandes.
_Avoid_ : Consommateur, acheteur, compte
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
# Carte du glossaire

## Contextes

- [Ordering](./src/ordering/GLOSSARY.md) : reçoit et suit les commandes client
- [Billing](./src/billing/GLOSSARY.md) : génère les factures et traite les paiements
- [Fulfillment](./src/fulfillment/GLOSSARY.md) : gère la préparation et l'expédition en entrepôt

## Relations

- **Ordering → Fulfillment** : Ordering émet des événements `OrderPlaced` ; Fulfillment les consomme pour lancer la préparation
- **Fulfillment → Billing** : Fulfillment émet des événements `ShipmentDispatched` ; Billing les consomme pour générer des factures
- **Ordering ↔ Billing** : types partagés pour `CustomerId` et `Money`
```

Le skill déduit quelle structure s'applique :

- Si `GLOSSARY-MAP.md` existe, lisez-le pour trouver les contextes
- Si seul un `GLOSSARY.md` racine existe, contexte unique
- Si aucun des deux n'existe, créez un `GLOSSARY.md` racine paresseusement quand le premier terme est résolu

Quand plusieurs contextes existent, déduisez de quel contexte relève le sujet en cours. Si c'est flou, demandez.
