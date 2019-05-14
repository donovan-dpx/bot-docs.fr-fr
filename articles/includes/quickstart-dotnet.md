---
ms.openlocfilehash: 04f9101d0cf29618fb7d50e126c008190064a831
ms.sourcegitcommit: 3e3c9986b95532197e187b9cc562e6a1452cbd95
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199004"
---
## <a name="prerequisites"></a>Prérequis
- Visual Studio [2017 ou ultérieur](https://www.visualstudio.com/downloads)
- Modèle du kit SDK Bot Framework v4[ pour C#](https://aka.ms/bot-vsix)
- [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)
- Connaissances d’[ASP.Net Core](https://docs.microsoft.com/aspnet/core/) et de la [programmation asynchrone en C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/index)

## <a name="create-a-bot"></a>Créer un bot
Installez le modèle BotBuilderVSIX.vsix que vous avez téléchargé à la section des prérequis.

Dans Visual Studio, créez un projet de bot en utilisant le modèle **Echo Bot (Bot Framework v4)**.

![Projet Visual Studio](~/media/azure-bot-quickstarts/bot-builder-dotnet-project.png)

> [!TIP] 
> Si nécessaire, modifiez le type de build de projet en ``.Net Core 2.1``. De même, mettez éventuellement à jour les [packages NuGet](https://docs.microsoft.com/en-us/nuget/quickstart/install-and-use-a-package-in-visual-studio) `Microsoft.Bot.Builder`.

Grâce au modèle, votre projet contient tout le code nécessaire pour créer le bot dans ce guide de démarrage rapide. En fait, vous n’avez pas besoin d’écrire du code supplémentaire.

## <a name="start-your-bot-in-visual-studio"></a>Démarrer votre bot dans Visual Studio

Lorsque vous cliquez sur le bouton d’exécution, Visual Studio génère l’application, la déploie sur localhost et lance le navigateur web pour afficher la page `default.htm` de l’application. À ce stade, votre bot s’exécute localement.

## <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot

Démarrez à présent l’émulateur, puis connectez-vous à votre bot dans l’émulateur :

1. Cliquez sur le lien **Create a new bot configuration** (Créer une configuration de bot) sous l’onglet « Welcome » de l’émulateur. 
2. Renseignez les champs pour votre bot, puis cliquez sur **Enregistrer et se connecter**.

## <a name="interact-with-your-bot"></a>Interagir avec votre bot

Envoyez un message à votre bot, et le bot vous enverra un message à son tour.

![Émulateur en cours d’exécution](~/media/emulator-v4/emulator-running.png)

> [!NOTE]
> Si vous voyez que le message ne peut pas être envoyé, vous devrez peut-être redémarrer votre ordinateur, car ngrok n’a pas encore obtenu les privilèges nécessaires sur votre système (à n’effectuer qu’une seule fois).
