---
title: Envoyer un indicateur de saisie | Microsoft Docs
description: Découvrez comment ajouter un indicateur « Veuillez patienter » à l’aide du kit SDK Bot Framework pour Node.js de façon à indiquer à un utilisateur qu’un bot traite une demande.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: b7bef8e17584d94d6821e8b936abae9ef20088e2
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299702"
---
# <a name="send-a-typing-indicator"></a>Envoyer un indicateur de saisie 

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Les utilisateurs attendent une réponse rapide à leurs messages. Si votre bot effectue une tâche longue comme l’appel d’un serveur ou l’exécution d’une requête sans fournir à l’utilisateur une indication que le bot a bien reçu sa demande, cet utilisateur peut perdre patience et envoyer des messages supplémentaires ou supposer tout simplement que le bot ne fonctionne pas.
De nombreux canaux prennent en charge l’envoi d’une indication de saisie pour prévenir l’utilisateur que le message a bien été reçu et est en cours de traitement.


## <a name="typing-indicator-example"></a>Exemple d’indicateur de saisie

L’exemple suivant montre comment envoyer une indication de saisie à l’aide [session.sendTyping()][SendTyping].  Vous pouvez le tester avec l’émulateur Bot Framework.


```javascript

// Create bot and default message handler
var bot = new builder.UniversalBot(connector, function (session) {
    session.sendTyping();
    setTimeout(function () {
        session.send("Hello there...");
    }, 3000);
});
```

Les indicateurs de saisie sont également utiles pour ajouter un délai de message afin d’éviter que les messages contenant des images soient envoyés de façon désordonnée.

Pour plus d’informations, consultez [Guide pratique pour envoyer une carte riche](bot-builder-nodejs-send-rich-cards.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [sendTyping][SendTyping]


[SendTyping]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session#sendtyping
[IMessage]: http://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
