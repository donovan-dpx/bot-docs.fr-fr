---
title: Ajouter des données de télémétrie à votre bot | Microsoft Docs
description: Découvrez comment intégrer votre bot aux nouvelles fonctionnalités de télémétrie.
keywords: télémétrie, appinsights, bot monitor
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7225387933630eb7343a57aa849581ff1cbfbb0c
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464702"
---
# <a name="add-telemetry-to-your-bot"></a>Ajouter des données de télémétrie à votre bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

Dans la version 4.2 du SDK Bot Framework, la journalisation des données de télémétrie a été ajoutée au produit.  Elle permet aux applications bot d’envoyer des données d’événement à des services comme Application Insights. La première section couvre ces méthodes et est suivie de fonctionnalités de télémétrie plus étendues.

Ce document explique comment intégrer votre bot aux nouvelles fonctionnalités de télémétrie. 

## <a name="basic-telemetry-options"></a>Options élémentaires de télémétrie

### <a name="basic-application-insights"></a>Fonctionnalités Application Insights de base

Tout d’abord, ajoutons des données de télémétrie de base à votre bot, à l’aide d’Application Insights. Pour plus d’informations sur la configuration, consultez les premières sections du [démarrage avec Application Insights](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started-with-Application-Insights-for-ASP.NET-Core).   

Si vous voulez « stocker » Application Insights, sans qu’aucune configuration supplémentaire spécifique à Application Insights ne soit nécessaire (par exemple, des initialiseurs de télémétrie), ajoutez les éléments suivants à votre méthode `ConfigureServices()`.   Il s’agit de la méthode la plus simple pour initialiser et configurer Application Insights en vue de commencer le suivi des requêtes et des appels externes à d’autres services, ainsi que la corrélation des événements entre les services.

Vous devez ajouter les packages NuGet inclus dans l’extrait de code ci-dessous.

**Startup.cs**
```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Bot.Builder.ApplicationInsights;
using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
using Microsoft.Bot.Builder.Integration.AspNet.Core;
 
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add Application Insights services into service collection
    services.AddApplicationInsightsTelemetry();

    // Add the standard telemetry client
    services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

    // Add ASP middleware to store the HTTP body, mapped with bot activity key, in the httpcontext.items
    // This will be picked by the TelemetryBotIdInitializer
    services.AddTransient<TelemetrySaveBodyASPMiddleware>();

    // Add telemetry initializer that will set the correlation context for all telemetry items
    services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

    // Add telemetry initializer that sets the user ID and session ID (in addition to other 
    // bot-specific properties, such as activity ID)
    services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();
    ...
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...
    app.UseBotApplicationInsights();
}
```

Vous devez ensuite stocker votre clé d’instrumentation Application Insights dans votre fichier `appsettings.json` ou comme variable d’environnement. Le fichier `appsettings.json` contient des métadonnées sur les services externes que le bot utilise quand il s’exécute.  Par exemple, CosmosDB, Application Insights ainsi que la connexion de service et les métadonnées de Language Understanding (LUIS) y sont stockés. Vous trouverez la clé d’instrumentation dans le portail Azure dans la section **Vue d’ensemble** (sous la liste déroulante `Essentials` de votre service dans cette page, si elle est réduite). Vous pouvez trouver plus d’informations sur l’obtention des clés [ici](~/bot-service-resources-app-insights-keys.md).

Le framework recherche la clé pour vous, si le format est correct. Le format de vos entrées `appsettings.json` doit se présenter comme suit :

```json
    "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
    },
    "Logging": {
        "LogLevel": {
            "Default": "Warning"
        }
    }
```

Pour plus d’informations sur l’ajout d’Application Insights à une application ASP.NET Core, consultez [cet article](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio). 

### <a name="customize-your-telemetry-client"></a>Personnaliser votre client de télémétrie

Si vous voulez personnaliser votre client Application Insights ou vous connecter à un service totalement distinct, vous devez configurer le système différemment. Téléchargez le package `Microsoft.Bot.Builder.ApplicationInsights` via nuget, ou utilisez npm pour installer `botbuilder-applicationinsights`. Vous trouverez plus d’informations sur l’obtention des clés Application Insights [ici](~/bot-service-resources-app-insights-keys.md).

**Modifier la configuration d’Application Insights**

Pour modifier votre configuration, vous devez inclure `options` quand vous ajoutez Application Insights. Sinon, tout est identique à ce qui précède.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add Application Insights services into service collection
    services.AddApplicationInsightsTelemetry(options);
    ...
}
```

L’objet `options` est du type `ApplicationInsightsServiceOptions`. Vous trouverez plus d’informations sur ces options [ici]().

**Utiliser une télémétrie personnalisée** Si vous voulez journaliser les événements de télémétrie générés par Bot Framework dans un système totalement distinct, créez une classe dérivée de l’interface de base `IBotTelemetryClient` et effectuez la configuration. Ensuite, quand vous ajoutez votre client de télémétrie comme indiqué ci-dessus, injectez simplement votre client personnalisé. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry client.
    services.AddSingleton<IBotTelemetryClient, CustomTelemetryClient>();
    ...
}
```

### <a name="add-custom-logging-to-your-bot"></a>Ajouter une journalisation personnalisée à votre bot

Une fois la nouvelle prise en charge de la journalisation des données de télémétrie configurée pour le bot, vous pouvez commencer à ajouter des données de télémétrie à votre bot.  `BotTelemetryClient` (en C#, `IBotTelemetryClient`) dispose de plusieurs méthodes pour journaliser différents types d’événements.  Si vous choisissez le type d’événement approprié, vous pouvez tirer parti des rapports Application Insights existants (si vous utilisez Application Insights).  Pour les scénarios généraux, `TraceEvent` est habituellement utilisé.  Les données journalisées à l’aide de `TraceEvent` arrivent dans la table `CustomEvent` dans Kusto.

Si vous utilisez un dialogue dans votre bot, chaque objet basé sur un dialogue (notamment les invites) contient une nouvelle propriété `TelemetryClient`.  C’est `BotTelemetryClient` qui vous permet d’effectuer la journalisation.  Ce n’est pas uniquement pour des raisons pratiques. Nous verrons plus loin dans cet article que si cette propriété est définie, `WaterfallDialogs` génère des événements.

#### <a name="identifiers-and-custom-events"></a>Identificateurs et événements personnalisés

Lors de la journalisation d’événements dans Application Insights, les événements générés contiennent des propriétés par défaut que vous n’avez pas besoin de renseigner.  Par exemple, les propriétés `user_id` et `session_id` sont contenues dans chaque événement personnalisé (généré avec l’API `TraceEvent`).  Par ailleurs, `activitiId`, `activityType` et `channelId` sont également ajoutées.

>Remarque : Ces valeurs ne sont pas fournies aux clients de télémétrie personnalisée.

Propriété |Type | Détails
--- | --- | ---
`user_id`| `string` | [ChannelID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) + [From.Id](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from)
`session_id`| `string`|  [ConversationID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation)
`customDimensions.activityId`| `string` | [ID d’activité du bot](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id)
`customDimensions.activityType` | `string` | [Type d’activité du bot](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
`customDimensions.channelId` | `string` |  [ID de canal d’activité du bot](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)

## <a name="in-depth-telemetry"></a>Télémétrie approfondie

Il existe trois nouveaux composants ajoutés au kit SDK dans la version 4.4.  Tous les composants journalisent des données à l’aide de l’interface `IBotTelemetryClient` (ou de `BotTelemetryClient` dans node.js) qui peut être substituée par une implémentation personnalisée.

- Composant d’intergiciel (middleware) de Bot Framework (*TelemetryLoggerMiddleware*) qui journalise des données lorsque des messages sont reçus, envoyés, mis à jour ou supprimés. Vous pouvez substituer cela par une journalisation personnalisée.
- Classe *LuisRecognizer*.  Vous pouvez substituer la journalisation personnalisée de deux manières : par appel (ajout/remplacement de propriétés) ou classes dérivées.
- Classe *QnAMaker*.  Vous pouvez substituer la journalisation personnalisée de deux manières : par appel (ajout/remplacement de propriétés) ou classes dérivées.

### <a name="telemetry-middleware"></a>Mmiddleware de télémétrie

|C#  | JavaScript |
|:-----|:------------|
|**Microsoft.Bot.Builder.TelemetryLoggerMiddleware** | **botbuilder-core** |

#### <a name="out-of-box-usage"></a>Composant prêt à l’emploi

TelemetryLoggerMiddleware est un composant de Bot Framework qui peut être ajouté sans modification, et il effectuera une journalisation qui permettra l’élaboration de rapports prêts à l’emploi fournis avec le kit SDK Bot Framework. 

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, TelemetryLoggerMiddleware>();

// Create the Bot Framework Adapter with error handling enabled.
services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();
```

#### <a name="adding-properties"></a>Ajout de propriétés
Si vous décidez d’ajouter des propriétés supplémentaires, la classe TelemetryLoggerMiddleware peut être dérivée.  Par exemple, si vous souhaitez ajouter la propriété « MyImportantProperty » à l’événement `BotMessageReceived`.  `BotMessageReceived` est journalisé lorsque l’utilisateur envoie un message au bot.  L’ajout de la propriété supplémentaire peut être effectué de la manière suivante :

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    public Task OnReceiveActivityAsync(
                  Activity activity,
                  CancellationToken cancellation)
    {
        // Fill in the "standard" properties for BotMessageReceived
        // and add our own property.
        var properties = FillReceiveEventProperties(activity, 
                    new Dictionary<string, string>
                    { {"MyImportantProperty", "myImportantValue" } } );
                    
        // Use TelemetryClient to log event
        TelemetryClient.TrackEvent(
                        TelemetryLoggerConstants.BotMsgReceiveEvent,
                        properties);
    }
    ...
}
```

De plus, dans Startup, il convient d’ajouter la nouvelle classe :

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, MyTelemetryMiddleware>();
```

#### <a name="completely-replacing-properties--additional-events"></a>Remplacement complet des propriétés / Événement(s) supplémentaire(s)

Si vous décidez de remplacer complètement les propriétés en cours de journalisation, la classe `TelemetryLoggerMiddleware` peut être dérivée (comme ci-dessus lors de l’extension des propriétés).   La journalisation des nouveaux événements est effectuée de la même façon.

Par exemple, si vous souhaitez remplacer complètement les propriétés `BotMessageSend` et envoyer plusieurs événements, il est possible de procéder comme suit :

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    public Task<RecognizerResult> OnLuisRecognizeAsync(
                  Activity activity,
                  string dialogId = null,
                  CancellationToken cancellation)
    {
        // Override properties for BotMsgSendEvent
        var botMsgSendProperties = new Dictionary<string, string>();
        properties.Add("MyImportantProperty", "myImportantValue");
        // Log event
        TelemetryClient.TrackEvent(
                        TelemetryLoggerConstants.BotMsgSendEvent,
                        botMsgSendProperties);
                        
        // Create second event.
        var secondEventProperties = new Dictionary<string, string>();
        secondEventProperties.Add("activityId",
                                   activity.Id);
        secondEventProperties.Add("MyImportantProperty",
                                   "myImportantValue");
        TelemetryClient.TrackEvent(
                        "MySecondEvent",
                        secondEventProperties);
    }
    ...
}
```
Remarque : Lorsque les propriétés standard ne sont pas journalisées, cela entraîne l’arrêt des rapports prêts à l’emploi fournis avec le produit.

#### <a name="events-logged-from-telemetry-middleware"></a>Événements journalisés à partir du middleware de télémétrie
[BotMessageSend](#customevent-botmessagesend)
[BotMessageReceived](#customevent-botmessagereceived)
[BotMessageUpdate](#customevent-botmessageupdate)
[BotMessageDelete](#customevent-botmessagedelete)

### <a name="telemetry-support-luis"></a>LUIS de prise en charge de télémétrie 

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.Luis** | **botbuilder-ai** |

#### <a name="out-of-box-usage"></a>Composant prêt à l’emploi
LuisRecognizer est un composant Bot Framework existant et la télémétrie peut être activée en transmettant une interface IBotTelemetryClient via `luisOptions`.  Vous pouvez remplacer les propriétés par défaut en cours de journalisation et journaliser de nouveaux événements en fonction des besoins.

Pendant la construction de `luisOptions`, un objet `IBotTelemetryClient` doit être fourni pour ce faire.

```csharp
var luisOptions = new LuisPredictionOptions(
      ...
      telemetryClient,
      false); // Log personal information flag. Defaults to false.

var client = new LuisRecognizer(luisApp, luisOptions);
```

#### <a name="adding-properties"></a>Ajout de propriétés
Si vous décidez d’ajouter des propriétés supplémentaires, la classe `LuisRecognizer` peut être dérivée.  Par exemple, si vous souhaitez ajouter la propriété « MyImportantProperty » à l’événement `LuisResult`.  `LuisResult` est journalisé quand un appel de prédiction LUIS est effectué.  L’ajout de la propriété supplémentaire peut être effectué de la manière suivante :

```csharp
class MyLuisRecognizer : LuisRecognizer 
{
   ...
   override protected Task OnRecognizerResultAsync(
           RecognizerResult recognizerResult,
           ITurnContext turnContext,
           Dictionary<string, string> properties = null,
           CancellationToken cancellationToken = default(CancellationToken))
   {
       var luisEventProperties = FillLuisEventProperties(result, 
               new Dictionary<string, string>
               { {"MyImportantProperty", "myImportantValue" } } );
        
        TelemetryClient.TrackEvent(
                        LuisTelemetryConstants.LuisResultEvent,
                        luisEventProperties);
        ..
   }    
   ...
}
```

#### <a name="add-properties-per-invocation"></a>Ajouter des propriétés par appel
Il est parfois nécessaire d’ajouter des propriétés supplémentaires lors de l’appel :
```csharp
var additionalProperties = new Dictionary<string, string>
{
   { "dialogId", "myDialogId" },
   { "conversationInfo", "myConversationInfo" },
};

var result = await recognizer.RecognizeAsync(turnContext,
     additionalProperties,
     CancellationToken.None).ConfigureAwait(false);
```

#### <a name="completely-replacing-properties--additional-events"></a>Remplacement complet des propriétés / Événement(s) supplémentaire(s)
Si vous décidez de remplacer complètement les propriétés en cours de journalisation, la classe `LuisRecognizer` peut être dérivée (comme ci-dessus lors de l’extension des propriétés).   La journalisation des nouveaux événements est effectuée de la même façon.

Par exemple, si vous souhaitez remplacer complètement les propriétés `LuisResult` et envoyer plusieurs événements, il est possible de procéder comme suit :

```csharp
class MyLuisRecognizer : LuisRecognizer
{
    ...
    override protected Task OnRecognizerResultAsync(
             RecognizerResult recognizerResult,
             ITurnContext turnContext,
             Dictionary<string, string> properties = null,
             CancellationToken cancellationToken = default(CancellationToken))
    {
        // Override properties for LuisResult event
        var luisProperties = new Dictionary<string, string>();
        properties.Add("MyImportantProperty", "myImportantValue");
        
        // Log event
        TelemetryClient.TrackEvent(
                        LuisTelemetryConstants.LuisResult,
                        luisProperties);
                        
        // Create second event.
        var secondEventProperties = new Dictionary<string, string>();
        secondEventProperties.Add("MyImportantProperty2",
                                   "myImportantValue2");
        TelemetryClient.TrackEvent(
                        "MySecondEvent",
                        secondEventProperties);
        ...
    }
    ...
}
```
Remarque : Lorsque les propriétés standard ne sont pas journalisées, cela entraîne l’arrêt des rapports Application Insights prêts à l’emploi fournis avec le produit.

#### <a name="events-logged-from-telemetryluisrecognizer"></a>Événements journalisés à partir de TelemetryLuisRecognizer
[LuisResult](#customevent-luisevent)

### <a name="telemetry-qna-recognizer"></a>Module de reconnaissance QnA de télémétrie

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.QnA** | **botbuilder-ai** |


#### <a name="out-of-box-usage"></a>Composant prêt à l’emploi
La classe QnAMaker est un composant Bot Framework existant qui ajoute deux paramètres de constructeur supplémentaires qui activent la journalisation et permettent d’utiliser les rapports prêts à l’emploi fournis avec le kit SDK Bot Framework. Le nouveau `telemetryClient` fait référence à une interface `IBotTelemetryClient` qui effectue la journalisation.  

```csharp
var qna = new QnAMaker(endpoint, options, client, 
                       telemetryClient: telemetryClient,
                       logPersonalInformation: true);
```
#### <a name="adding-properties"></a>Ajout de propriétés 
Si vous décidez d’ajouter des propriétés supplémentaires, il existe deux méthodes pour cela : quand les propriétés doivent être ajoutées pendant l’appel QnA pour récupérer les réponses ou en dérivant de la classe `QnAMaker`.  

La dérivation de la classe `QnAMaker` est illustrée ci-après.  Cet exemple montre l’ajout de la propriété « MyImportantProperty » à l’événement `QnAMessage`.  L’événement `QnAMessage` est journalisé quand un appel QnA `GetAnswers` est effectué.  De plus, nous journalisons un deuxième événement, « MySecondEvent ».

```csharp
class MyQnAMaker : QnAMaker 
{
   ...
   protected override Task OnQnaResultsAsync(
                 QueryResult[] queryResults, 
                 ITurnContext turnContext, 
                 Dictionary<string, string> telemetryProperties = null, 
                 Dictionary<string, double> telemetryMetrics = null, 
                 CancellationToken cancellationToken = default(CancellationToken))
   {
            var eventData = await FillQnAEventAsync(queryResults, turnContext, telemetryProperties, telemetryMetrics, cancellationToken).ConfigureAwait(false);

            // Add my property
            eventData.Properties.Add("MyImportantProperty", "myImportantValue");

            // Log QnaMessage event
            TelemetryClient.TrackEvent(
                            QnATelemetryConstants.QnaMsgEvent,
                            eventData.Properties,
                            eventData.Metrics
                            );

            // Create second event.
            var secondEventProperties = new Dictionary<string, string>();
            secondEventProperties.Add("MyImportantProperty2",
                                       "myImportantValue2");
            TelemetryClient.TrackEvent(
                            "MySecondEvent",
                            secondEventProperties);       }    
    ...
}
```

#### <a name="adding-properties-during-getanswersasync"></a>Ajout de propriétés pendant GetAnswersAsync
Si vous avez des propriétés qui doivent être ajoutées pendant l’exécution, la méthode `GetAnswersAsync` peut fournir des propriétés et/ou des métriques à ajouter à l’événement.

Par exemple, si vous souhaitez ajouter un `dialogId` à l’événement, il est possible de procéder comme suit :
```csharp
var telemetryProperties = new Dictionary<string, string>
{
   { "dialogId", myDialogId },
};

var results = await qna.GetAnswersAsync(context, opts, telemetryProperties);
```
La classe `QnaMaker` offre la possibilité de substituer des propriétés, y compris les propriétés PersonalInfomation.

#### <a name="completely-replacing-properties--additional-events"></a>Remplacement complet des propriétés / Événement(s) supplémentaire(s)
Si vous décidez de remplacer complètement les propriétés en cours de journalisation, la classe `TelemetryQnAMaker` peut être dérivée (comme ci-dessus lors de l’extension des propriétés).   La journalisation des nouveaux événements est effectuée de la même façon.

Par exemple, si vous souhaitez remplacer complètement les propriétés `QnAMessage`, il est possible de procéder comme suit :

```csharp
class MyLuisRecognizer : TelemetryQnAMaker
{
    ...
    protected override Task OnQnaResultsAsync(
         QueryResult[] queryResults, 
         ITurnContext turnContext, 
         Dictionary<string, string> telemetryProperties = null, 
         Dictionary<string, double> telemetryMetrics = null, 
         CancellationToken cancellationToken = default(CancellationToken))
    {
        // Add properties from GetAnswersAsync
        var properties = telemetryProperties ?? new Dictionary<string, string>();
        // GetAnswerAsync properties overrides - don't add if already present.
        properties.TryAdd("MyImportantProperty", "myImportantValue");

        // Log event
        TelemetryClient.TrackEvent(
                           QnATelemetryConstants.QnaMsgEvent,
                            properties);
    }
    ...
}
```
Remarque : Lorsque les propriétés standard ne sont pas journalisées, cela entraîne l’arrêt des rapports prêts à l’emploi fournis avec le produit.

#### <a name="events-logged-from-telemetryluisrecognizer"></a>Événements journalisés à partir de TelemetryLuisRecognizer
[QnAMessage](#customevent-qnamessage)


## <a name="waterfalldialog-events"></a>Événements WaterfallDialog

En plus de générer vos propres événements, l’objet `WaterfallDialog` du kit SDK génère maintenant des événements. La section suivante décrit les événements générés à partir de Bot Framework. Si la propriété `TelemetryClient` est définie sur `WaterfallDialog`, ces événements sont stockés.

Voici un exemple de la modification d’un échantillon (CoreBot) qui utilise `WaterfallDialog` pour journaliser des événements de télémétrie.  CoreBot emploie un modèle courant utilisé dans lequel un `WaterfallDialog` est placé dans un `ComponentDialog` (`GreetingDialog`).

```csharp
// IBotTelemetryClient is direct injected into our Bot
public CoreBot(BotServices services, UserState userState, ConversationState conversationState, IBotTelemetryClient telemetryClient)
...

// The IBotTelemetryClient passed to the GreetingDialog
...
Dialogs = new DialogSet(_dialogStateAccessor);
Dialogs.Add(new GreetingDialog(_greetingStateAccessor, telemetryClient));
...

// The IBotTelemetryClient to the WaterfallDialog
...
AddDialog(new WaterfallDialog(ProfileDialog, waterfallSteps) { TelemetryClient = telemetryClient });
...

```

Une fois que `WaterfallDialog` a configuré `IBotTelemetryClient`, il commence à journaliser des événements.

## <a name="events-generated-by-the-bot-framework-service"></a>Événements générés par le service Bot Framework

En plus de `WaterfallDialog`, qui génère des événements à partir de votre code de bot, le service Bot Framework Channel journalise également des événements.  Cela vous permet de diagnostiquer les problèmes liés aux canaux ou l’ensemble des défaillances du bot.

### <a name="customevent-activity"></a>CustomEvent : « Activity »
**Journalisé à partir de :** Service Channel   Journalisé par le service Channel quand un message est reçu.

### <a name="exception-bot-errors"></a>Exception : « Bot Errors »
**Journalisé à partir de :** Service Channel   Journalisé par le canal quand un appel au bot retourne une réponse HTTP non 2XX.

## <a name="all-events-generated"></a>Tous les événements générés

### <a name="customevent-waterfallstart"></a>CustomEvent : « WaterfallStart » 

Quand un WaterfallDialog commence, un événement `WaterfallStart` est journalisé.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

### <a name="customevent-waterfallstep"></a>CustomEvent : « WaterfallStep » 

Journalise les étapes individuelles d’un dialogue en cascade.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.StepName` (un nom de méthode ou `StepXofY` en cas d’expression lambda)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

### <a name="customevent-waterfalldialogcomplete"></a>CustomEvent : « WaterfallDialogComplete »

Journalise quand un dialogue en cascade se termine.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

### <a name="customevent-waterfalldialogcancel"></a>CustomEvent : « WaterfallDialogCancel » 

Journalise quand un dialogue en cascade est annulé.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.StepName` (un nom de méthode ou `StepXofY` en cas d’expression lambda)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

### <a name="customevent-botmessagereceived"></a>CustomEvent : BotMessageReceived 
Journalisé quand le bot reçoit un nouveau message d’un utilisateur.

Lorsqu’il n’est pas remplacé, cet événement est journalisé à partir de `Microsoft.Bot.Builder.TelemetryLoggerMiddleware` à l’aide de la méthode `Microsoft.Bot.Builder.IBotTelemetry.TrackEvent()`.

- Identificateur de session  
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant qu’identificateur de **session** (*Temeletry.Context.Session.Id*) utilisé dans Application Insights.  
  - Correspond à l’[ID de conversation](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation) tel que défini par le protocole Bot Framework.
  - Le nom de propriété journalisé est `session_id`.

- Identificateur d’utilisateur
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant qu’identificateur d’**utilisateur** (*Telemetry.Context.User.Id*) utilisé dans Application Insights.  
  - La valeur de cette propriété est une combinaison des propriétés [Identificateur de canal](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) et [ID utilisateur](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (concaténées ensemble) telles que définies par le protocole Bot Framework.
  - Le nom de propriété journalisé est `user_id`.

- ActivityID 
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.
  - Correspond à l’[ID d’activité](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#Id) tel que défini par le protocole Bot Framework.
  - Le nom de la propriété est `activityId`.

- Identificateur de canal
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.  
  - Correspond au champ [Channel Identifier](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id) (Identificateur de canal) du protocole Bot Framework.
  - Le nom de propriété journalisé est `channelId`.

- ActivityType 
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.  
  - Correspond au champ [Activity Type](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#type) (Type d’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `activityType`.

- Texte
  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [Activity Text](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#text) (Texte de l’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `text`.

- Speak

  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [Activity Speak](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#speak) (Discours d’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `speak`.

  - 

- FromId
  - Correspond au champ [From Identifier](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant de l’identificateur) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromId`.

- FromName
  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- RecipientId
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- RecipientName
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- ConversationId
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- ConversationName
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- Paramètres régionaux
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

### <a name="customevent-botmessagesend"></a>CustomEvent : BotMessageSend 
**Journalisé à partir de :** TelemetryLoggerMiddleware 

Journalisé quand le bot envoie un message.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ReplyToID
- RecipientId
- ConversationName
- Paramètres régionaux
- RecipientName (facultatif pour PII)
- Text (facultatif pour PII)
- Speak (facultatif pour PII)


### <a name="customevent-botmessageupdate"></a>CustomEvent : BotMessageUpdate
**Journalisé à partir de :** TelemetryLoggerMiddleware   Journalisé quand un message est mis à jour par le bot (cas rare)
- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName
- Paramètres régionaux
- Text (facultatif pour PII)


### <a name="customevent-botmessagedelete"></a>CustomEvent : BotMessageDelete
**Journalisé à partir de :** TelemetryLoggerMiddleware   Journalisé quand un message est supprimé par le bot (cas rare)
- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName

### <a name="customevent-luisevent"></a>CustomEvent : LuisEvent
**Journalisé à partir de :** LuisRecognizer

Journalise les résultats du service LUIS.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ApplicationId
- Intention
- IntentScore
- Intent2 
- IntentScore2 
- FromId
- SentimentLabel
- SentimentScore
- Entities (en tant que json)
- Question (facultatif pour PII)

## <a name="customevent-qnamessage"></a>CustomEvent : QnAMessage
**Journalisé à partir de :** QnAMaker

Journalise les résultats du service QnA Maker.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Username (facultatif pour PII)
- Question (facultatif pour PII)
- MatchedQuestion
- QuestionId
- Réponse
- Score
- ArticleFound

## <a name="querying-the-data"></a>Interrogation des données
Quand vous utilisez Application Insights, toutes les données sont corrélées (même entre les services).  Nous pouvons le constater en interrogeant une requête réussie et voir tous les événements associés pour cette requête.  
Les requêtes suivantes vous indiquent les requêtes les plus récentes :
```sql
requests 
| where timestamp > ago(3d) 
| where resultCode == 200
| order by timestamp desc
| project timestamp, operation_Id, appName
| limit 10
```

Avec la première requête, sélectionnez quelques-unes des valeurs `operation_Id` et recherchez plus d’informations :

```sql
let my_operation_id = "<OPERATION_ID>";
let union_all = () {
    union
    (traces | where operation_Id == my_operation_id),
    (customEvents | where operation_Id == my_operation_id),
    (requests | where operation_Id == my_operation_id),
    (dependencies | where operation_Id  == my_operation_id),
    (exceptions | where operation_Id == my_operation_id)
};
union_all
    | order by timestamp asc
    | project itemType, name, performanceBucket
```

Cela vous donne la répartition chronologique d’une requête unique, avec le compartiment de durée de chaque appel.
![Exemple d’appel](media/performance_query.png)

> Remarque : L’horodatage d’événement `customEvent` « Activity » est hors service, car ces événements sont journalisés de façon asynchrone.

## <a name="create-a-dashboard"></a>Création d’un tableau de bord

La façon la plus simple d’effectuer un test consiste à créer un tableau de bord à l’aide de la [page de déploiement de modèle du portail Azure](https://portal.azure.com/#create/Microsoft.Template).  
- Cliquer sur ["Créer votre propre modèle dans l’éditeur"]
- Copiez et collez l’un des fichiers .json suivants qui sont fournis pour vous aider à créer le tableau de bord :
  - [Tableau de bord d’intégrité du système](https://aka.ms/system-health-appinsights)
  - [Tableau de bord d’intégrité de conversation](https://aka.ms/conversation-health-appinsights)
- Cliquez sur « Enregistrer »
- Remplir `Basics` : 
   - Abonnement : <your test subscription>
   - Groupe de ressources : <a test resource group>
   - Lieu : <such as West US>
- Remplir `Settings` :
   - Nom du composant Insights : <like `core672so2hw`>
   - Groupe de ressources de composant Insights : <like `core67`>
   - Nom du tableau de bord : <like `'ConversationHealth'` ou `SystemHealth`>
- Cliquez sur `I agree to the terms and conditions stated above`
- Cliquez sur `Purchase`
- Valider
   - Cliquez sur [`Resource Groups`](https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fsubscriptions%2FresourceGroups)
   - Sélectionnez votre groupe de ressources ci-dessus (comme `core67`).
   - Si vous ne voyez pas de nouvelle ressource, regardez « Déploiements » pour voir s’il y a des échecs.
   - Voici ce que vous voyez généralement concernant les échecs :
     
```json
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"BadRequest","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidTemplate\",\r\n \"message\": \"Unable to process template language expressions for resource '/subscriptions/45d8a30e-3363-4e0e-849a-4bb0bbf71a7b/resourceGroups/core67/providers/Microsoft.Portal/dashboards/Bot Analytics Dashboard' at line '34' and column '9'. 'The template parameter 'virtualMachineName' is not found. Please see https://aka.ms/arm-template/#parameters for usage details.'\"\r\n }\r\n}"}]}
```

Pour afficher les données, accédez au portail Azure. Cliquez sur **Tableau de bord** à gauche, puis sélectionnez le tableau de bord que vous avez créé dans la liste déroulante.

## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez vous référer à ces exemples qui implémentent des mesures de télémétrie :
- C#
  - [LUIS avec AppInsights](https://aka.ms/luis-with-appinsights-cs)
  - [QnA avec AppInsights](https://aka.ms/qna-with-appinsights-cs)
- JS
  - [LUIS avec AppInsights](https://aka.ms/luis-with-appinsights-js)
  - [QnA avec AppInsights](https://aka.ms/qna-with-appinsights-js)

