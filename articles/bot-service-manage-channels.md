---
title: Configurer un bot pour qu’il s’exécute sur un ou plusieurs canaux | Microsoft Docs
description: Découvrez comment configurer un bot pour l’exécuter sur un ou plusieurs canaux à l’aide du portail Bot Framework.
keywords: canaux de bots, configurer, cortana, facebook messenger, kik, slack, Skype, portail azure
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/22/2018
ms.openlocfilehash: cb951e9fd2bad6e68552a025ff3188fecd1783b2
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297602"
---
# <a name="connect-a-bot-to-channels"></a>Connecter un bot à des canaux

Un canal est une connexion entre le bot et les applications de communication. Vous configurez un bot pour le connecter aux canaux sur lesquels vous souhaitez le rendre disponible. Bot Framework Service, configuré via le portail Azure, connecte votre bot à ces canaux et facilite la communication entre votre bot et l’utilisateur. Vous pouvez vous connecter à de nombreux services populaires tels que [Cortana](bot-service-channel-connect-cortana.md), [Facebook Messenger](bot-service-channel-connect-facebook.md), [Kik](bot-service-channel-connect-kik.md) et [Slack](bot-service-channel-connect-slack.md), entre autres. Le canal [Web Chat](bot-service-channel-connect-webchat.md) est préconfiguré. En plus des canaux standard fournis avec le service Bot Connector, vous pouvez utiliser [Direct Line](bot-service-channel-connect-directline.md) comme canal pour connecter votre bot à votre propre application cliente.

Bot Framework Service vous permet de développer votre bot indépendamment du canal en normalisant les messages envoyés par le bot à un canal. Cela suppose de le convertir du schéma Bot Framework vers le schéma du canal. Cependant, si le canal ne prend pas en charge tous les aspects du schéma Bot Framework, le service tente de convertir le message dans un format que le canal prend en charge. Par exemple, si le bot envoie au canal e-mail un message qui contient une carte avec des boutons d’action, le connecteur peut envoyer la carte comme image et inclure les actions sous forme de liens dans le texte du message.

Pour la plupart des canaux, vous devez fournir les informations de configuration de canal afin d’exécuter votre bot sur le canal. La plupart des canaux nécessitent que votre bot dispose d’un compte sur le canal, tandis que d’autres, par exemple Facebook Messenger, nécessitent que votre bot utilise également une application inscrite avec le canal.

Pour configurer votre bot afin de le connecter à un canal, procédez comme suit :

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">Portail Azure</a>.
2. Sélectionnez le bot à configurer.
3. Dans le panneau Bot Service, cliquez sur **Canaux** sous **Gestion du bot**.
4. Cliquez sur l’icône du canal que vous souhaitez ajouter à votre bot.

![Se connecter aux canaux](./media/channels/connect-to-channels.png)

Une fois que vous avez configuré le canal, les utilisateurs sur ce canal peuvent commencer à utiliser votre bot.

## <a name="publish-a-bot"></a>Publier un bot

Le processus de publication est différent pour chaque canal.

[!INCLUDE [publishing](./includes/snippet-publish-to-channel.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

Le SDK inclut des exemples que vous pouvez utiliser pour créer des bots. Pour voir la liste des exemples, consultez le [dépôt d’exemples sur GitHub](https://github.com/Microsoft/BotBuilder-samples).
