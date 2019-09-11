---
title: Aide-mémoire sur la migration de .NET v3 vers v4 | Microsoft Docs
description: Liste des principales différences entre la version v3 et la version v4 du SDK .NET Bot Framework.
keywords: .net, migration bot, dialogues, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3ede676cd1a09566b42dc49cc3258aa6e42cbe05
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298948"
---
# <a name="net-migration-quick-reference"></a>Aide-mémoire sur la migration .NET

Le SDK .NET BotBuilder v4 comprend des changements fondamentaux qui affectent la façon dont les bots sont créés. L’objectif de ce guide est de vous permettre de comprendre rapidement les différences qui existent entre les SDK v3 et v4 au niveau de l’exécution des tâches.

- La façon dont les informations sont transmises entre un bot et les canaux a changé. Dans la version v3, vous utilisiez l’objet _Conversation_ et la méthode _SendAsync_ pour traiter un message, et Autofac était souvent utilisé pour charger les différentes dépendances. Dans la version v4, vous utilisez les objets _Adapter_ et _TurnContext_ pour traiter un message, et vous pouvez utiliser la bibliothèque d’injection de dépendances de votre choix.

- Par ailleurs, les dialogues et les instances de bot ont fait l’objet d’un découplage supplémentaire. Dans la version v3, les dialogues étaient intégrés au SDK principal et la pile était traitée en interne, tandis que les dialogues enfants étaient chargés avec les méthodes _Call_ et _Forward_. Dans la version v4, vous passez désormais les dialogues dans des instances de bot en tant qu’arguments, ce qui permet une plus grande flexibilité de composition et un meilleur contrôle du développeur sur la pile de dialogues. Les dialogues enfants sont chargés avec les méthodes _BeginDialogAsync_ et _ReplaceDialogAsync_.

- La version v4 fournit aussi une classe `ActivityHandler`, qui aide à automatiser la gestion des différents types d’activités, comme les activités _message_, _conversation update_ et _event_.

Ces améliorations impliquent des changements au niveau de la syntaxe de développement des bots dans .NET, notamment au niveau de la création d’objets bot, de la définition de dialogues et de l’encodage d’une logique de gestion des événements.

Le reste de cette rubrique compare les constructions du SDK .NET Bot Framework v3 à leurs équivalents dans la version v4.

## <a name="to-process-incoming-messages"></a>Pour traiter les messages entrants

### <a name="v3"></a>v3

```cs
[BotAuthentication]
public class MessagesController : ApiController
{
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
        if (activity.GetActivityType() == ActivityTypes.Message)
        {
            await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }
}
```

### <a name="v4"></a>v4

```cs
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter Adapter;
    private readonly IBot Bot;

    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        Adapter = adapter;
        Bot = bot;
    }

    [HttpPost]
    public async Task PostAsync()
    {
        await Adapter.ProcessAsync(Request, Response, Bot);
    }
}
```

## <a name="to-send-a-message-to-a-user"></a>Pour envoyer un message à un utilisateur

### <a name="v3"></a>v3

```cs
await context.PostAsync("Hello and welcome to the help desk bot.");
```

### <a name="v4"></a>v4

```cs
await turnContext.SendActivityAsync("Hello and welcome to the help desk bot.");
```

## <a name="to-load-a-root-dialog"></a>Pour charger un dialogue racine

### <a name="v3"></a>v3

```cs
await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
```

### <a name="v4"></a>v4

```cs
// Create a DialogExtensions class with a Run method.
public static class DialogExtensions
{
    public static async Task Run(
        this Dialog dialog,
        ITurnContext turnContext,
        IStatePropertyAccessor<DialogState> accessor,
        CancellationToken cancellationToken)
    {
        var dialogSet = new DialogSet(accessor);
        dialogSet.Add(dialog);

        var dialogContext = await dialogSet.CreateContextAsync(turnContext, cancellationToken);

        var results = await dialogContext.ContinueDialogAsync(cancellationToken);
        if (results.Status == DialogTurnStatus.Empty)
        {
            await dialogContext.BeginDialogAsync(dialog.Id, null, cancellationToken);
        }
    }
}

// Call it from the ActivityHandler's OnMessageActivityAsync override
protected override async Task OnMessageActivityAsync(
    ITurnContext<IMessageActivity> turnContext,
    CancellationToken cancellationToken)
{
    // Run the Dialog with the new message Activity.
    await Dialog.Run(
        turnContext,
        ConversationState.CreateProperty<DialogState>("DialogState"),
        cancellationToken);
}
```

## <a name="to-start-a-child-dialog"></a>Pour démarrer un dialogue enfant

### <a name="v3"></a>v3

```cs
context.Call(new NextDialog(), this.ResumeAfterNextDialog);
```

or

```cs
await context.Forward(new NextDialog(), this.ResumeAfterNextDialog, message);
```

### <a name="v4"></a>v4

```cs
dialogContext.BeginDialogAsync("<child-dialog-id>", options);
```

or

```cs
dialogContext.ReplaceDialogAsync("<child-dialog-id>", options);
```

## <a name="to-end-a-dialog"></a>Pour terminer un dialogue

### <a name="v3"></a>v3

```cs
context.Done(ReturnValue);
```

### <a name="v4"></a>v4

```cs
await context.EndDialogAsync(ReturnValue);
```

## <a name="to-prompt-a-user-for-input"></a>Pour inviter l’utilisateur à entrer du texte

### <a name="v3"></a>v3

```cs
PromptDialog.Choice(
    context,
    this.OnOptionSelected,
    Options, PromptMessage,
    ErrorMessage,
    3,
    PromptStyle.PerLine);
```

### <a name="v4"></a>v4

```cs
// In the dialog's constructor, register the prompt, and waterfall steps.
AddDialog(new TextPrompt(nameof(TextPrompt)));
AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
{
    FirstStepAsync,
    SecondStepAsync,
}));

// The initial child Dialog to run.
InitialDialogId = nameof(WaterfallDialog);

// ...

// In the first step, invoke the prompt.
private async Task<DialogTurnResult> FirstStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    return await stepContext.PromptAsync(
        nameof(TextPrompt),
        new PromptOptions { Prompt = MessageFactory.Text("Please enter your destination.") },
        cancellationToken);
}

// In the second step, retrieve the Result from the stepContext.
private async Task<DialogTurnResult> SecondStepAsync(
    WaterfallStepContext stepContext,
    CancellationToken cancellationToken)
{
    var destination = (string)stepContext.Result;
}
```

## <a name="to-save-information-to-dialog-state"></a>Pour enregistrer des informations dans l’état d’un dialogue

### <a name="v3"></a>v3

Tous les dialogues, ainsi que leurs champs, étaient sérialisés automatiquement dans la version V3.

### <a name="v4"></a>v4

```cs
// StepContext values are auto-serialized in V4, and scoped to the dialog.
stepContext.values.destination = destination;
```

## <a name="to-write-changes-in-state-to-the-persistance-layer"></a>Pour écrire des changements d’état dans la couche de persistance

### <a name="v3"></a>v3

Par défaut, les données d’état sont enregistrées automatiquement à la fin du tour.

### <a name="v4"></a>v4

```cs
// You now must explicitly save state changes before the end of the turn.
await this.conversationState.saveChanges(context, false);
await this.userState.saveChanges(context, false);
```

## <a name="to-create-and-register-state-storage"></a>Pour créer et inscrire un stockage d’état

### <a name="v3"></a>v3

```cs
// Autofac was used internally by the sdk, and state was automatic.
Conversation.UpdateContainer(
builder =>
{
    builder.RegisterModule(new AzureModule(Assembly.GetExecutingAssembly()));
    var store = new InMemoryDataStore();
    builder.Register(c => store)
        .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
        .AsSelf()
        .SingleInstance();
});
```

### <a name="v4"></a>v4

```cs
// Create the storage we'll be using for User and Conversation state.
// In-memory storage is great for testing purposes.
services.AddSingleton<IStorage, MemoryStorage>();

// Create the user state (used in this bot's Dialog implementation).
services.AddSingleton<UserState>();

// Create the conversation state (used by the Dialog system itself).
services.AddSingleton<ConversationState>();

// The dialog that will be run by the bot.
services.AddSingleton<MainDialog>();

// Create the bot as a transient. In this case the ASP.NET controller is expecting an IBot.
services.AddTransient<IBot, DialogBot>();

// In the bot's ActivityHandler implementation, call SaveChangesAsync after the OnTurnAsync completes.
public class DialogBot : ActivityHandler
{
    protected readonly Dialog Dialog;
    protected readonly BotState ConversationState;
    protected readonly BotState UserState;

    public DialogBot(ConversationState conversationState, UserState userState, Dialog dialog)
    {
        ConversationState = conversationState;
        UserState = userState;
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        // Save any state changes that might have ocurred during the turn.
        await ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
        await UserState.SaveChangesAsync(turnContext, false, cancellationToken);
    }

    protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
    {
        // Run the dialog, passing in the message activity for this turn.
        await Dialog.Run(turnContext, ConversationState.CreateProperty<DialogState>("DialogState"), cancellationToken);
    }
}
```

## <a name="to-catch-an-error-thrown-from-a-dialog"></a>Pour intercepter une erreur levée par un dialogue

### <a name="v3"></a>v3

```cs
// Create a custom IPostToBot implementation to catch exceptions.
public sealed class CustomPostUnhandledExceptionToUser : IPostToBot
{
    private readonly IPostToBot inner;
    private readonly IBotToUser botToUser;
    private readonly ResourceManager resources;
    private readonly System.Diagnostics.TraceListener trace;

    public CustomPostUnhandledExceptionToUser(IPostToBot inner, IBotToUser botToUser, ResourceManager resources, System.Diagnostics.TraceListener trace)
    {
        SetField.NotNull(out this.inner, nameof(inner), inner);
        SetField.NotNull(out this.botToUser, nameof(botToUser), botToUser);
        SetField.NotNull(out this.resources, nameof(resources), resources);
        SetField.NotNull(out this.trace, nameof(trace), trace);
    }

    async Task IPostToBot.PostAsync(IActivity activity, CancellationToken token)
    {
        try
        {
            await this.inner.PostAsync(activity, token);
        }
        catch (Exception ex)
        {
            try
            {
                // Log exception and send custom error message here.
                await this.botToUser.PostAsync("custom error message");
            }
            catch (Exception inner)
            {
                this.trace.WriteLine(inner);
            }

            throw;
        }
    }
}

// Register this using AutoFac, replacing the default PostUnhandledExceptionToUser.
builder
  .RegisterType<CustomPostUnhandledExceptionToUser>()
  .Keyed<IPostToBot>(typeof(PostUnhandledExceptionToUser));
```

### <a name="v4"></a>v4

```cs
// Provide an error handler in your implementation of the BotFrameworkHttpAdapter.
public class AdapterWithErrorHandler : BotFrameworkHttpAdapter
{
    public AdapterWithErrorHandler(
        ICredentialProvider credentialProvider,
        ILogger<BotFrameworkHttpAdapter> logger,
        ConversationState conversationState = null)
        : base(credentialProvider)
    {
        OnTurnError = async (turnContext, exception) =>
        {
            // Log any leaked exception from the application.
            logger.LogError($"Exception caught : {exception.Message}");

            // Send a catch-all apology to the user.
            await turnContext.SendActivityAsync("Sorry, it looks like something went wrong.");

            if (conversationState != null)
            {
                try
                {
                    // Delete the conversation state for the current conversation, to prevent the
                    // bot from getting stuck in a error-loop caused by being in a bad state.
                    // Conversation state is similar to "cookie-state" in a web page.
                    await conversationState.DeleteAsync(turnContext);
                }
                catch (Exception e)
                {
                    logger.LogError(
                        $"Exception caught on attempting to Delete ConversationState : {e.Message}");
                }
            }
        };
    }
}
```

## <a name="to-process-different-activity-types"></a>Pour traiter les différents types d’activités

### <a name="v3"></a>v3

```cs
// Within your MessageController, check the message type.
string messageType = activity.GetActivityType();
if (messageType == ActivityTypes.Message)
{
    await Conversation.SendAsync(activity, () => new Dialogs.RootDialog());
}
else if (messageType == ActivityTypes.DeleteUserData)
{
}
else if (messageType == ActivityTypes.ConversationUpdate)
{
}
else if (messageType == ActivityTypes.ContactRelationUpdate)
{
}
else if (messageType == ActivityTypes.Typing)
{
}
```

### <a name="v4"></a>v4

```cs
// In the bot's ActivityHandler implementation, override relevant methods.

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle message activities here.
}

protected override Task OnConversationUpdateActivityAsync(ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle conversation update activities in general here.
}

protected override Task OnEventActivityAsync(ITurnContext<IEventActivity> turnContext, CancellationToken cancellationToken)
{
    // Handle event activities in general here.
}
```

## <a name="to-log-all-activities"></a>Pour journaliser toutes les activités

### <a name="v3"></a>v3

[IActivityLogger](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.history.iactivitylogger) était utilisé.

```csharp
builder.RegisterType<ActivityLoggerImplementation>().AsImplementedInterfaces().InstancePerDependency(); 

public class ActivityLoggerImplementation : IActivityLogger
{
    async Task IActivityLogger.LogAsync(IActivity activity)
    {
        // Store the activity.
    }
}
```

### <a name="v4"></a>v4

Utilisez [ITranscriptLogger](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.itranscriptlogger).

```csharp
var transcriptMiddleware = new TranscriptLoggerMiddleware(new TranscriptLoggerImplementation(Configuration.GetSection("StorageConnectionString").Value));
adapter.Use(transcriptMiddleware);

public class TranscriptLoggerImplementation : ITranscriptLogger
{
    async Task ITranscriptLogger.LogActivityAsync(IActivity activity)
    {
        // Store the activity.
    }
}
```

## <a name="to-add-bot-state-storage"></a>Pour ajouter du stockage d’état de bot

L’interface pour le stockage des _données utilisateur_, des _données de conversation_ et des _données de conversation privée_ a changé.

### <a name="v3"></a>v3

L’état était rendu persistant en utilisant une implémentation `IBotDataStore` et en l’injectant dans le système d’état de dialogue du SDK à l’aide d’Autofac.  Microsoft fournissait des classes `MemoryStorage`, `DocumentDbBotDataStore`, `TableBotDataStore` et `SqlBotDataStore` dans [Microsoft.Bot.Builder.Azure](https://github.com/Microsoft/BotBuilder-Azure/).

[IBotDataStore<BotData> ](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.dialogs.internals.ibotdatastore-1?view=botbuilder-dotnet-3.0) était utilisé pour rendre les données persistantes.

```csharp
Task<bool> FlushAsync(IAddress key, CancellationToken cancellationToken);
Task<T> LoadAsync(IAddress key, BotStoreType botStoreType, CancellationToken cancellationToken);
Task SaveAsync(IAddress key, BotStoreType botStoreType, T data, CancellationToken cancellationToken);
```

```csharp
var dbPath = ConfigurationManager.AppSettings["DocDbPath"];
var dbKey = ConfigurationManager.AppSettings["DocDbKey"];
var docDbUri = new Uri(dbPath);
var storage = new DocumentDbBotDataStore(docDbUri, dbKey);
builder.Register(c => storage)
                .Keyed<IBotDataStore<BotData>>(AzureModule.Key_DataStore)
                .AsSelf()
                .SingleInstance();
```

### <a name="v4"></a>v4

La couche de stockage utilise l’interface `IStorage`, spécifiez l’objet de la couche de stockage lors de la création de chaque objet de gestion d’état pour votre bot, comme `UserState`, `ConversationState` ou `PrivateConversationState`. L’objet de gestion d’état fournit des clés à la couche de stockage sous-jacente et fait également office de gestionnaire de propriétés. Par exemple, utilisez `IPropertyManager.CreateProperty<T>(string name)` pour créer un accesseur de propriété d’état.  Ces accesseurs de propriété sont utilisés pour récupérer et stocker les valeurs qui sont à l’intérieur et à l’extérieur du stockage sous-jacent du bot.

Utilisez [IStorage](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.istorage?view=botbuilder-dotnet-stable) pour rendre les données persistantes.

```csharp
Task DeleteAsync(string[] keys, CancellationToken cancellationToken = default(CancellationToken));
Task<IDictionary<string, object>> ReadAsync(string[] keys, CancellationToken cancellationToken = default(CancellationToken));
Task WriteAsync(IDictionary<string, object> changes, CancellationToken cancellationToken = default(CancellationToken));
```

```csharp
var storageOptions = new CosmosDbStorageOptions()
{
    AuthKey = configuration["cosmosKey"],
    CollectionId = configuration["cosmosCollection"],
    CosmosDBEndpoint = new Uri(configuration["cosmosPath"]),
    DatabaseId = configuration["cosmosDatabase"]
};

IStorage dataStore = new CosmosDbStorage(storageOptions);
var conversationState = new ConversationState(dataStore);
services.AddSingleton(conversationState);

```

## <a name="to-use-form-flow"></a>Pour utiliser Form Flow

### <a name="v3"></a>v3

[Microsoft.Bot.Builder.FormFlow](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.formflow?view=botbuilder-dotnet-3.0) était inclus dans le SDK Bot Builder principal.

### <a name="v4"></a>v4

[Bot.Builder.Community.Dialogs.FormFlow](https://www.nuget.org/packages/Bot.Builder.Community.Dialogs.FormFlow/) est maintenant une bibliothèque Bot Builder Community.  La source est disponible dans le [dépôt](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Dialogs.FormFlow) de la communauté.

## <a name="to-use-luisdialog"></a>Pour utiliser LuisDialog

### <a name="v3"></a>v3

[Microsoft.Bot.Builder.Dialogs.LuisDialog](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.dialogs.luisdialog-1?view=botbuilder-dotnet-3.0) était inclus dans le SDK Bot Builder principal.

### <a name="v4"></a>v4

[Bot.Builder.Community.Dialogs.Luis](https://www.nuget.org/packages/Bot.Builder.Community.Dialogs.Luis/) est maintenant une bibliothèque Bot Builder Community.  La source est disponible dans le [dépôt](https://github.com/BotBuilderCommunity/botbuilder-community-dotnet/tree/develop/libraries/Bot.Builder.Community.Dialogs.Luis) de la communauté.

## <a name="to-use-qna-maker"></a>Pour utiliser QnA Maker

### <a name="v3"></a>v3

```csharp
[Serializable]
[QnAMaker("QnAEndpointKey", "QnAKnowledgebaseId", <ScoreThreshold>, <TotalResults>, "QnAEndpointHostName")]
public class SimpleQnADialog : QnAMakerDialog
{
}
```

### <a name="v4"></a>v4

```csharp
public class QnABot : ActivityHandler
{
  private readonly IConfiguration _configuration;
  private readonly ILogger<QnABot> _logger;
  private readonly IHttpClientFactory _httpClientFactory;

  public QnABot(IConfiguration configuration, ILogger<QnABot> logger, IHttpClientFactory httpClientFactory)
  {
    _configuration = configuration;
    _logger = logger;
    _httpClientFactory = httpClientFactory;
  }

  protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
  {
    var httpClient = _httpClientFactory.CreateClient();

    var qnaMaker = new QnAMaker(new QnAMakerEndpoint
    {
      KnowledgeBaseId = _configuration["QnAKnowledgebaseId"],
      EndpointKey = _configuration["QnAEndpointKey"],
      Host = _configuration["QnAEndpointHostName"]
    },
    null,
    httpClient);

    _logger.LogInformation("Calling QnA Maker");

    // The actual call to the QnA Maker service.
    var response = await qnaMaker.GetAnswersAsync(turnContext);
    if (response != null && response.Length > 0)
    {
      await turnContext.SendActivityAsync(MessageFactory.Text(response[0].Answer), cancellationToken);
    }
    else
    {
      await turnContext.SendActivityAsync(MessageFactory.Text("No QnA Maker answers were found."), cancellationToken);
    }
  }
}
```
