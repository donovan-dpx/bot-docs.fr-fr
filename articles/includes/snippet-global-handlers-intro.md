---
ms.openlocfilehash: b5809b6d46cdc09035efb36c3ea58c2ca9dc6c3e
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230720"
---
Les utilisateurs tentent souvent d’accéder à certaines fonctionnalités d’un bot en utilisant des mots clés comme « aide », « annuler » ou « recommencer ». Cela se produit souvent au milieu d’une conversation, lorsque le bot attend une réponse différente. En implémentant des **gestionnaires de messages global**, vous pouvez concevoir votre robot de façon à ce qu’il puisse gérer ces demandes.
Les gestionnaires cherchent les mots clés spécifiés (par exemple, « aide », « annuler », « recommencer », etc.) dans les entrées utilisateur et réagissent de façon appropriée. 

![manière dont les utilisateurs s’expriment](~/media/designing-bots/capabilities/trigger-actions.png)

> [!NOTE]
> En définissant la logique dans les gestionnaires de messages globaux, vous les rendez accessibles à tous les types de dialogues. Les invites et boîtes de dialogue individuelles peuvent être configurées pour ignorer les mots clés en toute sécurité.
