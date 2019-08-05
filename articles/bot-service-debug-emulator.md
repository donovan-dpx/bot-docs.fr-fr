---
title: Tester et déboguer des robots à l’aide de l’émulateur Bot Framework | Microsoft Docs
description: Découvrez comment inspecter, tester et déboguer des robots à l’aide de l’application de bureau Émulateur Bot Framework.
keywords: transcription, outil msbot, services linguistiques, reconnaissance vocale
author: DeniseMak
ms.author: v-demak
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/26/2019
ms.openlocfilehash: 847ae51791ae66ef190ebefee765f2806ec91c5e
ms.sourcegitcommit: 23a1808e18176f1704f2f6f2763ace872b1388ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68484037"
---
# <a name="debug-with-the-emulator"></a>Déboguer avec l’émulateur

L’émulateur Bot Framework est une application de bureau qui permet aux développeurs de robots de tester et déboguer leurs créations localement ou à distance. L’émulateur vous permet de converser avec le robot et d’inspecter les messages qu’il envoie et reçoit. L’émulateur affiche les messages tels qu’ils apparaîtraient dans une interface utilisateur de conversation web, et journalise les requêtes et réponses JSON à mesure que vous échangez des messages avec le robot. Avant de déployer votre robot dans le cloud, exécutez-le et testez-le localement à l’aide de l’émulateur. Vous pouvez tester votre bot à l’aide de l’émulateur, même si vous ne l’avez pas encore [créé](./bot-service-quickstart.md) avec Azure Bot Service ou configuré pour s’exécuter sur des canaux.

## <a name="prerequisites"></a>Prérequis
- Installez le [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).

## <a name="run-a-bot-locally"></a>Exécuter un robot localement
Avant de connecter votre robot au Bot Framework Emulator, vous devez exécuter votre robot localement. Vous pouvez utiliser Visual Studio ou Visual Studio Code pour exécuter votre robot ou utiliser la ligne de commande. Pour exécuter un robot à l’aide de la ligne de commande, procédez comme suit :


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* Allez à l’invite de commande et remplacez le répertoire par le répertoire de votre projet de robot.
* Démarrez le robot en exécutant la commande suivante : 
    ```
    dotnet run
    ```
* Copiez le numéro de port dans la ligne avant *L’application a démarré. Appuyez sur Ctrl+C pour arrêter.*

    ![Numéro de port C#](media/bot-service-debug-emulator/csharp_port_number.png)


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

* Allez à l’invite de commande et remplacez le répertoire par le répertoire de votre projet de robot.
* Démarrez le robot en exécutant la commande suivante :
    ```
    node index.js
    ```
* Copiez le numéro de port sur lequel Restify écoute.

    ![Numéro de port JS](media/bot-service-debug-emulator/js_port_number.png)
---

À ce stade, votre robot s’exécute localement. 


## <a name="connect-to-a-bot-running-on-localhost"></a>Se connecter à un bot s’exécutant sur un hôte local

![Interface utilisateur de l’émulateur](media/emulator-v4/emulator-welcome.png)

Pour vous connecter à un robot s’exécutant localement, cliquez sur **Ouvrir le robot**. Ajoutez le numéro de port que vous avez copié précédemment dans l’URL suivante et collez l’URL mise à jour dans la barre d’URL du robot :

*http://localhost:**numéro de port**/api/messages*

![Interface utilisateur de l’émulateur](media/bot-service-debug-emulator/open_bot_emulator.png)

Si votre bot s’exécute avec les [informations d’identification de compte Microsoft (MSA)](#use-bot-credentials), entrez également ces informations d’identification.


### <a name="use-bot-credentials"></a>Utiliser les informations d’identification du bot

Lorsque vous ouvrez le bot, définissez l’**ID d’application Microsoft** et le **mot de passe d’application Microsoft** si votre bot s’exécute avec des informations d’identification. Si vous avez créé votre bot avec Azure Bot Service, les informations d’identification sont disponibles sur l’App Service du bot, sous la section **Paramètres -> Configuration**. Si vous ne connaissez pas les valeurs, vous pouvez les supprimer du fichier de configuration du bot exécuté localement, puis exécuter le bot dans l’émulateur. Si le bot n’est pas en cours d’exécution avec ces paramètres, vous n’avez pas besoin d’exécuter l’émulateur avec les paramètres. 

## <a name="view-detailed-message-activity-with-the-inspector"></a>Afficher l’activité de message détaillée avec l’inspecteur

Envoyez un message à votre robot et ce dernier doit répondre. Vous pouvez cliquer sur la bulle du message dans la fenêtre de conversation et inspecter l’activité JSON brute à l’aide de la fonctionnalité **INSPECTOR** à droite de la fenêtre. Lorsque cette option est sélectionnée, la bulle de message devient jaune et l’objet JSON d’activité s’affiche à gauche de la fenêtre de conversation. Les informations JSON incluent des métadonnées clés, dont l’ID de canal, le type d’activité, l’ID de conversation, le SMS, l’URL du point de terminaison, etc. Vous pouvez inspecter les activités envoyées par l’utilisateur, ainsi que les activités avec lesquelles le bot répond.

![Émulateur d’activité de message](media/emulator-v4/emulator-view-message-activity-03.png)

> [!TIP]
> Vous pouvez déboguer des modifications d’État dans un robot connecté à un canal en ajoutant un [Intergiciel d’inspection](bot-service-debug-inspection-middleware.md) au robot.

<!--
## Save and load conversations with bot transcripts

Activities in the emulator can be saved as transcripts. From an open live chat window, select **Save Transcript As** to the transcript file. The **Start Over** button can be used any time to clear a conversation and restart a connection to the bot.  

![Emulator save transcripts](media/emulator-v4/emulator-save-transcript.png)

To load transcripts, simply select **File > Open Transcript File** and select the transcript. A new Transcript window will open and render the message activity to the output window. 

![Emulator load transcripts](media/emulator-v4/emulator-load-transcript.png)
--->
<!---
## Add services 

You can easily add a LUIS app, QnA knowledge base, or dispatch model to your bot directly from the emulator. When the bot is loaded, select the services button on the far left of the emulator window. You will see options under the **Services** menu to add LUIS, QnA Maker, and Dispatch. 

To add a service app, simply click on the **+** button and select the service you want to add. You will be prompted to sign in to the Azure portal to add the service to the bot file, and connect the service to your bot application. 

> [!IMPORTANT]
> Adding services only works if you're using a `.bot` configuration file. Services will need to be added independently. For details on that, see [Manage bot resources](v4sdk/bot-file-basics.md) or the individual how to articles for the service you're trying to add.
>
> If you are not using a `.bot` file, the left pane won't have your services listed (even if your bot uses services) and will display *Services not available*.

![LUIS connect](media/emulator-v4/emulator-connect-luis-btn.png)

When either service is connected, you can go back to a live chat window and verify that your services are connected and working. 

![QnA connected](media/emulator-v4/emulator-view-message-activity.png)

--->

## <a name="inspect-services"></a>Inspecter des services

Avec le nouvel émulateur v4, vous pouvez également inspecter les réponses JSON à partir de LUIS et QnA. À l’aide d’un robot avec un service de langage connecté, vous pouvez sélectionner **trace** dans la fenêtre LOG (JOURNAL) en bas à droite. Ce nouvel outil offre également des fonctionnalités pour mettre à jour vos services de langage directement à partir de l’émulateur. 

![Inspecteur LUIS](media/emulator-v4/emulator-luis-inspector.png)

Avec un service LUIS connecté, vous remarquerez que le lien de trace spécifie **Luis Trace** (Trace LUIS). En suivant ce lien, vous verrez la réponse brute de votre service LUIS, qui inclut des intentions, des entités avec leurs scores spécifiés. Vous avez également la possibilité de réaffecter des intentions pour les énoncés de votre utilisateur. 

![Inspecteur de QnA](media/emulator-v4/emulator-qna-inspector.png)

Avec un service de QnA connecté, le journal affichera l’option **QnA Trace** (Trace QnA) qui permet de voir un aperçu de la paire question-réponse associée à cette activité, ainsi qu’un score de confiance. À partir de là, vous pouvez ajouter une autre formulation de question pour une réponse.

<!--## Configure ngrok

If you are using Windows and you are running the Bot Framework Emulator behind a firewall or other network boundary and want to connect to a bot that is hosted remotely, you must install and configure **ngrok** tunneling software. The Bot Framework Emulator integrates tightly with ngrok tunnelling software (developed by [inconshreveable][inconshreveable]), and can launch it automatically when it is needed.

Open the **Emulator Settings**, enter the path to ngrok, select whether or not to bypass ngrok for local addresses, and click **Save**.

![ngrok path](media/emulator-v4/emulator-ngrok-path.png)
-->

<!---## Login to Azure

You can use Emulator to login in to your Azure account. This is particularly helpful for you to add and manage services your bot depends on. 
See [above](#add-services) to learn more about services you can manage using the Emulator.
-->

### <a name="login-to-azure"></a>Connexion à Azure
Vous pouvez utiliser l’émulateur pour vous connecter à votre compte Azure. Cela vous est particulièrement utile pour ajouter et gérer les services dont votre bot dépend. Connectez-vous à Azure avec les étapes suivantes :
- Cliquez sur Fichier -> Se connecter à Azure ![Connexion à Azure](media/emulator-v4/emulator-azure-login.png)
- Dans l’écran d’accueil, cliquez sur Vous connecter avec votre compte Microsoft Azure. Vous pouvez éventuellement indiquer à l’émulateur de maintenir votre connexion lors du redémarrage du programme d’émulation.
![Connexion à Azure](media/emulator-v4/emulator-azure-login-success.png)

## <a name="disabling-data-collection"></a>Désactivation de la collecte des données

Si vous ne voulez plus autoriser l’émulateur à collecter des données d’utilisation, vous pouvez facilement désactiver la collecte de données en effectuant les étapes suivantes :

1. Accédez à la page des paramètres de l’émulateur en cliquant sur le bouton Paramètres (icône d’engrenage) dans la barre de navigation sur le côté gauche.

    ![Désactivation de la collecte des données](media/emulator-v4/emulator-disable-data-1.png)

2. Décochez la case *Help improve the Emulator by allowing us to collect usage data* (Aidez-nous à améliorer l’émulateur en nous autorisant à collecter des données d’utilisation) sous la section **Data Collection** (Collecte des données).

    ![Désactivation de la collecte des données](media/emulator-v4/emulator-disable-data-2.png)

3. Cliquez sur le bouton « Save » (« Enregistrer »).

    ![Désactivation de la collecte des données](media/emulator-v4/emulator-disable-data-3.png)
    
Si vous changez d’avis, vous pouvez toujours l’activer en cochant de nouveau la case.

## <a name="additional-resources"></a>Ressources supplémentaires

L’émulateur Bot Framework est open source. Vous pouvez [contribuer][EmulatorGithubContribute] to the development and [submit bugs and suggestions][EmulatorGithubBugs].

Pour des informations de dépannage, consultez [Résoudre les problèmes généraux](bot-service-troubleshoot-bot-configuration.md) ainsi que les autres articles de résolution des problèmes de cette section.

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’intergiciel d’inspection pour déboguer un robot connecté à un canal.

> [!div class="nextstepaction"]
> [Déboguer votre bot à l’aide de fichiers de transcription](bot-service-debug-inspection-middleware.md)

<!--
Saving a conversation to a transcript file allows you to quickly draft and replay a certain set of interactions for debugging.

> [!div class="nextstepaction"]
> [Debug your bot using transcript files](~/v4sdk/bot-builder-debug-transcript.md)
-->

<!-- Footnote-style URLs -->

[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
