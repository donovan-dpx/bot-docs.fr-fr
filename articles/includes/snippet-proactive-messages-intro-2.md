---
ms.openlocfilehash: 4108f02a350d7e02d66db06cd7e65fa9c7a98e7d
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "70297348"
---
Un **message proactif ad hoc** constitue le type de message proactif le plus simple.
Le bot injecte simplement le message dans la conversation chaque fois qu’il est déclenché, que l’utilisateur soit ou non déjà engagé dans une autre sujet de conversation avec le bot, et ne tentera pas de modifier la conversation d’une quelconque manière.

Pour gérer plus facilement les notifications, pensez à d’autres méthodes pour intégrer la notification dans le flux de messages. Par exemple, vous pouvez définir un indicateur dans l’état de la conversation ou ajouter la notification à une file d’attente.

<!--Snip
A **dialog-based proactive message** is more complex than an ad hoc proactive message. 
Before it can inject this type of proactive message into the conversation, 
the bot must identify the context of the existing conversation and decide how (or if)
it will resume that conversation after the message interrupts. 

For example, consider a bot that needs to initiate a survey at a given point in time. 
When that time arrives, the bot stops the existing conversation with the user and 
redirects the user to a `SurveyDialog`. 
The `SurveyDialog` is added to the top of the dialog stack and takes control of the conversation. 
When the user finishes all required tasks at the `SurveyDialog`, the `SurveyDialog` closes,
 returning control to the previous dialog, where the user can continue with the prior topic of conversation.

A dialog-based proactive message is more than just simple notification. 
In sending the notification, the bot changes the topic of the existing conversation. 
It then must decide whether to resume that conversation later, or to abandon that conversation altogether by resetting the dialog stack. 
/Snip-->