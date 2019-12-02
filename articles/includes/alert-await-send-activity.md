---
ms.openlocfilehash: 99bd2b58bafd1c4ece57aad5decc710346f56f86
ms.sourcegitcommit: 08f9dc91152e0d4565368f72f547cdea1885af89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536372"
---
> [!IMPORTANT]
> Le thread qui traite le tour de bot principal se charge de supprimer l’objet de contexte quand il prend fin. **Veillez à exécuter une opération `await` sur tous les appels d’activité**, afin que le thread principal attende l’activité générée avant de terminer son traitement et de supprimer le contexte de tour. Sinon, si une réponse (et ses gestionnaires) prend un certain temps et essaie d’agir sur l’objet de contexte, elle risque de recevoir une erreur de type _contexte supprimé_.