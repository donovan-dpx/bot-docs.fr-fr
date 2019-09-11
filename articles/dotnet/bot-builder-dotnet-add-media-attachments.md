---
title: Ajouter des pièces jointes multimédias aux messages | Microsoft Docs
description: Découvrez comment ajouter des pièces jointes multimédias aux messages à l’aide du kit SDK Bot Framework pour .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 19f3db74aef7aeffcd754e0bea753fa73b7ea285
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298442"
---
# <a name="add-media-attachments-to-messages"></a>Ajouter des pièces jointes multimédia aux messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-media-attachments.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-send-receive-attachments.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-media-attachments.md)

Un échange de messages entre l’utilisateur et le bot peut contenir des pièces jointes multimédias (par exemple, des images, des vidéos, des données audio et des fichiers). 

La propriété `Attachments` de l’objet <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Activity</a> contient un tableau d’objets <a href="https://docs.microsoft.com/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachment</a> qui représentent les pièces jointes multimédias et les cartes enrichies au sein du message. 

> [!NOTE]
> [Ajoutez des cartes enrichies aux messages](bot-builder-dotnet-add-rich-card-attachments.md).

## <a name="add-a-media-attachment"></a>Ajouter une pièce jointe multimédia  

Pour ajouter une pièce jointe multimédia à un message, créez un objet `Attachment` pour l’activité `message` et définissez les propriétés `ContentType`, `ContentUrl` et `Name`. 

[!code-csharp[Add media attachment](../includes/code/dotnet-add-attachments.cs#addMediaAttachment)]

Si la pièce jointe est une image, un contenu audio ou une vidéo, le service Connector communique les données de pièce jointe au canal de manière à permettre au [canal](bot-builder-dotnet-channeldata.md) d’afficher cette pièce jointe dans la conversation. Si la pièce jointe est un fichier, l’URL du fichier s’affiche sous la forme d’un lien hypertexte dans la conversation.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Aperçu des fonctionnalités avec l’inspecteur de canaux][inspector]
- [Vue d’ensemble des activités](bot-builder-dotnet-activities.md)
- [Créer des messages](bot-builder-dotnet-create-messages.md)
- [Ajouter des cartes enrichies aux messages](bot-builder-dotnet-add-rich-card-attachments.md)
- <a href="https://docs.botframework.com/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activity</a>
- <a href="https://docs.microsoft.com/dotnet/api/microsoft.bot.connector.attachments?view=botconnector-3.12.2.4" target="_blank">Attachments Class</a> (Classe Attachments)

[inspector]: ../bot-service-channel-inspector.md


