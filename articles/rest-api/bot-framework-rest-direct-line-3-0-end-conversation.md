---
title: Mettre fin à une conversation | Microsoft Docs
description: Découvrez comment mettre fin à une conversation à l’aide de l’API Direct Line v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 3006672ba8459ebec7d754a7ec31679debdba4aa
ms.sourcegitcommit: e815e786413296deea0bd78e5a495df329a9a7cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876161"
---
# <a name="end-a-conversation"></a>Mettre fin à une conversation

L’[activité](https://aka.ms/botSpecs-activitySchema) **endOfConversation** signifie que le canal ou le bot a mis fin à la conversation. 

> [!NOTE] 
> Alors que l’événement **endOfConversation** est envoyé uniquement par très peu de canaux, le canal Cortana est le seul qui l’accepte. D’autres canaux, dont Direct Line, n’implémentent pas cette fonctionnalité. À la place, ils déposent ou transfèrent l’activité. Chaque canal détermine comment réagir à une activité endOfConversation. Si vous concevez un client DirectLine, vous devrez mettre à jour le client pour qu’il se comporte de manière adéquate, en générant par exemple une erreur si le bot a envoyé une activité à une conversation déjà terminée.

## <a name="send-an-endofconversation-activity"></a>Envoyer une activité endOfConversation

Une activité **endOfConversation** met fin à la communication entre le robot et le client. Après l’envoi d’une activité **endOfConversation**, le client peut toujours [récupérer des messages](bot-framework-rest-direct-line-3-0-receive-activities.md#http-get) avec `HTTP GET`, mais ni le client ni le robot ne peuvent envoyer de messages supplémentaires à la conversation. 

Pour mettre fin à une conversation, il suffit d’émettre une requête de PUBLICATION afin d’envoyer une activité **endOfConversation**.

### <a name="request"></a>Requête

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
    "type": "endOfConversation",
    "from": {
        "id": "user1"
    }
}
```

### <a name="response"></a>response

Si la requête est acceptée, la réponse contiendra un identifiant de l’activité envoyée.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0004"
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

- [Concepts clés](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentification](bot-framework-rest-direct-line-3-0-authentication.md)
- [Envoyer une activité au robot](bot-framework-rest-direct-line-3-0-send-activity.md)
