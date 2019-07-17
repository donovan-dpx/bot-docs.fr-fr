---
ms.openlocfilehash: b320aadc876074a76fe209ad55a81cb70b1ddcac
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230550"
---
Le <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">contrôle de webchat open source</a> communique avec les bots au moyen de l’[API Direct Line](https://docs.botframework.com/restapi/directline3/#navtitle), ce qui permet à `activities` de faire la navette entre le client et le bot. Le type d’activité le plus courant est `message`, mais il existe également d’autres types. Par exemple, le type d’activité `typing` indique qu’un utilisateur tape sur un clavier, ou que le bot travaille à la compilation d’une réponse. 

Vous pouvez utiliser le mécanisme de backchannel pour échanger des informations entre le client et le bot, sans le présenter à l’utilisateur, en définissant le type d’activité sur `event`. Le contrôle de webchat ignore automatiquement toutes les activités où `type="event"` est mentionné.