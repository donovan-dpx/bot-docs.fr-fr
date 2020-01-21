---
title: Ajouter des actions suggérées aux messages (v3 C#) - Bot Service
description: Découvrez comment ajouter des actions suggérées aux messages à l’aide du kit SDK Bot Framework pour .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/13/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 61aaff340a235f7b9b552d79205fab8e772216f9
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75796678"
---
# <a name="add-suggested-actions-to-messages"></a>Ajouter des actions suggérées aux messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## <a name="send-suggested-actions"></a>Envoyer des actions suggérées

Pour ajouter des actions suggérées à un message, définissez la propriété `SuggestedActions` de l’activité sur une liste d’objets [CardAction][cardAction] qui représentent les boutons à présenter à l’utilisateur. 

Cet exemple de code indique comment créer un message qui présente trois actions suggérées à l’utilisateur :

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

Lorsque l’utilisateur appuie sur l’une des actions suggérées, le robot reçoit un message de l’utilisateur contenant la valeur `Value` de l’action correspondante.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble des activités](bot-builder-dotnet-activities.md)
- [Créer des messages](bot-builder-dotnet-create-messages.md)
- [Classe Activité](https://aka.ms/ActivityClass-dotnet-API)
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">Interface IMessageActivity</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">CardAction class</a> (Classe CardAction)
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">SuggestedActions class</a> (Classe SuggestedActions)

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md


