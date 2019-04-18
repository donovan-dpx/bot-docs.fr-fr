---
title: Gérer les ressources avec un fichier .bot | Microsoft Docs
description: Décrit l’objectif et l’utilisation du fichier de bot.
keywords: fichier de bot, .bot, fichier .bot, ressources de bot, gérer les ressources de bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/30/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 14552c55da4b1f9b581b81917496de179e92762b
ms.sourcegitcommit: 721bb09f10524b0cb3961d7131966f57501734b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58811501"
---
# <a name="manage-bot-resources"></a>Gérer les ressources du bot

Généralement, les bots utilisent différents services tels que [LUIS.ai](https://luis.ai) ou [QnaMaker.ai](https://qnamaker.ai). Quand vous développez un bot, vous devez être en mesure de les suivre tous. Vous pouvez utiliser différentes méthodes comme appsettings.json, web.config ou .env. 

> [!IMPORTANT]
> Avant la version 4.3 du kit SDK Bot Framework, nous proposions le fichier .bot comme mécanisme pour gérer les ressources. Toutefois, nous vous recommandons à l’avenir d’utiliser le fichier appsettings.json ou .env pour gérer ces ressources. Les bots qui utilisent le fichier .bot continuent de fonctionner pour l’instant, même si le fichier .bot a été **_déprécié_**. Si vous utilisez un fichier .bot pour gérer les ressources, suivez les étapes qui s’appliquent pour migrer les paramètres. 

## <a name="migrating-settings-from-bot-file"></a>Migration des paramètres à partir du fichier .bot
Les sections suivantes expliquent comment migrer les paramètres à partir du fichier .bot. Suivez le scénario qui vous convient.

**Scénario 1 : Bot local avec un fichier .bot**

Dans ce scénario, vous avez un bot local qui utilise un fichier .bot, mais le _bot n’a pas été migré_ vers le portail Azure. Suivez les étapes ci-dessous pour migrer les paramètres du fichier .bot vers le fichier appsettings.json ou .env.

- Si le fichier .bot est chiffré, vous devez le déchiffrer à l’aide de la commande suivante :

```cli
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear` command.
```

- Ouvrez le fichier .bot déchiffré, copiez les valeurs et ajoutez-les au fichier appsettings.json ou .env.
- Mettez à jour le code pour lire les paramètres du fichier appsettings.json ou .env.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans la méthode `ConfigureServices`, utilisez l’objet de configuration fourni par ASP.NET Core, par exemple : 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Dans JavaScript, référencez des variables .env en dehors de l’objet `process.env`, par exemple :
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

Si nécessaire, provisionnez des ressources et connectez-les à votre bot à l’aide du fichier appsettings.json ou .env.

**Scénario 2 : Bot déployé sur Azure avec un fichier .bot**

Dans ce scénario, vous avez déjà déployé un bot sur le portail Azure à l’aide du fichier .bot et vous souhaitez maintenant migrer les paramètres du fichier .bot vers le fichier appsettings.json ou .env.

- Téléchargez le code de bot à partir du portail Azure. Quand vous téléchargez le code, vous êtes invité à inclure le fichier appsettings.json ou .env avec vos références MicrosoftAppId et MicrosoftAppPassword ainsi que tous les autres paramètres qui y sont contenus. 
- Ouvrez le fichier appsettings.json ou .env _téléchargé_ et copiez les paramètres à partir de celui-ci dans votre fichier appsettings.json ou .env _local_. N’oubliez pas de supprimer les entrées botSecret et botFilePath de votre fichier appsettings.json ou .env local.
- Mettez à jour le code pour lire les paramètres du fichier appsettings.json ou .env.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Dans la méthode `ConfigureServices`, utilisez l’objet de configuration fourni par ASP.NET Core, par exemple : 

**Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```
# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Dans JavaScript, référencez des variables .env en dehors de l’objet `process.env`, par exemple :
   
**index.js**

```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```
---

Vous devez également supprimer les entrées `botFilePath` et `botFileSecret` de la section **Paramètres d’application** dans le **portail Azure**.

_Si nécessaire_, provisionnez des ressources et connectez-les à votre bot à l’aide du fichier appsettings.json ou .env.

**Scénario 3 : Pour les bots qui utilisent le fichier appsettings.json ou .env**

Ce scénario couvre le cas dans lequel vous commencez à développer des bots à l’aide de la version 4.3 du kit SDK à partir de zéro et n’avez aucun fichier .bot existant à migrer. Tous les paramètres que vous souhaitez utiliser dans votre bot se trouvent dans le fichier appsettings.json ou .env comme indiqué ci-dessous :

```JSON
{
  "MicrosoftAppId": "<your-AppId>",
  "MicrosoftAppPassword": "<your-AppPwd>"
}
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour lire les paramètres ci-dessus dans votre code C#, vous allez utiliser l’objet de configuration fourni par ASP.NET Core, par exemple : **Startup.cs**
```csharp
var appId = Configuration.GetSection("MicrosoftAppId").Value;
var appPassword = Configuration.GetSection("MicrosoftAppPassword").Value;
options.CredentialProvider = new SimpleCredentialProvider(appId, appPassword);
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
Dans JavaScript, référencez des variables .env en dehors de l’objet `process.env`, par exemple : **index.js**
```js
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});
```

---

Si nécessaire, provisionnez des ressources et connectez-les à votre bot à l’aide du fichier appsettings.json ou .env.


## <a name="faq"></a>Forum Aux Questions
**Q :** Je souhaite créer un bot V4 dans le portail Azure. En quoi l’utilisation du portail Azure a-t-elle changé avec la suppression du fichier .bot ?

**R :** Quand vous créez un bot dans le portail Azure, le fichier .bot n’est pas créé. Vous pouvez utiliser la section **Paramètres d’application** dans le **portail Azure** pour rechercher des ID/clés. Lorsque vous téléchargez le code, ces paramètres sont stockés dans le fichier appsettings.json ou .env pour vous. Dans votre bot, vous pouvez mettre à jour le code pour lire les paramètres avant de faire appel au service individuel. Une fois que vous avez mis à jour le code de votre bot, vous pouvez utiliser az bot publish pour déployer votre bot.

**Q :** Qu’en est-il des bots V3 ?

**R :** Le scénario pour un bot V3 est similaire à celui pour les bots V4 sans un fichier .bot. Le processus de déploiement continue de fonctionner. 

## <a name="additional-resources"></a>Ressources supplémentaires
- Pour savoir comment déployer le bot, consultez la rubrique sur le [déploiement](../bot-builder-deploy-az-cli.md).
- Pour protéger les clés et secrets, nous vous recommandons d’utiliser Azure Key Vault. Azure Key Vault est un outil pour stocker les secrets et y accéder en toute sécurité, comme le point de terminaison de votre bot et les clés de création. Il fournit une solution de [gestion des clés](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) et permet de créer et contrôler facilement vos clés de chiffrement. Vous avez ainsi un contrôle étroit sur vos secrets.


<!--

# Manage resources with a .bot file

Bots usually consume lots of different services, such as [LUIS.ai](https://luis.ai) or [QnaMaker.ai](https://qnamaker.ai). When you are developing a bot, there is no uniform place to store the metadata about the services that are in use.  This prevents us from building tooling that looks at a bot as a whole.

To address this problem, we have created a **.bot file** to act as the place to bring all service references together in one place to 
enable tooling.  For example, the Bot Framework Emulator ([V4](https://aka.ms/Emulator-wiki-getting-started)) uses a  .bot file to create a unified view over the connected services your bot consumes.  

With a .bot file, you can register services like:

* **Localhost** local debugger endpoints
* [**Azure Bot Service**](https://azure.microsoft.com/en-us/services/bot-service/) Azure Bot Service registrations.
* [**LUIS.AI**](https://www.luis.ai/) LUIS gives your bot the ability to communicate with people using natural language.. 
* [**QnA Maker**](https://qnamaker.ai/) Build, train and publish a simple question and answer bot based on FAQ URLs, structured documents or editorial content in minutes.
* [**Dispatch**](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) models for dispatching across multiple services.
* [**Azure Application Insights**](https://azure.microsoft.com/en-us/services/application-insights/) for insights and bot analytics.
* [**Azure Blob Storage**](https://azure.microsoft.com/en-us/services/storage/blobs/) for bot state persistence. 
* [**Azure Cosmos DB**](https://azure.microsoft.com/en-us/services/cosmos-db/) - globally distributed, multi-model database service to persist bot state.

Apart from these, your bot might rely on other custom services. You can leverage the [generic service](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/add-services.md) capability to connect a generic service configuration.

## When is a .bot file created? 
- If you create a bot using [Azure Bot Service](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryResultsListBlade/selectedSubMenuItemId/%7B%22menuItemId%22%3A%22gallery%2FCognitiveServices_MP%2FBotService%22%2C%22resourceGroupId%22%3A%22%22%2C%22resourceGroupLocation%22%3A%22%22%2C%22dontDiscardJourney%22%3Afalse%2C%22launchingContext%22%3A%7B%22source%22%3A%5B%22GalleryFeaturedMenuItemPart%22%5D%2C%22menuItemId%22%3A%22CognitiveServices_MP%22%2C%22subMenuItemId%22%3A%22BotService%22%7D%7D), a .bot file is automatically created for you with list of connected services provisioned. The .bot is encrypted by default.
- If you create a bot using Bot Framework V4 SDK [Template](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4) for Visual Studio or using Bot Builder [Yeoman Generator](https://www.npmjs.com/package/generator-botbuilder), a .bot file is automatically created. No connected services are provisioned in this flow and the bot file is not encrypted.
- If you are starting with [BotBuilder-samples](https://github.com/Microsoft/botbuilder-samples), every sample for Bot Framework V4 SDK includes a .bot file and the .bot file is not encrypted. 
- You can also create a bot file using the [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md) tool.

## What does a bot file look like? 
Take a look at a sample [.bot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/sample-bot-file.json) file.
To learn about encrypting and decrypting the .bot file, see [Bot Secrets](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/docs/bot-file-encryption.md).

## Why do I need a .bot file?

A .bot file is **not** a requirement to build bots with Bot Framework SDK. You can continue to use appsettings.json, web.config, env, 
keyvault or any mechanism you see fit to keep track of service references and keys that your bot depends on. However, to test
the bot using the Emulator, you'll need a .bot file. The good news is that Emulator can create a .bot file for testing. To do that, 
start the Emulator, click on the **create a new bot configuration** link on the Welcome page. In the dialog box that appears, type a **Bot name** and an **Endpoint URL**. Then connect.

The advantages of using .bot file are:
- Provides a standard way of storing resources regardless of the language/platform you use.   
- Bot Framework Emulator and CLI tools rely on and work great with tracking connected services in a consistent format (in a .bot file) 
- Elegant tooling solutions around services creation and management is harder without a well defined schema (.bot file).  


## Using .bot file in your Bot Framework SDK bot

You can use the .bot file to get service configuration information in your bot's code. The BotFramework-Configuration library available 
for [C#](https://www.nuget.org/packages/Microsoft.Bot.Configuration) and [JS](https://www.npmjs.com/package/botframework-config) helps you load a bot file and supports several methods to query and get the appropriate service configuration information.

## Additional resources
Refer to [MSBot](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/MSBot/README.md) readme file for more information on using a bot file.

-->

