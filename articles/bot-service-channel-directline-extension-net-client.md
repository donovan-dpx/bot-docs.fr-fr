---
title: Créer un client .NET pour se connecter à l’extension App Service Direct Line
titleSuffix: Bot Service
description: Client .NET C# pour se connecter à l’extension App Service Direct Line
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani
ms.date: 07/25/2019
ms.openlocfilehash: 62fda46569c6134f798b4d253a0676a037fdfa0f
ms.sourcegitcommit: 6a83b2c8ab2902121e8ee9531a7aa2d85b827396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68866457"
---
# <a name="create-net-client-to-connect-to-direct-line-app-service-extension"></a>Créer un client .NET pour se connecter à l’extension App Service Direct Line

Cet article explique comment créer un client .NET en C# qui se connecte à l’extension App Service Direct Line.

## <a name="gather-your-direct-line-extension-keys"></a>Rassembler vos clés d’extension Direct Line

1. Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com/)
1. Dans le portail Azure, recherchez votre ressource **Azure Bot Service**
1. Cliquez sur **Canaux** pour configurer les canaux du bot
1. S’il n’est pas déjà activé, cliquez sur le canal **Direct Line** pour l’activer. 
1. S’il est déjà activé, dans la table Se connecter aux canaux, cliquez sur le lien **Modifier** sur la ligne Direct Line.
1. Faites défiler l’écran jusqu’à la section Sites. Il existe généralement un site par défaut, sauf si vous l’avez supprimé ou renommé.
1. Cliquez sur **Afficher le lien** pour afficher l’une des clés, puis copiez sa valeur.

    ![Clés d’extension App Service](./media/channels/direct-line-extension-extension-keys-net-client.png)

> [!NOTE]
> Cette valeur est votre secret client Direct Line utilisé pour se connecter à l’extension App Service Direct Line. Vous pouvez également créer des sites supplémentaires si vous le souhaitez et utiliser ces valeurs secrètes.

## <a name="add-the-preview-nuget-package-source"></a>Ajouter un aperçu de la source de package NuGet

L’aperçu des packages NuGet nécessaires à la création d’un client Direct Line en C# est disponible dans un flux NuGet.

1. Dans Visual Studio, accédez à l’élément de menu **Outils->Options**.
1. Sélectionnez l’élément **Gestionnaire de package NuGet->Sources de package** .
1. Cliquez sur le bouton + pour ajouter une nouvelle source de package avec les valeurs suivantes :
    - Nom : DL ASE Preview
    - Source : https://botbuilder.myget.org/F/experimental/api/v3/index.json
1. Cliquez sur le bouton **Mettre à jour** pour enregistrer les valeurs.
1. Cliquez sur **OK** pour quitter la configuration des sources de package.

## <a name="create-a-c-direct-line-client"></a>Créer un client Direct Line en C#

Les interactions avec l’extension App Service Direct Line se produisent différemment d’avec l’API Direct Line classique, car la plupart des communications se déroulent sur un *WebSocket*. Le client Direct Line mis à jour comprend des classes d’assistance pour l’ouverture et la fermeture d’un *WebSocket*, l’envoi de commandes via le WebSocket et la réception d’activités à partir du bot. Cette section décrit comment créer un client C# simple pour interagir avec un bot.

1. Créez un projet d’application console .NET Core 2.2 dans Visual Studio.
1. Ajoutez le **client Direct Line NuGet** à votre projet
    - Cliquez sur Dépendances dans l’arbre de solution
    - Sélectionnez **Gérer les packages NuGet...** .
    - Remplacez la source de package par celle que vous avez définie ci-dessus (DL ASE Preview)
    - Recherchez le package *Microsoft.Bot.Connector.Directline* version v3.0.3-Preview1 ou ultérieure.
    - Cliquez sur **Installer le package**.
1. Créez un client et générez un jeton à l’aide d’un secret. Cette étape est identique à la création de tout autre client Direct Line en C#, à l’exception du point de terminaison que vous devez utiliser dans votre bot, ajouté avec le chemin **.bot/** comme indiqué ci-dessous. N’oubliez pas le signe **/** de fin.

    ```csharp
    string endpoint = "https://<YOUR_BOT_HOST>.azurewebsites.net/.bot/";
    string secret = "<YOUR_BOT_SECRET>";

    var tokenClient = new DirectLineClient(
        new Uri(endpoint),
        new DirectLineClientCredentials(secret));
    var conversation = await tokenClient.Tokens.GenerateTokenForNewConversationAsync();
    ```

1. Une fois que vous avez une référence de conversation issue de la génération d’un jeton, vous pouvez utiliser cet ID de conversation pour ouvrir un WebSocket avec la nouvelle propriété `StreamingConversations` sur le `DirectLineClient`. Pour ce faire, vous devez créer un rappel qui est appelé lorsque le bot souhaite envoyer `ActivitySets` au client :

    ```csharp
    public static void ReceiveActivities(ActivitySet activitySet)
    {
        if (activitySet != null)
        {
            foreach (var a in activitySet.Activities)
            {
                if (a.Type == ActivityTypes.Message && a.From.Id.Contains("bot"))
                {
                    Console.WriteLine($"<Bot>: {a.Text}");
                }
            }
        }
    }
    ```

1. Vous êtes maintenant prêt à ouvrir le WebSocket sur la propriété `StreamingConversations` à l’aide du jeton de la conversation, `conversationId`, et de votre rappel `ReceiveActivities` :

    ```csharp
    var client = new DirectLineClient(
        new Uri(endpoint),
        new DirectLineClientCredentials(conversation.Token));

    await client.StreamingConversations.ConnectAsync(
        conversation.ConversationId,
        ReceiveActivities);
    ```

1. Le client peut maintenant être utilisé pour démarrer une conversation et envoyer `Activities` au bot :

    ```csharp

    var startConversation = await client.StreamingConversations.StartConversationAsync();
    var from = new ChannelAccount() { Id = "123", Name = "Fred" };
    var message = Console.ReadLine();

    while (message != "end")
    {
        try
        {
            var response = await client.StreamingConversations.PostActivityAsync(
                startConversation.ConversationId,
                new Activity()
                {
                    Type = "message",
                    Text = message,
                    From = from
                });
        }
        catch (OperationException ex)
        {
            Console.WriteLine(
                $"OperationException when calling PostActivityAsync: ({ex.StatusCode})");
        }
        message = Console.ReadLine();
    }
    ```
