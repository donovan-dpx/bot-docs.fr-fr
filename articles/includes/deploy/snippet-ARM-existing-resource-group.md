---
ms.openlocfilehash: 029275eae6f7b0b4448613ede898575eb491a56f
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386062"
---
Quand vous utilisez un groupe de ressources existant, vous pouvez utiliser un plan App Service existant ou en créer un. Les étapes de ces deux options sont indiquées ci-dessous. 

**Option 1 : Plan App Service existant** 

Ici, nous utilisons un plan App Service existant, mais nous créons une application web et une inscription Bot Channels Registration. 

> [!NOTE]
> Cette commande définit l’ID et le nom d’affichage du bot. Le paramètre `botId`, qui est utilisé comme ID de bot immuable, doit être globalement unique. Le nom d’affichage du bot est mutable.

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
