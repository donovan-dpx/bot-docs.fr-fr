---
ms.openlocfilehash: 867e65b25878f810e3247eb3cace95f4d31e11db
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035729"
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