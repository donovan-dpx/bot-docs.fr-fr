---
title: Créer vos propres invites pour collecter des entrées utilisateur | Microsoft Docs
description: Découvrez comment gérer un flux de conversation avec des invites primitives dans le kit SDK Bot Framework.
keywords: flux de conversation, invites, état de conversation, état utilisateur, invites personnalisées
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/08/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3470b1c8f3fbcfb7fecbb060a54b1a356ad41b61
ms.sourcegitcommit: 4086189a9c856fbdc832eb1a1d205e5f1b4e3acd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65733319"
---
# <a name="create-your-own-prompts-to-gather-user-input"></a>Créer vos propres invites pour collecter des entrées utilisateur

[!INCLUDE[applies-to](../includes/applies-to.md)]

Une conversation entre un bot et un utilisateur implique souvent une demande (invite) d’informations à l’utilisateur, l’analyse de sa réponse et l’action en fonction des informations obtenues. Votre bot doit suivre le contexte d’une conversation pour pouvoir gérer son comportement et se rappeler des réponses aux questions précédentes. L’*état* d’un bot représente des informations qu’il suit pour répondre correctement aux messages entrants. 

> [!TIP]
> La bibliothèque de dialogues propose des invites intégrées fournissant de nouvelles fonctionnalités que les utilisateurs peuvent utiliser. Vous trouverez des exemples de ces invites dans l’article [Implémenter des flux de conversation séquentiels](bot-builder-dialog-manage-conversation-flow.md).

## <a name="prerequisites"></a>Prérequis

- Le code affiché dans cet article est basé sur l’exemple « Demander aux utilisateurs d’effectuer une saisie ». Vous aurez besoin d’une copie de l’**[exemple C#](https://aka.ms/cs-primitive-prompt-sample) ou de l’[exemple JavaScript](https://aka.ms/js-primitive-prompt-sample)**.
- Connaissances de la [gestion de l’état](bot-builder-concept-state.md) et du fait d’[enregistrer les données d’utilisateur et de conversation](bot-builder-howto-v4-state.md).

## <a name="about-the-sample-code"></a>Au sujet de l’exemple de code

L’exemple de bot pose à l’utilisateur une série de questions, valide certaines de ses réponses et enregistre son entrée. Le diagramme suivant montre la relation entre le bot, le profil utilisateur et les classes de flux de conversation. 

## <a name="ctabcsharp"></a>[C#](#tab/csharp)
![custom-prompts](media/CustomPromptBotSample-Overview.png)

- Une classe `UserProfile` pour les informations utilisateur qui seront collectées par le bot.
- Une classe `ConversationFlow` pour contrôler l’état de notre conversation pendant la collecte des informations utilisateur.
- Une énumération `ConversationFlow.Question` interne pour le suivi d’où nous en sommes dans la conversation.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
![custom-prompts](media/CustomPromptBotSample-JS-Overview.png)

- Une classe `userProfile` pour les informations utilisateur qui seront collectées par le bot.
- Une classe `conversationFlow` pour contrôler l’état de notre conversation pendant la collecte des informations utilisateur.
- Une énumération `conversationFlow.question` interne pour le suivi d’où nous en sommes dans la conversation.

---

L’état utilisateur effectue le suivi du nom de l’utilisateur, de son âge et de la date choisie, tandis que l’état de conversation effectue le suivi de ce que nous venons de demander à l’utilisateur.
Étant donné que nous ne prévoyons de déployer ce bot, nous allons configurer un état d’utilisateur et de conversation pour utiliser le _stockage mémoire_. 

Nous utilisons le gestionnaire de tour de messages du bot ainsi que les propriétés d’état utilisateur et de conversion pour gérer le flux de la conversation et la collection d’entrée. Dans notre bot, nous allons enregistrer les informations de propriétés d’état reçues pendant chaque itération du gestionnaire de tour de messages.

## <a name="create-conversation-and-user-objects"></a>Créer des objets utilisateur et de conversation

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Les objets d’état utilisateur et de conversation sont créés au démarrage et la dépendance est injectée dans le constructeur de bot. 

**Startup.cs** [!code-csharp[Startup](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Startup.cs?range=27-34)]

**Bots/CustomPromptBot.cs** [!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=21-28)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dans **index.js**, créez les propriétés d’état et le bot, puis appelez la méthode de bot `run` à partir de `processActivity`.

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/index.js?range=32-35)]

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/index.js?range=55-58)]

---

## <a name="create-property-accessors"></a>Créer des accesseurs de propriété

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nous commençons par créer des accesseurs de propriété qui nous donnent un handle vers le `BotState` dans la méthode `OnMessageActivityAsync`. Ensuite, nous appelons la méthode `GetAsync` pour obtenir la clé incluse dans l’étendue appropriée :

**Bots/CustomPromptBot.cs** [!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-37)]

Enfin, nous enregistrons les données à l’aide de la méthode `SaveChangesAsync`.

[!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=42-43)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Dans le constructeur, nous créons les accesseurs de propriété d’état et nous configurons les objets de gestion des états (créés ci-dessus) pour notre conversation.

**bots/customPromptBot.js** [!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=23-29)]

Nous définissons ensuite un deuxième gestionnaire, `onDialog`, à exécuter après le gestionnaire de messages principal (procédure décrite dans la section suivante). Ce deuxième gestionnaire s’assure que nous enregistrons notre état à chaque tour.

[!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=41-48)]

---

## <a name="the-bots-message-turn-handler"></a>Gestionnaire de tours de message du bot

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour gérer les activités de message, nous utilisons la méthode d’assistance _FillOutUserProfileAsync()_ avant d’enregistrer l’état à l’aide de _SaveChangesAsync()_. Voici le code complet.

**Bots/CustomPromptBot.cs** [!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=30-44)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour gérer les activités de message, nous configurons notre conversation et nos données utilisateur, puis nous utilisons la méthode d’assistance `fillOutUserProfile()`. Voici le code complet pour le gestionnaire de tours.

**bots/customPromptBot.js** [!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=31-39)]
---

## <a name="filling-out-the-user-profile"></a>Remplissage du profil utilisateur

Nous allons commencer par la collecte d’informations. Chacune d’elles fournit une interface similaire.

- La valeur de retour indique si l’entrée est une réponse valide pour cette question.
- Si la validation réussit, elle génère une valeur analysée et normalisée pour l’enregistrer.
- Si la validation échoue, elle génère un message avec lequel le bot peut redemander les informations.

 Dans la section suivante, nous allons définir les méthodes d’assistance permettant d’analyser et de valider l’entrée utilisateur.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots/CustomPromptBot.cs** [!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=46-103)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.js** [!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=52-116)]

---

## <a name="parse-and-validate-input"></a>Analyser et valider l’entrée

Nous allons utiliser les critères suivants pour valider l’entrée.

- Le **nom** doit être une chaîne non vide. Nous allons le normaliser en supprimant les espaces.
- L’**âge** doit être une valeur comprise entre 18 et 120. Nous allons le normaliser en retournant un nombre entier.
- La **date** doit être de n’importe quelle date ou heure située au moins une heure dans le futur.
  Nous allons la normaliser en retournant uniquement la partie de date de l’entrée analysée.

> [!NOTE]
> Pour l’âge et la date d’entrée, nous utilisons les bibliothèques [Microsoft/Recognizers-Text](https://github.com/Microsoft/Recognizers-Text/) pour effectuer l’analyse initiale.
> Bien que nous fournissions des exemples de code, nous n’expliquons pas la façon dont les bibliothèques de modules de reconnaissance de texte fonctionnent et il s’agit juste d’une manière d’analyser l’entrée.
> Pour plus d’informations sur ces bibliothèques, consultez le fichier **LISEZ-MOI** du référentiel.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ajoutez les méthodes de validation suivantes à votre bot.

**Bots/CustomPromptBot.cs** [!code-csharp[custom prompt bot](~/../botbuilder-samples/samples/csharp_dotnetcore/44.prompt-users-for-input/Bots/CustomPromptBot.cs?range=105-203)]

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/customPromptBot.cs** [!code-javascript[custom prompt bot](~/../botbuilder-samples/samples/javascript_nodejs/44.prompt-for-user-input/bots/customPromptBot.js?range=118-189)]

---

## <a name="test-the-bot-locally"></a>Testez le bot localement
Téléchargez et installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) pour tester le bot localement.

1. Exécutez l’exemple en local sur votre machine. Si vous avez besoin d’instructions, consultez le fichier LISEZ-MOI pour l’[exemple C#](https://aka.ms/cs-primitive-prompt-sample) ou l’[exemple JS](https://aka.ms/js-primitive-prompt-sample).
1. Testez-le à l’aide de l’émulateur comme indiqué ci-dessous.

![primitive-prompts](media/primitive-prompts.png)

## <a name="additional-resources"></a>Ressources supplémentaires

La [bibliothèque de boîtes de dialogue](bot-builder-concept-dialog.md) fournit des classes qui automatisent de nombreux aspects de la gestion des conversations. 

## <a name="next-step"></a>Étape suivante

> [!div class="nextstepaction"]
> [Implémenter des flux de conversation séquentiels](bot-builder-dialog-manage-conversation-flow.md)
