---
title: Envoyer un message de bienvenue aux utilisateurs | Microsoft Docs
description: Découvrez comment développer votre bot pour offrir une expérience utilisateur conviviale.
keywords: vue d’ensemble, développer, expérience utilisateur, Accueil, expérience personnalisée, C#, JS, message de bienvenue, bot, salut, salutations
author: DanDev33
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d3c369772736bddc96cab8b9e15e4005bd05bc3c
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491696"
---
# <a name="send-welcome-message-to-users"></a>Envoyer un message de bienvenue aux utilisateurs

[!INCLUDE[applies-to](../includes/applies-to.md)]

L’objectif principal lors de la création d’un bot est d’impliquer votre utilisateur dans une conversation utile. Pour atteindre cet objectif, l’une des meilleures méthodes consiste à s’assurer qu’à partir du moment où un utilisateur se connecte pour la première fois, il comprenne les fonctionnalités et l’utilité principales de votre bot, c’est-à-dire la raison pour laquelle il a été créé. Cet article fournit des exemples de code qui vous permettent d’accueillir les utilisateurs sur votre bot.

## <a name="prerequisites"></a>Conditions préalables requises

- Comprendre les [concepts de base des bots](bot-builder-basics.md).
- Une copie de l’**exemple de bienvenue à l’utilisateur** dans l’[exemple en C#](https://aka.ms/welcome-user-mvc), l’[exemple en JS](https://aka.ms/bot-welcome-sample-js) ou l’[exemple en Python](https://aka.ms/bot-welcome-python-sample-code). Le code de l’exemple est utilisé pour expliquer comment envoyer des messages de bienvenue.

## <a name="about-this-sample-code"></a>À propos de cet exemple de code

Cet exemple de code montre comment détecter et accueillir de nouveaux utilisateurs quand ils sont initialement connectés à votre bot. Le diagramme suivant montre le flux logique pour ce bot.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Les deux principaux événements rencontrés par le bot sont :

- `OnMembersAddedAsync`, qui est appelé chaque fois qu’un nouvel utilisateur est connecté à votre bot
- `OnMessageActivityAsync`, qui est appelé chaque fois qu’une nouvelle entrée utilisateur est reçue.

![Flux logique de bienvenue à l’utilisateur](media/welcome-user-flow.png)

Chaque fois qu’un nouvel utilisateur est connecté, le bot lui fournit un `WelcomeMessage`, un `InfoMessage` et un `PatternMessage`.
Quand une nouvelle entrée utilisateur est reçue, WelcomeUserState est vérifié pour voir si `DidBotWelcomeUser` a la valeur _true_. Si ce n’est pas le cas, un premier message de bienvenue est retourné à l’utilisateur.

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les deux principaux événements rencontrés par le bot sont :

- `onMembersAdded`, qui est appelé chaque fois qu’un nouvel utilisateur est connecté à votre bot
- `onMessage`, qui est appelé chaque fois qu’une nouvelle entrée utilisateur est reçue.

![Flux logique de bienvenue à l’utilisateur](media/welcome-user-flow-js.png)

Chaque fois qu’un nouvel utilisateur est connecté, le bot lui fournit un `welcomeMessage`, un `infoMessage` et un `patternMessage`.
Quand une nouvelle entrée utilisateur est reçue, `welcomedUserProperty` est vérifié pour voir si `didBotWelcomeUser` a la valeur _true_. Si ce n’est pas le cas, un premier message de bienvenue est retourné à l’utilisateur.
Si `DidBotWelcomeUser` a la valeur _true_, l’entrée de l’utilisateur est évaluée. En fonction du contenu de l’entrée de l’utilisateur, ce bot effectuera l’une des opérations suivantes :

- Renvoyer un message d’accueil reçu de l’utilisateur.
- Afficher une bannière fournissant des informations supplémentaires sur les bots.
- Renvoyer le message `WelcomeMessage` en expliquant des entrées attendues pour ce bot.

### <a name="pythontabpython"></a>[Python](#tab/python)

Les deux principaux événements rencontrés par le bot sont :

- `on_members_added_activity`, qui est appelé chaque fois qu’un nouvel utilisateur est connecté à votre bot
- `on_message_activity`, qui est appelé chaque fois qu’une nouvelle entrée utilisateur est reçue.

![Flux logique de bienvenue à l’utilisateur](media/welcome-user-flow-python.png)

Chaque fois qu’un nouvel utilisateur est connecté, le bot lui fournit un *message de bienvenue*, *un message d’information* et un *message de modèle*.
Quand une nouvelle entrée utilisateur est reçue, la propriété `welcome_user_state.did_welcome_user` fait l’objet d’une vérification pour déterminer si elle a la valeur *true*. Si elle n’a pas la valeur *true*, un message de bienvenue initial est retourné à l’utilisateur. Si elle a la valeur *true*, ce bot effectue l’une des actions suivantes en fonction du contenu de l’entrée de l’utilisateur :

- Renvoyer un message d’accueil reçu de l’utilisateur. 
- Afficher une bannière fournissant des informations supplémentaires sur les bots.

---

## <a name="create-user-object"></a>Créer un objet utilisateur

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’objet d’état utilisateur est créé au démarrage et la dépendance est injectée dans le constructeur de bot.

**Startup.cs**  
[!code-csharp[Configure services](~/../botBuilder-samples/samples/csharp_dotnetcore/03.welcome-user/Startup.cs?range=30-34)]

**WelcomeUserBot.cs**  
[!code-csharp[Consume services](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=41-47)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Au démarrage, le stockage en mémoire et l’état utilisateur sont définis dans `index.js`.

**Index.js**  
[!code-javascript[Import statement](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/Index.js?range=8-10)]
[!code-javascript[Create objects](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/Index.js?range=51-55)]

### <a name="pythontabpython"></a>[Python](#tab/python)

Au démarrage, l’état utilisateur et le message de bienvenue sont définis dans `welcome-user-bot.py`.

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=23-37)]

---

## <a name="create-property-accessors"></a>Créer des accesseurs de propriété

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nous créons maintenant un accesseur de propriété qui nous fournit un handle vers WelcomeUserState dans la méthode OnMessageActivityAsync.
Appelons ensuite la méthode GetAsync pour obtenir la clé incluse dans l’étendue appropriée. Nous enregistrons alors les données d’état utilisateur après chaque itération d’entrée utilisateur à l’aide de la méthode `SaveChangesAsync`.

**WelcomeUserBot.cs**  
[!code-csharp[Get state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-71)]
[!code-csharp[Save state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range= 103-105)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Nous créons maintenant un accesseur de propriété qui nous fournit un handle vers welcomedUserProperty, lequel est conservé dans userState.

**WelcomeBot.js**  
[!code-javascript[Define welcome property key](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=7-8)]
[!code-javascript[Create welcome property accessor](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=16-22)]

### <a name="pythontabpython"></a>[Python](#tab/python)

Créez un accesseur de propriété `user_state_accessor` pour fournir un handle à `WelcomeUserState` dans `on_message_activity`.

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=31)]
---

## <a name="detect-and-greet-newly-connected-users"></a>Détecter et accueillir les utilisateurs nouvellement connectés

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans **WelcomeUserBot**, nous recherchons une mise à jour de l’activité à l’aide de `OnMembersAddedAsync()` pour voir si un nouvel utilisateur a été ajouté à la conversation, puis nous lui envoyons un ensemble de trois premiers messages de bienvenue `WelcomeMessage`, `InfoMessage` et `PatternMessage`. Le code complet de cette interaction est affiché ci-dessous.

**WelcomeUserBot.cs**  
[!code-csharp[Define messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=20-39)]
[!code-csharp[Send messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=55-66)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ce code JavaScript envoie les premiers messages de bienvenue quand un utilisateur est ajouté. Pour effectuer cette opération, consultez l’activité de conversation et vérifiez qu’un nouveau membre a été ajouté à la conversation.

**WelcomeBot.js**  
[!code-javascript[Send messages](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=65-88)]

### <a name="pythontabpython"></a>[Python](#tab/python)

`on_members_added_activity` vérifie si un nouvel utilisateur a été ajouté, puis envoie trois messages de bienvenue initiaux : un *message de bienvenue*, un *message d’information* et un *message de modèle*.

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=55-74)]

---

## <a name="welcome-new-user-and-discard-initial-input"></a>Accueillir un nouvel utilisateur et ignorer l’entrée initiale

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Il est également important de déterminer si l’entrée de votre utilisateur contient des informations utiles, ce qui peut varier en fonction de chaque canal. Pour vérifier que votre utilisateur bénéficie d’une bonne expérience sur tous les canaux possibles, nous examinons l’indicateur d’état _didBotWelcomeUser_. Si la valeur est « false », nous ne traitons pas l’entrée utilisateur initiale. Au lieu de cela, nous fournissons à l’utilisateur un premier message de bienvenue. La valeur booléenne _welcomedUserProperty_ est ensuite définie sur « true » et stockée dans UserState. Notre code traitera désormais l’entrée de cet utilisateur à partir de toutes les activités de message supplémentaires.

**WelcomeUserBot.cs**  
[!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-82)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Il est également important de déterminer si l’entrée de votre utilisateur contient des informations utiles, ce qui peut varier en fonction de chaque canal. Pour vérifier si l’utilisateur bénéficie d’une bonne expérience sur tous les canaux possibles, nous examinons la propriété didBotWelcomedUser. Si elle n’existe pas, nous lui affectons la valeur « false », et nous ne traitons pas l’entrée initiale de l’utilisateur. Au lieu de cela, nous fournissons à l’utilisateur un premier message de bienvenue. La valeur booléenne didBotWelcomeUser prend ensuite la valeur « true », puis notre code traite l’entrée utilisateur à partir de toutes les activités de message supplémentaires.

**WelcomeBot.js**  
[!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-38,57-59,63)]

### <a name="pythontabpython"></a>[Python](#tab/python)

Il est également important de déterminer si l’entrée de l’utilisateur contient des informations utiles, ce qui peut varier en fonction de chaque canal. Pour garantir à l’utilisateur une bonne expérience sur tous les canaux possibles, `on_message_activity` vérifie la propriété `did_welcome_user`. La première fois, il lui affecte la valeur *false* et ne traite pas l’entrée utilisateur. À la place, il fournit à l’utilisateur un message de bienvenue initial. Il affecte ensuite à `did_welcome_user` la valeur *true*, puis traite l’entrée utilisateur à partir de toutes les activités de message supplémentaires.

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=85-95)]

---

## <a name="process-additional-input"></a>Traiter une entrée supplémentaire

Une fois qu’un nouvel utilisateur a été accueilli, les informations d’entrée utilisateur sont évaluées pour chaque tour de message, et le bot fournit une réponse en fonction du contexte de cette entrée utilisateur. Le code suivant illustre la logique de décision utilisée pour générer cette réponse.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Une entrée 'intro' ou 'help' appelle la fonction `SendIntroCardAsync` pour présenter à l’utilisateur une bannière d’informations. Ce code est examiné dans la section suivante de cet article.

**WelcomeUserBot.cs**  
[!code-csharp[SwitchOnUtterance](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=85-100)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Une entrée 'intro' ou 'help' utilise CardFactory pour présenter à l’utilisateur une carte adaptative d’introduction. Ce code est examiné dans la section suivante de cet article.

**WelcomeBot.js**  
[!code-javascript[SwitchOnUtterance](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=40-56)]

### <a name="pythontabpython"></a>[Python](#tab/python)

Si un utilisateur entre *intro* ou *help* (aide), le bot appelle `__send_intro_card` qui présente à l’utilisateur une carte adaptative d’introduction.

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=101-106&highlight=103-104)]

---

## <a name="using-hero-card-greeting"></a>Utilisation d’un message de bienvenue sous forme de bannière

Comme indiqué ci-dessus, certaines entrées utilisateur génèrent une *bannière* en réponse à leur demande. Vous pouvez en apprendre plus sur les messages de bienvenue sous forme de bannière, consultez [Envoyer une carte d’introduction](./bot-builder-howto-add-media-attachments.md). Vous trouverez ci-dessous le code nécessaire pour créer cette réponse sous forme de bannière du bot.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**WelcomeUserBot.cs**  
[!code-csharp[SendHeroCardGreeting](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=107-125)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**WelcomeBot.js**  
[!code-javascript[SendIntroCard](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=91-116)]

### <a name="pythontabpython"></a>[Python](#tab/python)

**welcome-user-bot.py** [!code-python[user state](~/../botbuilder-python/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=108-143)]

---

## <a name="test-the-bot"></a>Tester le bot

Téléchargez et installez la dernière version de [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).

1. Exécutez l’exemple en local sur votre machine. Si vous avez besoin d’instructions, consultez le fichier LISEZ-MOI pour l’[exemple C#](https://aka.ms/welcome-user-mvc) ou l’[exemple JS](https://aka.ms/bot-welcome-sample-js).
1. Utilisez l’émulateur pour tester le bot comme indiqué ci-dessous.

![tester l’exemple de bienvenue du bot](media/welcome-user-emulator-1.png)

Testez un message de bienvenue sous forme de bannière.

![tester la carte de bienvenue du bot](media/welcome-user-emulator-2.png)

## <a name="additional-resources"></a>Ressources supplémentaires

Découvrez-en plus sur les réponses de différents médias dans [Ajouter des actifs multimédias aux messages](./bot-builder-howto-add-media-attachments.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Collecter les entrées utilisateur](bot-builder-prompts.md)
