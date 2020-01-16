---
title: Utiliser QnA Maker pour répondre aux questions - Bot Service
description: Découvrez comment utiliser QnA Maker dans votre bot.
keywords: questions et réponses, QnA, FAQ, QnA Maker
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9671365cddb9653584974d6063fea9cf38b2e571
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75798329"
---
# <a name="use-qna-maker-to-answer-questions"></a>Utiliser QnA Maker pour répondre aux questions

[!INCLUDE[applies-to](../includes/applies-to.md)]

QnA Maker fournit une couche de questions et réponses conversationnelle sur vos données. Cela permet à votre bot d’envoyer une question à QnA Maker et de recevoir une réponse sans avoir besoin d’analyser et d’interpréter l’intention de la question.

L’une des exigences de base pour créer votre propre service QnA Maker est de remplir ce dernier à l’aide de questions et de réponses. Dans de nombreux cas, les questions et réponses existent déjà dans du contenu comme un Forum aux questions ou d’autres documents ; parfois, vous souhaiterez personnaliser vos réponses aux questions de manière plus naturelle et conversationnelle.

## <a name="prerequisites"></a>Conditions préalables requises

- Le code de cet article est basé sur l’exemple QnA Maker. Vous aurez besoin d’une copie de ce dernier en **[C#](https://aka.ms/cs-qna)** , **[JavaScript](https://aka.ms/js-qna-sample)** ou **[Python](https://aka.ms/bot-qna-python-sample-code)** . 
- Compte [QnA Maker](https://www.qnamaker.ai/)
- Connaissances des [concepts de base des bots](bot-builder-basics.md), de [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/overview) et de la [gestion des ressources de bot](bot-file-basics.md).

## <a name="about-this-sample"></a>À propos de cet exemple

Pour utiliser QnA Maker dans votre bot, vous devez créer une base de connaissances dans le portail [​​QnA Maker](https://www.qnamaker.ai/), comme indiqué dans la section suivante. Votre bot peut alors envoyer les questions de l’utilisateur au QNA Maker pour fournir les meilleures réponses.

## <a name="ctabcs"></a>[C#](#tab/cs)

![Flux de la logique QnABot](./media/qnabot-logic-flow.png)

`OnMessageActivityAsync` est appelé pour chaque entrée d’utilisateur reçue. Quand il est appelé, il accède à des informations `_configuration` stockées dans le fichier `appsetting.json` de l’exemple de code pour rechercher la valeur qui permet de se connecter à votre base de connaissances QnA Maker préconfigurée.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

![Flux de la logique JS QnABot](./media/qnabot-js-logic-flow.png)

`OnMessage` est appelé pour chaque entrée d’utilisateur reçue. Quand il est appelé, il accède à votre connecteur `qnamaker` qui a été préconfiguré à l’aide des valeurs fournies à partir du fichier `.env` de votre exemple de code.  La méthode `getAnswers` qnamaker connecte votre bot à votre base de connaissances QnA Maker externe.

## <a name="pythontabpython"></a>[Python](#tab/python)

![Flux de la logique JS QnABot](./media/qnabot-python-logic-flow.png)

`on_message_activity` est appelé pour chaque entrée d’utilisateur reçue. Quand il est appelé, il accède à votre connecteur `qna_maker` qui a été préconfiguré à l’aide des valeurs fournies à partir du fichier `config.py` de votre exemple de code.  La méthode `qna_maker.getAnswers` connecte votre bot à votre base de connaissances externe QnA Maker.

---

L’entrée de l’utilisateur est envoyée à votre base de connaissances et la meilleure réponse retournée est présentée à l’utilisateur.

## <a name="create-a-qna-maker-service-and-publish-a-knowledge-base"></a>Créer un service QnA Maker et publier une base de connaissances

La première étape consiste à créer un service QnA Maker. Suivez les étapes listées dans la [documentation](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) QnA Maker pour créer le service dans Azure.

Ensuite, vous allez créer une base de connaissances à l’aide du fichier `smartLightFAQ.tsv` situé dans le dossier CognitiveModels de l’exemple de projet. Les étapes permettant de créer, de former et de publier votre [base de connaissances](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) QnA Maker sont répertoriés dans la documentation de QnA Maker. Pendant que vous suivez ces étapes, nommez votre base de connaissances `qna`et utilisez le fichier `smartLightFAQ.tsv` pour la remplir.

> Remarque. Cet article peut également être utilisé pour accéder à votre propre base de connaissances QnA Maker développée par l’utilisateur.

## <a name="obtain-values-to-connect-your-bot-to-the-knowledge-base"></a>Obtenir des valeurs pour connecter votre bot à la base de connaissances

1. Sur le site [QnA Maker](https://www.qnamaker.ai/), sélectionnez votre base de connaissances.
1. Ouvrez votre base de connaissances et sélectionnez **Settings** (Paramètres). Enregistrez la valeur indiquée pour _service name_. Cette valeur est utile pour rechercher la base de connaissances qui vous intéresse quand vous utilisez l’interface du portail QnA Maker. Elle n’est pas utilisée pour connecter votre application bot à cette base de connaissances.
1. Faites défiler la page jusqu’à la section **Détails du déploiement** et enregistrez les valeurs suivantes à partir de la requête HTTP de l’exemple Postman :
   - POST /knowledgebases/\<ID-base-connaissances>/generateAnswer
   - Hôte : \<votre-nom-hôte> // URL complète se terminant par /qnamaker
   - Autorisation : EndpointKey \<votre_clé_de_point de terminaison>

La chaîne d’URL complète du nom d’hôte doit ressembler à « https://< >.azure.net/qnamaker ». Ces trois valeurs fournissent les informations nécessaires pour que votre application se connecte à votre base de connaissances QnA Maker par le biais de votre service Azure QnA.  

## <a name="update-the-settings-file"></a>Mettre à jour le fichier de paramètres

Ajoutez d’abord les informations nécessaires pour accéder à votre base de connaissances, notamment le nom d’hôte, la clé du point de terminaison et l’ID de la base de connaissances (kbId) dans le fichier de paramètres. Il s’agit des valeurs que vous avez enregistrées à partir de l’onglet des paramètres (**Settings**) de votre base de connaissances dans QnA Maker.

Si vous n’effectuez pas le déploiement dans un environnement de production, vous pouvez laisser les champs d’ID d’application et de mot de passe vides.

> [!NOTE]
> Si vous ajoutez un accès à une base de connaissances QnA Maker dans une application bot existante, veillez à ajouter des titres évocateurs pour vos entrées QnA. La valeur « name » de cette section fournit la clé nécessaire pour accéder à ces informations à partir de votre application.

## <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="update-your-appsettingsjson-file"></a>Mettre à jour votre fichier appsettings.json

[!code-csharp[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/appsettings.json)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="update-your-env-file"></a>Mettre à jour votre fichier .env

[!code-javascript[.env file](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/.env)]

## <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="update-your-configpy-file"></a>Mettre à jour votre fichier config.py

[!code-python[config.py](~/../botbuilder-python/samples/python/11.qnamaker/config.py?range=10-18)]

---

## <a name="set-up-the-qna-maker-instance"></a>Configurer l’instance de QnA Maker

Tout d’abord, nous créons un objet pour accéder à notre base de connaissances QnA Maker.

## <a name="ctabcs"></a>[C#](#tab/cs)

Vérifiez que le package NuGet **Microsoft.Bot.Builder.AI.QnA** est installé pour votre projet.

Dans **QnABot.cs**, dans la méthode `OnMessageActivityAsync`, nous créons une instance de QnAMaker. La classe `QnABot` est également l’endroit où sont insérés les noms des informations de connexion, enregistrées dans `appsettings.json` ci-dessus. Si vous avez choisi des noms différents pour vos informations de connexion à la base de connaissances dans votre fichier de paramètres, veillez à mettre à jour les noms ici afin qu’ils reflètent le nom choisi.

**Bots/QnABot.cs**

[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=32-39)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Veillez à ce que le package npm **botbuilder-ai** soit installé pour votre projet.

Dans notre exemple, le code de la logique du bot se trouve dans le fichier **QnABot.js**.

Dans le fichier **QnABot.js**, nous utilisons les informations de connexion fournies par votre fichier .env pour établir une connexion au service QnA Maker : _this.qnaMaker_.

**bots/QnABot.js**

[!code-javascript[QnAMaker](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=12-16)]

## <a name="pythontabpython"></a>[Python](#tab/python)

Dans le fichier **qna_bot.py**, nous utilisons les informations de connexion fournies par le fichier `config.py` pour établir une connexion au service QnA Maker : `self.qna_maker`.

**bots/qna_bot.py** [!code-python[QnAMaker](~/../botbuilder-python/samples/python/11.qnamaker/bots/qna_bot.py?range=13-19)]

---

## <a name="calling-qna-maker-from-your-bot"></a>Appel de QnA Maker à partir de votre bot

## <a name="ctabcs"></a>[C#](#tab/cs)

Lorsque votre bot a besoin d’une réponse de QnA Maker, appelez `GetAnswersAsync()` à partir du code de votre bot pour obtenir la réponse appropriée en fonction du contexte actuel. Si vous accédez à votre propre base de connaissances, changez le message _no answers found_ (aucune réponse trouvée) ci-dessous pour fournir des instructions utiles à vos utilisateurs.

**Bots/QnABot.cs**

[!code-csharp[qna get answers](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=43-52)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Dans le fichier **QnABot.js**, nous passons l’entrée de l’utilisateur dans la méthode `getAnswers` du service QnA Maker pour obtenir des réponses à partir de la base de connaissances. Si QnA Maker retourne une réponse, elle est présentée à l’utilisateur. Sinon, l’utilisateur reçoit le message « No QnA Maker answers were found » (« Aucune réponse QnA Maker n’a été trouvée »).

**bots/QnABot.js**

[!code-javascript[OnMessage](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=46-55)]

## <a name="pythontabpython"></a>[Python](#tab/python)

Dans le fichier **qna_bot.py**, nous passons l’entrée de l’utilisateur à la méthode `get_answers` du service QnA Maker pour obtenir des réponses provenant de la base de connaissances. Si QnA Maker retourne une réponse, elle est présentée à l’utilisateur. Sinon, l’utilisateur reçoit le message *No QnA Maker answers were found* (Réponses QnA Maker introuvables).

**bots/qna_bot.py** [!code-python[get_answers](~/../botbuilder-python/samples/python/11.qnamaker/bots/qna_bot.py?range=33-37)]

---

## <a name="test-the-bot"></a>Tester le bot

Exécutez l’exemple en local sur votre machine. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download). Pour d’autres instructions, consultez le fichier readme (lisez-moi) de l’[exemple C#](https://aka.ms/cs-qna) ou de l’[exemple Javascript](https://aka.ms/js-qna-sample).
Vous pouvez également consulter l’[exemple en Python](https://aka.ms/bot-qna-python-sample-code). 

Démarrez l’émulateur, connectez-vous à votre bot et envoyez un message, comme indiqué ci-dessous.

![exemple de qna test](../media/emulator-v4/qna-test-bot.png)

## <a name="additional-information"></a>Informations supplémentaires

### <a name="multi-turn-prompts"></a>Invites multitours

QnA Maker prend en charge les invites de suivi, aussi appelées invites multitours.
Si la base de connaissances QnA Maker nécessite une réponse supplémentaire de part de l’utilisateur, QnA Maker envoie des informations de contexte que vous pouvez utiliser pour inviter l’utilisateur. Ces informations servent aussi à passer des appels de suivi au service QnA Maker.
Dans la version 4.6, le SDK Bot Framework a ajouté la prise en charge de cette fonctionnalité.

Pour construire une base de connaissances de ce type, consultez la documentation QnA Maker pour savoir comment [Utiliser des invites de suivi pour créer plusieurs tours d’une conversation](https://aka.ms/qnamaker-multiturn-conversation). <!--To learn how to incorporate multi-turn support in your bot, take a look at the QnA Maker Multi-turn [[**C#**](https://aka.ms/cs-qna-multiturn) | [**JS**](https://aka.ms/js-qna-multiturn)] sample.-->

<!--TODO: Update code based on final sample 
The following code snippets come from the proof-of-concept **multi-turn QnA Maker prompts** sample for
[**C#**](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting/csharp_dotnetcore) and
[**JavaScript**](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting/javascript_nodejs).
-->
<!--
#### Sample code

This sample uses a custom QnA dialog to track state for QnA Maker and handle the user's input and QnA Maker's response. When the user sends a message to the bot, the bot treats the input as either an initial query or a response to a follow-up question. The bot starts or continues its QnA dialog, which tracks the QnA Maker context information.

1. When the dialog **starts**, it makes an initial call to the QnA Maker service. QnA Maker context information **is not** included in the call.
1. When the dialog **continues**, it makes a follow-up call to the QnA Maker service. QnA Maker context information included **is** included in the call.
1. In either case, the dialog evaluates the query results.
   - Each QnA Maker result includes either an answer or a follow-up question to the user's initial query. (This sample uses only the first result.)
   - If the result includes follow-up prompts, the dialog prompts the user, saves the context information, and stays on the dialog stack, waiting for additional information from the user.
   - Otherwise, the result represents an answer, the dialog sends the answer and ends.

This sample implements this across two dialog classes:

- The base _functional dialog_ defines the begin, continue, and state logic for the dialog, notably, the _run state machine_ method.
- The derived _QnA dialog_ defines the logic to call QnA Maker, evaluate its response, and send an answer or follow-up question to the user.

#### QnA Maker context

You need to track context information for the QnA Maker service.
The dialog funnels incoming activities through its _run state machine_ method, which:

1. Gets previous QnA Maker context, if any, from state.
1. Calls its _process_ method to call QnA Maker and generates a response for the user.
1. If the result was a follow-up question, sends the question, saves new QnA Maker context to state, and waits for more input on the next turn.
1. If the result was an answer, sends the answer and ends the dialog.

##### [C#](#tab/csharp)

**Dialogs\FunctionDialogBase.cs** defines the **RunStateMachineAsync** method.

```csharp
private async Task<DialogTurnResult> RunStateMachineAsync(DialogContext dialogContext, CancellationToken cancellationToken)
{
     // Get the Process function's current state from the dialog state
     var oldState = GetPersistedState(dialogContext.ActiveDialog);

     // Run the Process function.
     var (newState, output, result) = await ProcessAsync(oldState, dialogContext.Context.Activity).ConfigureAwait(false);

     // If we have output to send then send it.
     foreach (var activity in output)
     {
          await dialogContext.Context.SendActivityAsync(activity).ConfigureAwait(false);
     }

     // If we have new state then we must still be running.
     if (newState != null)
     {
          // Add the state returned from the Process function to the dialog state.
          dialogContext.ActiveDialog.State[FunctionStateName] = newState;

          // Return Waiting indicating this dialog is still in progress.
          return new DialogTurnResult(DialogTurnStatus.Waiting);
     }
     else
     {
          // The Process function indicates it's completed by returning null for the state.
          return await dialogContext.EndDialogAsync(result).ConfigureAwait(false);
     }
}
```

##### [JavaScript](#tab/javascript)

**dialogs/functionDialogBase.js** defines the **runStateMachine** method.

```javascript
async runStateMachine(dc) {

     var oldState = this.getPersistedState(dc.activeDialog);

     var processResult = await this.processAsync(oldState, dc.context.activity);

     var newState = processResult[0];
     var output = processResult[1];
     var result = processResult[2];

     await dc.context.sendActivity(output);

     if(newState != null){
          dc.activeDialog.state[functionStateName] = newState;
          return { status: DialogTurnStatus.waiting };
     }
     else{
          return await dc.endDialog();
     }
}
```

---

#### QnA Maker input and response

You need to provide any previous context information when you call the QnA Maker service.
The dialog handles the call to QnA Maker in its _process_ method, which:

1. Makes the call to QnA Maker, passing in the previous context, if any.
   - This sample uses a _query QnA service_ helper method to format the parameters and make the call.
   - Importantly, if this is a follow-up call to QnA Maker, the _QnA Maker options_ should include values for the _QnA request context_ and the _QnA question ID_.
1. Gets QnA Maker's response and any follow-up prompts from the top result.
1. If the result was a follow-up question:
   - Generates a hero card that contains the question and options for the user's response.
   - Includes the hero card in a message activity.
   - Returns the message activity and the new QnA Maker context.
1. If the result was an answer, returns a message activity that contains the answer.

You can format a follow-up activity in many ways. This sample uses a hero card. However, using suggested actions would be another option.

##### [C#](#tab/csharp)

**Dialogs\QnADialog.cs** defines the **RunStateMachineAsync** method.

```csharp
protected override async Task<(object newState, IEnumerable<Activity> output, object result)> ProcessAsync(object oldState, Activity inputActivity)
{
     Activity outputActivity = null;
     QnABotState newState = null;

     var query = inputActivity.Text;
     var qnaResult = await _qnaService.QueryQnAServiceAsync(query, (QnABotState)oldState);
     var qnaAnswer = qnaResult[0].Answer;
     var prompts = qnaResult[0].Context?.Prompts;

     if (prompts == null || prompts.Length < 1)
     {
          outputActivity = MessageFactory.Text(qnaAnswer);
     }
     else
     {
          // Set bot state only if prompts are found in QnA result
          newState = new QnABotState
          {
          PreviousQnaId = qnaResult[0].Id,
          PreviousUserQuery = query
          };

          outputActivity = CardHelper.GetHeroCard(qnaAnswer, prompts);
     }

     return (newState, new Activity[] { outputActivity }, null);
}
```

##### [JavaScript](#tab/javascript)

**dialogs/qnaDialog.js** defines the **runStateMachine** method.

```javascript
async processAsync(oldState, activity){

     var newState = null;
     var query = activity.text;
     var qnaResult = await QnAServiceHelper.queryQnAService(query, oldState);
     var qnaAnswer = qnaResult[0].answer;

     var prompts = null;
     if(qnaResult[0].context != null){
          prompts = qnaResult[0].context.prompts;
     }

     var outputActivity = null;
     if(prompts == null || prompts.length < 1){
          outputActivity = MessageFactory.text(qnaAnswer);
     }
     else{
          var newState = {
               PreviousQnaId: qnaResult[0].id,
               PreviousUserQuery: query
          }

          outputActivity = CardHelper.GetHeroCard(qnaAnswer, prompts);
     }

     return [newState, outputActivity , null];
}  
```

---
-->
## <a name="next-steps"></a>Étapes suivantes

Vous pouvez combiner QnA Maker à d’autres services Cognitive Services pour rendre votre bot encore plus puissant. L’outil Dispatch offre un moyen de combiner QnA avec Language Understanding (LUIS) dans votre bot.

> [!div class="nextstepaction"]
> [Combiner des services QnA et LUIS à l’aide de l’outil Dispatch](./bot-builder-tutorial-dispatch.md)
