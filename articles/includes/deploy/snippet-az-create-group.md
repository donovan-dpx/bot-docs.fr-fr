---
ms.openlocfilehash: 53db401b00e53b964027f08c32ca7a38a4915f60
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035700"
---
```cmd
az group create --name <resource-group-name> --location <geographic-location> --verbose
```

| Option | Description |
|:---|:---|
| --name | Nom unique du groupe de ressources. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| --location | Emplacement géographique utilisé pour créer le groupe de ressources. Par exemple, `eastus`, `westus`, `westus2`, etc. Utilisez `az account list-locations` pour obtenir une liste d’emplacements. |