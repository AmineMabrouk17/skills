---
name: diagnosing-bugs
description: Boucle de diagnostic pour les bugs difficiles et les régressions de performance. À utiliser quand l'utilisateur dit « diagnostique »/« debug this », ou signale quelque chose de cassé, qui lève une erreur, échoue ou est lent.
---

# Diagnostiquer les bugs

Une discipline pour les bugs difficiles. Ne sautez des phases que lorsque c'est explicitement justifié.

Quand vous explorez la base de code, lisez `GLOSSARY.md` (s'il existe) pour vous faire un modèle mental clair des modules concernés, et consultez les ADR de la zone que vous touchez.

## Caviarder

Ce skill vous amène à montrer des commandes, des sorties et des artefacts capturés. **Caviardez d'abord chaque secret** : écrivez `<REDACTED>` à sa place. Construisez les boucles à partir de variables d'environnement, pour que l'identifiant reste dans l'environnement plutôt que dans ce que vous montrez. Les artefacts capturés portent des en-têtes d'authentification : ne citez que les lignes qui portent le signal.

Si la sortie caviardée ne suffit pas à diagnostiquer le bug, dites-le et demandez à l'utilisateur.

## Phase 1 : Construire une boucle de rétroaction

**C'est ça, le skill.** Tout le reste est mécanique. Si vous avez un signal pass/fail **serré** pour le bug (un qui passe au rouge sur _ce_ bug), vous trouverez la cause ; la bissection, le test d'hypothèses et l'instrumentation ne font que le consommer. Si vous n'en avez pas, aucune quantité de contemplation du code ne vous sauvera.

Dépensez un effort disproportionné ici. **Soyez agressif. Soyez créatif. Refusez d'abandonner.**

### Façons d'en construire une, à peu près dans cet ordre

1. **Test qui échoue** au seam qui atteint le bug : unitaire, intégration, e2e.
2. **Script Curl / HTTP** contre un serveur de dev en cours d'exécution.
3. **Invocation CLI** avec une entrée fixture, en comparant stdout à un snapshot connu comme bon.
4. **Script de navigateur headless** (Playwright / Puppeteer) qui pilote l'UI et vérifie le DOM/la console/le réseau.
5. **Rejouer une trace capturée.** Enregistrez une vraie requête réseau / payload / journal d'événements sur disque ; rejouez-la dans le chemin de code en isolation.
6. **Harnais jetable.** Montez un sous-ensemble minimal du système (un service, des dépendances mockées) qui exerce le chemin de code du bug avec un seul appel de fonction.
7. **Boucle property / fuzz.** Si le bug est « parfois une sortie fausse », lancez 1000 entrées aléatoires et cherchez le mode de défaillance.
8. **Harnais de bissection.** Si le bug est apparu entre deux états connus (commit, jeu de données, version), automatisez « démarrer à l'état X, vérifier, répéter » pour pouvoir le `git bisect run`.
9. **Boucle différentielle.** Passez la même entrée dans l'ancienne version vs la nouvelle (ou deux configs) et comparez les sorties.
10. **Script bash HITL.** Dernier recours. Si un humain doit cliquer, pilotez-le _lui_ avec `scripts/hitl-loop.template.sh` pour que la boucle reste structurée. La sortie capturée vous revient.

Construisez la bonne boucle de rétroaction, et le bug est corrigé à 90 %.

### Resserrer la boucle

Traitez la boucle comme un produit. Une fois que vous avez _une_ boucle, **resserrez-la** :

- Puis-je la rendre plus rapide ? (Mettre en cache la préparation, sauter l'init sans rapport, réduire la portée du test.)
- Puis-je rendre le signal plus net ? (Vérifier le symptôme précis, pas « n'a pas planté ».)
- Puis-je la rendre plus déterministe ? (Figer le temps, grainer le RNG, isoler le système de fichiers, geler le réseau.)

Une boucle instable de 30 secondes vaut à peine mieux que pas de boucle ; une boucle déterministe de 2 secondes est serrée, un superpouvoir de débogage.

### Bugs non déterministes

Le but n'est pas une repro propre mais un **taux de reproduction plus élevé**. Bouclez le déclencheur 100×, parallélisez, ajoutez de la charge, resserrez les fenêtres temporelles, injectez des sleeps. Un bug instable à 50 % est débogable ; à 1 %, non, donc continuez d'augmenter le taux jusqu'à ce qu'il le soit.

### Quand vous ne pouvez vraiment pas construire de boucle

Arrêtez-vous et dites-le explicitement. Listez ce que vous avez essayé. Demandez à l'utilisateur : (a) l'accès à l'environnement qui le reproduit, (b) un artefact capturé et caviardé (fichier HAR, dump de logs, core dump, enregistrement d'écran avec horodatages), ou (c) la permission d'ajouter une instrumentation temporaire en production. Ne **passez pas** à l'hypothèse sans boucle.

### Critère d'achèvement : une boucle serrée qui passe au rouge

La phase 1 est terminée quand la boucle est **serrée** et **capable de rouge** : vous pouvez nommer **une commande** (un chemin de script, une invocation de test, un curl) que vous avez **déjà lancée au moins une fois** (montrez l'invocation et sa sortie, caviardées), et qui est :

- [ ] **Capable de rouge** : elle exerce le vrai chemin de code du bug et vérifie le **symptôme exact de l'utilisateur**, donc elle peut passer au rouge sur ce bug et au vert une fois corrigé. Pas « tourne sans erreur » ; elle doit pouvoir _attraper ce bug précis_.
- [ ] **Déterministe** : même verdict à chaque exécution (bugs instables : un taux de reproduction élevé et épinglé, comme ci-dessus).
- [ ] **Rapide** : des secondes, pas des minutes.
- [ ] **Exécutable par l'agent** : vous pouvez la lancer sans surveillance ; un humain dans la boucle seulement via `scripts/hitl-loop.template.sh`.

Si vous vous surprenez à lire du code pour bâtir une théorie avant que cette commande existe, **arrêtez : sauter directement à une hypothèse est exactement l'échec que ce skill prévient.** Pas de commande capable de rouge, pas de phase 2.

## Phase 2 : Reproduire + minimiser

Lancez la boucle. Regardez-la passer au rouge quand le bug apparaît.

Confirmez :

- [ ] La boucle produit le mode de défaillance décrit par l'**utilisateur**, pas une autre défaillance qui se trouve être proche. Mauvais bug = mauvais correctif.
- [ ] La défaillance est reproductible sur plusieurs exécutions (ou, pour les bugs non déterministes, reproductible à un taux assez élevé pour déboguer).
- [ ] Vous avez capturé le symptôme exact (message d'erreur, sortie fausse, timing lent) pour que les phases suivantes puissent vérifier que le correctif y répond vraiment.

### Minimiser

Une fois qu'elle est rouge, réduisez la repro au **plus petit scénario qui passe encore au rouge**. Coupez les entrées, les appelants, la config, les données et les étapes **un à la fois**, en relançant la boucle après chaque coupe, et ne gardez que ce qui est porteur pour la défaillance.

Pourquoi s'embêter : une repro minimale réduit l'espace d'hypothèses en phase 3 (moins de pièces mobiles à soupçonner) et devient le test de régression propre en phase 5.

Terminé quand **chaque élément restant est porteur** : retirer n'importe lequel fait passer la boucle au vert.

Ne continuez pas avant d'avoir reproduit **et** minimisé.

## Phase 3 : Formuler des hypothèses

Générez **3 à 5 hypothèses classées** avant d'en tester une seule. Générer une seule hypothèse vous ancre sur la première idée plausible.

Chaque hypothèse doit être **falsifiable** : énoncez la prédiction qu'elle fait.

> Format : « Si <X> est la cause, alors <changer Y> fera disparaître le bug / <changer Z> l'aggravera. »

Si vous ne pouvez pas énoncer la prédiction, l'hypothèse est une intuition : écartez-la ou affinez-la.

**Montrez la liste classée à l'utilisateur avant de tester.** Il a souvent une connaissance du domaine qui reclasse instantanément (« on vient de déployer un changement sur le #3 »), ou connaît des hypothèses déjà écartées. Point de contrôle bon marché, gros gain de temps. Ne bloquez pas dessus ; continuez avec votre classement si l'utilisateur est AFK.

## Phase 4 : Instrumenter

Chaque sonde doit correspondre à une prédiction précise de la phase 3. **Changez une variable à la fois.**

Préférence d'outils :

1. **Inspection au debugger / REPL** si l'environnement le permet. Un point d'arrêt vaut dix logs.
2. **Logs ciblés** aux frontières qui distinguent les hypothèses.
3. Jamais « tout logger et greper ».

**Étiquetez chaque log de debug** avec un préfixe unique, par ex. `[DEBUG-a4f2]`. Le nettoyage final devient un seul grep. Les logs non étiquetés survivent ; les logs étiquetés meurent.

**Branche perf.** Pour les régressions de performance, les logs sont généralement le mauvais outil. À la place : établissez une mesure de référence (harnais de chronométrage, `performance.now()`, profileur, plan de requête), puis bisectez. Mesurez d'abord, corrigez ensuite.

## Phase 5 : Corriger + test de régression

Écrivez le test de régression **avant le correctif**, mais seulement s'il existe un **seam correct** pour lui.

Un seam correct est un seam où le test exerce le **vrai motif du bug** tel qu'il se produit au site d'appel. Si le seul seam disponible est trop superficiel (test à un seul appelant quand le bug en demande plusieurs, test unitaire incapable de reproduire la chaîne qui a déclenché le bug), un test de régression là-dedans donne une fausse confiance.

**S'il n'existe aucun seam correct, c'est en soi la conclusion.** Notez-le. L'architecture de la base de code empêche de verrouiller le bug. Signalez-le pour la phase suivante.

Si un seam correct existe :

1. Transformez la repro minimisée en test qui échoue à ce seam.
2. Regardez-le échouer. Si vous avez forcé le rouge en mutant du code ou une fixture, faites un `diff` contre une copie intacte pour prouver que la mutation a bien pris avant de lui faire confiance.
3. Appliquez le correctif.
4. Regardez-le passer.
5. Relancez la boucle de rétroaction de la phase 1 sur le scénario d'origine (non minimisé).

## Phase 6 : Nettoyage

Requis avant de déclarer terminé :

- [ ] La repro d'origine ne se reproduit plus (relancer la boucle de la phase 1)
- [ ] Le test de régression passe (ou l'absence de seam est documentée)
- [ ] Toute l'instrumentation `[DEBUG-...]` retirée (`grep` le préfixe)
- [ ] Prototypes jetables supprimés (ou déplacés vers un emplacement de debug clairement marqué)
- [ ] L'hypothèse qui s'est révélée correcte est énoncée dans le commit / le message de PR, pour que le prochain débogueur apprenne
