---
title: Informations de référence sur l’API | Microsoft Docs
description: Découvrez les en-têtes, les opérations, les objets et les erreurs associés aux services Bot Connector et Bot State.
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/02/2019
ms.openlocfilehash: 5a520c2fc5b9e94976e9a2618286aa184045bdeb
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68866641"
---
# <a name="api-reference"></a>Informations de référence sur l'API

> [!NOTE]
> L’API REST n’est pas équivalente au SDK. L’API REST permet une communication standard REST. Toutefois, la méthode d’interaction recommandée avec Bot Framework reste le SDK. 

Au sein de Bot Framework, le service Bot Connector permet à votre bot d’échanger des messages avec des utilisateurs sur des canaux configurés dans le portail Bot Framework. Le service utilise les ressources REST et JSON standard sur HTTPS.

## <a name="base-uri"></a>URI de base

Lorsqu’un utilisateur envoie un message à votre bot, la requête entrante contient un objet [Activity](#bot-framework-activity-schema) avec une propriété `serviceUrl` qui spécifie le point de terminaison auquel votre bot doit envoyer sa réponse. Pour accéder au service Bot Connector, utilisez la valeur `serviceUrl` comme URI de base pour les requêtes d’API. 

Supposons, par exemple, que votre bot reçoive l’activité suivante lorsque l’utilisateur lui envoie un message.

```json
{
    "type": "message",
    "id": "bf3cc9a2f5de...",
    "timestamp": "2016-10-19T20:17:52.2891902Z",
    "serviceUrl": "https://smba.trafficmanager.net/apis",
    "channelId": "channel's name/id",
    "from": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "12345678",
        "name": "bot's name"
    },
    "text": "Haircut on Saturday"
}
```

Dans le message de l’utilisateur, la propriété `serviceUrl` indique que le bot doit envoyer sa réponse au point de terminaison `https://smba.trafficmanager.net/apis`. Il s’agira de l’URI de base pour toutes les prochaines requêtes émises par le bot dans le cadre de cette conversation. Si votre bot a besoin d’envoyer un message proactif à l’utilisateur, veillez à enregistrer la valeur de `serviceUrl`.

L’exemple suivant montre la requête émise par le bot pour répondre au message de l’utilisateur. 

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/bf3cc9a2f5de... 
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "bot's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
   "recipient": {
        "id": "1234abcd",
        "name": "user's name"
    },
    "text": "I have several times available on Saturday!",
    "replyToId": "bf3cc9a2f5de..."
}
```

## <a name="headers"></a>headers

### <a name="request-headers"></a>En-têtes de requête

Outre les en-têtes de requête HTTP standard, chaque requête d’API que vous émettez doit inclure un en-tête `Authorization` qui spécifie un jeton d’accès permettant d’authentifier votre bot. Spécifiez l’en-tête `Authorization` au format suivant :

```http
Authorization: Bearer ACCESS_TOKEN
```

Pour plus d’informations sur l’obtention d’un jeton d’accès pour votre bot, consultez [Authentifier les requêtes envoyées par votre bot au service Bot Connector](bot-framework-rest-connector-authentication.md#bot-to-connector).

### <a name="response-headers"></a>En-têtes de réponse

Outre les en-têtes de réponse HTTP standard, chaque réponse contient un en-tête `X-Correlating-OperationId`. La valeur de cet en-tête est un ID qui correspond à l’entrée de journal Bot Framework où se trouvent des informations détaillées sur la requête. Dès que vous recevez une réponse d’erreur, vous devez capturer la valeur de cet en-tête. Si vous ne parvenez pas à résoudre le problème, fournissez cette valeur à l’équipe du support technique lorsque vous lui signalez le problème.

## <a name="http-status-codes"></a>Codes d’état HTTP

Le <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html" target="_blank">code d’état HTTP</a> retourné avec chaque réponse indique le résultat de la requête correspondante. 

| Code d'état HTTP | Signification |
|----|----|
| 200 | La requête a réussi. |
| 201 | La requête a réussi. |
| 202 | La requête a été acceptée en vue de son traitement. |
| 204 | La requête a réussi, mais aucun contenu n’a été retourné. |
| 400 | La requête présentait un format inadéquat ou était incorrecte. |
| 401 | Le bot n’est pas autorisé à envoyer cette requête. |
| 403 | Le bot n’est pas autorisé à effectuer l’opération demandée. |
| 404 | La ressource demandée est introuvable. |
| 500 | Une erreur de serveur interne s’est produite. |
| 503 | Le service n’est pas disponible. |

### <a name="errors"></a>Errors

Toute réponse qui spécifie un code d’état HTTP dans la plage 4xx ou 5xx va inclure un objet [ErrorResponse](#bot-framework-activity-schema) dans le corps de la réponse qui fournit des informations sur l’erreur. Si vous recevez une réponse d’erreur dans la plage 4xx, examinez l’objet **ErrorResponse** pour identifier la cause de l’erreur et résoudre votre problème avant de renvoyer la requête.

## <a name="conversation-operations"></a>Opérations de conversation 
Utilisez ces opérations pour créer des conversations, envoyer des messages (activités) et gérer le contenu des conversations.

| Opération | Description |
|----|----|
| [Créer une conversation](#create-conversation) | Crée une conversation. |
| [Envoyer vers la conversation](#send-to-conversation) | Envoie une activité (message) à la fin de la conversation spécifiée. |
| [Répondre à l’activité](#reply-to-activity) | Envoie une activité (message) à la conversation spécifiée, en réponse à l’activité spécifiée. |
| [Obtenir des conversations](#get-conversations) | Obtient la liste des conversations auxquelles un robot a participé. |
| [Obtenir les membres de la conversation](#get-conversation-members) | Obtient les membres de la conversation spécifiée. |
| [Obtenir les membres paginés de la conversation](#get-conversation-paged-members) | Obtient les membres de la conversation spécifiée, une page à la fois. |
| [Obtenir les membres de l’activité](#get-activity-members) | Obtient les membres de l’activité spécifiée dans la conversation spécifiée. |
| [Mettre à jour l’activité](#update-activity) | Met à jour une activité existante. |
| [Supprimer l’activité](#delete-activity) | Supprime une activité existante. |
| [Supprimer un membre de conversation](#delete-conversation-member) | Supprime un membre d’une conversation. |
| [Envoyer l’historique des conversations](#send-conversation-history) | Charge une transcription des activités passées de la conversation. |
| [Charger la pièce jointe vers le canal](#upload-attachment-to-channel) | Charge une pièce jointe directement dans le stockage d’objets blob d’un canal. |

### <a name="create-conversation"></a>Créer une conversation
Crée une conversation.
```http 
POST /v3/conversations
```

| | |
|----|----|
| **Corps de la demande** | Objet `ConversationParameters` |
| **Retourne** | Objet `ConversationResourceResponse` |

### <a name="send-to-conversation"></a>Envoyer vers la conversation
Envoie une activité (message) vers la conversation spécifiée. L’activité est ajoutée à la fin de la conversation, selon l’horodatage ou la sémantique du canal. Pour répondre à un message de la conversation, utilisez plutôt [Répondre à l’activité](#reply-to-activity).
```http
POST /v3/conversations/{conversationId}/activities
```

| | |
|----|----|
| **Corps de la demande** | Objet [Activity](#bot-framework-activity-schema) |
| **Retourne** | Objet [Identification](#bot-framework-activity-schema) | 

### <a name="reply-to-activity"></a>Répondre à l’activité
Envoie une activité (message) à la conversation spécifiée, en réponse à l’activité spécifiée. L’activité sera ajoutée comme réponse à une autre activité, si le canal le permet. Si le canal ne prend pas en charge les réponses imbriquées, cette opération se comporte comme l’opération [Envoyer vers la conversation](#send-to-conversation).
```http
POST /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corps de la demande** | Objet [Activity](#bot-framework-activity-schema) |
| **Retourne** | Objet [Identification](#bot-framework-activity-schema) | 

### <a name="get-conversations"></a>Obtenir des conversations
Obtient la liste des conversations auxquelles un robot a participé.
```http
GET /v3/conversations?continuationToken={continuationToken}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Objet [ConversationResult](#bot-framework-activity-schema) | 

### <a name="get-conversation-members"></a>Obtenir les membres de la conversation
Obtient les membres de la conversation spécifiée.
```http
GET /v3/conversations/{conversationId}/members
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Tableau d’objets [ChannelAccount](#bot-framework-activity-schema) | 

### <a name="get-conversation-paged-members"></a>Obtenir les membres paginés de la conversation
Obtient les membres de la conversation spécifiée, une page à la fois.
```http
GET /v3/conversations/{conversationId}/pagedmembers?pageSize={pageSize}&continuationToken={continuationToken}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Un tableau d’objets [ChannelAccount](#bot-framework-activity-schema) et un jeton de liaison qui peut être utilisé pour obtenir plus de valeurs |

### <a name="get-activity-members"></a>Obtenir les membres de l’activité
Obtient les membres de l’activité spécifiée dans la conversation spécifiée.
```http
GET /v3/conversations/{conversationId}/activities/{activityId}/members
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Tableau d’objets [ChannelAccount](#bot-framework-activity-schema) | 

### <a name="update-activity"></a>Mettre à jour l’activité
Certains canaux vous permettent de modifier une activité existante afin de refléter le nouvel état d’une conversation de bot. Par exemple, vous pouvez supprimer des boutons dans un message de la conversation après qu’un utilisateur a cliqué sur l’un d’eux. En cas de réussite, cette opération met à jour l’activité spécifiée dans la conversation spécifiée. 
```http
PUT /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corps de la demande** | Objet [Activity](#bot-framework-activity-schema) |
| **Retourne** | Objet [Identification](#bot-framework-activity-schema) | 

### <a name="delete-activity"></a>Supprimer l’activité
Certains canaux vous permettent de supprimer une activité existante. En cas de réussite, cette opération supprime l’activité spécifiée dans la conversation spécifiée.
```http
DELETE /v3/conversations/{conversationId}/activities/{activityId}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Code d’état HTTP indiquant le résultat de l’opération. Rien n’est spécifié dans le corps de la réponse. | 

### <a name="delete-conversation-member"></a>Supprimer un membre de conversation
Supprime un membre d’une conversation. Si ce membre a été le dernier membre de la conversation, la conversation est également supprimée.
```http
DELETE /v3/conversations/{conversationId}/members/{memberId}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Code d’état HTTP indiquant le résultat de l’opération. Rien n’est spécifié dans le corps de la réponse. | 

### <a name="send-conversation-history"></a>Envoyer l’historique des conversations
Charge une transcription des activités passées de la conversation pour permettre au client de les afficher.
```http
POST /v3/conversations/{conversationId}/activities/history
```

| | |
|----|----|
| **Corps de la demande** | Objet [Transcript](#bot-framework-activity-schema). |
| **Retourne** | Objet [ResourceResponse](#bot-framework-activity-schema). | 

### <a name="upload-attachment-to-channel"></a>Charger la pièce jointe vers le canal
Charge une pièce jointe pour la conversation spécifiée, directement dans le stockage d’objets blob d’un canal. Cela permet de stocker des données dans un magasin conforme.
```http 
POST /v3/conversations/{conversationId}/attachments
```

| | |
|----|----|
| **Corps de la demande** | Objet [AttachmentUpload](#bot-framework-activity-schema) |
| **Retourne** | Objet [ResourceResponse](#bot-framework-activity-schema). La propriété **id** spécifie l’ID de pièce jointe qui peut être utilisé avec l’opération [Obtenir les informations de la pièce jointe](#get-attachment-info) et l’opération [Obtenir la pièce jointe](#get-attachment). | 

## <a name="attachment-operations"></a>Opérations de pièce jointe 
Utilisez ces opérations pour récupérer des informations sur une pièce jointe, ainsi que les données binaires du fichier.

| Opération | Description |
|----|----|
| [Obtenir les informations de la pièce jointe](#get-attachment-info) | Obtient des informations sur la pièce jointe spécifiée, y compris le nom du fichier, le type du fichier et les affichages disponibles (affichage d’origine ou miniature, par exemple). |
| [Obtenir la pièce jointe](#get-attachment) | Obtient l’affichage spécifié de la pièce jointe spécifiée sous forme de contenu binaire. | 

### <a name="get-attachment-info"></a>Obtenir les informations de la pièce jointe 
Obtient des informations sur la pièce jointe spécifiée, y compris le nom du fichier, son type, et les affichages disponibles (affichage d’origine ou miniature, par exemple).
```http
GET /v3/attachments/{attachmentId}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Objet [AttachmentInfo](#bot-framework-activity-schema) | 

### <a name="get-attachment"></a>Obtenir la pièce jointe
Obtient l’affichage spécifié de la pièce jointe spécifiée sous forme de contenu binaire.
```http
GET /v3/attachments/{attachmentId}/views/{viewId}
```

| | |
|----|----|
| **Corps de la demande** | n/a |
| **Retourne** | Contenu binaire qui représente l’affichage spécifié de la pièce jointe spécifiée. | 

## <a name="state-operations"></a>Opérations d’état
Le service Microsoft Bot Framework State a été mis hors service le 30 mars 2018. Auparavant, les bots basés sur Azure Bot Service ou le kit SDK Bot Builder avaient une connexion par défaut à ce service hébergé par Microsoft pour stocker les données d’état du bot. Les bots doivent être mis à jour pour utiliser leur propre stockage d’état.

| Opération | Description |
|----|----|
| `Set User Data` | Stocke les données d’état pour un utilisateur d’un canal. |
| `Set Conversation Data` | Stocke les données d’état pour une conversation d’un canal. |
| `Set Private Conversation Data` | Stocke les données d’état d’un utilisateur dans le contexte d’une conversation d’un canal. |
| `Get User Data` | Récupère les données d’état précédemment stockées pour un utilisateur dans toutes les conversations d’un canal. |
| `Get Conversation Data` | Récupère les données d’état précédemment stockées pour une conversation d’un canal. |
| `Get Private Conversation Data` | Récupère les données d’état précédemment stockées pour un utilisateur dans le contexte d’une conversation d’un canal. |
| `Delete State For User` | Supprime les données d’état qui ont été précédemment stockées pour un utilisateur. |

## <a name="bot-framework-activity-schema"></a>Schéma d’activité Bot Framework

Consultez le [schéma d’activité Bot Framework](https://aka.ms/botSpecs-activitySchema) pour connaître les objets et les propriétés que votre bot peut utiliser pour communiquer avec un utilisateur.
