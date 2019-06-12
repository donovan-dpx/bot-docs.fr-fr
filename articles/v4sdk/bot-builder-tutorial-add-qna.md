---
title: Tutoriel Azure Bot Service pour que votre bot réponde aux questions | Microsoft Docs
description: Tutoriel sur l’utilisation de QnA Maker pour que votre bot réponde à des questions.
keywords: QnA Maker, question et réponse, base de connaissances
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: tutorial
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5c8b164aa97ff4ea74acbd6765c72ea0c3f1ad3b
ms.sourcegitcommit: e276008fb5dd7a37554e202ba5c37948954301f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693648"
---
# <a name="tutorial-use-qna-maker-in-your-bot-to-answer-questions"></a>Didacticiel : Utiliser QnA Maker dans votre bot pour répondre à des questions

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Vous pouvez utiliser le service QnA Maker et une base de connaissances pour ajouter une prise en charge des questions et réponses à votre bot. Quand vous créez une base de connaissances, vous l’amorcez avec des questions et réponses.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un service QnA Maker et une base de connaissances
> * Ajouter des informations de la base de connaissances à votre fichier de configuration
> * Mettre à jour votre votre bot pour qu’il interroge la base de connaissances
> * Republier votre bot

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Le bot créé dans le [tutoriel précédent](bot-builder-tutorial-basic-deploy.md). Nous allons ajouter une fonctionnalité de questions et réponses au bot.
* Il est utile de connaître les bases de [QnA Maker](https://qnamaker.ai/). Nous allons créer, entraîner et publier la base de connaissances à utiliser avec le bot via le portail QnA Maker.
* Vous êtes familiarisé avec la [création de bot QnA](https://aka.ms/azure-create-qna) à l’aide d’Azure Bot Service.

Par ailleurs, les prérequis du tutoriel précédent doivent déjà être en place.

## <a name="sign-in-to-qna-maker-portal"></a>Se connecter au portail QnA Maker

<!-- This and the next step are close duplicates of what's in the QnA How-To -->

Connectez-vous au [portail QnA Maker](https://qnamaker.ai/) avec vos informations d’identification Azure.

## <a name="create-a-qna-maker-service-and-knowledge-base"></a>Créer un service QnA Maker et une base de connaissances

Nous allons importer une définition de base de connaissances existante de l’exemple QnA Maker dans le dépôt [BotBuilder/Microsoft-Samples](https://github.com/Microsoft/BotBuilder-Samples).

1. Clonez ou copiez le dépôt d’exemples sur votre ordinateur.
1. Sur le portail QnA Maker, sélectionnez **create a knowledge base** (Créer une base de connaissances).
   1. Si nécessaire, créez un service QnA. (Vous pouvez utiliser un service QnA Maker existant ou en créer un pour ce tutoriel.) Pour des instructions plus détaillées sur QnA Maker, consultez [Créer un service QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure) et [Créer, entraîner et publier votre base de connaissances QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base).
   1. Connectez votre service QnA à votre base de connaissances.
   1. Nommez votre base de connaissances.
   1. Pour remplir votre base de connaissances, utilisez le fichier **BotBuilder-Samples\samples\csharp_dotnetcore\11.qnamaker\CognitiveModels\smartLightFAQ.tsv** à partir du dépôt d’exemples.
   1. Cliquez sur **Create your KB** (Créer votre base de connaissances) pour créer la base de connaissances.
1. **Enregistrez et entraînez** votre base de connaissances.
1. **Publiez** votre base de connaissances.

Une fois que votre application QnA Maker est publiée, sélectionnez l’onglet _SETTINGS_ et faites défiler jusqu’à « Deployment details ». Enregistrez les valeurs suivantes à partir de la requête HTTP de l’exemple _Postman_.

```text
POST /knowledgebases/<knowledge-base-id>/generateAnswer
Host: <your-hostname>  // NOTE - this is a URL ending in /qnamaker.
Authorization: EndpointKey <qna-maker-resource-key>
```

La chaîne d’URL complète du nom d’hôte doit ressembler à « https://< >.azure.net/qnamaker ».

Ces valeurs seront utilisées dans votre fichier `appsettings.json` ou `.env` à l’étape suivante.

La base de connaissances est maintenant prête à être utilisée par votre bot.

## <a name="add-knowledge-base-information-to-your-bot"></a>Ajouter les informations de la base de connaissances à votre bot
À compter de Bot Framework 4.3, Azure ne fournit plus de fichier .bot dans le code source de votre bot téléchargé. Utilisez les instructions suivantes pour connecter votre bot CSharp ou JavaScript à votre base de connaissances.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ajoutez les valeurs suivantes à votre fichier appsetting.json :

```json
{
   "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  "ScmType": "None",
  
  "QnAKnowledgebaseId": "<knowledge-base-id>",
  "QnAAuthKey": "<qna-maker-resource-key>",
  "QnAEndpointHostName": "<your-hostname>" // This is a URL ending in /qnamaker
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez les valeurs suivantes à votre fichier .env :

```
MicrosoftAppId=""
MicrosoftAppPassword=""
ScmType=None

QnAKnowledgebaseId="<knowledge-base-id>"
QnAAuthKey="<qna-maker-resource-key>"
QnAEndpointHostName="<your-hostname>" // This is a URL ending in /qnamaker
```

---

| Champ | Valeur |
|:----|:----|
| QnAKnowledgebaseId | ID de base de connaissances que le portail QnA Maker a généré automatiquement. |
| QnAAuthKey | Clé du point de terminaison que le portail QnA Maker a générée automatiquement. |
| QnAEndpointHostName | URL d’hôte généré par le portail QnA Maker. Utilisez l’URL complète, avec `https://` au début et `/qnamaker` à la fin. La chaîne d’URL complète doit ressembler à « https://< >.azure.net/qnamaker ». |

Enregistrez vos modifications.

## <a name="update-your-bot-to-query-the-knowledge-base"></a>Mettre à jour votre votre bot pour qu’il interroge la base de connaissances

Mettez à jour votre code d’initialisation pour charger les informations du service pour votre base de connaissances.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

1. Ajoutez le package NuGet **Microsoft.Bot.Builder.AI.QnA** à votre projet.
1. Ajoutez le package NuGet **Microsoft.Extensions.Configuration** à votre projet.
1. Dans votre fichier **Startup.cs**, ajoutez ces références d’espace de noms.

   **Startup.cs**
   ```csharp
   using Microsoft.Bot.Builder.AI.QnA;
   using Microsoft.Extensions.Configuration;
   ```
1. Modifiez la méthode _ConfigureServices_ de manière à créer un QnAMakerEndpoint qui se connecte à la base de connaissances définie dans le fichier **appsettings.json**.

   **Startup.cs**
   ```csharp
   // Create QnAMaker endpoint as a singleton
   services.AddSingleton(new QnAMakerEndpoint
   {
      KnowledgeBaseId = Configuration.GetValue<string>($"QnAKnowledgebaseId"),
      EndpointKey = Configuration.GetValue<string>($"QnAAuthKey"),
      Host = Configuration.GetValue<string>($"QnAEndpointHostName")
    });

   ```
1. Dans votre fichier **EchoBot.cs**, ajoutez ces références d’espace de noms.

   **EchoBot.cs**
   ```csharp
   using System.Linq;
   using Microsoft.Bot.Builder.AI.QnA;
   ```

1. Ajoutez un connecteur `EchoBotQnA` et initialisez-la dans le constructeur du bot.

   **EchoBot.cs**
   ```csharp
   public QnAMaker EchoBotQnA { get; private set; }
   public EchoBot(QnAMakerEndpoint endpoint)
   {
      // connects to QnA Maker endpoint for each turn
      EchoBotQnA = new QnAMaker(endpoint);
   }
   ```
1. La méthode _OnMembersAddedAsync( )_ ci-dessous crée la méthode _AccessQnAMaker( )_ en ajoutant le code suivant :

   **EchoBot.cs**
   ```csharp
   private async Task AccessQnAMaker(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      var results = await EchoBotQnA.GetAnswersAsync(turnContext);
      if (results.Any())
      {
         await turnContext.SendActivityAsync(MessageFactory.Text("QnA Maker Returned: " + results.First().Answer), cancellationToken);
      }
      else
      {
         await turnContext.SendActivityAsync(MessageFactory.Text("Sorry, could not find an answer in the Q and A system."), cancellationToken);
      }
   }
   ```
1. Dans _OnMessageActivityAsync( )_ , appelez votre nouvelle méthode _AccessQnAMaker( )_ comme suit :

   **EchoBot.cs**
   ```csharp
   protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      // First send the user input to your QnA Maker knowledgebase
      await AccessQnAMaker(turnContext, cancellationToken);
      ...
   }
   ```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Ouvrez un terminal ou une invite de commandes dans le répertoire racine de votre projet.
1. Ajoutez le package npm **botbuilder-ai** à votre projet.
   ```shell
   npm i botbuilder-ai
   ```

1. Dans **index.js**, après la section // Create Adapter, ajoutez le code suivant pour lire les informations de configuration de votre fichier .env afin de générer les services QnA Maker.

   **index.js**
   ```javascript
   // Map knowledgebase endpoint values from .env file into the required format for `QnAMaker`.
   const configuration = {
      knowledgeBaseId: process.env.QnAKnowledgebaseId,
      endpointKey: process.env.QnAAuthKey,
      host: process.env.QnAEndpointHostName
   };

   ```

1. Mettez à jour la construction du bot à passer dans les informations de configuration des services QnA.

   **index.js**
   ```javascript
   // Create the main dialog.
   const myBot = new MyBot(configuration, {});
   ```

1. Dans votre fichier **bot.js**, ajoutez ce require pour QnAMaker.

   **bot.js**
   ```javascript
   const { QnAMaker } = require('botbuilder-ai');
   ```

1. Modifiez le constructeur de manière à ce qu’il reçoive les paramètres de configuration passés nécessaires pour créer un connecteur QnAMaker et lever une erreur si ces paramètres ne sont pas fournis.

   **bot.js**
   ```javascript
      class MyBot extends ActivityHandler {
         constructor(configuration, qnaOptions) {
            super();
            if (!configuration) throw new Error('[QnaMakerBot]: Missing parameter. configuration is required');
            // now create a qnaMaker connector.
            this.qnaMaker = new QnAMaker(configuration, qnaOptions);
   ```

1. Enfin, pour obtenir une réponse de vos bases de connaissances, ajoutez le code suivant à votre appel onMessage() qui passe chaque entrée d’utilisateur à votre base de connaissances QnA Maker et retourne la réponse de QnA Maker à l’utilisateur.
 
    **bot.js**
    ```javascript
   // send user input to QnA Maker.
   const qnaResults = await this.qnaMaker.getAnswers(turnContext);

   // If an answer was received from QnA Maker, send the answer back to the user.
   if (qnaResults[0]) {
      await turnContext.sendActivity(`QnAMaker returned response: ' ${ qnaResults[0].answer}`);
   } 
   else { 
      // If no answers were returned from QnA Maker, reply with help.
      await turnContext.sendActivity('No QnA Maker response was returned.'
           + 'This example uses a QnA Maker Knowledge Base that focuses on smart light bulbs. '
           + `Ask the bot questions like "Why won't it turn on?" or "I need help."`);
   }
   ```
---

### <a name="test-the-bot-locally"></a>Testez le bot localement

À ce stade, votre bot doit pouvoir répondre à certaines questions. Exécutez le bot localement et ouvrez-le dans l’émulateur.

![exemple de qna test](./media/qna-test-bot.png)

## <a name="republish-your-bot"></a>Republier votre bot

Nous pouvons maintenant republier votre bot sur Azure.

> [!IMPORTANT]
> Avant de créer un fichier zip de vos fichiers projet, vérifiez que vous êtes bien _dans_ le dossier approprié. 
> - Pour les bots C#, il s’agit du dossier contenant le fichier .csproj. 
> - Pour les bots JS, il s’agit du dossier contenant le fichier app.js ou index.js. 
>
> Sélectionnez tous les fichiers et compressez-les dans ce dossier, puis exécutez la commande toujours dans ce dossier.
>
> Si l’emplacement de votre dossier racine est incorrect, l’**exécution du bot échouera dans le portail Azure**.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)
```cmd
az webapp deployment source config-zip --resource-group <resource-group-name> --name <bot-name-in-azure> --src "c:\bot\mybot.zip"
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [publish snippet](~/includes/deploy/snippet-publish-js.md)]

---

### <a name="test-the-published-bot"></a>Tester le bot publié

Après avoir publié le bot, patientez une à deux minutes, le temps qu’Azure mette à jour le bot et le démarre.

Servez-vous de l’émulateur pour tester le point de terminaison de production pour votre bot ou utilisez le portail Azure pour tester le bot dans Web Chat.
Dans les deux cas, vous devez observer le même comportement qu’au moment du test local.

## <a name="clean-up-resources"></a>Supprimer des ressources

<!-- In the first tutorial, we should tell them to use a new resource group, so that it is easy to clean up resources. We should also mention in this step in the first tutorial not to clean up resources if they are continuing with the sequence. -->

Si vous ne pensez pas continuer à utiliser cette application, supprimez les ressources associées en effectuant les étapes suivantes :

1. Sur le portail Azure, ouvrez le groupe de ressources de votre bot.
1. Cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe et toutes les ressources qu’il contient.
1. Dans le volet de confirmation, entrez le nom du groupe de ressources, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment ajouter des fonctionnalités à votre bot, consultez l’article **Envoyer et recevoir des SMS** et les autres articles figurant dans la section Développement, sous Procédure.
> [!div class="nextstepaction"]
> [Envoyer et recevoir des SMS](bot-builder-howto-send-messages.md)
