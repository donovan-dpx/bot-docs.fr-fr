---
title: Connecter un bot à Teams | Microsoft Docs
description: Découvrez comment configurer un bot pour qu’il soit accessible via Teams.
keywords: Teams, canal de bot, configurer Teams
author: kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 07/05/2019
ms.openlocfilehash: d2609e4294416691e156ba3dbd09eabc8e0d3423
ms.sourcegitcommit: b498649da0b44f073dc5b23c9011ea2831edb31e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592228"
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

## <a name="additional-information"></a>Informations supplémentaires
Pour plus d’informations propres à Microsoft Teams, consultez la [documentation](https://docs.microsoft.com/en-us/microsoftteams/platform/overview) de Teams. 
