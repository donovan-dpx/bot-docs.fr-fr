---
title: Utiliser QnA Maker pour répondre aux questions | Microsoft Docs
description: Découvrez comment utiliser QnA Maker dans votre bot.
keywords: questions et réponses, QnA, FAQ, QnA Maker
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: cognitive-services
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 15581daa570b9e51ff8f7bec93d16deebcd71d45
ms.sourcegitcommit: 93508adfb79523f610a919b361fc34f5c8dd3eff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67533380"
---
# <a name="use-qna-maker-to-answer-questions"></a>Utiliser QnA Maker pour répondre aux questions

[!INCLUDE[applies-to](../includes/applies-to.md)]

QnA Maker fournit une couche de questions et réponses conversationnelle sur vos données. Votre bot peut ainsi envoyer une question à QnA Maker et recevoir une réponse sans que vous ayez besoin d’analyser et d’interpréter l’intention de la question. 

Une des exigences de base de la création de votre propre service QnA Maker est à l’amorçage des questions et des réponses. Dans de nombreux cas, les questions et réponses existent déjà dans du contenu comme un Forum aux questions ou d’autres documents ; parfois, vous souhaiterez personnaliser vos réponses aux questions de manière plus naturelle et conversationnelle. 

## <a name="prerequisites"></a>Prérequis

- Le code de cet article est basé sur l’exemple QnA Maker. Vous aurez besoin d’une copie de ce dernier en **[CSharp](https://aka.ms/cs-qna) ou [JavaScript](https://aka.ms/js-qna-sample)** .
- Compte [QnA Maker](https://www.qnamaker.ai/)
- Connaissances des [concepts de base des bots](bot-builder-basics.md), de [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/overview/overview) et de la [gestion des ressources de bot](bot-file-basics.md).

## <a name="about-this-sample"></a>À propos de cet exemple

Pour que votre bot utilise QnA Maker, vous devez d’abord créer une base de connaissances sur [QnA Maker](https://www.qnamaker.ai/), opération que nous aborderons dans la section suivante. Votre bot peut ensuite lui envoyer la requête d’un utilisateur et obtenir en retour la meilleure réponse.

## <a name="ctabcs"></a>[C#](#tab/cs)
![Flux de la logique QnABot](./media/qnabot-logic-flow.png)

`OnMessageActivityAsync` est appelé pour chaque entrée d’utilisateur reçue. Quand il est appelé, il accède à des informations `_configuration` stockées dans le fichier `appsetting.json` de l’exemple de code pour rechercher la valeur permettant la connexion à votre base de connaissances QnA Maker préconfigurée. 

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)
![Flux de la logique JS QnABot](./media/qnabot-js-logic-flow.png)

`OnMessage` est appelé pour chaque entrée d’utilisateur reçue. Quand il est appelé, il accède à votre connecteur `qnamaker` qui a été préconfiguré à l’aide des valeurs fournies à partir du fichier `.env` de votre exemple de code.  La méthode `getAnswers` qnamaker connecte votre bot à votre base de connaissances QnA Maker externe.

---
L’entrée de l’utilisateur est envoyée à votre base de connaissances, puis la meilleure réponse retournée est présentée à l’utilisateur.

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

```json
{
  "MicrosoftAppId": "",
  "MicrosoftAppPassword": "",
  
  "QnAKnowledgebaseId": "<knowledge-base-id>",
  "QnAAuthKey": "<your-endpoint-key>",
  "QnAEndpointHostName": "<your-hostname>"
}
```

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="update-your-env-file"></a>Mettre à jour votre fichier .env

```file
MicrosoftAppId=""
MicrosoftAppPassword=""

QnAKnowledgebaseId="<knowledge-base-id>"
QnAAuthKey="<your-endpoint-key>"
QnAEndpointHostName="<your-hostname>"
```

---

## <a name="set-up-the-qna-maker-instance"></a>Configurer l’instance de QnA Maker

Tout d’abord, nous créons un objet pour accéder à notre base de connaissances QnA Maker.

## <a name="ctabcs"></a>[C#](#tab/cs)

Vérifiez que le package NuGet **Microsoft.Bot.Builder.AI.QnA** est installé pour votre projet.

Dans **QnABot.cs**, dans la méthode `OnMessageActivityAsync`, nous créons une instance de QnAMaker. La classe `QnABot` est également l’endroit où sont insérés les noms des informations de connexion, enregistrées dans `appsettings.json` ci-dessus. Si vous avez choisi des noms différents pour vos informations de connexion à la base de connaissances dans votre fichier de paramètres, veillez à mettre à jour les noms ici afin qu’ils reflètent le nom choisi.

**Bots/QnABot.cs**  
[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=32-37)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Veillez à ce que le package npm **botbuilder-ai** soit installé pour votre projet.

Dans notre exemple, le code de la logique de bot se trouve dans un fichier **QnABot.js**.

Dans le fichier **QnABot.js**, nous utilisons les informations de connexion fournies par votre fichier .env pour établir une connexion au service QnA Maker : _this.qnaMaker_.

**QnAMaker.js** [!code-javascript[QnAMaker](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=19-23)]


---

## <a name="calling-qna-maker-from-your-bot"></a>Appel de QnA Maker à partir de votre bot

## <a name="ctabcs"></a>[C#](#tab/cs)

Lorsque votre bot a besoin d’une réponse de QnA Maker, appelez `GetAnswersAsync()` à partir du code de votre bot pour obtenir la réponse appropriée en fonction du contexte actuel. Si vous accédez à votre propre base de connaissances, changez le message _no answers found_ (aucune réponse trouvée) ci-dessous pour fournir des instructions utiles à vos utilisateurs.

**QnABot.cs**  
[!code-csharp[qna connection](~/../botbuilder-samples/samples/csharp_dotnetcore/11.qnamaker/Bots/QnABot.cs?range=43-52)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Dans le fichier **QnABot.js**, nous passons l’entrée de l’utilisateur dans la méthode `getAnswers` du service QnA Maker pour obtenir des réponses à partir de la base de connaissances. Si QnA Maker retourne une réponse, elle est présentée à l’utilisateur. Sinon, l’utilisateur reçoit le message « No QnA Maker answers were found » (« Aucune réponse QnA Maker n’a été trouvée »). 

**QnABot.js** [!code-javascript[OnMessage](~/../botbuilder-samples/samples/javascript_nodejs/11.qnamaker/bots/QnABot.js?range=43-59)]

---

## <a name="test-the-bot"></a>Tester le bot

Exécutez l’exemple en local sur votre machine. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md#download). Pour d’autres instructions, consultez le fichier readme (lisez-moi) de l’[exemple C#](https://aka.ms/cs-qna) ou de l’[exemple Javascript](https://aka.ms/js-qna-sample).

Démarrez l’émulateur, connectez-vous à votre bot et envoyez un message, comme indiqué ci-dessous.

![exemple de qna test](../media/emulator-v4/qna-test-bot.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez combiner QnA Maker à d’autres services Cognitive Services pour rendre votre bot encore plus puissant. L’outil Dispatch offre un moyen de combiner QnA avec Language Understanding (LUIS) dans votre bot.

> [!div class="nextstepaction"]
> [Combiner des services QnA et LUIS à l’aide de l’outil Dispatch](./bot-builder-tutorial-dispatch.md)
