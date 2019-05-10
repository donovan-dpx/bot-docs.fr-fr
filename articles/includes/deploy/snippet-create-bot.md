---
ms.openlocfilehash: f95c8a37b1207a26dab0a714b86412a9dba2dcb4
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035761"
---
```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```

| Option | Description |
|:---|:---|
| --name | Nom unique utilisé pour déployer le bot dans Azure. Il peut s’agir du même nom que celui de votre bot local. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| --location | Emplacement géographique utilisé pour créer les ressources de service du bot. Par exemple, `eastus`, `westus`, `westus2`, etc. |
| --lang | Langage à utiliser pour créer le bot : `Csharp` ou `Node` ; la valeur par défaut est `Csharp`. |
| --resource-group | Nom du groupe de ressources dans lequel créer le bot. Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |