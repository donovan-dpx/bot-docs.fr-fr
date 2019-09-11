---
title: Canaux supplémentaires | Microsoft Docs
description: Découvrez comment configurer des canaux supplémentaires pour votre bot.
keywords: bot channels, hangouts, Twilio, facebook, azure portal
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/08/2019
ms.openlocfilehash: 15b5a0a654996bd01a09e63c13631c39f716f133
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70297673"
---
# <a name="additional-channels"></a>Canaux supplémentaires

Outre les canaux répertoriés dans cette documentation, quelques canaux supplémentaires sont disponibles sous la forme d’un adaptateur, à la fois via nos [plateformes fournies](https://botkit.ai/docs/v4/platforms/) via Botkit ou via les [dépôts de la communauté](https://github.com/BotBuilderCommunity/). Voici les canaux supplémentaires fournis :

- [Webex Teams](https://botkit.ai/docs/v4/platforms/webex.html)
- [Websocket et Webhooks](https://botkit.ai/docs/v4/platforms/web.html)
- [Google Hangouts et Google Assistant](https://github.com/BotBuilderCommunity/) (disponibles via la communauté)
- [Amazon Alexa](https://github.com/BotBuilderCommunity/) (disponible via la communauté)

## <a name="currently-available-adapters"></a>Adaptateurs actuellement disponibles

Une liste complète d’adaptateurs disponibles est disponible [ici](https://botkit.ai/docs/v4/platforms/). Vous remarquerez que certains canaux sont disponibles également en tant qu’adaptateurs, et c’est à vous de décider quand utiliser un canal ou un adaptateur.

### <a name="when-to-use-an-adapter"></a>Quand utiliser un adaptateur

1. Le service ne prend pas en charge le canal voulu.
2. Les exigences de conformité et de sécurité de votre déploiement font que vous ne pouvez pas compter sur un service externe.
3. La profondeur des fonctionnalités dont vous avez besoin dans un canal particulier n’est peut-être pas prise en charge.

### <a name="when-to-use-a-channel"></a>Quand utiliser un canal

1. Vous avez besoin d’une compatibilité entre les différents canaux : votre bot doit fonctionner sur plusieurs des canaux disponibles.
2. Support intégré : Microsoft tient à jour, corrige et gère de façon cohérente chaque canal pour vous chaque fois qu’un tiers effectue des mises à jour.
3. Autorise l’accès à des canaux supplémentaires réservés à Microsoft, tels que Microsoft Teams qui se développe rapidement.
4. Si vous souhaitez vous appuyer sur une interface utilisateur graphique pour activer des canaux supplémentaires pour votre bot.
