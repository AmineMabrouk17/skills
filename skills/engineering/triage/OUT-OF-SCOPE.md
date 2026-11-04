# Base de connaissances hors périmètre

Le répertoire `.out-of-scope/` d'un repo stocke des enregistrements persistants des demandes de fonctionnalités rejetées. Il remplit deux objectifs :

1. **Mémoire institutionnelle** : pourquoi une fonctionnalité a été rejetée, afin que le raisonnement ne soit pas perdu lorsque l'issue est fermée
2. **Déduplication** : lorsqu'une nouvelle issue arrive et correspond à un rejet antérieur, le skill peut faire remonter la décision précédente au lieu de la remettre en cause

## Structure du répertoire

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

Un fichier par **concept**, pas par issue. Plusieurs issues demandant la même chose sont regroupées sous un même fichier.

## Format de fichier

Le fichier doit être rédigé dans un style détendu et lisible, plus proche d'un court document de conception que d'une entrée de base de données. Utilisez des paragraphes, des exemples de code et des exemples pour rendre le raisonnement clair et utile à quelqu'un qui le découvre pour la première fois.

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42: "Add dark mode support"
- #87: "Night theme for accessibility"
- #134: "Dark theme option"
```

### Nommer le fichier

Utilisez un nom court et descriptif en kebab-case pour le concept : `dark-mode.md`, `plugin-system.md`, `graphql-api.md`. Le nom doit être suffisamment reconnaissable pour que quelqu'un qui parcourt le répertoire comprenne ce qui a été rejeté sans ouvrir le fichier.

### Rédiger la raison

La raison doit être substantielle : pas « nous ne voulons pas de ça » mais pourquoi. Les bonnes raisons font référence à :

- Le périmètre ou la philosophie du projet (« Ce projet se concentre sur X ; le theming est une préoccupation en aval »)
- Des contraintes techniques (« Soutenir cela nécessiterait Y, ce qui entre en conflit avec notre architecture Z »)
- Des décisions stratégiques (« Nous avons choisi d'utiliser A au lieu de B parce que... »)

La raison doit être durable. Évitez de référencer des circonstances temporaires (« nous sommes trop occupés en ce moment ») ; ce ne sont pas de vrais rejets, ce sont des reports.

## Quand consulter `.out-of-scope/`

Pendant le triage (Étape 1 : Rassembler le contexte), lisez tous les fichiers de `.out-of-scope/`. Lors de l'évaluation d'une nouvelle issue :

- Vérifiez si la demande correspond à un concept hors périmètre existant
- La correspondance se fait par similarité de concept, pas par mot-clé : « night theme » correspond à `dark-mode.md`
- S'il y a une correspondance, signalez-la au mainteneur : « Ceci est similaire à `.out-of-scope/dark-mode.md`. Nous avons rejeté cela auparavant parce que [raison]. Pensez-vous toujours la même chose ? »

Le mainteneur peut :

- **Confirmer** : la nouvelle issue est ajoutée à la liste « Prior requests » du fichier existant, puis fermée
- **Reconsidérer** : le fichier hors périmètre est supprimé ou mis à jour, et l'issue poursuit le triage normal
- **Ne pas être d'accord** : les issues sont liées mais distinctes, poursuivez le triage normal

## Quand écrire dans `.out-of-scope/`

Uniquement lorsqu'une **amélioration** (pas un bug) est *rejetée* comme `wontfix`. Cela s'applique aux PR d'amélioration exactement comme aux issues : une PR rejetée est enregistrée ici pour que la même demande ne revienne pas comme du code neuf.

N'écrivez **pas** ici lorsqu'un élément est fermé comme `wontfix` parce qu'il est **déjà implémenté**. C'est une fonctionnalité construite, pas une fonctionnalité rejetée ; l'enregistrer empoisonnerait les vérifications de déduplication avec de faux rejets. À la place, le commentaire de fermeture indique où la fonctionnalité existe déjà.

Le flux :

1. Le mainteneur décide qu'une demande de fonctionnalité est hors périmètre
2. Vérifiez si un fichier `.out-of-scope/` correspondant existe déjà
3. Si oui : ajoutez la nouvelle issue à la liste « Prior requests »
4. Si non : créez un nouveau fichier avec le nom du concept, la décision, la raison et la première demande antérieure
5. Publiez un commentaire sur l'issue expliquant la décision et mentionnant le fichier `.out-of-scope/`
6. Fermez l'issue avec le label `wontfix`

## Mettre à jour ou supprimer les fichiers hors périmètre

Si le mainteneur change d'avis sur un concept précédemment rejeté :

- Supprimez le fichier `.out-of-scope/`
- Le skill n'a pas besoin de rouvrir les anciennes issues ; ce sont des enregistrements historiques
- La nouvelle issue qui a déclenché la reconsidération poursuit le triage normal
