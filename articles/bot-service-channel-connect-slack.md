---
title: Connecter un bot à Slack - Bot Service
description: Découvrez comment configurer la connexion d’un bot à Slack.
keywords: connecter un bot, canal de bot, bot Slack, application de messagerie Slack, adaptateur Slack
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/09/2019
ms.openlocfilehash: d4e29d1349d441d8208800cb534e02320f326bb1
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791904"
---
# <a name="connect-a-bot-to-slack"></a>Connecter un bot à Slack

Deux méthodes sont disponibles pour configurer l'application de messagerie Slack :
- Utilisation du portail Azure Bot Service pour connecter votre bot
- Utilisation de l'adaptateur Slack

## <a name="azure-bot-service-portaltababs"></a>[Portail Azure Bot Service](#tab/abs)
## <a name="create-a-slack-application-for-your-bot"></a>Créer une application Slack pour votre bot

Connectez-vous à [Slack](https://slack.com/signin) et accédez à la section sur la [création d’un canal d’application Slack](https://api.slack.com/apps).

![Installer le robot](~/media/channels/slack-NewApp.png)

## <a name="create-an-app-and-assign-a-development-slack-team"></a>Créer une application et assigner une équipe Slack de développement

Entrez un nom d’application et sélectionnez une équipe Slack de développement. Si vous n’êtes pas déjà membre d’une équipe Slack de développement, [créez-en une, ou rejoignez-en une](https://slack.com/).

![Créer une application](~/media/channels/slack-CreateApp.png)

Cliquez sur **Créer une application**. Slack crée votre application et génère un ID client et une clé secrète client.

## <a name="add-a-new-redirect-url"></a>Ajouter une nouvelle URL de redirection

Vous devez ensuite ajouter une nouvelle URL de redirection.

1. Sélectionnez l’onglet **OAuth & Permissions** (OAuth et autorisations).
2. Cliquez sur **Add a new Redirect URL** (Ajouter une nouvelle URL de redirection).
3. Entrez https://slack.botframework.com.
4. Cliquez sur **Add**.
5. Cliquez sur **Save URLs** (Enregistrer les URL).

![Ajouter une URL de redirection](~/media/channels/slack-RedirectURL.png)

## <a name="create-a-slack-bot-user"></a>Créer un utilisateur de bot Slack

L’ajout d’un utilisateur de bot vous permet d’assigner un nom d’utilisateur à votre bot et de choisir s’il doit toujours apparaître comme étant en ligne.

1. Cliquez sur l’onglet **Bot Users** (Utilisateurs de bot).
2. Cliquez sur **Add a Bot User** (Ajouter un utilisateur de bot).

![Créer un bot](~/media/channels/slack-CreateBot.png)

Cliquez sur **Add Bot User** (Ajouter un utilisateur de bot) pour valider vos paramètres. Définissez l’option **Always Show My Bot as Online** (Toujours afficher mon bot comme étant en ligne)sur **On** (Activé), puis cliquez sur **Save Changes** (Enregistrer les modifications).

![Créer un bot](~/media/channels/slack-CreateApp-AddBotUser.png)

## <a name="subscribe-to-bot-events"></a>S’abonner aux événements de bot

Suivez ces étapes pour vous abonner à six événements de bot spécifiques. Lorsque vous vous abonnez à des événements de bot, votre application est informée des activités de l’utilisateur à l’URL que vous spécifiez.

> [!TIP]
> Votre descripteur de bot est le nom de votre bot. Pour trouver le descripteur d’un bot, consultez la page [https://dev.botframework.com/bots](https://dev.botframework.com/bots), choisissez un bot, et enregistrez son nom.

1. Sélectionnez l’onglet **Event Subscriptions** (Abonnements à des événements).
2. Définissez l’option **Enable Events** (Activer des événements) sur **On** (Activé).
3. Dans **Request URL** (URL de la demande), entrez `https://slack.botframework.com/api/Events/{YourBotHandle}`, où `{YourBotHandle}` est le descripteur de votre bot, sans les accolades. Le descripteur du bot utilisé dans cet exemple est **ContosoBot**.

   ![S’abonner à des événements : haut](~/media/channels/slack-SubscribeEvents-a.png)

4. Sous **Subscribe to Bot Events** (S’abonner à des événements de bot), cliquez sur **Add Bot User Event** (Ajouter un événement d’utilisateur de bot).
5. Dans la liste des événements, sélectionnez ces six types d’événements :
    * `member_joined_channel`
    * `member_left_channel`
    * `message.channels`
    * `message.groups`
    * `message.im`
    * `message.mpim`

   ![S’abonner à des événements : milieu](~/media/channels/slack-SubscribeEvents-b.png)

6. Cliquez sur **Enregistrer les modifications**.

   ![S’abonner à des événements : bas](~/media/channels/slack-SubscribeEvents-c.png)

## <a name="add-and-configure-interactive-messages-optional"></a>Ajouter et configurer des messages interactifs (facultatif)

Si votre bot doit utiliser des fonctionnalités spécifiques à Slack comme des boutons, procédez comme suit :

1. Sélectionnez l’onglet **Interactive Components** (Composants interactifs) et cliquez sur **Enable Interactive Components** (Activer les composants interactifs).
2. Entrez `https://slack.botframework.com/api/Actions` sous **Request URL** (URL de demande).
3. Cliquez sur le bouton **Save changes** (Enregistrer les modifications).

![Activer les messages](~/media/channels/slack-MessageURL.png)

## <a name="gather-credentials"></a>Collecter les informations d’identification

Sélectionnez l’onglet **Basic Information** (Informations de base) et faites défiler jusqu’à la section **App Credentials** (Informations d’identification de l’application).
L’ID Client, la clé secrète client et le jeton de vérification requis pour la configuration de votre bot Slack sont affichés.

![Collecter les informations d’identification](~/media/channels/slack-AppCredentials.png)

## <a name="submit-credentials"></a>Envoyer les informations d’identification

Dans une fenêtre de navigateur distincte, revenez sur le site du Bot Framework à l’adresse `https://dev.botframework.com/`.

1. Sélectionnez **My bots** (Mes bots) et choisissez le bot que vous souhaitez connecter à Slack.
2. Dans la section **Channels** (Canaux), cliquez sur l’icône de Slack.
3. Dans la section **Enter your Slack credentials** (Entrez vos informations d’identification Slack), collez les informations d’identification de l’application issues du site web Slack dans les champs appropriés.
4. Le champ **Landing Page URL** (URL de la page d’accueil) est facultatif. Vous pouvez l’omettre ou le modifier.
5. Cliquez sur **Enregistrer**.

![Envoyer les informations d’identification](~/media/channels/slack-SubmitCredentials.png)

Suivez les instructions fournies pour permettre à votre équipe Slack de développement d’accéder à votre application Slack.

## <a name="enable-the-bot"></a>Activer le bot

Dans la page Configure Slack (Configurer Slack), vérifiez que le curseur en regard du bouton Enregistrer est bien positionné sur **Activé**.
Votre bot est configuré pour communiquer avec des utilisateurs dans Slack.

## <a name="create-an-add-to-slack-button"></a>Créer un bouton Add to Slack (Ajouter à Slack)

Slack fournit du code HTML que vous pouvez utiliser pour aider les utilisateurs de Slack à trouver votre bot dans la section *Add the Slack button* (Ajouter le bouton Slack) de [cette page](https://api.slack.com/docs/slack-button).
Pour utiliser ce code HTML avec votre bot, remplacez la valeur href (qui commence par `https://`) par l’URL figurant dans les paramètres de canal Slack de votre bot.
Suivez ces étapes pour obtenir l’URL de remplacement.

1. À la page [https://dev.botframework.com/bots](https://dev.botframework.com/bots), cliquez sur votre bot.
2. Cliquez sur **Channels** (Canaux), cliquez avec le bouton droit sur l’entrée **Slack**, puis cliquez sur **Copier le lien**. Cette URL est désormais dans le Presse-papiers.
3. Collez cette URL à partir de votre Presse-papiers dans le code HTML fourni pour le bouton Slack. Cette URL remplace la valeur href fournie par Slack pour ce bot.

Les utilisateurs autorisés peuvent cliquer sur le bouton **Add to Slack** (Ajouter à Slack) fourni par ce code HTML pour trouver votre bot sur Slack.

## <a name="slack-adaptertabadapter"></a>[Adaptateur Slack](#tab/adapter)
## <a name="connect-a-bot-to-slack-using-the-slack-adapter"></a>Connecter un bot à Slack à l'aide de l'adaptateur Slack

Outre le canal disponible dans Azure Bot Service pour connecter votre bot à Slack, vous pouvez également utiliser l'adaptateur Slack. Dans cet article, vous allez apprendre à connecter un bot à Slack à l'aide de l'adaptateur.  Cet article vous guide tout au long de la procédure de modification de l'exemple EchoBot pour le connecter à l'application Slack.

> [!NOTE]
> Les instructions ci-dessous s'appliquent à l'implémentation C# de l'adaptateur Slack. Pour en savoir plus sur l'utilisation de l'adaptateur JS, qui fait partie des bibliothèques BotKit, [consultez la documentation BotKit Slack](https://botkit.ai/docs/v4/platforms/slack.html).

## <a name="prerequisites"></a>Conditions préalables requises

* L'[exemple de code EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Accès à un espace de travail Slack en disposant des autorisations suffisantes pour créer et gérer des applications sur [https://api.slack.com/apps](https://api.slack.com/apps). Si vous n'avez accès à aucun environnement Slack, vous pouvez créer un espace de travail [gratuitement](https://www.slack.com).

## <a name="create-a-slack-application-for-your-bot"></a>Créer une application Slack pour votre bot

Connectez-vous à [Slack](https://slack.com/signin) et accédez à la section sur la [création d’un canal d’application Slack](https://api.slack.com/apps).

![Installer le robot](~/media/channels/slack-NewApp.png)

Cliquez sur le bouton « Créer une application ».

### <a name="create-an-app-and-assign-a-development-slack-team"></a>Créer une application et attribuer une équipe de développement Slack

Entrez un **Nom d'application** et sélectionnez un **Espace de travail de développement Slack**. Si vous n'êtes membre d'aucune équipe de développement Slack, [créez-en une, ou rejoignez-en une](https://slack.com/).

![Créer une application](~/media/channels/slack-CreateApp.png)

Cliquez sur **Créer une application**. Slack crée votre application, et génère un ID client et une clé secrète client.

### <a name="gather-required-configuration-settings-for-your-bot"></a>Collecter les paramètres de configuration requis pour votre bot

Une fois votre application créée, collectez les informations suivantes. Vous en aurez besoin pour connecter votre bot à Slack.

1. Notez le **Jeton de vérification** et la **Clé secrète de signature** à partir de l'onglet **Informations de base** et conservez-les pour la configuration des paramètres de votre bot.

![Jetons Slack](~/media/bot-service-adapter-connect-slack/slack-tokens.png)

2. Accédez à la page **Installer l'application** sous le menu **Paramètres**, et suivez les instructions pour installer votre application dans une équipe Slack.  Une fois l'application installée, copiez le **Jeton d'accès OAuth d'utilisateur du bot** et, une fois de plus, conservez-le pour la configuration des paramètres de votre bot.

## <a name="wiring-up-the-slack-adapter-in-your-bot"></a>Connecter l'adaptateur Slack dans votre bot

### <a name="install-the-slack-adapter-nuget-package"></a>Installer le package NuGet de l'adaptateur Slack

Ajoutez le package NuGet [Microsoft.Bot.Builder.Adapters.Slack](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Slack/). Pour plus d'informations sur l'utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs).

### <a name="create-a-slack-adapter-class"></a>Créer une classe d'adaptateur Slack

Créer une classe qui hérite de la classe ***SlackAdapter***. Cette classe sera notre adaptateur pour le canal Slack et inclura des fonctionnalités de traitement des erreurs (comme pour la classe ***BotFrameworkAdapterWithErrorHandler*** déjà présente dans l'exemple et utilisée pour traiter d'autres requêtes d'Azure Bot Service).

```csharp
public class SlackAdapterWithErrorHandler : SlackAdapter
{
    public SlackAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
    : base(configuration, logger)
    {
        OnTurnError = async (turnContext, exception) =>
        {
            // Log any leaked exception from the application.
            logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");

            // Send a message to the user
            await turnContext.SendActivityAsync("The bot encountered an error or bug.");
            await turnContext.SendActivityAsync("To continue to run this bot, please fix the bot source code.");

            // Send a trace activity, which will be displayed in the Bot Framework Emulator
            await turnContext.TraceActivityAsync("OnTurnError Trace", exception.Message, "https://www.botframework.com/schemas/error", "TurnError");
        };
    }
}
```

### <a name="create-a-new-controller-for-handling-slack-requests"></a>Créer un contrôleur pour traiter les requêtes Slack

Créez un contrôleur qui traitera les requêtes de votre application Slack, sur un nouveau point de terminaison « api/slack » au lieu du point de terminaison « api/messages » par défaut utilisé pour les requêtes des canaux Azure Bot Service.  L'ajout d'un point de terminaison supplémentaire à votre bot vous permettra d'accepter les requêtes des canaux Bot Service, ainsi que de Slack, en utilisant le même bot.

```csharp
[Route("api/slack")]
[ApiController]
public class SlackController : ControllerBase
{
    private readonly SlackAdapter _adapter;
    private readonly IBot _bot;

    public SlackController(SlackAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost]
    [HttpGet]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

### <a name="add-slack-app-settings-to-your-bots-configuration-file"></a>Ajouter les paramètres de l'application Slack au fichier de configuration de votre bot

Ajoutez les 3 paramètres ci-dessous au fichier appSettings.json de votre projet de bot, en renseignant chacun d'eux avec les valeurs recueillies précédemment lors de la création de votre application Slack.

```json
  "SlackVerificationToken": "",
  "SlackBotToken": "",
  "SlackClientSigningSecret": ""
```

### <a name="inject-the-slack-adapter-in-your-bot-startupcs"></a>Injecter l'adaptateur Slack dans le fichier startup.cs de votre bot

Dans votre fichier startup.cs, ajoutez la ligne suivante à la méthode ***ConfigureServices***. Cela permettra d'enregistrer votre adaptateur Slack et de le mettre à la disposition de votre nouvelle classe de contrôleur.  Les paramètres de configuration que vous avez ajoutés à l'étape précédente seront automatiquement utilisés par l'adaptateur.

```csharp
services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();
```

Une fois ajoutée, votre méthode ***ConfigureServices*** doit être semblable à ce qui suit.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Slack Adapter
    services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

## <a name="complete-configuration-of-your-slack-app"></a>Terminer la configuration de votre application Slack

### <a name="obtain-a-url-for-your-bot"></a>Récupérer l'URL de votre bot

Maintenant que vous avez créé une application Slack et connecté l'adaptateur dans votre projet de bot, la dernière étape consiste à pointer l'application Slack vers le point de terminaison qui convient à votre bot et à vous abonner à votre application pour veiller à ce que votre bot reçoive les messages.  Pour ce faire, votre bot doit être en cours d'exécution afin que Slack puisse vérifier que l'URL d'accès au point de terminaison est valide.

Pour accomplir cette étape, [déployez votre bot sur Azure](https://aka.ms/bot-builder-deploy-az-cli) et notez l'URL du bot déployé.

> [!NOTE]
> Si vous n'êtes pas prêt à déployer votre bot sur Azure, ou si vous souhaitez le déboguer lors de l'utilisation de l'adaptateur Slack, vous pouvez utiliser un outil tel que [ngrok](https://www.ngrok.com) (que vous aurez probablement déjà installé si vous avez précédemment utilisé l'émulateur Bot Framework) pour passer par le bot local et obtenir une URL publiquement accessible. 
> 
> Si vous souhaitez créer un tunnel ngrok et obtenir une URL d'accès à votre bot, utilisez la commande suivante dans une fenêtre de terminal (cela suppose que votre bot local s'exécute sur le port 3978 ; si ce n'est pas le cas, modifiez les numéros de port dans la commande).
> 
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

### <a name="update-your-slack-app"></a>Mettre à jour votre application Slack

Revenez au [tableau de bord de l'API Slack]([https://api.slack.com/apps]) et sélectionnez votre application.  Vous devez maintenant configurer 2 URL pour votre application et vous abonner aux événements appropriés.

1. Dans l'onglet **OAuth et autorisations**, l'**URL de redirection** doit être l'URL de votre bot, plus le point de terminaison `api/slack` que vous avez spécifié dans le contrôleur nouvellement créé. Par exemple : `https://yourboturl.com/api/slack`.

![URL de redirection Slack](~/media/bot-service-adapter-connect-slack/redirect-url.png)

2. Dans l'onglet **Abonnements aux événements**, renseignez le champ **URL de demande** avec l'URL utilisée à l'étape 1.

3. Activez les événements à l'aide du bouton bascule situé en haut de la page.

4. Développez la section **S'abonner aux événements de bot** et utilisez le bouton **Ajouter un événement d'utilisateur de bot** pour vous abonner aux événements **im_created** et **message.im**.

![Abonnements à des événements Slack](~/media/bot-service-adapter-connect-slack/event-subscriptions.png)

## <a name="test-your-bot-with-adapter-in-slack"></a>Tester votre bot avec l'adaptateur Slack

Votre application Slack est maintenant configurée et vous pouvez vous connecter à l'espace de travail Slack dans lequel vous l'avez installée. (Vous la trouverez sous la section « Applications » du menu de gauche.) Sélectionnez votre application et essayez d'envoyer un message. Celui-ci doit vous être renvoyé en écho dans la fenêtre de messagerie instantanée.

Vous pouvez également tester cette fonctionnalité à l'aide de l'[exemple de bot relatif à l'adaptateur Slack](https://aka.ms/csharp-60-slack-adapter-sample) en renseignant le fichier appSettings.json avec les mêmes valeurs que celles décrites dans les étapes ci-dessus. Cet exemple comporte des étapes supplémentaires décrites dans le fichier LISEZ-MOI pour présenter des exemples de partage de liens, de réception de pièces jointes et d'envoi de messages interactifs.

---
