---
title: Fonctionnement des bots | Microsoft Docs
description: Décrit le fonctionnement des activités et de http dans le kit SDK Bot Framework.
keywords: flux de conversation, tour, conversation du bot, dialogues, invites, cascades, jeu de dialogues
author: johnataylor
ms.author: johtaylo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0e8a8275a7ede599b3d25576abcd3c1160873db7
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70167200"
---
# <a name="how-bots-work"></a>Fonctionnement des bots

[!INCLUDE[applies-to](../includes/applies-to.md)]

Un bot est une application avec laquelle les utilisateurs interagissent par le biais d’une conversation textuelle, graphique (cartes ou images) ou vocale. Chaque interaction entre l’utilisateur et le bot génère une *activité*. Bot Framework Service, qui est un composant d’Azure Bot Service, envoie des informations entre l’application connectée au bot de l’utilisateur (Facebook, Skype, Slack, etc., que nous appelons le *canal*) et le bot. Chaque canal peut inclure des informations supplémentaires dans les activités qu’ils envoient. Avant de créer des bots, il est important de comprendre comment un bot utilise les objets d’activité pour communiquer avec ses utilisateurs. Examinons tout d’abord les activités qui sont échangées lorsque nous exécutons un simple bot d’écho. 

![diagramme d’activités](media/bot-builder-activity.png)

Voici les deux types d’activités illustrés ici : *mise à jour de conversation* et *message*.

Le service Bot Framework peut envoyer une mise à jour de conversation lorsque quelqu’un rejoint la conversation. Par exemple, au démarrage d’une conversation avec l’émulateur Bot Framework, vous verrez deux activités de mise à jour de conversation (une pour l’utilisateur rejoignant la conversation et l’autre pour le bot rejoignant la conversation). Pour distinguer ces activités de mise à jour de conversation, vérifiez si la propriété *members added* inclut un membre autre que le robot. 

L’activité de message transfère les informations de conversation entre les parties. Dans un exemple de bot d’écho, les activités de message transfèrent du texte simple et le canal rend ce texte. L’activité de message peut également contenir du texte à prononcer, des actions suggérées ou des cartes à afficher.

Dans cet exemple, le bot crée et envoie une activité de message en réponse à l’activité de message entrant reçu. Toutefois, un robot peut répondre par d’autres moyens à une activité de message reçu. Il n’est pas rare qu’un robot répondre à une activité de mise à jour de conversation en envoyant un texte de bienvenue dans une activité de message. La section [Accueillir l’utilisateur](bot-builder-welcome-user.md) fournit plus d’informations à ce sujet.

### <a name="http-details"></a>Détails HTTP

Les activités arrivent dans le bot à partir du service Bot Framework via une requête HTTP POST. Le bot répond à la requête POST entrante avec un code d’état HTTP 200. Les activités sont envoyées du bot au canal dans une requête HTTP POST séparée vers le service Bot Framework. Leur réception génère un code d’état HTTP 200.

Le protocole ne spécifie pas l’ordre dans lequel ces demandes POST et leurs accusés de réception sont transmis. Toutefois, pour s’adapter aux infrastructures de service HTTP courantes, ces demandes sont généralement imbriquées, ce qui signifie que la requête HTTP sortante est effectuée à partir du bot dans le périmètre de la requête HTTP entrante. Ce schéma de fonctionnement est illustré dans le diagramme ci-dessus. Dans la mesure où il existe deux connexions HTTP distinctes à la suite, le modèle de sécurité doit couvrir les deux.

### <a name="defining-a-turn"></a>Définition d’un tour

Dans une conversation, les gens parlent un à la fois, tour à tour. En général, un bot réagit à l’entrée utilisateur. Dans le kit SDK Bot Framework, un _tour_ désigne l’activité entrante de l’utilisateur dans le bot et toute activité que le bot renvoie à l’utilisateur comme réponse immédiate. Imaginez un tour comme le traitement associé à l’arrivée d’une activité donnée.

L’objet de *contexte de tour* fournit des informations sur l’activité, comme l’expéditeur et le destinataire, le canal, et d’autres données nécessaires pour traiter l’activité. Il permet également d’ajouter des informations pendant le tour entre les différentes couches du bot.

Le contexte de tour est l’une des abstractions les plus importantes du SDK. Il transfère non seulement l’activité entrante à tous les composants d’intergiciel et à la logique d’application, mais il fournit également le mécanisme par lequel les composants d’intergiciel et la logique d’application peuvent envoyer des activités sortantes.

## <a name="the-activity-processing-stack"></a>Pile de traitement d’une activité

Nous allons explorer le diagramme précédent en nous concentrant sur l’arrivée d’une activité de message.

![pile de traitement d’une activité](media/bot-builder-activity-processing-stack.png)

Dans l’exemple ci-dessus, le bot a répondu à l’activité de message avec une autre activité de message contenant le même message texte. Le traitement commence par la requête HTTP POST. Les informations de l’activité sont transportées sous la forme d’une charge JSON arrivant sur le serveur Web. En C#, il s’agit généralement d’un projet ASP.NET. Dans un projet JavaScript Node.js, il s’agit souvent d’une infrastructure populaire comme Express ou Restify.

L’*adaptateur*, un composant intégré du Kit de développement logiciel (SDK), est l’élément central du runtime du Kit de développement logiciel (SDK). L’activité est transmise sous forme de code JSON dans le corps HTTP POST. Ce code JSON est désérialisé pour créer l’objet d’activité qui est ensuite transmis à l’adaptateur en appelant la méthode de *traitement d’activité*. Lors de la réception de l’activité, l’adaptateur crée un *contexte de tour* et appelle l’intergiciel. 

Comme mentionné ci-dessus, le contexte de tour fournit le mécanisme pour permettre au bot d’envoyer des activités sortantes, le plus souvent en réponse à une activité entrante. Pour ce faire, le contexte de tour fournit des méthodes de réponse _d’envoi, de mise à jour et de suppression d’activité_. Chaque méthode de réponse s’exécute dans un processus asynchrone. 

[!INCLUDE [alert-await-send-activity](../includes/alert-await-send-activity.md)]

## <a name="activity-handlers"></a>Gestionnaires d’activités

Lorsque le bot reçoit une activité, il la transmet à son *gestionnaire d’activités*. En réalité, il existe un gestionnaire de base, nommé le *gestionnaire de tours*. Toutes les activités sont acheminées par son intermédiaire. Ce gestionnaire de tours appelle ensuite le gestionnaire d’activité individuelle pour le type d’activité qu’il a reçu.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Par exemple, si le bot reçoit une activité de message, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités `OnMessageActivityAsync`. 

Lors de la création du bot, votre logique de bot pour la gestion des messages et leur réponse passe dans ce gestionnaire `OnMessageActivityAsync`. De la même façon, votre logique pour la gestion des membres ajoutés à la conversation va dans votre gestionnaire `OnMembersAddedAsync`, qui est appelé chaque fois qu’un membre est ajouté à la conversation.

Pour implémenter votre logique sur ces gestionnaires, vous substituez ces méthodes dans votre bot, comme expliqué dans la section [Logique de bot](#bot-logic) ci-dessous. Comme aucun de ces gestionnaires ne dispose d’une implémentation de base, ajoutez simplement la logique que vous voulez dans votre remplacement.

Il peut arriver que vous souhaitiez remplacer le gestionnaire de tours standard, tels que l’[enregistrement de l’état](bot-builder-concept-state.md) à la fin d’un tour. Lors de cette opération, veillez à appeler en premier `await base.OnTurnAsync(turnContext, cancellationToken);` pour vous assurer que l’implémentation de base de `OnTurnAsync` est exécutée avant votre code supplémentaire. Cette implémentation de base est, entre autres choses, chargée d’appeler le reste des gestionnaires d’activités, tels que `OnMessageActivityAsync`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Par exemple, si le bot reçoit une activité de message, le gestionnaire de tours voit cette activité entrante et l’envoie au gestionnaire d’activités `onMessage`.

Lors de la création du bot, votre logique de bot pour la gestion des messages et leur réponse passe dans ce gestionnaire `onMessage`. De la même façon, votre logique pour la gestion des membres ajoutés à la conversation va dans votre gestionnaire `onMembersAdded`, qui est appelé chaque fois qu’un membre est ajouté à la conversation.

Pour implémenter votre logique sur ces gestionnaires, vous substituez ces méthodes dans votre bot, comme expliqué dans la section [Logique de bot](#bot-logic) ci-dessous. Pour chacun de ces gestionnaires, définissez votre logique de bot, puis **veillez à appeler `next()` à la fin**. En appelant `next()` vous êtes certain que le gestionnaire suivant est exécuté.

Généralement, il n’existe pas de situation qui justifie de vouloir remplacer le gestionnaire de tours standard, alors faites preuve de prudence si vous tentez de le faire. Pour des choses telles que l’[enregistrement de l’état](bot-builder-concept-state.md), que vous souhaitez faire à la fin d’un tour, il existe un gestionnaire spécial appelé `onDialog`. Le gestionnaire `onDialog` s’exécute à la fin, après l’exécution des gestionnaires restants, et il n’est pas lié à un certain type d’activité. Comme pour tous les gestionnaires ci-dessus, veillez à appeler `next()` pour garantir la clôture du reste du processus.

---

## <a name="middleware"></a>Middlewares

Les intergiciels sont très similaires à n’importe quel autre intergiciel de messagerie, et comprennent un ensemble linéaire de composants qui sont exécutés dans un ordre précis, ce qui donne à chacun une chance d’agir sur l’activité. La dernière étape du pipeline d’intergiciels est un rappel au gestionnaire de tours, sur la classe du bot avec laquelle l’application s’est inscrite auprès de la méthode de *traitement d’activité* de l’adaptateur. Le gestionnaire de tours est généralement `OnTurnAsync` en C#, et `onTurn` en JavaScript.

Le gestionnaire de tours prend un contexte de tour comme argument, en général la logique d’application s’exécutant sur la fonction du gestionnaire de tours traite le contenu de l’activité entrante et génère une ou plusieurs activités en réponse, en les envoyant via la fonction *d’envoi d’activité* sur le contexte de tour. Appelez l’*envoi d’activité* sur le contexte de tour entraîne l’appel des composants des intergiciels sur les activités sortantes. Les composants des intergiciels s’exécutent avant et après la fonction du gestionnaire de tours du bot. Par nature, l’exécution est imbriquée, à l’image d’une poupée russe. Pour plus d’informations sur les intergiciels, consultez [la rubrique consacrée aux intergiciels](~/v4sdk/bot-builder-concept-middleware.md).

## <a name="bot-structure"></a>Structure du bot

Dans les sections suivantes, nous examinons les _éléments clés_ d’un EchoBot que vous pouvez créer facilement à l’aide des modèles fournis pour [**CSharp**](../dotnet/bot-builder-dotnet-sdk-quickstart.md) ou [**JavaScript**](../javascript/bot-builder-javascript-quickstart.md).

<!--Need to add section calling out the controller in code, and explaining it further-->

Un bot est une application web, pour laquelle nous fournissons des modèles dans chaque langage.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le modèle VSIX génère une application web [ASP.NET MVC Core](https://dotnet.microsoft.com/apps/aspnet/mvc). Si vous étudiez les notions de base [d’ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/index?view=aspnetcore-2.1&tabs=aspnetcore2x), vous découvrez qu’il existe un code similaire dans les fichiers tels que **Program.cs** et **Startup.cs**. Ces fichiers sont requis pour toutes les applications web et ne sont pas propres à un bot.

### <a name="appsettingsjson-file"></a>fichier appsettings.json

Le fichier **appsettings.json** spécifie les informations de configuration de votre bot, notamment l’ID d’application et le mot de passe associé. Si vous utilisez certaines technologies ou ce bot en production, vous devrez ajouter vos propres clés ou URL à cette configuration. Toutefois, pour les besoins de ce bot Echo, vous n’avez rien à faire à ce stade ; l’ID de l’application et le mot de passe peuvent rester non définis pour l’instant.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!-- TODO: Update this aka link to point to samples/javascript_nodejs/02.echobot (instead of samples/javascript_nodejs/02.a.echobot) once work-in-progress is merged into master. -->

Le générateur Yeoman crée un type d’application web [restify](http://restify.com/). Si vous consultez le démarrage rapide dédié à restify dans la documentation, vous pouvez voir une application similaire au fichier **index.js** généré. Nous décrivons certains des fichiers clés générés par le modèle. Le code de certains fichiers n’est pas copié, mais vous pouvez le voir en exécutant le bot ; vous pouvez également consulter l’exemple [Node.js echobot](https://aka.ms/js-echobot-sample).

### <a name="packagejson"></a>package.json

Le fichier **package.json** spécifie les dépendances et leurs versions associées pour votre bot. Toutes ces informations sont configurées par le modèle et par votre système.

### <a name="env-file"></a>Fichier .env

Le fichier **.env** spécifie les informations de configuration de votre bot, notamment le numéro de port, l’ID de l’application et le mot de passe. Si vous utilisez certaines technologies ou ce bot en production, vous devrez ajouter vos propres clés ou URL à cette configuration. Toutefois, pour les besoins de ce bot Echo, vous n’avez rien à faire à ce stade ; l’ID de l’application et le mot de passe peuvent rester non définis pour l’instant.

Pour utiliser le fichier de configuration **.env**, le modèle doit inclure un package supplémentaire.  Commencez par obtenir le package `dotenv` à partir de npm :

`npm install dotenv`

---

### <a name="bot-logic"></a>Logique du bot

La logique du bot traite les activités entrantes à partir d’un ou de plusieurs canaux, et génère des activités sortantes en réponse.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

La logique principale du bot est définie dans le code du bot, appelé ici `Bots/EchoBot.cs`. `EchoBot` dérive de `ActivityHandler`, qui à son tour dérive de l’interface `IBot`. `ActivityHandler` définit divers gestionnaires pour différents types d’activités, telles que les deux définis ici : `OnMessageActivityAsync` et `OnMembersAddedAsync`. Ces méthodes sont protégées, mais peuvent être remplacées dans la mesure où nous dérivons de `ActivityHandler`.

Les gestionnaires définis dans `ActivityHandler` sont :

| Événement | Handler | Description |
| :-- | :-- | :-- |
| Tout type d’activité reçu | `OnTurnAsync` | Appelle l’un des autres gestionnaires, selon le type d’activité reçu. |
| Activité de message reçue | `OnMessageActivityAsync` | Remplacer celui-ci pour gérer une activité `message`. |
| Activité de mise à jour de conversation reçue | `OnConversationUpdateActivityAsync` | Sur une activité `conversationUpdate`, appelle un gestionnaire si des membres autres que le bot ont rejoint ou quitté la conversation. |
| Des membres autres que le bot ont rejoint la conversation | `OnMembersAddedAsync` | Substituer celui-ci pour gérer les membres se joignant à une conversation. |
| Des membres autres que le bot ont quitté la conversation | `OnMembersRemovedAsync` | Substituer celui-ci pour gérer les membres quittant une conversation. |
| Activité d’événement reçue | `OnEventActivityAsync` | Sur une activité `event`, appelle un gestionnaire spécifique au type d’événement. |
| Activité reçue d’événement de réponse de jeton | `OnTokenResponseEventAsync` | Substituer celui-ci pour gérer les événements de réponse de jeton. |
| Activité reçue d’événement de réponse autre que celle du jeton | `OnEventAsync` | Substituer celui-ci pour gérer d’autres types d’événements. |
| Activité de réaction à un message reçue | `OnMessageReactionActivityAsync` | Sur une activité `messageReaction`, appelle un gestionnaire si une ou plusieurs réactions ont été ajoutées ou supprimées d’un message. |
| Réactions de message ajoutées à un message | `OnReactionsAddedAsync` | Remplacez ceci pour gérer les réactions ajoutées à un message. |
| Réactions de message supprimées d’un message | `OnReactionsRemovedAsync` | Remplacez ceci pour gérer les réactions supprimées d’un message. |
| Autre type d’activité reçu | `OnUnrecognizedActivityTypeAsync` | Substituer celui-ci pour gérer tout type d’activité non géré autrement. |

Ces différents gestionnaires disposent de `turnContext` qui fournit des informations sur l’activité entrante ; celle-ci correspond à la requête HTTP entrante. Les activités pouvant être de différents types, chaque gestionnaire fournit donc une activité fortement typée dans son paramètre de contexte de tour ; dans la plupart des cas, `OnMessageActivityAsync` est toujours géré, et il est généralement le plus courant.

Comme dans les précédentes versions 4.x de ce framework, il existe également la possibilité d’implémenter la méthode publique `OnTurnAsync`. Actuellement, l’implémentation de base de cette méthode gère la vérification des erreurs, et appelle ensuite chacun des gestionnaires spécifiques (par exemple, les deux que nous définissons dans cet exemple) selon le type d’activité entrante. Bien souvent, vous pouvez ignorer cette méthode et utiliser les gestionnaires individuels, mais si votre situation nécessite une implémentation personnalisée de `OnTurnAsync`, elle demeure une alternative.

> [!IMPORTANT]
> Si jamais vous substituez la méthode `OnTurnAsync`, vous devez appeler `base.OnTurnAsync` pour obtenir que l’implémentation de base appelle tous les autres gestionnaires `On<activity>Async`, ou appeler ces gestionnaires vous-même. Sinon, ces gestionnaires ne sont pas appelés, et ce code n’est pas exécuté.

Dans cet exemple, nous accueillons un nouvel utilisateur ou renvoyons le message que l’utilisateur a envoyé à l’aide de l’appel `SendActivityAsync`. L’activité sortante correspond à la requête HTTP POST sortante.

```cs
public class MyBot : ActivityHandler
{
    protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
    {
        await turnContext.SendActivityAsync(MessageFactory.Text($"Echo: {turnContext.Activity.Text}"), cancellationToken);
    }

    protected override async Task OnMembersAddedAsync(IList<ChannelAccount> membersAdded, ITurnContext<IConversationUpdateActivity> turnContext, CancellationToken cancellationToken)
    {
        foreach (var member in membersAdded)
        {
            await turnContext.SendActivityAsync(MessageFactory.Text($"welcome {member.Name}"), cancellationToken);
        }
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

La logique principale du bot est définie dans le code du bot, appelé ici `bots\echoBot.js`. `EchoBot` dérive de `ActivityHandler`. `ActivityHandler` définit divers gestionnaires pour différents types d’activités, sachant que vous pouvez modifier le comportement de votre bot en fournissant une logique supplémentaire, par exemple avec `onMessage` et `onConversationUpdate` ici.

Les gestionnaires définis dans `ActivityHandler` sont :

| Événement | Handler | Description |
| :-- | :-- | :-- |
| Tout type d’activité reçu | `onTurn` | Appelé lorsqu’une activité est reçue. |
| Activité de message reçue | `onMessage` | Appelé lorsqu’une activité `message` est reçue. |
| Activité reçue de mise à jour de conversation | `onConversationUpdate` | Appelé lorsqu’une activité `conversationUpdate` est reçue. |
| Des membres ont rejoint la conversation | `onMembersAdded` | Appelé lorsque des membres ont rejoint la conversation, y compris le bot. |
| Des membres ont quitté la conversation | `onMembersRemoved` | Appelé lorsque des membres ont quitté la conversation, y compris le bot. |
| Activité de réaction à un message reçue | `onMessageReaction` | Appelé lorsqu’une activité `messageReaction` est reçue. |
| Réactions de message ajoutées à un message | `onReactionsAdded` | Appelé lorsque des réactions sont ajoutées à un message. |
| Réactions de message supprimées d’un message | `onReactionsRemoved` | Appelé lorsque des réactions sont supprimées d’un message. |
| Activité d’événement reçue | `onEvent` | Appelé lorsqu’une activité `event` est reçue. |
| Activité reçue d’événement de réponse de jeton | `onTokenResponseEvent` | Appelé lorsqu’un événement `tokens/response` est reçu. |
| Autre type d’activité reçu | `onUnrecognizedActivityType` | Appelé lorsqu’un gestionnaire pour le type d’activité spécifique n’est pas défini. |
| Les gestionnaires d’activités ont terminé | `onDialog` | Appelé après que les gestionnaires applicables ont terminé. |

Appelez le paramètre de fonction `next` de chaque gestionnaire pour permettre au traitement de continuer. Si `next` n’est pas appelé, le traitement de l’activité se termine.

À chaque tour, nous commençons par vérifier si le bot a reçu un message. Lorsque nous recevons un message de l’utilisateur, nous renvoyons le message qu’il a envoyé.

```javascript
const { ActivityHandler } = require('botbuilder');

class MyBot extends ActivityHandler {
    constructor() {
        super();
        // See https://aka.ms/about-bot-activity-message to learn more about the message and other activity types.
        this.onMessage(async (context, next) => {
            await context.sendActivity(`You said '${ context.activity.text }'`);
            // By calling next() you ensure that the next BotHandler is run.
            await next();
        });
        this.onConversationUpdate(async (context, next) => {
            await context.sendActivity('[conversationUpdate event detected]');
            // By calling next() you ensure that the next BotHandler is run.
            await next();
        });
    }
}

module.exports.MyBot = MyBot;
```

---

### <a name="access-the-bot-from-your-app"></a>Accéder au bot à partir de votre application

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="set-up-services"></a>Configurer les services

La méthode `ConfigureServices` dans le fichier `Startup.cs` charge les services connectés et leurs clés à partir de `appsettings.json` ou d’Azure Key Vault (le cas échéant), se connecte à l’état et ainsi de suite. Ici, nous ajoutons MVC et définissons la version de compatibilité sur nos services, puis nous configurons l’adaptateur et le bot pour qu’ils soient à la disposition du contrôleur de bot par le biais de l’injection de dépendances.

<!-- want to explain the singleton vs transient here?-->

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the credential provider to be used with the Bot Framework Adapter.
    services.AddSingleton<ICredentialProvider, ConfigurationCredentialProvider>();

    // Create the Bot Framework Adapter.
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkHttpAdapter>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

La méthode `Configure` achève la configuration de votre application en précisant que l’application utilise MVC ainsi que quelques autres fichiers. Tous les bots utilisant Bot Framework ont besoin de cet appel de configuration, néanmoins celui-là sera déjà défini dans les exemples ou dans le modèle VSIX lorsque vous créerez votre bot. `ConfigureServices` et `Configure` sont appelés par le runtime au démarrage de l’application.

#### <a name="bot-controller"></a>Contrôleur de bot

Le contrôleur, conformément à la structure MVC standard, vous permet de déterminer le routage des messages et des requêtes HTTP POST. Pour notre bot, nous transmettons la requête entrante à la méthode de *traitement asynchrone d’activité* de l’adaptateur, comme expliqué à la section [Pile de traitement d’une activité](#the-activity-processing-stack) ci-dessus. Dans cet appel, nous spécifions le bot et toute autre information d’autorisation pouvant être demandée.

Le contrôleur implémente `ControllerBase`, détient l’adaptateur et le bot que nous avons définis dans `Startup.cs` (qui sont disponibles ici par le biais de l’injection de dépendances) et transmet les informations nécessaires au bot lorsqu’il reçoit une requête HTTP POST entrante.

Ici, vous voyez la classe être poursuivie par attributs d’itinéraire et de contrôleur. Ces attributs aident le framework à acheminer les messages de façon appropriée, et à savoir quel contrôleur utiliser. Si vous modifiez la valeur dans l’attribut d’itinéraire, est également modifié le point de terminaison que l’émulateur ou d’autres canaux utilisent pour accéder à votre bot.

```cs
// This ASP Controller is created to handle a request. Dependency Injection will provide the Adapter and IBot
// implementation at runtime. Multiple different IBot implementations running at different endpoints can be
// achieved by specifying a more specific type for the bot constructor argument.
[Route("api/messages")]
[ApiController]
public class BotController : ControllerBase
{
    private readonly IBotFrameworkHttpAdapter Adapter;
    private readonly IBot Bot;

    public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
    {
        Adapter = adapter;
        Bot = bot;
    }

    [HttpPost]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await Adapter.ProcessAsync(Request, Response, Bot);
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="indexjs"></a>index.js

`index.js` configure votre bot et le service d’hébergement qui transférera les activités à la logique de votre bot.

#### <a name="required-libraries"></a>Bibliothèques requises

Le tout début de votre fichier `index.js` répertorie une série de modules ou de bibliothèques requis. Ces modules vous donnent accès à un ensemble de fonctions que vous souhaiterez peut-être inclure dans votre application.

```javascript
const dotenv = require('dotenv');
const path = require('path');
const restify = require('restify');

// Import required bot services.
// See https://aka.ms/bot-services to learn more about the different parts of a bot.
const { BotFrameworkAdapter } = require('botbuilder');

// This bot's main dialog.
const { MyBot } = require('./bot');

// Import required bot configuration.
const ENV_FILE = path.join(__dirname, '.env');
dotenv.config({ path: ENV_FILE });
```

#### <a name="set-up-services"></a>Configurer les services

Les parties suivantes configurent le serveur et l’adaptateur qui permettent à votre bot de communiquer avec l’utilisateur et d’envoyer des réponses. Le serveur écoute le port spécifié à partir du fichier de configuration, ou revient au port _3978_ pour la connexion à votre émulateur. L’adaptateur jouera le rôle de conducteur de votre bot en dirigeant les communications entrantes et sortantes, l’authentification, et ainsi de suite.

```javascript
// Create HTTP server
const server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, () => {
    console.log(`\n${ server.name } listening to ${ server.url }`);
    console.log(`\nGet Bot Framework Emulator: https://aka.ms/bot-framework-www-portal-emulator`);
    console.log(`\nTo talk to your bot, open the emulator select "Open Bot"`);
});

// Create adapter.
// See https://aka.ms/about-bot-adapter to learn more about how bots work.
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

// Catch-all for errors.
adapter.onTurnError = async (context, error) => {
    // This check writes out errors to console log .vs. app insights.
    console.error(`\n [onTurnError]: ${ error }`);
    // Send a message to the user
    await context.sendActivity(`Oops. Something went wrong!`);
};

// Create the main dialog.
const myBot = new MyBot();
```

#### <a name="forwarding-requests-to-the-bot-logic"></a>Transfert des requêtes à la logique du bot

L’élément `processActivity` de l’adaptateur envoie les activités entrantes à la logique de votre bot.
Le troisième paramètre de `processActivity` est un gestionnaire de fonctions ; il est appelé pour exécuter la logique du bot une fois que [l’activité](#the-activity-processing-stack) reçue a été prétraitée par l’adaptateur et acheminée par le biais d’un intergiciel. La variable de contexte de tour, transmise sous la forme d’un argument au gestionnaire de fonctions, peut être utilisée pour fournir des informations sur l’activité entrante, l’expéditeur et le destinataire, le canal, la conversation, etc. Le traitement de l’activité est acheminé vers la méthode `run` du bot. `run` est définie dans `ActivityHandler` ; elle effectue une vérification des erreurs, puis appelle les gestionnaires d’événements du bot en fonction du type de l’activité reçue.

```javascript
// Listen for incoming requests.
server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        // Route to main dialog.
        await myBot.run(context);
    });
});
```

---

## <a name="manage-bot-resources"></a>Gérer les ressources du bot

Les ressources du bot, telles que les ID d’application, mots de passe, clés ou secrets pour les services connectés, doivent être gérées en conséquence. Pour en savoir plus sur la façon de faire, consultez [Gérer les ressources du bot](bot-file-basics.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour comprendre le rôle de l’état dans les bots, consultez [Gestion de l’état](bot-builder-concept-state.md).
