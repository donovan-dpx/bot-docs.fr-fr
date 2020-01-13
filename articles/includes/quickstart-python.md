---
ms.openlocfilehash: e2a43cf5880da0036415d80d19e59db4c84d2f73
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491749"
---
## <a name="prerequisites"></a>Conditions préalables requises
- Python [3.6](https://www.python.org/downloads/release/python-369/) ou [3.7](https://www.python.org/downloads/release/python-375/)
- [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)
- [git](https://git-scm.com/)
- connaissances relatives à la programmation asynchrone en Python

## <a name="create-a-bot"></a>Créer un bot
1. Ouvrez un terminal, puis accédez au dossier dans lequel vous enregistrez votre bot localement. Installez les packages nécessaires en exécutant les commandes suivantes :
- `pip install botbuilder-core`
- `pip install asyncio`
- `pip install -r requirements.txt`
- `pip install cookiecutter`

Le dernier package, Cookiecutter, est utilisé pour générer votre bot. Vérifiez que Cookiecutter a été installé correctement en exécutant `cookiecutter --help`.

2. Pour créer votre bot :

```cmd
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Cette commande crée un bot d’écho basé sur le [modèle Echo](https://github.com/microsoft/BotBuilder-Samples/tree/master/generators/python/app/templates/echo/%7B%7Bcookiecutter.bot_name%7D%7D) en Python.

3. Vous êtes ensuite invité à entrer le *nom* du bot et une *description*. Nommez votre bot `echo-bot`, puis indiquez la description `A bot that echoes back user response.` comme indiqué ci-dessous :

![définir le nom et la description](~/media/python/quickstart/set-name-description.png)

Copiez les quatre derniers chiffres de l’adresse sur la dernière ligne (en règle générale 3978), car vous allez les utiliser à l’étape suivante. Vous êtes maintenant prêt à démarrer votre bot.

## <a name="start-you-bot"></a>Démarrer votre bot
1. Depuis un terminal, accédez au dossier `echo-bot` dans lequel vous avez enregistré votre bot. Exécutez `pip install -r requirements.txt` pour installer les packages nécessaires à l’exécution de votre bot.

2. Une fois les packages installés, exécutez `python app.py` pour démarrer votre bot. Vous savez que votre bot est prêt à être testé quand vous voyez s’afficher la dernière ligne indiquée dans la capture d’écran ci-dessous :

![bot s’exécutant localement](~/media/python/quickstart/bot-running-locally.png)
<!---
Alternatively, you can set the file in an environment variable with set `FLASK_APP=app.py` in Windows and `export FLASK_APP=app.py` in Mac OS/Linux and then run `flask run --host=127.0.0.1 --port=3978`.-->

## <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot
1. Démarrez l’émulateur, puis cliquez sur le bouton **Ouvrir le bot**.

2. Une fois que vous avez cliqué sur le bouton, une fenêtre s’affiche pour vous permettre de définir les valeurs nécessaires à l’exécution du bot. Utilisez le numéro que vous avez enregistré précédemment, puis affectez à l’**URL du bot** la valeur `http://localhost:<saved number>/api/messages` comme indiqué ci-dessous :

![écran d’ouverture de bot](~/media/python/quickstart/open-bot.png)

3. Cliquez sur le bouton **Connecter**. Votre bot doit démarrer. Essayez de tester le bot en tapant quelque chose, puis cliquez sur *Entrée* comme indiqué ci-dessous :

![connecter et tester](~/media/python/quickstart/connect-and-start.png)
