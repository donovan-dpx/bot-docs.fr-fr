---
ms.openlocfilehash: cf0e23e349ace78958f861ea2e650a5ebcb78466
ms.sourcegitcommit: 980612a922b8290b2faadaca193496c4117e415a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64563748"
---
Le <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">contrôle de webchat open source</a> communique avec les bots au moyen de l’[API Direct Line](https://docs.botframework.com/en-us/restapi/directline3/#navtitle), ce qui permet à `activities` de faire la navette entre le client et le bot. Le type d’activité le plus courant est `message`, mais il existe également d’autres types. Par exemple, le type d’activité `typing` indique qu’un utilisateur tape sur un clavier, ou que le bot travaille à la compilation d’une réponse. 

Vous pouvez utiliser le mécanisme de backchannel pour échanger des informations entre le client et le bot, sans le présenter à l’utilisateur, en définissant le type d’activité sur `event`. Le contrôle de webchat ignore automatiquement toutes les activités où `type="event"` est mentionné.