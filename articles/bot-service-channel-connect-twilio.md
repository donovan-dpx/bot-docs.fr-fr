---
title: Connecter un bot à Twilio - Bot Service
description: Découvrez comment configurer la connexion d’un bot à Twilio.
keywords: Twilio, canaux de bot, SMS, application, téléphone, configurer Twilio, communication cloud, texte
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/9/2018
ms.openlocfilehash: 42e79bbf79418c30ff0b4b6c584ab4791cd246c0
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75793090"
---
# <a name="connect-a-bot-to-twilio"></a>Connecter un bot à Twilio

Vous pouvez configurer votre bot pour communiquer avec des personnes utilisant la plateforme de communication cloud Twilio.

## <a name="log-in-to-or-create-a-twilio-account-for-sending-and-receiving-sms-messages"></a>Se connecter à un compte Twilio ou créer un compte Twilio pour envoyer et recevoir des messages SMS

Si vous n’avez pas de compte Twilio, <a href="https://www.twilio.com/try-twilio" target="_blank">créez-en un</a>.

## <a name="create-a-twiml-application"></a>Créer une application TwiML

<a href="https://support.twilio.com/hc/articles/223180928-How-Do-I-Create-a-TwiML-App-" target="_blank">Créer une application TwiML</a> en suivant les instructions fournies.

![Créer une application](~/media/channels/twi-StepTwiml.png)

Sous **Properties** (Propriétés), entrez un **NOM CONVIVIAL**. Dans ce didacticiel, nous utilisons « Mon application TwiML » comme exemple. L’**URL DE DEMANDE** sous Voice (Voix) peut être laissée vide. Sous **Messaging** (Messagerie), l’**URL de demande** doit être `https://sms.botframework.com/api/sms`.

## <a name="select-or-add-a-phone-number"></a>Sélectionner ou ajouter un numéro de téléphone

Suivez les instructions indiquées <a href = "https://support.twilio.com/hc/articles/223180048-Adding-a-Verified-Phone-Number-or-Caller-ID-with-Twilio" target="_blank">ici</a> pour ajouter un ID d’appelant vérifié via le site de la console. Une fois que vous avez terminé, vous verrez votre numéro vérifié dans **Active Numbers** (Numéros actifs) sous **Manage Numbers** (Gestion des numéros).

![Définir un numéro de téléphone](~/media/channels/twi-StepPhone.png)

## <a name="specify-application-to-use-for-voice-and-messaging"></a>Spécifier l’application à utiliser pour la voix et la messagerie

Cliquez sur le numéro et accédez à la configuration (**Configure**). Sous Voice (Voix) et Messaging (Messagerie), définissez les options **CONFIGURE WITH** (Configurer avec) sur l’application TwiML et **TWIML APP** sur My TwiML app (Mon application TwiML). Une fois que vous avez terminé, cliquez sur **Save** (Enregistrer).

![Spécifier l’application](~/media/channels/twi-StepPhone2.png)

Revenez à la page de gestion des numéros (**Manage Numbers**), et vous verrez que la voix et de la messagerie ont bien été définies sur l’application TwiML.

![Numéro spécifié](~/media/channels/twi-StepPhone3.png)


## <a name="gather-credentials"></a>Collecter les informations d’identification

Revenez à la [page d’accueil de la console](https://www.twilio.com/console/), vous verrez votre SID de compte et un jeton d’authentification sur le tableau de bord du projet, comme indiqué ci-dessous.

![Collecter les informations d’identification de l’application](~/media/channels/twi-StepAuth.png)

## <a name="submit-credentials"></a>Envoyer les informations d’identification

Dans une fenêtre distincte, revenez sur le site du Bot Framework à l’adresse https://dev.botframework.com/. 

- Sélectionnez **My bots** (Mes bots) et choisissez le bot que vous souhaitez connecter à Twilio. Ceci vous dirigera vers le portail Azure.
- Sélectionnez **Canaux** sous **Gestion du bot**. Cliquez sur l’icône de Twilio (SMS).
- Entrez le numéro de téléphone, le SID de compte et le jeton d’authentification enregistrés précédemment. Une fois que vous avez terminé, cliquez sur **Save** (Enregistrer).

![Envoyer les informations d’identification](~/media/channels/twi-StepSubmit.png)

Une fois ces étapes effectuées, votre bot est correctement configuré pour communiquer avec les utilisateurs de Twilio.

## <a name="connect-a-bot-to-twilio-using-the-twilio-adapter"></a>Connecter un bot à Twilio à l'aide de l'adaptateur Twilio

Outre le canal disponible dans Azure Bot Service pour connecter votre bot à Twilio, vous pouvez également utiliser l'adaptateur Twilio. Dans cet article, vous allez apprendre à connecter un bot à Twilio à l'aide de l'adaptateur.  Cet article vous guide tout au long de la procédure de modification de l'exemple EchoBot pour le connecter à Twilio.

> [!NOTE]
> Les instructions ci-dessous s'appliquent à l'implémentation C# de l'adaptateur Twilio. Pour en savoir plus sur l'utilisation de l'adaptateur JS, qui fait partie des bibliothèques BotKit, [consultez la documentation BotKit Twilio](https://botkit.ai/docs/v4/platforms/twilio.html).

### <a name="prerequisites"></a>Conditions préalables requises

* L'[exemple de code EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Un compte Twilio Si vous n'avez pas de compte Twilio, vous pouvez [en créer un ici](https://www.twilio.com/try-twilio).

### <a name="get-a-twilio-number-and-gather-account-credentials"></a>Obtenir un numéro Twilio et collecter les informations d'identification du compte

1. Connectez-vous à [Twilio](https://twilio.com/console). Sur le côté droit de la page, vous verrez le **SID DU COMPTE** et le **JETON D'AUTHENTIFICATION** de votre compte. Notez-les car vous en aurez besoin plus tard lors de la configuration de votre application bot.

2. Choisissez l'option **Voix programmable** sous **Prise en main de Twilio**.

![Prise en main de la fonctionnalité Voix programmable](~/media/bot-service-channel-connect-twilio/get-started-voice.png)

3. Sur la page suivante, cliquez sur le bouton **Obtenir votre premier numéro Twilio**.  Une fenêtre contextuelle vous présente un nouveau numéro, que vous pouvez accepter en cliquant sur **Choisir ce numéro** (vous pouvez également rechercher un autre numéro en suivant les instructions affichées à l'écran).

4. Une fois votre numéro choisi, notez-le, car vous en aurez besoin plus tard lors de la configuration de votre application de bot.

### <a name="wiring-up-the-twilio-adapter-in-your-bot"></a>Connecter l'adaptateur Twilio dans votre bot

Maintenant que vous disposez de votre numéro Twilio et des informations d'identification du compte, vous devez configurer votre application bot.

#### <a name="install-the-twilio-adapter-nuget-package"></a>Installer le package NuGet de l'adaptateur Twilio

Ajoutez le package NuGet [Microsoft.Bot.Builder.Adapters.Twilio](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Twilio/). Pour plus d'informations sur l'utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs).

#### <a name="create-a-twilio-adapter-class"></a>Créer une classe d'adaptateur Twilio

Créez une classe qui hérite de la classe ***TwilioAdapter***. Cette classe sera notre adaptateur pour le canal Twilio et inclura des fonctionnalités de traitement des erreurs (comme pour la classe ***BotFrameworkAdapterWithErrorHandler*** déjà présente dans l'exemple et utilisée pour traiter d'autres requêtes d'Azure Bot Service).

```csharp
public class TwilioAdapterWithErrorHandler : TwilioAdapter
{
    public TwilioAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
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

#### <a name="create-a-new-controller-for-handling-twilio-requests"></a>Créer un contrôleur pour traiter les requêtes Twilio

Créez un contrôleur qui traitera les requêtes de Twilio, sur un nouveau point de terminaison « api/twilio » au lieu du point de terminaison « api/messages » par défaut utilisé pour les requêtes des canaux Azure Bot Service.  L'ajout d'un point de terminaison supplémentaire à votre bot vous permettra d'accepter les requêtes des canaux Bot Service, ainsi que de Twilio, en utilisant le même bot.

```csharp
[Route("api/twilio")]
[ApiController]
public class TwilioController : ControllerBase
{
    private readonly TwilioAdapter _adapter;
    private readonly IBot _bot;

    public TwilioController(TwilioAdapter adapter, IBot bot)
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

#### <a name="inject-the-twilio-adapter-in-your-bot-startupcs"></a>Injecter l'adaptateur Twilio dans le fichier startup.cs de votre bot

Dans votre fichier startup.cs, ajoutez la ligne suivante à la méthode ***ConfigureServices***. Cela permettra d'enregistrer votre adaptateur Twilio et de le mettre à la disposition de votre nouvelle classe de contrôleur.  Les paramètres de configuration que vous avez ajoutés à l'étape précédente seront automatiquement utilisés par l'adaptateur.

```csharp
services.AddSingleton<TwilioAdapter, TwilioAdapterWithErrorHandler>();
```

Une fois ajoutée, votre méthode ***ConfigureServices*** doit être semblable à ce qui suit.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Twilio Adapter
    services.AddSingleton<TwilioAdapter, TwilioAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

#### <a name="obtain-a-url-for-your-bot"></a>Récupérer l'URL de votre bot

Maintenant que vous avez connecté l'adaptateur dans votre projet de bot, vous devez identifier le point de terminaison qui doit être fourni à Twilio afin que votre bot puisse recevoir des messages. Vous aurez également besoin de cette URL pour terminer la configuration de votre application bot.

Pour accomplir cette étape, [déployez votre bot sur Azure](https://aka.ms/bot-builder-deploy-az-cli) et notez l'URL du bot déployé.

> [!NOTE]
> Si vous n'êtes pas prêt à déployer votre bot sur Azure, ou si vous souhaitez le déboguer lors de l'utilisation de l'adaptateur Twilio, vous pouvez utiliser un outil tel que [ngrok](https://www.ngrok.com) (que vous aurez probablement déjà installé si vous avez précédemment utilisé l'émulateur Bot Framework) pour passer par le bot local et obtenir une URL publiquement accessible. 
> 
> Si vous souhaitez créer un tunnel ngrok et obtenir une URL d'accès à votre bot, utilisez la commande suivante dans une fenêtre de terminal (cela suppose que votre bot local s'exécute sur le port 3978 ; si ce n'est pas le cas, modifiez les numéros de port dans la commande).
> 
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="add-twilio-app-settings-to-your-bots-configuration-file"></a>Ajouter les paramètres de l'application Twilio au fichier de configuration de votre bot

Ajoutez les paramètres ci-dessous au fichier appSettings.json de votre projet de bot. Vous devez renseigner les champs**TwilioNumber**, **TwilioAccountSid** et **TwilioAuthToken** à l'aide des valeurs que vous avez recueillies lors de la création de votre numéro Twilio. **TwilioValidationUrl** doit être l'URL de votre bot, plus le point de terminaison `api/twilio` que vous avez spécifié dans le contrôleur nouvellement créé. Par exemple : `https://yourboturl.com/api/twilio`.


```json
  "TwilioNumber": "",
  "TwilioAccountSid": "",
  "TwilioAuthToken": "",
  "TwilioValidationUrl", ""
```

Après avoir renseigné les champs ci-dessus, vous devez redéployer votre bot (ou le redémarrer en cas d'exécution locale avec ngrok).

### <a name="complete-configuration-of-your-twilio-number"></a>Terminer la configuration de votre numéro Twilio

La dernière étape consiste à configurer le point de terminaison de messagerie de votre nouveau numéro Twilio pour veiller à ce que votre bot reçoive les messages.

1. Accédez à la [page Numéros actifs](https://www.twilio.com/console/phone-numbers/incoming) de Twilio.

2. Cliquez sur le numéro de téléphone que vous avez créé à l'étape précédente.

3. Dans la section **Messagerie**, complétez la section **UN MESSAGE ARRIVE DANS** en choisissant **Webhook** dans la liste déroulante et en renseignant la zone de texte avec le point de terminaison que vous avez utilisé pour le paramètre **TwilioValidationUrl** à l'étape précédente, par exemple `https://yourboturl.com/api/twilio`.

![Configurer le webhook du numéro Twilio](~/media/bot-service-channel-connect-twilio/twilio-number-messaging-settings.png)

4. Cliquez sur le bouton **Enregistrer** .

### <a name="test-your-bot-with-adapter-in-twilio"></a>Tester votre bot avec l'adaptateur Twilio

Vous pouvez maintenant vérifier que votre bot est correctement connecté à Twilio en envoyant un SMS à votre numéro Twilio.  Une fois le message reçu par votre bot, il vous renvoie un message contenant le même texte.

Vous pouvez également tester cette fonctionnalité à l'aide de l'[exemple de bot relatif à l'adaptateur Twilio](https://aka.ms/csharp-63-twilio-adapter-sample) en renseignant le fichier appSettings.json avec les mêmes valeurs que celles décrites dans les étapes ci-dessus.
