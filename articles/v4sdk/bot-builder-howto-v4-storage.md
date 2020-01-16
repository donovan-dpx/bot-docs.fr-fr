---
title: Écrire directement dans le stockage - Bot Service
description: Découvrez comment écrire directement dans le stockage avec le kit SDK Bot Framework pour .NET.
keywords: stockage, lecture et écriture, stockage de données, stockage de mémoire, étiquette d’entité
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2f16b34715b78635b1dd5028f4955bad0269973b
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75798231"
---
# <a name="write-directly-to-storage"></a>Écrire directement dans le stockage

[!INCLUDE[applies-to](../includes/applies-to.md)]

Vous pouvez lire et écrire directement dans votre objet de stockage sans utiliser d’intergiciel ou d’objet de contexte. Cela peut convenir pour les données que votre bot utilise afin de préserver une conversation, ou les données provenant d'une source située en dehors du flux de conversation de votre bot. Dans ce modèle de stockage de données, plutôt que d'utiliser un gestionnaire d'états, les données sont lues directement à partir du stockage. Les exemples de code de cet article montrent comment lire et écrire des données dans le stockage à l'aide du **stockage en mémoire**, de **Cosmos DB**, du **Stockage Blob**, du **Stockage de table Azure** et du **magasin de transcription d'objets blob Azure**. 

## <a name="prerequisites"></a>Conditions préalables requises
- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Bonne connaissance de l’article : Créez un bot localement pour [dotnet](https://aka.ms/bot-framework-www-c-sharp-quickstart), [nodeJS](https://aka.ms/bot-framework-www-node-js-quickstart) ou [Python](https://aka.ms/bot-framework-www-node-python-quickstart).
- Modèle de kit SDK Bot Framework v4 pour [modèle C#](https://aka.ms/bot-vsix), [nodeJS](https://nodejs.org) et [yeoman](http://yeoman.io).

## <a name="about-this-sample"></a>À propos de cet exemple
L’exemple de code de cet article commence par la structure d’un bot d’écho de base, puis étend les fonctionnalités de ce bot par ajout de code (ci-dessous). Ce code étendu crée une liste pour conserver les entrées utilisateur au fur et à mesure qu’elles sont reçues. À chaque tour, la liste complète des entrées utilisateur est répercutée vers l’utilisateur. La structure de données contenant cette liste d’entrées est ensuite enregistrée dans le stockage à la fin du tour. Différents types de stockage sont explorés lorsque des fonctionnalités supplémentaires sont ajoutées à cet exemple de code.

## <a name="memory-storage"></a>Stockage mémoire

Le kit SDK Bot Framework permet de stocker les entrées utilisateur en utilisant un stockage en mémoire. Le stockage mémoire est utilisé uniquement à des fins de test et n’est pas destiné à une utilisation en production. Le stockage en mémoire est volatile et temporaire puisque les données sont effacées à chaque redémarrage du bot. Les types de stockage persistant, tels que le stockage de base de données, sont plus adaptés aux bots de production. Veillez à définir le stockage sur **Cosmos DB**, **Stockage Blob** ou [**Stockage de table Azure**](~/nodejs/bot-builder-nodejs-state-azure-table-storage.md) avant de publier votre bot.

#### <a name="build-a-basic-bot"></a>Créer un bot de base

Le reste de cette rubrique s’appuie sur un bot Echo. Vous pouvez générer localement l’exemple de code de bot d’écho en suivant les instructions du guide de démarrage rapide pour la génération d’un [EchoBot en C#](https://aka.ms/bot-framework-www-c-sharp-quickstart), d’un [EchoBot en JS](https://aka.ms/bot-framework-www-node-js-quickstart) ou d’un [EchoBot en Python](https://aka.ms/bot-framework-www-node-python-quickstart).

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Bot.Builder;
using Microsoft.Bot.Schema;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

// Represents a bot saves and echoes back user input.
public class EchoBot : ActivityHandler
{
   // Create local Memory Storage.
   private static readonly MemoryStorage _myStorage = new MemoryStorage();

   // Create cancellation token (used by Async Write operation).
   public CancellationToken cancellationToken { get; private set; }

   // Class for storing a log of utterances (text of messages) as a list.
   public class UtteranceLog : IStoreItem
   {
      // A list of things that users have said to the bot
      public List<string> UtteranceList { get; } = new List<string>();

      // The number of conversational turns that have occurred        
      public int TurnNumber { get; set; } = 0;

      // Create concurrency control where this is used.
      public string ETag { get; set; } = "*";
   }
     
   // Echo back user input.
   protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
   {
      // preserve user input.
      var utterance = turnContext.Activity.Text;  
      // make empty local logitems list.
      UtteranceLog logItems = null;
          
      // see if there are previous messages saved in storage.
      try
      {
         string[] utteranceList = { "UtteranceLog" };
         logItems = _myStorage.ReadAsync<UtteranceLog>(utteranceList).Result?.FirstOrDefault().Value;
      }
      catch
      {
         // Inform the user an error occured.
         await turnContext.SendActivityAsync("Sorry, something went wrong reading your stored messages!");
      }
         
      // If no stored messages were found, create and store a new entry.
      if (logItems is null)
      {
         // add the current utterance to a new object.
         logItems = new UtteranceLog();
         logItems.UtteranceList.Add(utterance);
         // set initial turn counter to 1.
         logItems.TurnNumber++;

         // Show user new user message.
         await turnContext.SendActivityAsync($"{logItems.TurnNumber}: The list is now: {string.Join(", ", logItems.UtteranceList)}");

         // Create Dictionary object to hold received user messages.
         var changes = new Dictionary<string, object>();
         {
            changes.Add("UtteranceLog", logItems);
         }
         try
         {
            // Save the user message to your Storage.
            await _myStorage.WriteAsync(changes, cancellationToken);
         }
         catch
         {
            // Inform the user an error occured.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
      // Else, our Storage already contained saved user messages, add new one to the list.
      else
      {
         // add new message to list of messages to display.
         logItems.UtteranceList.Add(utterance);
         // increment turn counter.
         logItems.TurnNumber++;
         
         // show user new list of saved messages.
         await turnContext.SendActivityAsync($"{logItems.TurnNumber}: The list is now: {string.Join(", ", logItems.UtteranceList)}");
         
         // Create Dictionary object to hold new list of messages.
         var changes = new Dictionary<string, object>();
         {
            changes.Add("UtteranceLog", logItems);
         };
         
         try
         {
            // Save new list to your Storage.
            await _myStorage.WriteAsync(changes,cancellationToken);
         }
         catch
         {
            // Inform the user an error occured.
            await turnContext.SendActivityAsync("Sorry, something went wrong storing your message!");
         }
      }
      ...  // OnMessageActivityAsync( )
   }
}

```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser le fichier de configuration .env, votre bot doit inclure un package supplémentaire. S’il n’est pas encore installé, obtenez le package dotnet à partir de npm :

```powershell
npm install --save dotenv
```

**bot.js**
```javascript
const { ActivityHandler, MemoryStorage } = require('botbuilder');
const restify = require('restify');

// Add memory storage.
var storage = new MemoryStorage();

// Process incoming requests - adds storage for messages.
class MyBot extends ActivityHandler {
    constructor() {
        super();
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        this.onMessage(async turnContext => { console.log('this gets called (message)'); 
        await turnContext.sendActivity(`You said '${ turnContext.activity.text }'`); 
        // Save updated utterance inputs.
        await logMessageText(storage, turnContext);
    });
        this.onConversationUpdate(async turnContext => { console.log('this gets called (conversation update)'); 
        await turnContext.sendActivity('[conversationUpdate event detected]'); });
    }
}

// This function stores new user messages. Creates new utterance log if none exists.
async function logMessageText(storage, turnContext) {
    let utterance = turnContext.activity.text;
    // debugger;
    try {
        // Read from the storage.
        let storeItems = await storage.read(["UtteranceLogJS"])
        // Check the result.
        var UtteranceLogJS = storeItems["UtteranceLogJS"];
        if (typeof (UtteranceLogJS) != 'undefined') {
            // The log exists so we can write to it.
            storeItems["UtteranceLogJS"].turnNumber++;
            storeItems["UtteranceLogJS"].UtteranceList.push(utterance);
            // Gather info for user message.
            var storedString = storeItems.UtteranceLogJS.UtteranceList.toString();
            var numStored = storeItems.UtteranceLogJS.turnNumber;

            try {
                await storage.write(storeItems)
                await turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                await turnContext.sendActivity(`Write failed of UtteranceLogJS: ${err}`);
            }
        }
        else{
            await turnContext.sendActivity(`Creating and saving new utterance log`);
            var turnNumber = 1;
            storeItems["UtteranceLogJS"] = { UtteranceList: [`${utterance}`], "eTag": "*", turnNumber }
            // Gather info for user message.
            var storedString = storeItems.UtteranceLogJS.UtteranceList.toString();
            var numStored = storeItems.UtteranceLogJS.turnNumber;

            try {
                await storage.write(storeItems)
                await turnContext.sendActivity(`${numStored}: The list is now: ${storedString}`);
            } catch (err) {
                await turnContext.sendActivity(`Write failed: ${err}`);
            }
        }
    }
    catch (err){
        await turnContext.sendActivity(`Read rejected. ${err}`);
    }
}

module.exports.MyBot = MyBot;

```

### <a name="pythontabpython"></a>[Python](#tab/python)

**bot.py**
```py
from botbuilder.core import ActivityHandler, TurnContext, StoreItem, MemoryStorage


class UtteranceLog(StoreItem):
    """
    Class for storing a log of utterances (text of messages) as a list.
    """

    def __init__(self):
        super(UtteranceLog, self).__init__()
        self.utterance_list = []
        self.turn_number = 0
        self.e_tag = "*"


class MyBot(ActivityHandler):
    """
    Represents a bot saves and echoes back user input.
    """

    def __init__(self):
        self.storage = MemoryStorage()

    async def on_message_activity(self, turn_context: TurnContext):
        utterance = turn_context.activity.text

        # read the state object
        store_items = await self.storage.read(["UtteranceLog"])

        if "UtteranceLog" not in store_items:
            # add the utterance to a new state object.
            utterance_log = UtteranceLog()
            utterance_log.utterance_list.append(utterance)
            utterance_log.turn_number = 1
        else:
            # add new message to list of messages existing state object.
            utterance_log: UtteranceLog = store_items["UtteranceLog"]
            utterance_log.utterance_list.append(utterance)
            utterance_log.turn_number = utterance_log.turn_number + 1

        # Show user list of utterances.
        await turn_context.send_activity(f"{utterance_log.turn_number}: "
                                         f"The list is now: {','.join(utterance_log.utterance_list)}")

        try:
            # Save the user message to your Storage.
            changes = {"UtteranceLog": utterance_log}
            await self.storage.write(changes)
        except Exception as exception:
            # Inform the user an error occurred.
            await turn_context.send_activity("Sorry, something went wrong storing your message!")
```

---

### <a name="start-your-bot"></a>Démarrer votre robot
Exécutez votre bot en local.

### <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot
- Installez l’[émulateur](https://aka.ms/bot-framework-emulator-readme) Bot Framework. Ensuite, démarrez l’émulateur, puis connectez-vous à votre bot dans l’émulateur :

1. Cliquez sur le lien **Create new bot configuration** sous l’onglet « Welcome » de l’émulateur. 
2. Renseignez les champs pour vous connecter à votre bot en utilisant les informations figurant dans la page web affichée lorsque vous avez démarré votre bot.

### <a name="interact-with-your-bot"></a>Interagir avec votre bot
Envoyez un message à votre bot. Le bot liste les messages qu’il a reçus.

![Bot de stockage de test d’émulateur](./media/emulator-direct-storage-test.png)

## <a name="using-cosmos-db"></a>Utilisation de Cosmos DB
Maintenant que vous avez utilisé le stockage mémoire, nous allons mettre à jour le code pour utiliser Azure Cosmos DB. Cosmos DB est un service de base de données multimodèle mondialement distribué de Microsoft. Azure Cosmos DB vous permet de faire évoluer en toute flexibilité et de façon indépendante le débit et le stockage sur n’importe quel nombre de régions géographiques Azure. Il offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des contrats SLA complets. 

### <a name="set-up"></a>Configurer
Pour utiliser Cosmos DB dans votre bot, vous devez créer une ressource de base de données avant d’aborder le code. Pour une description approfondie de la création de base de données et d’application par Cosmos DB, accédez ici à la documentation pour [Cosmos DB dotnet](https://aka.ms/Bot-framework-create-dotnet-cosmosdb) ou [Cosmos DB nodejs](https://aka.ms/Bot-framework-create-nodejs-cosmosdb).

### <a name="create-your-database-account"></a>Créer votre compte de base de données

1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com).

![Créer un compte de base de données Cosmos DB](./media/create-cosmosdb-database.png)

2. Cliquez sur **Créer une ressource > Bases de données > Azure Cosmos DB**

![Nouvelle page de compte Cosmos DB](./media/cosmosdb-new-account-page.png)

3. Dans la **page Nouveau compte**, spécifiez les informations **Abonnement** et **Groupe de ressources**. Créez un nom unique pour votre champ **Nom du compte**. Il figurera finalement dans votre nom d’URL d’accès aux données. Pour **API**, sélectionnez **Core(SQL)** et fournissez un **emplacement** proche pour réduire le temps d’accès aux données.
4. Ensuite, cliquez sur **Vérifier + créer**.
5. Une fois la validation réussie, cliquez sur **Créer**.

La création du compte prend quelques minutes. Attendez que le portail affiche la page Congratulations! Your Azure Cosmos DB account was created (Félicitations ! Votre compte Azure Cosmos DB a été créé).

### <a name="add-a-database"></a>Ajouter une base de données

1. Accédez à la page **Explorateur de données** de votre compte Cosmos DB nouvellement créé, puis choisissez **Créer une base de données** dans la zone de liste déroulante en regard du bouton **Créer un conteneur**. Un panneau s’ouvre sur le côté droit de la fenêtre, où vous pouvez entrer les détails du nouveau conteneur. 

![Cosmos DB](./media/create-cosmosdb-database-resource.png)

2. Attribuez un ID à votre nouvelle base de données et, si vous le souhaitez, définissez le débit (vous pouvez le modifier pas la suite), puis cliquez sur **OK** pour créer votre base de données. Notez cet ID de base de données, vous en aurez besoin par la suite pour configurer votre bot.

![Cosmos DB](./media/create-cosmosdb-database-resource-details.png)

3. Maintenant que vous avez créé un compte et une base de données Cosmos DB, vous devez copier certaines des valeurs pour intégrer la nouvelle base de données dans votre bot.  Pour les récupérer, accédez à l’onglet **Clés** dans les paramètres de base de données de votre compte Cosmos DB.  Dans cette page, vous aurez besoin de votre point de terminaison Cosmos DB (**URI**) et de votre clé d’autorisation (**CLÉ PRIMAIRE**).

![Clés Cosmos DB](./media/comos-db-keys.png)

Vous devez maintenant disposer d’un compte Cosmos DB contenant une base de données ainsi que des détails ci-dessous pour configurer votre bot.

- Point de terminaison Cosmos DB
- Clé d’autorisation
- ID de base de données

### <a name="add-configuration-information"></a>Ajouter des informations de configuration
Les données de configuration permettant d’ajouter un stockage Cosmos DB sont succinctes et simples.  Utilisez les détails que vous avez notés dans la section précédente de cet article pour définir votre point de terminaison, la clé d’autorisation et l’ID de base de données.  Enfin, vous devez attribuer un nom approprié au conteneur qui sera créé dans votre base de données pour stocker l’état de votre bot. Dans l’exemple ci-dessous, le conteneur est appelé « bot-storage ».

> [!NOTE]
> Vous ne devez pas créer vous-même le conteneur. Votre bot le créera à votre place, en même temps que son client Cosmos DB, tout en veillant à ce qu’il soit correctement configuré pour le stockage de l’état du bot.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
public class EchoBot : ActivityHandler
{
   private const string CosmosServiceEndpoint = "<your-cosmos-db-URI>";
   private const string CosmosDBKey = "<your-authorization-key>";
   private const string CosmosDBDatabaseId = "<your-database-id>";
   private const string CosmosDBContainerId = "bot-storage";
   ...
   
}
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez les informations suivantes à votre fichier `.env`.

**.env**
```javascript
DB_SERVICE_ENDPOINT="<your-cosmos-db-URI>"
AUTH_KEY="<your-authorization-key>"
DATABASE_ID="<your-database-id>"
CONTAINER="bot-storage"
```

### <a name="pythontabpython"></a>[Python](#tab/python)

Ajoutez les informations suivantes à votre fichier `bot.py`.

```javascript
COSMOSDB_SERVICE_ENDPOINT = "<your-cosmos-db-URI>"
COSMOSDB_KEY = "<your-authorization-key>"
COSMOSDB_DATABASE_ID = "<your-database-id>"
COSMOSDB_CONTAINER_ID = "bot-storage"
```

---

#### <a name="installing-packages"></a>Installation des packages
Vérifiez que vous avez installé les packages nécessaires pour Cosmos DB

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Vous pouvez ajouter des références à botbuilder-azure dans votre projet via npm.
>**Remarque** – Ce package npm s’appuie sur une installation de Python qui existe sur votre machine de développement. Si vous n’avez pas encore installé Python, vous trouverez des ressources d’installation pour votre machine ici : [Python.org](https://www.python.org/downloads/)

```powershell
npm install --save botbuilder-azure 
```

S’il n’est pas encore installé, obtenez le package dotnet à partir de npm afin d’accéder à vos paramètres de fichier `.env`.

```powershell
npm install --save dotenv
```

### <a name="pythontabpython"></a>[Python](#tab/python)

vous pouvez ajouter des références à botbuilder-azure dans votre projet via pip.

```powershell
pip install botbuilder-azure 
```

---

### <a name="implementation"></a>Implémentation 

> [!NOTE]
> La version 4.6 a introduit un nouveau fournisseur de stockage Cosmos DB, `CosmosDbPartitionedStorage`. Les bots existants qui utilisent le `CosmosDbStorage` d’origine doivent continuer d’utiliser `CosmosDbStorage`. Les bots qui utilisent l’ancien fournisseur continuent de fonctionner normalement. Les nouveaux bots doivent utiliser `CosmosDbPartitionedStorage`, car le partitionnement offre de meilleures performances.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple de code suivant s’exécute avec le même code de bot que l’exemple de [stockage mémoire](#memory-storage) fourni ci-dessus.
L’extrait de code ci-dessous illustre une implémentation de stockage Cosmos DB pour « _myStorage_ » qui remplace le stockage mémoire local. Le stockage de mémoire est mis en commentaires et remplacé par une référence à Cosmos DB.

**EchoBot.cs**
```csharp

using System;
...
using Microsoft.Bot.Builder.Azure;
...
public class EchoBot : ActivityHandler
{
   // Create local Memory Storage - commented out.
   // private static readonly MemoryStorage _myStorage = new MemoryStorage();

   // Replaces Memory Storage with reference to Cosmos DB.
   private static readonly CosmosDbStorage _myStorage = new CosmosDbPartitionedStorage(new CosmosDbPartitionedStorageOptions
   {
        CosmosDbEndpoint = CosmosServiceEndpoint,
        AuthKey = CosmosDBKey,
        DatabaseId = CosmosDBDatabaseId,
        ContainerId = CosmosDBContainerId,
   });
   
   ...
}

```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple de code suivant est semblable au [stockage mémoire](#memory-storage), à quelques petites différences près.

Demandez `CosmosDbPartitionedStorage` à partir de `botbuilder-azure` et configurez dotenv pour lire le fichier `.env`.

**bot.js**

```javascript
const { CosmosDbPartitionedStorage } = require("botbuilder-azure");
```
Décommentez Memory Storage, remplacez-le par une référence à Cosmos DB.

**bot.js**
```javascript
// initialized to access values in .env file.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });

// Create local Memory Storage - commented out.
// var storage = new MemoryStorage();

// Create access to CosmosDb Storage - this replaces local Memory Storage.
var storage = new CosmosDbPartitionedStorage({
    cosmosDbEndpoint: process.env.DB_SERVICE_ENDPOINT, 
    authKey: process.env.AUTH_KEY, 
    databaseId: process.env.DATABASE_ID,
    containerId: process.env.CONTAINER
})

```

### <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple de code suivant est semblable au [stockage mémoire](#memory-storage), à quelques petites différences près.

Imposez l’obtention de `CosmosDbStorage` à partir de `botbuilder-azure`, et créez l’objet CosmosDBStorage.

**bot.py**

```py
from botbuilder.azure import CosmosDbStorage, CosmosDbConfig
```

Décommentez le stockage en mémoire dans `__init__`, et remplacez-le par une référence à Cosmos DB.  Utilisez le point de terminaison, la clé d’authentification, l’ID de base de données et l’ID de conteneur employés ci-dessus.

**bot.py**
```py
def __init__(self):
    cosmos_config = CosmosDbConfig(
        endpoint=COSMOSDB_SERVICE_ENDPOINT,
        masterkey=COSMOSDB_KEY,
        database=COSMOSDB_DATABASE_ID,
        container=COSMOSDB_CONTAINER_ID
    )
    self.storage = CosmosDbStorage(cosmos_config)
```

---

## <a name="start-your-bot"></a>Démarrer votre robot
Exécutez votre bot en local.

## <a name="test-your-bot-with-bot-framework-emulator"></a>Tester votre bot à l’aide de Bot Framework Emulator
Démarrez Bot Framework Emulator et connectez-vous à votre bot :

1. Cliquez sur le lien **Create new bot configuration** sous l’onglet « Welcome » de l’émulateur. 
2. Renseignez les champs pour vous connecter à votre bot en utilisant les informations figurant dans la page web affichée lorsque vous avez démarré votre bot.

## <a name="interact-with-your-bot"></a>Interagir avec votre bot
Envoyez un message à votre bot, et le bot va répertorier les messages qu’il reçoit.
![Émulateur en cours d’exécution](./media/emulator-direct-storage-test.png)


### <a name="view-your-data"></a>Affichage de vos données
Une fois que vous avez exécuté votre bot et enregistré vos informations, nous pouvons visualiser les données stockées dans le portail Azure, sous l’onglet **Explorateur de données**. 

![Exemple de l’Explorateur de données](./media/data_explorer.PNG)


## <a name="using-blob-storage"></a>Utilisation du stockage Blob 
Le stockage Blob Azure est la solution de stockage d’objet de Microsoft pour le cloud. Le stockage Blob est optimisé pour stocker de grandes quantités de données non structurées, telles que des données texte ou binaires.

### <a name="create-your-blob-storage-account"></a>Créer votre compte de stockage Blob
Pour utiliser le stockage Blob dans votre bot, vous devez configurer certaines choses avant d’aborder le code.
1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com).

![Créer un stockage Blob](./media/create-blob-storage.png)

2. Cliquez sur **Créer une ressource > Stockage > Compte de stockage - blob, fichier, table, file d’attente**

![Page Nouveau compte de stockage d’objets blob](./media/blob-storage-new-account.png)

3. Sur la **page Nouveau compte**, tapez le **nom** du compte de stockage, sélectionnez **Stockage d’objets blob** pour **Type de compte**, puis fournissez les informations relatives aux champs **Emplacement**, **Groupe de ressources** et **Abonnement**.  
4. Ensuite, cliquez sur **Vérifier + créer**.
5. Une fois la validation réussie, cliquez sur **Créer**.

### <a name="create-blob-storage-container"></a>Créer un conteneur de stockage d’objets blob
Une fois que votre compte de stockage d’objets blob a été créé, ouvrez ce compte en effectuant les opérations suivantes : 
1. Sélectionnez la ressource.
2. Ouvrez à l’aide de l’Explorateur Stockage (préversion).

![Créer un conteneur de stockage d’objets blob](./media/create-blob-container.png)

3. Cliquez avec le bouton droit sur BLOB CONTAINERS et sélectionnez _Créer un conteneur d'objets blob_ .
4. Ajoutez un nom. Vous utiliserez ce nom pour la valeur « your-blob-storage-container-name » afin de permettre l’accès à votre compte de stockage d’objets blob.

#### <a name="add-configuration-information"></a>Ajouter des informations de configuration
Recherchez les clés de Stockage Blob dont vous avez besoin pour configurer le Stockage Blob de votre bot, comme indiqué ci-dessus :
1. Dans le Portail Azure, ouvrez votre compte Stockage Blob et sélectionnez **Paramètres > Clés d’accès**.

![Rechercher des clés de Stockage Blob](./media/find-blob-storage-keys.png)

Vous utiliserez la _chaîne de connexion_ key1 comme valeur « your-blob-storage-container-name » pour permettre l’accès à votre compte de stockage d’objets blob.

#### <a name="installing-packages"></a>Installation des packages
Si vous ne les avez pas déjà installés pour utiliser Cosmos DB, installez les packages suivants.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```powershell
Install-Package Microsoft.Bot.Builder.Azure
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez des références à botbuilder-azure dans votre projet via npm.
>**Remarque** – Ce package npm s’appuie sur une installation de Python qui existe sur votre machine de développement. Si vous n’avez pas encore installé Python, vous trouverez des ressources d’installation pour votre machine ici : [Python.org](https://www.python.org/downloads/)

```powershell
npm install --save botbuilder-azure 
```

S’il n’est pas encore installé, obtenez le package dotnet à partir de npm afin d’accéder à vos paramètres de fichier `.env`.

```powershell
npm install --save dotenv
```

### <a name="pythontabpython"></a>[Python](#tab/python)

vous pouvez ajouter des références à botbuilder-azure dans votre projet via pip.

```powershell
pip install botbuilder-azure 
```

---

### <a name="implementation"></a>Implémentation 

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

**EchoBot.cs**
```csharp
using Microsoft.Bot.Builder.Azure;
```
Mettez à jour la ligne de code qui pointe « _myStorage_ » vers votre compte Stockage Blob existant.

**EchoBot.cs**
```csharp
private static readonly AzureBlobStorage _myStorage = new AzureBlobStorage("<your-blob-storage-account-string>", "<your-blob-storage-container-name>");
```

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez les informations suivantes à votre fichier `.env`.

**.env**
```javascript
BLOB_NAME="<your-blob-storage-container-name>"
BLOB_STRING="<your-blob-storage-account-string>"
```

Mettez à jour votre fichier `bot.js` comme suit. Demandez `BlobStorage` à partir de `botbuilder-azure`

**bot.js**
```javascript
const { BlobStorage } = require("botbuilder-azure");
```

Si vous n’avez pas ajouté de code pour charger votre fichier `.env` afin de mettre en œuvre le stockage Cosmos DB, ajoutez ceci ici.

```javascript
// initialized to access values in .env file.
const ENV_FILE = path.join(__dirname, '.env');
require('dotenv').config({ path: ENV_FILE });
```
À présent, mettez à jour votre code afin de pointer « _storage_ » sur votre compte de stockage d’objets blob existant, en mettant en commentaires les définitions de stockage précédentes et en ajoutant le code suivant.

**bot.js**
```javascript
var storage = new BlobStorage({
    containerName: process.env.BLOB_NAME,
    storageAccountOrConnectionString: process.env.BLOB_STRING
});
```



### <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple de code suivant est semblable au [stockage mémoire](#memory-storage), à quelques petites différences près.

Imposez l’obtention de `BlobStorage` à partir de `botbuilder-azure`, et créez l’objet CosmosDBStorage.

**bot.py**

```py
from botbuilder.azure import BlobStorage, BlobStorageSettings
```

Décommentez le stockage en mémoire dans `__init__`, et remplacez-le par une référence à Cosmos DB.  Utilisez le nom du conteneur et la chaîne de connexion employés ci-dessus.

**bot.py**
```py
def __init__(self):
    blob_settings = BlobStorageSettings(
        container_name="<your_container_name>",
        connection_string="<your_connection_string>"
    )
    self.storage = BlobStorage(blob_settings)
```

---

Une fois que votre stockage est défini pour pointer sur votre compte de stockage d’objets blob, le code de votre bot va maintenant stocker et récupérer des données depuis le stockage d’objets blob.

## <a name="start-your-bot"></a>Démarrer votre robot
Exécutez votre bot en local.

## <a name="start-the-emulator-and-connect-your-bot"></a>Démarrer l’émulateur et connecter votre robot
Démarrez à présent l’émulateur, puis connectez-vous à votre bot dans l’émulateur :

1. Cliquez sur le lien **Create new bot configuration** sous l’onglet « Welcome » de l’émulateur. 
2. Renseignez les champs pour vous connecter à votre bot en utilisant les informations figurant dans la page web affichée lorsque vous avez démarré votre bot.

## <a name="interact-with-your-bot"></a>Interagir avec votre bot
Envoyez un message à votre bot, suite à quoi le bot listera les messages qu’il reçoit.

![Bot de stockage de test d’émulateur](./media/emulator-direct-storage-test.png)

### <a name="view-your-data"></a>Affichage de vos données
Une fois que vous avez exécuté votre bot et enregistré vos informations, nous pouvons les afficher sous l’onglet **Explorateur de données** du Portail Azure.

## <a name="blob-transcript-storage"></a>Stockage de transcriptions d’objets blob
Le stockage de transcriptions d’objets blob Azure fournit une option de stockage spécialisée qui vous permet d’enregistrer et de récupérer facilement des conversations d’utilisateur sous la forme d’une transcription enregistrée. Le stockage de transcriptions d’objets blob Azure est particulièrement utile pour capturer automatiquement les entrées utilisateur à examiner lors du débogage des performances de votre bot.

**REMARQUE : JavaScript et Python ne prennent pas en charge AzureBlobTranscriptStore.  Les instructions suivantes s’appliquent uniquement au C#**

### <a name="set-up"></a>Configurer
Le stockage de transcriptions d’objets blob Azure peut utiliser le même compte de stockage Blob créé en suivant les étapes détaillées dans les sections « _Créer votre compte de stockage Blob_ » et « _Ajouter des informations de configuration_ ». Un conteneur est maintenant ajouté pour stocker vos transcriptions.


![Créer un conteneur de transcriptions](./media/create-blob-transcript-container.png)

1. Ouvrez votre compte de stockage d’objets blob Azure.
1. Cliquez sur _Explorateur Stockage_.
1. Cliquez avec le bouton droit sur _BLOB CONTAINERS_ et sélectionnez _Créer un conteneur d’objets blob_.
1. Entrez un nom pour votre conteneur de transcriptions, puis sélectionnez _OK_. (Nous avons saisi mybottranscripts.)

### <a name="implementation"></a>Implémentation 
Le code suivant connecte le pointeur de stockage de transcriptions `_myTranscripts` à votre nouveau compte de stockage de transcriptions d’objets blob Azure. Pour créer ce lien avec un nouveau nom de conteneur, <your-blob-transcript-container-name>, créez un conteneur dans le stockage d’objets blob pour stocker vos fichiers de transcription.

**echoBot.cs**
```csharp
using Microsoft.Bot.Builder.Azure;

public class EchoBot : ActivityHandler
{
   ...
   
   private readonly AzureBlobTranscriptStore _myTranscripts = new AzureBlobTranscriptStore("<your-blob-transcript-storage-account-string>", "<your-blob-transcript-container-name>");
   
   ...
}

```

### <a name="store-user-conversations-in-azure-blob-transcripts"></a>Stocker les conversations utilisateur dans les transcriptions d’objets blob Azure
Une fois qu’un conteneur d’objets blob est disponible pour stocker les transcriptions, vous pouvez commencer à conserver les conversations de vos utilisateurs avec votre bot. Ces conversations peuvent être utilisées plus tard en tant qu’outil de débogage pour observer la façon dont les utilisateurs interagissent avec votre bot. Chaque émulateur _Redémarrer la conversation_ lance la création d’une nouvelle liste de conversation de transcription. Le code suivant conserve les entrées de conversation utilisateur dans un fichier de transcription stocké.
- La transcription actuelle est enregistrée à l’aide de `LogActivityAsync`.
- Les transcriptions enregistrées sont récupérées à l’aide de `ListTranscriptsAsync`.
Dans cet exemple de code, l’ID de chaque transcription stockée est enregistrée dans une liste nommée « storedTranscripts ». Cette liste est utilisée ultérieurement pour gérer le nombre de transcriptions d’objet blob stockées que nous conservons.

**echoBot.cs**
```csharp

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    await _myTranscripts.LogActivityAsync(turnContext.Activity);

    List<string> storedTranscripts = new List<string>();
    PagedResult<Microsoft.Bot.Builder.TranscriptInfo> pagedResult = null;
    var pageSize = 0;
    do
    {
       pagedResult = await _myTranscripts.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
       pageSize = pagedResult.Items.Count();

       // transcript item contains ChannelId, Created, Id.
       // save the channelIds found by "ListTranscriptsAsync" to a local list.
       foreach (var item in pagedResult.Items)
       {
          storedTranscripts.Add(item.Id);
       }
    } while (pagedResult.ContinuationToken != null);
    
    ...
}

```

### <a name="manage-stored-blob-transcripts"></a>Gérer les transcriptions d’objet blob stockées
Les transcriptions stockées peuvent être utilisées comme outil de débogage, mais, au fil du temps, le nombre de transcriptions stockées peut dépasser le nombre que vous voulez conserver. Le code supplémentaire inclus ci-dessous utilise `DeleteTranscriptAsync` pour supprimer tous les éléments de transcription récupérés de votre magasin de transcriptions d’objet blob, sauf les trois derniers.

**echoBot.cs**
```csharp

protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    await _myTranscripts.LogActivityAsync(turnContext.Activity);

    List<string> storedTranscripts = new List<string>();
    PagedResult<Microsoft.Bot.Builder.TranscriptInfo> pagedResult = null;
    var pageSize = 0;
    do
    {
       pagedResult = await _myTranscripts.ListTranscriptsAsync("emulator", pagedResult?.ContinuationToken);
       pageSize = pagedResult.Items.Count();

       // transcript item contains ChannelId, Created, Id.
       // save the channelIds found by "ListTranscriptsAsync" to a local list.
       foreach (var item in pagedResult.Items)
       {
          storedTranscripts.Add(item.Id);
       }
    } while (pagedResult.ContinuationToken != null);
    
    // Manage the size of your transcript storage.
    for (int i = 0; i < pageSize; i++)
    {
       // Remove older stored transcripts, save just the last three.
       if (i < pageSize - 3)
       {
          string thisTranscriptId = storedTranscripts[i];
          try
          {
             await _myTranscripts.DeleteTranscriptAsync("emulator", thisTranscriptId);
           }
           catch (System.Exception ex)
           {
              await turnContext.SendActivityAsync("Debug Out: DeleteTranscriptAsync had a problem!");
              await turnContext.SendActivityAsync("exception: " + ex.Message);
           }
       }
    }
    ...
}

```

Le lien suivant fournit plus d’informations sur le [stockage de transcriptions d’objet blob Azure](https://docs.microsoft.com/dotnet/api/microsoft.bot.builder.azure.azureblobtranscriptstore) 

## <a name="additional-information"></a>Informations supplémentaires

### <a name="manage-concurrency-using-etags"></a>Gérer la concurrence à l’aide des étiquettes d’entité
Dans notre exemple de code de bot, nous définissons la propriété `eTag` de chaque `IStoreItem` sur `*`. L’élément `eTag` (étiquette d’entité) de votre objet de stockage est utilisé au sein de Cosmos DB pour gérer la concurrence. L’élément `eTag` indique à votre base de données ce qu’il faut faire si une autre instance du bot modifie l’objet de stockage sur lequel votre bot écrit. 

<!-- define optimistic concurrency -->

#### <a name="last-write-wins---allow-overwrites"></a>La dernière écriture prévaut : autorise les remplacements
Une valeur de propriété `eTag` de l’astérisque (`*`) indique que celui qui prévaut est le dernier qui écrit. Lorsque vous créez une nouvelle banque de données, vous pouvez définir l’élément `eTag` d’une propriété à `*` pour indiquer que vous n’avez pas précédemment enregistré les données que vous écrivez, ou que vous voulez que le dernier auteur remplace toutes les propriétés précédemment enregistrées. Si la concurrence ne constitue pas un problème pour votre bot, le remplacement est autorisé si vous définissez la propriété `eTag` sur `*` pour toutes les données que vous écrivez.

#### <a name="maintain-concurrency-and-prevent-overwrites"></a>Gérer la concurrence et empêcher les remplacements
Lorsque vous stockez vos données dans Cosmos DB, utilisez une valeur autre que `*` pour l’élément `eTag` si vous souhaitez empêcher l’accès simultané à une propriété et éviter de remplacer les modifications apportées par une autre instance du bot. Le bot reçoit une réponse d’erreur avec le message `etag conflict key=` quand il tente d’enregistrer des données d’état et que la valeur de l’élément `eTag` est différente de celle de l’élément `eTag` qui se trouve dans le stockage. <!-- To control concurrency of data that is stored using `IStorage`, the BotBuilder SDK checks the entity tag (ETag) for `Storage.Write()` requests. -->

Par défaut, le magasin Cosmos DB vérifie que la propriété `eTag` d’un objet de stockage reste la même chaque fois qu’un bot écrit dessus, puis la met à jour avec une nouvelle valeur unique après chaque écriture. Si la propriété `eTag` d’écriture ne correspond pas à la propriété `eTag` de stockage, un autre robot ou thread a donc modifié les données. 

Par exemple, si vous souhaitez que votre robot modifie une note enregistrée, mais que vous ne voulez pas qu’il remplace les modifications apportées par une autre instance du robot. Si une autre instance du robot a apporté des modifications, vous souhaitez que l’utilisateur modifie la version avec les dernières mises à jour.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Commencez par créer une classe qui implémente `IStoreItem`.

**EchoBot.cs**
```csharp
public class Note : IStoreItem
{
    public string Name { get; set; }
    public string Contents { get; set; }
    public string ETag { get; set; }
}
```

Créez ensuite une première note avec un nouvel objet de stockage que vous a ajoutez à la banque.

**EchoBot.cs**
```csharp
// create a note for the first time, with a non-null, non-* ETag.
var note = new Note { Name = "Shopping List", Contents = "eggs", ETag = "x" };

var changes = Dictionary<string, object>();
{
    changes.Add("Note", note);
};
await NoteStore.WriteAsync(changes, cancellationToken);
```

Accédez à la note et actualisez-la ultérieurement, en gardant le `eTag` que vous lisez dans la banque.

**EchoBot.cs**
```csharp
var note = NoteStore.ReadAsync<Note>("Note").Result?.FirstOrDefault().Value;

if (note != null)
{
    note.Contents += ", bread";
    var changes = new Dictionary<string, object>();
    {
         changes.Add("Note1", note);
    };
    await NoteStore.WriteAsync(changes, cancellationToken);
}
```

Si la note a été actualisée dans la banque avant que vous n’écriviez vos modifications, l’appel à `Write` lance une exception.


### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Ajoutez une fonction d’assistance à la fin de votre bot, qui écrira une note de l’exemple dans un magasin de données.
Commencez par créer l’objet `myNoteData`.

**bot.js**
```javascript
// Helper function for writing a sample note to a data store
async function createSampleNote(storage, context) {
    var myNoteData = {
        name: "Shopping List",
        contents: "eggs",
        // If any Note file is already stored, the eTag field
        // must be set to "*" in order to allow writing without first reading the stored eTag
        // otherwise you'll likely get an exception indicating an eTag conflict. 
        eTag: "*"
    }
}
```

Dans la fonction d’assistance `createSampleNote`, initialisez un objet `changes` et ajoutez vos *notes*, puis écrivez-le dans le stockage.

**bot.js**
```javascript
// Write the note data to the "Note" key
var changes = {};
changes["Note"] = myNoteData;
// Creates a file named Note, if it doesn't already exist.
// specifying eTag= "*" will overwrite any existing contents.
// The act of writing to the file automatically updates the eTag property
// The first time you write to Note, the eTag is changed from *, and file contents will become:
//    {"name":"Shopping List","contents":"eggs","eTag":"1"}
try {
     await storage.write(changes);
     var list = changes["Note"].contents;
     await context.sendActivity(`Successful created a note: ${list}`);
} catch (err) {
     await context.sendActivity(`Could not create note: ${err}`);
}
```

La fonction d’assistance est accessible à partir de la logique de votre bot en ajoutant l’appel suivant :

**bot.js**
```javascript
// Save a note with etag.
await createSampleNote(storage, turnContext);
```

Une fois la note créée, pour l’extraire et la mettre à jour ultérieurement, nous créons une autre fonction d’assistance accessible quand l’utilisateur tape « update note » (mettre à jour la note).

**bot.js**
```javascript
async function updateSampleNote(storage, context) {
    try {
        // Read in a note
        var note = await storage.read(["Note"]);
        console.log(`note.eTag=${note["Note"].eTag}\n note=${JSON.stringify(note)}`);
        // update the note that we just read
        note["Note"].contents += ", bread";
        console.log(`Updated note=${JSON.stringify(note)}`);

        try {
             await storage.write(note); // Write the changes back to storage
             var list = note["Note"].contents;
             await context.sendActivity(`Successfully updated note: ${list}`);
        } catch (err) {            
             console.log(`Write failed: ${err}`);
        }
    }
    catch (err) {
        await context.sendActivity(`Unable to read the Note: ${err}`);
    }
}
```

Cette fonction d’assistance est accessible à partir de la logique de votre bot en ajoutant l’appel suivant :

**bot.js**
```javascript
// Update a note with etag.
await updateSampleNote(storage, turnContext);
```

Si la note est mise à jour dans le magasin par un autre utilisateur avant que vous tentiez de mettre à jour vos modifications, la valeur `eTag` ne correspondra plus et l’appel à `write` lèvera une exception.

### <a name="pythontabpython"></a>[Python](#tab/python)

Commencez par créer une classe qui implémente `StoreItem`.

**bot.py**
```py
class Note(StoreItem):
    def __init__(self, name: str, contents: str, e_tag="*"):
        super(Note, self).__init__()
        self.name = name
        self.contents = contents
        self.e_tag = e_tag
```

Créez ensuite une première note avec un nouvel objet de stockage que vous a ajoutez à la banque.

**bot.py**
```py
# create a note for the first time, with a non-null, non-* ETag.
changes = {"Note": Note(name="Shopping List", contents="eggs", e_tag="x")}

await self.storage.write(changes)
```

Accédez à la note et actualisez-la ultérieurement, en gardant le `eTag` que vous lisez dans la banque.

**bot.py**
```py
store_items = await self.storage.read(["Note"])
    note = store_items["Note"]
    note.contents = note.contents + ", bread"

    changes = {"Note": note}
    await self.storage.write(changes)
```

Si la note a été actualisée dans la banque avant que vous n’écriviez vos modifications, l’appel à `write` lance une exception.

---

Pour gérer la concurrence, lisez toujours une propriété à partir du stockage, puis modifiez la propriété que vous lisez afin de conserver `eTag`. Si vous lisez les données de l’utilisateur dans la banque, la réponse contient la propriété de l’étiquette d’entité. Si vous modifiez les données et que vous enregistrez les nouvelles données dans la banque, votre requête doit inclure la propriété de l’étiquette d’entité qui indique la même valeur que celle que vous avez lue précédemment. Lorsque l’élément `eTag` d’un objet est défini à `*`, il est toutefois possible de l’enregistrer en remplaçant les autres modifications.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment lire et écrire directement dans le stockage, voyons comment effectuer ces opérations à l’aide du gestionnaire d’état.

> [!div class="nextstepaction"]
> [Enregistrer l’état à l’aide des propriétés de la conversation et de l’utilisateur](bot-builder-howto-v4-state.md)
