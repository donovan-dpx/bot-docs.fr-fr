---
title: Ajouter des conseils de saisie aux messages (v3 C#) - Bot Service
description: Découvrez comment ajouter des conseils de saisie aux messages à l’aide du kit SDK Bot Framework pour .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 27d8ed5255eee04603ea1f9da3786930c05329e9
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75795246"
---
# <a name="add-input-hints-to-messages"></a>Ajouter des conseils de saisie aux messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-input-hints.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-send-input-hints.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-input-hints.md)

En spécifiant un conseil de saisie pour un message, vous pouvez indiquer si votre robot accepte, attend ou ignore l’entrée utilisateur une fois le message remis au client. Pour de nombreux canaux, cela permet aux clients de définir l’état des contrôles d’entrée utilisateur en conséquence. Par exemple, si le conseil de saisie d’un message indique que le bot ignore l’entrée utilisateur, le client peut fermer le microphone et désactiver la zone de saisie pour empêcher l’utilisateur de fournir une entrée.

## <a name="accepting-input"></a>Acceptation d’entrées

Pour indiquer que votre bot est passivement prêt pour l’entrée sans être en attente d’une réponse de l’utilisateur, définissez le conseil de saisie du message sur `InputHints.AcceptingInput`. Sur de nombreux canaux, cela entraîne l’activation de la zone de saisie du client, ainsi que la coupure du microphone, tout en restant accessible à l’utilisateur. Par exemple, Cortana ouvre le microphone afin d’accepter une entrée de l’utilisateur si l’utilisateur maintient le bouton du microphone enfoncé. L’exemple de code suivant crée un message qui indique que le bot accepte l’entrée utilisateur.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.AcceptingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="expecting-input"></a>Attente d’entrée

Pour indiquer que votre bot attend une réponse de l’utilisateur, définissez le conseil de saisie du message sur `InputHints.ExpectingInput`. Sur de nombreux canaux, cela entraîne l’activation de la zone de saisie du client, ainsi que l’ouverture du microphone. L’exemple de code suivant crée un message qui indique que le bot attend l’entrée utilisateur.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.ExpectingInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="ignoring-input"></a>Ignorer l’entrée

Pour indiquer que votre bot n’est pas prêt à recevoir une entrée de l’utilisateur, définissez le conseil de saisie du message sur `InputHints.IgnorningInput`. Sur de nombreux canaux, cela entraîne la désactivation de la zone de saisie du client ainsi que la coupure du microphone. L’exemple de code suivant crée un message qui indique que le bot ignore l’entrée utilisateur.

```cs
Activity reply = activity.CreateReply("This is the text that will be displayed.");
reply.Speak = "This is the text that will be spoken.";
reply.InputHint = InputHints.IgnoringInput;
await connector.Conversations.ReplyToActivityAsync(reply);
```

## <a name="default-values-for-input-hint"></a>Valeurs par défaut pour le conseil de saisie

Si vous ne définissez pas de conseil de saisie pour un message, le kit SDK Bot Framework le fait automatiquement en suivant cette logique :

- Si votre bot envoie une invite, le conseil de saisie pour le message spécifie que votre bot **attend une entrée**.</li>
- Si votre bot envoie un message simple, le conseil de saisie pour le message spécifie que votre bot **accepte une entrée**.</li>
- Si votre bot envoie une série de messages consécutifs, le conseil de saisie pour tous les messages sauf le dernier de la série spécifiera que votre bot **ignorant les entrées**, et le conseil de saisie pour le message final de la série spécifie que votre bot **accepte une entrée**.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Créer des messages](bot-builder-dotnet-create-messages.md)
- [Ajouter la reconnaissance vocale aux messages](bot-builder-dotnet-text-to-speech.md)
- <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activité</a>
- <a href="/dotnet/api/microsoft.bot.connector.inputhints" target="_blank">Classe InputHints</a>
