---
ms.openlocfilehash: 0891b9652154f8ed086cc45ce6018aa0be1a67b8
ms.sourcegitcommit: fa6e775dcf95a4253ad854796f5906f33af05a42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68230575"
---
Pour republier votre bot JavaScript local sur Azure, vous devez d’abord créer manuellement un fichier zip contenant tous les fichiers utilisés pour générer et exécuter localement votre bot. Cela inclut toutes les bibliothèques npm téléchargées dans votre dossier `node_modules`. Au moment de la création de ce fichier zip, _vérifiez que le répertoire racine que vous utilisez est le même que celui où se trouve votre fichier index.js_.

Après avoir créé un fichier zip contenant tout le code source de votre bot, ouvrez une fenêtre d’invite de commandes et exécutez la commande _Az cli_ suivante. 

Cette étape peut prendre du temps.

```cmd
az webapp deployment source config-zip --resource-group <resource-group-name> --name <bot-resource-name> --src <directory-path>
```

| Option | Description |
|:---|:---|
| --resource-group | Nom du groupe de ressources dans Azure. |
| --name | Nom de ressource du bot dans Azure. |
| --src | Chemin complet du répertoire dans lequel charger le fichier zip du code de votre bot. Par exemple `c:\my-local-repository\this-app-folder\my-zipped-code.zip` |

Une fois cette opération terminée, votre bot est déployé sur Azure.
