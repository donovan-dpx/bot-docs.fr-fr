---
title: Événements générés par la télémétrie de Bot Framework Service | Microsoft Docs
description: Découvrez les événements déclenchés avec les nouvelles fonctionnalités de télémétrie.
keywords: télémétrie, appinsights, bot monitor
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: dbb58a6a287124d0b393a4fc5a617e89840d1542
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299158"
---
# <a name="events-generated-by-the-bot-framework-service-telemetry"></a>Événements générés par la télémétrie Bot Framework Service

## <a name="channel-service-events"></a>Événements de service de canal

En plus de `WaterfallDialog`, présentée dans la [rubrique Télémétrie](bot-builder-telemetry.md) et qui génère des événements à partir de votre code de robot, le service Bot Framework Channel enregistre également des événements.  Cela vous permet de diagnostiquer les problèmes liés aux canaux ou l’ensemble des défaillances du bot.

### <a name="customevent-activity"></a>CustomEvent : « Activity »
**Journalisé à partir de :** Service Channel   Journalisé par le service Channel quand un message est reçu.

### <a name="exception-bot-errors"></a>Exception : « Bot Errors »
**Journalisé à partir de :** Service Channel   Journalisé par le canal quand un appel au bot retourne une réponse HTTP non 2XX.

## <a name="customevent-waterfallstart"></a>CustomEvent : « WaterfallStart » 

Quand un WaterfallDialog commence, un événement `WaterfallStart` est journalisé.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

## <a name="customevent-waterfallstep"></a>CustomEvent : « WaterfallStep » 

Journalise les étapes individuelles d’un dialogue en cascade.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.StepName` (un nom de méthode ou `StepXofY` en cas d’expression lambda)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

## <a name="customevent-waterfalldialogcomplete"></a>CustomEvent : « WaterfallDialogComplete »

Journalise quand un dialogue en cascade se termine.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

## <a name="customevent-waterfalldialogcancel"></a>CustomEvent : « WaterfallDialogCancel » 

Journalise quand un dialogue en cascade est annulé.

- `user_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `session_id` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.activityType` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.channelId` ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- `customDimensions.DialogId` (Il s’agit de la valeur dialogId (string) passée dans votre cascade.  Vous pouvez la considérer comme le « type de cascade »)
- `customDimensions.StepName` (un nom de méthode ou `StepXofY` en cas d’expression lambda)
- `customDimensions.InstanceID` (unique pour chaque instance de dialogue)

## <a name="customevent-botmessagereceived"></a>CustomEvent : BotMessageReceived 
Journalisé quand le bot reçoit un nouveau message d’un utilisateur.

Lorsqu’il n’est pas remplacé, cet événement est journalisé à partir de `Microsoft.Bot.Builder.TelemetryLoggerMiddleware` à l’aide de la méthode `Microsoft.Bot.Builder.IBotTelemetry.TrackEvent()`.

- Identificateur de session  
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant qu’identificateur de **session** (*Temeletry.Context.Session.Id*) utilisé dans Application Insights.  
  - Correspond à l’[ID de conversation](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#conversation) tel que défini par le protocole Bot Framework.
  - Le nom de propriété journalisé est `session_id`.

- Identificateur d’utilisateur
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant qu’identificateur d’**utilisateur** (*Telemetry.Context.User.Id*) utilisé dans Application Insights.  
  - La valeur de cette propriété est une combinaison des propriétés [Identificateur de canal](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#channel-id) et [ID utilisateur](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (concaténées ensemble) telles que définies par le protocole Bot Framework.
  - Le nom de propriété journalisé est `user_id`.

- ActivityID 
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.
  - Correspond à l’[ID d’activité](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#Id) tel que défini par le protocole Bot Framework.
  - Le nom de la propriété est `activityId`.

- Identificateur de canal
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.  
  - Correspond au champ [Channel Identifier](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#id) (Identificateur de canal) du protocole Bot Framework.
  - Le nom de propriété journalisé est `channelId`.

- ActivityType 
  - Lorsque vous utilisez Application Insights, cela est journalisé à partir de `TelemetryBotIdInitializer` en tant que propriété de l’événement.  
  - Correspond au champ [Activity Type](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#type) (Type d’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `activityType`.

- Texte
  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [Activity Text](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#text) (Texte de l’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `text`.

- Speak

  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [Activity Speak](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#speak) (Discours d’activité) du protocole Bot Framework.
  - Le nom de propriété journalisé est `speak`.

  - 

- FromId
  - Correspond au champ [From Identifier](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant de l’identificateur) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromId`.

- FromName
  - **Éventuellement** journalisé lorsque la propriété `logPersonalInformation` a pour valeur `true`.
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- RecipientId
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- RecipientName
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- ConversationId
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- ConversationName
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

- Paramètres régionaux
  - Correspond au champ [From Name](https://github.com/Microsoft/BotBuilder/blob/master/specs/botframework-activity/botframework-activity.md#from) (Provenant du nom) du protocole Bot Framework.
  - Le nom de propriété journalisé est `fromName`.

## <a name="customevent-botmessagesend"></a>CustomEvent : BotMessageSend 
**Journalisé à partir de :** TelemetryLoggerMiddleware 

Journalisé quand le bot envoie un message.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ReplyToID
- RecipientId
- ConversationName
- Paramètres régionaux
- RecipientName (facultatif pour PII)
- Text (facultatif pour PII)
- Speak (facultatif pour PII)


## <a name="customevent-botmessageupdate"></a>CustomEvent : BotMessageUpdate
**Journalisé à partir de :** TelemetryLoggerMiddleware   Journalisé quand un message est mis à jour par le bot (cas rare)
- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName
- Paramètres régionaux
- Text (facultatif pour PII)


## <a name="customevent-botmessagedelete"></a>CustomEvent : BotMessageDelete
**Journalisé à partir de :** TelemetryLoggerMiddleware   Journalisé quand un message est supprimé par le bot (cas rare)
- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- RecipientId
- ConversationId
- ConversationName

## <a name="customevent-luisevent"></a>CustomEvent : LuisEvent
**Journalisé à partir de :** LuisRecognizer

Journalise les résultats du service LUIS.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ApplicationId
- Intention
- IntentScore
- Intent2 
- IntentScore2 
- FromId
- SentimentLabel
- SentimentScore
- Entities (en tant que json)
- Question (facultatif pour PII)

## <a name="customevent-qnamessage"></a>CustomEvent : QnAMessage
**Journalisé à partir de :** QnAMaker

Journalise les résultats du service QnA Maker.

- UserID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- SessionID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityID ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Channel ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- ActivityType ([provenant de l’initialiseur de télémétrie](https://aka.ms/telemetry-initializer))
- Username (facultatif pour PII)
- Question (facultatif pour PII)
- MatchedQuestion
- QuestionId
- Réponse
- Score
- ArticleFound

