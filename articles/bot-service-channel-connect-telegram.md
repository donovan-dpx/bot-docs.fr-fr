---
title: Créer un bot pour Telegram - Bot Service
description: Découvrez comment configurer la connexion d’un bot à Telegram.
keywords: configurer un bot, Telegram, canal de bot, bot de Telegram, jeton d’accès
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: fd60fbb10e9560e3df6e427f0d5193df463ec473
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791817"
---
# <a name="connect-a-bot-to-telegram"></a>Connecter un bot à Telegram

Vous pouvez configurer votre bot pour communiquer avec des personnes utilisant l’application de messagerie Telegram.

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="visit-the-bot-father-to-create-a-new-telegram-bot"></a>Visitez Bot Father pour créer un nouveau bot Telegram

<a href="https://telegram.me/botfather" target="_blank">Créer un bot Telegram</a> avec Bot Father.

![Visitez Bot Father](~/media/channels/tg-StepVisitBotFather.png)

## <a name="create-a-new-telegram-bot"></a>Créez un bot Telegram
Pour créer un bot Telegram, envoyez la commande `/newbot`.

![Créez un bot](~/media/channels/tg-StepNewBot.png)

### <a name="specify-a-friendly-name"></a>Donnez-lui un nom convivial

Donnez un nom convivial au bot Telegram.

![Donnez un nom convivial au bot](~/media/channels/tg-StepNameBot.png)

### <a name="specify-a-username"></a>Spécifiez un nom d’utilisateur

Donnez un nom d’utilisateur unique au bot Telegram.

![Donnez un nom unique au bot](~/media/channels/tg-StepUsername.png)

### <a name="copy-the-access-token"></a>Copiez le jeton d’accès

Copiez le jeton d’accès au bot Telegram.

![Copiez le jeton d'accès](~/media/channels/tg-StepBotCreated.png)

## <a name="enter-the-telegram-bots-access-token"></a>Entrez le jeton d'accès au bot Telegram

Accédez à la section **Canaux** de votre bot dans le portail Azure, puis cliquez sur le bouton **Telegram**. 

> [!NOTE]
>  L’interface utilisateur du portail Azure est légèrement différente si vous avez déjà connecté votre bot à Telegram. 

![Sélectionner Telegram dans les canaux](~/media/channels/tg-connectBot-Azure.png)

Collez le jeton que vous avez copié précédemment dans le champ **Jeton d’accès** et cliquez sur **Enregistrer**.

![Jeton d’accès Telegram](~/media/channels/tg-accessToken-Azure.png)

Votre bot est à présent correctement configuré pour communiquer avec des utilisateurs dans Telegram. 

![Bot Telegram activé](~/media/channels/tg-botEnabled-Azure.png)
