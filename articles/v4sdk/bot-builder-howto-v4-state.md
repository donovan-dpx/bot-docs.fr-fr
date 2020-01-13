---
title: Enregistrer les données d’utilisateur et de conversation | Microsoft Docs
description: Découvrez comment enregistrer et récupérer des données d’état avec le kit SDK Bot Framework.
keywords: état de conversation, état d’utilisateur, conversation, enregistrement de l’état, gestion de l’état d’un bot
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b97b0a79e4bba02abe5045a5d48bcb019454d965
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491461"
---
# <a name="save-user-and-conversation-data"></a>Enregistrer les données d’utilisateur et de conversation

[!INCLUDE[applies-to](../includes/applies-to.md)]

Un bot est par nature sans état. Une fois que votre bot est déployé, il ne peut pas s’exécuter dans le même processus ni sur le même ordinateur d’un tour à l’autre. Votre bot peut toutefois peut avoir besoin de suivre le contexte d’une conversation pour pouvoir gérer son comportement et se rappeler les réponses aux questions précédentes. Les fonctionnalités d’état et de stockage du SDK Bot Framework permettent d’ajouter un état à votre bot. Les bots utilisent la gestion de l’état et les objets de stockage pour gérer et conserver l’état. Le gestionnaire d’état fournit une couche d’abstraction qui vous permet d’accéder aux propriétés d’état à l’aide d’accesseurs de propriété, indépendamment du type de stockage sous-jacent.

## <a name="prerequisites"></a>Conditions préalables requises

- Connaissances des [concepts de base des bots](bot-builder-basics.md) et de la façon dont ils [gèrent l’état](bot-builder-concept-state.md).
- Le code présenté dans cet article est basé sur l’**exemple de bot de gestion d’état**. Vous avez besoin d’une copie de l’exemple en [CSharp](https://aka.ms/statebot-sample-cs), [JavaScript](https://aka.ms/statebot-sample-js) ou [Python](https://aka.ms/bot-state-python-sample-code).

## <a name="about-this-sample"></a>À propos de cet exemple
Quand il reçoit l’entrée utilisateur, cet exemple vérifie l’état de conversation stocké pour voir si cet utilisateur a précédemment été invité à fournir son nom. Si ce n’est pas le cas, le nom de l’utilisateur est demandé et cette entrée est stockée dans l’état utilisateur. Dans l’affirmative, le nom stocké dans l’état utilisateur sert à communiquer avec l’utilisateur. Par ailleurs, ses données d’entrée ainsi que l’heure de réception et l’ID du canal d’entrée sont retournés à l’utilisateur. Les valeurs d’heure et d’ID de canal sont récupérées à partir des données de conversation utilisateur, puis enregistrées dans l’état de conversation. Le diagramme suivant montre la relation entre le bot, le profil utilisateur et les classes de données de conversation.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

![exemple de bot avec état](media/StateBotSample-Overview.png)

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![exemple de bot avec état](media/StateBotSample-JS-Overview.png)

## <a name="pythontabpython"></a>[Python](#tab/python)

![exemple de bot avec état](media/StateBotSample-Python-Overview.png)

---

## <a name="define-classes"></a>Définir les classes

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

La première étape de la configuration de la gestion d’état consiste à définir les classes qui contiennent les informations à gérer dans l’état utilisateur et l’état de conversation. L’exemple utilisé dans cet article définit les classes suivantes :

- Dans **UserProfile.cs**, nous définissons une classe `UserProfile` pour les informations utilisateur qui seront collectées par le bot. 
- Dans **ConversationData.cs**, nous définissons une classe `ConversationData` pour contrôler l’état de notre conversation pendant la collecte des informations utilisateur.

L’exemple de code suivant montre la définition de la classe `UserProfile`.

**UserProfile.cs**  
[!code-csharp[UserProfile](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/UserProfile.cs?range=7-11)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

La première étape nécessite le service de Bot Builder qui inclut des définitions pour `UserState` et `ConversationState`.

**index.js**  
[!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=7-9)]

## <a name="pythontabpython"></a>[Python](#tab/python)

La première étape de la configuration de la gestion d’état consiste à définir les classes qui contiennent les informations à gérer dans l’état utilisateur et l’état de conversation. L’exemple utilisé dans cet article définit les classes suivantes :

- **user-profile.py** contient la classe `UserProfile`, qui stocke les informations utilisateur collectées par le bot.
- **conversation_data.py** contient la classe `ConversationData`, qui contrôle l’état de la conversation pendant la collecte des informations utilisateur.

L’exemple de code suivant montre la définition de la classe `ConversationData`. 

[!code-python[conversation_data](~/../botbuilder-python/samples/python/45.state-management/data_models/conversation_data.py?range=5-14)]

---

## <a name="create-conversation-and-user-state-objects"></a>Créer des objets d’état utilisateur et de conversation

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ensuite, nous inscrivons `MemoryStorage` qui est utilisé pour créer les objets `UserState` et `ConversationState`. Les objets d’état utilisateur et de conversation sont créés lors de `Startup` et la dépendance est injectée dans le constructeur de bot. Les autres services inscrits qui disponibles pour un bot sont les suivants : un fournisseur d’informations d’identification, un adaptateur et l’implémentation de bot.

**Startup.cs**  
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=26-29)]
[!code-csharp[ConfigureServices](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Startup.cs?range=51-57)]

**Bots/StateManagementBot.cs**

[!code-csharp[StateManagement](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=15-22)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ensuite, nous inscrivons `MemoryStorage` qui est alors utilisé pour créer les objets `UserState` et `ConversationState`.

**index.js**  
[!code-javascript[DefineMemoryStore](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/index.js?range=33-39)]

## <a name="pythontabpython"></a>[Python](#tab/python)

Ensuite, nous inscrivons `MemoryStorage` qui est utilisé pour créer les objets `UserState` et `ConversationState`. Les objets d’état utilisateur et de conversation sont créés au démarrage dans `app.py`, et la dépendance est injectée dans le constructeur de bot.

**app.py** [!code-python[state](~/../botbuilder-python/samples/python/45.state-management/app.py?range=63-69)]

**bots/state_management_bot.py** [!code-python[state](~/../botbuilder-python/samples/python/45.state-management/bots/state_management_bot.py?range=14-30)]

---

## <a name="add-state-property-accessors"></a>Ajouter des accesseurs de propriété d’état

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Maintenant, nous créons des accesseurs de propriété à l’aide de la méthode `CreateProperty` qui fournit un handle à l’objet `BotState`. Chaque accesseur de propriété d’état vous permet d’obtenir ou de définir la valeur de la propriété d’état associée. Avant d’utiliser nos propriétés d’état, nous devons utiliser chaque accesseur pour charger la propriété à partir du stockage et la récupérer dans le cache d’état. Pour obtenir la clé incluse dans l’étendue appropriée et associée à la propriété d’état, nous appelons la méthode `GetAsync`.

**Bots/StateManagementBot.cs**

[!code-csharp[StateAccessors](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/Bots/StateManagementBot.cs?range=38-46)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nous créons maintenant des accesseurs de propriété pour `UserState` et `ConversationState`. Chaque accesseur de propriété d’état vous permet d’obtenir ou de définir la valeur de la propriété d’état associée. Nous utilisons chaque accesseur pour charger la propriété associée à partir du stockage et récupérer son état actuel à partir du cache.

**bots/stateManagementBot.js**

[!code-javascript[BotService](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=6-19)]

## <a name="pythontabpython"></a>[Python](#tab/python)

Nous créons maintenant des accesseurs de propriété pour `UserProfile` et `ConversationData`. Chaque accesseur de propriété d’état vous permet d’obtenir ou de définir la valeur de la propriété d’état associée. Nous utilisons chaque accesseur pour charger la propriété associée à partir du stockage et récupérer son état actuel à partir du cache.

**bots/state_management_bot.py** [!code-python[state_accessors](~/../botbuilder-python/samples/python/45.state-management/bots/state_management_bot.py?range=24-30)]

---

## <a name="access-state-from-your-bot"></a>Accéder à l’état à partir du bot

La section précédente présente les étapes nécessaires au moment de l’initialisation pour ajouter des accesseurs de propriété d’état à notre bot. Nous pouvons à présent utiliser ces accesseurs au moment de l’exécution pour lire et écrire des informations d’état. L’exemple de code ci-dessous utilise le flux logique suivant :


## <a name="ctabcsharp"></a>[C#](#tab/csharp)

- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _true_, nous récupérons le nom d’utilisateur fourni et nous le stockons dans l’état utilisateur.
- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _false_, nous demandons le nom de l’utilisateur.
- Si userProfile.Name a été stocké, nous récupérons l’heure du message et l’ID de canal à partir de l’entrée utilisateur, nous retournons toutes les données à l’utilisateur, puis nous stockons les données récupérées dans l’état de conversation.

**Bots/StateManagementBot.cs**

[!code-csharp[OnMessageActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=38-85)]

Avant de quitter le gestionnaire de tours, nous utilisons la méthode _SaveChangesAsync()_ des objets de gestion d’état pour écrire tous les changements d’état dans le stockage.

**Bots/StateManagementBot.cs**

[!code-csharp[OnTurnAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/45.state-management/bots/StateManagementBot.cs?range=24-31)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _true_, nous récupérons le nom d’utilisateur fourni et nous le stockons dans l’état utilisateur.
- Si userProfile.Name est vide et que conversationData.PromptedUserForName a la valeur _false_, nous demandons le nom de l’utilisateur.
- Si userProfile.Name a été stocké, nous récupérons l’heure du message et l’ID de canal à partir de l’entrée utilisateur, nous retournons toutes les données à l’utilisateur, puis nous stockons les données récupérées dans l’état de conversation.

**bots/stateManagementBot.js**

[!code-javascript[OnMessage](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=21-58)]

Avant de quitter chaque tour de dialogue, nous utilisons la méthode _saveChanges()_ des objets de gestion d’état pour conserver tous les changements en récrivant l’état dans le stockage.

**bots/stateManagementBot.js**

[!code-javascript[OnDialog](~/../BotBuilder-Samples/samples/javascript_nodejs/45.state-management/bots/stateManagementBot.js?range=60-67)]

## <a name="pythontabpython"></a>[Python](#tab/python)

- Si `user_profile.name` est vide et si `conversation_data.prompted_for_user_name` a la valeur *true*, le bot récupère le nom fourni par l’utilisateur et le stocke dans l’état de l’utilisateur.
- Si `user_profile.name` est vide et si `conversation_data.prompted_for_user_name` a la valeur *false*, le bot demande le nom de l’utilisateur.
- Si `user_profile.name` a été stocké, le bot récupère l’**heure du message** et l’**ID de canal** à partir de l’entrée utilisateur, retourne les données à l’utilisateur, puis stocke les données récupérées dans l’état de conversation.

**bots/state_management_bot.py** [!code-python[state_message_activity](~/../botbuilder-python/samples/python/45.state-management/bots/state_management_bot.py?range=47-89)]

Avant de quitter chaque tour de dialogue, le bot utilise la méthode `save_changes` des objets de gestion d’état pour conserver tous les changements apportés en écrivant les informations d’état dans le stockage.

**bots/state_management_bot.py** [!code-python[state_storage](~/../botbuilder-python/samples/python/45.state-management/bots/state_management_bot.py?range=32-36)]

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
