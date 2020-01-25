---
title: Bot .NET avec extension App Service Direct Line
titleSuffix: Bot Service
description: Permettre à un bot .NET de fonctionner avec l’extension App Service Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 246e9ecace56126d625d5f9e2571e8d27ba780e8
ms.sourcegitcommit: df2b8d4e29ebfbb9e8a10091bb580389fe4c34cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76255992"
---
# <a name="configure-net-bot-for-extension"></a>Configurer un bot .NET pour l’extension

[!INCLUDE[applies-to-v4](includes/applies-to.md)]

Cet article explique comment mettre à jour un bot pour qu’il fonctionne avec des **canaux nommés** et comment activer l’extension App Service Direct Line dans la ressource **Azure App Service** où le bot est hébergé.  

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les étapes décrites ci-après, la ressource **Azure App Service** et le service **App service** associé doivent être présents dans Azure.

## <a name="enable-direct-line-app-service-extension"></a>Activer l’extension App Service Direct Line

Cette section décrit comment activer l’extension App Service Direct Line à l’aide de clés à partir de la configuration de canal de votre bot et de la ressource **Azure App Service** dans laquelle votre bot est hébergé.

## <a name="update-net-bot-to-use-direct-line-app-service-extension"></a>Mettre à jour un bot .NET pour utiliser l’extension App Service Direct Line

>! REMARQUE `Microsoft.Bot.Builder.StreamingExtensions` sont des packages en préversion ; ceux-ci ne seront pas mis à jour. Le kit de développement logiciel (SDK) v4.7 contient le [code de streaming](https://github.com/microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder/Streaming). Il n'est pas nécessaire d'installer les packages de streaming séparément. Si vous avez effectué une mise à niveau vers le kit SDK v4.7, reportez-vous aux [Informations supplémentaires](bot-service-channel-directline-extension-net-bot.md#additional-information) pour connaître les étapes à modifier dans cette section afin d'activer la fonctionnalité. 

1. Dans Visual Studio, ouvrez votre projet de bot.
2. Ajoutez le package **NuGet StreamingExtensions** à votre projet :
    1. Cliquez avec le bouton droit dans votre projet sur **Dépendances** et sélectionnez **Gérer les packages NuGet**.
    2. Sous l’onglet *Parcourir*, cliquez sur **Inclure la version préliminaire** pour afficher un aperçu des packages.
    3. Sélectionnez le package **Microsoft.Bot.Builder.StreamingExtensions**.
    4. Cliquez sur le bouton **Installer** pour installer le package ; lisez et acceptez le contrat de licence. 
3. Autorisez votre application à utiliser le **NamedPipe Bot Framework** :
    - Ouvrez le fichier `Startup.cs` .
    - Dans la méthode ``Configure``, ajoutez du code à ``UseBotFrameworkNamedPipe``

    ```csharp

    using Microsoft.Bot.Builder.StreamingExtensions;

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseHsts();
        }

        app.UseDefaultFiles();
        app.UseStaticFiles();

        // Allow bot to use named pipes.
        app.UseBotFrameworkNamedPipe();

        app.UseMvc();
    }
    ```

4. Enregistrez le fichier `Startup.cs`.
5. Ouvrez le fichier `appsettings.json` et entrez les valeurs suivantes :
    1. `"MicrosoftAppId": "<secret Id>"`
    2. `"MicrosoftAppPassword": "<secret password>"`

    Les valeurs sont les **AppID** et **AppSecret** associés au groupe d’inscription du service.

6. **Publiez** le bot sur votre Azure App Service.

### <a name="gather-your-direct-line-extension-keys"></a>Rassembler vos clés d’extension Direct Line

1. Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com/)
1. Dans le portail Azure, recherchez votre ressource **Azure Bot Service**
1. Cliquez sur **Canaux** pour configurer les canaux du bot
1. S’il n’est pas déjà activé, cliquez sur le canal **Direct Line** pour l’activer. 
1. S’il est déjà activé, dans la table Se connecter aux canaux, cliquez sur le lien **Modifier** sur la ligne Direct Line.
1. Faites défiler l’écran vers le bas jusqu’à la section Clés d’extension App Service. 
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

## <a name="confirm-direct-line-app-extension-and-the-bot-are-initialized"></a>Vérifier l’initialisation du bot et de l’extension App Service Direct Line

1. Dans votre navigateur, accédez à https://<votre_app_service>.azurewebsites.net/.bot. Si tout est correct, la page retourne le contenu JSON suivant : `{"k":true,"ib":true,"ob":true,"initialized":true}`. Il s’agit des informations que vous obtenez quand **tout fonctionne correctement**, où

    - **k** détermine si l’extension App Service Direct Line peut lire une clé d’extension App Service à partir de sa configuration. 
    - **initialized** détermine si l’extension App Service Direct Line peut utiliser la clé d’extension App Service pour télécharger les métadonnées de bot à partir d’Azure Bot Service.
    - **ib** détermine si l’extension App Service Direct Line peut établir une connexion entrante avec le bot.
    - **ob** détermine si l’extension App Service Direct Line peut établir une connexion sortante avec le bot. 

## <a name="additional-information"></a>Informations supplémentaires 

Si vous avez effectué une mise à niveau vers le kit SDK v4.7, les instructions de la section « Mettre à jour un bot .NET pour utiliser l'extension App Service Direct Line » sont légèrement différentes : 
- Ignorez l'**étape 2** car vous n'avez pas besoin d'installer les packages en préversion. 
- À l'**étape 3**, procédez comme suit :  

Autorisez votre application à utiliser **UseNamedPipes** :
- Ouvrez le fichier `Startup.cs` .
- Dans la méthode ``Configure``, ajoutez du code à ``UseNamedPipes``

    ```csharp

    using Microsoft.Bot.Builder.StreamingExtensions;

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseHsts();
        }

        app.UseDefaultFiles();
        app.UseStaticFiles();

        // Allow bot to use named pipes.
        app.UseNamedPipes();

        app.UseMvc();
    }

    ```

