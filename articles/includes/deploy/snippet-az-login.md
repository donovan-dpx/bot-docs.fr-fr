---
ms.openlocfilehash: f8aad539a2d1e415833609f66cd5b398c88206f1
ms.sourcegitcommit: 4ff7a8772124a567f43e2c3e13aded368c4002e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035777"
---
Ouvrez une invite de commandes pour vous connecter au portail Azure.

```cmd
az login
```

Une nouvelle fenêtre de navigateur s’ouvre ; connectez-vous.

### <a name="set-the-subscription"></a>Définir l’abonnement

Définissez l’abonnement par défaut à utiliser.

```cmd
az account set --subscription "<azure-subscription>"
```

Si vous n’êtes pas sûr de savoir quel abonnement utiliser pour déployer le bot, vous pouvez voir la liste des `subscriptions` de votre compte à l’aide de la commande `az account list`.

Accédez au dossier bot.

```cmd
cd <local-bot-folder>
```