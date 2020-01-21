---
title: Créer un bot à l’aide du kit SDK Bot Framework pour Python | Microsoft Docs
description: Créez rapidement un bot à l’aide du kit SDK Bot Framework pour Python.
keywords: démarrage rapide, kit sdk bot framework, bien démarrer
author: emgrol
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3df1e44b5643f0dae98e659d76b8e30224081782
ms.sourcegitcommit: 86495b597e55c94309a0c73fc1945a3393ddcbbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75756375"
---
# <a name="create-a-bot-with-the-bot-framework-sdk-for-python"></a>Créer un bot à l’aide du kit SDK Bot Framework pour Python

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Ce guide de démarrage rapide vous oriente tout au long de la création d’un bot à l’aide du modèle de bot d’écho en Python, puis vous aide à le tester avec Bot Framework Emulator.

## <a name="prerequisites"></a>Conditions préalables requises
- Python [3.6](https://www.python.org/downloads/release/python-369/) ou [3.7](https://www.python.org/downloads/release/python-375/)
- [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)
- [git](https://git-scm.com/)
- connaissances relatives à la programmation asynchrone en Python

## <a name="create-a-bot"></a>Créer un bot
1. Ouvrez un terminal, puis accédez au dossier dans lequel vous enregistrez votre bot localement. Installez les packages nécessaires en exécutant les commandes suivantes :
- `pip install botbuilder-core`
- `pip install asyncio`
- `pip install cookiecutter`

Le dernier package, Cookiecutter, est utilisé pour générer votre bot. Vérifiez que Cookiecutter a été installé correctement en exécutant `cookiecutter --help`.

2. Pour créer votre bot :

```cmd
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Cette commande crée un bot d’écho basé sur le [modèle Echo](https://github.com/microsoft/botbuilder-python/tree/master/generators/app/templates/echo) en Python.

3. Vous êtes ensuite invité à entrer le *nom* du bot et une *description*. Nommez votre bot `echo-bot`, puis indiquez la description `A bot that echoes back user response.` comme indiqué ci-dessous :

![définir le nom et la description](../media/python/quickstart/set-name-description.png)

Copiez les quatre derniers chiffres de l’adresse sur la dernière ligne (en règle générale 3978), car vous allez les utiliser à l’étape suivante. Vous êtes maintenant prêt à démarrer votre bot.

## <a name="start-you-bot"></a>Démarrer votre bot
1. Depuis un terminal, accédez au dossier `echo-bot` dans lequel vous avez enregistré votre bot. Exécutez `pip install -r requirements.txt` pour installer les packages nécessaires à l’exécution de votre bot.

2. Une fois les packages installés, exécutez `python app.py` pour démarrer votre bot. Vous savez que votre bot est prêt à être testé quand vous voyez s’afficher la dernière ligne indiquée dans la capture d’écran ci-dessous :

![bot s’exécutant localement](../media/python/quickstart/bot-running-locally.png)

## <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot
1. Démarrez l’émulateur, puis cliquez sur le bouton **Ouvrir le bot**.

2. Une fois que vous avez cliqué sur le bouton, une fenêtre s’affiche pour vous permettre de définir les valeurs nécessaires à l’exécution du bot. Utilisez le numéro que vous avez enregistré précédemment, puis affectez à l’**URL du bot** la valeur `http://localhost:<saved number>/api/messages` comme indiqué ci-dessous :

![écran d’ouverture de bot](../media/python/quickstart/open-bot.png)

3. Cliquez sur le bouton **Connecter**. Votre bot doit démarrer. Essayez de tester le bot en tapant quelque chose, puis cliquez sur *Entrée* comme indiqué ci-dessous :

![connecter et tester](../media/python/quickstart/connect-and-start.png)

## <a name="additional-resources"></a>Ressources supplémentaires
Pour savoir comment se connecter à un bot hébergé à distance, consultez [tunneling (ngrok)](https://github.com/Microsoft/BotFramework-Emulator/wiki/Tunneling-(ngrok)).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer votre bot sur Azure](../bot-builder-deploy-az-cli.md)

