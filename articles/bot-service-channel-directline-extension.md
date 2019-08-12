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
ms.openlocfilehash: 27067cf2582de63cf67785cfaaa70b9a33685894
ms.sourcegitcommit: a1eaa44f182a7210197bd793250907df00e9edab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68757761"
---
## <a name="direct-line-app-service-extension"></a>Extension App Service Direct Line

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

L’extension App Service Direct Line établit un ensemble de **canaux nommés persistants** pour se connecter à un bot via un **BotAdapter** que le client peut ajouter.

Elle ajoute un nouvel ensemble d’extensions de streaming au protocole Bot Framework. Ces extensions remplacent HTTP comme transport pour échanger des messages avec un transport qui autorise l’envoi de requêtes bidirectionnelles sur un **WebSocket persistant**. Cela améliore les performances et permet une plus grande isolation pendant l’échange d’informations.

Avant les extensions de streaming, l’API Direct Line offrait une méthode permettant à un client d’envoyer des activités à Direct Line et deux méthodes pour un client de récupérer des activités de Direct Line. Les messages étaient envoyés via une requête HTTP POST et reçus par HTTP GET (interrogation) ou en ouvrant un WebSocket pour recevoir des ensembles d’activités.
Les extensions de streaming étendent l’utilisation du WebSocket et permettent l’envoi de **toute la communication par messagerie** sur ce WebSocket. Les extensions de streaming peuvent également être utilisées entre les services de canal et le bot.


L’extension App Service Direct Line est préinstallée sur toutes les instances d’Azure App Services de chaque centre de données dans le monde entier. Elle est conservée et gérée par Microsoft sans travail de déploiement supplémentaire pour le client.
Elle est désactivée par défaut sur Azure App Services, mais elle peut facilement être activée pour pouvoir se connecter à votre bot hébergé.

L’illustration suivante montre l’architecture globale :

![Architecture de l’extension Direct Line](./media/channels/direct-line-extension-architecture.png)

## <a name="see-also"></a>Voir aussi

|Nom|Description|
|---|---|
|[Bot .NET avec extension](bot-service-channel-directline-extension-net-bot.md)|Mettez à jour un bot pour qu’il fonctionne avec des **canaux nommés** et activez l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  |
|[Client .NET avec extension](bot-service-channel-directline-extension-net-client.md)|Créez un client .NET en C# qui se connecte à l’extension App Service Direct Line|
|[WebChat avec extension](bot-service-channel-directline-extension-webchat-client.md)|Utilisez WebChat avec l’extension App Service Direct Line|
|[Utiliser une extension dans un réseau virtuel](bot-service-channel-directline-extension-vnet.md)|Utilisez l’extension App Service Direct Line avec un réseau virtuel Azure|

## <a name="addtional-resources"></a>Ressources supplémentaires

- [Connecter un robot à Direct Line](bot-service-channel-connect-directline.md)
- [Connecter un bot à Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
