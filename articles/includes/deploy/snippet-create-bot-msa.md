---
ms.openlocfilehash: 14d9632ad578014a36b5f13e6dee883e2a6e1722
ms.sourcegitcommit: a47183f5d1c2b2454c4a06c0f292d7c075612cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2019
ms.locfileid: "67252649"
---
1. Connectez-vous au [**portail d’inscription des applications**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
1. Cliquez sur **Ajouter une application** pour inscrire votre application, créez un **ID d’application**, puis **générez un nouveau mot de passe**. Si vous avez déjà une application et un mot de passe mais que vous avez oublié le mot de passe, vous devrez en générer un nouveau dans la section des secrets d’application.
1. Enregistrez l’ID d’application et le nouveau mot de passe que vous venez de générer afin de pouvoir les utiliser avec la commande `az bot create`.  

```cmd
az bot create --kind webapp --name <bot-resource-name> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Option | Description |
|:---|:---|
| --name | Nom unique utilisé pour déployer le bot dans Azure. Il peut s’agir du même nom que celui de votre bot local. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| --location | Emplacement géographique utilisé pour créer les ressources de service du bot. Par exemple, `eastus`, `westus`, `westus2`, etc. |
| --lang | Langage à utiliser pour créer le bot : `Csharp` ou `Node` ; la valeur par défaut est `Csharp`. |
| --resource-group | Nom du groupe de ressources dans lequel créer le bot. Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| --appid | ID du compte Microsoft (ID MSA) à utiliser avec le bot. |
| --password | Mot de passe du compte Microsoft (MSA) pour le bot. |
