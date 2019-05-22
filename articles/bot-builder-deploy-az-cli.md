---
title: Déployer votre bot | Microsoft Docs
description: Déployez votre bot sur le cloud Azure.
keywords: déployer bot, déployer bot azure, publier bot
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 05/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6e7bd108bc67595c1d523c3c038e8b403ce387b1
ms.sourcegitcommit: 4086189a9c856fbdc832eb1a1d205e5f1b4e3acd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65733297"
---
# <a name="deploy-your-bot"></a>Déployer votre bot

[!INCLUDE [applies-to](./includes/applies-to.md)]

Dans cet article, nous allons vous montrer comment déployer votre bot dans Azure. Il est plus judicieux de lire cet article avant de suivre les étapes, afin de bien comprendre ce qu’implique le déploiement d’un bot.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, créez un [compte](https://azure.microsoft.com/free/) avant de commencer.
- Un bot CSharp, JavaScript ou TypeScript que vous avez développé sur votre ordinateur local.
- Version la plus récente d’[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest).

## <a name="1-prepare-for-deployment"></a>1. Préparation du déploiement
Quand vous créez un bot à l’aide de modèles Visual Studio ou Yeoman, le code source généré contient un dossier `deploymentTemplates` avec des modèles ARM. Le processus de déploiement documenté ici utilise le modèle ARM pour provisionner les ressources requises pour le bot dans Azure à l’aide d’Azure CLI. 

> [!IMPORTANT]
> Avec la publication du SDK Bot Framework 4.3, nous avons _déprécié_ l’utilisation du fichier .bot en faveur d’un fichier appsettings.json ou .env pour la gestion des ressources. Pour plus d’informations sur la migration des paramètres du fichier .bot vers le fichier appsettings.json ou .env, consultez [Gérer les ressources du bot](v4sdk/bot-file-basics.md).

### <a name="login-to-azure"></a>Connexion à Azure

Vous avez déjà créé et testé un bot localement et vous souhaitez maintenant le déployer sur Azure. Ouvrez une invite de commandes pour vous connecter au portail Azure.

```cmd
az login
```
Une nouvelle fenêtre de navigateur s’ouvre ; connectez-vous.

### <a name="set-the-subscription"></a>Définir l’abonnement
Définissez l’abonnement par défaut à utiliser.

```cmd
az account set --subscription "<azure-subscription>"
```

Si vous ne savez pas quel abonnement utiliser pour déployer le bot, vous pouvez consulter la liste des abonnements pour votre compte à l’aide de la commande `az account list`. Accédez au dossier bot.

### <a name="create-an-app-registration"></a>Créer une inscription d’application
Inscrire l’application signifie que vous pouvez utiliser Azure AD pour authentifier les utilisateurs et demander l’accès aux ressources utilisateur. Votre bot a besoin d’une application inscrite dans Azure qui permet au bot d’accéder à Bot Framework Service pour l’envoi et la réception de messages authentifiés. Pour créer et inscrire une application par le biais de l’interface Azure CLI, exécutez la commande suivante :

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Option   | Description |
|:---------|:------------|
| display-name | Nom d’affichage de l’application. |
| password | Mot de passe, également appelé « secret client ». Le mot de passe doit comporter au moins 16 caractères, contenir au moins un caractère alphabétique minuscule ou majuscule, et contenir au moins un caractère spécial.|
| available-to-other-tenants| L’application peut être utilisée à partir de n’importe quel locataire Azure AD. Ce paramètre doit être `true` pour permettre à votre bot de fonctionner avec les canaux Azure Bot Service.|

La commande ci-dessus génère du code JSON avec la clé `appId` et enregistre la valeur de cette clé pour le déploiement ARM, où elle sera utilisée pour le paramètre `appId`. Le mot de passe fourni sera utilisé pour le paramètre `appSecret`.

Vous pouvez déployer votre bot dans un groupe de ressources nouveau ou existant. Choisissez l’option qui vous convient le mieux.

# <a name="deploy-via-arm-template-with-new-resource-grouptabnewrg"></a>[Déployer par le biais du modèle ARM (avec un **nouveau** groupe de ressources)](#tab/newrg)

### <a name="create-azure-resources"></a>Créer des ressources Azure

Vous allez créer un groupe de ressources dans Azure, puis utiliser le modèle ARM pour créer les ressources qui y sont spécifiées. Ici, nous spécifions le plan App Service, l’application web et une inscription Bot Channels Registration.

```cmd
az deployment create --name "<name-of-deployment>" --template-file "template-with-new-rg.json" --location "location-name" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" botSku=F0 newAppServicePlanName="<name-of-app-service-plan>" newWebAppName="<name-of-web-app>" groupName="<new-group-name>" groupLocation="<location>" newAppServicePlanLocation="<location>"
```

| Option   | Description |
|:---------|:------------|
| Nom | Nom convivial pour le déploiement. |
| template-file | Chemin du modèle ARM. Vous pouvez utiliser le fichier `template-with-new-rg.json` fourni dans le dossier `deploymentTemplates` du projet. |
| location |Lieu. Valeurs provenant de : `az account list-locations`. Vous pouvez configurer le lieu par défaut en utilisant `az configure --defaults location=<location>`. |
| parameters | Spécifiez les valeurs des paramètres du déploiement. Valeur `appId` que vous avez obtenue en exécutant la commande `az ad app create`. `appSecret` est le mot de passe que vous avez fourni à l’étape précédente. Le paramètre `botId`, qui est utilisé comme ID de bot immuable, doit être globalement unique. Il sert aussi à configurer le nom d’affichage du bot, qui est mutable. `botSku` est le niveau tarifaire ; il peut s’agir de F0 (gratuit) ou S1 (Standard). `newAppServicePlanName` est le nom du plan App Service. `newWebAppName` est le nom de l’application web que vous créez. `groupName` est le nom du groupe de ressources Azure que vous créez. `groupLocation` est l’emplacement du groupe de ressources Azure. `newAppServicePlanLocation` est l’emplacement du plan App Service. |

# <a name="deploy-via-arm-template-with-existing--resource-grouptaberg"></a>[Déployer par le biais du modèle ARM (avec un groupe de ressources **existant**)](#tab/erg)

### <a name="create-azure-resources"></a>Créer des ressources Azure

Quand vous utilisez un groupe de ressources existant, vous pouvez utiliser un plan App Service existant ou en créer un. Les étapes de ces deux options sont indiquées ci-dessous. 

**Option 1 : Plan App Service existant** 

Ici, nous utilisons un plan App Service existant, mais nous créons une application web et une inscription Bot Channels Registration. 

_Remarque : Le paramètre botId, qui est utilisé comme ID de bot immuable, doit être globalement unique. Il sert aussi à configurer le nom d’affichage du bot, qui est mutable._

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" existingAppServicePlan="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

**Option 2 : Nouveau plan App Service** 

Ici, nous créons le plan App Service, l’application web et une inscription Bot Channels Registration. 

```cmd
az group deployment create --name "<name-of-deployment>" --resource-group "<name-of-resource-group>" --template-file "template-with-preexisting-rg.json" --parameters appId="<msa-app-guid>" appSecret="<msa-app-password>" botId="<id-or-name-of-bot>" newWebAppName="<name-of-web-app>" newAppServicePlanName="<name-of-app-service-plan>" appServicePlanLocation="<location>"
```

| Option   | Description |
|:---------|:------------|
| Nom | Nom convivial pour le déploiement. |
| resource-group | Nom du groupe de ressources Azure. |
| template-file | Chemin du modèle ARM. Vous pouvez utiliser le fichier `template-with-preexisting-rg.json` fourni dans le dossier `deploymentTemplates` du projet. |
| location |Lieu. Valeurs provenant de : `az account list-locations`. Vous pouvez configurer le lieu par défaut en utilisant `az configure --defaults location=<location>`. |
| parameters | Spécifiez les valeurs des paramètres du déploiement. Valeur `appId` que vous avez obtenue en exécutant la commande `az ad app create`. `appSecret` est le mot de passe que vous avez fourni à l’étape précédente. Le paramètre `botId`, qui est utilisé comme ID de bot immuable, doit être globalement unique. Il sert aussi à configurer le nom d’affichage du bot, qui est mutable. `newWebAppName` est le nom de l’application web que vous créez. `newAppServicePlanName` est le nom du plan App Service. `newAppServicePlanLocation` est l’emplacement du plan App Service. |

---

### <a name="retrieve-or-create-necessary-iiskudu-files"></a>Récupérer ou créer les fichiers IIS/Kudu nécessaires

**Pour les bots C#**

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Vous devez fournir le chemin du fichier .csproj par rapport à --code-dir. Vous pouvez, pour cela, utiliser l’argument --proj-file-path. La commande résoudrait --code-dir et --proj-file-path en « ./MyBot.csproj ».

**Pour les bots JavaScript**

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Cette commande récupère un fichier web.config qui est nécessaire au fonctionnement des applications Node.js avec IIS sur Azure App Services. Vérifiez que le fichier web.config est enregistré à la racine de votre bot.

**Pour les bots TypeScript**

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Cette commande fonctionne de manière similaire à la commande pour JavaScript ci-dessus, mais pour un bot Typescript.

### <a name="zip-up-the-code-directory-manually"></a>Compresser le répertoire du code manuellement

Quand vous utilisez l’[API de déploiement à partir d’un fichier zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) non configurée pour déployer le code de votre bot, le comportement de Kudu/de l’application web est le suivant :

_Par défaut, Kudu part du principe que les déploiements à partir de fichiers zip sont prêts à être exécutés et ne nécessitent aucune étape de génération supplémentaire pendant le déploiement, telle que npm install ou dotnet restore/dotnet publish._

Par conséquent, il est important d’inclure votre code généré et toutes les dépendances nécessaires dans le fichier zip déployé sur l’application web, sans quoi votre bot ne fonctionnera pas comme prévu.

> [!IMPORTANT]
> Avant de compresser vos fichiers projet, vérifiez que vous êtes bien _dans_ le dossier approprié. 
> - Pour les bots C#, il s’agit du dossier contenant le fichier .csproj. 
> - Pour les bots JS, il s’agit du dossier contenant le fichier app.js ou index.js. 
>
> Si l’emplacement de votre dossier racine est incorrect, l’**exécution du bot échouera dans le portail Azure**.

## <a name="2-deploy-code-to-azure"></a>2. Déployer le code sur Azure
À ce stade, nous sommes prêts à déployer le code sur l’application web Azure. Exécutez la commande suivante à partir de la ligne de commande pour effectuer un déploiement push de fichier zip kudu pour une application web.

```cmd
az webapp deployment source config-zip --resource-group "<new-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Option   | Description |
|:---------|:------------|
| resource-group | Nom du groupe de ressources dans Azure que vous avez créé précédemment. |
| Nom | Nom de l’application web utilisée précédemment. |
| src  | Chemin du fichier compressé que vous avez créé. |

## <a name="3-test-in-web-chat"></a>3. Tester dans la Discussion Web
- Dans le portail Azure, accédez au panneau de votre bot d’application web.
- Dans la section **Gestion du bot**, cliquez sur **Tester dans la Discussion Web**. Azure Bot Service charge le contrôle Discussion Web et se connecte à votre bot.
- Attendez quelques secondes après un déploiement réussi et redémarrez éventuellement votre application Web App pour effacer tous les caches. Revenez à votre panneau Web App Bot et testez le bot à l’aide du Web Chat fourni dans le portail Azure.

## <a name="additional-information"></a>Informations supplémentaires
Le déploiement de votre bot sur Azure implique de payer les services que vous utilisez. L’article sur la [gestion de la facturation et des coûts](https://docs.microsoft.com/en-us/azure/billing/) vous aide à comprendre la facturation Azure, à superviser votre utilisation et vos coûts, ainsi qu’à gérer votre compte et vos abonnements.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Configurer le déploiement continu](bot-service-build-continuous-deployment.md)
