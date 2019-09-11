---
ms.openlocfilehash: c664749bc6ad63d1b0f60e001b2603898e58a9ea
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386000"
---
Une fois que vous avez créé et testé un bot localement, vous pouvez le déployer sur Azure. Ouvrez une invite de commandes pour vous connecter au portail Azure.

```cmd
az login
```
Une nouvelle fenêtre de navigateur s’ouvre ; connectez-vous.

> [!NOTE]
> Si vous déployez votre bot sur un cloud autre qu’Azure comme Gov (US), vous devez exécuter `az cloud set --name <name-of-cloud>` avant `az login`, où &lt;nom-de-cloud> est le nom d’un cloud inscrit, par exemple `AzureUSGovernment`. Si vous souhaitez revenir au cloud public, vous pouvez exécuter `az cloud set --name AzureCloud`. 
