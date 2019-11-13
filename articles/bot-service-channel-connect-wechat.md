---
title: Connecter un bot à WeChat | Microsoft Docs
description: Découvrez comment configurer la connexion d’un bot à WeChat.
keywords: WeChat, Tencent, canal de bot, application WeChat, bot WeChat, ID d’application, secret d’application, informations d’identification
author: seaen
manager: kamrani
ms.topic: article
ms.author: egorn
ms.service: bot-service
ms.date: 11/01/2019
ms.openlocfilehash: aee02ba1707f08fbcb4479b37edd065fd28efd8f
ms.sourcegitcommit: 4751c7b8ff1d3603d4596e4fa99e0071036c207c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2019
ms.locfileid: "73443087"
---
# <a name="connect-a-bot-to-wechat"></a>Connecter un bot à WeChat

Vous pouvez configurer votre bot pour communiquer avec des personnes utilisant WeChat Official Accounts Platform.

## <a name="download-wechat-adapter-for-bot-framework"></a>Télécharger WeChat Adapter for Bot Framework

WeChat Adapter for Microsoft Bot Framework est un adaptateur open source qui se trouve sur GitHub. [Télécharger WeChat Adapter for Bot Framework](https://github.com/microsoft/BotFramework-WeChat/).

## <a name="create-a-wechat-account"></a>Créer un compte WeChat

Pour configurer un bot afin qu’il communique avec WeChat, vous devez créer un compte officiel WeChat sur [WeChat Official Account Platform](https://mp.weixin.qq.com/?lang=en_US), puis connecter le bot à l’application. Seul le compte Service Account est pris en charge pour le moment.

### <a name="change-your-prefer-language"></a>Changer votre langue par défaut

Vous pouvez changer la langue d’affichage par défaut avant de vous connecter.

 ![change_language](./media/channels/wechat-change-language.png)

### <a name="register-a-service-account"></a>Inscrire un compte Service Account

Un compte de service réel doit être vérifié par WeChat ; vous ne pouvez pas activer le webhook tant que le compte n’est pas vérifié. Pour créer votre propre compte de service, suivez les instructions [ici](https://kf.qq.com/product/weixinmp.html#hid=87).
Pour résumer, il vous suffit de cliquer sur Register Now dans la partie supérieure, de sélectionner Service Account, puis de suivre les instructions.

 ![register_account](./media/channels/wechat-register-account.png)

### <a name="sandbox-account"></a>Compte de bac à sable (sandbox)

Si vous voulez simplement tester l’intégration de WeChat et du bot, vous pouvez utiliser un compte de bac à sable (sandbox) au lieu de créer le compte de service. Apprenez-en plus sur la création d’un [compte de bac à sable (sandbox)](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login).

## <a name="enable-wechat-adapter-to-bot"></a>Activer WeChat Adapter pour le bot

Bot Project est un projet SDK Bot Framework V4 normal. Pour pouvoir le lancer, vous devez vérifier que vous pouvez exécuter le bot. Téléchargez [WeChat Adapter for Bot Framework](https://github.com/microsoft/BotFramework-WeChat/).

### <a name="prerequisites"></a>Prérequis

    .NET Core SDK (version 2.2.x)

### <a name="add-reference-to-wechat-adapter-source"></a>Ajouter une référence à la source de WeChat Adapter

Référencez directement le projet WeChat Adapter ou ajoutez ~/BotFramework-WeChat/libraries/csharp_dotnetcore/outputpackages comme source NuGet locale.

### <a name="inject-wechat-adapter-in-your-bot-startupcs"></a>Injecter WeChat Adapter dans le fichier startup.cs de votre bot

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);

    // Create the storage we'll be using for User and Conversation state. (Memory is great for testing purposes.)
    services.AddSingleton<IStorage, MemoryStorage>();

    // Create the User state. (Used in this bot's Dialog implementation.)
    services.AddSingleton<UserState>();

    // Create the Conversation state. (Used by the Dialog system itself.)
    services.AddSingleton<ConversationState>();

    // Load WeChat settings.
    var wechatSettings = new WeChatSettings();
    Configuration.Bind("WeChatSettings", wechatSettings);
    services.AddSingleton<WeChatSettings>(wechatSettings);

    // Configure hosted serivce.
    services.AddSingleton<IBackgroundTaskQueue, BackgroundTaskQueue>();
    services.AddHostedService<QueuedHostedService>();
    services.AddSingleton<WeChatHttpAdapter>();

    // The Dialog that will be run by the bot.
    services.AddSingleton<MainDialog>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

### <a name="update-your-bot-controller"></a>Mettre à jour le contrôleur de votre bot

```csharp
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{  
    private readonly IBot _bot;
    private readonly WeChatHttpAdapter _weChatHttpAdapter;
    private readonly string Token;
    public BotController(IBot bot, WeChatHttpAdapter weChatAdapter)
    {
        _bot = bot;
        _weChatHttpAdapter = weChatAdapter;
    }

    [HttpPost("/WeChat")]
    [HttpGet("/WeChat")]
    public async Task PostWeChatAsync([FromQuery] SecretInfo secretInfo)
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _weChatHttpAdapter.ProcessAsync(Request, Response, _bot, secretInfo);
    }
}
```

### <a name="setup-appsettingsjson"></a>Configurer appsettings.json

Vous devrez configurer appsettings.json avant de démarrer le bot. Vous trouverez ci-dessous ce dont vous avez besoin.

```json
"WeChatSettings": {
    "UploadTemporaryMedia": true,
    "PassiveResponseMode": false,
    "Token": "",
    "EncodingAESKey": "",
    "AppId": "",
    "AppSecret": ""
}
```

#### <a name="service-account"></a>Compte Service Account

Si vous avez déjà un compte de service et que vous êtes prêt pour le déploiement, vous trouverez **AppID**, **AppSecret**, **EncodingAESKey** et **Token** dans les configurations de base de la barre de navigation gauche, comme ci-dessous.

N’oubliez pas que vous devez configurer la liste verte IP, sinon WeChat n’acceptera pas votre demande.

 ![serviceaccount_console](./media/channels/wechat-serviceaccount-console.png)

#### <a name="sandbox-account"></a>Compte de bac à sable (sandbox)

Le compte de bac à sable (sandbox) n’ayant pas de **EncodingAESKey**, le message de WeChat n’a pas été chiffré. Laissez EncodingAESKey vide. Vous n’avez ici que trois paramètres : **appID**, **appsecret** et **Token**.

 ![sandbox_account](./media/channels/wechat-sandbox-account.png)

### <a name="start-bot-and-set-endpoint-url"></a>Démarrer le bot et définir l’URL du point de terminaison

Vous pouvez maintenant définir le back-end de votre bot. Avant cela, vous devez démarrer le bot avant d’enregistrer les paramètres ; WeChat vous enverra une demande de vérification de l’URL.
Définissez le point de terminaison en suivant ce modèle : **https://your_end_point/WeChat** . Sinon, définissez vos paramètres personnels comme vous l’avez fait dans BotController.cs

 ![sandbox_account2](./media/channels/wechat-sandbox-account-2.png)

### <a name="subscribe-your-official-account"></a>Abonner votre compte officiel

Vous pouvez trouver un code QR pour abonner votre compte de test comme dans WeChat.

 ![subscribe](./media/channels/wechat-subscribe.png)

## <a name="test-through-wechat"></a>Tester via WeChat

Une fois que vous avez terminé, vous pouvez essayer le bot dans votre client WeChat. Vous pouvez essayer notre exemple de bot présent sous le dossier tests. Cet exemple de bot, qui inclut WeChat Adapter, est intégré avec le bot Echo et le bot Cards.

 ![chat](./media/channels/wechat-chat.png)
