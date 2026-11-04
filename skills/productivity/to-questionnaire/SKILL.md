---
name: to-questionnaire
description: Transformez une décision que vous ne pouvez pas pleinement trancher en un questionnaire que quelqu'un d'autre remplira.
disable-model-invocation: true
---

Transformez quelque chose que l'utilisateur ne peut pas trancher seul en un **questionnaire** : un document Markdown qu'il remet à une personne pour qu'elle le remplisse en asynchrone, ou qu'ils remplissent ensemble lors d'une réunion. Le destinataire détient un savoir qui manque à l'utilisateur ; le questionnaire le lui extirpe.

**Interrogez l'envoi, pas le sujet.** N'interrogez l'utilisateur que sur l'_envoi_, auquel il peut toujours répondre : à qui cela va, et ce qu'il lui faut en retour. Les questions du document ciblent alors l'**écart** entre ce que le destinataire sait et ce dont l'utilisateur a besoin.


1. **À qui cela va-t-il ?** Demandez, en un seul échange, le rôle du destinataire, son expertise et sa relation avec l'utilisateur. Cela fixe le ton du questionnaire et la quantité de contexte qu'il doit porter. Terminé quand vous savez qui est le destinataire et ce qu'il sait que l'utilisateur ignore.

2. **De quoi avez-vous besoin en retour ?** Demandez, en un seul échange, les décisions ou faits précis que l'utilisateur ne peut pas trancher seul et dont il a besoin de cette personne. Terminé quand vous disposez d'une liste concrète de ce que l'utilisateur doit être capable de faire ou de décider en sortant.

3. **Rédigez le questionnaire.** Rédigez des questions visant l'écart des étapes 1–2, en suivant la structure de document ci-dessous. Écrivez-le dans `to-questionnaire-<slug>.md` dans le répertoire courant (slug tiré du sujet) et indiquez le chemin. Terminé quand le fichier existe et que chaque élément nommé par l'utilisateur à l'étape 2 est couvert par une question.

## Structure du document

Présentez le document comme un **questionnaire de découverte** : l'utilisateur manque de contexte, le destinataire le détient. Ordonnez les questions de la plus importante à la moins importante, car l'asynchrone signifie que vous n'aurez peut-être qu'une seule passe, et regroupez-les sous des titres `##` par thème dès qu'il y en a plus qu'une poignée. Rédigez-le à l'aide du modèle ci-dessous.

<questionnaire-template>

# <Titre du questionnaire>

**Objet :** pourquoi ce questionnaire existe et la décision qui en dépend.

**De :** <l'utilisateur>, **À :** <le destinataire>, **Utilisation de vos réponses :** <où elles vont>

## Contexte

Un paragraphe pour orienter un destinataire qui n'était pas dans la tête de l'utilisateur. Assez pour bien répondre, pas une page.

## Comment répondre

Échéance et effort approximatif. Les réponses partielles et « je ne sais pas » sont utiles : signalez tout ce dont vous n'êtes pas sûr plutôt que de l'omettre.

## <Titre du thème>

Une section `##` par thème. Sous chacune, ses questions, de la plus importante à la moins importante. Chaque question est une seule idée, jamais composée, avec une amorce de réponse juste en dessous, et une ligne _pourquoi c'est important_ uniquement là où la question pourrait être mal lue ou inviter à une réponse bâclée.

<question-example>
### Quelle charge le système doit-il supporter au lancement ?

_Pourquoi c'est important : cela décide si nous provisionnons pour le trafic en rafale maintenant ou si nous le reportons._

>
</question-example>

## Autre chose ?

Une question finale fourre-tout : y a-t-il quelque chose que nous n'avons pas demandé et que nous devrions savoir ?

</questionnaire-template>
