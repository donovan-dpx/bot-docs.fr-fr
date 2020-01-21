---
title: Développer un bot Direct Line Speech - Bot Service
description: Développer un bot Direct Line Speech
keywords: développer un bot Direct Line Speech, un bot vocal
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2aaafb7c46097a178761b356299612d874b1a823
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75795962"
---
# <a name="use-direct-line-speech-in-your-bot"></a>Utiliser Direct Line Speech dans votre bot

[!INCLUDE [applies-to-v4](includes/applies-to.md)]

Direct Line Speech utilise une nouvelle fonctionnalité de streaming basée sur WebSocket de Bot Framework pour échanger des messages entre le canal Direct Line Speech et votre bot. Après avoir activé le canal Direct Line Speech dans le portail Azure, vous devez mettre à jour votre bot pour écouter et accepter ces connexions WebSocket. Les instructions suivantes expliquent comment procéder.  

## <a name="step-1-upgrade-to-the-46-sdk"></a>Étape 1 : Effectuer une mise à niveau vers le kit SDK 4.6 

Pour Direct Line Speech, veillez à utiliser la version 4.6 ou supérieure du kit SDK Bot Builder. 

## <a name="step-2-update-your-net-core-bot-codeif-your-bot-uses-addbot-and-usebotframework-instead-of-a-botcontroller"></a>Étape 2 : Mettre à jour le code de votre bot .NET Core si celui-ci utilise AddBot et UseBotFramework et non un BotController 

Si vous avez créé un bot en utilisant la version v4 du kit SDK Bot Builder avant la version 4.3.2, votre bot n’inclut probablement pas un BotController mais utilise à la place les méthodes AddBot() et UseBotFramework() dans le fichier Startup.cs pour exposer le point de terminaison POST où le bot reçoit les messages. Pour exposer le nouveau point de terminaison de streaming, vous devez ajouter un BotController et supprimer les méthodes AddBot() et UseBotFramework(). Ces instructions passent en revue les modifications à apporter. Si vous avez déjà apporté ces modifications, passez à l’étape suivante. 

Ajoutez un nouveau contrôleur MVC à votre projet de bot en ajoutant un fichier appelé BotController.cs. Ajoutez le code du contrôleur dans ce fichier : 

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

Dans le fichier **Startup.cs**, repérez la méthode Configure. Supprimez la ligne `UseBotFramework()` et vérifiez que vous disposez de ces lignes pour `UseWebSockets` : 

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

De plus, dans le fichier Startup.cs, localisez la méthode ConfigureServices. Supprimez la ligne `AddBot()` et vérifiez que vous disposez de lignes pour ajouter votre `IBot` et un `BotFrameworkHttpAdapter` : 

```cs

public void ConfigureServices(IServiceCollection services) 
{ 
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1); 
    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>(); 
    services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>(); 
    
    // Create the Bot Framework Adapter. 
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkHttpAdapter>(); 

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot. 
    services.AddTransient<IBot, EchoBot>(); 
} 
```

Le reste de votre code de bot reste le même. 

## <a name="step3-ensure-websockets-are-enabled"></a>Étape 3 : Vérifier que les WebSockets sont activés 

Quand vous créez un bot sur le portail Azure en utilisant un modèle comme EchoBot, vous obtenez un bot avec un contrôleur ASP.NET MVC qui expose un point de terminaison GET et POST et qui utilise aussi des WebSockets. Ces instructions expliquent comment ajouter ces éléments à votre bot dans le cas d’une mise à niveau ou si vous n’avez pas créé votre bot à partir d’un modèle. 

Ouvrez **BotController.cs** sous le dossier Controllers dans votre solution. 

Recherchez la méthode `PostAsync` dans la classe et mettez à jour sa décoration en remplaçant [HttpPost] par [HttpPost, HttpGet] : 

```cs

[HttpPost, HttpGet] 
public async Task PostAsync() 
{ 
    await _adapter.ProcessAsync(Request, Response, _bot); 
} 
```

Enregistrez et fermez BotController.cs. 

Ouvrez **Startup.cs** à la racine de votre solution. 

Dans Startup.cs, accédez au bas de la méthode Configure. Avant l’appel à  `app.UseMvc()`, ajoutez un appel à  `app.UseWebSockets()`. C’est important, car l’ordre de ces appels use compte. La fin de la méthode doit ressembler à : 

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
Le reste de votre code de bot reste le même. 

 

Étape 4 : Définir le champ Speak des activités pour personnaliser le message prononcé à l’intention de l’utilisateur (facultatif) 

Par défaut, tous les messages envoyés à l’utilisateur via Direct Line Speech sont prononcés.  

Vous pouvez éventuellement personnaliser la façon dont le message est prononcé en définissant le champ Speak des activités envoyées par le bot : 

```cs 

public IActivity Speak(string message) 
{ 
    var activity = MessageFactory.Text(message); 
    string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'> 

        <voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>" + 
        $"{message}" + "</voice></speak>"; 

    activity.Speak = body; 
    return activity; 
} 
```

L’extrait de code suivant montre comment utiliser la fonction Speak précédente : 

```cs

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken) 
{ 
    await turnContext.SendActivityAsync(Speak($"Echo: {turnContext.Activity.Text}"), cancellationToken); 
} 
``` 

## <a name="additional-information"></a>Informations supplémentaires 

- Pour voir un exemple complet de création et d’utilisation de bot vocal, consultez  [Tutoriel : Activer les fonctions vocales dans votre bot à l’aide du SDK Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk). 

- Pour plus d’informations sur l’utilisation d’activités, consultez  [Fonctionnement des bots](https://docs.microsoft.com/azure/bot-service/bot-builder-basics) et  [Envoyer et recevoir des SMS https://docs.microsoft.com/azure/bot-service/bot-builder-howto-send-messages?view=azure-bot-service-4.0](). 

 
