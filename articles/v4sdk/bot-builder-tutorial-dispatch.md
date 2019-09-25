---
title: Utiliser plusieurs modèles LUIS et QnA | Microsoft Docs
description: Découvrez comment utiliser LUIS et QnA Maker dans votre bot.
keywords: LUIS, QnA, outil Dispatch, services multiples, intentions de route
author: diberry
ms.author: diberry
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: df7c608b32b2b570c50eb9a045d965adabd96881
ms.sourcegitcommit: b869b6c325017df22158f4575576fb63c8ded488
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224787"
---
# <a name="use-multiple-luis-and-qna-models"></a>Utiliser plusieurs modèles LUIS et QnA

[!INCLUDE[applies-to](../includes/applies-to.md)]

Si un bot utilise plusieurs modèles LUIS et bases de connaissances QnA Maker, vous pouvez utiliser l’outil Disptach pour déterminer quel modèle LUIS ou quelle base de connaissances QnA Maker correspond le mieux à l’entrée utilisateur. L’outil Dispatch peut le déterminer en créant une application LUIS unique pour router l’entrée utilisateur vers le modèle correct. Pour plus d’informations sur l’outil Disptach, notamment les commandes CLI, reportez-vous au fichier [LISEZMOI][dispatch-readme].

## <a name="prerequisites"></a>Prérequis
- Connaissances des [concepts de base des bots](bot-builder-basics.md), de [LUIS][howto-luis] et de [QnA Maker][howto-qna]. 
- [Outil Répartition](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
- Une copie du **NLP avec Dispatch** issue du dépôt de code de l’[exemple C#][cs-sample] ou de l’[exemple JS][js-sample].
- Un compte [luis.ai](https://www.luis.ai/) pour publier des applications LUIS.
- Un compte [QnA Maker](https://www.qnamaker.ai/) pour publier la base de connaissances QnA Maker.

## <a name="about-this-sample"></a>À propos de cet exemple

Cet exemple est basé sur un ensemble prédéfini d’applications LUIS et QnA Maker.

## <a name="ctabcs"></a>[C#](#tab/cs)

![Flux de la logique de l’exemple de code](./media/tutorial-dispatch/dispatch-logic-flow.png)

`OnMessageActivityAsync` est appelé pour chaque entrée d’utilisateur reçue. Ce module recherche l’intention utilisateur du scoring le plus élevé et passe ce résultat à `DispatchToTopIntentAsync`. DispatchToTopIntentAsync, à son tour, appelle le gestionnaire d’applications approprié.

- `ProcessSampleQnAAsync` : pour les questions fréquentes sur les bots.
- `ProcessWeatherAsync` : pour les requêtes météo.
- `ProcessHomeAutomationAsync` : pour les commandes d’éclairage domestique.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

![Flux de la logique de l’exemple de code](./media/tutorial-dispatch/dispatch-logic-flow-js.png)

`onMessage` est appelé pour chaque entrée d’utilisateur reçue. Ce module recherche l’intention utilisateur du scoring le plus élevé et passe ce résultat à `dispatchToTopIntentAsync`. DispatchToTopIntentAsync, à son tour, appelle le gestionnaire d’applications approprié.

- `processSampleQnA` : pour les questions fréquentes sur les bots.
- `processWeather` : pour les requêtes météo.
- `processHomeAutomation` : pour les commandes d’éclairage domestique.

---

Le gestionnaire appelle le service LUIS ou QnA Maker, puis retourne le résultat généré à l’utilisateur.

## <a name="create-luis-apps-and-qna-knowledge-base"></a>Créer des applications LUIS et une base de connaissances QnA
Pour pouvoir créer le modèle de dispatch, vous avez besoin de créer et publier vos applications LUIS et bases de connaissances QnA. Dans cet article, nous allons publier les modèles suivants inclus dans l’exemple _NLP avec Dispatch_ dans le dossier `\CognitiveModels` : 

| Nom | Description |
|------|------|
| HomeAutomation | Application LUIS qui reconnaît une intention domotique avec les données d’entité associées.|
| Météo | Application LUIS qui reconnaît les intentions liées à la météo avec des données de localisation.|
| QnAMaker  | Base de connaissances QnA Maker qui fournit des réponses à certaines questions simples concernant le bot. |

### <a name="create-luis-apps"></a>Créer des applications LUIS
1. Connectez-vous au [portail web de LUIS](https://www.luis.ai/). Dans la section _My apps_, sélectionnez l’onglet _Import new app_. La boîte de dialogue suivante s’affiche :

    ![Importer un fichier json LUIS](./media/tutorial-dispatch/import-new-luis-app.png)

2. Sélectionnez le bouton _Choisir un fichier d’application_, accédez au dossier CognitiveModel de votre exemple de code et sélectionnez le fichier « HomeAutomation.json ». Laissez le champ de nom facultatif vide. 

3. Sélectionnez _Terminé_.

4. Une fois que LUIS ouvre votre application Home Automation, sélectionnez le bouton _Train_. Cela vous permet d’entraîner votre application avec l’ensemble des énoncés que vous venez d’importer à l’aide du fichier « home-automation.json ».

5. Une fois l’entraînement terminé, sélectionnez le bouton _Publish_. La boîte de dialogue suivante s’affiche :

    ![Publier l’application LUIS](./media/tutorial-dispatch/publish-luis-app.png)

6. Choisissez l’environnement « production » et sélectionnez le bouton _Publish_.

7. Une fois que votre nouvelle application LUIS a été publiée, sélectionnez l’onglet _MANAGE_. Depuis la page Informations sur l’application, enregistrez les valeurs `Application ID` sous _app-id-for-app_ et `Display name` sous _name-of-app_. Dans la page Clé et points de terminaison, enregistrez les valeurs `Authoring Key` sous _your-luis-authoring-key_ et `Region` sous _your-region_. Ces valeurs seront utilisées ultérieurement dans votre fichier appsetting.json.

8. Une fois que vous avez terminé, _entraînez_ et _publiez_ votre application de **météo** LUIS et votre application de **dispatch** LUIS en répétant ces mêmes étapes pour le fichier Weather.json.

### <a name="create-qna-maker-knowledge-base"></a>Créer une base de connaissances QnA Maker

La première étape pour configurer une base de connaissances QnA Maker est de configurer un service QnA Maker dans Azure. Pour ce faire, suivez les instructions détaillées [ici](https://aka.ms/create-qna-maker).

Une fois votre service QnA Maker créé dans Azure, vous devez enregistrer la clé _Clé 1_ Cognitive Services fournie pour celui-ci. Elle sera utilisée comme \<clé1-service-qna-azure> lors de l’ajout de l’application QnA Maker à votre application de dispatch. 

Découvrez plus en détail les [deux différents types de clés](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure#types-of-keys-in-qna-maker) utilisés avec QnA Maker.

Les étapes suivantes vous fournissent cette clé :
    
![Sélectionner Cognitive Services](./media/tutorial-dispatch/select-qna-cognitive-service.png)

1. À partir du portail Azure, sélectionnez votre service cognitif QnA Maker.

    ![Sélectionner des clés Cognitive Services](./media/tutorial-dispatch/select-cognitive-service-keys.png)

1. Sélectionnez l’icône de clés qui figure sous la section _Gestion des ressources_ dans le menu de gauche.

    ![Sélectionner la clé Clé 1 Cognitive Services](./media/tutorial-dispatch/select-cognitive-service-key1.png)

1. Copiez la valeur de _Clé 1_ dans le Presse-papiers et enregistrez-la en local. Elle sera utilisée ultérieurement pour la valeur de clé (-k) \<clé1-service-qna-azure> lors de l’ajout de l’application QnA Maker à votre application de dispatch.

1. Connectez-vous maintenant au [portail web de QnAMaker](https://qnamaker.ai). 

1. À l’étape 2, sélectionnez les éléments suivants :

    * Votre compte Azure AD.
    * Le nom de votre abonnement Azure.
    * Le nom que vous avez créé pour votre service QnA Maker. (Si votre service Azure QnA n’apparaît pas dans cette liste déroulante, essayez d’actualiser la page.)

    ![Créer QnA - Étape 2](./media/tutorial-dispatch/create-qna-step-2.png) 
     

1. À l’étape 3, fournissez un nom pour votre base de connaissances QnA Maker. Pour cet exemple, utilisez le nom sample-qna.

    ![Créer QnA - Étape 3](./media/tutorial-dispatch/create-qna-step-3.png)

1. À l’étape 4, sélectionnez l’option _+ Ajouter un fichier_, accédez au dossier CognitiveModel de votre exemple de code et sélectionnez le fichier « QnAMaker.tsv ». Il existe une sélection supplémentaire pour ajouter une personnalité _Chit-chat_ à votre base de connaissances, mais notre exemple n’inclut pas cette option.

    ![Créer QnA - Étape 4](./media/tutorial-dispatch/create-qna-step-4.png)

1. À l’étape 5, _créez votre base de connaissances_.

1. Une fois qu’une base de connaissances a été créée à partir de votre fichier chargé, sélectionnez _Save and train_ (Enregistrer et entraîner) et quand vous avez terminé, sélectionnez l’onglet _PUBLISH_ et publiez votre application.

1. Une fois que votre application QnA Maker est publiée, sélectionnez l’onglet _SETTINGS_ et faites défiler jusqu’à « Deployment details ». Enregistrez les valeurs suivantes à partir de la requête HTTP de l’exemple _Postman_.

    ```text
    POST /knowledge bases/<knowledge-base-id>/generateAnswer
    Host: <your-hostname>  // NOTE - this is a URL.
    Authorization: EndpointKey <qna-maker-resource-key>
    ```
    
    La chaîne d’URL complète du nom d’hôte doit ressembler à « https://< >.azure.net/qnamaker ». Ces valeurs seront utilisées ultérieurement dans votre fichier `appsettings.json` ou `.env`.

## <a name="dispatch-app-needs-read-access-to-existing-apps"></a>L’application de dispatch a besoin d’un accès en lecture aux applications existantes

L’outil de dispatch a besoin d’un accès en création pour lire les applications LUIS et QnA Maker existantes afin de créer une nouvelle application parente LUIS qui effectue le dispatch aux applications LUIS et QnA Maker. Cet accès est fourni avec les ID d’application et les clés de création. 

### <a name="service-authoring-keys"></a>Clés de création de service

La **clé de création** est utilisée uniquement pour la création et la modification des modèles. Vous avez besoin d’un ID et d’une clé pour chacune des applications LUIS et QnA Maker.

|Application|Emplacement des informations|
|--|--|
|LUIS|**ID d’application** : disponible dans le [portal LUIS](https://www.luis.ai) pour chaque application, Manage (Gérer) -> Application Information (Informations sur l’application)<br>**Clé de création** : disponible dans le portail LUIS ; dans le coin supérieur droit, sélectionnez votre propre utilisateur, puis Paramètres.|
|QnA Maker| **ID d’application** : disponible dans le [portail QnA Maker](https://http://qnamaker.ai) dans la page Paramètres une fois que vous publiez l’application. Il s’agit de l’ID figurant dans la première partie de la commande POST après la base de connaissances. `POST /knowledgebases/{APP-ID}/generateAnswer` est un exemple d’emplacement où trouver l’ID d’application.<br>**Clé de création** : disponible dans le portail Azure, pour la ressource QnA Maker, sous **Clés**. Vous avez seulement besoin d’une des clés.|

La clé de création n’est pas utilisée pour obtenir un score de prédiction ni un score de confiance de l’application publiée. Vous avez besoin des clés de point de terminaison pour cette action. Les **[clés de point de terminaison](#service-endpoint-keys)** sont disponibles et utilisées ultérieurement dans ce tutoriel. 

Découvrez plus en détail les [deux différents types de clés](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/set-up-qnamaker-service-azure#types-of-keys-in-qna-maker) utilisés avec QnA Maker.

## <a name="create-the-dispatch-model"></a>Créer le modèle de dispatch

L’interface CLI de l’outil Dispatch crée le modèle de dispatch à l’application LUIS ou QnA Maker appropriée.

1. Ouvrez une invite de commandes ou une fenêtre de terminal, puis remplacez les répertoires par le répertoire **CognitiveModels**.
1. Vérifiez que la version actuelle de npm et l’outil Dispatch sont installés.

    ```cmd
    npm i -g npm
    npm i -g botdispatch
    ```

1. Utilisez `dispatch init` pour initialiser la création d’un fichier `.dispatch` pour votre modèle de dispatch. Créez-le en utilisant un nom de fichier facile à reconnaître.

    ```cmd
    dispatch init -n <filename-to-create> --luisAuthoringKey "<your-luis-authoring-key>" --luisAuthoringRegion <your-region>
    ```

1. Utilisez `dispatch add` pour ajouter vos applications LUIS et bases de connaissances QnA Maker au fichier `.dispatch`.

    ```cmd
    dispatch add -t luis -i "<app-id-for-weather-app>" -n "<name-of-weather-app>" -v <app-version-number> -k "<your-luis-authoring-key>" --intentName l_Weather
    dispatch add -t luis -i "<app-id-for-home-automation-app>" -n "<name-of-home-automation-app>" -v <app-version-number> -k "<your-luis-authoring-key>" --intentName l_HomeAutomation
    dispatch add -t qna -i "<knowledge-base-id>" -n "<knowledge-base-name>" -k "<azure-qna-service-key1>" --intentName q_sample-qna
    ```

1. Utilisez `dispatch create` pour générer un modèle de dispatch à partir du fichier `.dispatch`.

    ```cmd
    dispatch create
    ```

1. Publiez l’application de dispatch LUIS que vous venez de créer.

## <a name="use-the-dispatch-luis-app"></a>Utiliser l’application de dispatch LUIS

L’application LUIS générée définit les intentions de chacune des applications enfants et des bases de connaissances, ainsi qu’une intention _aucune_ pour les cas où l’énoncé ne correspond à rien.

- `l_HomeAutomation`
- `l_Weather`
- `None`
- `q_sample-qna`

Ces services ont besoin d’être publiés sous les noms corrects pour que le bot s’exécute correctement. Le bot a besoin d’informations sur les services publiés pour pouvoir accéder à ces services.

### <a name="service-endpoint-keys"></a>Clés de point de terminaison de service

Le bot a besoin de points de terminaison de prédiction de requête pour les trois applications LUIS (dispatch, météo et domotique) et la base de connaissances QnA Maker unique. Aidez-vous du tableau suivant pour trouver les clés de points de terminaison :

|Application|Emplacement de la clé de point de terminaison de requête|
|--|--|
|LUIS|Dans le portail LUIS, pour chaque application LUIS, dans la section Manager (Gérer), sélectionnez **Paramètres des clés et points de terminaison** pour trouver les clés associées à chaque application. Si vous suivez ce tutoriel, la clé de point de terminaison est la même clé que `<your-luis-authoring-key>`. La clé de création est valide pour 1 000 points de terminaison, puis elle expire.|
|QnA Maker|Dans le portail QnA Maker, pour la base de connaissances, dans les paramètres Gérer, utilisez la valeur de clé qui s’affiche dans les paramètres Postman pour l’en-tête **Authorization**, sans le texte de `EndpointKey `.|

Ces valeurs sont utilisées dans le fichier **appsettings.json** pour C# et le fichier **.env** pour javascript.

## <a name="ctabcs"></a>[C#](#tab/cs)

### <a name="installing-packages"></a>Installation des packages

Avant d’exécuter cette application pour la première fois, vérifiez que plusieurs packages nuget sont installés :

**Microsoft.Bot.Builder**

**Microsoft.Bot.Builder.AI.Luis**

**Microsoft.Bot.Builder.AI.QnA**

### <a name="manually-update-your-appsettingsjson-file"></a>Mettre à jour manuellement votre fichier appsettings.json

Une fois que toutes vos applications de service sont créées, les informations de chacune d’elles doivent être ajoutées dans votre fichier « appsettings.json ». Le code de l’[exemple C#][cs-sample] initial contient un fichier appsettings.json vide :

**appsettings.json**  
[!code-json[AppSettings](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/AppSettings.json?range=8-17)]

Pour chacune des entités indiquées ci-dessous, ajoutez les valeurs que vous avez enregistrées précédemment dans ces instructions :

**appsettings.json**
```json
"MicrosoftAppId": "",
"MicrosoftAppPassword": "",
  
"QnAKnowledgebaseId": "<knowledge-base-id>",
"QnAEndpointKey": "<qna-maker-resource-key>",
"QnAEndpointHostName": "<your-hostname>",

"LuisAppId": "<app-id-for-dispatch-app>",
"LuisAPIKey": "<your-luis-endpoint-key>",
"LuisAPIHostName": "<your-dispatch-app-region>",
```
Quand tous les changements sont en place, enregistrez ce fichier.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

### <a name="installing-packages"></a>Installation des packages

Avant d’exécuter cette application pour la première fois, vous devez installer plusieurs packages npm.

```powershell
npm install --save botbuilder
npm install --save botbuilder-ai
```
Pour utiliser le fichier de configuration .env, votre bot doit inclure un package supplémentaire :

```powershell
npm install --save dotenv
```

### <a name="manually-update-your-env-file"></a>Mettre à jour manuellement votre fichier .env

Une fois que toutes vos applications de service sont créées, les informations de chacune d’elles doivent être ajoutées dans votre fichier « .env ». Le code de l’[exemple JavaScript][js-sample] initial contient un fichier .env vide. 

**.env**  
[!code-file[EmptyEnv](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/.env?range=1-10)]

Ajoutez les valeurs de connexion de votre service comme indiqué ci-dessous :

**.env**

```file
MicrosoftAppId=""
MicrosoftAppPassword=""

QnAKnowledgebaseId="<knowledge-base-id>"
QnAEndpointKey="<qna-maker-resource-key>"
QnAEndpointHostName="<your-hostname>"

LuisAppId=<app-id-for-dispatch-app>
LuisAPIKey=<your-luis-endpoint-key>
LuisAPIHostName=<your-dispatch-app-region>
```

Quand tous les changements sont en place, enregistrez ce fichier.

---

### <a name="connect-to-the-services-from-your-bot"></a>Vous connecter aux services à partir de votre bot

Pour vous connecter aux services Dispatch, LUIS et QnA Maker, votre bot extrait des informations du fichier de paramètres (`appsettings.json` ou `.env`).

## <a name="ctabcs"></a>[C#](#tab/cs)

Dans **BotServices.cs**, les informations contenues dans le fichier de configuration _appsettings.json_ permettent de connecter votre bot de dispatch aux services `Dispatch` et `SampleQnA`. Les constructeurs utilisent les valeurs que vous avez fournies pour se connecter à ces services.

**BotServices.cs**  
[!code-csharp[ReadConfigurationInfo](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/BotServices.cs?range=14-30)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Dans **dispatchBot.js**, les informations contenues dans le fichier de configuration _.env_ permettent de connecter votre bot de dispatch aux services _LuisRecognizer(dispatch)_ et _QnAMaker_. Les constructeurs utilisent les valeurs que vous avez fournies pour se connecter à ces services.

**bots/dispatchBot.js**  
[!code-javascript[ReadConfigurationInfo](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=11-24)]

---

### <a name="call-the-services-from-your-bot"></a>Appeler les services à partir de votre bot

Pour chaque entrée utilisateur, la logique du bot effectue une vérification par rapport au modèle Dispatch combiné, trouve l’intention retournée en premier et utilise ces informations pour appeler le service approprié à l’entrée.

## <a name="ctabcs"></a>[C#](#tab/cs)

Dans le fichier **DispatchBot.cs**, chaque fois que la méthode `OnMessageActivityAsync` est appelée, nous vérifions le message utilisateur entrant par rapport au modèle Dispatch. Nous passons ensuite les éléments `topIntent` et `recognizerResult` du modèle Dispatch à la méthode appropriée pour appeler le service et retourner le résultat.

**bots\DispatchBot.cs**  
[!code-csharp[OnMessageActivity](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=26-36)]

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Dans la méthode `onMessage` **dispatchBot.js**, nous vérifions le message d’entrée utilisateur par rapport au modèle Dispatch, nous recherchons _topIntent_, puis nous passons le résultat en appelant  _dispatchToTopIntentAsync_.

**bots/dispatchBot.js**  

[!code-javascript[OnMessageActivity](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=29-42)]

---

### <a name="work-with-the-recognition-results"></a>Utiliser les résultats de reconnaissance

## <a name="ctabcs"></a>[C#](#tab/cs)

Lorsque le modèle renvoie un résultat, il indique quel service est le mieux à même de gérer l’énoncé. Le code de ce bot achemine la demande vers le service correspondant, puis il résume la réponse du service appelé. Selon l’_intention_ retournée par Dispatch, ce code utilise l’intention retournée pour router vers le modèle LUIS ou service QnA approprié.

**bots\DispatchBot.cs**  
[!code-csharp[DispatchToTop](~/../botbuilder-samples/samples/csharp_dotnetcore/14.nlp-with-dispatch/bots/DispatchBot.cs?range=51-69)]

Si la méthode `ProcessHomeAutomationAsync` ou `ProcessWeatherAsync` est appelée, les résultats lui sont passés à partir du modèle de dispatch au sein de _luisResult.ConnectedServiceResult_. La méthode spécifiée fournit alors des commentaires utilisateur indiquant la première intention du modèle de dispatch, ainsi que la liste ordonnée de tous les intentions et entités détectées.

Si la méthode `q_sample-qna` est appelée, elle utilise l’entrée utilisateur contenue dans turnContext pour générer une réponse à partir de la base de connaissances et présenter ce résultat à l’utilisateur.

## <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Lorsque le modèle renvoie un résultat, il indique quel service est le mieux à même de gérer l’énoncé. Le code de cet exemple utilise le _topIntent_ reconnu pour montrer comment router la demande vers le service correspondant.

**bots/dispatchBot.js**  
[!code-javascript[dispatchToTopIntentAsync](~/../botbuilder-samples/samples/javascript_nodejs/14.nlp-with-dispatch/bots/dispatchBot.js?range=59-75)]

Si la méthode `processHomeAutomation` ou `processWeather` est appelée, les résultats lui sont passés à partir du modèle de dispatch au sein de _recognizerResult.luisResult_. La méthode spécifiée fournit alors des commentaires utilisateur indiquant la première intention du modèle de dispatch, ainsi que la liste ordonnée de tous les intentions et entités détectées.

Si la méthode `q_sample-qna` est appelée, elle utilise l’entrée utilisateur contenue dans turnContext pour générer une réponse à partir de la base de connaissances et présenter ce résultat à l’utilisateur.

---

> [!NOTE]
> S’il s’agissait d’une application de production, c’est là que les méthodes LUIS sélectionnées se connecteraient à leur service spécifié, passeraient l’entrée utilisateur et traiteraient les données d’intention et d’entité LUIS retournées.

## <a name="test-your-bot"></a>Tester votre robot

1. À l’aide de votre environnement de développement, démarrez l’exemple de code. Notez l’adresse _localhost_ indiquée dans la barre d’adresses de la fenêtre de navigateur ouverte par votre application : « https://localhost:<Port_Number>  ». 
1. Ouvrez votre émulateur Bot Framework Emulator, puis sélectionnez `Create a new bot configuration`. Un fichier `.bot` vous permet d’utiliser l’_inspecteur_ dans l’émulateur de bot pour voir le code JSON retourné à partir de LUIS et de QnA Maker.
1. Dans la boîte de dialogue **New bot configuration (Nouvelle configuration de bot)** , entrez le nom de votre bot et votre URL de point de terminaison, telle que `http://localhost:3978/api/messages`. Enregistrez le fichier à la racine de votre projet d’exemples de code de bot.
1. Ouvrez le fichier du bot et ajoutez des sections pour vos applications LUIS et QnA Maker. Utilisez [cet exemple de fichier](https://github.com/microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json) en tant que modèle pour les paramètres. Enregistrez les modifications.
1. Sélectionnez le nom de bot dans la liste **Mes bots** pour accéder à votre bot actif. À titre de référence, voici quelques-unes des questions et commandes couvertes par les services générés pour votre bot :

    - QnA Maker
      - `hi`, `good morning`
      - `what are you`, `what do you do`
    - LUIS (domotique)
      - `turn on bedroom light`
      - `turn off bedroom light`
      - `make some coffee`
    - LUIS (météo)
      - `whats the weather in redmond washington`
      - `what's the forecast for london`
      - `show me the forecast for nebraska`

## <a name="dispatch-for-user-utterance-to-qna-maker"></a>Dispatch pour énoncé utilisateur à QnA Maker

1. Dans l’émulateur de bot, entrez le texte `hi` et soumettez l’énoncé. Le bot soumet cette requête à l’application de dispatch LUIS et reçoit en retour une réponse indiquant quelle application enfant doit obtenir cet énoncé pour un traitement ultérieur. 

1. En sélectionnant la ligne `LUIS Trace` dans le journal, vous pouvez voir la réponse de LUIS dans l’émulateur de bot. Le résultat LUIS de l’application de dispatch LUIS s’affiche dans l’inspecteur. 

    ```json
    {
      "luisResponse": {
        "entities": [],
        "intents": [
          {
            "intent": "q_sample-qna",
            "score": 0.9489713
          },
          {
            "intent": "l_HomeAutomation",
            "score": 0.0612499453
          },
          {
            "intent": "None",
            "score": 0.008567564
          },
          {
            "intent": "l_Weather",
            "score": 0.0025761195
          }
        ],
        "query": "Hi",
        "topScoringIntent": {
          "intent": "q_sample-qna",
          "score": 0.9489713
        }
      }
    }
    ```
    
    Étant donné que l’énoncé `hi` fait partie de l’intention **q_sample-qna** de l’application de dispatch LUIS et qu’il est sélectionné en tant que `topScoringIntent`, le bot effectue une deuxième requête, cette fois à l’application QnA Maker, avec le même énoncé. 

1. Sélectionnez la ligne `QnAMaker Trace` dans le journal de l’émulateur de bot. Le résultat QnA Maker s’affiche dans l’inspecteur. 

```json
{
    "questions": [
        "hi",
        "greetings",
        "good morning",
        "good evening"
    ],
    "answer": "Hello!",
    "score": 1,
    "id": 96,
    "source": "QnAMaker.tsv",
    "metadata": [],
    "context": {
        "isContextOnly": false,
        "prompts": []
    }
}
```

## <a name="resolving-incorrect-top-intent-from-dispatch"></a>Résolution d’une intention supérieure incorrecte à partir de Dispatch

Une fois que votre bot est en cours d’exécution, il est possible d’améliorer les performances du bot en supprimant les énoncés similaires ou qui se recoupent entre les applications dispatchées. Par exemple, supposons que dans l’application LUIS `Home Automation`, des demandes comme « allumer la lumière » correspondent à une intention « TurnOnLights » (Allumer la lumière), mais que des demandes comme « Pourquoi la lumière ne s’allume-t-elle pas ? » correspondent à une intention « None » (Aucune) afin qu’elles puissent être transmises à QnA Maker. Ces deux énoncés sont trop proches pour que l’application de dispatch LUIS puisse déterminer si l’application enfant correcte est l’application LUIS ou l’application QnA Maker.

Quand vous combinez l’application LUIS et l’application QnA Maker à l’aide de l’outil Dispatch, vous devez effectuer l’_une_ des opérations suivantes :

* Supprimez l’intention « None » de l’application LUIS `Home Automation` enfant et ajoutez plutôt les énoncés de cette intention dans l’intention « None » de l’application de dispatch.
* Ajoutez de la logique à votre bot pour transmettre les messages qui correspondent à l’intention « None » de l’application de dispatch LUIS au service QnA Maker. Comparez le score de l’application de dispatch LUIS et le score de l’application QnA Maker. Utilisez le score le plus élevé. Ceci a pour effet de supprimer QnA Maker du cycle de dispatch. 

Les deux actions ci-dessus réduisent le nombre de fois où votre bot répond à vos utilisateurs avec le message « Réponse introuvable ».

### <a name="to-update-or-create-a-new-luis-model"></a>Mettre à jour ou créer un modèle LUIS

cet exemple est basé sur un modèle LUIS préconfiguré. Vous trouverez [ici](https://aka.ms/create-luis-model#updating-your-cognitive-models) des informations complémentaires qui vous aideront à mettre à jour ce modèle ou à créer un modèle LUIS.

### <a name="to-delete-resources"></a>Pour supprimer des ressources

cet exemple crée un certain nombre d’applications et de ressources que vous pouvez supprimer à l’aide de la procédure ci-dessous. Veillez toutefois à ne pas supprimer les ressources dont dépendent *d’autres applications ou services*.

Pour supprimer des ressources LUIS :

1. Connectez-vous au portail [luis.ai](https://www.luis.ai).
1. Accédez à la page _My Apps_ (Mes applications).
1. Sélectionnez les applications créées par cet exemple.
   - `Home Automation`
   - `Weather`
   - `NLP-With-Dispatch-BotDispatch`
1. Cliquez sur _Delete_ (Supprimer), puis sur _Ok_ pour confirmer.

Pour supprimer des ressources QnA Maker :

1. Connectez-vous au portail [qnamaker.ai](https://www.qnamaker.ai/).
1. Accédez à la page _My knowledge bases_ (Mes bases de connaissances).
1. Cliquez sur le bouton de suppression de la base de connaissances `Sample QnA`, puis cliquez sur _Delete_ (Supprimer) pour confirmer.

### <a name="best-practice"></a>Bonne pratique

Pour améliorer les services utilisés dans cet exemple, reportez-vous aux bonnes pratiques pour [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices) et [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices).


[howto-luis]: bot-builder-howto-v4-luis.md
[howto-qna]: bot-builder-howto-qna.md

[cs-sample]: https://aka.ms/dispatch-sample-cs
[js-sample]: https://aka.ms/dispatch-sample-js

[dispatch-readme]: https://aka.ms/botbuilder-tools-dispatch
