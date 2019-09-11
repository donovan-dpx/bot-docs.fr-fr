---
ms.openlocfilehash: 12ead266dea859c84450e08ae12ed98d4952d698
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "70297372"
---
La fonctionnalité d’**intergiciel (middleware)** du kit SDK Bot Framework permet à votre bot d’intercepter tous les messages échangés entre l’utilisateur et le bot. Pour chaque message intercepté, vous pouvez choisir d’effectuer des opérations telles qu’enregistrer le message dans un magasin de données spécifié, ce qui crée un journal de conversation, ou d’inspecter le message d’une façon ou d’une autre et d’effectuer l’action spécifiée par votre code. 

> [!NOTE]
> Le Bot Framework n’enregistre pas automatiquement les détails de la conversation, car cette opération peut potentiellement enregistrer des informations privées que les bots et les utilisateurs ne souhaitent pas partager avec des tiers. Si votre bot enregistre les détails de la conversation, il doit le communiquer à l’utilisateur et décrire ce qu’il adviendra de ces données.