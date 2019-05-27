---
title: Migrer un bot JavaScript v3 existant vers un nouveau projet v4 | Microsoft Docs
description: Nous prenons un bot JavaScript v3 existant que nous migrons vers le SDK v4 en utilisant un nouveau projet.
keywords: JavaScript, migration de bot, formflow, dialogues, bot v3
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/13/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 3692c723bf2ac4d2275176bb0f7c5f8103225808
ms.sourcegitcommit: 178140eb060d71803f1c6357dd5afebd7f44fe1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65856658"
---
# <a name="migrate-a-sdk-v3-javascript-bot-to-v4"></a>Migrer un bot du kit SDK JavaScript v3 vers v4

Dans cet article, nous allons déplacer le bot du kit SDK JavaScript v3 [core-MultiDialogs-v3](https://aka.ms/v3-js-core-multidialog-migration-sample) vers un nouveau bot JavaScript v4.
Cette conversion se décompose en plusieurs étapes :

1. Créer le projet et ajouter des dépendances.
1. Mettre à jour le point d’entrée et définir des constantes.
1. Créer les dialogues en les implémentant de nouveau à l’aide du SDK v4.
1. Mettre à jour le code du bot pour exécuter les dialogues.
1. Déplacer le fichier utilitaire **store.js**.

À la fin de ce processus, nous aurons un bot v4 opérationnel. Une copie du bot converti figure également dans le référentiel d’exemples, [core-MultiDialogs-v4](https://aka.ms/v4-js-core-multidialog-migration-sample).

Le kit SDK Bot Framework v4 est basé sur la même API REST sous-jacente que le kit SDK v3. Toutefois, le kit SDK v4 est une refactorisation de la version précédente du kit pour offrir aux développeurs plus de flexibilité et de contrôle sur leurs bots. Les principaux changements du kit SDK sont notamment les suivants :

- L’état est géré via des objets de gestion d’état et des accesseurs de propriété.
- Le mode de gestion des tours a changé, autrement dit la façon dont le bot reçoit une activité entrante et y répond à partir du canal de l’utilisateur.
- v4 n’utilise pas d’objet `session`, mais dispose à la place d’un objet de _contexte de tour_ qui contient des informations sur l’activité entrante et peut être utilisé pour renvoyer une activité de réponse.
- Nouvelle bibliothèque de dialogues qui est très différente de celle de v3. Vous devez convertir les anciens dialogues dans le nouveau système de dialogues à l’aide de dialogues composants et en cascade.

<!-- TODO
For more information about specific changes, see [differences between the v3 and v4 JavaScript SDK](???.md).
-->

> [!NOTE]
> Dans le cadre de la migration, nous avons également nettoyé une partie du code, mais nous mettrons seulement en évidence les modifications que nous avons apportées à la logique v3.

## <a name="prerequisites"></a>Prérequis

- Node.js
- Visual Studio Code
- [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)

## <a name="about-this-bot"></a>À propos de ce bot

Le bot que nous allons migrer illustre l’utilisation de plusieurs dialogues pour gérer les flux de conversation. Le bot peut rechercher des informations sur un vol ou un hôtel.

- Le dialogue principal demande à l’utilisateur quel type d’informations il recherche.
- Le dialogue Hôtel demande à l’utilisateur des paramètres de recherche et effectue ensuite une simulation de recherche.
- Le dialogue Vol génère une erreur que le bot intercepte et gère correctement.

## <a name="create-and-open-a-new-v4-bot-project"></a>Créer et ouvrir un nouveau projet de bot v4

1. Vous avez besoin d’un projet v4 dans lequel déplacer le code de bot. Pour créer un projet localement, consultez [Créer un bot à l’aide du kit SDK Bot Framework pour JavaScript](../../javascript/bot-builder-javascript-quickstart.md).

    > [!TIP]
    > Vous pouvez également créer un projet sur Azure. Consultez [Créer un bot avec Azure Bot Service](../../bot-service-quickstart.md).
    > Toutefois, ces deux méthodes entraînent une légère différence dans les fichiers de prise en charge. Le projet v4 pour cet article a été créé comme un projet local.

1. Ouvrez ensuite le projet dans Visual Studio Code.

## <a name="update-the-packagejson-file"></a>Mettre à jour le fichier package.json

1. Ajoutez une dépendance sur le package **botbuilder-dialogs** en entrant `npm i botbuilder-dialogs` dans la fenêtre de terminal Visual Studio Code.

1. Modifiez **./package.json** et mettez à jour les propriétés `name`, `version`, `description` ainsi que d’autres propriétés si vous le souhaitez.

## <a name="update-the-v4-app-entry-point"></a>Mettre à jour le point d’entrée d’application v4

Le modèle v4 crée un fichier **index.js** pour le point d’entrée d’application et un fichier **bot.js** pour la logique spécifique au bot. Dans les étapes ultérieures, nous allons renommer le fichier **bot.js** en **bots/reservationBot.js** et ajouter une classe pour chaque dialogue.

Modifiez **./index.js**, qui est le point d’entrée de notre application bot. Il contient les parties du fichier **app.js** v3 qui a configuré le serveur HTTP.

1. En plus de `BotFrameworkAdapter`, importez `MemoryStorage` et `ConversationState` à partir du package **botbuilder**. Importez également les modules de dialogues bot et principal. (Nous allons les créer bientôt, mais nous devons les référencer ici.)

    ```javascript
    // Import required bot services.
    // See https://aka.ms/bot-services to learn more about the different parts of a bot.
    const { BotFrameworkAdapter, MemoryStorage, ConversationState } = require('botbuilder');

    // This bot's main dialog.
    const { MainDialog } = require('./dialogs/main')
    const { ReservationBot } = require('./bots/reservationBot');
    ```

1. Définissez un gestionnaire `onTurnError` pour l’adaptateur.

    ```javascript
    // Catch-all for errors.
    adapter.onTurnError = async (context, error) => {
        const errorMsg = error.message ? error.message : `Oops. Something went wrong!`;
        // This check writes out errors to console log .vs. app insights.
        console.error(`\n [onTurnError]: ${ error }`);
        // Clear out state
        await conversationState.delete(context);
        // Send a message to the user
        await context.sendActivity(errorMsg);
    };
    ```

    Dans v4, nous utilisons un _adaptateur de bot_ pour router les activités entrantes vers le bot. L’adaptateur nous permet d’intercepter des erreurs et d’y réagir avant la fin d’un tour. Nous effaçons ici l’état des conversations si une erreur d’application se produit, ce qui permet de réinitialiser tous les dialogues et d’empêcher le bot de rester dans un état de conversation endommagé.

1. Remplacez le code du modèle pour la création du bot par ce qui suit.

    ```javascript
    // Define state store for your bot.
    const memoryStorage = new MemoryStorage();

    // Create conversation state with in-memory storage provider.
    const conversationState = new ConversationState(memoryStorage);

    // Create the base dialog and bot
    const dialog = new MainDialog();
    const reservationBot = new ReservationBot(conversationState, dialog);
    ```

    La couche de stockage en mémoire est désormais fournie par la classe `MemoryStorage` et nous devons créer explicitement un objet de gestion d’état de conversation.

    Le code de définition de dialogue a été déplacé vers une classe `MainDialog` que nous définirons dans peu de temps. Nous allons également migrer le code de définition de bot dans une classe `ReservationBot`.

1. Enfin, nous mettons à jour le gestionnaire de demandes du serveur pour utiliser l’adaptateur afin de router les activités vers le bot.

    ```javascript
    // Listen for incoming requests.
    server.post('/api/messages', (req, res) => {
        adapter.processActivity(req, res, async (context) => {
            // Route incoming activities to the bot.
            await reservationBot.run(context);
        });
    });
    ```

    Dans v4, notre bot dérive de `ActivityHandler`, ce qui définit la méthode `run` afin de recevoir une activité pour un tour.

## <a name="add-a-constants-file"></a>Ajouter un fichier de constantes

Créez un fichier **./const.js** devant contenir les identificateurs pour notre bot.

```javascript
module.exports = {
    MAIN_DIALOG: 'mainDialog',
    INITIAL_PROMPT: 'initialPrompt',
    HOTELS_DIALOG: 'hotelsDialog',
    INITIAL_HOTEL_PROMPT: 'initialHotelPrompt',
    CHECKIN_DATETIME_PROMPT: 'checkinTimePrompt',
    HOW_MANY_NIGHTS_PROMPT: 'howManyNightsPrompt',
    FLIGHTS_DIALOG: 'flightsDialog',
};
```

Dans v4, les ID sont affectés à des objets de dialogue et d’invite, et les dialogues et invites sont appelés par ID.

## <a name="create-new-dialog-files"></a>Créer des fichiers de dialogue

Créez les fichiers suivants :

| Nom de fichier | Description |
|:---|:---|
| **./dialogs/flights.js** | Il contient la logique migrée pour le dialogue `hotels`. |
| **./dialogs/hotels.js** | Il contient la logique migrée pour le dialogue `flights`. |
| **./dialogs/main.js** | Il contient la logique migrée pour notre bot et sert pour le dialogue _racine_. |

Nous n’avons pas migré le dialogue de prise en charge. Pour obtenir un exemple d’implémentation d’un dialogue d’aide dans v4, consultez [Gérer les interruptions par l’utilisateur](../bot-builder-howto-handle-user-interrupt.md?tabs=javascript).

### <a name="implement-the-main-dialog"></a>Implémenter le dialogue principal

Dans v3, tous les bots ont été créés sur un système de dialogues. Dans v4, la logique de bot et la logique de dialogue sont désormais distinctes. Nous avons pris ce qui était le _dialogue racine_ dans le bot v3 et avons créé une classe `MainDialog` pour prendre sa place.

Modifiez **./dialogs/main.js**.

1. Importez les classes et les constantes dont nous avons besoin pour le dialogue.

    ```javascript
    const { DialogSet, DialogTurnStatus, ComponentDialog, WaterfallDialog,
        ChoicePrompt } = require('botbuilder-dialogs');
    const { FlightDialog } = require('./flights');
    const { HotelsDialog } = require('./hotels');
    const { MAIN_DIALOG,
        INITIAL_PROMPT,
        HOTELS_DIALOG,
        FLIGHTS_DIALOG
    } = require('../const');
    ```

1. Définissez et exportez la classe `MainDialog`.

    ```javascript
    const initialId = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor() {
            super(MAIN_DIALOG);

            // Create a dialog set for the bot. It requires a DialogState accessor, with which
            // to retrieve the dialog state from the turn context.
            this.addDialog(new ChoicePrompt(INITIAL_PROMPT, this.validateNumberOfAttempts.bind(this)));
            this.addDialog(new FlightDialog(FLIGHTS_DIALOG));

            // Define the steps of the base waterfall dialog and add it to the set.
            this.addDialog(new WaterfallDialog(initialId, [
                this.promptForBaseChoice.bind(this),
                this.respondToBaseChoice.bind(this)
            ]));

            // Define the steps of the hotels waterfall dialog and add it to the set.
            this.addDialog(new HotelsDialog(HOTELS_DIALOG));

            this.initialDialogId = initialId;
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

    Vous déclarez ainsi les autres dialogues et invites auxquels le dialogue principal fait référence directement.

    - Dialogue en cascade principal qui contient les étapes de ce dialogue. Quand le dialogue composant démarre, il lance son _dialogue initial_.
    - Invite de choix que nous allons utiliser pour demander à l’utilisateur la tâche qu’il veut effectuer. Nous avons créé l’invite de choix avec un validateur.
    - Les deux dialogues enfants, Vol et Hôtel.

1. Ajoutez une méthode d’assistance `run` à la classe.

    ```javascript
    /**
     * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
     * If no dialog is active, it will start the default dialog.
     * @param {*} turnContext
     * @param {*} accessor
     */
    async run(turnContext, accessor) {
        const dialogSet = new DialogSet(accessor);
        dialogSet.add(this);

        const dialogContext = await dialogSet.createContext(turnContext);
        const results = await dialogContext.continueDialog();
        if (results.status === DialogTurnStatus.empty) {
            await dialogContext.beginDialog(this.id);
        }
    }
    ```

    Dans v4, un bot interagit avec le système de dialogues en créant d’abord un contexte de dialogue, puis en appelant `continueDialog`. S’il existe un dialogue actif, le contrôle lui est transmis. Sinon, cet appel est simplement retourné. Le résultat `empty` indique qu’aucun dialogue n’était actif et nous démarrons donc ici le dialogue principal une nouvelle fois.

    Le paramètre `accessor` transmet l’accesseur pour la propriété d’état du dialogue. L’état de la _pile de dialogues_ est stocké dans cette propriété. Pour plus d’informations sur le fonctionnement de l’état et des dialogues dans v4, consultez [Gestion de l’état](../bot-builder-concept-state.md) et [Bibliothèque des dialogues](../bot-builder-concept-dialog.md), respectivement.

1. Ajoutez à la classe les étapes en cascade du dialogue principal et le validateur pour l’invite de choix.

    ```javascript
    async promptForBaseChoice(stepContext) {
        return await stepContext.prompt(
            INITIAL_PROMPT, {
                prompt: 'Are you looking for a flight or a hotel?',
                choices: ['Hotel', 'Flight'],
                retryPrompt: 'Not a valid option'
            }
        );
    }

    async respondToBaseChoice(stepContext) {
        // Retrieve the user input.
        const answer = stepContext.result.value;
        if (!answer) {
            // exhausted attempts and no selection, start over
            await stepContext.context.sendActivity('Not a valid option. We\'ll restart the dialog ' +
                'so you can try again!');
            return await stepContext.endDialog();
        }
        if (answer === 'Hotel') {
            return await stepContext.beginDialog(HOTELS_DIALOG);
        }
        if (answer === 'Flight') {
            return await stepContext.beginDialog(FLIGHTS_DIALOG);
        }
        return await stepContext.endDialog();
    }

    async validateNumberOfAttempts(promptContext) {
        if (promptContext.attemptCount > 3) {
            // cancel everything
            await promptContext.context.sendActivity('Oops! Too many attempts :( But don\'t worry, I\'m ' +
                'handling that exception and you can try again!');
            return await promptContext.context.endDialog();
        }

        if (!promptContext.recognized.succeeded) {
            await promptContext.context.sendActivity(promptContext.options.retryPrompt);
            return false;
        }
        return true;
    }
    ```

    La première étape de la cascade demande à l’utilisateur de faire un choix, en lançant l’invite de choix, qui est elle-même un dialogue. La deuxième étape de la cascade consomme le résultat de l’invite de choix. Elle démarre un dialogue enfant (si un choix a été effectué) ou met fin au dialogue principal (si l’utilisateur n’est pas parvenu à faire un choix).

    L’invite de choix retourne le choix de l’utilisateur, s’il s’agit d’un choix valide, ou réinvite l’utilisateur à choisir à nouveau. Le validateur vérifie combien de fois dans une ligne l’invite a été proposée à l’utilisateur de l’invite et permet à celle-ci d’échouer après 3 tentatives n’ayant pas abouti, en retournant le contrôle au dialogue en cascade principal.

### <a name="implement-the-flights-dialog"></a>Implémenter le dialogue Vol

Dans le bot v3, le dialogue Vol était un stub qui illustrait comment le bot gère une erreur de conversation. Nous faisons de même ici.

Modifiez **./dialogs/flights.js**.

```javascript
const { ComponentDialog, WaterfallDialog } = require('botbuilder-dialogs');

const initialId = 'flightsWaterfallDialog';

class FlightDialog extends ComponentDialog {
    constructor(id) {
        super(id);

        // ID of the child dialog that should be started anytime the component is started.
        this.initialDialogId = initialId;

        // Define the conversation flow using a waterfall model.
        this.addDialog(new WaterfallDialog(initialId, [
            async () => {
                throw new Error('Flights Dialog is not implemented and is instead ' +
                    'being used to show Bot error handling');
            }
        ]));
    }
}

exports.FlightDialog = FlightDialog;
```

### <a name="implement-the-hotels-dialog"></a>Implémenter le dialogue Hôtel

Nous conservons le même flux global du dialogue Hôtel : demander une destination, une date, le nombre de nuits sur place, puis donner à l’utilisateur une liste d’options qui correspond à sa recherche.

Modifiez **./dialogs/hotels.js**.

1. Importez les classes et les constantes dont nous avons besoin pour le dialogue.

    ```javascript
    const { ComponentDialog, WaterfallDialog, TextPrompt, DateTimePrompt } = require('botbuilder-dialogs');
    const { AttachmentLayoutTypes, CardFactory } = require('botbuilder');
    const store = require('../store');
    const {
        INITIAL_HOTEL_PROMPT,
        CHECKIN_DATETIME_PROMPT,
        HOW_MANY_NIGHTS_PROMPT
    } = require('../const');
    ```

1. Définissez et exportez la classe `HotelsDialog`.

    ```javascript
    const initialId = 'hotelsWaterfallDialog';

    class HotelsDialog extends ComponentDialog {
        constructor(id) {
            super(id);

            // ID of the child dialog that should be started anytime the component is started.
            this.initialDialogId = initialId;

            // Register dialogs
            this.addDialog(new TextPrompt(INITIAL_HOTEL_PROMPT));
            this.addDialog(new DateTimePrompt(CHECKIN_DATETIME_PROMPT));
            this.addDialog(new TextPrompt(HOW_MANY_NIGHTS_PROMPT));

            // Define the conversation flow using a waterfall model.
            this.addDialog(new WaterfallDialog(initialId, [
                this.destinationPromptStep.bind(this),
                this.destinationSearchStep.bind(this),
                this.checkinPromptStep.bind(this),
                this.checkinTimeSetStep.bind(this),
                this.stayDurationPromptStep.bind(this),
                this.stayDurationSetStep.bind(this),
                this.hotelSearchStep.bind(this)
            ]));
        }
    }

    exports.HotelsDialog = HotelsDialog;
    ```

1. Ajoutez à la classe des fonctions d’assistance que nous allons utiliser dans les étapes du dialogue.

    ```javascript
    addDays(startDate, days) {
        const date = new Date(startDate);
        date.setDate(date.getDate() + days);
        return date;
    };

    createHotelHeroCard(hotel) {
        return CardFactory.heroCard(
            hotel.name,
            `${hotel.rating} stars. ${hotel.numberOfReviews} reviews. From ${hotel.priceStarting} per night.`,
            CardFactory.images([hotel.image]),
            CardFactory.actions([
                {
                    type: 'openUrl',
                    title: 'More details',
                    value: `https://www.bing.com/search?q=hotels+in+${encodeURIComponent(hotel.location)}`
                }
            ])
        );
    }
    ```

    `createHotelHeroCard` crée une bannière contenant des informations sur un hôtel.

1. Ajoutez à la classe les étapes en cascade utilisées dans le dialogue.

    ```javascript
    async destinationPromptStep(stepContext) {
        await stepContext.context.sendActivity('Welcome to the Hotels finder!');
        return await stepContext.prompt(
            INITIAL_HOTEL_PROMPT, {
                prompt: 'Please enter your destination'
            }
        );
    }

    async destinationSearchStep(stepContext) {
        const destination = stepContext.result;
        stepContext.values.destination = destination;
        await stepContext.context.sendActivity(`Looking for hotels in ${destination}`);
        return stepContext.next();
    }

    async checkinPromptStep(stepContext) {
        return await stepContext.prompt(
            CHECKIN_DATETIME_PROMPT, {
                prompt: 'When do you want to check in?'
            }
        );
    }

    async checkinTimeSetStep(stepContext) {
        const checkinTime = stepContext.result[0].value;
        stepContext.values.checkinTime = checkinTime;
        return stepContext.next();
    }

    async stayDurationPromptStep(stepContext) {
        return await stepContext.prompt(
            HOW_MANY_NIGHTS_PROMPT, {
                prompt: 'How many nights do you want to stay?'
            }
        );
    }

    async stayDurationSetStep(stepContext) {
        const numberOfNights = stepContext.result;
        stepContext.values.numberOfNights = parseInt(numberOfNights);
        return stepContext.next();
    }

    async hotelSearchStep(stepContext) {
        const destination = stepContext.values.destination;
        const checkIn = new Date(stepContext.values.checkinTime);
        const checkOut = this.addDays(checkIn, stepContext.values.numberOfNights);

        await stepContext.context.sendActivity(`Ok. Searching for Hotels in ${destination} from 
            ${checkIn.toDateString()} to ${checkOut.toDateString()}...`);
        const hotels = await store.searchHotels(destination, checkIn, checkOut);
        await stepContext.context.sendActivity(`I found in total ${hotels.length} hotels for your dates:`);

        const hotelHeroCards = hotels.map(this.createHotelHeroCard);

        await stepContext.context.sendActivity({
            attachments: hotelHeroCards,
            attachmentLayout: AttachmentLayoutTypes.Carousel
        });

        return await stepContext.endDialog();
    }
    ```

    Nous avons migré les étapes du dialogue Hôtel v3 dans les étapes en cascade de notre dialogue Hôtel v4.

## <a name="update-the-bot"></a>Mettre à jour le bot

Dans v4, les bots peuvent réagir aux activités en dehors du système de dialogues. La classe `ActivityHandler` définit des gestionnaires pour les types courants d’activités, afin de faciliter la gestion de votre code.

Renommez **./bot.js** en **./bots/reservationBot.js**, puis modifiez-le.

1. Le fichier importe déjà **ActivityHandler**, qui fournit une implémentation de base d’un bot.

    ```javascript
    const { ActivityHandler } = require('botbuilder');
    ```

1. Renommez la classe en `ReservationBot`.

    ```javascript
    class ReservationBot extends ActivityHandler {
        // ...
    }

    module.exports.ReservationBot = ReservationBot;
    ```

1. Mettez à jour la signature du constructeur, afin d’accepter les objets que nous recevons.

    ```javascript
    /**
     *
     * @param {ConversationState} conversationState
     * @param {Dialog} dialog
     * @param {any} logger object for logging events, defaults to console if none is provided
    */
    constructor(conversationState, dialog, logger) {
        super();
        // ...
    }
    ```

1. Dans le constructeur, ajoutez des contrôles de paramètres Null et définissez des propriétés de constructeur de classe.

    ```javascript
    if (!conversationState) throw new Error('[DialogBot]: Missing parameter. conversationState is required');
    if (!dialog) throw new Error('[DialogBot]: Missing parameter. dialog is required');
    if (!logger) {
        logger = console;
        logger.log('[DialogBot]: logger not passed in, defaulting to console');
    }

    this.conversationState = conversationState;
    this.dialog = dialog;
    this.logger = logger;
    this.dialogState = this.conversationState.createProperty('DialogState');
    ```

    C’est ici que nous créons l’accesseur de propriété d’état du dialogue qui stockera l’état de la pile de dialogues.

1. Dans le constructeur, mettez à jour le gestionnaire `onMessage` et ajoutez un gestionnaire `onDialog`.

    ```javascript
    this.onMessage(async (context, next) => {
        this.logger.log('Running dialog with Message Activity.');

        // Run the Dialog with the new message Activity.
        await this.dialog.run(context, this.dialogState);

        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });

    this.onDialog(async (context, next) => {
        // Save any state changes. The load happened during the execution of the Dialog.
        await this.conversationState.saveChanges(context, false);

        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });
    ```

    `ActivityHandler` route les activités de message vers `onMessage`. Ce bot gère toutes les entrées d’utilisateur par le biais de dialogues.

    `ActivityHandler` appelle `onDialog` à la fin du tour, avant de restituer le contrôle à l’adaptateur. Nous devons enregistrer explicitement l’état avant de quitter le tour. Sinon, les changements d’état ne seront pas enregistrés et le dialogue ne fonctionnera pas correctement.

1. Enfin, mettez à jour le gestionnaire `onMembersAdded` dans le constructeur.

    ```javascript
    this.onMembersAdded(async (context, next) => {
        const membersAdded = context.activity.membersAdded;
        for (let cnt = 0; cnt < membersAdded.length; ++cnt) {
            if (membersAdded[cnt].id !== context.activity.recipient.id) {
                await context.sendActivity('Hello and welcome to Contoso help desk bot.');
            }
        }
        // By calling next() you ensure that the next BotHandler is run.
        await next();
    });
    ```

    `ActivityHandler` appelle `onMembersAdded` quand il reçoit une activité de mise à jour de conversation qui indique que des participants autres que le bot ont été ajoutés à la conversation. Nous mettons à jour cette méthode pour envoyer un message d’accueil quand un utilisateur rejoint la conversation.

## <a name="create-the-store-file"></a>Créer le fichier de magasin

Créez le fichier **./store.js**, utilisé par le dialogue Hôtel. `searchHotels` représente une simulation de fonction de recherche d’hôtels, la même que dans le bot v3.

```javascript
module.exports = {
    searchHotels: destination => {
        return new Promise(resolve => {

            // Filling the hotels results manually just for demo purposes
            const hotels = [];
            for (let i = 1; i <= 5; i++) {
                hotels.push({
                    name: `${destination} Hotel ${i}`,
                    location: destination,
                    rating: Math.ceil(Math.random() * 5),
                    numberOfReviews: Math.floor(Math.random() * 5000) + 1,
                    priceStarting: Math.floor(Math.random() * 450) + 80,
                    image: `https://placeholdit.imgix.net/~text?txtsize=35&txt=Hotel${i}&w=500&h=260`
                });
            }

            hotels.sort((a, b) => a.priceStarting - b.priceStarting);

            // complete promise with a timer to simulate async response
            setTimeout(() => { resolve(hotels); }, 1000);
        });
    }
};
```

## <a name="test-the-bot-in-the-emulator"></a>Tester le bot dans l’émulateur

À ce stade, nous devrions être en mesure d’exécuter le bot localement et de nous y attacher avec l’émulateur.

1. Exécutez l’exemple en local sur votre machine.
    Si vous démarrez une session de débogage dans Visual Studio Code, les informations de journalisation sont envoyées à la console de débogage quand vous testez le bot.
1. Démarrez l’émulateur et connectez-vous au bot.
1. Envoyez des messages pour tester les dialogues principal, Vol et Hôtel.

## <a name="additional-resources"></a>Ressources supplémentaires

Rubriques conceptuelles v4 :

- [Fonctionnement des bots](../bot-builder-basics.md)
- [Gestion de l’état](../bot-builder-concept-state.md)
- [Bibliothèque de boîtes de dialogues](../bot-builder-concept-dialog.md)

Rubriques de procédures v4 :

- [Envoyer et recevoir des SMS](../bot-builder-howto-send-messages.md)
- [Enregistrer les données d’utilisateur et de conversation](../bot-builder-howto-v4-state.md)
- [Implémenter des flux de conversation séquentiels](../bot-builder-dialog-manage-conversation-flow.md)
