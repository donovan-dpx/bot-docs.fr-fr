---
title: Ajouter des actions suggérées aux messages (v3 JS) - Bot Service
description: Découvrez comment envoyer des actions suggérées dans des messages à l’aide du kit SDK Bot Framework pour Note.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2019
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: ebad14abfdef2e274562b17ca1945d709a2c8d54
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790384"
---
# <a name="add-suggested-actions-to-messages"></a>Ajouter des actions suggérées aux messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## <a name="suggested-actions-example"></a>Exemples d’actions suggérées

Pour ajouter des actions suggérées à un message, définissez la propriété `suggestedActions` du message sur une liste [d’actions de carte][ICardAction] qui représentent les boutons à présenter à l’utilisateur.

Cet exemple de code indique comment envoyer un message qui présente trois actions suggérées à l’utilisateur :

[!code-javascript[Send suggested actions](../includes/code/node-send-suggested-actions.js#sendSuggestedActions)]

Lorsque l’utilisateur appuie sur l’une des actions suggérées, le robot reçoit un message de l’utilisateur contenant la valeur `value` de l’action correspondante.

N’oubliez pas que la méthode `imBack` publiera la valeur `value` dans la fenêtre de discussion du canal que vous utilisez. Si ce comportement n’est pas souhaité, vous pouvez utiliser la méthode `postBack`, qui renvoie toujours la sélection à votre bot, mais sans l’afficher dans la fenêtre de discussion. Toutefois, certains canaux ne prennent pas en charge la méthode `postBack` ; dans ce cas, cette dernière se comportera comme la méthode `imBack`.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Exemples][samples]
- [IMessage][IMessage]
- [ICardAction][ICardAction]
- [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage

[SessionSend]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send

[ICardAction]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.icardaction.html

<!-- The inspector is no longer supported: we're redirecting to the samples for now. -->
[samples]: https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples
