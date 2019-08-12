---
title: Concepts clés des services Bot Connector et Bot State | Microsoft Docs
description: Comprenez les concepts clés des services Bot Connector et Bot State de Bot Framework.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/16/2019
ms.openlocfilehash: a2fa3f5e1363cb155504cdf903df2f6c25877af3
ms.sourcegitcommit: a1eaa44f182a7210197bd793250907df00e9edab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68756950"
---
# <a name="key-concepts"></a>Concepts clés

Vous pouvez utiliser le service Bot Connector pour communiquer avec les utilisateurs via plusieurs canaux tels que Skype, Courrier, Slack, etc. Cet article présente les concepts clés du service Bot Connector.

## <a name="bot-connector-service"></a>Service Bot Connector

Le service Bot Connector permet à votre robot d’échanger des messages avec des canaux configurés dans le <a href="https://dev.botframework.com/" target="_blank">Portail Bot Framework</a>. Il utilise des ressources REST et JSON standard sur HTTPS, et permet l’authentification avec des jetons de support JWT. Pour plus d’informations sur l’utilisation du service Bot Connector, voir [ Authentification](bot-framework-rest-connector-authentication.md) et les autres articles de cette section.

### <a name="activity"></a>Activité

Le service Bot Connector échange des informations entre bot et canal (utilisateur) en transmettant un objet `Activity`. Le type d’activité le plus courant est **message**, mais il existe d’autres types d’activités qui peuvent être utilisés pour communiquer différents types d’informations à un bot ou à un canal. Pour plus d’informations sur les activités dans le service Bot Connector, voir [Vue d’ensemble des activités](bot-framework-rest-connector-activities.md).

## <a name="bot-state-service"></a>Service Bot State

Le service Microsoft Bot Framework State a été mis hors service le 30 mars 2018. Auparavant, les bots basés sur Azure Bot Service ou le kit SDK Bot Builder avaient une connexion par défaut à ce service hébergé par Microsoft pour stocker les données d’état du bot. Les bots doivent être mis à jour pour utiliser leur propre stockage d’état.

## <a name="authentication"></a>Authentication

Le service Bot Connector permet l’authentification à l’aide de jetons de support JWT. Pour plus d’informations sur l’authentification des demandes sortantes que votre robot envoie à Bot Framework, sur l’authentification des demandes entrantes que votre robot reçoit de Bot Framework, et bien plus, voir [ Authentification ](bot-framework-rest-connector-authentication.md). 

## <a name="client-libraries"></a>Bibliothèques clientes

Le Bot Framework fournit des bibliothèques clientes utilisables pour générer des robots en C# ou Node.js. 

- Pour créer un bot en C#, utilisez le kit [SDK Bot Framework pour C#](../dotnet/bot-builder-dotnet-overview.md). 
- Pour créer un bot en utilisant Node.js, utilisez le kit [SDK Bot Framework pour Node.js](../nodejs/index.md). 

En plus de la modélisation du service Bot Connector, chaque kit SDK Bot Framework propose aussi un système puissant capable de générer des dialogues qui encapsulent une logique conversationnelle, des invites intégrées pour des choses aussi simples que Oui ou Non, des chaînes, des nombres, des énumérations, la prise en charge intégrée de puissants frameworks d’intelligence artificielle tels que <a href="https://www.luis.ai/" target="_blank"> LUIS </a>, et bien plus. 

> [!NOTE]
> Au lieu d’utiliser les kits SDK C# ou Node.js, vous pouvez générer votre propre bibliothèque cliente dans la langue de votre choix en utilisant le <a href="https://aka.ms/connector-swagger-file" target="_blank">fichier Swagger de Bot Connector</a>.

## <a name="additional-resources"></a>Ressources supplémentaires

Pour en savoir plus sur la création de bots à l’aide du service Bot Connector, lisez les articles de cette section, à commencer par [Authentification](bot-framework-rest-connector-authentication.md). Si vous rencontrez des problèmes ou avez des suggestions concernant le service Bot Connector, consultez la page [Support](../bot-service-resources-links-help.md) pour accéder à la liste des ressources disponibles. 
