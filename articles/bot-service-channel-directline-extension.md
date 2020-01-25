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
ms.openlocfilehash: 2145709c9cc458ec5dd9f4fbf1145ddbad893a04
ms.sourcegitcommit: df2b8d4e29ebfbb9e8a10091bb580389fe4c34cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256002"
---
# <a name="direct-line-app-service-extension"></a>Extension App Service Direct Line

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

> [!WARNING]
> L’**extension Direct Line App Service** est en **préversion** publique.  

L’extension App Service Direct Line permet aux clients de se connecter directement à l’hôte où se trouve le bot. Cela permet d’isoler les charges de travail et, dans certains cas, d’améliorer les performances. L’illustration suivante montre l’architecture globale :

![Architecture de l’extension Direct Line](./media/channels/direct-line-extension-architecture.png)

L’extension App Service Direct Line ajoute un nouvel ensemble d’extensions de streaming au protocole Bot Framework, celles-ci remplaçant HTTP pour l’échange de messages par un transport qui autorise l’envoi de requêtes bidirectionnelles sur un **WebSocket persistant.** .

Avant les extensions de streaming, l’API Direct Line offrait une méthode permettant à un client d’envoyer des activités à Direct Line et deux méthodes pour un client de récupérer des activités de Direct Line. Les messages étaient envoyés par le biais d’une requête HTTP POST et reçus par HTTP GET (interrogation) ou en ouvrant un WebSocket pour recevoir des ensembles d’activités.
Les extensions de streaming étendent l’utilisation du WebSocket et permettent l’envoi de **toute la communication par messagerie** sur ce WebSocket. Les extensions de streaming peuvent également être utilisées entre les services de canal et le bot.

L’extension App Service Direct Line est préinstallée sur toutes les instances d’Azure App Services de chaque centre de données dans le monde entier. Elle est conservée et gérée par Microsoft sans travail de déploiement supplémentaire pour le client. Elle est désactivée par défaut sur Azure App Services, mais elle peut facilement être activée pour pouvoir se connecter à votre bot hébergé.


## <a name="see-also"></a>Voir aussi

|Name|Description|
|---|---|
|[Configurer un bot .NET pour l’extension](bot-service-channel-directline-extension-net-bot.md)|Mettez à jour un bot .NET pour qu’il fonctionne avec des **canaux nommés** et activez l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  |
|[Configurer un bot Node.js pour l’extension](bot-service-channel-directline-extension-node-bot.md)|Mettez à jour un bot Node.js pour qu’il fonctionne avec des **canaux nommés** et activez l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  |
|[Créer un client .NET avec l’extension](bot-service-channel-directline-extension-net-client.md)|Créez un client .NET en C# qui se connecte à l’extension App Service Direct Line|
|[Utiliser l’extension avec Webchat](bot-service-channel-directline-extension-webchat-client.md)|Utiliser WebChat avec l’extension App Service Direct Line|
|[Utiliser une extension dans un réseau virtuel](bot-service-channel-directline-extension-vnet.md)|Utiliser l’extension App Service Direct Line avec un réseau virtuel Azure|

## <a name="addtional-resources"></a>Ressources supplémentaires

- [Connecter un robot à Direct Line](bot-service-channel-connect-directline.md)
- [Connecter un bot à Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
