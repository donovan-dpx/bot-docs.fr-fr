---
title: Fonctionnement des bots pour Microsoft Teams
description: Suite de l’article sur le fonctionnement des bots dans le cas précis de Microsoft Teams
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: overview
ms.service: bot-service
ms.date: 10/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3a7e60e1fa72402b5f783676f5281579a2be5371
ms.sourcegitcommit: 490810d278d1c8207330b132f28a5eaf2b37bd07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73592207"
---
# <a name="how-microsoft-teams-bots-work"></a>Fonctionnement des bots Microsoft teams

Ceci est une introduction qui s’appuie sur ce que vous avez appris dans l’article [Fonctionnement des bots](https://docs.microsoft.com/azure/bot-service/bot-builder-basics) et que vous devez avoir assimilé.

Les bots développés pour Microsoft Teams se distinguent principalement par la façon dont les activités sont gérées. Le gestionnaire d’activités Microsoft Teams dérive du gestionnaire d’activités Bot Framework pour le routage de toutes les activités des équipes, qui sont gérées avant les activités indépendantes des équipes.

## <a name="teams-activity-handlers"></a>Gestionnaires d’activités Teams

Comme pour n’importe quel autre bot, quand un bot conçu pour Microsoft Teams reçoit une activité, il la transmet à ses *gestionnaires d’activités*. En réalité, toutes les activités sont routées vers un seul et même gestionnaire de base appelé *gestionnaire de tours*. Le *gestionnaire de tours* appelle le gestionnaire d’activités nécessaire à la gestion du type d’activité qui a été reçue. Ce qui distingue un bot conçu pour Microsoft Teams est qu’il est dérivé d’une classe _Teams_ `Activity Handler`, qui dérive elle-même de la classe `Activity Handler` de Bot Framework.  La classe _Teams_ `Activity Handler` comprend divers gestionnaires d’activités propres à Microsoft Teams qui sont décrits dans cet article.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Comme pour n’importe quel bot créé à l’aide de Microsoft Bot Framework, si le bot reçoit une activité de message, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités `OnMessageActivityAsync`. Cette fonctionnalité est inchangée, mais si le bot reçoit une activité de mise à jour de conversation, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités`OnConversationUpdateActivityAsync` _Teams_ qui recherche en premier lieu les événements propres à Teams et transmet l’activité au gestionnaire d’activités de Bot Framework si aucun événement n’est trouvé.

Dans la classe de gestionnaire d’activités Teams, il existe deux gestionnaires d’activités principaux : `OnConversationUpdateActivityAsync`, qui route toutes les activités de mise à jour de conversation et `OnInvokeActivityAsync`, qui route toutes les activités d’appel de Teams.  Tous les gestionnaires d’activités décrits dans la section [Logique de bot](https://aka.ms/how-bots-work#bot-logic) de l’article `How bots work` continuent de fonctionner comme ils le feraient avec un bot non-Teams, sauf qu’ils ne gèrent pas les membres ajoutés et les activités supprimées dans le contexte d’une équipe de la même manière ; un nouveau membre est ajouté à l’équipe, et non à un thread de message.  Pour plus d’informations, consultez le tableau _Activités de mise à jour de conversation Teams_ dans la section [Logique de bot](#bot-logic).

Pour implémenter votre logique sur ces gestionnaires d’activités propres à Teams, vous devez substituer ces méthodes dans votre bot, comme indiqué dans la section [Logique de bot](#bot-logic) ci-après. Comme aucun de ces gestionnaires ne dispose d’une implémentation de base, ajoutez simplement la logique que vous voulez dans votre remplacement.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Comme pour n’importe quel bot créé à l’aide de Microsoft Bot Framework, si le bot reçoit une activité de message, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités `onMessage`. Cette fonctionnalité est inchangée, mais si le bot reçoit une activité de mise à jour de conversation, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités `dispatchConversationUpdateActivity` _Teams_ qui recherche en premier lieu les événements propres à Teams et transmet l’activité au gestionnaire d’activités de Bot Framework si aucun événement n’est trouvé.

Dans la classe de gestionnaire d’activités Teams, il existe deux gestionnaires d’activités principaux : `dispatchConversationUpdateActivity`, qui route toutes les activités de mise à jour de conversation et `onInvokeActivity`, qui route toutes les activités d’appel de Teams.  Tous les gestionnaires d’activités décrits dans la section [Logique de bot](https://aka.ms/how-bots-work#bot-logic) de l’article `How bots work` continuent de fonctionner comme ils le feraient avec un bot non-Teams, sauf qu’ils ne gèrent pas les membres ajoutés et les activités supprimées dans le contexte d’une équipe de la même manière ; un nouveau membre est ajouté à l’équipe, et non à un thread de message.  Pour plus d’informations, consultez le tableau _Activités de mise à jour de conversation Teams_ dans la section [Logique de bot](#bot-logic).

Comme pour n’importe quel gestionnaire de bot, pour implémenter votre logique sur les gestionnaires Teams, vous devez substituer les méthodes dans votre bot, comme indiqué dans la section [Logique de bot](#bot-logic) ci-dessous. Pour chacun de ces gestionnaires, définissez votre logique de bot, puis **veillez à appeler `next()` à la fin**. En appelant `next()` vous êtes certain que le gestionnaire suivant est exécuté.

---

### <a name="bot-logic"></a>Logique du bot

La logique de bot traite les activités entrantes d’un ou plusieurs canaux de vos bots, puis génère des activités sortantes en réponse.  C’est toujours le cas pour un bot dérivé de la classe de gestionnaire d’activités Teams, qui recherche en premier lieu les activités Teams et transmet toutes les autres activités au gestionnaire d’activités de Bot Framework.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="teams-conversation-update-activities"></a>Activités de mise à jour de conversation Teams

La liste ci-dessous présente tous les gestionnaires d’activités Teams appelés à partir du gestionnaire d’activités _Teams_ `OnConversationUpdateActivityAsync`. L’article [Événements de mise à jour de conversation](https://aka.ms/azure-bot-subscribe-to-conversation-events) explique comment utiliser chacun de ces événements dans un bot.

| Événement | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedAsync` | Remplacez cet élément pour gérer un canal Teams en cours de création. Pour plus d’informations, consultez [Canal créé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `OnTeamsChannelDeletedAsync` | Remplacez cet élément pour gérer un canal Teams en cours de suppression. Pour plus d’informations, consultez [Canal supprimé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted). |
| channelRenamed | `OnTeamsChannelRenamedAsync` | Remplacez cet élément pour gérer un canal Teams en cours de renommage. Pour plus d’informations, consultez [Canal renommé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `OnTeamsTeamRenamedAsync` | `return Task.CompletedTask;` Remplacez cet élément pour gérer une équipe Teams en cours de renommage. Pour plus d’informations, consultez [Équipe renommée](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `OnTeamsMembersAddedAsync` | Appelle la méthode `OnMembersAddedAsync` dans `ActivityHandler`. Remplacez cet élément pour gérer les membres rejoignant une équipe. Pour plus d’informations, consultez [Membre d’équipe ajouté](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added).|
| MembersRemoved | `OnTeamsMembersRemovedAsync` | Appelle la méthode `OnMembersRemovedAsync` dans `ActivityHandler`. Remplacez cet élément pour gérer les membres quittant une équipe. Pour plus d’informations, consultez [Membre d’équipe supprimé](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed).|


<!--
| Event | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedAsync` | Override this to handle a Teams channel being created. |
| channelDeleted | `OnTeamsChannelDeletedAsync` | Override this to handle a Teams channel being deleted. |
| channelRenamed | `OnTeamsChannelRenamedAsync` | Override this to handle a Teams channel being renamed. |
| teamRenamed | `OnTeamsTeamRenamedAsync` | `return Task.CompletedTask;` Override this to handle a Teams Team being Renamed. |
| MembersAdded | `OnTeamsMembersAddedAsync` | Calls the `OnMembersAddedAsync` method in `ActivityHandler`. Override this to handle members joining a team. |
| MembersRemoved | `OnTeamsMembersRemovedAsync` | Calls the `OnMembersRemovedAsync` method in `ActivityHandler`. Override this to handle members leaving a team. |
-->

#### <a name="teams-invoke--activities"></a>Activités d’appel Teams

Voici la liste de tous les gestionnaires d’activités Teams appelés à partir du gestionnaire d’activités _Teams_ `OnInvokeActivityAsync` :

| Types d’appel                    | Handler                              | Description                                                  |
| :-----------------------------  | :----------------------------------- | :----------------------------------------------------------- |
| CardAction.Invoke               | `OnTeamsCardActionInvokeAsync`       | Appel d’action de carte Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentAcceptAsync`      | Acceptation du consentement de fichier Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentAsync`            | Consentement de fichier Teams. |
| fileConsent/invoke              | `OnTeamsFileConsentDeclineAsync`     | Consentement de fichier Teams. |
| actionableMessage/executeAction | `OnTeamsO365ConnectorCardActionAsync` | Action de carte de connecteur Teams Office 365. |
| signin/verifyState              | `OnTeamsSigninVerifyStateAsync`      | État de vérification de connexion Teams. |
| task/fetch                      | `OnTeamsTaskModuleFetchAsync`        | Récupération (fetch) de module de tâche Teams. |
| task/submit                     | `OnTeamsTaskModuleSubmitAsync`       | Envoi de module de tâche Teams. |

Les activités d’appel listées ci-dessus sont destinées aux bots conversationnels dans Teams. Le kit SDK Bot Framework prend aussi en charge les appels propres aux extensions de messagerie. Pour plus d’informations, consultez [Extensions de messagerie](https://aka.ms/azure-bot-what-are-messaging-extensions).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="teams-conversation-update-activities"></a>Activités de mise à jour de conversation Teams

La liste ci-dessous présente tous les gestionnaires d’activités Teams appelés à partir du gestionnaire d’activités _Teams_ `dispatchConversationUpdateActivity`. L’article [Événements de mise à jour de conversation](https://aka.ms/azure-bot-subscribe-to-conversation-events) explique comment utiliser chacun de ces événements dans un bot.

| Événement | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedEvent` | Remplacez cet élément pour gérer un canal Teams en cours de création. Pour plus d’informations, consultez [Canal créé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-created). |
| channelDeleted | `OnTeamsChannelDeletedEvent` | Remplacez cet élément pour gérer un canal Teams en cours de suppression. Pour plus d’informations, consultez [Canal supprimé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-deleted).|
| channelRenamed | `OnTeamsChannelRenamedEvent` | Remplacez cet élément pour gérer un canal Teams en cours de renommage. Pour plus d’informations, consultez [Canal renommé](https://aka.ms/azure-bot-subscribe-to-conversation-events#channel-renamed). |
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Remplacez cet élément pour gérer une équipe Teams en cours de renommage. Pour plus d’informations, consultez [Équipe renommée](https://aka.ms/azure-bot-subscribe-to-conversation-events#team-renamed). |
| MembersAdded | `OnTeamsMembersAddedEvent` | Appelle la méthode `OnMembersAddedEvent` dans `ActivityHandler`. Remplacez cet élément pour gérer les membres rejoignant une équipe. Pour plus d’informations, consultez [Membre d’équipe ajouté](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Added). |
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Appelle la méthode `OnMembersRemovedEvent` dans `ActivityHandler`. Remplacez cet élément pour gérer les membres quittant une équipe. Pour plus d’informations, consultez [Membre d’équipe supprimé](https://aka.ms/azure-bot-subscribe-to-conversation-events#Team-Member-Removed). |

<!--
| Event | Handler | Description |
| :-- | :-- | :-- |
| channelCreated | `OnTeamsChannelCreatedEvent` | Override this to handle a Teams channel being created. |
| channelDeleted | `OnTeamsChannelDeletedEvent` | Override this to handle a Teams channel being deleted. |
| channelRenamed | `OnTeamsChannelRenamedEvent` | Override this to handle a Teams channel being renamed. |
| teamRenamed | `OnTeamsTeamRenamedEvent` | `return Task.CompletedTask;` Override this to handle a Teams Team being Renamed. |
| MembersAdded | `OnTeamsMembersAddedEvent` | Calls the `OnMembersAddedEvent` method in `ActivityHandler`. Override this to handle members joining a team. |
| MembersRemoved | `OnTeamsMembersRemovedEvent` | Calls the `OnMembersRemovedEvent` method in `ActivityHandler`. Override this to handle members leaving a team. |
-->

#### <a name="teams-invoke--activities"></a>Activités d’appel Teams

Voici la liste de tous les gestionnaires d’activités Teams appelés à partir du gestionnaire d’activités _Teams_ `onInvokeActivity` :

| Types d’appel                    | Handler                              | Description                                                  |
| :-----------------------------  | :----------------------------------- | :----------------------------------------------------------- |
| CardAction.Invoke               | `handleTeamsCardActionInvoke`       | Appel d’action de carte Teams. |
| fileConsent/invoke              | `handleTeamsFileConsentAccept`      | Acceptation du consentement de fichier Teams. |
| fileConsent/invoke              | `handleTeamsFileConsent`            | Consentement de fichier Teams. |
| fileConsent/invoke              | `handleTeamsFileConsentDecline`     | Consentement de fichier Teams. |
| actionableMessage/executeAction | `handleTeamsO365ConnectorCardAction` | Action de carte de connecteur Teams Office 365. |
| signin/verifyState              | `handleTeamsSigninVerifyState`      | État de vérification de connexion Teams. |
| task/fetch                      | `handleTeamsTaskModuleFetch`        | Récupération (fetch) de module de tâche Teams. |
| task/submit                     | `handleTeamsTaskModuleSubmit`       | Envoi de module de tâche Teams. |

Les activités d’appel listées ci-dessus sont destinées aux bots conversationnels dans Teams. Le kit SDK Bot Framework prend aussi en charge les appels propres aux extensions de messagerie. Pour plus d’informations, consultez [Extensions de messagerie](https://aka.ms/azure-bot-what-are-messaging-extensions).

---

## <a name="next-steps"></a>Étapes suivantes
Pour créer des bots Teams, reportez-vous à la [documentation](https://aka.ms/teams-docs) Microsoft Teams Developer.