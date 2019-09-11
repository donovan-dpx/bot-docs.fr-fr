---
ms.openlocfilehash: 80cc69ca3d863a4f96f1dd241e6471074cc9976f
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386052"
---
Quand vous utilisez l’[API de déploiement à partir d’un fichier zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) non configurée pour déployer le code de votre bot, le comportement de Kudu/de l’application web est le suivant :

_Par défaut, Kudu part du principe que les déploiements à partir de fichiers zip sont prêts à être exécutés et ne nécessitent aucune étape de génération supplémentaire pendant le déploiement, telle que npm install ou dotnet restore/dotnet publish._

Par conséquent, il est important d’inclure votre code généré et toutes les dépendances nécessaires dans le fichier zip déployé sur l’application web, sans quoi votre bot ne fonctionnera pas comme prévu.

> [!IMPORTANT]
> Avant de compresser vos fichiers projet, vérifiez que vous êtes bien _dans_ le dossier du projet. 
> - Pour les bots C#, il s’agit du dossier contenant le fichier .csproj. 
> - Pour les bots JS, il s’agit du dossier contenant le fichier app.js ou index.js. 
>
>**Dans** le dossier du projet, sélectionnez tous les fichiers et compressez-les, puis exécutez la commande, toujours dans le dossier. 
>
> Si l’emplacement de votre dossier racine est incorrect, l’**exécution du bot échouera dans le portail Azure**.
