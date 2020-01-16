---
title: Gérer un bot - Bot Service
description: Découvrez comment gérer un robot via le portail Service Bot.
keywords: portail azure, gestion de robot, test dans discussion web, MicrosoftAppID, MicrosoftAppPassword, paramètres ’application
author: v-ducvo
ms.author: rstand
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 8/13/2019
ms.openlocfilehash: 9a62a6d2bc1d47862d8ab25250dd3839d3e21d56
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75794154"
---
# <a name="manage-a-bot"></a>Gérer un robot

[!INCLUDE [applies-to-both](includes/applies-to-both.md)]

Cette rubrique explique comment gérer un robot à l’aide du portail Azure.

## <a name="bot-settings-overview"></a>Vue d’ensemble des paramètres du robot

![Vue d’ensemble des paramètres du robot](~/media/azure-manage-a-bot/overview.png)

Le panneau **Vue d’ensemble** présente des informations de haut niveau sur votre robot. Par exemple, vous pouvez y voir l’**ID d’abonnement**, le **niveau tarifaire** et le **point de terminaison de messagerie** de votre robot.

## <a name="bot-management"></a>Gestion du robot

 La plupart des options de gestion de votre robot figurent dans la section **BOT MANAGEMENT** (GESTION DU ROBOT). Voici la liste des options pour vous aider à gérer votre robot.

![Gestion du robot](~/media/azure-manage-a-bot/bot-management.png)

| Option |  Description |
| ---- | ---- |
| **Créer** | L’onglet Créer fournit des options pour apporter des modifications à votre robot. Cette option n’est pas disponible pour le **Bot d’inscription uniquement**. |
| **Tester dans la discussion web** | Utilisez le contrôle intégré Discussion Web pour vous aider à tester rapidement votre robot. |
| **Analyse** | Si l’analytique est activée pour votre robot, vous pouvez afficher les données d’analyse qu’Application Insights a collectées pour votre robot. |
| **Canaux** | Configurez les canaux que votre robot utilise pour communiquer avec les utilisateurs. |
| **Paramètres** | Gérer les divers paramètres de profil de robot, tels que le nom d’affichage, l’analytique et de point de terminaison de messagerie. |
| **Préparation vocale** | Gérer les connexions entre votre application LUIS et le service Reconnaissance vocale Bing... |
| **Tarification de Service Bot** | Gérer le niveau tarifaire pour le Service Bot. |

## <a name="app-service-settings"></a>Paramètres d’App Service

![Paramètres d’App Service](~/media/azure-manage-a-bot/app-service-settings.png)

Le panneau **Paramètres de l’application** contient des informations détaillées sur votre bot, comme l’environnement du bot, les paramètres de débogage et les clés des paramètres de l’application.

### <a name="microsoftappid-and-microsoftapppassword"></a>MicrosoftAppID et MicrosoftAppPassword

**MicrosoftAppID** et **MicrosoftAppPassword** sont conservés dans le fichier de paramètres du bot(`appsettings.json` ou `.env`) ou dans Azure Key Vault. Pour les récupérer, téléchargez le fichier de paramètres ou de configuration (pour les bots plus anciens, le cas échéant) de votre bot, ou accédez à Azure Key Vault. Vous pouvez être amené à effectuer des tests locaux avec l’ID et le mot de passe.

> [!NOTE]
> Le Service Bot **Enregistrement de canaux de robot** est fourni avec un *MicrosoftAppID* mais, aucun service d’application n’étant associé à ce type de service, il n’existe pas de panneau **Paramètres de l’application** dans lequel rechercher le *MicrosoftAppPassword*. Pour obtenir le mot de passe, vous devez en générer un. Pour générer le mot de passe pour une **Inscription de canaux de bot**, voir [Mot de passe d’inscription de canaux de robot](bot-service-quickstart-registration.md#get-registration-password).

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez exploré le panneau Service Bot dans le portail Azure, découvrez comment utiliser l’éditeur de code en ligne pour personnaliser votre robot.
> [!div class="nextstepaction"]
> [Utiliser l’éditeur de code en ligne](bot-service-build-online-code-editor.md)
