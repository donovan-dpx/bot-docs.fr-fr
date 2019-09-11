---
title: Déboguer un bot | Microsoft Docs
description: Découvrez comment déboguer un bot créé à l’aide de Bot Service.
author: v-ducvo
ms.author: kamrani
keywords: Kit SDK Bot Framework, déboguer un bot, tester un bot, émulateur de bot, émulateur
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/26/2019
ms.openlocfilehash: 27af606506eb19b98a327e276a00201ac417c7e4
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298185"
---
# <a name="debug-a-bot"></a>Déboguer un bot

Cet article explique comment déboguer votre bot à l’aide d’un environnement de développement intégré (IDE) tels que Visual Studio ou Visual Studio Code et l’émulateur Bot Framework. Vous pouvez utiliser ces méthodes pour déboguer n’importe quel bot localement, mais cet article utilise un [bot C#](~/dotnet/bot-builder-dotnet-sdk-quickstart.md) ou un [bot Javascript](~/javascript/bot-builder-javascript-quickstart.md) créé dans le démarrage rapide.

> [!NOTE]
> Dans cet article, nous utilisons Bot Framework Emulator pour envoyer et recevoir des messages à partir du robot durant le débogage. Si vous recherchez d’autres façons de déboguer votre robot à l’aide de Bot Framework Emulator, veuillez lire l’article [Déboguer avec Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

## <a name="prerequisites"></a>Prérequis 
- Téléchargez et installez l’[émulateur Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Téléchargez et installez [Visual Studio Code](https://code.visualstudio.com) ou [Visual Studio](https://www.visualstudio.com/downloads) (Community Edition ou version ultérieure).

<!-- ### Debug a JavaScript bot using command-line and emulator

To run a JavaScript bot using the command line and testing the bot with the emulator, do the following:
1. From the command line, change directory to your bot project directory.
1. Start the bot by running the command **node app.js**.
1. Start the emulator and connect to the bot's endpoint (e.g.: **http://localhost:3978/api/messages**). If this is the first time you are running 
the bot then click **File > New Bot** and follow the instructions on screen. Otherwise, click **File > Open Bot** to open an existing bot. 
Since this bot is running locally on your computer, you can leave the **MicrosoftAppId** and **MicrosoftAppPassword** fields blank. 
For more information, see [Debug with the Emulator](bot-service-debug-emulator.md).
1. From the emulator, send your bot a message (e.g.: send the message "Hi"). 
1. Use the **Inspector** and **Log** panels on the right side of the emulator window to debug your bot. For example, clicking on any of the messages bubble (e.g.: the "Hi" message bubble in the screenshot below) will show you the detail of that message in the **Inspector** panel. You can use it to view requests and responses as messages are exchanged between the emulator and the bot. Alternatively, you can click on any of the linked text in the **Log** panel to view the details in the **Inspector** panel.


   ![Inspector panel on the Emulator](~/media/bot-service-debug-bot/emulator_inspector.png) -->

## <a name="debug-a-javascript-bot-using-breakpoints-in-visual-studio-code"></a>Déboguer un bot JavaScript à l’aide de points d’arrêt dans Visual Studio Code

Dans Visual Studio Code, vous pouvez définir des points d’arrêt et exécuter le bot en mode débogage pour parcourir votre code. Pour définir des points d’arrêt dans VS Code, procédez comme suit :

1. Lancez VS Code et ouvrez le dossier de votre projet de bot.
2. Dans la barre de menus, cliquez sur **Déboguer**, puis sur **Démarrer le débogage**. Si vous êtes invité à sélectionner un moteur d’exécution pour exécuter votre code, sélectionnez **Node.js**. À ce stade, le bot s’exécute localement. 
3. Cliquez sur le fichier **.js** et définissez les points d’arrêt selon vos besoins. Dans VS Code, vous pouvez définir des points d’arrêt en plaçant votre souris sur la colonne à gauche des numéros de ligne. Un petit point rouge s’affiche. Si vous cliquez sur ce point, le point d’arrêt est défini. Si vous cliquez à nouveau sur le point, le point d’arrêt est supprimé.
   ![Définir un point d’arrêt dans VS Code](~/media/bot-service-debug-bot/breakpoint-set.png)
4. Démarrez Bot Framework Emulator et connectez-vous à votre robot, comme décrit dans l’article [Déboguer avec Bot Framework Emulator](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 
5. À partir de l’émulateur, envoyez un message à votre robot (par exemple, envoyer le message « Salut »). L’exécution s’arrêtera à la ligne où vous placez le point d’arrêt.
   ![Déboguer dans VS Code](~/media/bot-service-debug-bot/breakpoint-caught.png)

## <a name="debug-a-c-bot-using-breakpoints-in-visual-studio"></a>Déboguer un bot C# à l’aide de points d’arrêt dans Visual Studio

Dans Visual Studio (VS), vous pouvez définir des points d’arrêt et exécuter le bot en mode débogage pour parcourir votre code. Pour définir des points d’arrêt dans VS, procédez comme suit :

1. Accédez au dossier de votre bot et ouvrez le fichier **.sln**. Cela permet d’ouvrir la solution dans Visual Studio.
2. Dans la barre de menus, cliquez sur **Générer** puis sur **Générer la solution**.
3. Dans l’**Explorateur de solutions**, cliquez sur le fichier **.cs** et définissez les points d’arrêt selon vos besoins. Ce fichier définit la logique principale de votre bot. Dans VS, vous pouvez définir des points d’arrêt en plaçant votre souris sur la colonne à gauche des numéros de ligne. Un petit point rouge s’affiche. Si vous cliquez sur le point, le point d’arrêt est défini. Si vous cliquez à nouveau sur le point, le point d’arrêt est supprimé.
4. Dans le menu, cliquez sur **Déboguer**, puis sur **Démarrer le débogage**. À ce stade, le bot s’exécute localement. 

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->
   ![Définir le point d’arrêt dans Visual Studio](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

5. Démarrez l’émulateur Bot Framework et connectez-vous à votre bot, comme décrit dans la section ci-dessus. 
6. À partir de l’émulateur, envoyez un message à votre bot (par exemple : le message « Salut »). L’exécution s’arrêtera à la ligne où vous placez le point d’arrêt.
   ![Déboguer dans VS](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)
::: moniker range="azure-bot-service-3.0" 

## <a id="debug-csharp-serverless"></a> Déboguer un bot Plan consommation C\# Functions

L’environnement de plan consommation sans serveur C\# de Bot Service partage plus d’éléments avec Node.js qu’une application C\# standard, car il nécessite un hôte de runtime, comme le moteur du nœud. Dans Azure, le runtime fait partie de l’environnement d’hébergement dans le cloud, mais vous devez répliquer cet environnement localement sur votre bureau. 

### <a name="prerequisites"></a>Prérequis

Avant de pouvoir déboguer votre bot C# Plan de consommation, vous devez effectuer ces tâches.

- Téléchargez le code source de votre bot (à partir d’Azure), comme décrit dans [Configurer un déploiement continu](bot-service-continuous-deployment.md).
- Téléchargez et installez l’[émulateur Bot Framework](https://aka.ms/Emulator-wiki-getting-started).
- Installez l’<a href="https://www.npmjs.com/package/azure-functions-cli" target="_blank">interface de ligne de commande Azure Functions</a>.
- Installez l’<a href="https://github.com/dotnet/cli" target="_blank">interface de ligne de commande DotNet</a>.
  
Si vous souhaitez être en mesure de déboguer votre code à l’aide de points d’arrêt dans Visual Studio 2017, vous devez également effectuer ces tâches.
  
- Téléchargez et installez <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition ou ultérieure).
- Téléchargez et installez l’<a href="https://visualstudiogallery.msdn.microsoft.com/e6bf6a3d-7411-4494-8a1e-28c1a8c4ce99" target="_blank">extension Visual Studio Command Task Runner</a>.

> [!NOTE]
> Visual Studio Code n’est pas pris en charge pour le moment.

### <a name="debug-a-consumption-plan-c-functions-bot-using-the-emulator"></a>Déboguer un bot Plan consommation C# Functions à l’aide de l’émulateur

La façon la plus simple de déboguer votre bot localement consiste à démarrer le bot puis à vous y connecter à partir de l’émulateur Bot Framework. 
Tout d’abord, ouvrez une invite de commandes et accédez au dossier où se trouve le fichier **project.json** dans votre référentiel. Exécutez ensuite la commande `dotnet restore` pour restaurer les différents packages référencés dans votre bot.

> [!NOTE]
> Visual Studio 2017 modifie la façon dont Visual Studio gère les dépendances. Bien que Visual Studio 2015 utilise **project.json** pour gérer les dépendances, Visual Studio 2017 utilise un modèle **.csproj** lors du chargement dans Visual Studio. Si vous utilisez Visual Studio 2017, <a href="https://aka.ms/bf-debug-project">téléchargez ce fichier **.csproj**</a> dans le dossier **/messages** de votre référentiel avant d’exécuter la commande `dotnet restore`.

![Invite de commande](~/media/bot-service-debug-bot/csharp-azureservice-debug-envconfig.png)

Exécutez maintenant `debughost.cmd` pour charger et démarrer votre bot. 

![Invite de commandes run debughost.cmd](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughost.png)

À ce stade, le bot s’exécute localement. À partir de la fenêtre de console, copiez le point de terminaison sur lequel debughost est à l’écoute (dans cet exemple, `http://localhost:3978`). Ensuite, démarrez l’émulateur Bot Framework et collez le point de terminaison dans la barre d’adresse de l’émulateur. Pour cet exemple, vous devez également ajouter `/api/messages` au point de terminaison. Puisque vous n’avez pas besoin de sécurité pour le débogage local, vous pouvez laisser vides les champs **ID d’application Microsoft** et **Mot de passe d’application Microsoft**. Cliquez sur **CONNECT** (Connexion) pour établir une connexion à votre bot à l’aide du point de terminaison spécifié.

![Configurer l’émulateur](~/media/bot-service-debug-bot/mac-azureservice-emulator-config.png)

Une fois que vous avez connecté l’émulateur à votre bot, envoyez un message à votre bot en tapant un texte dans la zone de texte en bas de la fenêtre de l’émulateur (où **Tapez votre message...**  s’affiche dans le coin inférieur gauche). Dans les panneaux **Log** et **Inspector** sur le côté droit de la fenêtre de l’émulateur, vous pouvez visualiser les demandes et les réponses à mesure que des messages sont échangés entre l’émulateur et le bot.

![tester via l’émulateur](~/media/bot-service-debug-bot/mac-azureservice-debug-emulator.png)

En outre, vous pouvez afficher les détails du journal dans la fenêtre de console.

![Fenêtre de console](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughostlogging.png)

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

- Consultez [Résoudre les problèmes généraux](bot-service-troubleshoot-bot-configuration.md) ainsi que les autres articles de résolution des problèmes de cette section.
- Consultez le guide pratique pour [déboguer avec l’émulateur](bot-service-debug-emulator.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déboguer votre bot à l’aide de fichiers de transcription](v4sdk/bot-builder-debug-transcript.md).
