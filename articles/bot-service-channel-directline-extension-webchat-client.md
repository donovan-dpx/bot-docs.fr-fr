---
title: Utiliser WebChat avec l’extension App Service Direct Line
titleSuffix: Bot Service
description: Utiliser WebChat avec l’extension App Service Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 35772bd4e31cad4787f6ef8b50a56503bfbef55c
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491216"
---
# <a name="use-webchat-with-the-direct-line-app-service-extension"></a>Utiliser WebChat avec l’extension App Service Direct Line

Cet article explique comment utiliser WebChat avec l’extension App Service Direct Line.

## <a name="get-your-direct-line-secret"></a>Obtenir votre secret Direct Line

La première étape consiste à trouver votre secret Direct Line. Pour ce faire, suivez les instructions qui figurent dans l’article [ Connecter un bot à Direct Line](bot-service-channel-connect-directline.md).

## <a name="get-the-preview-version-of-directlinejs"></a>Obtenir la préversion de DirectLineJS
La préversion de DirectLineJS se trouve ici : https://github.com/Jeffders/DirectLineAppServiceExtensionPreview/tree/master/libraries

## <a name="integrate-webchat-client"></a>Intégrer le client WebChat

De façon générale, l’approche est la même que précédemment. Seule différence, une version de **WebChat** a été créée et prend en charge le trafic **WebSocket** bidirectionnel, qui se connecte directement à votre bot hébergé au lieu de se connecter à https://directline.botframework.com/.
L’URL Direct Line de votre bot sera `https://<your_app_service>.azurewebsites.net/.bot/`, où l’extension `/.bot/` est le **point de terminaison** Direct Line sur votre App Service.
Si vous pouvez configurer votre propre nom de domaine, vous devez toujours ajouter le chemin `/.bot/` pour accéder aux API REST Direct Line.

1. Échangez le secret pour un jeton en suivant les instructions fournies dans l’article [Authentification](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0). Toutefois, au lieu d’obtenir un jeton à l’emplacement `https://directline.botframework.com/v3/directline/tokens/generate`, vous générez le jeton directement à partir de votre extension App Service Direct Line à l’emplacement `https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate`.  

1. Une fois que vous avez un jeton, vous pouvez mettre à jour la page web qui utilise Webchat avec les modifications suivantes :

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
    <title>Direct Line Streaming Sample</title>
    <script src="~/directLine.js"></script>
    <script src="https://cdn.botframework.com/botframework-webchat/master/webchat.js"></script>
    <style>
        html, body {
            height: 100%
        }

        body {
            margin: 0
        }

        #webchat,

        #webchat > * {
            height: 100%;
            width: 100%;
        }
    </style>
</head>

<body>
    <div id="webchat" role="main"></div>
    <script>
        const activityMiddleware = () => next => card => {
            if (card.activity.type === 'trace') {
                // Return false means, don't render the trace activities
                return () => false;
            } else {
                return children => next(card)(children);
            }
        };


        var dl = new DirectLine.DirectLine({
            secret: '<your token>',
            domain: 'https://<your_site>.azurewebsites.net/.bot/v3/directline',
            webSocket: true,
            conversationId: '<your conversation id>'
        });
        window.WebChat.renderWebChat({
            activityMiddleware,
            directLine: dl,
            userID: '<your generated user id>'
        }, document.getElementById('webchat'));
    </script>
</body>
</html>

```
