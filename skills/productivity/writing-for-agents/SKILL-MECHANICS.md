# Mécanique des skills

La branche propre aux skills de [`writing-for-agents`](SKILL.md) : ce qui change quand le document est un skill (le frontmatter, le choix d'invocation et les skills routeurs). Tout le reste de son écriture relève de la référence universelle de `SKILL.md`.

## Invocation

Deux choix, qui échangent les deux charges :

- Un skill **invoqué par le modèle** garde une `description`, pour que l'agent puisse le déclencher de façon autonome et que d'autres skills puissent l'atteindre. Vous pouvez toujours taper son nom : l'invocation par le modèle _inclut_ toujours l'accès par l'utilisateur ; une description ne fait qu'ajouter la découverte par l'agent, jamais retirer celle de l'humain. La description est le pointeur de contexte de premier niveau du skill, forcé de rester chargé en permanence : une charge de contexte permanente en échange de la découvrabilité. Un skill invoqué par le modèle dont le contenu est tout en référence est aussi un foyer pour la référence partagée : un autre skill peut l'invoquer, donc une référence nécessaire à plusieurs skills vit à un seul endroit. Mécanique : omettez `disable-model-invocation`, et écrivez une description tournée vers le modèle qui porte les branches de déclenchement (les règles d'écriture de pointeurs de `SKILL.md` s'appliquent intégralement).
- Un skill **invoqué par l'utilisateur** retire la description de la portée de l'agent : seul l'humain qui tape son nom peut l'invoquer, et aucun autre skill ne le peut. Charge de contexte nulle, mais il dépense de la charge cognitive : c'est vous l'index qui devez vous souvenir qu'il existe. Mécanique : réglez `disable-model-invocation: true` ; la `description` devient tournée vers l'humain : un résumé d'une ligne, les listes de déclencheurs retirées.

Choisissez l'invocation par le modèle seulement quand l'agent doit atteindre le skill de lui-même, ou quand un autre skill le doit. S'il ne se déclenche jamais qu'à la main, rendez-le invoqué par l'utilisateur et ne payez aucune charge de contexte.

Une référence partagée dont deux skills invoqués par l'utilisateur ont tous deux besoin ne peut vivre dans aucun des deux : sans descriptions, ni l'un ni l'autre ne peut déclencher l'autre. Poussez-la dans un fichier ordinaire hors du système de skills : une référence externe que n'importe quel skill peut viser.

## Scinder par invocation

La coupe par invocation (la coupe par séquence vit dans `SKILL.md`) : détachez un skill invoqué par le modèle quand vous avez un mot directeur distinct qui doit le déclencher de lui-même (un mot déclencheur que vous employez réellement dans vos prompts), ou quand un autre skill doit l'atteindre. Vous payez la charge de contexte de la nouvelle description toujours chargée, donc cet accès indépendant doit le valoir.

## Skills routeurs

Quand les skills invoqués par l'utilisateur se multiplient au-delà de ce que vous pouvez retenir, cette charge cognitive accumulée se soigne avec un **skill routeur** : un skill invoqué par l'utilisateur qui nomme les autres et quand recourir à chacun, pour que l'humain n'ait qu'un skill à retenir au lieu de beaucoup. Il ne peut que faire signe, jamais les déclencher : les skills invoqués par l'utilisateur n'ont pas de description, donc rien d'autre que l'humain ne peut les atteindre.
