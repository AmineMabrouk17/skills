---
name: writing-shape
description: "Écriture, exploit : façonnez de la matière brute en un article, paragraphe par paragraphe."
disable-model-invocation: true
---

<what-to-do>

L'utilisateur a passé (ou passera) un fichier markdown de matière brute. Traitez-le comme la pile d'entrée : d'une liste bien rangée de fragments à un mur de prose non structurée en passant par une transcription. Le format n'a pas d'importance. Lisez-le d'un bout à l'autre avant toute autre chose.

Puis menez une session de façonnage qui produit un document d'article distinct. C'est de l'**exploit** : l'exploration est terminée, la pile est figée : engagez-vous sur une structure et puisez dans la pile pour la remplir. Ne modifiez pas le fichier de matière brute : il est en lecture seule pour ce skill.

Si l'utilisateur n'a pas indiqué où enregistrer l'article, demandez-le une fois et retenez le chemin.

</what-to-do>

<supporting-info>

## La boucle

1. **Lisez la pile.** Lisez le fichier d'entrée en entier. Faites-vous une idée de son contenu.
2. **Établissez les prérequis.** Convenez avec l'utilisateur de ce que le lecteur sait en entrant : les concepts **ancrés** dès le départ. Tout le reste doit être ancré par un bloc avant qu'un bloc ultérieur puisse s'appuyer dessus. Voir [Ancrage](#ancrage).
3. **Rédigez 2–3 ouvertures candidates.** Chaque ouverture doit impliquer une thèse ou un angle différent pour l'article. Montrez-les toutes. Forcez l'utilisateur à en choisir une ou à en composer une hybride. L'ouverture choisie définit ce que le reste de l'article doit faire.
4. **Développez paragraphe par paragraphe.** Une fois l'ouverture posée, demandez « vu cette ouverture, qu'est-ce que le lecteur a besoin d'entendre ensuite ? » Puisez de la matière dans la pile pour répondre. Le bloc suivant ne peut s'appuyer que sur des concepts ancrés, et il ancre les nouveaux en se posant. Débattez de la forme que prend le bloc suivant : un paragraphe, une liste, un tableau, un encadré, une citation, un bloc de code. Chaque choix de format doit être délibéré et défendable.
5. **Ajoutez au fichier de l'article au fur et à mesure.** Ne regroupez pas. Écrivez immédiatement chaque paragraphe ou bloc convenu pour que l'utilisateur voie l'article prendre forme.
6. **Bouclez sur l'étape 4 jusqu'à ce que l'article soit terminé.** C'est l'utilisateur qui décide quand il l'est.

## Ancrage

Chaque **concept** doit être **ancré** avant qu'un bloc puisse s'appuyer dessus : soit le lecteur le connaissait en entrant, soit il l'a rencontré dans un bloc précédent. Un bloc qui tend vers un concept non ancré perd le lecteur. L'unité est le concept, pas le mot qui le désigne : un bloc peut s'appuyer sur une idée qui manque au lecteur même sans aucun jargon visible. Quand un concept a un nom (un **terme**), l'ancrer signifie poser l'idée et le terme ensemble.

Un concept s'ancre de deux façons :

- **Prérequis** : ancré avant l'ouverture. Le lecteur l'apporte. Fixé au départ.
- **Introduit** : un bloc l'établit, et à partir de là il est ancré pour le reste de l'article.

Tenez une liste à jour de ce qui est ancré. Quand vous demandez « qu'est-ce que le lecteur a besoin d'entendre ensuite ? », un concept non ancré dont le mouvement suivant a besoin est lui-même la réponse : ancrez-le d'abord (ici ou dans un bloc antérieur) sinon le mouvement est impossible. C'est le nommage des manques de [Puiser dans la pile](#puiser-dans-la-pile) un cran plus haut : là, c'est la pile qui manque de matière ; ici, c'est l'article qui manque de fondation.

Le levier, c'est ce que vous érigez en prérequis par rapport à ce que vous ancrez dans l'article. Exigez trop en amont et vous excluez des lecteurs ; ancrez trop à l'intérieur et l'ouverture se noie dans les définitions. Réglez cela avec l'utilisateur quand vous établissez les prérequis.

## Ton de la conversation

C'est une session de grilling inversée. En idéation, la question était « qu'est-ce que vous remarquez vraiment ? » Ici, c'est « qu'est-ce que cet article soutient vraiment, et dans quel ordre le lecteur a-t-il besoin de l'entendre ? » Contestez. Refusez de laisser passer des transitions faibles. Si un paragraphe ne mérite pas sa place, coupez-le.

Des mouvements précis à continuer d'employer :

- « Qu'apporte ce paragraphe au lecteur que le précédent n'apportait pas ? »
- « Si je coupe ça, qu'est-ce qui casse ? »
- « Est-ce de la prose, ou est-ce que ça devrait être une liste ? Pourquoi de la prose ? »
- « Cette phrase fait deux choses : séparez-la ou choisissez-en une. »
- « L'ouverture promettait X. On a dérivé vers Y. Soit on renoue le fil, soit on change l'ouverture. »

## Puiser dans la pile

Traitez la matière brute comme une carrière, pas comme un script. Puisez un fragment, retravaillez-le pour l'adapter au paragraphe environnant, et placez-le. Un fragment peut être réparti sur plusieurs paragraphes, fusionné avec un autre ou paraphrasé. Le rôle de la pile est d'être exploitée ; le rôle de l'article est de se lire d'une seule voix.

Si la pile manque de quelque chose dont l'article a besoin, nommez le manque explicitement : « Il nous faut un exemple ici et la pile n'en a pas. Donnez-m'en un maintenant ou on coupe cette section. »

## Débats de format à avoir vraiment

Quand vous choisissez comment rendre un bloc, pesez ces compromis à voix haute avec l'utilisateur, pas en silence :

- **Prose vs liste.** La prose porte l'argumentation ; les listes portent des éléments parallèles. Si les éléments ne sont pas vraiment parallèles, la prose est meilleure. S'ils le sont, une liste se parcourt plus vite.
- **En ligne vs encadré.** Astuces, avertissements et digressions vont dans des encadrés (`> [!TIP]`, `> [!NOTE]`), mais seulement s'ils déraillaient vraiment l'argument principal en ligne. Sinon, laissez-les en ligne.
- **Tableau vs structure répétée.** Si la même forme se répète 3+ fois avec les mêmes champs, un tableau. Sinon, de la prose avec des amorces en gras.
- **Citation vs paraphrase.** Citez quand la formulation d'origine est le point. Paraphrasez quand seule l'idée compte.
- **Bloc de code vs code en ligne.** Multi-ligne, exécutable ou illustratif → bloc. Jeton ou identifiant unique → en ligne.

## Rythme d'écriture

Ajoutez au fichier de l'article à mesure que chaque bloc est convenu. Relisez le fichier depuis le disque avant chaque écriture : l'utilisateur a peut-être modifié les choses entre-temps. N'écrasez jamais à l'aveugle. Si l'utilisateur veut qu'un paragraphe soit réécrit, modifiez ce paragraphe précis sur place ; laissez le reste intact.

## Hors périmètre

- Puiser de nouveaux fragments qui ne sont pas dans la pile (traitez les manques comme dans « Puiser dans la pile »).
- Modifier le fichier de matière brute.
- Publier, formater pour une plateforme précise ou ajouter du frontmatter que l'utilisateur n'a pas demandé.

</supporting-info>
