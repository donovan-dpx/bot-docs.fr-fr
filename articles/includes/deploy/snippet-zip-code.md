---
ms.openlocfilehash: fdbafb6ca852a3b06b791d6580c72b90e02d4218
ms.sourcegitcommit: dcacda776c927bcc7c76d00ff3cc6b00b062bd6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74410481"
---
Quand vous utilisez l’[API de déploiement à partir d’un fichier zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) non configurée pour déployer le code de votre bot, le comportement de Kudu/de l’application web est le suivant :

_Par défaut, Kudu part du principe que les déploiements à partir de fichiers zip sont prêts à être exécutés et ne nécessitent aucune étape de génération supplémentaire pendant le déploiement, telle que npm install ou dotnet restore/dotnet publish._

Par conséquent, il est important d’inclure votre code généré et toutes les dépendances nécessaires dans le fichier zip déployé sur l’application web, sans quoi votre bot ne fonctionnera pas comme prévu.

> [!IMPORTANT]
> Avant de compresser vos fichiers projet, vérifiez que vous êtes bien _dans_ le dossier du projet. 
> - Pour les bots C#, il s’agit du dossier contenant le fichier .csproj. 
> - Pour les bots JavaScript, il s’agit du dossier contenant le fichier app.js ou index.js. 
> - Pour les bots TypeScript, il s’agit du dossier qui contient le dossier _src_ (où se trouvent les fichiers bot.ts et index.ts). 
>
>**Dans** le dossier du projet, sélectionnez tous les fichiers et dossiers que vous souhaitez inclure dans votre fichier. zip avant d’exécuter la commande de création du zip ; en procédant ainsi, un seul fichier zip contenant tous les fichiers et dossiers sélectionnés est créé.
>
> Si l’emplacement de votre dossier racine est incorrect, l’**exécution du bot échouera dans le portail Azure**.
