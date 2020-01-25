---
title: Bot Node.js avec extension App Service Direct Line
titleSuffix: Bot Service
description: Permettre à un bot Node.js de fonctionner avec l’extension App Service Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: dev
ms.date: 01/15/2020
ms.openlocfilehash: 025b3db49f82643d0942518ff87810714389b54c
ms.sourcegitcommit: df2b8d4e29ebfbb9e8a10091bb580389fe4c34cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256440"
---
# <a name="configure-nodejs-bot-for-extension"></a>Configurer un bot Node.js pour l’extension

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Cet article explique comment mettre à jour un bot pour qu’il fonctionne avec des **canaux nommés** et comment activer l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes décrites ci-après, la ressource **Azure App Service** et le service **App service** associé doivent être présents dans Azure.

## <a name="enable-direct-line-app-service-extension"></a>Activer l’extension App Service Direct Line

Cette section décrit comment activer l’extension App Service Direct Line à l’aide de clés à partir de la configuration de canal de votre bot et de la ressource **Azure App Service** dans laquelle votre bot est hébergé.

## <a name="update-nodejs-bot-to-use-direct-line-app-service-extension"></a>Mettre à jour un bot Node.js pour utiliser l’extension App Service Direct Line

1. Vous avez besoin de BotBuilder version 4.7.0 ou ultérieure pour utiliser un bot Node.js avec l’extension App Service Direct Line.
1. Pour mettre à jour un bot existant à l’aide du SDK version 4.x
    1. Dans le répertoire racine de votre bot, exécutez `npm install --save botbuilder` pour installer les packages les plus récents.
1. Autorisez votre application à utiliser le **canal nommé Extension App Service Direct Line** :
    - Mettez à jour le fichier index.js du bot (sous l’attribution de l’adaptateur et du bot) en ajoutant ceci :
    
    ```Node.js
    
    adapter.useNamedPipe(async (context) => {
        await myBot.run(context);
    });
    ```

1. Enregistrez le fichier `index.js`.
1. Mettez à jour le fichier `Web.Config` par défaut en y ajoutant le gestionnaire `AspNetCore` dont a besoin l’extension App Service Direct Line pour traiter les requêtes :
    - Recherchez le fichier `Web.Config` dans le répertoire `wwwroot` de votre bot et remplacez le contenu par défaut par :
    
    ```XML
    
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.webServer>
        <handlers>      
          <add name="aspNetCore" path="*/.bot/*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
          <add name="iisnode" path="*" verb="*" modules="iisnode" />
        </handlers>
       </system.webServer>
    </configuration>
    ```
    
1. Ouvrez le fichier `appsettings.json` et entrez les valeurs suivantes :
    1. `"MicrosoftAppId": "<secret Id>"`
    1. `"MicrosoftAppPassword": "<secret password>"`

    Les valeurs sont les **AppID** et **AppSecret** associés au groupe d’inscription du service.

1. **Publiez** le bot sur votre Azure App Service.

### <a name="gather-your-direct-line-app-service-extension-keys"></a>Rassembler vos clés d’extension App Service Direct Line

1. Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com/)
1. Dans le portail Azure, recherchez votre ressource **Azure Bot Service**
1. Cliquez sur **Canaux** pour configurer les canaux du bot
1. S’il n’est pas déjà activé, cliquez sur le canal **Direct Line** pour l’activer. 
1. S’il est déjà activé, dans la table Se connecter aux canaux, cliquez sur le lien **Modifier** sur la ligne Direct Line.
1. Faites défiler l’écran vers le bas jusqu’à la section **Clés d’extension App Service**. 
1. Cliquez sur **Afficher le lien** pour afficher l’une des clés, puis copiez sa valeur.

![Clés d’extension App Service](./media/channels/direct-line-extension-extension-keys.png)

### <a name="enable-the-direct-line-app-service-extension"></a>Activer l’extension App Service Direct Line

1. Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com/)
1. Dans le portail Azure, recherchez la page de la ressource **Azure App Service** pour l’application web où votre bot est ou sera hébergé
1. Cliquez sur **Configuration**. Sous la section *Paramètres de l’application*, ajoutez les nouveaux paramètres suivants :

    |Name|Valeur|
    |---|---|
    |DirectLineExtensionKey|<Clé_Extension_App_Service_De_Section_1>|
    |DIRECTLINE_EXTENSION_VERSION|latest|

1. Dans la section *Configuration*, cliquez sur la section de paramètres **Général** et activez **WebSockets**
1. Cliquez sur **Enregistrer** pour enregistrer les paramètres. Ceci redémarre Azure App Service.

### <a name="confirm-direct-line-app-extension-and-the-bot-are-initialized"></a>Vérifier l’initialisation du bot et de l’extension App Service Direct Line

1. Dans votre navigateur, accédez à https://<votre_app_service>.azurewebsites.net/.bot. Si tout est correct, la page retourne le contenu JSON suivant : `{"k":true,"ib":true,"ob":true,"initialized":true}`. Il s’agit des informations que vous obtenez quand **tout fonctionne correctement**, où

    - **k** détermine si l’extension App Service Direct Line peut lire une clé d’extension App Service à partir de sa configuration. 
    - **initialized** détermine si l’extension App Service Direct Line peut utiliser la clé d’extension App Service pour télécharger les métadonnées de bot à partir d’Azure Bot Service.
    - **ib** détermine si l’extension App Service Direct Line peut établir une connexion entrante avec le bot.
    - **ob** détermine si l’extension App Service Direct Line peut établir une connexion sortante avec le bot. 
