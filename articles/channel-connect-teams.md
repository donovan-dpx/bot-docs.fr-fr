---
title: Connecter un bot à Teams | Microsoft Docs
description: Découvrez comment configurer un bot pour qu’il soit accessible via Teams.
keywords: Teams, canal de bot, configurer Teams
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/26/2019
ms.openlocfilehash: 1ba0873c880bfb9b1e0f449039e98859e5571028
ms.sourcegitcommit: 0b647dc6716b0c06f04ee22ebdd7b53039c2784a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076532"
---
# <a name="connect-a-bot-to-teams"></a>Connecter un bot à Teams

Pour ajouter le canal Microsoft Teams, ouvrez le bot dans le [Portail Azure](https://portal.azure.com), cliquez sur le panneau **Canaux**, puis cliquez sur **Teams**.

![Ajouter un canal Teams](media/teams/connect-teams-channel.png)

Cliquez ensuite sur **Enregistrer**.

![Enregistrer le canal Teams](media/teams/save-teams-channel.png)

Après avoir ajouté le canal Teams, accédez à la page **Canaux** et cliquez sur **Obtenir le code incorporé du bot**.

![Obtenir le code incorporé](media/teams/get-embed-code.png)

- Copiez la partie _https_ du code qui est illustré dans le dialogue **Obtenir le code incorporé du bot**. Par exemple : `https://teams.microsoft.com/l/chat/0/0?users=28:b8a22302e-9303-4e54-b348-343232`. 

- Dans le navigateur, collez cette adresse, puis choisissez l’application Microsoft Teams (client ou web) que vous utilisez pour ajouter le bot à Teams. Vous devriez voir le bot listé comme contact auquel vous pouvez envoyer des messages et duquel en recevoir dans Microsoft Teams. 

> [!IMPORTANT] 
> Il n’est pas recommandé d’ajouter un bot par GUID, sauf à des fins de test. Ceci limite fortement les fonctionnalités d’un bot. Les bots en production doivent être ajoutés à Teams dans le cadre d’une application. Consultez [Créer un bot](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-create) et [Tester et déboguer votre bot Microsoft Teams](https://docs.microsoft.com/microsoftteams/platform/concepts/bots/bots-test).


## <a name="additional-information"></a>Informations supplémentaires
Pour plus d’informations propres à Microsoft Teams, consultez la [documentation](https://docs.microsoft.com/en-us/microsoftteams/platform/overview) de Teams. 
