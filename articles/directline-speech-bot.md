---
title: Développer un bot Direct Line Speech | Microsoft Docs
description: Développer un bot Direct Line Speech
keywords: développer un bot direct line speech, un bot vocal
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 73a675c6e54d676f74dad2df24b3668d5e4e98be
ms.sourcegitcommit: a47183f5d1c2b2454c4a06c0f292d7c075612cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2019
ms.locfileid: "67252381"
---
## <a name="use-direct-line-speech-in-your-bot"></a>Utiliser Direct Line Speech dans votre bot 

[!INCLUDE [applies-to-v4](includes/applies-to.md)]

Direct Line Speech utilise une nouvelle fonctionnalité de streaming basée sur WebSocket de Bot Framework pour échanger des messages entre le canal Direct Line Speech et votre bot. Après avoir activé le canal Direct Line Speech dans le portail Azure, vous devez mettre à jour votre bot pour écouter et accepter ces connexions WebSocket. Les instructions suivantes expliquent comment procéder.

## <a name="add-the-nuget-package"></a>Ajouter le package NuGet
Pour la préversion de Direct Line Speech, il existe des packages NuGet supplémentaires que vous devez ajouter à votre bot. Ces packages sont hébergés sur un flux NuGet myget.org :
1.  Ouvrez votre projet de bot dans Visual Studio.

2.  Accédez à Gérer les packages Nuget sous les propriétés de votre projet de bot.

3.  Si vous ne l’avez pas encore en tant que source, ajoutez `https://botbuilder.myget.org/F/experimental/api/v3/index.json` en tant que flux à partir des paramètres de flux NuGet en haut à droite.

4.  Sélectionnez cette source NuGet et ajoutez l’un des packages `Microsoft.Bot.Protocol.StreamingExtensions.NetCore`.

5.  Acceptez les invites éventuelles pour terminer l’ajout du package à votre projet.

## <a name="option-1-update-your-net-core-bot-code-if-your-bot-has-a-botcontrollercs"></a>Option 1 : Mettre à jour votre code de bot .NET Core _si votre bot a un BotController.cs_
Lorsque vous créez un nouveau bot à partir du portail Azure à l’aide d’un des modèles tels qu’EchoBot, vous obtenez un bot comprenant un contrôleur ASP.NET MVC qui expose un point de terminaison POST unique. Ces instructions expliquent comment développer cela pour exposer également un point de terminaison afin d’accepter le point de terminaison de streaming WebSocket qui est un point de terminaison GET.
1.  Ouvrez BotController.cs sous le dossier Controllers dans votre solution.

2.  Recherchez la méthode PostAsync dans la classe et mettez à jour sa décoration en remplaçant [HttpPost] par [HttpPost, HttpGet] :
```cs
[HttpPost, HttpGet]
public async Task PostAsync()
{ 
    await _adapter.ProcessAsync(Request, Response, _bot);
}
```

3.  Enregistrez et fermez BotController.cs.

4.  Ouvrez Startup.cs à la racine de votre solution.

5.  Ajoutez un nouvel espace de noms :

```cs
using Microsoft.Bot.Protocol.StreamingExtensions.NetCore;
```

6.  Dans la méthode ConfigureServices, remplacez l’utilisation d’AdapterWithErrorHandler par WebSocketEnabledHttpAdapter dans l’appel services.AddSingleton approprié :

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...    

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, WebSocketEnabledHttpAdapter>();

    services.AddTransient<IBot, EchoBot>();

    ...
}
```

7. Toujours dans Startup.cs, accédez au bas de la méthode Configure. Avant l’appel app.UseMvc(); (ceci est important car l’ordre des appels d’utilisation compte), ajoutez app.UseWebSockets();. La fin de la méthode doit ressembler à :

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...

    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseWebSockets();
    app.UseMvc();

    ...
}
```

8.  Le reste de votre code de bot reste le même.

## <a name="option-2-update-your-net-core-bot-code-if-your-bot-uses-addbot-and-usebotframework-instead-of-a-botcontroller"></a>Option 2 : Mettre à jour votre code de bot .NET Core _si votre bot utilise AddBot et UseBotFramework à la place d’un BotController_

Si vous avez créé un bot en utilisant la version v4 du kit SDK Bot Builder avant la version 4.3.2, votre bot n’inclut probablement pas un BotController mais utilise à la place les méthodes AddBot() et UseBotFramework() dans le fichier Startup.cs pour exposer le point de terminaison POST où le bot reçoit les messages. Pour exposer le nouveau point de terminaison de streaming, vous devez ajouter un BotController et supprimer les méthodes AddBot() et UseBotFramework(). Ces instructions passent en revue les modifications à apporter.

1.  Ajoutez un nouveau contrôleur MVC à votre projet de bot en ajoutant un fichier appelé BotController.cs. Ajoutez le code du contrôleur dans ce fichier :

```cs
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter _adapter;
    private readonly IBot _bot;

    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost, HttpGet]
    public async Task ProcessMessageAsync()
    {
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```
2.  Dans le fichier Startup.cs, localisez la méthode Configure. Supprimez la ligne UseBotFramework() et veillez à avoir les lignes suivantes :

```cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    ...

    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseWebSockets();
    app.UseMvc();

    ...
}
```

3.  De plus, dans le fichier Startup.cs, localisez la méthode ConfigureServices. Supprimez la ligne AddBot() et veillez à avoir les lignes suivantes :

```cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();

    services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, WebSocketEnabledHttpAdapter>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```
4.  Le reste de votre code de bot reste le même.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Connecter un bot à Direct Line Speech (préversion)](./bot-service-channel-connect-directlinespeech.md)
