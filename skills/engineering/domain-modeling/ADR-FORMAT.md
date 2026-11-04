# Format des ADR

Les ADR vivent dans `docs/adr/` et utilisent une numérotation séquentielle : `0001-slug.md`, `0002-slug.md`, etc.

Créez le répertoire `docs/adr/` paresseusement : seulement quand le premier ADR est nécessaire.

## Modèle

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

Voilà tout. Un ADR peut tenir en un seul paragraphe. La valeur réside dans le fait d'enregistrer *qu'*une décision a été prise et *pourquoi*, pas dans le remplissage de sections.

## Sections facultatives

Ne les incluez que lorsqu'elles apportent une véritable valeur. La plupart des ADR n'en auront pas besoin.

- Frontmatter **Statut** (`proposed | accepted | deprecated | superseded by ADR-NNNN`) : utile quand les décisions sont réexaminées
- **Options envisagées** : seulement quand les alternatives rejetées méritent d'être mémorisées
- **Conséquences** : seulement quand des effets en aval non évidents doivent être signalés

## Numérotation

Parcourez `docs/adr/` pour trouver le plus grand numéro existant et incrémentez-le de un.

## Quand proposer un ADR

Les trois conditions suivantes doivent être réunies :

1. **Difficile à inverser** : le coût de changer d'avis plus tard est significatif
2. **Surprenant sans contexte** : un lecteur futur regardera le code et se demandera « pourquoi diable ont-ils fait ça comme ça ? »
3. **Le résultat d'un vrai compromis** : il existait de véritables alternatives et vous en avez choisi une pour des raisons précises

Si une décision est facile à inverser, sautez-la : vous l'inverserez, c'est tout. Si elle n'est pas surprenante, personne ne se demandera pourquoi. S'il n'y avait pas de véritable alternative, il n'y a rien à enregistrer au-delà de « nous avons fait ce qui était évident ».

### Ce qui est éligible

- **Forme architecturale.** « Nous utilisons un monorepo. » « Le modèle d'écriture est event-sourced, le modèle de lecture est projeté dans Postgres. »
- **Modèles d'intégration entre contextes.** « Ordering et Billing communiquent via des événements de domaine, pas par HTTP synchrone. »
- **Choix technologiques qui créent un enfermement.** Base de données, bus de messages, fournisseur d'authentification, cible de déploiement. Pas toutes les bibliothèques : seulement celles qu'il faudrait un trimestre pour remplacer.
- **Décisions de frontière et de périmètre.** « Les données client sont possédées par le contexte Customer ; les autres contextes n'y font référence que par ID. » Les non explicites valent autant que les oui.
- **Écarts délibérés par rapport au chemin évident.** « Nous utilisons du SQL manuel au lieu d'un ORM parce que X. » Tout cas où un lecteur raisonnable supposerait l'inverse. Ces entrées empêchent le prochain ingénieur de « corriger » quelque chose qui était délibéré.
- **Contraintes invisibles dans le code.** « Nous ne pouvons pas utiliser AWS à cause d'exigences de conformité. » « Les temps de réponse doivent être inférieurs à 200 ms à cause du contrat d'API du partenaire. »
- **Alternatives rejetées quand le rejet n'est pas évident.** Si vous avez envisagé GraphQL et choisi REST pour des raisons subtiles, enregistrez-le ; sinon quelqu'un proposera de nouveau GraphQL dans six mois.
