---
title: Utiliser l’extension App Service Direct Line au sein d’un réseau virtuel
titleSuffix: Bot Service
description: Utiliser l’extension App Service Direct Line au sein d’un réseau virtuel
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 7c565d77879641d92a3e331852ff38ea21fdaf9e
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68866446"
---
# <a name="use-direct-line-app-service-extension-within-a-vnet"></a>Utiliser l’extension App Service Direct Line au sein d’un réseau virtuel

Cet article explique comment utiliser l’extension App Service Direct Line avec un réseau virtuel Azure.

## <a name="create-an-app-service-environment-and-other-azure-resources"></a>Créer un environnement App Service Environment et d’autres ressources Azure

1. L’extension App Service Direct Line est disponible sur tous les **services Azure App Services**, notamment ceux hébergés dans un **environnement Azure App Service Environment**. Un environnement Azure App Service Environment assure l’isolation et est idéal pour travailler au sein d’un réseau virtuel.
    - Vous trouverez des instructions sur la création d’un environnement App Service Environment externe dans l’article [Créer un environnement App Service externe](https://docs.microsoft.com/en-us/azure/app-service/environment/create-external-ase).
    - Vous trouverez des instructions sur la création d’un environnement App Service Environment interne dans l’article [Créer et utiliser un environnement App Service Environment avec équilibreur de charge interne](https://docs.microsoft.com/en-us/azure/app-service/environment/create-ilb-ase).
1. Une fois que vous avez créé votre environnement App Service Environment, vous devez ajouter un plan App Service à l’intérieur de celui-ci dans lequel vous pouvez déployer vos bots (et donc exécuter l’extension App Service Direct Line). Pour ce faire :
    - Accédez à https://portal.azure.com/
    - Créez une ressource « Plan App Service ».
    - Sous Région, sélectionnez votre environnement App Service Environment
    - Terminez la création de votre plan App Service

## <a name="configure-the-vnet-network-security-groups-nsg"></a>Configurer les groupes de sécurité réseau de réseau virtuel

1. L’extension App Service Direct Line requiert une connexion sortante pour pouvoir émettre des requêtes HTTP. Cela peut être configuré comme une règle de trafic sortant dans votre groupe de sécurité réseau de réseau virtuel associé au sous-réseau de l’environnement App Service Environment. La règle requise est la suivante :

|Source|Quelconque|
|---|---|
|Port source|*|
|Destination|Adresses IP|
|Adresses IP de destination|20.38.80.64, 40.82.248.64|
|Plages de ports de destination|443|
|Protocole|Quelconque|
|Action|AUTORISER|


![Architecture de l’extension Direct Line](./media/channels/direct-line-extension-vnet.png)

>[!NOTE]
> Les adresses IP fournies sont explicitement pour la préversion. Nous allons passer à une balise de service pour Azure Bot Service plus tard dans l’année, ce qui modifiera cette configuration.

### <a name="configure-your-bots-app-service"></a>Configurer le service App Service de votre bot

Pour la préversion, vous devez modifier la manière dont votre extension App Service Direct Line communique avec Azure. Pour ce faire, ajoutez un nouveau **paramètre d’application App Service** à votre application en utilisant le portail ou le fichier `applicationsettings.json` :

- Propriété : DirectLineExtensionABSEndpoint
- Valeur: https://st-directline.botframework.com/v3/extension

>[!NOTE]
> Cela sera uniquement requis pour la préversion de l’extension App Service Direct Line.
