---
title: Ajouter la compréhension du langage naturel à votre bot | Microsoft Docs
description: Découvrez comment utiliser LUIS pour la compréhension du langage naturel avec le kit SDK Bot Framework.
keywords: Language Understanding, LUIS, intention, module de reconnaissance, entités, middleware (intergiciel)
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7b06d99ae1265d2519b5c1aa8fe838a4e3e4d43a
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215350"
---
# <a name="add-natural-language-understanding-to-your-bot"></a>Ajouter la compréhension du langage naturel à votre bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

La capacité à comprendre ce que veut dire votre utilisateur du point de vue de la conversation et du contexte peut être une tâche difficile, mais peut donner à votre bot un sens de la conversation plus naturel. Le service de compréhension langagière, appelé LUIS, vous permet justement de faire cela ; votre bot peut ainsi reconnaître l’intention des messages de l’utilisateur, prendre en charge un langage plus naturel de l’utilisateur et mieux diriger le flux de la conversation. Cette rubrique vous guide dans l’ajout de LUIS à une application de réservation de vol pour reconnaître les différentes intentions et entités contenues dans les entrées utilisateur. 

## <a name="prerequisites"></a>Prérequis
- Compte [LUIS](https://www.luis.ai)
- Le code de cet article est basé sur l’exemple **Bot de base**. Vous avez besoin d’une copie de l’exemple, au choix en **[CSharp](https://aka.ms/cs-core-sample) ou en [JavaScript](https://aka.ms/js-core-sample)** . 
- Connaissances des [concepts de base des bots](bot-builder-basics.md), du [traitement en langage naturel](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/what-is-luis) et de la [gestion des ressources de bot](bot-file-basics.md).

## <a name="about-this-sample"></a>À propos de cet exemple

Cet exemple de code du bot de base montre l’exemple d’une application de réservation de vols d’un aéroport. Elle utilise un service LUIS pour reconnaître les entrées utilisateur et retourner les premières intentions LUIS reconnues. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Après chaque traitement d’entrée utilisateur, `DialogBot` enregistre l’état actuel de `UserState` et de `ConversationState`. Lorsque toutes les informations nécessaires sont réunies, l’exemple de code crée une réservation de vol de démonstration. Dans cet article, nous allons aborder les aspects LUIS de cet exemple. Toutefois, le flux général de l’exemple est présenté ci-dessous :

- `OnMembersAddedAsync` est appelé lorsqu’un nouvel utilisateur est connecté et affiche une carte de bienvenue. 
- `OnMessageActivityAsync` est appelé pour chaque entrée d’utilisateur reçue. 

![Flux de la logique de l’exemple LUIS](./media/how-to-luis/luis-logic-flow.png)

Le module `OnMessageActivityAsync` exécute le dialogue approprié par le biais de la méthode d’extension de dialogue `Run`. Ce dialogue principal appelle l’application d’assistance LUIS pour rechercher les intentions utilisateur les mieux notées. Si la meilleure intention pour l’entrée utilisateur retourne « Book_Flight », l’application d’assistance remplit les informations à partir de l’utilisateur que LUIS a retourné, et démarre `BookingDialog`, qui acquiert les informations supplémentaires nécessaires auprès de l’utilisateur, par exemple :

- `Origin` la ville d’origine.
- `TravelDate` la date à laquelle réserver le vol. 
- `Destination` la ville de destination.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Après chaque traitement d’entrée utilisateur, `dialogBot` enregistre l’état actuel de `userState` et de `conversationState`. Lorsque toutes les informations nécessaires sont réunies, l’exemple de code crée une réservation de vol de démonstration. Dans cet article, nous allons aborder les aspects LUIS de cet exemple. Toutefois, le flux général de l’exemple est présenté ci-dessous :

- `onMembersAdded` est appelé lorsqu’un nouvel utilisateur est connecté et affiche une carte de bienvenue. 
- `OnMessage` est appelé pour chaque entrée d’utilisateur reçue. 

![Flux de la logique javascript de l’exemple LUIS](./media/how-to-luis/luis-logic-flow-js.png)

Le module `onMessage` exécute `mainDialog` qui envoie l’entrée utilisateur à LUIS. À réception d’une réponse de LUIS, `mainDialog` conserve les informations pour l’utilisateur retourné par LUIS et démarre `bookingDialog`. `bookingDialog` acquiert les informations supplémentaires nécessaires auprès de l’utilisateur, par exemple :

- `destination` la ville de destination.
- `origin` la ville d’origine.
- `travelDate` la date à laquelle réserver le vol. 

---

Pour plus d’informations sur les autres aspects de l’exemple, tels que les dialogues ou l’état, consultez [Collecter les entrées utilisateur avec une invite de dialogue](bot-builder-prompts.md) ou [Enregistrer les données d’utilisateur et de conversation](bot-builder-howto-v4-state.md). 

## <a name="create-a-luis-app-in-the-luis-portal"></a>Créer une application LUIS dans le portail LUIS
Connectez-vous au portail LUIS pour créer votre propre version de l’exemple d’application LUIS. Vous pouvez créer et gérer vos applications sur la page **Mes applications**. 

1. Sélectionnez **Importer une nouvelle application**. 
1. Cliquez sur **Choisir un fichier d’application (format JSON)...** 
1. Sélectionnez le fichier `FlightBooking.json` situé dans le dossier `CognitiveModels` de l’exemple. Dans **Nom facultatif**, entrez **FlightBooking**. Ce fichier contient trois intentions : « Réserver le vol », « Annuler » et « Aucune ». Nous allons utiliser ces intentions pour comprendre ce que l’utilisateur voulait dire lorsqu’il envoie un message au bot.
1. Effectuez l’[apprentissage](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-train) de l’application.
1. [Publiez](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/publishapp) l’application dans un environnement de *production*.

### <a name="why-use-entities"></a>Pourquoi utiliser des entités
Les entités LUIS permettent à votre bot de comprendre intelligemment certains éléments ou événements qui diffèrent des intentions standard. Vous pouvez ainsi collecter des informations supplémentaires auprès de l’utilisateur, ce qui permet à votre bot de répondre plus intelligemment ou même d’ignorer certaines questions visant à obtenir ces mêmes informations. En plus des définitions des trois intentions LUIS « Réserver le vol », « Annuler » et « Aucune », le fichier FlightBooking.json contient un ensemble d’entités, telles que « From.Airport » et « To.Airport ». Ces entités permettent à LUIS de détecter et de retourner des informations supplémentaires contenues dans l’entrée d’origine de l’utilisateur, lorsque celui-ci fait une nouvelle demande de réservation de voyage.

## <a name="obtain-values-to-connect-to-your-luis-app"></a>Obtenir les valeurs de connexion à votre application LUIS
Une fois votre application LUIS publiée, vous pouvez y accéder à partir de votre bot. Vous devrez enregistrer plusieurs valeurs pour accéder à votre application LUIS à partir de votre bot. Vous pouvez récupérer ces informations à l’aide du portail LUIS.

### <a name="retrieve-application-information-from-the-luisai-portal"></a>Récupérer des informations sur l’application à partir du portail LUIS.ai
Le fichier de paramètres (`appsettings.json` et `.env`) agit comme lieu de rassemblement pour toutes les références de service. Les informations que vous récupérez sont ajoutées à ce fichier dans la section suivante. 
1. Sélectionnez votre application LUIS publiée à partir du portail [luis.ai](https://www.luis.ai).
1. Ouvrez votre application LUIS publiée, et sélectionnez l’onglet **MANAGE** (Gérer). ![Gérer l’application LUIS](./media/how-to-luis/manage-luis-app.png)
1. Sélectionnez l’onglet **Application Information** (Informations sur l’application) à gauche, enregistrez la valeur affichée pour _Application ID_ (ID d’Application) en tant que <YOUR_APP_ID>.
1. Sélectionnez l’onglet **Keys and Endpoints** (Clés et points de terminaison) à gauche, enregistrez la valeur indiquée pour _Authoring Key_ (Clé de création) en tant que <YOUR_AUTHORING_KEY>.
1. Faites défiler jusqu'à la fin de la page, enregistrez la valeur affichée pour _Région_ comme <YOUR_REGION>.

### <a name="update-the-settings-file"></a>Mettre à jour le fichier de paramètres

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ajoutez les informations demandées pour accéder à votre application LUIS, y compris l’ID d’application, la clé de création et la région dans le fichier `appsettings.json`. Il s’agit des valeurs que vous avez enregistrées précédemment à partir de votre application LUIS publiée. Notez que le nom d’hôte API doit être au format `<your region>.api.cognitive.microsoft.com`.

**appsetting.json** [!code-json[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/appsettings.json?range=1-7)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez les informations demandées pour accéder à votre application LUIS, y compris l’ID d’application, la clé de création et la région dans le fichier `.env`. Il s’agit des valeurs que vous avez enregistrées précédemment à partir de votre application LUIS publiée. Notez que le nom d’hôte d’API doit être au format `<your region>.api.cognitive.microsoft.com`.

**.env**
[!code[env](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/.env?range=1-5)]

---

## <a name="configure-your-bot-to-use-your-luis-app"></a>Configurer votre bot pour qu’il utilise votre application LUIS

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Vérifiez que le package NuGet **Microsoft.Bot.Builder.AI.Luis** est installé pour votre projet.

Pour vous connecter au service LUIS, le bot tire (pull) du fichier appsetting.json les informations que vous avez ajoutées précédemment. La classe `LuisHelper` contient le code qui importe vos paramètres à partir du fichier appsetting.json et interroge le service LUIS en appelant la méthode `RecognizeAsync`. Si la meilleure intention retournée est « Book_Flight », il recherche ensuite des entités contenant les informations de réservation, À, De et DateVoyage.

**LuisHelper.cs** [!code-csharp[luis helper](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/LuisHelper.cs?range=15-54)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser LUIS, votre projet doit installer le package npm **botbuilder-ai**.

Pour vous connecter au service LUIS, le bot tire (pull) du fichier `.env` les informations que vous avez ajoutées précédemment. La classe `LuisHelper` contient le code qui importe vos paramètres à partir du fichier `.env` et interroge le service LUIS en appelant la méthode `recognize()`. Si la meilleure intention retournée est « Book_Flight », il recherche ensuite des entités contenant les informations de réservation, À, De et DateVoyage.

[!code-javascript[luis helper](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/luisHelper.js?range=6-65)]

---

LUIS est à présent configuré et connecté pour votre bot. 

## <a name="test-the-bot"></a>Tester le bot

Téléchargez et installez la dernière version de [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).

1. Exécutez l’exemple en local sur votre machine. Si vous avez besoin d’instructions, consultez le fichier lisez-moi pour l’[exemple en C#](https://aka.ms/cs-core-sample) ou l’[exemple en JS](https://aka.ms/js-core-sample).

1. Dans l’émulateur, tapez un message, tel que « voyage à paris » ou « aller de paris à berlin ». Utilisez n’importe quel énoncé trouvé dans le fichier FlightBooking.json pour l’apprentissage de l’intention « Réserver un vol ».

![Entrée de réservation de LUIS](./media/how-to-luis/luis-user-travel-input.png)

Si la meilleure intention retournée à partir de LUIS correspond à « Réserver un vol », votre bot pose des questions supplémentaires jusqu’à ce qu’il possède suffisamment de renseignements stockés pour créer une réservation de voyage. À ce stade, il retourne ces informations de réservation à votre utilisateur. 

![Résultat de réservation de LUIS](./media/how-to-luis/luis-travel-result.png)

À partir d’ici, la logique du code de bot est réinitialisée et vous pouvez continuer à créer d’autres réservations. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser QnA Maker pour répondre aux questions](./bot-builder-howto-qna.md)
