---
title: Aide-mémoire sur la migration de JavaScript  v3 vers v4 - Bot Service
description: Liste des principales différences entre la version v3 et la version v4 du SDK JavaScript Bot Framework.
keywords: JavaScript, migration de bot, dialogues, bot v3
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: ea71cfa7adb9ba4f38b3f0ec79641cf28b0f64f3
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791071"
---
# <a name="javascript-migration-quick-reference"></a>Aide-mémoire sur la migration JavaScript

Le SDK JavaScript BotBuilder v4 comprend des changements fondamentaux qui affectent la façon dont les bots sont créés. L’objectif de ce guide est de vous permettre de comprendre rapidement les différences qui existent entre les SDK v3 et v4 au niveau de l’exécution des tâches.

- La façon dont les informations sont transmises entre un bot et les canaux a changé.
    Dans la version v3, vous utilisiez les objets _connector_ et _session_.
    Dans la version v4, ceux-ci ont été remplacés par les objets _adapter_ et _turn context_.

- Par ailleurs, les dialogues et les instances de bot ont fait l’objet d’un découplage supplémentaire.
    Dans la version v3, les dialogues étaient enregistrés directement dans le constructeur du bot.
    Dans la version v4, vous passez désormais les dialogues dans des instances de bot en tant qu’arguments, ce qui permet une plus grande flexibilité de composition.

- La version v4 fournit aussi une classe `ActivityHandler`, qui aide à automatiser la gestion des différents types d’activités, comme les activités _message_, _conversation update_ et _event_.

Ces améliorations impliquent des changements au niveau de la syntaxe de développement des bots dans JavaScript, notamment au niveau de la création d’objets bot, de la définition de dialogues et de l’encodage d’une logique de gestion des événements.

Le reste de cette rubrique compare les constructions du SDK JavaScript Bot Framework v3 à leurs équivalents dans la version v4.

## <a name="to-listen-for-incoming-requests"></a>Pour écouter les requêtes entrantes

### <a name="v3"></a>v3

```javascript
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

server.post('/api/messages', connector.listen());
```

### <a name="v4"></a>v4

```javascript
const adapter = new BotFrameworkAdapter({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword
});

server.post('/api/messages', (req, res) => {
    adapter.processActivity(req, res, async (context) => {
        await bot.run(context);
    });
});
```

## <a name="to-create-a-bot-instance"></a>Pour créer une instance de bot

### <a name="v3"></a>v3

```javascript
var bot = new builder.UniversalBot(connector, [ ...DIALOG_STEPS ]);
```

### <a name="v4"></a>v4

```javascript
// Define the bot class as extending ActivityHandler.
const { ActivityHandler } = require('botbuilder');

class MyBot extends ActivityHandler {
    // ...
}

// Instantiate a new bot instance.
const bot = new MyBot(conversationState, dialog);
```

## <a name="to-send-a-message-to-a-user"></a>Pour envoyer un message à un utilisateur

### <a name="v3"></a>v3

```javascript
session.send('Hello and welcome to the help desk bot.');
```

### <a name="v4"></a>v4

```javascript
await context.sendActivity('Hello and welcome to the help desk bot.');
```

## <a name="to-define-a-default-dialog"></a>Pour définir un dialogue par défaut

### <a name="v3"></a>v3

```javascript
var bot = new builder.UniversalBot(connector, [
    // Add default dialog waterfall steps...
]);
```

### <a name="v4"></a>v4

```javascript
// In the main dialog class, define a run method.
async run(turnContext, accessor) {
    const dialogSet = new DialogSet(accessor);
    dialogSet.add(this);

    const dialogContext = await dialogSet.createContext(turnContext);
    const results = await dialogContext.continueDialog();
    if (results.status === DialogTurnStatus.empty) {
        await dialogContext.beginDialog(this.id);
    }
}

// Pass conversation state management and a main dialog objects to the bot (in index.js).
const bot = new MyBot(conversationState, dialog);

// Inside the bot's constructor, add the dialog as a member property and define a DialogState property accessor.
this.dialog = dialog;
this.dialogState = this.conversationState.createProperty('DialogState');

// Inside the bot's message handler, invoke the dialog's run method, passing in the turn context and DialogState accessor.
this.onMessage(async (context, next) => {
    // Run the Dialog with the new message Activity.
    await this.dialog.run(context, this.dialogState);
    await next();
});
```

## <a name="to-start-a-child-dialog"></a>Pour démarrer un dialogue enfant

Le dialogue parent est de nouveau exécuté une fois que l’exécution du dialogue enfant est terminée.

### <a name="v3"></a>v3

```javascript
session.beginDialog(DIALOG_ID);
```

### <a name="v4"></a>v4

```javascript
await context.beginDialog(DIALOG_ID);
```

## <a name="to-replace-a-dialog"></a>Pour remplacer un dialogue

Dans la pile, le dialogue actuel est remplacé par le nouveau dialogue.

### <a name="v3"></a>v3

```javascript
session.replaceDialog(DIALOG_ID);
```

### <a name="v4"></a>v4

```javascript
await context.replaceDialog(DIALOG_ID);
```

## <a name="to-end-a-dialog"></a>Pour terminer un dialogue

### <a name="v3"></a>v3

```javascript
session.endDialog();
```

### <a name="v4"></a>v4

Vous pouvez inclure une valeur de retour facultative.

```javascript
await context.endDialog(returnValue);
```

## <a name="to-register-a-dialog-with-a-bot-instance"></a>Pour inscrire un dialogue auprès d’une instance de bot

### <a name="v3"></a>v3

```javascript
// Create the bot.
var bot = new builder.UniversalBot(connector, [
    // ...
]};

// Add the dialog to the bot.
bot.dialog('myDialog', require('./mydialog'));
```

### <a name="v4"></a>v4

```javascript
// In the main dialog class constructor.
this.addDialog(new MyDialog(DIALOG_ID));

// In the app entry point file (index.js)
const { MainDialog } = require('./dialogs/main');

// Create the base dialog and bot, passing the main dialog as an argument.
const dialog = new MainDialog();
const reservationBot = new ReservationBot(conversationState, dialog);
```

## <a name="to-prompt-a-user-for-input"></a>Pour inviter l’utilisateur à entrer du texte

### <a name="v3"></a>v3

```javascript
var builder = require('botbuilder');
builder.Prompts.text(session, 'Please enter your destination');
```

### <a name="v4"></a>v4

```javascript
const { TextPrompt } = require('botbuilder-dialogs');

// In the dialog's constructor, register the prompt.
this.addDialog(new TextPrompt('initialPrompt'));

// In the dialog step, invoke the prompt.
return await stepContext.prompt(
    'initialPrompt', {
        prompt: 'Please enter your destination'
    }
);
```

## <a name="to-retrieve-the-users-response-to-a-prompt"></a>Pour récupérer la réponse de l’utilisateur à une invite

### <a name="v3"></a>v3

```javascript
function (session, result) {
    var destination = result.response;
    // ...
}
```

### <a name="v4"></a>v4

```javascript
// In the dialog step after the prompt step, retrieve the result from the waterfall step context.
async nextStep(stepContext) {
    const destination = stepContext.result;
    // ...
}
```

## <a name="to-save-information-to-dialog-state"></a>Pour enregistrer des informations dans l’état d’un dialogue

### <a name="v3"></a>v3

```javascript
session.dialogData.destination = results.response;
```

### <a name="v4"></a>v4

```javascript
// In a dialog, set the value in the waterfall step context.
stepContext.values.destination = destination;
```

## <a name="to-write-changes-in-state-to-the-persistance-layer"></a>Pour écrire des changements d’état dans la couche de persistance

### <a name="v3"></a>v3

```javascript
// State data is saved by default at the end of the turn.
// Do this to save it manually.
session.save();
```

### <a name="v4"></a>v4

```javascript
// State changes are not saved automatically at the end of the turn.
await this.conversationState.saveChanges(context, false);
await this.userState.saveChanges(context, false);
```

## <a name="to-create-and-register-state-storage"></a>Pour créer et inscrire un stockage d’état

### <a name="v3"></a>v3

```javascript
var builder = require('botbuilder');

// Create conversation state with in-memory storage provider.
var inMemoryStorage = new builder.MemoryBotStorage();

// Create the base dialog and bot
var bot = new builder.UniversalBot(connector, [ /*...*/ ]).set('storage', inMemoryStorage);
```

### <a name="v4"></a>v4

```javascript
const { MemoryStorage } = require('botbuilder');

// Create the memory layer object.
const memoryStorage = new MemoryStorage();

// Create the conversation state management object, using the storage provider.
const conversationState = new ConversationState(memoryStorage);

// Create the base dialog and bot.
const dialog = new MainDialog();
const reservationBot = new ReservationBot(conversationState, dialog);
```

## <a name="to-catch-an-error-thrown-from-a-dialog"></a>Pour intercepter une erreur levée par un dialogue

### <a name="v3"></a>v3

```javascript
// Set up the error handler.
session.on('error', function (err) {
    session.send('Failed with message:', err.message);
    session.endDialog();
});

// Throw an error.
session.error('An error has occurred.');
```

### <a name="v4"></a>v4

```javascript
// Set up the error handler, using the adapter.
adapter.onTurnError = async (context, error) => {
    const errorMsg = error.message

    // Clear out conversation state to avoid keeping the conversation in a corrupted bot state.
    await conversationState.delete(context);

    // Send a message to the user.
    await context.sendActivity(errorMsg);
};

// Throw an error.
async () => {
    throw new Error('An error has occurred.');
}
```

## <a name="to-register-activity-handlers"></a>Pour inscrire des gestionnaires d’activités

### <a name="v3"></a>v3

```javascript
bot.on('conversationUpdate', function (message) {
    // ...
}

bot.on('contactRelationUpdate', function (message) {
    // ...
}
```

### <a name="v4"></a>v4

```javascript
// In the bot's constructor, add the handlers.
this.onMessage(async (context, next) => {
    // ...
}
  
this.onDialog(async (context, next) => {
    // ...
}

this.onMembersAdded(async (context, next) => {
    // ...
}
```

## <a name="to-send-attachments"></a>Pour envoyer des pièces jointes

### <a name="v3"></a>v3

```javascript
var message = new builder.Message()
    .attachments(hotelHeroCards
    .attachmentLayout(builder.AttachmentLayout.carousel));
```

### <a name="v4"></a>v4

```javascript
await context.sendActivity({
    attachments: hotelHeroCards,
    attachmentLayout: AttachmentLayoutTypes.Carousel
});
```

## <a name="to-use-natural-language-recognition-luis"></a>Pour utiliser la reconnaissance en langage naturel (LUIS)

### <a name="v3"></a>v3

```javascript
// The LUIS recognizer was part of the 'botbuilder' library
var builder = require('botbuilder');

var recognizer = new builder.LuisRecognizer(LUIS_MODEL_URL);
bot.recognizer(recognizer);
```

### <a name="v4"></a>v4

```javascript
// The LUIS recognizer is now part of the 'botbuilder-ai' library
const { LuisRecognizer } = require('botbuilder-ai');

const luisApp = { applicationId: LuisAppId, endpointKey: LuisAPIKey, endpoint: `https://${ LuisAPIHostName }` };
const recognizer = new LuisRecognizer(luisApp);

const recognizerResult = await recognizer.recognize(context);
const intent = LuisRecognizer.topIntent(recognizerResult);
```

## <a name="v3-intent-dialog-and-v4-equivalent"></a>Boîte de dialogue d’intention v3 et équivalent v4

### <a name="v3"></a>v3

```javascript
// Create a 'greetings' RegExpRecognizer that can be turned off
var greetings = new builder.RegExpRecognizer('Greetings', /hello|hi|hey|greetings/i)
    .onEnabled(function (session, callback) {
        // Check to see if this recognizer should be enabled
        if (session.conversationData.useGreetings) {
            callback(null, true);
        } else {
            callback(null, false);
        }
    });

// Create our IntentDialog and add recognizers
var intents = new builder.IntentDialog({ recognizers: [greetings] });

bot.dialog('/', intents);

// If no intent is recognized, direct user to Recognizer Menu
intents.onDefault('RecognizerMenu');

// Match our "Greetings" and "Farewell" intents with their dialogs
intents.matches('Greetings', 'Greetings');

// Add a greetings dialog
bot.dialog('Greetings', [
    function (session) {
        session.endDialog('Greetings!');
    }
]);
```

### <a name="v4"></a>v4

```javascript
this.onMessage(async (context, next) => {

    const recognizerResult = {
        text: context.activity.text,
        intents: []
    };

    const greetingRegex = RegExp(/hello|hi|hey|greetings/i);

    if (greetingRegex.test(context.activity.text)) {
      // greeting intent identified
      recognizerResult.intents.push('Greeting');
    }

    if (recognizerResult.intents.includes('Greeting')) {
        // Run the 'Greeting' dialog
        await context.beginDialog(GREETING_DIALOG_ID);
    }

    await next();
});
```