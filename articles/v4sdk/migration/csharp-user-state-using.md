---
title: Utilisation de l’état utilisateur .NET v3 dans un bot v4 | Microsoft Docs
description: Exemple illustrant l’utilisation de l’état utilisateur v3 dans un bot v4
keywords: Csharp, migration de bot, bot v3
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/21/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1bae27b9cf2bdc6a53a5c55fe7458802729160c1
ms.sourcegitcommit: 008aa6223aef800c3abccda9a7f72684959ce5e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70026698"
---
# <a name="using-net-v3-user-state-in-a-v4-bot"></a>Utilisation de l’état utilisateur .NET v3 dans un bot v4

Cet article présente un exemple illustrant la façon dont un bot v4 peut lire, écrire et supprimer des informations sur l’état utilisateur dans un bot v3.
Un bot gère l’état de conversation avec `MemoryStorage` pour suivre et diriger la conversation tout en posant des questions à l’utilisateur.  Il conserve l’**état utilisateur** au format v3 pour suivre les réponses de l’utilisateur en utilisant une classe `IStorage` personnalisée appelée `V3V4Storage`.  `IBotDataStore` est l’un des arguments de cette classe. Le code de base du SDK v3 a été copié dans `Bot.Builder.Azure.V3V4`. Il contient les trois fournisseurs de stockage du SDK v3 (Azure SQL, Table Azure et Cosmos DB).  L’objectif est de permettre l’utilisation de l’**état utilisateur** v3 existant dans un bot v4 migré.

L’exemple de code est disponible [ici](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/V4StateBotFromV3Providers).

## <a name="prerequisites"></a>Prérequis

- [SDK .NET Core](https://dotnet.microsoft.com/download) version 2.1

    ```bash
    # determine dotnet version
    dotnet --version
    ```

## <a name="setup"></a>Paramétrage

1. Cloner le référentiel

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. Dans un terminal, accédez à `MigrationV3V4/CSharp/V4StateBotFromV3Providers`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers
    ```

1. Exécutez le bot à partir d’un terminal ou de Visual Studio ; choisissez l’option A ou B.

    - À partir d’un terminal

        ```bash
        # run the bot
        dotnet run
        ```

    - Ou à partir de Visual Studio

        - Lancez Visual Studio et sélectionnez Fichier -> Ouvrir -> Projet/Solution
        - Accédez au dossier `BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers`
        - Sélectionnez le fichier `V4StateBot.sln`
        - Appuyez sur F5 pour exécuter le projet


## <a name="storage-provider-setup"></a>Configuration du fournisseur de stockage

Il est supposé que vous utilisez déjà un magasin d’état v3 configuré. Dans ce cas, pour configurer cet exemple afin d’utiliser le magasin d’état existant, il vous suffit d’ajouter les informations de connexion du fournisseur de stockage dans le fichier `web.config`, comme indiqué ci-dessous.

- Cosmos DB

```json
  "v3CosmosEndpoint": "https://yourcosmosdb.documents.azure.com:443/",
  "v3CosmosKey": "YourCosmosDbKey",
  "v3CosmosDatataseName": "v3botdb",
  "v3CosmosCollectionName": "v3botcollection",
```

- Azure SQL

```json
 "ConnectionStrings": {
    "SqlBotData": "Server=YourServer;Initial Catalog=BotData;Persist Security Info=False;User ID=YourUserName;Password=YourUserPassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=True;Connection Timeout=30;"
  },
```

- table Azure

```json
 "ConnectionStrings": {
    "AzureTable": "DefaultEndpointsProtocol=https;AccountName=YourAccountName;AccountKey=YourAccountKey;EndpointSuffix=core.windows.net"
  },
```

- Définir le fournisseur de stockage du bot

    Ouvrez le fichier `Startup.cs` à la racine du projet `V4V3StateBot`. Vers le milieu du fichier (des lignes 52 à 76 environ), vous voyez la configuration spécifique de chaque fournisseur de stockage. Les valeurs de configuration sont lues dans le fichier web.config. 

    [!code-csharp[Storage configuration](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=52-76)]

    Spécifiez le fournisseur de stockage à utiliser par votre bot en décommentant les lignes correspondantes de l’instance de votre choix. Une fois que le fournisseur est configuré correctement, assurez-vous que la classe du fournisseur est passée à `V3V4Storage` (aux environs des lignes 72-75). 

    [!code-csharp[Storage provider](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=72-75)]

    Cosmos DB (anciennement Document DB) est défini par défaut. Les valeurs possibles sont les suivantes :

    ```bash
    documentDbBotDataStore
    tableBotDataStore
    tableBotDataStore2
    sqlBotDataStore
    ```

- Lancez l’application. 

## <a name="v3v4-storage-and-state-classes"></a>Classes de stockage et d’état V3V4

### <a name="v3v4storage"></a>V3V4Storage

La classe `V3V4Storage` contient les principales fonctionnalités de mappage du stockage. Elle implémente l’interface `IStorage` v4 et mappe les méthodes du fournisseur de stockage (lecture, écriture et suppression) aux classes du fournisseur de stockage v3 afin que l’état utilisateur au format v3 puisse être utilisé à partir d’un bot v4.

### <a name="v3v4state"></a>V3V4State

Cette classe hérite de la classe `BotState` v4 et utilise une clé de style v3 (`IAddress`). Elle permet la lecture, l’écriture et la suppression dans le stockage v3 de la même façon que le stockage d’état v3 a toujours fonctionné.


## <a name="testing-the-bot-using-bot-framework-emulator"></a>Tester le bot à l’aide de Bot Framework Emulator

[Bot Framework Emulator][5] est une application de bureau qui permet aux développeurs de bots de tester et déboguer leurs bots sur l’hôte local (localhost) ou en les exécutant à distance par le biais d’un tunnel.

- Installez Bot Framework Emulator version 4.3.0 ou ultérieure à partir d’[ici][6]


### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Se connecter au bot à l’aide de Bot Framework Emulator

- Lancez Bot Framework Emulator
- Fichier -> Ouvrir le bot
- Entrez l’URL de bot `http://localhost:3978/api/messages`


## <a name="further-reading"></a>Pour aller plus loin

- [Présentation d’Azure Bot Service][21]
- [Bot State][7]
- [Écrire directement dans le stockage][8]
- [Gérer l’état de conversation et l’état utilisateur][9]

[3]: https://aka.ms/botframework-emulator
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=csharp
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=csharp
[21]: https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[40]: https://aka.ms/azuredeployment
