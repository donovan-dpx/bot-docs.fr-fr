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
ms.openlocfilehash: 307a6bf697e274391336a0d216c64da85232616d
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033268"
---
# <a name="debug-with-the-emulator"></a>Déboguer avec l’émulateur

L’émulateur Bot Framework est une application de bureau qui permet aux développeurs de robots de tester et déboguer leurs créations localement ou à distance. L’émulateur vous permet de converser avec le robot et d’inspecter les messages qu’il envoie et reçoit. L’émulateur affiche les messages tels qu’ils apparaîtraient dans une interface utilisateur de conversation web, et journalise les requêtes et réponses JSON à mesure que vous échangez des messages avec le robot. Avant de déployer votre robot dans le cloud, exécutez-le et testez-le localement à l’aide de l’émulateur. Vous pouvez tester votre bot à l’aide de l’émulateur, même si vous ne l’avez pas encore [créé](./bot-service-quickstart.md) avec Azure Bot Service ou configuré pour s’exécuter sur des canaux.

## <a name="prerequisites"></a>Prérequis
- Installer [l’émulateur](https://aka.ms/Emulator-wiki-getting-started)

## <a name="connect-to-a-bot-running-on-localhost"></a>Se connecter à un bot s’exécutant sur un hôte local

![Interface utilisateur de l’émulateur](media/emulator-v4/emulator-welcome.png)

Pour vous connecter à un bot qui s’exécute localement, cliquez sur **Open bot** ou sélectionnez votre fichier de configuration préconfiguré (fichier .bot). Vous n’avez pas besoin d’un fichier de configuration pour vous connecter à votre bot, mais l’émulateur en utilise quand même un si votre bot en a un. Si votre bot s’exécute avec les informations d’identification de compte Microsoft (MSA), entrez également ces informations d’identification.

![Interface utilisateur de l’émulateur](media/emulator-v4/emulator-open-bot.png)

## <a name="view-detailed-message-activity-with-the-inspector"></a>Afficher l’activité de message détaillée avec l’inspecteur

Envoyez un message à votre bot, et ce dernier doit normalement vous répondre. Vous pouvez cliquer sur la bulle de message dans la fenêtre de conversation et inspecter l’activité JSON brute à l’aide de la fonctionnalité **INSPECTOR** (INSPECTEUR) à droite de la fenêtre. Lorsque cette option est sélectionnée, la bulle de message devient jaune et l’objet JSON d’activité s’affiche à gauche de la fenêtre de conversation. Ces informations JSON incluent des métadonnées clés, dont l’ID de canal, le type d’activité, l’ID de conversation, le message au format texte, l’URL du point de terminaison, etc. Vous pouvez inspecter les activités envoyées par l’utilisateur, ainsi que les activités avec lesquelles le bot répond. 

![Émulateur d’activité de message](media/emulator-v4/emulator-view-message-activity-03.png)

## <a name="save-and-load-conversations-with-bot-transcripts"></a>Enregistrer et charger des conversations avec des transcriptions de robot

Les activités dans l’émulateur peuvent être enregistrées sous la forme de transcriptions. Dans une fenêtre de conversation en direct ouverte, sélectionnez **Save Transcript As** (Enregistrer la transcription sous) dans le fichier de transcription. Vous pouvez utiliser le bouton **Start Over** (Recommencer) à tout moment pour effacer une conversation et rétablir une connexion au robot.  

![L’émulateur enregistre les transcriptions](media/emulator-v4/emulator-save-transcript.png)

Pour charger les transcriptions, sélectionnez simplement **Fichier > Open Transcript File** (Ouvrir un fichier de transcription), puis sélectionnez la transcription. Une nouvelle fenêtre de transcription s’ouvre et affiche l’activité de message dans la fenêtre de sortie. 

![L’émulateur charge les transcriptions](media/emulator-v4/emulator-load-transcript.png)

## <a name="add-services"></a>Ajouter des services 

Vous pouvez facilement ajouter une application LUIS, une base de connaissances QnA ou un modèle dispatch à votre bot directement à partir de l’émulateur. Lorsque le bot est chargé, sélectionnez le bouton des services tout à gauche de la fenêtre de l’émulateur. Vous pouvez voir des options sous le menu **Services** pour ajouter LUIS, QnA Maker et Dispatch. 

Pour ajouter une application de service, cliquez simplement sur le bouton **+** et sélectionnez le service que vous souhaitez ajouter. Vous êtes invité à vous connecter au Portail Azure pour ajouter le service au fichier bot et à connecter le service à votre application de bot. 

> [!IMPORTANT]
> L’ajout de services fonctionne seulement si vous utilisez un fichier de configuration `.bot`. Les services doivent être ajoutés de manière indépendante. Pour plus d’informations, consultez [Gérer les ressources du bot](v4sdk/bot-file-basics.md) ou les guides pratiques individuels du service que vous essayez d’ajouter.
>
> Si vous n’utilisez pas de fichier `.bot`, le volet gauche ne liste pas vos services (même si votre bot utilise des services) et affiche *Services not available* (Services non disponibles).

![Connexion à LUIS](media/emulator-v4/emulator-connect-luis-btn.png)

Quand un des services est connecté, vous pouvez revenir à une fenêtre de conversation en direct pour vérifier que vos services sont connectés et opérationnels. 

![QnA connecté](media/emulator-v4/emulator-view-message-activity.png)

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

## <a name="login-to-azure"></a>Connexion à Azure

Vous pouvez utiliser l’émulateur pour vous connecter à votre compte Azure. Cela vous est particulièrement utile pour ajouter et gérer les services dont votre bot dépend. Reportez-vous [ci-dessus](#add-services) pour en savoir plus sur les services que vous pouvez gérer à l’aide de l’émulateur.

### <a name="to-login"></a>Pour vous connecter

![Connexion Azure](media/emulator-v4/emulator-azure-login.png)

Pour vous connecter
- Vous pouvez cliquer sur File -> Sign in with Azure
- Dans l’écran d’accueil, cliquez sur Vous connecter avec votre compte Microsoft Azure. Vous pouvez éventuellement indiquer à l’émulateur de maintenir votre connexion lors du redémarrage du programme d’émulation.

![Connexion Azure](media/emulator-v4/emulator-azure-login-success.png)

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

L’émulateur Bot Framework est open source. Vous pouvez [contribuer][EmulatorGithubContribute] au développement en [envoyant des bogues et suggestions][EmulatorGithubBugs].

Pour des informations de dépannage, consultez [Résoudre les problèmes généraux](bot-service-troubleshoot-bot-configuration.md) ainsi que les autres articles de résolution des problèmes de cette section.

## <a name="next-steps"></a>Étapes suivantes

L’enregistrement d’une conversation dans un fichier de transcription vous permet de créer rapidement un brouillon et de relire un certain ensemble d’interactions à des fins de débogage.

> [!div class="nextstepaction"]
> [Déboguer votre bot à l’aide de fichiers de transcription](~/v4sdk/bot-builder-debug-transcript.md)

<!-- Footnote-style URLs -->

[EmulatorGithubContribute]: https://github.com/Microsoft/BotFramework-Emulator/wiki/How-to-Contribute
[EmulatorGithubBugs]: https://github.com/Microsoft/BotFramework-Emulator/wiki/Submitting-Bugs-%26-Suggestions

[ngrokDownload]: https://ngrok.com/
[inconshreveable]: https://inconshreveable.com/
