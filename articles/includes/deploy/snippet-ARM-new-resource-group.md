---
ms.openlocfilehash: fd279af81e0c94ac22f54429cb8ae330e5d60661
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386035"
---
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
