---
ms.openlocfilehash: 9c86001a51914f359163e7d755aa57e1c54127f8
ms.sourcegitcommit: dcacda776c927bcc7c76d00ff3cc6b00b062bd6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74414540"
---
## <a name="prerequisites"></a>Prérequis
- [Visual Studio 2017 ou version ultérieure](https://www.visualstudio.com/downloads)
- [Modèle du kit SDK Bot Framework v4 pour C#](https://aka.ms/bot-vsix)
- [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)
- Connaissances d’[ASP.Net Core](https://docs.microsoft.com/aspnet/core/) et de la [programmation asynchrone en C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Créer un bot
Installez le modèle [BotBuilderVSIX.vsix](https://aka.ms/bot-vsix) que vous avez téléchargé à la section des prérequis.

Dans Visual Studio, créez un projet de bot en utilisant le modèle **Echo Bot (Bot Framework v4)** . Entrez _bot framework v4_ dans la zone de recherche pour afficher uniquement les modèles de bot.

![Visual Studio crée un dialogue de nouveau projet.](../media/azure-bot-quickstarts/bot-builder-dotnet-project-vs2019.png)

> [!TIP] 
> Si vous utilisez Visual Studio 2017, assurez-vous que le type de build du projet est ``.Net Core 2.1`` ou une version ultérieure. De même, mettez éventuellement à jour les [packages NuGet](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) `Microsoft.Bot.Builder`.

Grâce au modèle, votre projet contient tout le code nécessaire pour créer le bot dans ce guide de démarrage rapide. En fait, vous n’avez pas besoin d’écrire du code supplémentaire.

## <a name="start-your-bot-in-visual-studio"></a>Démarrer votre bot dans Visual Studio

Lorsque vous cliquez sur le bouton d’exécution, Visual Studio génère l’application, la déploie sur localhost et lance le navigateur web pour afficher la page `default.htm` de l’application. À ce stade, votre bot s’exécute localement.

## <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot

Démarrez à présent l’émulateur, puis connectez-vous à votre bot dans l’émulateur :

1. Cliquez sur le lien **Create a new bot configuration** (Créer une configuration de bot) sous l’onglet « Welcome » de l’émulateur. 
2. Remplissez les champs de votre bot. Utilisez l’adresse de la page d’accueil de votre bot (généralement http://localhost:3978) ) et ajoutez les informations de routage « /api/messages » à cette adresse.
3. puis cliquez sur **Enregistrer et se connecter**.

## <a name="interact-with-your-bot"></a>Interagir avec votre bot

Envoyez un message à votre bot, et le bot vous enverra un message à son tour.

![Émulateur en cours d’exécution](~/media/emulator-v4/emulator-running.png)

> [!NOTE]
> Si vous voyez que le message ne peut pas être envoyé, vous devrez peut-être redémarrer votre ordinateur, car ngrok n’a pas encore obtenu les privilèges nécessaires sur votre système (à n’effectuer qu’une seule fois).
