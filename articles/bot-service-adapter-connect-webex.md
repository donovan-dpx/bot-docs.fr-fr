---
title: Connecter un bot à Webex Teams | Microsoft Docs
description: Découvrez comment configurer la connexion d’un bot à Webex via l'adaptateur Slack.
keywords: adaptateur de bot, Webex, bot Webex
author: garypretty
manager: kamrani
ms.topic: article
ms.author: gapretty
ms.service: bot-service
ms.date: 12/04/2019
ms.openlocfilehash: 590a4e3d4f2331580bb1fd823bdcd7a00f5efaab
ms.sourcegitcommit: 86495b597e55c94309a0c73fc1945a3393ddcbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75757303"
---
# <a name="connect-a-bot-to-webex-teams-using-the-webex-adapter"></a>Connecter un bot à Webex Teams à l’aide de l’adaptateur Webex

Dans cet article, vous allez apprendre à connecter un bot à Webex à l'aide de l'adaptateur disponible dans le kit de développement logiciel (SDK).  Cet article vous guide tout au long de la procédure de modification de l'exemple EchoBot pour le connecter à l'application Webex.

> [!NOTE]
> Les instructions ci-dessous s'appliquent à l'implémentation C# de l'adaptateur Slack. Pour en savoir plus sur l'utilisation de l'adaptateur JS, qui fait partie des bibliothèques BotKit, [consultez la documentation BotKit Slack](https://botkit.ai/docs/v4/platforms/webex.html).

## <a name="prerequisites"></a>Conditions préalables requises

* L'[exemple de code EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Accès à une équipe Webex en disposant des autorisations suffisantes pour vous connecter et créer et gérer des applications sur [https://developer.webex.com/my-apps](https://developer.webex.com/my-apps). Si vous n’avez pas accès à une équipe Webex, vous pouvez créer un compte gratuitement sur https://www.webex.com.

## <a name="create-a-webex-bot-app"></a>Créer une application de bot Webex

1. Connectez-vous au [tableau de bord du développeur Webex](https://developer.webex.com/my-apps), puis cliquez sur le bouton « Créer une application ».

2. Dans l’écran suivant, choisissez de créer un bot Webex en cliquant sur « Créer un bot ».

3. Dans l’écran suivant, entrez un nom, un nom d’utilisateur et une description appropriés pour votre bot, et choisissez une icône ou chargez l'image souhaitée.

![Installer le robot](~/media/bot-service-adapter-connect-webex/create-bot.png)

Cliquez sur le bouton « Ajouter un bot ».

4. Sur la page suivante, un jeton d’accès pour votre nouvelle application Webex vous est fourni. Notez-le car vous en aurez besoin lors de la configuration de votre bot.

![Installer le robot](~/media/bot-service-adapter-connect-webex/create-bot-settings.png)

## <a name="wiring-up-the-webex-adapter-in-your-bot"></a>Connecter l'adaptateur Webex dans votre bot

Avant de terminer la configuration de votre application Webex, vous devez connecter l’adaptateur Webex à votre bot.

### <a name="install-the-webex-adapter-nuget-package"></a>Installer le package NuGet de l'adaptateur Webex

Ajoutez le package NuGet [Microsoft.Bot.Builder.Adapters.Webex](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Webex/). Pour plus d'informations sur l'utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs).

### <a name="create-a-webex-adapter-class"></a>Créer une classe d'adaptateur Webex

Créer une classe qui hérite de la classe ***WebexAdapter***. Cette classe sera notre adaptateur pour le canal Webex. Il inclura des fonctionnalités de traitement des erreurs (comme pour la classe ***BotFrameworkAdapterWithErrorHandler*** déjà présente dans l'exemple et utilisée pour traiter les requêtes d'Azure Bot Service).

```csharp
public class WebexAdapterWithErrorHandler : WebexAdapter
{
    public WebexAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
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

### <a name="create-a-new-controller-for-handling-webex-requests"></a>Créer un contrôleur pour traiter les requêtes Webex

Créez un contrôleur qui traitera les requêtes de votre application Webex, sur un nouveau point de terminaison « api/webex » au lieu du point de terminaison « api/messages » par défaut utilisé pour les requêtes des canaux Azure Bot Service.  L'ajout d'un point de terminaison supplémentaire à votre bot vous permettra d'accepter les requêtes des canaux Bot Service (ou d'autres adaptateurs), ainsi que de Webex, en utilisant le même bot.

```csharp
[Route("api/webex")]
[ApiController]
public class WebexController : ControllerBase
{
    private readonly WebexAdapter _adapter;
    private readonly IBot _bot;

    public WebexController(WebexAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

### <a name="inject-webex-adapter-in-your-bot-startupcs"></a>Injecter l'adaptateur Webex dans le fichier Startup.cs de votre bot

Ajoutez la ligne suivante à la méthode ***ConfigureServices*** dans votre fichier Startup.cs afin d'enregistrer votre adaptateur Webex et de le mettre à la disposition de votre nouvelle classe de contrôleur.  Les paramètres de configuration, décrits à l'étape suivante, seront automatiquement utilisés par l'adaptateur.

```csharp
services.AddSingleton<SlackAdapter, WebexAdapterWithErrorHandler>();
```

Une fois ajoutée, votre méthode ***ConfigureServices*** doit être semblable à ce qui suit.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Slack Adapter
    services.AddSingleton<WebexAdapter, WebexAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

### <a name="add-webex-adapter-settings-to-your-bots-configuration-file"></a>Ajouter les paramètres de l'adaptateur Webex au fichier de configuration de votre bot

1. Ajoutez les quatre paramètres ci-dessous au fichier appSettings.json de votre projet de bot.

```json
  "WebexAccessToken": "",
  "WebexPublicAddress": "",
  "WebexSecret": "",
  "WebexWebhookName": ""
```

2. Renseignez le paramètre **WebexAccessToken** dans le jeton d’accès du bot Webex généré lors de la création de votre application de bot Webex, au cours des étapes précédentes. Laissez les trois autres paramètres vides jusqu’à ce que nous collections les informations requises lors des étapes ultérieures.

## <a name="complete-configuration-of-your-webex-app-and-bot"></a>Terminer la configuration de votre application et de votre bot Webex

### <a name="create-and-update-a-webex-webhook"></a>Créer et mettre à jour un webhook Webex

Maintenant que vous avez créé une application Webex et connecté l'adaptateur dans votre projet de bot, les dernières étapes consistent à configurer un webhook Webex, à le pointer vers le point de terminaison qui convient à votre bot et à vous abonner à votre application pour veiller à ce que votre bot reçoive les messages et pièces jointes. Pour ce faire, votre bot doit être en cours d'exécution afin que Webex puisse vérifier que l'URL d'accès au point de terminaison est valide.

1. Pour accomplir cette étape, [déployez votre bot sur Azure](https://aka.ms/bot-builder-deploy-az-cli) et notez l'URL du bot déployé. Votre point de terminaison de messagerie Webex correspond à l’URL de votre bot, à savoir l’URL de votre application déployée (ou point de terminaison ngrok), plus « /api/webex » (par exemple, `https://yourbotapp.azurewebsites.net/api/webex`).

> [!NOTE]
> Si vous n'êtes pas prêt à déployer votre bot sur Azure, ou si vous souhaitez le déboguer lors de l'utilisation de l'adaptateur Webex, vous pouvez utiliser un outil tel que [ngrok](https://www.ngrok.com) (que vous aurez probablement déjà installé si vous avez précédemment utilisé l'émulateur Bot Framework) pour passer par le bot local et obtenir une URL publiquement accessible. 
> 
> Si vous souhaitez créer un tunnel ngrok et obtenir une URL d'accès à votre bot, utilisez la commande suivante dans une fenêtre de terminal (cela suppose que votre bot local s'exécute sur le port 3978 ; si ce n'est pas le cas, modifiez les numéros de port dans la commande).
> 
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

2. Accédez à [https://developer.webex.com/docs/api/v1/webhooks](https://developer.webex.com/docs/api/v1/webhooks).

3. Cliquez sur le lien correspondant à la méthode PUT « https://api.ciscospark.com/v1/webhooks/{webhookId}  » (avec la description « Mettre à jour un webhook »). Cela permet de développer un formulaire pour envoyer une requête au point de terminaison.

![Installer le robot](~/media/bot-service-adapter-connect-webex/webex-webhook-put-endpoint.png)

4. Renseignez le formulaire avec les informations suivantes :

* Nom (entrez un nom pour votre webhook, par exemple « Webhook messages »)
* URL cible (URL complète vers le point de terminaison Webex de votre bot, par exemple https://yourbotapp.azurewebsites.net/api/webex)
* Secret (entrez le secret de votre choix pour sécuriser votre webhook)
* État (conservez le paramètre par défaut « actif »)

![Installer le robot](~/media/bot-service-adapter-connect-webex/webex-webhook-form.png)

5. Cliquez sur le bouton « Exécuter » afin de créer votre webhook et d'obtenir un message de réussite.

### <a name="complete-the-remaining-settings-in-your-bot-application"></a>Définir les paramètres restants de votre application de bot

Définissez les trois paramètres restants dans le fichier appsettings.json de votre bot (vous avez déjà renseigné **WebexAccessToken** lors d'une étape précédente).

* WebexPublicAddress (URL complète vers le point de terminaison Webex de votre bot)
* WebexSecret (secret fourni lors de la création de votre webhook à l’étape précédente)
* WebexWebhookName (nom de votre webhook fourni à l’étape précédente)

## <a name="re-deploy-your-bot-in-your-webex-team"></a>Redéployer votre bot dans votre équipe Webex

Une fois la configuration des paramètres de votre bot dans le fichier appsettings.json terminée, vous devez redéployer votre bot (ou le redémarrer en cas de tunneling vers un point de terminaison local à l’aide de ngrok).  La configuration de votre application et de votre bot Webex est terminée.  
Vous pouvez maintenant vous connecter à votre équipe Webex sur [https://www.webex.com](https://www.webex.com) et discuter avec votre bot en lui envoyant un message, de la même façon que vous contacteriez une autre personne.

![Installer le robot](~/media/bot-service-adapter-connect-webex/webex-contact-person.png)
