---
title: Déploiement d’un modèle de bot d’entreprise | Microsoft Docs
description: Découvrez comment déployer toutes les ressources Azure compatibles pour votre bot d’entreprise.
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/18/2018
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: e888b2473269cf576fd9edda0d99a30aa6212f7b
ms.sourcegitcommit: b2245df2f0a18c5a66a836ab24a573fd70c7d272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571852"
---
# <a name="enterprise-bot-template---getting-started"></a>Bien démarrer avec Enterprise Bot Template

> [!NOTE]
> Cet article s’applique à la version v4 du Kit de développement logiciel (SDK). 

## <a name="prerequisites"></a>Prérequis

Installez les éléments suivants :
- [VSIX Enterprise Bot Template](https://marketplace.visualstudio.com/items?itemName=BotBuilder.botbuilderv4enterprise)
- [Kit SDK .NET Core](https://www.microsoft.com/net/download) (dernière version)
- [Gestionnaire de package Node](https://nodejs.org/en/)
- [Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0) (dernière version)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Outils CLI d’Azure Bot Service](https://github.com/Microsoft/botbuilder-tools) (dernières versions)
    ```shell
    npm install -g ludown luis-apis qnamaker botdispatch msbot chatdown
    ```
- [LuisGen](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/LUISGen/src/npm/readme.md)
    ```shell
    dotnet tool install -g luisgen
    ```

## <a name="create-your-bot-project"></a>Créer votre projet de bot
1. Dans Visual Studio, cliquez sur **Fichier > Nouveau > Projet**.
1. Sous **Bot**, sélectionnez **Enterprise Bot Template**.

![Fichier - Nouveau - Projet - Modèle](media/enterprise-template/new_project.jpg)

1. Nommez votre projet, puis cliquez sur **Créer**.
1. Cliquez avec le bouton droit sur votre projet, puis cliquez sur **Générer** pour restaurer vos packages NuGet.

## <a name="deploy-your-bot"></a>Déployer votre bot

Les bots Enterprise Template Bot ont besoin des services Azure suivants pour assurer les opérations de bout en bout :
- Application web Azure
- Compte de stockage Azure (transcriptions)
- Azure Application Insights (données de télémétrie)
- Azure Cosmos DB (stockage de l’état des conversations)
- Azure Cognitive Services – Language Understanding
- Azure Cognitive Services – QnA Maker (inclut Recherche Azure et Application web Azure)

Suivez les étapes ci-dessous pour déployer ces services facilement à l’aide des scripts de déploiement fournis :

1. Récupérez votre clé de création LUIS.
   - Consultez [cette page](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) de documentation pour connaître le portail LUIS correspondant à la région de votre déploiement. Notez que www.luis.ai fait référence à la région des États-Unis et qu’une clé de création récupérée sur ce portail ne fonctionnera pas pour un déploiement en Europe.
   - Une fois connecté, cliquez sur votre nom dans l’angle supérieur droit.
   - Cliquez sur **Paramètres** et notez la clé de création pour plus tard.
    
    ![Capture d’écran de la clé de création LUIS](./media/enterprise-template/luis_authoring_key.jpg)

1. Ouvrez une invite de commandes.
1. Connectez-vous à votre compte Azure à l’aide de l’interface Azure CLI. La liste des abonnements auxquels vous avez accès est disponible dans la page [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) du portail Azure.
    ```shell
    az login
    az account set --subscription "YOUR_SUBSCRIPTION_NAME"
    ```

1. Exécutez la commande « msbot clone services » pour déployer vos services et configurer un fichier .bot dans votre projet. **REMARQUE : À la fin du déploiement, n’oubliez pas de noter le secret du fichier .bot affiché dans la fenêtre d’invite de commandes, car vous en aurez besoin plus tard.**

    ```shell
    msbot clone services --name "YOUR-BOT-NAME" --luisAuthoringKey "YOUR_AUTHORING_KEY" --folder "DeploymentScripts\LOCALE_FOLDER" --location "REGION"
    ```

    > **Remarques**:
    >- Le paramètre **YOUR-BOT-NAME** doit être un identificateur **global unique** et il peut contenir uniquement des lettres minuscules, des chiffres et des tirets (« - »).
    >- La région de déploiement que vous spécifiez à cette étape doit correspondre à la région du portail associé à votre clé de création LUIS (par exemple, westus pour luis.ai ou westeurope pour eu.luis.ai).
    >- Certains utilisateurs peuvent rencontrer un problème connu avec la configuration de l’ID d’application et du mot de passe pour un compte de service administré. Si vous recevez cette erreur `ERROR: Unable to provision MSA id automatically. Please pass them in as parameters and try again`, accédez à [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) et créez manuellement une application, en veillant à noter l’ID de l’application et le mot de passe/secret associé. Exécutez une nouvelle fois la commande « msbot clone services » ci-dessus, en indiquant les deux nouveaux arguments `--appId` et `--appSecret` avec les valeurs que vous venez de récupérer. Si le mot de passe contient des caractères spéciaux susceptibles d’être interprétés comme une commande par le shell, placez ces caractères dans une séquence d’échappement :
    >   - Pour l’*invite de commandes Windows*, entourez appSecret de guillemets doubles. Exemple : `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --appSecret "YOUR_APP_SECRET"`
    >   - Pour *Windows PowerShell, essayez de passer dans appSecret après l’argument --%. Exemple : `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --% --appSecret "YOUR_APP_SECRET"`
    >   - Pour *Mac OS ou Linux*, entourez appSecret de guillemets simples. Exemple : `msbot clone services --name xxxx --luisAuthoringKey xxxx --location xxxx --folder bot.recipe --appSecret 'YOUR_APP_SECRET'`

1. Une fois le déploiement terminé, mettez à jour le secret de votre fichier .bot dans le fichier **appsettings.json**. 
    
    ```
    "botFilePath": "./YOUR_BOT_FILE.bot",
    "botFileSecret": "YOUR_BOT_SECRET",
    ```
1. Exécutez la commande suivante et récupérez la clé d’instrumentation pour votre instance Application Insights.
    ```
    msbot list --bot YOUR_BOT_FILE.bot --secret "YOUR_BOT_SECRET"
    ```

1. Mettez à jour la clé d’instrumentation dans le fichier **appsettings.json** :

    ```
    "ApplicationInsights": {
        "InstrumentationKey": "YOUR_INSTRUMENTATION_KEY"
    }
    ```

## <a name="test-your-bot"></a>Tester votre robot

Quand vous avez terminé, exécutez votre projet de bot dans votre environnement de développement et ouvrez l’émulateur **Bot Framework Emulator**. Dans l’émulateur, cliquez sur **Fichier > Ouvrir le bot** et accédez au fichier .bot dans votre répertoire.

![Capture d’écran du point de terminaison de développement de l’émulateur](./media/enterprise-template/development_endpoint.jpg)

Vous recevez normalement un message de présentation au début de la conversation.

Tapez ```hi``` pour vérifier que tout fonctionne correctement.

## <a name="publish-your-bot"></a>Publier votre bot

Le test peut être effectué localement de bout en bout. Lorsque vous êtes prêt à déployer votre bot dans Azure en vue d’effectuer des tests supplémentaires, utilisez la commande suivante pour publier le code source :

```shell
az bot publish -g YOUR-BOT-NAME -n YOUR-BOT-NAME --proj-name YOUR-BOT-NAME.csproj --version v4
```

## <a name="view-your-bot-analytics"></a>Afficher des données analytiques relatives à votre bot
Enterprise Bot Template est fourni avec un tableau de bord Power BI préconfiguré qui se connecte au service Application Insights afin de collecter des données analytiques sur les conversations. Une fois que vous avez testé votre bot localement, vous pouvez ouvrir ce tableau de bord pour consulter ces données. 

1. Téléchargez le tableau de bord Power BI (fichier .pbit) [ici](https://github.com/Microsoft/AI/blob/master/solutions/analytics/ConversationalAnalyticsSample_02132019.pbit).
1. Ouvrez le tableau de bord dans [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Entrez votre ID d’application Application Insights (vous le trouverez dans votre fichier .bot).

    ![Ligne de l’ID d’application AppInsights dans le fichier .bot](./media/enterprise-template/appInsights_appId.jpg)

1. Cliquez [ici](https://github.com/Microsoft/AI/tree/master/solutions/analytics) pour en savoir plus sur les fonctionnalités de Power BI Dashboard.

# <a name="next-steps"></a>Étapes suivantes

Après avoir déployé votre bot préconfiguré, vous pouvez le personnaliser en fonction de votre scénario et de vos besoins. Poursuivre avec la [personnalisation du bot](bot-builder-enterprise-template-customize.md)
