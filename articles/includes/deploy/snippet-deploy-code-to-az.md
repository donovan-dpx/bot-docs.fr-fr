---
ms.openlocfilehash: 0c28cf506f2701acfc705fdcc67ec6a1e7224ad1
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386069"
---
À ce stade, nous sommes prêts à déployer le code sur l’application web Azure. Exécutez la commande suivante à partir de la ligne de commande pour effectuer un déploiement push de fichier zip kudu pour une application web.

```cmd
az webapp deployment source config-zip --resource-group "<resource-group-name>" --name "<name-of-web-app>" --src "code.zip" 
```

| Option   | Description |
|:---------|:------------|
| resource-group | Nom du groupe de ressources Azure qui contient votre bot. (Il s’agit du groupe de ressources que vous avez utilisé ou créé lors de la création de l’inscription de l’application pour votre bot.) |
| Nom | Nom de l’application web utilisée précédemment. |
| src  | Chemin du fichier compressé que vous avez créé. |
