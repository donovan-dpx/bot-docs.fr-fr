---
title: Connecter un bot à Direct Line Speech
titleSuffix: Bot Service
description: Vue d’ensemble et étapes nécessaires pour connecter un bot Bot Framework existant au canal Direct Line Speech pour des interactions vocales entrantes et sortantes avec une grande fiabilité et une faible latence.
services: bot-service
author: trrwilson
manager: nitinme
ms.service: bot-service
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: travisw
ms.openlocfilehash: 55bb6b63f35b2cb064229ed0a827af422ca83882
ms.sourcegitcommit: 490810d278d1c8207330b132f28a5eaf2b37bd07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73592231"
---
# <a name="connect-a-bot-to-direct-line-speech"></a>Connecter un bot à Direct Line Speech

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Vous pouvez configurer votre bot pour permettre aux applications clientes de communiquer avec lui via le canal Direct Line Speech.

Une fois que vous avez créé votre bot, son intégration avec Direct Line Speech permet de bénéficier d’une connexion de grande fiabilité et de faible latence avec les applications clientes à l’aide du [kit SDK Speech](https://aka.ms/speech/sdk). Ces connexions sont optimisées pour des expériences de conversation vocales entrantes et sortantes. Pour plus d’informations sur Direct Line Speech et la manière de générer des applications clientes, visitez la page relative à l’[assistant virtuel vocal personnalisé](https://aka.ms/bots/speech/va). 

## <a name="add-the-direct-line-speech-channel"></a>Ajouter le canal Direct Line Speech

1. Pour ajouter le canal Direct Line Speech, commencez par ouvrir le bot sur le [portail Azure](https://portal.azure.com) puis, dans vos ressources, sélectionnez la ressource **Bot Channel Registration** (Inscription aux canaux de bots). Cliquez sur **Canaux** dans le panneau de configuration.

    ![mise en évidence de l’emplacement de sélection des canaux auxquels se connecter ](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-selectchannel.png "sélection de canaux")

1. Dans la page de sélection des canaux, recherchez et cliquez sur `Direct Line Speech` pour choisir le canal.

    ![sélection du canal Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-connectspeechchannel.png "connexion de Direct Line Speech")

1. Le canal Direct Line Speech nécessite une ressource Cognitive Services. Vous pouvez soit utiliser une ressource existante, soit créer une ressource Cognitive Services en suivant [ces instructions](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). 

    ![sélection du canal Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-cognitivesericesaccount-selection.png "sélection de la ressource Cognitive Services")

1. Une fois que vous avez passé en revue les conditions d’utilisation, cliquez sur `Save` pour confirmer votre sélection de canal.

    ![enregistrement de l’activation du canal Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-savechannel.png "Enregistrer la configuration du canal")

## <a name="enable-the-bot-framework-protocol-streaming-extensions"></a>Activer les extensions de streaming du protocole Bot Framework

Le canal Direct Line Speech étant connecté à votre bot, vous devez maintenant activer la prise en charge des extensions de streaming de protocole Bot Framework pour une interaction optimale et à faible latence.

1. Si vous ne l’avez pas encore fait, ouvrez le panneau de votre bot dans le [portail Azure](https://portal.azure.com). 

1. Cliquez sur **Paramètres** sous la catégorie **Gestion du bot** (juste en dessous de **Canaux**). Cochez la case **Activer un point de terminaison de streaming**.

    ![activer le protocole de streaming](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablestreamingsupport.png "activer la prise en charge de l’extension de streaming")

1. En haut de la page, cliquez sur **Enregistrer**.

1. Dans le même panneau, sous la catégorie **Paramètres d’App Service**, cliquez sur **Configuration**.

    ![accéder aux paramètres App Service](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-configureappservice.png "configurer App Service")

1. Cliquez sur `General settings`, puis sélectionnez l’option d’activation de la prise en charge `Web socket`.

    ![activer les Websockets pour App Service](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablewebsockets.png "activer les Websockets")

1. Cliquez sur `Save` en haut de la page de configuration.

1. Les extensions de streaming du protocole Bot Framework sont maintenant activées pour votre bot. Vous êtes maintenant prêt à mettre à jour votre code de bot et à [intégrer la prise en charge des extensions de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) à un projet de bot existant.

## <a name="adding-protocol-support-to-your-bot"></a>Ajout de la prise en charge du protocole à votre bot

Le canal Direct Line Speech étant connecté et la prise en charge pour les extensions de streaming du protocole Bot Framework étant activée, il ne reste plus qu’à ajouter du code à votre bot pour prendre en charge la communication optimisée. Suivez les instructions sur l’[ajout de la prise en charge des extensions de streaming à votre bot](https://aka.ms/botframework/addstreamingprotocolsupport) pour garantir une compatibilité complète avec Direct Line Speech.


