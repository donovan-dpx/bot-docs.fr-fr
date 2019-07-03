---
ms.openlocfilehash: 867e65b25878f810e3247eb3cace95f4d31e11db
ms.sourcegitcommit: a47183f5d1c2b2454c4a06c0f292d7c075612cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2019
ms.locfileid: "67252618"
---
Utilisez un répertoire temporaire en dehors de votre répertoire de projet actuel. 

Cette commande crée un sous-répertoire sous save-path ; toutefois, le chemin spécifié doit déjà exister.

```cmd
az bot download --name <bot-resource-name> --resource-group <resource-group-name> --save-path "<path>"
```

| Option | Description |
|:---|:---|
| --name | Nom du bot dans Azure. |
| --resource-group | Nom du groupe de ressources dans lequel se trouve le bot. |
| --save-path | Répertoire existant sur lequel télécharger le code du bot. |