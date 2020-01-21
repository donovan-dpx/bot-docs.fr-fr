---
title: Création d’une compétence Cortana à l’aide de Node.js - Bot Service
description: Découvrez les concepts de base liés à la création d’une compétence Cortana dans le kit SDK Bot Framework pour Node.js.
keywords: Bot Framework, compétence Cortana, voix, Node.js, Bot Builder, Kit de développement logiciel (SDK), concepts clés, concepts fodamentaux
author: DeniseMak
manager: kamrani
ms.author: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/10/2019
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 477b20b090ae20ee9c4d723710a9fa11fff356fd
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790334"
---
# <a name="key-concepts-for-building-a-bot-for-cortana-skills-using-nodejs"></a>Concepts clés pour la création d’un robot pour Compétence Cortana à l’aide de Node.js
 
[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!NOTE]
> Cet article est un contenu préliminaire qui sera mis à jour.

Cet article présente les concepts clés de création d’une compétence Cortana dans le kit SDK Bot Framework pour Node.js. 

## <a name="what-is-a-cortana-skill"></a>Qu’est qu’une compétence Cortana ?
Une compétence Cortana est un robot que vous pouvez appeler à l’aide d’un client Cortana, tel que celui intégré à Windows 10. L’utilisateur lance le robot en disant des mots clés ou des expressions associées au robot. Vous utilisez le portail Bot Framework pour configurer les mots clés utilisés pour lancer votre robot. 

On peut se représenter Cortana comme un canal de reconnaissance vocale capable d’envoyer et recevoir des messages vocaux en plus d’une conversation textuelle. Un robot publié en tant que compétence Cortana doit être conçu tant pour la voix que pour le texte. Bot Framework fournit des méthodes permettant de spécifier Speech Synthesis Markup Language (SSML) pour définir des messages parlés que votre robot envoie.

## <a name="acknowledge-user-utterances"></a>Accuser réception d’énoncés d’utilisateur 

<!-- Establishing conversational understanding -->
<!-- Placeholder: In this section, describe how you have to write your speech to sound natural -->


Lorsque vous créez un robot de reconnaissance vocale, vous devez tenter d’établir un fondement commun et une compréhension mutuelle dans la conversation. Le robot doit « fonder » les énoncés de l’utilisateur en indiquant que celui-ci a été entendu et compris.

Les utilisateurs sont plongés dans la confusion quand un système ne parvient pas fonder leurs énoncés. Par exemple, la conversation suivante peut être une source de confusion quand le robot demande « Et ensuite ? » :

> **Cortana** : Voulez-vous voir d’autres éléments de votre profil ?  
> **Utilisateur** : Non.  
> **Cortana** : Quelle est l’étape suivante ?

Si le robot ajoute « OK » en tant qu’accusé de réception, c’est plus convivial pour l’utilisateur :

> **Cortana** : Voulez-vous voir d’autres éléments de votre profil ?  
> **Utilisateur** : Non.  
> **Cortana** : **OK**, et maintenant ?

Degrés de fondement, du plus faible au plus fort :

1. Attention continue
2. Prochaine contribution pertinente
3. Accusé de réception : réponse minimale : « Oui », « OK », « d’accord », « super »
4. Démonstration : marquer la compréhension par une reformulation, une finalisation.
5. Affichage : répéter en tout ou partie.

### <a name="acknowledgement-and-next-relevant-contribution"></a>Accusé de réception et contribution pertinents suivante

> **Utilisateur** : Je dois voyager en mai.  
> **Cortana** : **OK**. Quel jour du mois de mai voulez-vous voyager ?  
> **Utilisateur** : Je dois être là-bas du 12 au 15.  
> **Cortana** : **OK**. Vous prenez l’avion pour quelle ville ?  

### <a name="grounding-by-demonstration"></a>Fondement par démonstration

> **Utilisateur** : Je dois voyager en mai.  
> **Cortana** : Et, **quel jour** de mai vouliez-vous voyager ?  
> **Utilisateur** : Je dois être là-bas du 12 au 15.  
> **Cortana** : **Et**, quelle est votre destination ?  
    
### <a name="closure"></a>Fermeture

Le robot effectuant une action doit présenter une preuve de son accomplissement. Il est également important d’indiquer l’échec ou la compréhension. 

* Fermeture non vocale : si vous appuyez sur un bouton d’ascenseur, son témoin s’allume.  
Le processus comprend deux étapes :
    * Présentation (lorsque vous appuyez sur le bouton)
    * Acceptation (lorsque le bouton s’allume)

## <a name="differences-in-content-presentation"></a>Différences de présentation du contenu
N’oubliez pas que Cortana est pris en charge sur divers types d’appareils, mais que tous ne sont pas équipés d’un écran. Lorsque vous concevez un bot de reconnaissance vocale, vous devez prendre en compte le fait que les dialogues réels ne correspondront pas forcément au texte affiché par votre bot.
<!-- If there are differences in what the bot will say, in the text vs the speak fields of a prompt or in a waterfall, for example, discuss them here.

## Speech

You bot uses the **session.say** method to speak to the user. The speak method has three overloads:
* If you pass only one parameter to **session.say**, it can be a text parameter.
* If you pass two parameters to **session.say**, it can take text and SSML.
* If you pass three parameters, the third parameter takes an options structure that specifies all the options you can pass to build an **IMessage** object.

```javascript
var bot = new builder.UniversalBot(connector, function (session) {
    session.say("Hello... I'm a decision making bot.'.", 
        ssml.speak("Hello. I can help you answer all of life's tough questions."));
    session.replaceDialog('rootMenu');
});

```
## Speech in messages

The **IMessage** object provides a **speak** property for SSML. It can be used to play a .wav file.

The **inputHint** property helps indicate to Cortana whether your bot is expecting input. If you're using a built-in prompt, this value is automatically set to the default of **expectingInput**.

The **inputHint** property can take the following values: 
* **expectingInput**: Indicates that the bot is actively expecting a response from the user. Cortana listens for the user to speak into the microphone.
* **acceptingInput**: Indicates that the bot is passively ready for input but is not waiting on a response. Cortana accepts input from the user if the user holds down the microphone button.
* **ignoringInput**: Cortana is ignoring input. Your bot may send this hint if it is actively processing a request and will ignore input from users until the request is complete.

Prompts must use the `speak:` option.

```javascript
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```

Prompts.number has *ordinal support*, meaning that you can say "the last", "the first", "the next-to-last" to choose an item in a list.

## Using synonyms

<!-- Axl Rose example -->
```javascript   
         var choices = [
            { 
                value: 'flipCoinDialog',
                action: { title: "Flip A Coin" },
                synonyms: 'toss coin|flip quarter|toss quarter'
            },
            {
                value: 'rollDiceDialog',
                action: { title: "Roll Dice" },
                synonyms: 'roll die|shoot dice|shoot die'
            },
            {
                value: 'magicBallDialog',
                action: { title: "Magic 8-Ball" },
                synonyms: 'shake ball'
            },
            {
                value: 'quit',
                action: { title: "Quit" },
                synonyms: 'exit|stop|end'
            }
        ];
        builder.Prompts.choice(session, "Decision Options", choices, {
            listStyle: builder.ListStyle.button,
            speak: ssml.speak("How would you like me to decide?")
        });
```

## <a name="configuring-your-bot"></a>Configuration de votre robot

## <a name="prompts"></a>Invites

## <a name="additional-resources"></a>Ressources supplémentaires

Documentation Cortana : [Documentation sur les compétences Cortana](/cortana/skills/)

Informations de référence sur SSML Cortana : [Informations de référence sur SSML (Speech Synthesis Markup Language)](/cortana/skills/speech-synthesis-markup-language)
