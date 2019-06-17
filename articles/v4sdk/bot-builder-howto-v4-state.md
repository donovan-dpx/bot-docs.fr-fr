---
title: Enregistrer les données d’utilisateur et de conversation | Microsoft Docs
description: Découvrez comment enregistrer et récupérer des données d’état avec le kit SDK Bot Framework.
keywords: état de conversation, état d’utilisateur, conversation, enregistrement de l’état, gestion de l’état d’un bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3e844a254f1aee709d7dd0b0866ee4ae14737f65
ms.sourcegitcommit: e276008fb5dd7a37554e202ba5c37948954301f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693716"
---
# <a name="save-user-and-conversation-data"></a>Enregistrer les données d’utilisateur et de conversation

[!INCLUDE[applies-to](../includes/applies-to.md)]

Un bot est par nature sans état. Une fois que votre bot est déployé, il ne peut pas s’exécuter dans le même processus ni sur le même ordinateur d’un tour à l’autre. Votre bot peut toutefois peut avoir besoin de suivre le contexte d’une conversation pour pouvoir gérer son comportement et se rappeler les réponses aux questions précédentes. Les fonctionnalités d’état et de stockage du SDK Bot Framework permettent d’ajouter un état à votre bot. Les bots utilisent la gestion de l’état et les objets de stockage pour gérer et conserver l’état. Le gestionnaire d’état fournit une couche d’abstraction qui vous permet d’accéder aux propriétés d’état à l’aide d’accesseurs de propriété, indépendamment du type de stockage sous-jacent.

## <a name="prerequisites"></a>Prérequis
- Connaissances des [concepts de base des bots](bot-builder-basics.md) et de la façon dont ils [gèrent l’état](bot-builder-concept-state.md).
- Le code présenté dans cet article est basé sur l’**exemple de bot de gestion d’état**. Vous aurez besoin d’une copie de l’exemple en [CSharp](https://aka.ms/statebot-sample-cs) ou en [JavaScript](https://aka.ms/statebot-sample-js).

## <a name="about-this-sample"></a>À propos de cet exemple
Quand il reçoit l’entrée utilisateur, cet exemple vérifie l’état de conversation stocké pour voir si cet utilisateur a précédemment été invité à fournir son nom. Si ce n’est pas le cas, le nom de l’utilisateur est demandé et cette entrée est stockée dans l’état utilisateur. Dans l’affirmative, le nom stocké dans l’état utilisateur sert à communiquer avec l’utilisateur. Par ailleurs, ses données d’entrée ainsi que l’heure de réception et l’ID du canal d’entrée sont retournés à l’utilisateur. Les valeurs d’heure et d’ID de canal sont récupérées à partir des données de conversation utilisateur, puis enregistrées dans l’état de conversation. Le diagramme suivant montre la relation entre le bot, le profil utilisateur et les classes de données de conversation.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)
![exemple de bot avec état](media/StateBotSample-Overview.png)

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
![exemple de bot avec état](media/StateBotSample-JS-Overview.png)

---

## <a name="define-classes"></a>Définir les classes

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

La première étape de la configuration de la gestion d’état consiste à définir les classes qui contiendront toutes les informations que nous voulons gérer dans l’état utilisateur et de conversation. Pour cet exemple, nous avons défini les éléments suivants :

- Dans **UserProfile.cs**, nous définissons une classe `UserProfile` pour les informations utilisateur qui seront collectées par le bot. 
- Dans **ConversationData.cs**, nous définissons une classe `ConversationData` pour contrôler l’état de notre conversation pendant la collecte des informations utilisateur.

L’exemple de code suivant illustre la création de la définition de la classe UserProfile.

**UserProfile.cs**  
[!code-csharp[UserProfile](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/UserProfile.cs?range=7-11)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

La première étape nécessite le service de Bot Builder qui inclut des définitions pour `UserState` et `ConversationState`.

**index.js**  
[!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=7-9)]

---

## <a name="create-conversation-and-user-state-objects"></a>Créer des objets d’état utilisateur et de conversation

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ensuite, nous inscrivons `MemoryStorage` qui est utilisé pour créer les objets `UserState` et `ConversationState`. Les objets d’état utilisateur et de conversation sont créés lors de `Startup` et la dépendance est injectée dans le constructeur de bot. Les autres services inscrits qui disponibles pour un bot sont les suivants : un fournisseur d’informations d’identification, un adaptateur et l’implémentation de bot.

**Startup.cs**  
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=16-38)]

**StateManagementBot.cs**  
[!code-csharp[StateManagement](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=15-22)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ensuite, nous inscrivons `MemoryStorage` qui est alors utilisé pour créer les objets `UserState` et `ConversationState`.

**index.js**  
[!code-javascript[DefineMemoryStore](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=32-38)]

---

## <a name="add-state-property-accessors"></a>Ajouter des accesseurs de propriété d’état

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Maintenant, nous créons des accesseurs de propriété à l’aide de la méthode `CreateProperty` qui fournit un handle à l’objet `BotState`. Chaque accesseur de propriété d’état vous permet d’obtenir ou de définir la valeur de la propriété d’état associée. Avant d’utiliser nos propriétés d’état, nous devons utiliser chaque accesseur pour charger la propriété à partir du stockage et la récupérer dans le cache d’état. Pour obtenir la clé incluse dans l’étendue appropriée et associée à la propriété d’état, nous appelons la méthode `GetAsync`.

**StateManagementBot.cs**  
[!code-csharp[StateAccessors](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=38-46)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nous créons maintenant des accesseurs de propriété pour `UserState` et `ConversationState`. Chaque accesseur de propriété d’état vous permet d’obtenir ou de définir la valeur de la propriété d’état associée. Nous utilisons chaque accesseur pour charger la propriété associée à partir du stockage et récupérer son état actuel à partir du cache.

**StateManagementBot.js**.  
[!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=6-19)]

---

## <a name="access-state-from-your-bot"></a>Accéder à l’état à partir du bot
La section précédente présente les étapes nécessaires au moment de l’initialisation pour ajouter des accesseurs de propriété d’état à notre bot. Nous pouvons à présent utiliser ces accesseurs au moment de l’exécution pour lire et écrire des informations d’état. L’exemple de code ci-dessous utilise le flux logique suivant :
- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _true_, nous récupérons le nom d’utilisateur fourni et nous le stockons dans l’état utilisateur.
- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _false_, nous demandons le nom de l’utilisateur.
- Si userProfile.Name a été préalablement stocké, nous récupérons l’heure et l’ID de canal du message à partir de l’entrée utilisateur, nous renvoyons toutes les données à l’utilisateur, puis nous stockons les données récupérées dans l’état de conversation.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

**StateManagementBot.cs**  
[!code-csharp[OnMessageActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=38-85)]

Avant de quitter le gestionnaire de tours, nous utilisons la méthode _SaveChangesAsync()_ des objets de gestion d’état pour écrire tous les changements d’état dans le stockage.

**StateManagementBot.cs**  
[!code-csharp[OnTurnAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=24-31)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**StateManagementBot.js**  
[!code-javascript[OnMessage](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=21-54)]

Avant de quitter chaque tour de dialogue, nous utilisons la méthode _saveChanges()_ des objets de gestion d’état pour conserver tous les changements en récrivant l’état dans le stockage.

**StateManagementBot.js**  
[!code-javascript[OnDialog](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=60-67)]

---

## <a name="test-the-bot"></a>Tester le bot

Téléchargez et installez la dernière version de [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).

1. Exécutez l’exemple en local sur votre machine. Si vous avez besoin d’instructions, consultez le fichier LISEZ-MOI pour l’[exemple C#](https://aka.ms/statebot-sample-cs) ou l’[exemple JS](https://aka.ms/statebot-sample-js).
1. Utilisez l’émulateur pour tester le bot comme indiqué ci-dessous.

![exemple de bot avec état à des fins de test](media/state-bot-testing-emulator.png)

## <a name="additional-resources"></a>Ressources supplémentaires

**Confidentialité :** Si vous prévoyez de stocker les données personnelles de l’utilisateur, vous devez veiller au respect du [Règlement général sur la protection des données](https://blog.botframework.com/2018/04/23/general-data-protection-regulation-gdpr).

**Gestion de l’état :** Tous les appels de gestion d’état sont asynchrones et, par défaut, le dernier qui écrit gagne (last-writer-wins). Dans la pratique, vous devez obtenir, définir et enregistrer l’état dans votre bot de la façon la plus rapprochée possible.

**Données critique sur l’activité :** Utilisez l’état du bot pour stocker les préférences, le nom d’utilisateur ou le dernier élément commandé. En revanche, ne l’utilisez pas pour stocker des données critiques. Pour les données critiques, [créez vos propres composants de stockage](bot-builder-custom-storage.md) ou écrivez directement dans le [stockage](bot-builder-howto-v4-storage.md).

**Recognizers-Text :** L’exemple utilise les bibliothèques Microsoft/Recognizers-Text pour analyser et valider les entrées utilisateur. Pour plus d’informations, consultez la page de [présentation](https://github.com/Microsoft/Recognizers-Text#microsoft-recognizers-text-overview).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment configurer l’état pour vous aider à lire et à écrire les données d’un bot dans le stockage, examinons comment poser une série de questions à l’utilisateur, valider ses réponses et enregistrer ses entrées.

> [!div class="nextstepaction"]
> [Créez vos propres invites pour collecter les entrées utilisateur](bot-builder-primitive-prompts.md).
