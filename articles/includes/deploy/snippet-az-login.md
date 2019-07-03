---
ms.openlocfilehash: f8aad539a2d1e415833609f66cd5b398c88206f1
ms.sourcegitcommit: a47183f5d1c2b2454c4a06c0f292d7c075612cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2019
ms.locfileid: "67252668"
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