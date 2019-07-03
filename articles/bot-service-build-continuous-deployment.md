---
title: Configurer le déploiement continu pour un service de bot | Microsoft Docs
description: Découvrez comment configurer le déploiement continu à partir du contrôle de code source pour un service de bot.
keywords: déploiement continu, publier, déployer, portail azure
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8d324138db60c2b34f9bebd3ff53c30a12c3cefa
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67405957"
---
# <a name="set-up-continuous-deployment"></a>Configurer un déploiement continu

[!INCLUDE [applies-to](./includes/applies-to.md)]

Cet article vous explique comment configurer un déploiement continu pour votre bot. Vous pouvez activer le déploiement continu pour déployer automatiquement les modifications de code à partir de votre dépôt source dans Azure. Dans cette rubrique, nous parlerons de la configuration du déploiement continu pour GitHub. Pour des informations sur la configuration du déploiement continu avec d’autres systèmes de contrôle de code source, consultez la section des ressources supplémentaires au bas de cette page.

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](http://portal.azure.com) avant de commencer.
- Vous **devez** [déployer votre bot dans Azure](bot-builder-deploy-az-cli.md) avant d’activer le déploiement continu.

## <a name="prepare-your-repository"></a>Préparer votre dépôt
Vérifiez que la racine de votre dépôt comprend les fichiers nécessaires à votre projet. Cela vous permettra d’obtenir des builds automatiques à partir du serveur de build Kudu d’Azure App Service. 

|Runtime | Fichiers du répertoire racine |
|:-------|:---------------------|
| ASP.NET Core | .sln ou .csproj |
| Node.js | server.js, app.js ou package.json avec un script de démarrage |


## <a name="continuous-deployment-using-github"></a>Déploiement continu avec GitHub
Pour activer le déploiement continu avec GitHub, accédez à la page **App Service** de votre bot dans le portail Azure.

Cliquez sur **Centre de déploiement** > **GitHub** > **Autoriser**.

![Déploiement continu](~/media/azure-bot-build/azure-deployment.png)

Dans la fenêtre de navigateur qui s’ouvre, cliquez sur **Autoriser AzureAppService**. 

![Autorisation d’Azure Github](~/media/azure-bot-build/azure-deployment-github.png)

Après avoir autorisé **AzureAppService**, revenez au **Centre de déploiement** dans le portail Azure.

1. Cliquez sur **Continuer**. 

1. Sélectionnez **Service de build App Service**.

1. Cliquez sur **Continuer**.

1. Sélectionnez **Organisation**, **Référentiel** et **Branche**.

1. Cliquez sur **Continuer**, puis sur **Terminer** pour terminer la configuration.

À ce stade, le déploiement continu avec GitHub est configuré. Chaque fois que vous validez dans le référentiel de code source, vos modifications sont automatiquement déployées dans Azure Bot Service.

## <a name="disable-continuous-deployment"></a>Désactiver le déploiement continu

Même si votre bot est configuré pour le déploiement continu, vous ne pouvez pas utiliser l’éditeur de code en ligne pour apporter des modifications à votre bot. Si vous souhaitez utiliser l’éditeur de code en ligne, vous pouvez désactiver temporairement le déploiement continu.

Pour désactiver le déploiement continu, procédez comme suit :
1. Dans le [portail Azure](https://portal.azure.com), accédez au panneau de **tous les paramètres App Service** et cliquez sur **Centre de déploiement**. 
1. Cliquez sur **Déconnecter** pour désactiver le déploiement continu. Pour réactiver le déploiement continu, répétez les étapes décrites dans les sections correspondantes ci-dessus.

## <a name="additional-resources"></a>Ressources supplémentaires
- Pour activer le déploiement continu à partir de BitBucket et d’Azure DevOps Services, consultez [Déploiement continu à l’aide d’Azure App Service](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).


