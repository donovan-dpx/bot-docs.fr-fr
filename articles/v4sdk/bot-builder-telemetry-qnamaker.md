---
title: Ajouter la télémétrie à votre bot QnA - Bot Service
description: Découvrez comment intégrer les nouvelles fonctionnalités de télémétrie à votre bot QnA Maker.
keywords: télémétrie, appinsights, Application Insights, superviser le bot, QnA Maker
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d73bd8c26dde8826b145108268417a24840ed83e
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791241"
---
# <a name="add-telemetry-to-your-qnamaker-bot"></a>Ajouter des données de télémétrie à votre bot QnAMaker

[!INCLUDE[applies-to](../includes/applies-to.md)]


La journalisation de télémétrie a été ajoutée à la version 4.2 du Kit de développement logiciel (SDK) de Bot Framework.  Elle permet aux applications de robot d’envoyer des données d’événement à des services de télémétrie comme [Application Insights](https://aka.ms/appinsights-overview). La télémétrie offre des aperçus de votre robot en indiquant les fonctionnalités qui sont utilisées le plus, détecte le comportement indésirable et offre une visibilité sur la disponibilité, les performances et l’utilisation.

Deux nouveaux composants ont été ajoutés au kit SDK Bot Framework, qui activent la journalisation de la télémétrie dans les bots QnA Maker : `TelemetryLoggerMiddleware` et la classe `QnAMaker`. `TelemetryLoggerMiddleware` est un composant middleware (intergiciel) qui journalise chaque fois que des messages sont reçus, envoyés, mis à jour ou supprimés, tandis que la classe « QnAMaker » fournit une journalisation personnalisée qui étend des fonctionnalités de télémétrie.

Cet article porte sur les points suivants :

* Code nécessaire au raccordement de la télémétrie à votre bot 

* Code nécessaire à l’activation de la journalisation QnA prête à l’emploi et des rapports qui utilisent les propriétés d’événement standard. 

* Modification ou extension des propriétés d’événement par défaut du kit SDK pour répondre à un large éventail de besoins en matière de rapports.


## <a name="prerequisites"></a>Conditions préalables requises

* L’[exemple de code QnA Maker](https://aka.ms/cs-qna)

* Un abonnement à [Microsoft Azure](https://portal.azure.com/)

* Une [clé Application Insights](../bot-service-resources-app-insights-keys.md)

* Une connaissance de [QnA Maker](https://qnamaker.ai/) est utile.

* Un compte [QnA Maker](https://aka.ms/create-qna-maker).

* Une base de connaissances QnA Maker publiée. Si ce n’est pas le cas, suivez les étapes du tutoriel [Créer et répondre à partir de la base de connaissances](https://aka.ms/create-publish-query-in-portal) pour créer une base de connaissances QnA Maker avec des questions et des réponses.

> [!NOTE]
> Cet article s’appuie sur l’[exemple de code QnA Maker ](https://aka.ms/cs-qna) pour vous présenter les étapes de l’incorporation de la télémétrie. 

## <a name="wiring-up-telemetry-in-your-qna-maker-bot"></a>Raccordement de la télémétrie à votre bot QnA Maker

Nous allons commencer par l’[exemple d’application QnA Maker](https://aka.ms/cs-qna), auquel nous allons ajouter le code nécessaire pour intégrer des données de télémétrie dans un bot à l’aide du service QnA Maker. Cela permet à Application Insights de commencer à suivre les requêtes.

1. Ouvrez l’[exemple d’application QnA Maker](https://aka.ms/cs-qna) dans Visual Studio.

2. Ajoutez le package NuGet `Microsoft.Bot.Builder.Integration.ApplicationInsights.Core `. Pour plus d’informations sur l’utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs) :

3. Ajoutez les instructions suivantes dans `Startup.cs` :
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.Bot.Builder.ApplicationInsights;
    using Microsoft.Bot.Builder.Integration.ApplicationInsights.Core;
    ```

    > [!NOTE] 
    > Si vous suivez la procédure en mettant à jour l’exemple de code QnA Maker, vous remarquerez que l’instruction d’utilisation pour `Microsoft.Bot.Builder.Integration.AspNet.Core` existe déjà dans l’exemple QnA Maker.

4. Ajoutez le code suivant à la méthode `ConfigureServices()` dans `Startup.cs`. Les services de télémétrie sont ainsi rendus disponibles pour votre bot via [l’injection de dépendances (DI)](https://aka.ms/asp.net-core-dependency-interjection) :
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
    
    > [!NOTE] 
    > Si vous procédez comme suit pour mettre à jour l’exemple de code QnA Maker, vous remarquerez que `services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();` existe déjà.

5. Demandez à l’adaptateur d’utiliser le code de l’intergiciel qui a été ajouté à la méthode `ConfigureServices()`. Ouvrez `AdapterWithErrorHandler.cs` et ajoutez `IMiddleware middleware` à la liste des paramètres des constructeurs. Ajoutez l’instruction `Use(middleware);` comme dernière ligne dans le constructeur :
    ```csharp
    public AdapterWithErrorHandler(ICredentialProvider credentialProvider, ILogger<BotFrameworkHttpAdapter> logger, IMiddleware middleware, ConversationState conversationState = null)
            : base(credentialProvider)
    {
        ...

        Use(middleware);
    }
    ```

6. Ajoutez la clé d’instrumentation Application Insights à votre fichier `appsettings.json`. Le fichier `appsettings.json` contient des métadonnées sur les services externes que le bot utilise quand il s’exécute. Par exemple, les métadonnées de CosmosDB, d’Application Insights et de la connexion de service QnA Maker y sont stockés. L’ajout à votre fichier `appsettings.json` doit être au format suivant :

    ```json
    {
        "MicrosoftAppId": "",
        "MicrosoftAppPassword": "",
        "QnAKnowledgebaseId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "QnAEndpointKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "QnAEndpointHostName": "https://xxxxxxxx.azurewebsites.net/qnamaker",
        "ApplicationInsights": {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        }
    }
    ```
   > [!Note] 
   > 
   > * Pour plus d’informations sur l’obtention de la _clé d’instrumentation Application Insights_, consultez l’article [clés Application Insights](../bot-service-resources-app-insights-keys.md).
    >
    > * Vous devez déjà disposer d’un compte [QnA Maker](https://aka.ms/create-qna-maker) ; le cas échéant, les informations permettant d’obtenir les valeurs QnA de l’ID de la base de connaissances, de la clé du point de terminaison et du nom d’hôte se trouvent [ici](https://aka.ms/bot-framework-emulator-qna-keys).
    > 


À ce stade, le travail préliminaire d’activation de la télémétrie à l’aide d’Application Insights est effectué.  Vous pouvez exécuter votre robot localement à l’aide de l’émulateur de robot, puis passer à Application Insights pour voir ce qui est enregistré, comme le temps de réponse, l’intégrité globale de l’application et les informations d’exécution générales. 

> [!TIP] 
> Pour plus d’informations sur l’activation/désactivation de la journalisation des événements d’activité et des informations personnelles, consultez [Ajouter des données de télémétrie à votre bot](bot-builder-telemetry.md#enabling-or-disabling-activity-logging)

Nous verrons ensuite ce qui doit être inclus pour ajouter la fonctionnalité de télémétrie au service QnA Maker. 


## <a name="enabling-telemetry-to-capture-usage-data-from-the-qna-maker-service"></a>Activation de la télémétrie pour capturer les données d’utilisation à partir du service QnA Maker

Le service QnA Maker dispose d’une journalisation de télémétrie intégrée, il est donc très facile de parvenir à obtenir des données de télémétrie à partir de QnA Maker.  Tout d’abord, nous allons voir comment incorporer la télémétrie dans le code QnA Maker pour activer la journalisation de la télémétrie intégrée, puis nous découvrirons comment remplacer ou ajouter des propriétés supplémentaires aux données d’événement existantes pour répondre à un large éventail de besoins en matière de rapports.

### <a name="enabling-default-qna-logging"></a>Activation de la journalisation QnA par défaut

1. Créez un champ readonly privé de type `IBotTelemetryClient` dans votre classe `QnABot` du fichier `QnABot.cs` :

    ```cs
    public class QnABot : ActivityHandler
        {
            private readonly IBotTelemetryClient _telemetryClient;
            ...
   }
    ```

2. Ajoutez un paramètre `IBotTelemetryClient` à votre constructeur de classe `QnABot` dans `QnABot.cs` et affectez sa valeur au champ privé créé à l’étape précédente :

    ```cs
    public QnABot(IConfiguration configuration, ILogger<QnABot> logger, IHttpClientFactory httpClientFactory, IBotTelemetryClient telemetryClient)
    {
        ...
        _telemetryClient = telemetryClient;
    }
    ```

3. Le paramètre _`telemetryClient`_ est nécessaire lors de l’instanciation du nouvel objet QnAMaker dans `QnABot.cs` :

    ```cs
    var qnaMaker = new QnAMaker(new QnAMakerEndpoint
                {
                    KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
                    EndpointKey = _configuration["QnAEndpointKey"],
                    Host = _configuration["QnAEndpointHostName"]
                },
                null,
                httpClient,
                _telemetryClient);
    ```

    > [!TIP] 
    > Assurez-vous que les noms de propriété que vous utilisez dans les entrées `_configuration` correspondent aux noms de propriétés que vous avez utilisés dans le fichier AppSettings.json, et que les valeurs de ces propriétés sont obtenues en sélectionnant le bouton _View Code_ (Voir le code) dans https://www.qnamaker.ai/Home/MyServices:

        
    ![AppSettings](media/AppSettings.json-QnAMaker.png)

#### <a name="viewing-telemetry-data-logged-from-the-qna-maker-default-entries"></a>Affichage des données de télémétrie enregistrées à partir des entrées QnA Maker par défaut
Vous pouvez voir les résultats de l’utilisation de votre bot QnA Maker dans Application Insights, après son exécution dans l’émulateur de bot, en suivant ces étapes :

1. Accédez au [Portail Azure](https://portal.azure.com/).

2. Accédez à votre instance Application Insights en cliquant sur __Surveillance > Applications__.

3. Une fois dans Application Insights, cliquez sur _Journaux (analytique)_ dans la barre de navigation, comme montré ci-dessous :

    ![Log Analytics](media/AppInsights-LogView-QnaBot.png)

4. Entrez la requête Kusto suivante, puis sélectionnez _Exécuter_

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend answer = tostring(customDimensions.answer)
    | summarize count() by answer
    ```
5. Laissez cette page ouverte dans votre navigateur, nous y reviendrons après l’ajout d’une nouvelle propriété personnalisée.

> [!TIP]
> Si vous débutez avec le langage de requête Kusto, qui est utilisé pour écrire des requêtes de journal dans Azure Monitor, mais que vous connaissez le langage de requête SQL, l’[Aide-mémoire sur les requêtes de journal SQL vers Azure Monitor](https://aka.ms/azureMonitor-SQL-cheatsheet) peut vous être utile. 

### <a name="modifying-or-extending-the-default-event-properties"></a>Modification ou extension des propriétés d’événement par défaut
Si vous avez besoin de propriétés qui ne sont pas définies dans la classe `QnAMaker`, deux possibilités s’offrent à vous, l’une comme l’autre nécessitent la création de votre propre classe dérivée de la classe `QnAMaker`. La première est expliquée dans la section ci-dessous intitulée [Ajout de propriétés](#adding-properties), et dans laquelle vous ajoutez des propriétés à l’événement `QnAMessage` existant. La seconde méthode vous permet de créer des événements auxquels vous pouvez ajouter des propriétés, comme décrit dans [Ajout de nouveaux événements avec des propriétés personnalisées](#adding-new-events-with-custom-properties).  

> [!Note]
> L’événement `QnAMessage` fait partie du kit SDK Bot Framework et fournit toutes les propriétés d’événement prêtes à l’emploi qui sont consignées dans Application Insights.



#### <a name="adding-properties"></a>Ajout de propriétés 

La façon dont vous pouvez faire dériver la classe `QnAMaker` est illustrée ci-après.  Cet exemple montre l’ajout de la propriété « MyImportantProperty » à l’événement `QnAMessage`.  L’événement `QnAMessage` est enregistré chaque fois qu’un appel QnA [GetAnswers](https://aka.ms/namespace-QnAMaker-GetAnswersAsync) est effectué.  

Après avoir appris à ajouter des propriétés personnalisées, nous allons voir comment créer un événement personnalisé et lui associer des propriétés. Nous exécuterons ensuite le bot localement à l’aide de Bot Framework Emulator pour voir ce qui est consigné dans Application Insights par le biais du langage de requête Kusto.

1. Créez une nouvelle classe nommée `MyQnAMaker` dans l’espace de noms `Microsoft.BotBuilderSamples` qui hérite de la classe `QnAMaker` et enregistrez-la sous `MyQnAMaker.cs`. Pour hériter de la classe `QnAMaker`, vous devez ajouter l’instruction using `Microsoft.Bot.Builder.AI.QnA`. Votre code doit s’afficher comme suit :


    ```cs
    using Microsoft.Bot.Builder.AI.QnA;

    namespace Microsoft.BotBuilderSamples
    {
        public class MyQnAMaker : QnAMaker
        {

        }
    }
    ```
2. Ajoutez un constructeur de classe dans `MyQnAMaker`. Notez que vous aurez besoin de deux instructions using supplémentaires pour les paramètres des constructeurs `System.Net.Http` et `Microsoft.Bot.Builder` :

    ```cs
    ...
    using Microsoft.Bot.Builder.AI.QnA;
    using System.Net.Http;
    using Microsoft.Bot.Builder;

    namespace Microsoft.BotBuilderSamples
    {
        public class MyQnAMaker : QnAMaker
        {
            public MyQnAMaker(
                QnAMakerEndpoint endpoint,
                QnAMakerOptions options = null,
                HttpClient httpClient = null,
                IBotTelemetryClient telemetryClient = null,
                bool logPersonalInformation = false)
                : base(endpoint, options, httpClient, telemetryClient, logPersonalInformation)
            {

            }
        } 
    }  
    ```
3. Ajoutez la nouvelle propriété à l’événement QnAMessage après le constructeur, et incluez les instructions `System.Collections.Generic`, `System.Threading` et `System.Threading.Tasks` :

    ```cs
    using Microsoft.Bot.Builder.AI.QnA;
    using System.Net.Http;
    using Microsoft.Bot.Builder;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
 
    namespace Microsoft.BotBuilderSamples
    {
            public class MyQnAMaker : QnAMaker
            {
            ...

            protected override async Task OnQnaResultsAsync(
                                QueryResult[] queryResults,
                                Microsoft.Bot.Builder.ITurnContext turnContext,
                                Dictionary<string, string> telemetryProperties = null,
                                Dictionary<string, double> telemetryMetrics = null,
                                CancellationToken cancellationToken = default(CancellationToken))
            {
                var eventData = await FillQnAEventAsync(
                                        queryResults,
                                        turnContext,
                                        telemetryProperties,
                                        telemetryMetrics,
                                        cancellationToken)
                                    .ConfigureAwait(false);

                // Add new property
                eventData.Properties.Add("MyImportantProperty", "myImportantValue");

                // Log QnAMessage event
                TelemetryClient.TrackEvent(
                                QnATelemetryConstants.QnaMsgEvent,
                                eventData.Properties,
                                eventData.Metrics
                                );
            }

        } 
    }    
    ```

4. Modifiez votre bot pour utiliser la nouvelle classe, au lieu de créer un objet `QnAMaker` vous allez créer un objet `MyQnAMaker` dans `QnABot.cs` :

    ```cs
    var qnaMaker = new MyQnAMaker(new QnAMakerEndpoint
                {
                    KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
                    EndpointKey = _configuration["QnAEndpointKey"],
                    Host = _configuration["QnAEndpointHostName"]
                },
                null,
                httpClient,
                _telemetryClient);
    ```

##### <a name="viewing-telemetry-data-logged-from-the-new-property-_myimportantproperty_"></a>Affichage des données de télémétrie enregistrées à partir de la nouvelle propriété _MyImportantProperty_
Après avoir exécuté votre bot dans l’émulateur, vous pouvez voir les résultats dans Application Insights en suivant ces étapes :

1. Revenez à votre navigateur sur lequel la vue _Logs (Analytics)_ est active.

2. Entrez la requête Kusto suivante et sélectionnez _Exécuter_.  Le nombre de fois que la nouvelle propriété a été exécutée y est renseigné :

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend MyImportantProperty = tostring(customDimensions.MyImportantProperty)
    | summarize count() by MyImportantProperty
    ```

3. Pour afficher les détails plutôt que le nombre, supprimez la dernière ligne et réexécutez la requête :

    ```SQL
    customEvents
    | where name == 'QnaMessage'
    | extend MyImportantProperty = tostring(customDimensions.MyImportantProperty)
    ```
### <a name="adding-new-events-with-custom-properties"></a>Ajout de nouveaux événements avec des propriétés personnalisées
Si vous devez enregistrer des données dans un autre événement que `QnaMessage`, vous pouvez créer votre propre événement personnalisé doté de ses propriétés spécifiques.  Pour ce faire, nous allons ajouter du code à la fin de la classe `MyQnAMaker`, comme suit :

```CS
public class MyQnAMaker : QnAMaker
{
    ...

    // Create second event.
    var secondEventProperties = new Dictionary<string, string>();

    // Create new property for the second event.
    secondEventProperties.Add(
                        "MyImportantProperty2",
                        "myImportantValue2");

    // Log secondEventProperties event
    TelemetryClient.TrackEvent(
                    "MySecondEvent",
                    secondEventProperties);

} 
```                            
## <a name="the-application-insights-dashboard"></a>Le tableau de bord Application Insights

Chaque fois que vous créez une ressource Application Insights dans Azure, un nouveau tableau de bord est automatiquement créé et associé.  Vous pouvez consulter ce tableau de bord en sélectionnant le bouton en haut du panneau Application Insights, intitulé **Tableau de bord de l’application**. 

![Lien vers le tableau de bord de l’application](media/Application-Dashboard-Link.png)


Sinon, pour afficher les données, accédez au Portail Azure. Cliquez sur **Tableau de bord** à gauche, puis sélectionnez le tableau de bord désiré dans la liste déroulante.

Vous y trouverez des informations par défaut sur les performances de votre bot, et des requêtes supplémentaires que vous avez épinglées à votre tableau de bord.


## <a name="additional-information"></a>Informations supplémentaires

* [Ajouter la télémétrie à votre bot](bot-builder-telemetry.md)

* [Présentation d’Application Insights](https://aka.ms/appinsights-overview)

* [Utilisation de la recherche dans Application Insights](https://aka.ms/search-in-application-insights)

* [Créer des tableaux de bord d’indicateurs de performance clé à l’aide d’Azure Application Insights](https://aka.ms/custom-kpi-dashboards-application-insights)
