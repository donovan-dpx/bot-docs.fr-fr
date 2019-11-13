---
title: Envoyer des notifications proactives aux utilisateurs | Microsoft Docs
description: Comprendre comment envoyer des messages de notification
keywords: message proactif, message de notification, notification de bot,
author: jonathanfingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 774738186127bff1e680d905d208b69097402d8e
ms.sourcegitcommit: 312a4593177840433dfee405335100ce59aac347
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933582"
---
# <a name="send-proactive-notifications-to-users"></a>Envoyer des notifications proactives aux utilisateurs

[!INCLUDE[applies-to](../includes/applies-to.md)]

En règle générale, chaque message envoyé à l’utilisateur par un robot se rapporte directement à la saisie précédente de l’utilisateur.
Dans certains cas, il se peut qu’un bot ait besoin d’envoyer à l’utilisateur un message qui n’est pas directement associé au sujet de conversation actuel ou au dernier message envoyé par l’utilisateur. Il s’agit de _messages proactifs_.

Les messages proactifs peuvent être utiles dans différents cas de figure. Par exemple, si l’utilisateur a précédemment demandé au bot de surveiller le prix d’un produit, le bot peut alerter l’utilisateur dès que le prix du produit diminue de 20 %. En outre, si un bot a besoin de temps pour compiler une réponse à la question de l’utilisateur, il peut informer l’utilisateur de ce délai et autoriser la conversation à se poursuivre dans l’intervalle. Puis, dès que le bot a terminé de compiler la réponse à la question, il partage cette information avec l’utilisateur.

Lors de l’implémentation de messages proactifs dans votre bot, n’envoyez pas plusieurs messages proactifs dans un même court laps de temps. Certains canaux appliquent des restrictions concernant la fréquence à laquelle un bot peut envoyer des messages à l’utilisateur, et désactivent le bot si ce dernier enfreint ces restrictions.

Un message proactif ad hoc constitue le type de message proactif le plus simple. Le bot injecte simplement le message dans la conversation chaque fois qu’il est déclenché, que l’utilisateur soit ou non déjà engagé dans une autre sujet de conversation avec le bot, et ne tentera pas de modifier la conversation d’une quelconque manière.

Pour gérer plus facilement les notifications, pensez à d’autres méthodes pour intégrer la notification dans le flux de messages. Par exemple, vous pouvez définir un indicateur dans l’état de la conversation ou ajouter la notification à une file d’attente.

## <a name="prerequisites"></a>Prérequis

- Comprendre les [concepts de base des bots](bot-builder-basics.md).
- Une copie de l’exemple de messages proactifs en **[C#](https://aka.ms/proactive-sample-cs) ou [JavaScript](https://aka.ms/proactive-sample-js)** . Cet exemple est utilisé pour expliquer la messagerie proactive dans cet article.

## <a name="about-the-proactive-sample"></a>À propos de l’exemple proactif

L’exemple comporte un bot, et un contrôleur supplémentaire qui est utilisé pour envoyer des messages proactifs au bot, comme indiqué dans l’illustration suivante.

![bot proactif](media/proactive-sample-bot.png)

## <a name="retrieve-and-store-conversation-reference"></a>Récupérer et stocker la référence de conversation

Lorsque l’émulateur se connecte au bot, celui-ci reçoit deux activités de mise à jour de conversation. Dans le gestionnaire d’activité de mise à jour de conversation du bot, la référence de conversation est récupérée et stockée dans un dictionnaire, comme indiqué ci-dessous.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\ProactiveBot.cs**

[!code-csharp[OnConversationUpdateActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/16.proactive-messages/Bots/ProactiveBot.cs?range=26-37&highlight=3-4,9)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/proactiveBot.js**

[!code-javascript[onConversationUpdateActivity](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/bots/proactiveBot.js?range=13-17&highlight=2)]

[!code-javascript[onConversationUpdateActivity](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/bots/proactiveBot.js?range=41-44&highlight=2-3)]

---

Remarque : Dans un scénario réel, vous conserveriez les références de conversation dans une base de données au lieu d’utiliser un objet en mémoire.

La référence de conversation est dotée d’une propriété _conversation_ qui décrit la conversation dans laquelle se trouve l’activité. La conversation est dotée d’une propriété _user_ qui répertorie les utilisateurs participant à la conversation, et une propriété _service URL_ que les canaux utilisent pour désigner l’URL où les réponses à l’activité en cours peuvent être envoyées. Une référence de conversation valide est nécessaire pour envoyer des messages proactifs aux utilisateurs.

## <a name="send-proactive-message"></a>Envoyer un message proactif

Le deuxième contrôleur, le contrôleur _notify_, est chargé d’envoyer le message proactif au bot. Utilisez les étapes suivantes pour générer un message proactif.

1. Récupérez la référence de la conversation à laquelle envoyer le message proactif.
1. Appelez la méthode de l’adaptateur _continue conversation_, en fournissant la référence de conversation et le délégué de gestionnaire de tours à utiliser. La méthode de poursuite de conversation génère un contexte de tour pour la conversation référencée, puis appelle le délégué de gestionnaire de tours.
1. Dans le délégué, utilisez le contexte de tour pour envoyer le message proactif.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Controllers\NotifyController.cs**

Chaque fois que la page de notification du bot est demandée, le contrôleur de notification récupère les références de conversation à partir du dictionnaire.
Le contrôleur utilise ensuite les méthodes `ContinueConversationAsync` et `BotCallback` pour envoyer le message proactif.

[!code-csharp[Notify logic](~/../botbuilder-samples/samples/csharp_dotnetcore/16.proactive-messages/Controllers/NotifyController.cs?range=17-60&highlight=28,40-43)]

Pour envoyer un message proactif, l’adaptateur a besoin d’un ID d’application pour le bot. Dans un environnement de production, vous pouvez utiliser l’ID d’application du bot. Dans un environnement de test local, vous pouvez utiliser un GUID. Si aucun ID d’application n’est à ce moment-là affecté au bot, le contrôleur de notification génère automatiquement un ID d’espace réservé à utiliser pour l’appel.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Chaque fois que la page `/api/notify` du serveur est demandée, le serveur récupère les références de conversation à partir du dictionnaire.
Le serveur utilise ensuite la méthode `continueConversation` pour envoyer le message proactif.
Le paramètre pour `continueConversation` est une fonction qui sert de gestionnaire de tours au bot pour ce tour.

[!code-javascript[Notify logic](~/../botbuilder-samples/samples/javascript_nodejs/16.proactive-messages/index.js?range=68-80&highlight=4-6)]

---

## <a name="test-your-bot"></a>Tester votre robot

1. Si ce n’est pas déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur et connectez-vous à votre bot.
1. Chargez à la page de notification/d’api de votre bot. Un message proactif est alors généré dans l’émulateur.

## <a name="additional-information"></a>Informations supplémentaires

En plus de l’exemple utilisé dans cet article, d’autres exemples sont disponibles en C# et en JS sur [GitHub](https://github.com/Microsoft/BotBuilder-Samples/).

### <a name="avoiding-401-unauthorized-errors"></a>Évitez les erreurs 401 « Non autorisé » 

Par défaut, le kit SDK BotBuilder ajoute `serviceUrl` à la liste des noms d’hôte approuvés si la requête entrante est authentifiée par BotAuthentication. Ils sont conservés dans un cache en mémoire. Si votre bot est redémarré, un utilisateur en attente d’un message proactif ne peut pas le recevoir, sauf s’il a sollicité de nouveau le bot par un message après son redémarrage. 

Pour éviter ce problème, vous devez ajouter manuellement `serviceUrl` à la liste des noms d’hôtes approuvés à l’aide de : 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp 
MicrosoftAppCredentials.TrustServiceUrl(serviceUrl); 
``` 

Pour la messagerie proactive, `serviceUrl` est l’URL du canal que le destinataire du message proactif utilise ; elle se trouve dans `Activity.ServiceUrl`. 

Vous devez ajouter le code ci-dessus, juste avant le code qui envoie le message proactif. Dans l’[exemple de messages proactifs](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages), vous devez le placer dans `NotifyController.cs` juste avant `await turnContext.SendActivityAsync("proactive hello");`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```js
MicrosoftAppCredentials.trustServiceUrl(serviceUrl);
```

Pour la messagerie proactive, `serviceUrl` est l’URL du canal que le destinataire du message proactif utilise ; elle se trouve dans `activity.serviceUrl`.

Vous devez ajouter le code ci-dessus, juste avant le code qui envoie le message proactif. Dans l’[exemple de messages proactifs](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages), vous devez le placer dans `index.js` juste avant `await turnContext.sendActivity('proactive hello');`.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Implémenter des flux de conversation séquentiels](bot-builder-dialog-manage-conversation-flow.md)
