---
title: Extension App Service Direct Line
titleSuffix: Bot Service
description: Fonctionnalités de l’extension App Service Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/09/2019
ms.openlocfilehash: f5d23d31b0f2665993a8cc774d7c4577d0333441
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68970595"
---
# <a name="direct-line-app-service-extension"></a>Extension App Service Direct Line

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

L’extension App Service Direct Line permet aux clients de se connecter directement à l’hôte où se trouve le bot. Cela permet d’isoler les charges de travail et, dans certains cas, d’améliorer les performances. L’illustration suivante montre l’architecture globale :

![Architecture de l’extension Direct Line](./media/channels/direct-line-extension-architecture.png)

L’extension App Service Direct Line ajoute un nouvel ensemble d’extensions de streaming au protocole Bot Framework, celles-ci remplaçant HTTP pour l’échange de messages par un transport qui autorise l’envoi de requêtes bidirectionnelles sur un **WebSocket persistant.** .

Avant les extensions de streaming, l’API Direct Line offrait une méthode permettant à un client d’envoyer des activités à Direct Line et deux méthodes pour un client de récupérer des activités de Direct Line. Les messages étaient envoyés par le biais d’une requête HTTP POST et reçus par HTTP GET (interrogation) ou en ouvrant un WebSocket pour recevoir des ensembles d’activités.
Les extensions de streaming étendent l’utilisation du WebSocket et permettent l’envoi de **toute la communication par messagerie** sur ce WebSocket. Les extensions de streaming peuvent également être utilisées entre les services de canal et le bot.

L’extension App Service Direct Line est préinstallée sur toutes les instances d’Azure App Services de chaque centre de données dans le monde entier. Elle est conservée et gérée par Microsoft sans travail de déploiement supplémentaire pour le client. Elle est désactivée par défaut sur Azure App Services, mais elle peut facilement être activée pour pouvoir se connecter à votre bot hébergé.


## <a name="see-also"></a>Voir aussi

|Nom|Description|
|---|---|
|[Configurer un bot .NET pour l’extension](bot-service-channel-directline-extension-net-bot.md)|Mettez à jour un bot pour qu’il fonctionne avec des **canaux nommés** et activez l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  |
|[Créer un client .NET avec l’extension](bot-service-channel-directline-extension-net-client.md)|Créez un client .NET en C# qui se connecte à l’extension App Service Direct Line|
|[Utiliser l’extension avec Webchat](bot-service-channel-directline-extension-webchat-client.md)|Utiliser WebChat avec l’extension App Service Direct Line|
|[Utiliser une extension dans un réseau virtuel](bot-service-channel-directline-extension-vnet.md)|Utiliser l’extension App Service Direct Line avec un réseau virtuel Azure|

## <a name="addtional-resources"></a>Ressources supplémentaires

- [Connecter un robot à Direct Line](bot-service-channel-connect-directline.md)
- [Connecter un bot à Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
