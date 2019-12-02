---
title: Ajouter des données de télémétrie à votre bot | Microsoft Docs
description: Découvrez comment intégrer votre bot aux nouvelles fonctionnalités de télémétrie.
keywords: télémétrie, appinsights, bot monitor
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: a023fd97bfb7b8d55ad01d118075a6441e426575
ms.sourcegitcommit: 08f9dc91152e0d4565368f72f547cdea1885af89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74510757"
---
# <a name="add-telemetry-to-your-bot"></a>Ajouter des données de télémétrie à votre bot

[!INCLUDE[applies-to](../includes/applies-to.md)]


La journalisation de télémétrie a été ajoutée à la version 4.2 du Kit de développement logiciel (SDK) de Bot Framework.  Elle permet aux applications de robot d’envoyer des données d’événement à des services de télémétrie comme [Application Insights](https://aka.ms/appinsights-overview). La télémétrie offre des aperçus de votre robot en indiquant les fonctionnalités qui sont utilisées le plus, détecte le comportement indésirable et offre une visibilité sur la disponibilité, les performances et l’utilisation.

***Remarque : Dans la version 4.6, la méthode standard d’implémentation de la télémétrie dans un bot a été mise à jour pour que les données de télémétrie soient correctement journalisées quand un adaptateur personnalisé est utilisé. Cet article a été mis à jour pour présenter la méthode mise à jour. Les modifications sont rétrocompatibles et les bots qui utilisent la méthode précédente continuent de journaliser correctement les données de télémétrie.***


Dans cet article, vous allez apprendre à implémenter la télémétrie dans votre robot à l’aide de Application Insights :

* Le code requis pour relier la télémétrie dans votre robot et se connecter à Application Insights

* Activation de la télémétrie dans les [Boîtes de dialogue](bot-builder-concept-dialog.md) de vos robots

* L’activation de la télémétrie pour capturer les données d'utilisation d'autres services, comme [LUIS](bot-builder-howto-v4-luis.md) et [QnA Maker](bot-builder-howto-qna.md).

* Visualisation des données de votre télémétrie dans Application Insights

## <a name="prerequisites"></a>Prérequis

* L’[exemple de code CoreBot](https://aka.ms/cs-core-sample)

* L’[exemple de code d’Application Insights](https://aka.ms/csharp-corebot-app-insights-sample)

* Un abonnement à [Microsoft Azure](https://portal.azure.com/)

* Une [clé Application Insights](../bot-service-resources-app-insights-keys.md)

* Vous êtes familiarisé à [Application Insights](https://aka.ms/appinsights-overview)

> [!NOTE]
> L'[exemple de code Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) a été créé au-dessus de l’[exemple de code CoreBot](https://aka.ms/cs-core-sample). Cet article vous guide tout au long de la modification de l’exemple de code CoreBot pour incorporer la télémétrie. Si vous suivez la procédure dans Visual Studio, vous obtiendrez l’exemple de code Application Insights à la fin de l’opération.

## <a name="wiring-up-telemetry-in-your-bot"></a>Connexion de la télémétrie dans votre robot

Nous allons commencer par l'[exemple d’application CoreBot](https://aka.ms/cs-core-sample) et ajouter le code requis pour intégrer la télémétrie dans n’importe quel robot. Cela permet à Application Insights de commencer à suivre les requêtes.

1. Ouvrez l’[exemple d’application CoreBot](https://aka.ms/cs-core-sample) dans Visual Studio

2. Ajoutez le package NuGet `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core `. Pour plus d’informations sur l’utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs) :


3. Ajoutez les instructions suivantes dans `Startup.cs` :
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.Bot.Builder.ApplicationInsights;
    using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    ```

    Remarque : Si vous suivez la procédure en mettant à jour l’exemple de code CoreBot, vous remarquerez que l’instruction d’utilisation pour `Microsoft.Bot.Builder.Integration.AspNet.Core` existe déjà dans l’exemple CoreBot.

4. Ajoutez le code suivant à la méthode `ConfigureServices()` dans `Startup.cs`. Cela rendra les services de télémétrie disponibles pour votre robot via [l’injection de dépendances (DI)](https://aka.ms/asp.net-core-dependency-interjection) :
    ```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        // Create the Bot Framework Adapter with error handling enabled.
        services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();

        // Add Application Insights services into service collection
        services.AddApplicationInsightsTelemetry();

        // Add the standard telemetry client
        services.AddSingleton<IBotTelemetryClient, BotTelemetryClient>();

        // Create the telemetry middleware to track conversation events
        services.AddSingleton<TelemetryLoggerMiddleware>();

        // Add the telemetry initializer middleware
        services.AddSingleton<IMiddleware, TelemetryInitializerMiddleware>();

        // Add telemetry initializer that will set the correlation context for all telemetry items
        services.AddSingleton<ITelemetryInitializer, OperationCorrelationTelemetryInitializer>();

        // Add telemetry initializer that sets the user ID and session ID (in addition to other bot-specific properties, such as activity ID)
        services.AddSingleton<ITelemetryInitializer, TelemetryBotIdInitializer>();
        ...
    }
    ```
    
    Remarque : Si vous procédez comme suit pour mettre à jour l’exemple de code CoreBot , vous remarquerez que `services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();` existe déjà 

5. Demandez à l’adaptateur d’utiliser le code de l’intergiciel qui a été ajouté à la méthode `ConfigureServices()`. Pour ce faire dans `AdapterWithErrorHandler.cs` avec l’intergiciel du IMiddleware du paramètre dans la liste de paramètres des constructeurs et l'instruction `Use(middleware);` dans le constructeur comme indiqué ici :
    ```csharp
    public AdapterWithErrorHandler(ICredentialProvider credentialProvider, ILogger<BotFrameworkHttpAdapter> logger, IMiddleware middleware, ConversationState conversationState = null)
            : base(credentialProvider)
    {
        ...

        Use(middleware);
    }
    ```

6. Ajoutez la clé d’instrumentation Application Insights dans votre fichier `appsettings.json`. Le fichier `appsettings.json` contient des métadonnées sur les services externes que le robot utilise lors de son exécution. Par exemple, la connexion à CosmosDB, Application Insights et au service Language Understanding (LUIS) et les métadonnées y sont stockées. L’ajout à votre fichier `appsettings.json` doit être au format suivant :

    ```json
    {
        "MicrosoftAppId": "",
        "MicrosoftAppPassword": "",
        "ApplicationInsights": {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        }
    }
    ```
    Remarque : Pour plus d’informations sur l’obtention de la _clé d’instrumentation Application Insights_, consultez l’article [clés Application Insights](../bot-service-resources-app-insights-keys.md).

À ce stade, le travail préliminaire d’activation de la télémétrie à l’aide d’Application Insights est effectué.  Vous pouvez exécuter votre robot localement à l’aide de l’émulateur de robot, puis passer à Application Insights pour voir ce qui est enregistré, comme le temps de réponse, l’intégrité globale de l’application et les informations d’exécution générales. 

## <a name="enabling--disabling-activity-event-and-personal-information-logging"></a>Activation/désactivation de la journalisation des événements d’activité et des informations personnelles

### <a name="enabling-or-disabling-activity-logging"></a>Activation ou désactivation de la journalisation des activités

Par défaut, `TelemetryInitializerMiddleware` utilise `TelemetryLoggerMiddleware` pour journaliser les données de télémétrie quand votre bot envoie/reçoit des activités. La journalisation des activités crée des journaux d’événements personnalisés dans votre ressource Application Insights.  Si vous le souhaitez, vous pouvez désactiver la journalisation des événements d’activité en affectant à `logActivityTelemetry` la valeur false dans `TelemetryInitializerMiddleware` avant de l’inscrire dans **Startup.cs**.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<IMiddleware, TelemetryInitializerMiddleware>(sp =>
            {
                var httpContextAccessor = sp.GetService<IHttpContextAccessor>();
                var loggerMiddleware = sp.GetService<TelemetryLoggerMiddleware>();
                return new TelemetryInitializerMiddleware(httpContextAccessor, loggerMiddleware, logActivityTelemetry: false);
            });
    ...
}
```

### <a name="enable-or-disable-logging-personal-information"></a>Activer ou désactiver la journalisation des informations personnelles

Par défaut, si la journalisation des activités est activée, certaines propriétés des activités entrantes/sortantes sont exclues de la journalisation, car elles sont susceptibles de contenir des informations personnelles, comme le nom d’utilisateur et le texte de l’activité. Vous pouvez choisir d’inclure ces propriétés dans votre journalisation en apportant la modification suivante à **Startup.cs** au moment d’inscrire `TelemetryLoggerMiddleware`.

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry initializer middleware
    services.AddSingleton<TelemetryLoggerMiddleware>(sp =>
            {
                var telemetryClient = sp.GetService<IBotTelemetryClient>();
                return new TelemetryLoggerMiddleware(telemetryClient, logPersonalInformation: true);
            });
    ...
}
```

Nous verrons ensuite ce qui doit être inclus pour ajouter la fonctionnalité de télémétrie aux boîtes de dialogue. Cela vous permettra d’obtenir des informations supplémentaires, telles que ce qui est exécuté par les boîtes de dialogue et les statistiques sur chacune d’elles.

## <a name="enabling-telemetry-in-your-bots-dialogs"></a>Activation de la télémétrie dans les boîtes de dialogue de vos robots

 Suivez les étapes ci-dessous pour mettre à jour votre exemple de CoreBot :

1.  Dans `MainDialog.cs`, ajoutez un nouveau champ TelemetryClient à la classe `MainDialog`, puis mettez à jour la liste des paramètres de constructeur pour inclure un paramètre `IBotTelemetryClient`, et passez-le à chaque appel de la méthode `AddDialog()`.


    * Ajoutez le paramètre `IBotTelemetryClient telemetryClient` au constructeur de la classe MainDialog, puis affectez- le au champ `TelemetryClient` :

        ```csharp
        public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger, IBotTelemetryClient telemetryClient)
            : base(nameof(MainDialog))
        {
            TelemetryClient = telemetryClient;

            ...

        }
        ```

    * Lorsque vous ajoutez chaque boîte de dialogue à l’aide de la méthode `AddDialog`, définissez la valeur `TelemetryClient` :

        ```cs
        public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger, IBotTelemetryClient telemetryClient)
            : base(nameof(MainDialog))
        {
            ...

            AddDialog(new TextPrompt(nameof(TextPrompt))
            {
                TelemetryClient = telemetryClient,
            });

            ...
        }
        ```

    * Les boîtes de dialogue en cascade génèrent des événements indépendamment des autres boîtes de dialogue. Définissez la propriété `TelemetryClient` après la liste des étapes en cascade :

        ```csharp
        // The IBotTelemetryClient to the WaterfallDialog
        AddDialog(new WaterfallDialog(
            nameof(WaterfallDialog),
            new WaterfallStep[]
        {
            IntroStepAsync,
            ActStepAsync,
            FinalStepAsync,
        })
        {
            TelemetryClient = telemetryClient,
        });

        ```

2. Dans `DialogExtensions.cs`, définissez la propriété `TelemetryClient` de l'objet `dialogSet` dans la méthode `Run()` :


    ```csharp
    public static async Task Run(this Dialog dialog, ITurnContext turnContext, IStatePropertyAccessor<DialogState> accessor, CancellationToken cancellationToken = default(CancellationToken))
    {
        ...

        dialogSet.TelemetryClient = dialog.TelemetryClient;

        ...
        
    }

    ```

3. Dans `BookingDialog.cs`, utilisez le même processus que celui utilisé lors de la mise à jour de `MainDialog.cs` pour activer la télémétrie dans les quatre boîtes de dialogue ajoutées dans ce fichier. N’oubliez pas d’ajouter le champ `TelemetryClient` à la classe BookingDialog et le paramètre `IBotTelemetryClient telemetryClient` au constructeur BookingDialog.


> [!TIP] 
> Si vous suivez et mettez à jour l’exemple de code CoreBot, vous pouvez vous référer à l’[exemple de code Application Insights](https://aka.ms/csharp-corebot-app-insights-sample) si vous rencontrez des problèmes.

C’est tout ce qu’il faut savoir pour ajouter la télémétrie à vos boîtes de dialogue de robots. à ce stade, si vous avez exécuté votre robot, vous devriez voir les éléments journalisés dans Application Insights. Toutefois, si vous disposez d’une technologie intégrée comme LUIS et QnA Maker, vous devrez également ajouter le `TelemetryClient` au code.


## <a name="enabling-telemetry-to-capture-usage-data-from-other-services-like-luis-and-qna-maker"></a>Activation de la télémétrie pour capturer les données d'utilisation d'autres services, comme LUIS et QnA Maker

Nous allons ensuite implémenter la fonctionnalité de télémétrie dans votre service LUIS. Le service LUIS intègre une journalisation de télémétrie intégrée. Il est par conséquent très facile de commencer à obtenir des données de télémétrie à partir de LUIS.  Si vous souhaitez activer la télémétrie dans un bot QnA Maker, consultez [Ajouter des données de télémétrie à votre bot QnAMaker](bot-builder-telemetry-QnAMaker.md)

Pour cet exemple, nous devons simplement fournir le client de télémétrie de la même façon que nous l’avons fait pour les boîtes de dialogue. 

1. Le paramètre _`IBotTelemetryClient telemetryClient`_ est requis dans la méthode `ExecuteLuisQuery()` dans `LuisHelper.cs` :

    ```cs
    public static async Task<BookingDetails> ExecuteLuisQuery(IBotTelemetryClient telemetryClient, IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
    ```

2. La classe `LuisPredictionOptions` vous permet de fournir des paramètres facultatifs pour une requête de prédiction LUIS.  Pour activer la télémétrie, vous devez définir le paramètre `TelemetryClient` lors de la création de l'objet `luisPredictionOptions` dans `LuisHelper.cs` :

    ```cs
    var luisPredictionOptions = new LuisPredictionOptions()
    {
        TelemetryClient = telemetryClient,
    };

    var recognizer = new LuisRecognizer(luisApplication, luisPredictionOptions);
    ```

3. La dernière étape consiste à transmettre au client de télémétrie l’appel à `ExecuteLuisQuery` dans la méthode `ActStepAsync()` de `MainDialog.cs` :

    ```cs
    await LuisHelper.ExecuteLuisQuery(TelemetryClient, Configuration, Logger, stepContext.Context, cancellationToken)
    ```

Et voilà ! Vous disposez d’un robot fonctionnel qui enregistre les données de télémétrie dans Application Insights. Vous pouvez utiliser [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme) pour exécuter votre robot localement. Vous ne devriez pas voir les modifications apportées au comportement du robot, mais les informations de journalisation seront enregistrées dans Application Insights. Interagissez avec le robot en envoyant plusieurs messages et dans la section suivante, nous allons passer en revue les résultats de la télémétrie dans Application Insights.

Pour plus d’informations sur le test et le débogage de votre robot, vous pouvez consulter les articles suivants :

 * [Déboguer un bot](../bot-service-debug-bot.md)
 * [Recommandations en matière de test et de débogage](bot-builder-testing-debugging.md)
 * [Déboguer avec l’émulateur](../bot-service-debug-emulator.md)


## <a name="visualizing-your-telemetry-data-in-application-insights"></a>Visualisation des données de votre télémétrie dans Application Insights
Application Insights surveille la disponibilité, les performances et l’utilisation de votre application robot, qu’elle soit hébergée dans le cloud ou localement. Cette solution s’appuie sur la puissante plateforme d’analyse de données dans Azure Monitor pour vous fournir des informations détaillées sur le fonctionnement de votre application et pour diagnostiquer les erreurs sans attendre qu’un utilisateur les signale. Il existe plusieurs façons de consulter les données de télémétrie collectées par Application Insights, deux des principales méthodes s’effectuent via des requêtes et le tableau de bord. 

### <a name="querying-your-telemetry-data-in-application-insights-using-kusto-queries"></a>Interrogation des données de votre télémétrie dans Application Insights à l’aide des requêtes Kusto
Utilisez cette section comme point de départ pour apprendre à utiliser des requêtes de journal dans Application Insights. Elle illustre deux requêtes utiles et fournit des liens vers d’autres documents contenant des informations supplémentaires.

Pour interroger vos données

1. Accédez au [Portail Azure](https://portal.azure.com).
2. Naviguez vers Application Insights. Pour ce faire, la méthode la plus simple consiste à cliquer sur **Surveiller > Applications** et à y accéder. 
3. Une fois dans votre Application Insights, vous pouvez cliquer sur _Journaux (Analytics)_ dans la barre de navigation.

    ![Logs (Analytics)](media/AppInsights-LogView.png)

4. Cela fera apparaître la fenêtre Requête.  Entrez la requête suivante et sélectionnez _Exécuter_ :

    ```sql
    customEvents
    | where name=="WaterfallStart"
    | extend DialogId = customDimensions['DialogId']
    | extend InstanceId = tostring(customDimensions['InstanceId'])
    | join kind=leftouter (customEvents | where name=="WaterfallComplete" | extend InstanceId = tostring(customDimensions['InstanceId'])) on InstanceId    
    | summarize starts=countif(name=='WaterfallStart'), completes=countif(name1=='WaterfallComplete') by bin(timestamp, 1d), tostring(DialogId)
    | project Percentage=max_of(0.0, completes * 1.0 / starts), timestamp, tostring(DialogId) 
    | render timechart
    ```
5. Cette opération renvoie le pourcentage de boîtes de dialogue en cascade qui s’exécutent jusqu’à leur achèvement.

    ![Logs (Analytics)](media/AppInsights-Query-PercentCompleteDialog.png)


> [!TIP]
> Vous pouvez épingler une requête à votre tableau de bord Application Insights en sélectionnant le bouton en haut à droite du panneau **Journaux (Analytics)** . Il vous suffit de sélectionner le tableau de bord auquel vous voulez épingler la requête pour qu’elle soit disponible lors de votre prochaine visite de ce tableau de bord.


## <a name="the-application-insights-dashboard"></a>Le tableau de bord Application Insights

Chaque fois que vous créez une ressource Application Insights dans Azure, un nouveau tableau de bord est automatiquement créé et associé.  Vous pouvez consulter ce tableau de bord en sélectionnant le bouton en haut du panneau Application Insights, intitulé **Tableau de bord de l’application**. 

![Lien vers le tableau de bord de l’application](media/Application-Dashboard-Link.png)


Sinon, pour afficher les données, accédez au Portail Azure. Cliquez sur **Tableau de bord** à gauche, puis sélectionnez le tableau de bord désiré dans la liste déroulante.

Vous y trouverez des informations par défaut sur les performances de votre robot et des requêtes supplémentaires que vous avez épinglées à votre tableau de bord.



## <a name="additional-information"></a>Informations supplémentaires

* [Ajouter des données de télémétrie à votre bot QnAMaker](bot-builder-telemetry-qnamaker.md)

* [Présentation d’Application Insights](https://aka.ms/appinsights-overview)

* [Utilisation de la recherche dans Application Insights](https://aka.ms/search-in-application-insights)

* [Créer des tableaux de bord d’indicateurs de performance clé à l’aide d’Azure Application Insights](https://aka.ms/custom-kpi-dashboards-application-insights)


<!--
The easiest way to test is by creating a dashboard using [Azure portal's template deployment page](https://portal.azure.com/#create/Microsoft.Template).
- Click ["Build your own template in the editor"]
- Copy and paste either one of these .json file that is provided to help you create the dashboard:
  - [System Health Dashboard](https://aka.ms/system-health-appinsights)
  - [Conversation Health Dashboard](https://aka.ms/conversation-health-appinsights)
- Click "Save"
- Populate `Basics`: 
   - Subscription: <your test subscription>
   - Resource group: <a test resource group>
   - Location: <such as West US>
- Populate `Settings`:
   - Insights Component Name: <like `core672so2hw`>
   - Insights Component Resource Group: <like `core67`>
   - Dashboard Name:  <like `'ConversationHealth'` or `SystemHealth`>
- Click `I agree to the terms and conditions stated above`
- Click `Purchase`
- Validate
   - Click on [`Resource Groups`](https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fsubscriptions%2FresourceGroups)
   - Select your Resource Group from above (like `core67`).
   - If you don't see a new Resource, then look at "Deployments" and see if any have failed.
   - Here's what you typically see for failures:
     
```json
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug-deployment for usage details.","details":[{"code":"BadRequest","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidTemplate\",\r\n \"message\": \"Unable to process template language expressions for resource '/subscriptions/45d8a30e-3363-4e0e-849a-4bb0bbf71a7b/resourceGroups/core67/providers/Microsoft.Portal/dashboards/Bot Analytics Dashboard' at line '34' and column '9'. 'The template parameter 'virtualMachineName' is not found. Please see https://aka.ms/arm-template-structure for usage details.'\"\r\n }\r\n}"}]}
```
-->

<!--
## Additional information

### Customize your telemetry client

If you want to customize your telemetry to log into a separate service, you have to configure the system differently. If using Application Insights to do so, download the package `Microsoft.Bot.Builder.ApplicationInsights` via NuGet, or use npm to install `botbuilder-applicationinsights`. Details on getting the Application Insights keys can be found [here](../bot-service-resources-app-insights-keys.md). Otherwise, include what is necessary for logging to that service, then follow the section below.

**Use Custom Telemetry**
If you want to log telemetry events generated by the Bot Framework into a completely separate system, create a new class derived from the base interface `IBotTelemetryClient` and configure. Then, when adding your telemetry client as above, just inject your custom client. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    // Add the telemetry client.
    services.AddSingleton<IBotTelemetryClient, CustomTelemetryClient>();
    ...
}
```

### Add custom logging to your bot

Once the Bot has the new telemetry logging support configured, you can begin adding telemetry to your bot.  The `BotTelemetryClient`(in C#, `IBotTelemetryClient`) has several methods to log distinct types of events.  Choosing the appropriate type of event enables you to take advantage of Application Insights existing reports (if you are using Application Insights).  For general scenarios `TraceEvent` is typically used.  The data logged using `TraceEvent` lands in the `CustomEvent` table in Kusto.

If using a Dialog within your Bot, every Dialog-based object (including Prompts) will contain a new `TelemetryClient` property.  This is the `BotTelemetryClient` that enables you to perform logging.  This is not just a convenience, we'll see later in this article if this property is set, `WaterfallDialogs` will generate events.

### Details of telemetry options

There are three main components available for your bot to log telemetry, and each component has customization available for logging your own events, which are discussed in this section. 

- A  [Bot Framework Middleware component](#telemetry-middleware) (*TelemetryLoggerMiddleware*) that will log when messages are received, sent, updated or deleted. You can override for custom logging.
- [*LuisRecognizer* class.](#telemetry-support-luis)  You can override for custom logging in two ways - per invocation (add/replace properties) or derived classes.
- [*QnAMaker*  class.](#telemetry-qnamaker)  You can override for custom logging in two ways - per invocation (add/replace properties) or derived classes.

All components log using the `IBotTelemetryClient`  (or `BotTelemetryClient` in node.js) interface which can be overridden with a custom implementation.

#### Telemetry Middleware

|C#  | JavaScript |
|:-----|:------------|
|**Microsoft.Bot.Builder.TelemetryLoggerMiddleware** | **botbuilder-core** |

##### Out of box usage

The TelemetryLoggerMiddleware is a Bot Framework component that can be added without modification, and it will perform logging that enables out of the box reports that ship with the Bot Framework SDK. 

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, TelemetryLoggerMiddleware>();

// Create the Bot Framework Adapter with error handling enabled.
services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();
```

And in our adapter, we would specify the use of middleware:

```csharp
public AdapterWithErrorHandler(ICredentialProvider credentialProvider, ILogger<BotFrameworkHttpAdapter> logger, IMiddleware middleware, ConversationState conversationState = null)
           : base(credentialProvider)
{
    ...
    Use(middleware);
    ...
}
```

##### Adding properties
If you decide to add additional properties, the TelemetryLoggerMiddleware class can be derived.  For example, if you would like to add the property "MyImportantProperty" to the `BotMessageReceived` event.  `BotMessageReceived` is logged when the user sends a message to the bot.  Adding the additional property can be accomplished in the following way:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    protected override Task OnReceiveActivityAsync(
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

In Startup, we would add the new class:

```csharp
// Create the telemetry middleware to track conversation events
services.AddSingleton<IMiddleware, MyTelemetryMiddleware>();
```

##### Completely replacing properties / Additional event(s)

If you decide to completely replace properties being logged, the `TelemetryLoggerMiddleware` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`BotMessageSend` properties and send multiple events, the following demonstrates how this could be performed:

```csharp
class MyTelemetryMiddleware : TelemetryLoggerMiddleware
{
    ...
    protected override Task OnReceiveActivityAsync(
                  Activity activity,
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
Note: When the standard properties are not logged, it will cause the out of box reports shipped with the product to stop working.

##### Events Logged from Telemetry Middleware
[BotMessageSend](bot-builder-telemetry-reference.md#customevent-botmessagesend)
[BotMessageReceived](bot-builder-telemetry-reference.md#customevent-botmessagereceived)
[BotMessageUpdate](bot-builder-telemetry-reference.md#customevent-botmessageupdate)
[BotMessageDelete](bot-builder-telemetry-reference.md#customevent-botmessagedelete)

#### Telemetry support LUIS 

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.Luis** | **botbuilder-ai** |

##### Out of box usage
The LuisRecognizer is an existing Bot Framework component, and telemetry can be enabled by passing a IBotTelemetryClient interface through `luisOptions`.  You can override the default properties being logged and log new events as required.

During construction of `luisOptions`, an `IBotTelemetryClient` object must be provided for this to work.

```csharp
var luisPredictionOptions = new LuisPredictionOptions()
{
    TelemetryClient = telemetryClient
};
var recognizer = new LuisRecognizer(luisApplication, luisPredictionOptions);
```

##### Adding properties
If you decide to add additional properties, the `LuisRecognizer` class can be derived.  For example, if you would like to add the property "MyImportantProperty" to the `LuisResult` event.  `LuisResult` is logged when a LUIS prediction call is performed.  Adding the additional property can be accomplished in the following way:

```csharp
class MyLuisRecognizer : LuisRecognizer 
{
   ...
   protected override Task OnRecognizerResultAsync(
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

##### Add properties per invocation
Sometimes it's necessary to add additional properties during the invocation:
```csharp
var additionalProperties = new Dictionary<string, string>
{
   { "dialogId", "myDialogId" },
   { "conversationInfo", "myConversationInfo" },
};

var result = await recognizer.RecognizeAsync(turnContext,
     additionalProperties).ConfigureAwait(false);
```

##### Completely replacing properties / Additional event(s)
If you decide to completely replace properties being logged, the `LuisRecognizer` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`LuisResult` properties and send multiple events, the following demonstrates how this could be performed:

```csharp
class MyLuisRecognizer : LuisRecognizer
{
    ...
    protected override Task OnRecognizerResultAsync(
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
Note: When the standard properties are not logged, it will cause the Application Insights out of box reports shipped with the product to stop working.

##### Events Logged from TelemetryLuisRecognizer
[LuisResult](bot-builder-telemetry-reference.md#customevent-luisevent)

### Telemetry QnAMaker

|C#  | JavaScript |
|:-----|:------------|
| **Microsoft.Bot.Builder.AI.QnA** | **botbuilder-ai** |


##### Out of box usage
The QnAMaker class is an existing Bot Framework component that adds two additional constructor parameters which enable logging that enable out of the box reports that ship with the Bot Framework SDK. The new `telemetryClient` references a `IBotTelemetryClient` interface which performs the logging.  

```csharp
var qna = new QnAMaker(endpoint, options, client, 
                       telemetryClient: telemetryClient,
                       logPersonalInformation: true);
```
##### Adding properties 
If you decide to add additional properties, there are two methods of doing this - when properties need to be added during the QnA call to retrieve answers or deriving from the `QnAMaker` class.  

The following demonstrates deriving from the `QnAMaker` class.  The example shows adding the property "MyImportantProperty" to the `QnAMessage` event.  The`QnAMessage` event is logged when a QnA `GetAnswers`call is performed.  In addition, we log a second event "MySecondEvent".

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

##### Adding properties during GetAnswersAsync
If you have properties that need to be added during runtime, the `GetAnswersAsync` method can provide properties and/or metrics to add to the event.

For example, if you want to add a `dialogId` to the event, it can be done like the following:
```csharp
var telemetryProperties = new Dictionary<string, string>
{
   { "dialogId", myDialogId },
};

var results = await qna.GetAnswersAsync(context, opts, telemetryProperties);
```
The `QnaMaker` class provides the capability of overriding properties, including PersonalInfomation properties.

##### Completely replacing properties / Additional event(s)
If you decide to completely replace properties being logged, the `TelemetryQnAMaker` class can be derived (like above when extending properties).   Similarly, logging new events is performed in the same way.

For example, if you would like to completely replace the`QnAMessage` properties, the following demonstrates how this could be performed:

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
Note: When the standard properties are not logged, it will cause the out of box reports shipped with the product to stop working.

##### Events Logged from TelemetryLuisRecognizer
[QnAMessage](bot-builder-telemetry-reference.md#customevent-qnamessage)

### All other events

A full list of events logged for your bot's telemetry can be found on the [telemetry reference page](bot-builder-telemetry-reference.md).

#### Identifiers and Custom Events

When logging events into Application Insights, the events generated contain default properties that you won't have to fill.  For example, `user_id` and `session_id`properties are contained in each Custom Event (generated with the `TraceEvent` API).  In addition, `activitiId`, `activityType` and `channelId` are also added.

> [!NOTE]
> Custom telemetry clients will not be provided these values.


Property |Type | Details
--- | --- | ---
`user_id`| `string` | [ChannelID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) + [From.Id](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from)
`session_id`| `string`|  [ConversationID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation)
`customDimensions.activityId`| `string` | [The bot activity ID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id)
`customDimensions.activityType` | `string` | [The bot activity type](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
`customDimensions.channelId` | `string` |  [The bot activity channel ID](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id)
-->