---
title: API REST Bot Framework | Microsoft Docs
description: Commencez avec les API REST Bot Framework permettant des créer des robots et des clients qui se connectent à des robots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 9f9acf2dc2b1aa0981196bf316a3f1c351467d32
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299528"
---
# <a name="bot-framework-rest-apis"></a>API REST Bot Framework
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Les API REST Bot Framework vous permettent de créer des robots qui échangent des messages avec des canaux configurés dans le <a href="https://dev.botframework.com/" target="_blank">Portail Bot Framework</a>, stockent et récupèrent des données d’état, et connectent vos propres applications clientes à vos robots. Tous les services Bot Framework utilisent les ressources REST et JSON standard sur HTTPS.

## <a name="build-a-bot"></a>Créez un robot

Vous pouvez créer un robot avec n’importe quel langage de programmation en utilisant le service Bot Connector pour échanger des messages avec des canaux configurés dans le portail Bot Framework. 

> [!TIP]
> Le Bot Framework fournit des bibliothèques clientes utilisables pour générer des robots en C# ou Node.js. Pour créer un bot en C#, utilisez le kit [SDK Bot Framework pour C#](../dotnet/bot-builder-dotnet-overview.md). Pour créer un bot en utilisant Node.js, utilisez le kit [SDK Bot Framework pour Node.js](../nodejs/index.md). 

Pour en savoir plus sur la création de robots à l’aide du service Bot Connector, voir [Concepts clés](bot-framework-rest-connector-concepts.md).

## <a name="build-a-client"></a>Créer un client

Vous pouvez autoriser votre propre application cliente à communiquer avec votre robot à l’aide de l’API Direct Line. L’API Direct Line implémente un mécanisme d’authentification qui utilise des modèles de secret/jeton standard et fournit un schéma stable, même si votre robot change sa version de protocole. Pour en savoir plus sur l’utilisation de l’API Direct Line pour permettre la communication entre un client et votre robot, voir [Concepts clés](bot-framework-rest-direct-line-3-0-concepts.md). 