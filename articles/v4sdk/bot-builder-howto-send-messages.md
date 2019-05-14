---
title: Envoyer et recevoir des SMS | Microsoft Docs
description: Découvrez comment envoyer et recevoir des SMS dans le kit SDK Bot Framework.
keywords: envoi de messages, activités de messagerie, SMS simple, message, SMS, recevoir des messages
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 04/16/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0d4279df31aba6cecb12b7d8d7262069aed8836b
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033388"
---
# <a name="send-and-receive-text-message"></a>Envoyer et recevoir des SMS

[!INCLUDE[applies-to](../includes/applies-to.md)]

Pour communiquer avec les utilisateurs ainsi que pour recevoir des communications, votre bot aura pour moyen principal les activités de **message**. Certains messages peuvent simplement se composer de texte brut, tandis que d’autres peuvent contenir un contenu plus riche, comme les cartes ou les pièces jointes. Le gestionnaire de tours de votre robot reçoit des messages de l’utilisateur, et vous pouvez envoyer des réponses à l’utilisateur à ce moment. L’objet de contexte de tour fournit des méthodes pour renvoyer des messages à l’utilisateur. Cet article décrit comment envoyer des SMS simples.

Markdown est pris en charge pour la plupart des champs de texte, mais sa prise en charge varie selon le canal.

## <a name="send-a-text-message"></a>Envoyer un message texte

Pour envoyer un message texte simple, spécifiez la chaîne que vous souhaitez envoyer en tant qu’activité :

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les gestionnaires d’activités du bot, utilisez la méthode `SendActivityAsync` de l’objet de contexte de tour pour envoyer une réponse de message unique. Vous pouvez également utiliser la méthode `SendActivitiesAsync` de l’objet pour envoyer plusieurs réponses à la fois.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dans les gestionnaires d’activités du bot, utilisez la méthode `sendActivity` de l’objet de contexte de tour pour envoyer une réponse de message unique. Vous pouvez également utiliser la méthode `sendActivities` de l’objet pour envoyer plusieurs réponses à la fois.

```javascript
await context.sendActivity("Welcome!");
```
---
## <a name="receive-a-text-message"></a>Recevoir un message texte

Pour recevoir un SMS simple, utilisez la propriété *texte* de l’objet *activité*. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les gestionnaires d’activités du bot, utilisez le code suivant pour recevoir un message. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dans les gestionnaires d’activités du bot, utilisez le code suivant pour recevoir un message.

```javascript
let text = turnContext.activity.text;
```

---

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour plus d’informations sur le traitement de l’activité en général, consultez l’article dédié au [traitement d’activité](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack).
- Pour plus d’informations sur la mise en forme, consultez la [section sur l’activité de message](https://aka.ms/botSpecs-activitySchema#message-activity) dans le schéma d’activité Bot Framework.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des médias aux messages](./bot-builder-howto-add-media-attachments.md)
