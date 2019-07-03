---
ms.openlocfilehash: 117f95799df0abbe957000d4979b10f05baf262c
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67405535"
---
Avant de commencer le déploiement, assurez-vous d’avoir la dernière version d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) et de [.NET CLI](https://dotnet.microsoft.com/download). Si vous n’avez pas .NET CLI, installez-le à l’aide de l’option Runtime .NET Core en suivant le lien fourni ci-dessus. 

### <a name="login-to-azure-cli-and-set-your-subscription"></a>Se connecter à Azure CLI et définir votre abonnement
Vous avez déjà créé et testé un bot localement et vous souhaitez maintenant le déployer sur Azure. Ouvrez une invite de commandes pour vous connecter au portail Azure.

```cmd
az login
```
### <a name="set-the-subscription"></a>Définir l’abonnement

Définissez l’abonnement par défaut à utiliser.

```cmd
az account set --subscription "<azure-subscription>"
```

Si vous ne savez pas quel abonnement utiliser pour déployer le bot, vous pouvez consulter la liste des abonnements pour votre compte à l’aide de la commande `az account list`.

Accédez au dossier bot.
`cd <local-bot-folder>`

### <a name="create-a-web-app-bot-in-azure"></a>Créer un Web App Bot dans Azure 

Si vous ne disposez pas déjà d’un groupe de ressources dans lequel publier votre bot local, créez-en un :

```cmd
az group create --name <resource-group-name> --location <geographic-location> --verbose
```

| Option     | Description |
|:-----------|:---|
| Nom     | Nom unique du groupe de ressources. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| location | Emplacement géographique utilisé pour créer le groupe de ressources. Par exemple, `eastus`, `westus`, `westus2`, etc. Utilisez `az account list-locations` pour obtenir une liste d’emplacements. |

Créez ensuite la ressource de bot dans laquelle vous publierez votre bot. Les ressources nécessaires seront ainsi provisionnées dans Azure et une application web de bot sera créée, que vous remplacerez par votre bot local. 

Avant de continuer, lisez les instructions qui s’appliquent à votre cas en fonction du type de compte e-mail que vous utilisez pour vous connecter à Azure.

#### <a name="msa-email-account"></a>Compte e-mail MSA
Si vous utilisez un compte e-mail MSA, vous devez créer un ID et un mot de passe d’application sur le portail d’inscription des applications à utiliser avec la commande `az bot create`.
1. Connectez-vous au [**portail d’inscription des applications**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
1. Cliquez sur **Ajouter une application** pour inscrire votre application, créez un **ID d’application**, puis **générez un nouveau mot de passe**. Si vous avez déjà une application et un mot de passe mais que vous avez oublié le mot de passe, vous devrez en générer un nouveau dans la section des secrets d’application.
1. Enregistrez l’ID d’application et le nouveau mot de passe que vous venez de générer afin de pouvoir les utiliser avec la commande `az bot create`.  

```cmd
az bot create --kind webapp --name <bot-name-in-azure> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name> --appid "<application-id>" --password "<application-password>" --verbose
```

| Option | Description |
|:---|:---|
| Nom | Nom unique utilisé pour déployer le bot dans Azure. Il peut s’agir du même nom que celui de votre bot local. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| location | Emplacement géographique utilisé pour créer les ressources de service du bot. Par exemple, `eastus`, `westus`, `westus2`, etc. |
| resource-group | Nom du groupe de ressources dans lequel créer le bot. Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |
| appid | ID du compte Microsoft (ID MSA) à utiliser avec le bot. |
| password | Mot de passe du compte Microsoft (MSA) pour le bot. |

#### <a name="business-or-school-account"></a>Compte professionnel ou scolaire

```cmd
az bot create --kind webapp --name <bot-name-in-azure> --location <geographic-location> --version v4 --lang <language> --verbose --resource-group <resource-group-name>
```
| Option | Description |
|:---|:---|
| Nom | Nom unique utilisé pour déployer le bot dans Azure. Il peut s’agir du même nom que celui de votre bot local. N’incluez PAS d’espaces ni de traits de soulignement dans le nom. |
| location | Emplacement géographique utilisé pour créer les ressources de service du bot. Par exemple, `eastus`, `westus`, `westus2`, etc. |
| lang | Langage à utiliser pour créer le bot : `Csharp` ou `Node` ; la valeur par défaut est `Csharp`. |
| resource-group | Nom du groupe de ressources dans lequel créer le bot. Vous pouvez configurer le groupe par défaut en utilisant `az configure --defaults group=<name>`. |

#### <a name="update-appsettingsjson-or-env-file"></a>Mettre à jour le fichier appsettings.json ou .env
Une fois que le bot a été créé, vous devez voir les informations suivantes, affichées dans la fenêtre de la console : 

```JSON
{
  "appId": "as234-345b-4def-9047-a8a44b4s",
  "appPassword": "34$#w%^$%23@334343",
  "endpoint": "https://mybot.azurewebsites.net/api/messages",
  "id": "mybot",
  "name": "mybot",
  "resourceGroup": "botresourcegroup",
  "serviceName": "mybot",
  "subscriptionId": "234532-8720-5632-a3e2-a1qw234",
  "tenantId": "32f955bf-33f1-43af-3ab-23d009defs47",
  "type": "abs"
}
```

Vous devez copier les valeurs `appId` et `appPassword`, et les coller dans le fichier appsettings.json ou .env. Par exemple :

```JSON
{
  MicrosoftAppId: "as234-345b-4def-9047-a8a44b4s",
  MicrosoftAppPassword: "34$#w%^$%23@334343"
}
```
Notez que si votre fichier appsettings.json ou .env a des clés supplémentaires pour d’autres services que vous avez configurés pour votre bot, ne supprimez pas ces entrées.

Enregistrez le fichier .

Ensuite, selon le langage de programmation (**C#** ou **JS**) que vous avez utilisé pour créer le bot, suivez les étapes qui s’appliquent à vous.

**Bot C# :** 

Ouvrez une invite de commandes et accédez au dossier du projet. Exécutez les commandes suivantes à partir de la ligne de commande.

| Tâche | Commande |
|:-----|:--------|
| 1. Restaurer les dépendances du projet | `dotnet restore`|
| 2. Créer le projet     | `dotnet build` |
| 3. Compresser les fichiers du projet | Utilisez un utilitaire pour compresser les fichiers du projet. Accédez au dossier contenant le fichier .csproj et sélectionnez tous les fichiers et le dossier à ce niveau pour créer le dossier compressé. |
| 4. Définir le paramètre de déploiement de build | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_DO_BUILD_DEPLOYMENT=false`|
| 5. Définir les arguments du générateur de script | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_SCRIPT_GENERATOR_ARGS="--aspNetCore mybot.csproj"`|

**Bot JS :**
1. Téléchargez [ici](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) le fichier web.config et enregistrez-le dans votre dossier de projet. 
1. Modifiez le fichier et remplacez toutes les occurrences de « server.js » par « index.js ». 
1. Enregistrez le fichier .

Ouvrez une invite de commandes et accédez au dossier du projet. Exécutez les commandes suivantes à partir de la ligne de commande.

| Tâche | Commande |
|:-----|:--------|
| 1. Installer les modules Node | `npm install` |
| 2. Compresser les fichiers du projet | Utilisez un utilitaire pour compresser les fichiers du projet. Accédez au dossier contenant le fichier .csproj et sélectionnez tous les fichiers et le dossier à ce niveau pour créer le dossier compressé. |
| 3. Définir le paramètre de déploiement de build | `az webapp config appsettings set --resource-group <resource-group-name> --name <bot-name> --settings SCM_DO_BUILD_DEPLOYMENT=false`|
