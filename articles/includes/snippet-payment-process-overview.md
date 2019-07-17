---
ms.openlocfilehash: 86faca976bc95a5e91e17749096cd148483edc61
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230677"
---
## <a name="payment-process-overview"></a>Vue d’ensemble du processus de paiement

Le processus de paiement se compose de trois parties distinctes :

1. Le bot envoie une demande de paiement.

2. L’utilisateur se connecte avec un compte Microsoft pour fournir des informations concernant le paiement, la livraison, ainsi que ses coordonnées. Des rappels sont envoyés au bot pour lui indiquer le moment où il doit effectuer certaines opérations (mettre à jour l’adresse de livraison, mettre à jour une option de livraison, finaliser le paiement).

3. Le bot traite les rappels qu’il reçoit, notamment la mise à jour de l’adresse de livraison, la mise à jour de l’option de livraison et la finalisation du paiement. 

Votre bot doit implémenter uniquement l’étape 1 et l’étape 3 de ce processus ; l’étape 2 a lieu en dehors du contexte de votre bot. 
