---
ms.openlocfilehash: 28f49af1610f3c154e76d1cfcdb1333f08374f6c
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386102"
---
Inscrire l’application signifie que vous pouvez utiliser Azure AD pour authentifier les utilisateurs et demander l’accès aux ressources utilisateur. Votre bot a besoin d’une application inscrite dans Azure qui permet au bot d’accéder à Bot Framework Service pour l’envoi et la réception de messages authentifiés. Pour créer et inscrire une application par le biais de l’interface Azure CLI, exécutez la commande suivante :

```cmd
az ad app create --display-name "displayName" --password "AtLeastSixteenCharacters_0" --available-to-other-tenants
```

| Option   | Description |
|:---------|:------------|
| display-name | Nom d’affichage de l’application. |
| password | Mot de passe de l’application, également appelé « secret client ». Le mot de passe doit comporter au moins 16 caractères, contenir au moins un caractère alphabétique minuscule ou majuscule, et contenir au moins un caractère spécial.|
| available-to-other-tenants| Indique si l’application peut être utilisée à partir de n’importe quel locataire Azure AD. La valeur `true` doit être définie pour permettre à votre bot de fonctionner avec les canaux Azure Bot Service.|

La commande ci-dessus génère du code JSON avec la clé `appId` et enregistre la valeur de cette clé pour le déploiement ARM, où elle sera utilisée pour le paramètre `appId`. Le mot de passe fourni sera utilisé pour le paramètre `appSecret`.

> [!NOTE] 
> Si vous souhaitez utiliser une inscription d’application existante, vous pouvez utiliser la commande :
> ``` cmd
> az bot create --kind webapp --resource-group "<name-of-resource-group>" --name "<name-of-web-app>" --appid "<existing-app-id>" --password "<existing-app-password>" --lang <Javascript|Csharp>
> ```
