---
ms.openlocfilehash: 91b2729aa10c8ac1985e62845126296e8141ef77
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230652"
---
> [!IMPORTANT]
> Le thread qui traite le tour de bot principal se charge de supprimer l’objet de contexte quand il prend fin. **Veillez à exécuter une opération `await` sur tous les appels d’activité** afin que le thread principal attende l’activité générée avant de terminer son traitement et de supprimer le contexte de tour. Sinon, si une réponse (et ses gestionnaires) prend un certain temps et essaie d’agir sur l’objet de contexte, elle risque de recevoir une erreur de type _contexte supprimé_.