---
title: Gérer les données d'état personnalisé avec le Stockage de table Azure (v3 JS) - Bot Service
description: Découvrez comment enregistrer et récupérer des données d’état en utilisant Stockage Table Azure avec le kit SDK Bot Framework pour Node.js.
author: DucVo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 6eb4645437210f98cf43270a50b1654faf30ef25
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790344"
---
# <a name="manage-custom-state-data-with-azure-table-storage-for-nodejs"></a>Gérer les données d’état personnalisé avec le stockage de table Azure pour Node.js

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

Dans cet article, vous implémentez le stockage de table Azure pour stocker et gérer les données d’état de votre robot. Le service d’état du connecteur par défaut utilisé par les bots n’est pas destiné à l’environnement de production. Vous devez utiliser les [Extensions Azure](https://www.npmjs.com/package/botbuilder-azure) disponibles sur GitHub ou implémenter un client avec un état personnalisé à l’aide de la plateforme de stockage de données de votre choix. Voici quelques-unes des raisons d’utiliser le stockage d’état personnalisé :

- Débit d’API d’état plus élevé (meilleur contrôle des performances)
- Latence inférieure pour la distribution géographique
- Contrôle de l’emplacement de stockage des données (ex. : USA Ouest ou USA Est)
- Accès aux données d’état réelles
- Base de données d’état non partagée avec d’autres robots
- Stockage de plus de 32 Ko

## <a name="prerequisites"></a>Conditions préalables requises

- [Node.js](https://nodejs.org/en/).
- [Émulateur Bot Framework](~/bot-service-debug-emulator.md).
- Vous devez avoir un bot Node.js. Si vous n’en avez pas, accédez à [Créer un bot](bot-builder-nodejs-quickstart.md). 
- [Explorateur Stockage](http://storageexplorer.com/).

## <a name="create-azure-account"></a>Créer un compte Azure
Si vous n’avez pas de compte Azure, cliquez [ici](https://azure.microsoft.com/free/) pour vous inscrire et bénéficier d’un compte gratuit.

## <a name="set-up-the-azure-table-storage-service"></a>Configurer le service de stockage de table Azure
1. Une fois connecté au portail Azure, créez un service de stockage de table Azure en cliquant sur **Nouveau**. 
2. Recherchez un **compte de stockage** qui implémente la table Azure. Cliquez sur **créer** pour commencer à créer le compte de stockage. 
3. Renseignez les champs, puis cliquez sur le bouton **Créer** en bas de l’écran pour déployer le nouveau service de stockage. 
4. Une fois le nouveau service de stockage déployé, accédez au compte de stockage que vous venez de créer. Il est répertoriée dans le panneau **comptes de stockage**.
4. Sélectionnez **Clés d’accès**, puis copiez la clé en vue d’un usage ultérieur. Votre robot utilisera le **nom du compte de stockage** et la **clé** pour appeler le service de stockage afin d’enregistrer les données d’état.

## <a name="install-botbuilder-azure-module"></a>Installer le module botbuilder-azure

Pour installer le module `botbuilder-azure` à partir d’une invite de commande, accédez au répertoire du bot, puis exécutez la commande npm suivante :

```nodejs
npm install --save botbuilder-azure
```

## <a name="modify-your-bot-code"></a>Modifier le code de votre bot

Pour utiliser votre stockage **Table azure** , ajoutez les lignes de code suivantes au fichier **app.js** de votre robot.

1. Exigez le module nouvellement installé.

   ```javascript
   var azure = require('botbuilder-azure'); 
   ```

2. Configurez les paramètres de connexion pour se connecter à Azure.
   ```javascript
   // Table storage
   var tableName = "Table-Name"; // You define
   var storageName = "Table-Storage-Name"; // Obtain from Azure Portal
   var storageKey = "Azure-Table-Key"; // Obtain from Azure Portal
   ```
   Les valeurs `storageName` et `storageKay` figurent dans le menu **Clés d’accès** de votre Table Azure. Si le `tableName` n’existe pas dans la Table Azure, il est créé pour vous.

3. À l’aide du module `botbuilder-azure`, créez deux nouveaux objets à connecter à la Table Azure. Tout d’abord, créez une instance de `AzureTableClient` transmettant les paramètres de configuration de la connexion. Ensuite, créez une instance de `AzureBotStorage` transmettant l’objet `AzureTableClient`. Par exemple :

   ```javascript
   var azureTableClient = new azure.AzureTableClient(tableName, storageName, storageKey);

   var tableStorage = new azure.AzureBotStorage({gzipData: false}, azureTableClient);
   ```

4. Indiquez que vous souhaitez utiliser votre base de données personnalisée au lieu du stockage en mémoire et ajouter des informations de session à la base de données. Par exemple :

   ```javascript
   var bot = new builder.UniversalBot(connector, function (session) {
        // ... Bot code ...

        // capture session user information
        session.userData = {"userId": session.message.user.id, "jobTitle": "Senior Developer"};

        // capture conversation information  
        session.conversationData[timestamp.toISOString().replace(/:/g,"-")] = session.message.text;

        // save data
        session.save();
   })
   .set('storage', tableStorage);
   ```
Vous êtes à présent prêt à tester le robot avec l’émulateur.

## <a name="run-your-bot-app"></a>Exécuter votre application de bot

À partir d’une invite de commandes, accédez au répertoire de votre bot et exécutez celui-ci avec la commande suivante :

```nodejs
node app.js
```

## <a name="connect-your-bot-to-the-emulator"></a>Connecter votre bot à l’émulateur

À ce stade, votre bot s’exécute localement. Démarrez l’émulateur, puis connectez-vous à votre bot à partir de l’émulateur :

1. Tapez <strong>http://localhost:port-number/api/messages</strong> dans la barre d’adresse de l’émulateur, où port-number correspond au numéro de port affiché dans le navigateur où s’exécute votre application. Pour l’instant, vous pouvez laisser vides les champs <strong>ID d’application Microsoft</strong> et <strong>Mot de passe d’application Microsoft</strong>. Vous obtiendrez ces informations ultérieurement lors de l’[inscription du bot](~/bot-service-quickstart-registration.md).
2. Cliquez sur **Connecter**.
3. Testez votre bot en lui envoyant un message. Interagissez avec votre bot comme vous le feriez normalement. Lorsque vous avez terminé, accédez à l’**Explorateur Stockage** et affichez vos données d’état enregistrées.

## <a name="view-data-in-storage-explorer"></a>Afficher les données dans l’Explorateur Stockage

Pour afficher les données d’état, ouvrez l’**Explorateur Stockage** et connectez-vous à Azure à l’aide de vos informations d’identification du portail Azure, ou connectez-vous directement à la table en utilisant le `storageName` et la `storageKey`, puis accédez à votre `tableName`. 

![Capture d’écran de l’Explorateur Stockage avec des lignes de table de données du robot](~/media/bot-builder-nodejs-state-azure-table-storage/bot-builder-nodejs-state-azure-table-storage-query.png)

Un enregistrement de la conversation dans la colonne **données** ressemble à ceci :

```JSON
{
    "2018-05-15T18-23-48.780Z": "I'm the second user",
    "2018-05-15T18-23-55.120Z": "Do you know what time it is?",
    "2018-05-15T18-24-12.214Z": "I'm looking for information about the new process."
}
```

## <a name="next-step"></a>Étape suivante

À présent que vous contrôlez totalement les données d’état de votre bot, voyons comment les utiliser pour mieux gérer le flux de la conversation.

> [!div class="nextstepaction"]
> [Gérer le flux de la conversation](bot-builder-nodejs-dialog-manage-conversation-flow.md)
