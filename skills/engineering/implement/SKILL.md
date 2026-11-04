---
name: implement
description: "Implémentez un travail à partir d'une spec ou d'un ensemble de tickets."
disable-model-invocation: true
---

Implémentez le travail décrit par l'utilisateur dans la spec ou les tickets.

Si l'utilisateur fournit une référence de ticket, récupérez-la depuis l'issue tracker et énoncez son titre avant de commencer. Si la référence est ambiguë, demandez.

Appelez l'outil Skill avec « tdd » lorsque c'est possible, aux seams convenus à l'avance.

Lancez la vérification de types régulièrement, les fichiers de test isolés régulièrement, et la suite de tests complète une fois à la fin.

Une fois terminé, appelez l'outil Skill avec « code-review » pour faire la revue de code du travail.

Faites un commit de votre travail sur la branch actuelle.
