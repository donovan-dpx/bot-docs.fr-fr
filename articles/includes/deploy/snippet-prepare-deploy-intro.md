---
ms.openlocfilehash: 3cb29b395019e720d5bc2ef7475c48f6c5757206
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491166"
---
Quand vous créez un bot à l’aide du [modèle Visual Studio](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0), du [modèle Yeoman](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0) ou du [modèle Cookiecutter](https://docs.microsoft.com/azure/bot-service/python/bot-builder-python-quickstart?view=azure-bot-service-4.0), le code source généré comprend un dossier `deploymentTemplates` qui contient des modèles ARM. Le processus de déploiement documenté ici utilise l’un des modèles ARM pour provisionner les ressources nécessaires pour le bot dans Azure à l’aide de l’interface Azure CLI. 

> [!NOTE]
> Avec la version 4.3 du kit SDK Bot Framework, nous avons _déprécié_ l’utilisation d’un fichier .bot. Au lieu de cela, nous utilisons un fichier appsettings.json ou .env pour gérer les ressources du bot. Pour plus d’informations sur la migration des paramètres du fichier .bot vers le fichier appsettings.json ou .env, consultez [Gérer les ressources du bot](https://docs.microsoft.com/azure/bot-service/bot-file-basics?view=azure-bot-service-4.0).
