---
title: Utilisation de l'état utilisateur JavaScript v3 dans un bot v4 - Bot Service
description: Exemple illustrant l’utilisation de l’état utilisateur v3 dans un bot v4
keywords: JavaScript, migration de bot, bot v3
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/14/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ad3f9a1cc9ce3f06bad71615e244a791025904d9
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791061"
---
<!-- This article is on hold -->

# <a name="using-javascript-v3-user-state-in-a-v4-bot"></a>Utilisation de l’état utilisateur JavaScript v3 dans un bot v4

Cet article présente un exemple illustrant la façon dont un bot v4 peut lire, écrire et supprimer des informations sur l’état utilisateur dans un bot v3.

L’exemple de code est disponible [ici](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot).

> [!NOTE]
> Un bot gère l’**état de conversation** pour suivre et diriger la conversation ainsi que poser des questions à l’utilisateur. Il gère l’**état utilisateur** pour suivre les réponses de l’utilisateur.

## <a name="prerequisites"></a>Conditions préalables requises

- npm version 6.9.0 ou ultérieure (nécessaire pour prendre en charge les alias de package).

- Node.js version 10.14.1 ou ultérieure.

    Pour connaître la version installée sur votre ordinateur, dans un terminal, exécutez la commande suivante :

    ```bash
    # determine node version
    node --version
    ```

## <a name="setup"></a>Programme d’installation

1. Cloner le référentiel

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. Dans un terminal, accédez à `BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot
    ```

1. Exécutez `npm install` aux emplacements suivants :

    ```bash
    root
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Exécutez ``npm run build`` ou ``tsc`` pour générer les modules `StorageMapper` et `UserState` aux emplacements suivants :

    ```bash
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Configurer la base de données

    1. Copiez le contenu du fichier `.env.example`.
    1. Créez un fichier nommé `.env` et collez-y le contenu copié précédemment. 
    1. Renseignez les valeurs propres à chaque fournisseur de stockage utilisé.
        Notez que le *nom d’utilisateur*, le *mot de passe* et les *informations de l’hôte* sont indiqués dans le portail Azure sous la section du fournisseur de stockage spécifique, par exemple *Cosmos DB*, *Stockage Table* ou *Base de données SQL*. Les noms de table et de collection sont définis par l’utilisateur.
  
1. Définir le fournisseur de stockage du bot

    1. Ouvrez le fichier `index.js` à la racine du projet. Dans la première partie du fichier (des lignes 38 à 98 environ), vous voyez la configuration spécifique de chaque fournisseur de stockage, comme indiqué dans les commentaires. Les valeurs de configuration sont lues dans le fichier `.env` via le nœud `process.env`. L’extrait de code suivant montre comment configurer la base de données SQL Database.

        [!code-javascript[Storage configuration](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=77-92)]

    1. Spécifiez le fournisseur de stockage à utiliser par votre bot en passant l’instance du client de stockage de votre choix à l’adaptateur `StorageMapper` (à la ligne 107 environ).  

        [!code-javascript[StorageMapper](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=105-107)]

        *Cosmos DB* est le paramètre par défaut. Les valeurs possibles sont les suivantes :

        ```bash
            cosmosStorageClient
            tableStorage
            sqlStorage
        ```

1. Lancez l’application. À partir de la racine du projet, exécutez la commande suivante :

    ```bash
    npm run start
    ```

## <a name="adapter-classes"></a>Classes d’adaptateur

### <a name="v4v3storagemapper"></a>V4V3StorageMapper

La classe `StorageMapper` contient les principales fonctionnalités d’adaptateur. Elle implémente l’interface de stockage v4 et mappe les méthodes du fournisseur de stockage (lecture, écriture et suppression) aux classes du fournisseur de stockage v3 afin que l’état utilisateur au format v3 puisse être utilisé à partir d’un bot v4.

### <a name="v4v3userstate"></a>V4V3UserState

Cette classe étend la classe `BotState` v4 (`botbuilder-core`) afin qu’elle utilise une clé de type v3, permettant la lecture, l’écriture et la suppression vers le stockage v3.

## <a name="testing-the-bot-using-bot-framework-emulator"></a>Tester le bot à l’aide de Bot Framework Emulator

[Bot Framework Emulator][5] est une application de bureau qui vous permet de tester et déboguer un bot sur l’hôte local (localhost) ou en l’exécutant à distance par le biais d’un tunnel.

- Installez Bot Framework Emulator version 4.3.0 ou ultérieure à partir d’[ici][6]

### <a name="connect-to-the-bot-using-bot-framework-emulator"></a>Se connecter au bot à l’aide de Bot Framework Emulator

1. Lancez Bot Framework Emulator.
1. Entrez l’URL suivante (point de terminaison) : `http://localhost:3978/api/messages`.

### <a name="testing-steps"></a>Étapes du test

1. Ouvrez le bot dans l’émulateur et envoyez un message. Entrez votre nom lorsque vous y êtes invité.
1. À la fin de cette étape, envoyez un autre message au bot.
1. Assurez-vous que vous n’êtes pas de nouveau invité à entrer votre nom. Le bot est censé lire le nom à partir du stockage et se rappeler qu’il vous a déjà invité à le fournir.
1. Le bot doit normalement retourner votre message.
1. Accédez à votre fournisseur de stockage dans Azure et vérifiez que votre nom est stocké en tant que données utilisateur dans la base de données.

## <a name="deploy-the-bot-to-azure"></a>Déployer le bot dans Azure

Pour en savoir plus sur le déploiement d’un bot dans Azure et obtenir des instructions de déploiement complètes, consultez [Déployer votre bot dans Azure][40].

## <a name="further-reading"></a>Lectures supplémentaires

- [Présentation d’Azure Bot Service][21]
- [Bot State][7]
- [Écrire directement dans le stockage][8]
- [Gérer l’état de conversation et l’état utilisateur][9]
- [Restify][30]
- [dotenv][31]

[3]: https://aka.ms/botframework-emulator
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=javascript
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=javascript
[21]: https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[30]: https://www.npmjs.com/package/restify
[31]: https://www.npmjs.com/package/dotenv
[40]: https://aka.ms/azuredeployment
