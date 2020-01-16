---
title: Intercepter des messages (v3 JS) - Bot Service
description: Découvrez comment créer des journaux d’activité ou d’autres enregistrements en interceptant et traitant des échanges d’informations à l’aide du kit SDK Bot Framework pour Node.js.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/02/2018
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 32934a8cb857b9ff3f2b4f2058a58fcce12cfc3d
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790881"
---
# <a name="intercept-messages"></a>Intercepter des messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-middleware.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-intercept-messages.md)

[!INCLUDE [Introduction to message logging](../includes/snippet-message-logging-intro.md)]

## <a name="example"></a>Exemple

L’exemple de code suivant montre comment intercepter des messages échangés entre un utilisateur et un bot en utilisant le concept d’**intergiciel (middleware)** dans le kit SDK Bot Framework pour Node.js. 

Tout d’abord, configurez le gestionnaire pour les messages entrants (`botbuilder`) et le gestionnaire pour les messages sortants (`send`).

```javascript
server.post('/api/messages', connector.listen());
var bot = new builder.UniversalBot(connector);
bot.use({
    botbuilder: function (session, next) {
        myMiddleware.logIncomingMessage(session, next);
    },
    send: function (event, next) {
        myMiddleware.logOutgoingMessage(event, next);
    }
})
```

Ensuite, implémentez chacun des gestionnaires pour définir l’action à effectuer pour chaque message intercepté.

```javascript
module.exports = {
    logIncomingMessage: function (session, next) {
        console.log(session.message.text);
        next();
    },
    logOutgoingMessage: function (event, next) {
        console.log(event.text);
        next();
    }
}
```

Désormais, chaque message entrant (de l’utilisateur ou du robot) déclenche `logIncomingMessage`, et chaque message sortant (du robot ou de l’utilisateur) déclenche `logOutgoingMessage`.
Dans cet exemple, le robot imprime simplement des informations sur chaque message, mais vous pouvez mettre à jour `logIncomingMessage` et `logOutgoingMessage` si nécessaire pour définir les actions à effectuer pour chaque message. 

## <a name="sample-code"></a>Exemple de code

Pour obtenir un exemple complet qui montre comment intercepter et journaliser les messages à l’aide du kit SDK Bot Framework pour Node.js, consultez l’<a href="https://aka.ms/v3-js-capability-middlewareLogging" target="_blank">exemple Middleware and Logging</a> dans GitHub.
