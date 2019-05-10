---
ms.openlocfilehash: bb7520e8e99ad6326d7d00d8190dae306bf11afa
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035744"
---
Publiez votre bot local sur Azure. Cette étape peut prendre du temps.

```cmd
az bot publish --name <bot-resource-name> --proj-file-path "<project-file-name>" --resource-group <resource-group-name> --code-dir <directory-path> --verbose --version v4
```

| Option | Description |
|:---|:---|
| --name | Nom de ressource du bot dans Azure. |
| --proj-file-path | Pour C#, utilisez le nom du fichier projet de démarrage (sans .csproj) qui doit être publié. Par exemple : `EnterpriseBot`. Pour Node.js, utilisez le point d’entrée principal du bot. Par exemple : `index.js`. |
| --resource-group | Nom du groupe de ressources. |
| --code-dir | Répertoire depuis lequel charger le code du bot. |

Une fois cette opération terminée avec le message « Déploiement réussi ! », votre bot est déployé sur Azure.
