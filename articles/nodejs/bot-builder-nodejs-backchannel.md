---
title: Échanger des informations à l’aide du contrôle web | Microsoft Docs
description: Découvrez comment échanger des informations entre le bot et une page web à l’aide du kit SDK Bot Framework pour Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 52f57cba5824deb01b176363880084760a95a41b
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299921"
---
# <a name="use-the-backchannel-mechanism"></a>Utiliser le mécanisme de backchannel

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

[!INCLUDE [Introduction to backchannel mechanism](../includes/snippet-backchannel.md)]

## <a name="walk-through"></a>Procédure

Le contrôle Discussion Web open source accède à l’API Direct Line en utilisant une classe JavaScript appelée <a href="https://github.com/microsoft/botframework-DirectLinejs" target="_blank">DirectLineJS</a>. Le contrôle peut créer sa propre instance de Direct Line ou en partager une avec la page d’hébergement. Si le contrôle partage une instance de Direct Line avec la page d’hébergement, le contrôle et la page seront tous deux en mesure d’envoyer et de recevoir des activités. Le diagramme ci-après illustre l’architecture générale d’un site web qui prend en charge la fonctionnalité de bot à l’aide du contrôle (Discussion) Web open source et de l’API Direct Line. 

![Backchannel](../media/designing-bots/patterns/back-channel.png)

### <a name="sample-code"></a>Exemple de code 

Dans cet exemple, le bot et la page web utilisent le mécanisme backchannel pour échanger des informations non visibles par l’utilisateur. Le bot demande que la page web modifie sa couleur d’arrière-plan, et la page web avertit le bot lorsque l’utilisateur clique sur un bouton de la page. 

> [!NOTE]
> Les extraits de code fournis dans cet article sont issus de <a href="https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/backchannel/index.html" target="_blank">l’exemple backchannel</a> et du <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">bot backchannel</a>. 

#### <a name="client-side-code"></a>Code côté client

Pour commencer, la page web crée un objet **DirectLine**.

```javascript
var botConnection = new BotChat.DirectLine(...);
```

Ensuite, elle partage l’objet **DirectLine** lors de la création de l’instance de Discussion Web.

```javascript
BotChat.App({
    botConnection: botConnection,
    user: user,
    bot: bot
}, document.getElementById("BotChatGoesHere"));
```

Lorsque l’utilisateur clique sur un bouton de la page web, cette dernière publie une activité de type « event » pour informer le bot que le bouton a été activé.

```javascript
const postButtonMessage = () => {
    botConnection
        .postActivity({type: "event", value: "", from: {id: "me" }, name: "buttonClicked"})
        .subscribe(id => console.log("success"));
    }
```

> [!TIP]
> Utilisez les attributs `name` et `value` pour communiquer toutes les informations dont le bot peut avoir besoin pour interpréter correctement l’événement et/ou répondre à ce dernier. 

Enfin, la page web écoute également un événement spécifique en provenance du bot.
Dans cet exemple, la page web écoute une activité dans laquelle type = "event" et name = "changeBackground". Lorsqu’elle reçoit ce type d’activité, la page web remplace sa couleur d’arrière-plan par la valeur `value` spécifiée par l’activité. 

```javascript
botConnection.activity$
    .filter(activity => activity.type === "event" && activity.name === "changeBackground")
    .subscribe(activity => changeBackgroundColor(activity.value))
```

#### <a name="server-side-code"></a>Code côté serveur

Le <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">bot backchannel</a> crée un événement à l’aide d’une fonction d’assistance.

```javascript
var bot = new builder.UniversalBot(connector, 
    function (session) {
        var reply = createEvent("changeBackground", session.message.text, session.message.address);
        session.endDialog(reply);
    }
);

const createEvent = (eventName, value, address) => {
    var msg = new builder.Message().address(address);
    msg.data.type = "event";
    msg.data.name = eventName;
    msg.data.value = value;
    return msg;
}
```

De même, le bot écoute également les événements qui proviennent du client. Dans cet exemple, si le bot reçoit un événement avec l’élément `name="buttonClicked"`, il envoie un message à l’utilisateur indiquant « I see that you clicked a button » (Je vois que vous avez cliqué sur un bouton).

```javascript
bot.on("event", function (event) {
    var msg = new builder.Message().address(event.address);
    msg.data.textLocale = "en-us";
    if (event.name === "buttonClicked") {
        msg.data.text = "I see that you clicked a button.";
    }
    bot.send(msg);
})
```

## <a name="additional-resources"></a>Ressources supplémentaires

- [API Direct Line][directLineAPI]
- <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Contrôle Discussion Web de Microsoft Bot Framework</a>
- <a href="https://aka.ms/v3-js-backchannel-sample" target="_blank">Exemple backchannel</a>
- <a href="https://github.com/ryanvolum/backChannelBot" target="_blank">Bot backchannel</a>

[directLineAPI]: https://docs.botframework.com/restapi/directline3/#navtitle
