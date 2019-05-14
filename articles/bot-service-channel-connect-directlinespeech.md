---
title: Connecter un bot à Direct Line Speech (préversion)
titleSuffix: Bot Service
description: Vue d’ensemble et étapes nécessaires pour connecter un bot Bot Framework existant au canal Direct Line Speech pour des interactions vocales entrantes et sortantes avec une grande fiabilité et une faible latence.
services: bot-service
author: trrwilson
manager: nitinme
ms.service: bot-service
ms.subservice: bot-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: 8e0d2939078e1e27162c7056373e95790a03eb88
ms.sourcegitcommit: 5042e31bc6b2762d7a6636e98c8f496b90ea33c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65240433"
---
# <a name="connect-a-bot-to-direct-line-speech-preview"></a>Connecter un bot à Direct Line Speech (préversion)

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Vous pouvez configurer votre bot pour permettre aux applications clientes de communiquer avec lui via le canal Direct Line Speech.

Une fois que vous avez créé votre bot, son intégration avec Direct Line Speech permet de bénéficier d’une connexion de grande fiabilité et de faible latence avec les applications clientes à l’aide du [kit SDK Speech](https://aka.ms/speech/sdk). Ces connexions sont optimisées pour des expériences de conversation vocales entrantes et sortantes. Pour plus d’informations sur Direct Line Speech et la manière de générer des applications clientes, visitez la page relative à l’[assistant virtuel vocal personnalisé](https://aka.ms/bots/speech/va).  

## <a name="sign-up-for-direct-line-speech-preview"></a>S'inscrire à la préversion de Direct Line Speech

Direct Line Speech est actuellement en préversion et demande une inscription rapide au [portail Azure](https://portal.azure.com). Consultez les détails ci-dessous. Une fois approuvé, vous obtiendrez l’accès au canal.

## <a name="add-the-direct-line-speech-channel"></a>Ajouter le canal Direct Line Speech

1. Pour ajouter le canal Direct Line Speech, commencez par ouvrir le bot dans le [portail Azure](https://portal.azure.com), puis cliquez sur **Canaux** dans le panneau de configuration.

    ![mise en surbrillance de l’emplacement pour la sélection des canaux auxquels se connecter ](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-selectchannel.png "sélection des canaux")

1. Dans la page de sélection des canaux, recherchez et cliquez sur `Direct Line Speech` pour choisir le canal.

    ![sélection du canal direct line speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-connectspeechchannel.png "connexion de Direct Line Speech")

1. Si vous n’avez pas encore reçu d’approbation d’accès, vous verrez une page de demande d’accès. Renseignez les informations demandées et cliquez sur « Demander ». Une page de confirmation s’affiche. Pendant que votre demande est en attente d’approbation, vous n’êtes pas en mesure d’aller au-delà de cette page.   

1. Une fois l’approbation d’accès reçue, une page de configuration pour Direct Line Speech s’affiche. Une fois que vous avez passé en revue les conditions d’utilisation, cliquez sur `Save` pour confirmer votre sélection de canal.

    ![enregistrement de l’activation du canal Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-savechannel.png "Enregistrer la configuration du canal")

## <a name="enable-the-bot-framework-protocol-streaming-extensions"></a>Activer les extensions de streaming du protocole Bot Framework

Le canal Direct Line Speech étant connecté à votre bot, vous devez maintenant activer la prise en charge des extensions de streaming de protocole Bot Framework pour une interaction optimale et à faible latence.

1. Si vous ne l’avez pas encore fait, ouvrez le panneau de votre bot dans le [portail Azure](https://portal.azure.com). 

1. Cliquez sur **Paramètres** sous la catégorie **Gestion du bot** (juste en dessous de **Canaux**). Cochez la case **Activer un point de terminaison de streaming**.

    ![activer le protocole de streaming](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablestreamingsupport.png "activer la prise en charge de l’extension de streaming")

1. En haut de la page, cliquez sur **Enregistrer**.

1. Dans le même panneau, sous la catégorie **Paramètres d’App Service**, cliquez sur **Configuration**.

    ![accéder aux paramètres d’App Service](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-configureappservice.png "configurer App Service")

1. Cliquez sur `General settings`, puis sélectionnez l’option d’activation de la prise en charge `Web socket`.

    ![activer les websockets pour App Service](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-enablewebsockets.png "activer les websockets")

1. Cliquez sur `Save` en haut de la page de configuration.

1. Les extensions de streaming du protocole Bot Framework sont maintenant activées pour votre bot. Vous êtes maintenant prêt à mettre à jour votre code de bot et à [intégrer la prise en charge des extensions de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) à un projet de bot existant.

## <a name="manage-secret-keys"></a>Gérer les clés secrètes

Les applications clientes ont besoin d’un secret de canal pour se connecter à votre bot via le canal Direct Line Speech. Une fois que vous avez enregistré votre sélection de canal, vous pouvez récupérer ces clés secrètes à partir de la page **Configurer Direct Line Speech** dans le portail Azure.

![obtention des clés secrètes pour Direct Line Speech](media/voice-first-virtual-assistants/bot-service-channel-directlinespeech-getspeechsecretkeys.png "obtention des clés secrètes pour Direct Line Speech")

## <a name="adding-protocol-support-to-your-bot"></a>Ajout de la prise en charge du protocole à votre bot

Le canal Direct Line Speech étant connecté et la prise en charge pour les extensions de streaming du protocole Bot Framework étant activée, il ne reste plus qu’à ajouter du code à votre bot pour prendre en charge la communication optimisée. Suivez les instructions sur l’[ajout de la prise en charge des extensions de streaming à votre bot](https://aka.ms/botframework/addstreamingprotocolsupport) pour garantir une compatibilité complète avec Direct Line Speech.

## <a name="known-issues"></a>Problèmes connus

Notez que le service est en préversion et susceptible d’être modifié, ce qui peut affecter le développement de votre bot et les performances globales. Voici la liste des problèmes connus : 

1. Le service est actuellement déployé dans la [région Azure](https://azure.microsoft.com/en-us/global-infrastructure/regions/) USA Ouest 2. Nous effectuerons bientôt le déploiement dans d’autres régions, de sorte que tous les clients bénéficieront des interactions vocales à faible latence avec leurs bots.

1. Des modifications mineures pour contrôler les champs, telles que [serviceUrl](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#service-url), seront disponibles

1. Les activités [conversationUpdate](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation-update-activity) et [endOfCoversation](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#end-of-conversation-activity) utilisées pour signaler le début et la fin des conversations, couramment utilisées pour générer des messages de bienvenue, seront actualisées par souci de cohérence avec d’autres canaux

1. [SigninCard](https://docs.microsoft.com/en-us/azure/bot-service/rest-api/bot-framework-rest-connector-add-rich-cards?view=azure-bot-service-4.0) n’est pas encore pris en charge par le canal 
