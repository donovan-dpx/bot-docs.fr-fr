---
title: Gérer les ressources du bot | Microsoft Docs
description: Décrit l’objectif et l’utilisation du fichier de bot.
keywords: fichier de bot, .bot, fichier .bot, msbot, ressources de bot, gérer les ressources de bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b4a5e3f0271d6b80a4245294e520ebc5abaede4e
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66214138"
---
# <a name="manage-bot-resources"></a>Gérer les ressources du bot

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Généralement, les bots utilisent différents services tels que [LUIS.ai](https://luis.ai) ou [QnaMaker.ai](https://qnamaker.ai). Quand vous développez un bot, vous devez être en mesure de les suivre tous. Vous pouvez utiliser différentes méthodes comme appsettings.json, web.config ou .env. 

> [!IMPORTANT]
> Avant la version 4.3 du kit SDK Bot Framework, nous proposions le fichier .bot comme mécanisme pour gérer les ressources. Toutefois, nous vous recommandons à l’avenir d’utiliser le fichier appsettings.json ou .env pour gérer ces ressources. Les bots qui utilisent le fichier .bot continuent de fonctionner pour l’instant, même si le fichier .bot a été **_déprécié_** . Si vous utilisez un fichier .bot pour gérer les ressources, suivez les étapes qui s’appliquent pour migrer les paramètres. 

## <a name="migrating-settings-from-bot-file"></a>Migration des paramètres à partir du fichier .bot
Les sections suivantes expliquent comment migrer les paramètres à partir du fichier .bot. Suivez le scénario qui vous convient.

**Scénario 1 : Bot local avec un fichier .bot**

Dans ce scénario, vous avez un bot local qui utilise un fichier .bot, mais le _bot n’a pas été migré_ vers le portail Azure. Suivez les étapes ci-dessous pour migrer les paramètres du fichier .bot vers le fichier appsettings.json ou .env.

- Si le fichier .bot est chiffré, vous devez le déchiffrer à l’aide de la commande suivante :

```cli
msbot secret --bot <name-of-bot-file> --secret "<bot-file-secret>" --clear
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

*Si nécessaire*, provisionnez des ressources et connectez-les à votre bot à l’aide du fichier appsettings.json ou .env.

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

*Si nécessaire*, provisionnez des ressources et connectez-les à votre bot à l’aide du fichier appsettings.json ou .env.

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

## <a name="additional-resources"></a>Ressources supplémentaires
- Pour savoir comment déployer le bot, consultez la rubrique sur le [déploiement](../bot-builder-deploy-az-cli.md).
- Apprenez à utiliser [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-overview) pour protéger et gérer les clés de chiffrement et les secrets utilisés par les services et les applications cloud.
