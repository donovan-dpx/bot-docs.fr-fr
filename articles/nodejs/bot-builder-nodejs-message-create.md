---
title: Créer des messages - Bot Service
description: Découvrez comment créer des messages avec le kit SDK Bot Framework pour Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/7/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 1837b302b8d0b1d05e16d4d979b5807fa3e2d268
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790844"
---
# <a name="create-messages"></a>Créer des messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

La communication entre le bot et l’utilisateur s’effectue via des messages. Votre bot enverra des activités de message pour communiquer des informations aux utilisateurs et, en retour, recevoir des activités de message de la part des utilisateurs. Certains messages peuvent consister simplement en un texte brut, tandis que d’autres peuvent contenir un contenu plus riche, par exemple un texte à énoncer, des actions suggérées, des pièces jointes multimédia, des cartes riches et des données spécifiques au canal.

Cet article décrit certaines des méthodes de messages courantes que vous pouvez utiliser pour améliorer votre expérience utilisateur.

## <a name="default-message-handler"></a>Gestionnaire de messages par défaut

Le kit SDK Bot Framework pour Node.js est fourni avec un gestionnaire de messages par défaut intégré à l’objet [`session`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html). Ce gestionnaire de messages vous permet d’envoyer et de recevoir des messages texte entre le bot et l’utilisateur.

### <a name="send-a-text-message"></a>Envoyer un message texte

L’envoi d’un message texte à l’aide du gestionnaire de messages par défaut est simple : il suffit d’appeler `session.send` et de passer une **chaîne**.

Cet exemple montre comment vous pouvez envoyer un message texte pour accueillir l’utilisateur.
```javascript
session.send("Good morning.");
```

Cet exemple montre comment vous pouvez envoyer un message texte à l’aide du modèle de chaîne JavaScript.
```javascript
var msg = `You ordered: ${order.Description} for a total of $${order.Price}.`;
session.send(msg); //msg: "You ordered: Potato Salad for a total of $5.99."
```

### <a name="receive-a-text-message"></a>Recevoir un message texte

Lorsqu’un utilisateur envoie un message au bot, le bot reçoit le message via la propriété `session.message`.

Cet exemple montre comment accéder aux messages de l’utilisateur.
```javascript
var userMessage = session.message.text;
```

## <a name="customizing-a-message"></a>Personnalisation d’un message

Pour avoir plus de contrôle sur la mise en forme du texte de vos messages, vous pouvez créer un objet [`message`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html) personnalisé et définir les propriétés nécessaires avant de l’envoyer à l’utilisateur.

Cet exemple montre comment créer un objet `message` personnalisé et définir les propriétés `text`, `textFormat` et `textLocale`.

```javascript
var customMessage = new builder.Message(session)
    .text("Hello!")
    .textFormat("plain")
    .textLocale("en-us");
session.send(customMessage);
```

Si l’objet `session` n’est pas dans la portée, vous pouvez utiliser la méthode `bot.send` pour envoyer à l’utilisateur un message mis en forme.

La propriété `textFormat` d’un message peut être utilisée pour spécifier le format du texte. La propriété `textFormat` peut être définie sur **brut**, **markdown** ou **xml**. La valeur par défaut de `textFormat` est **markdown**. 

## <a name="message-property"></a>Propriété du message

L’objet [`Message`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html) possède une propriété **data** interne qu’il utilise pour gérer le message envoyé. Les autres propriétés sont définies via les différentes méthodes que cet objet vous présente. 

## <a name="message-methods"></a>Méthodes de message

Les propriétés Message sont définies et extraites via les méthodes de l’objet. Le tableau ci-dessous fournit une liste des méthodes que vous pouvez appeler pour les différentes pour définir/obtenir les propriétés **Message**.

| Méthode | Description |
| ---- | ---- | 
| [`addAttachment(attachment:AttachmentType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addattachment) | Ajoute une pièce jointe à un message.|
| [`addEntity(obj:Object)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#addentity) | Ajoute une entité au message. |
| [`address(adr:IAddress)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#address) | Informations de routage d’adresses pour le message. Pour envoyer à l’utilisateur un message proactive, enregistrez l’adresse du message dans le conteneur userData. |
| [`attachmentLayout(style:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachmentlayout) | Astuce montrant comment les clients doivent disposer plusieurs pièces jointes. La valeur par défaut est « list ». |
| [`attachments(list:AttachmentType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#attachments) | Une liste de cartes ou d’images à envoyer à l’utilisateur. |
| [`compose(prompts:string[], ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#compose) | Compose une réponse complexe et aléatoire destinée à l’utilisateur. |
| [`entities(list:Object[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#entities) | Objets structurés transmis au bot ou à l’utilisateur. |
| [`inputHint(hint:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#inputhint) | Astuce envoyée à l’utilisateur pour l’informer si le bot attend des données complémentaires ou non. Les invites intégrées fourniront automatiquement cette valeur pour les messages sortants. |
| [`localTimeStamp((optional)time:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#localtimestamp) | Heure locale à laquelle le message a été envoyé (définie par le client ou le bot, par exemple : 2016-09-23T13:07:49.4714686-07:00.) |
| [`originalEvent(event:any)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#originalevent) | Message au format d’origine/natif du canal pour les messages entrants. |
| [`sourceEvent(map:ISourceEventMap)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#sourceevent) | Peut être utilisé avec les messages sortants pour transmettre des données source spécifiques à un événement, par exemple des pièces jointes personnalisées. |
| [`speak(ssml:TextType, ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#speak) | Définit le champ de lecture du message en tant que *Speech Synthesis Markup Language (SSML)* . Cette valeur sera lue à l’utilisateur sur les appareils pris en charge. |
| [`suggestedActions(suggestions:ISuggestedActions `&#124;` IIsSuggestedActions)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#suggestedactions) | Autres actions suggérées à envoyer à l’utilisateur. Les actions suggérées s’afficheront uniquement sur les canaux qui les prennent en charge. |
| [`summary(text:TextType, ...argus:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#summary) | Le texte à afficher comme texte de secours et comme brève description du contenu du message (par exemple : liste des dernières conversations.) |
| [`text(text:TextType, ...args:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#text) | Définit le texte du message. |
| [`textFormat(style:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textformat) | Définissez le format du texte. Le format par défaut est **markdown**. |
| [`textLocale(locale:string)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#textlocale) | Définissez la langue cible du message. |
| [`toMessage()`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#tomessage) | Obtient l’objet JSON du message. |
| [`composePrompt(session:Session, prompts:string[], args?:any[])`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#composeprompt-1) | Combine un tableau d’invites dans une seule invite localisée et remplit éventuellement les modèles d’emplacements d’invites avec les arguments transmis. |
| [`randomPrompt(prompts:TextType)`](https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.message.html#randomprompt) | Obtient une invite aléatoire à partir du tableau d’*invites** transmis. |

## <a name="next-step"></a>Étape suivante

> [!div class="nextstepaction"]
> [Envoyer et recevoir des pièces jointes](bot-builder-nodejs-send-receive-attachments.md)

