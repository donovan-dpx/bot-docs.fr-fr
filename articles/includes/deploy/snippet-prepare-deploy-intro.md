---
ms.openlocfilehash: db7c4b447c5a27fe2047cfa41e65fac0d3e3a86c
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386008"
---
Quand vous créez un bot à l’aide du [modèle Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0) ou [modèle Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0), le code source généré contient un dossier `deploymentTemplates` renfermant des modèles ARM. Le processus de déploiement documenté ici utilise l’un des modèles ARM pour provisionner les ressources nécessaires pour le bot dans Azure à l’aide de l’interface Azure CLI. 

> [!NOTE]
> Avec la version 4.3 du kit SDK Bot Framework, nous avons _déprécié_ l’utilisation d’un fichier .bot. Au lieu de cela, nous utilisons un fichier appsettings.json ou .env pour gérer les ressources du bot. Pour plus d’informations sur la migration des paramètres du fichier .bot vers le fichier appsettings.json ou .env, consultez [Gérer les ressources du bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).
