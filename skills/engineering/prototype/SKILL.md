---
name: prototype
description: Construisez un prototype jetable pour répondre à une question de conception. À utiliser quand l'utilisateur veut vérifier si un modèle d'état ou une logique sonne juste, ou explorer à quoi une UI devrait ressembler.
---

# Prototype

Un prototype est du **code jetable qui répond à une question**. La question décide de la forme.

## Choisir une branche

Identifiez à quelle question on répond, en vous appuyant sur le prompt de l'utilisateur, sur le code environnant, ou en demandant si l'utilisateur est disponible :

- **« Est-ce que cette logique / ce modèle d'état sonne juste ? »** → [LOGIC.md](LOGIC.md). Construisez un seul fichier HTML partageable (boutons de jeu libre plus parcours guidés en onglets) qui pousse la machine à états à travers des cas difficiles à raisonner sur le papier, et qu'une personne non développeuse peut piloter.
- **« À quoi cela devrait-il ressembler ? »** → [UI.md](UI.md). Générez plusieurs variations d'UI radicalement différentes sur une seule route, commutables via un paramètre de recherche d'URL et une barre flottante en bas.

Les deux branches produisent des artefacts très différents, donc se tromper de branche gaspille tout le prototype. Si la question est réellement ambiguë et que l'utilisateur est injoignable, prenez par défaut la branche qui correspond le mieux au code environnant (un module backend → logique ; une page ou un composant → UI) et énoncez l'hypothèse en haut du prototype.

## Règles qui s'appliquent aux deux

1. **Jetable dès le premier jour, et clairement signalé comme tel.** Placez le code du prototype près de l'endroit où il sera réellement utilisé (à côté du module ou de la page qu'il sert à prototyper) pour que le contexte soit évident, mais nommez-le pour qu'un lecteur de passage voie qu'il s'agit d'un prototype, pas de la production. Pour les routes d'UI jetables, respectez la convention de routage que le projet utilise déjà ; n'inventez pas une nouvelle structure de premier niveau.
2. **Trivial à lancer.** Un prototype d'UI démarre depuis une seule commande du task runner du projet : `pnpm <name>`, `python <path>`, `bun <path>`, etc. Une démo de logique est un seul fichier HTML que l'utilisateur double-clique. Dans les deux cas, aucune réflexion requise pour le démarrer.
3. **Pas de persistance par défaut.** L'état vit en mémoire. La persistance est ce que le prototype _vérifie_, pas ce dont il doit dépendre. Si la question implique explicitement une base de données, tapez sur une base de travail ou un fichier local au nom explicite « PROTOTYPE, wipe me ».
4. **Sautez le polissage.** Pas de tests, pas de gestion d'erreurs au-delà de ce qui rend le prototype _exécutable_, pas d'abstractions. Le but est d'apprendre quelque chose vite.
5. **Exposez l'état.** Après chaque action (logique) ou à chaque changement de variante (UI), imprimez ou rendez l'état pertinent complet pour que l'utilisateur voie ce qui a changé.
6. **Consignez-le une fois terminé.** Intégrez toute décision validée au vrai code, puis consignez le prototype lui-même comme **source primaire** : committez-le sur une branche jetable, hors de `main`, et laissez un pointeur de contexte vers cette branche sur l'issue d'implémentation. Consignez aussi la réponse (le verdict et la question qu'il a tranchée) dans l'issue ou un commit. La branche principale ne garde que la décision validée.
