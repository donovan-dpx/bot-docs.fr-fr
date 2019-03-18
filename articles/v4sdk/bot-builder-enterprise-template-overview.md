---
title: Présentation détaillée du modèle de bot d’entreprise | Microsoft Docs
description: Obtenez des informations sur la conception du modèle de bot d’entreprise
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 582ec21d36e15fcbaef2d26616a9e55a04d8e5f2
ms.sourcegitcommit: b2245df2f0a18c5a66a836ab24a573fd70c7d272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568216"
---
# <a name="enterprise-bot-template---overview"></a>Vue d’ensemble d’Enterprise Bot Template

> [!NOTE]
> Cet article s’applique à la version v4 du Kit de développement logiciel (SDK). 

Enterprise Bot Template offre une base solide des bonnes pratiques et des services nécessaires pour créer une expérience de conversation, avec à la clé une charge de travail moindre et une meilleure qualité. Le modèle tire parti du kit [Bot Builder SDK v4](https://github.com/Microsoft/botbuilder) et des outils [Bot Builder Tools](https://github.com/Microsoft/botbuilder-tools) pour offrir les fonctionnalités suivantes :

Fonctionnalité      | Description |
------------ | -------------
Introduction | Message de présentation avec une [carte adaptative]() au début de la conversation
Indicateurs de saisie  | Indicateurs de saisie visuels automatisés durant les conversations et répétition pour les opérations de longue durée
Modèle LUIS de base  | Prise en charge des intentions courantes comme **Annuler**, **Aide**, **Escalader**, etc.
Dialogues de base | Flux de dialogues pour capturer les informations utilisateur de base ainsi que la logique d’interruption pour les intentions Annuler et Aide
Réponses de base  | Réponses par reconnaissance et synthèse vocales pour les intentions et dialogues de base
Forum Aux Questions | Intégration à [QnA Maker](https://www.qnamaker.ai) pour répondre aux questions générales à partir d’une base de connaissances 
Échange de conversation (chit-chat) | Modèle d’échange de conversation professionnel pour fournir des réponses standard aux demandes courantes ([en savoir plus](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base))
Répartiteur | Modèle [Dispatch](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) intégré pour déterminer si un énoncé donné doit être traité par LUIS ou QnA Maker
Support multilingue | Disponible en anglais, français, italien, allemand, espagnol et chinois
Transcriptions | Transcriptions de toutes les conversations enregistrées dans Stockage Azure
Télémétrie  | Intégration à [Application Insights](https://azure.microsoft.com/en-gb/services/application-insights/) pour collecter des données de télémétrie sur toutes les conversations
Analytics | Exemple de tableau de bord Power BI pour vous aider à démarrer avec les insights dans vos expériences de conversation
Déploiement automatisé | Déploiement facile de tous les services mentionnés ci-dessus à l’aide des [outils Bot Builder](https://github.com/Microsoft/botbuilder-tools)

# <a name="background"></a>Arrière-plan

## <a name="introduction-card"></a>Carte de présentation
Une carte de présentation améliore les conversations en fournissant une vue d’ensemble des fonctionnalités du bot ainsi que des exemples d’énoncés pour démarrer plus facilement. Elle établit également la personnalité du bot pour l’utilisateur.

## <a name="base-luis-model-with-common-intents"></a>Modèle LUIS de base avec les intentions courantes
Le modèle LUIS de base inclus dans le modèle présente une sélection des intentions les plus courantes que la plupart des bots ont à traiter. Votre bot préconfiguré contient les intentions suivantes :

Intention       | Exemples d’énoncés |
-------------|-------------|
Annuler       |*cancel*, *nevermind*|
Escalader     |*puis-je parler à quelqu’un ?*|
FinishTask   |*terminé*, *tout fait*|
GoBack       |*retour*|
Aide         |*pouvez-vous m’aider ?*|
Répéter       |*pouvez-vous répéter ?*|
SelectAny    |*n’importe lequel*|
SelectItem   |*le premier*|
SelectNone   |*aucun*|
ShowNext     |*afficher plus*|
ShowPrevious |*afficher le précédent*|
StartOver    |*restart*|
Arrêter         |*stop*|

## <a name="qna-maker"></a>QnA Maker

[QnA Maker](https://www.qnamaker.ai/) offre la possibilité aux non-développeurs de générer un ensemble de questions-réponses à l’usage d’un bot. Ces connaissances peuvent être importées à partir de sources de données FAQ et de guides produit, ou créées manuellement dans le portail QnA Maker.

Le modèle contient deux exemples de modèles QnA Maker : un pour les questions-réponses et un pour les [échanges de conversation (chit-chat) professionnels](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base). 

## <a name="dispatch"></a>Dispatch

Le service Dispatch est utilisé pour gérer le routage entre plusieurs modèles LUIS et bases de connaissances QnA Maker, en extrayant les énoncés de chaque service et en créant un modèle LUIS de dispatch centralisé.

Ainsi, un bot peut rapidement identifier le composant qui doit traiter un énoncé donné et garantit que les données QnA Maker sont placées au plus haut niveau du traitement des intentions et pas en bas de la hiérarchie.

Cet outil Dispatch permet également d’évaluer vos modèles pour mettre en évidence les éventuels chevauchements d’énoncés et écarts entre les services.

## <a name="telemetry"></a>Télémétrie

Avoir des insights sur les conversations d’un bot est utile pour mieux comprendre quels sont les niveaux d’engagement utilisateur, les fonctionnalités appréciées des utilisateurs et les questions auxquelles le bot ne parvient pas à répondre.

[Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) capture des données de télémétrie opérationnelles sur votre service de bot ainsi que les événements liés aux conversations. Ces événements peuvent ensuite être agrégés en informations actionnables à l’aide d’outils tels que [Power BI](https://powerbi.microsoft.com/en-us/what-is-power-bi/). Pour illustrer cette fonctionnalité, il y a un exemple de tableau de bord Power BI spécifique que vous pouvez utiliser avec Enterprise Bot Template.

# <a name="next-steps"></a>Étapes suivantes
Consultez la rubrique [Bien démarrer](bot-builder-enterprise-template-getting-started.md) pour savoir comment créer et déployer votre Enterprise Bot. 

# <a name="resources"></a>Ressources
Le code source complet pour Enterprise Bot Template est disponible sur [GitHub](https://github.com/Microsoft/AI/tree/master/templates/Enterprise-Template).
