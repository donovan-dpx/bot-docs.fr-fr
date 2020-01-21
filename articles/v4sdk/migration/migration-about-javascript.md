---
title: Différences entre les kits de développement logiciel (SDK) NodeJS v3 et v4 - Bot Service
description: Décrit les différences entre les kits SDK NodeJS v3 et v4.
keywords: migration de bot, dialogues, état
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/28/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 5d614fc34f3daa9deddb2ccedbe58bdef2ba067a
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791041"
---
# <a name="differences-between-the-v3-and-v4-javascript-sdk"></a>Différences entre les kits SDK JavaScript v3 et v4

La v4 du kit SDK Bot Framework prend en charge le même service Bot Framework sous-jacent que la v3. Toutefois, la v4 est une refactorisation de la version précédente du kit SDK qui offre aux développeurs davantage de flexibilité et de contrôle sur leurs bots. Parmi les principaux changements apportés au kit SDK, citons les suivants :

- Présentation d’un adaptateur de bot
  - Il fait partie de la pile de traitement d’activité
  - Il gère l’authentification de Bot Framework
  - Il gère le trafic entrant et sortant entre un canal et le gestionnaire de tour de votre bot, encapsulant les appels au Bot Framework Connector
  - Il initialise le contexte pour chaque tour
  - Pour plus d’informations, consultez [Fonctionnement des bots](../bot-builder-basics.md).
- Gestion de l’état refactorisée
  - Les données d’état ne sont plus automatiquement disponibles au sein d’un bot
  - L’état est désormais géré par le biais d’objets de gestion d’état et d’accesseurs de propriété
  - Pour plus d’informations, consultez [Gestion de l’état](../bot-builder-concept-state.md)
- Nouvelle bibliothèque de dialogues
  - Les dialogues v3 doivent être réécrits pour la nouvelle bibliothèque de dialogues
  - Les dialogues scorables n’existent plus. Avant de passer le contrôle à vos dialogues, vous pouvez rechercher des commandes « globales » dans le gestionnaire de tours. Selon la façon dont vous concevez votre bot v4, cette opération peut s’effectuer dans le gestionnaire de messages ou dans un dialogue parent. Pour obtenir un exemple, découvrez comment [gérer les interruptions par l’utilisateur](../bot-builder-howto-handle-user-interrupt.md).
  - Pour plus d’informations, consultez [Bibliothèque des dialogues](../bot-builder-concept-dialog.md).

## <a name="activity-processing"></a>Traitement des activités

Quand vous créez l’adaptateur pour votre bot, vous fournissez également un délégué de gestionnaire de messages qui reçoit les activités entrantes des canaux et des utilisateurs. L’adaptateur crée un objet de contexte de tour pour chaque activité reçue. Il transmet l’objet de contexte de tour au gestionnaire de tours du bot, puis supprime l’objet une fois le tour terminé.

Le gestionnaire de tour peut recevoir de nombreux types d’activités. En général, vous ne transférez que les activités liées aux _messages_ vers les dialogues de votre bot. Si vous dérivez votre bot de `ActivityHandler`, le gestionnaire de tours du bot transfère toutes les activités de message à `OnMessage`. Substituez cette méthode pour ajouter la logique de gestion des messages. Pour plus d’informations sur les types d’activités, consultez la rubrique sur le [schéma d’activité](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md).

### <a name="handling-turns"></a>Gestion des tours

Lors du traitement d’un message, utilisez le contexte de tour pour obtenir des informations sur l’activité entrante et envoyer des activités à l’utilisateur :

|Activité |Description |
|:---|:---|
| Pour obtenir l’activité entrante | Obtenez la propriété `Activity` du contexte de tour. |
| Pour créer une activité et l’envoyer à l’utilisateur | Appelez la méthode `SendActivity` du contexte de tour.<br/> Pour plus d’informations, consultez la rubrique sur l’[envoi et la réception d’un SMS](../../rest-api/bot-framework-rest-direct-line-1-1-receive-messages.md) et [Ajouter des médias aux messages](../bot-builder-howto-add-media-attachments.md). |

La classe `MessageFactory` fournit des méthodes d’assistance pour créer et mettre en forme des activités.

### <a name="interruptions"></a>Interruptions

Gérez ces éléments dans la boucle de messages du bot. Pour la procédure à suivre avec les dialogues v4, consultez la rubrique sur la [gestion des interruptions de l’utilisateur](../bot-builder-howto-handle-user-interrupt.md).

## <a name="state-management"></a>Gestion de l'état

Dans v3, vous pouvez stocker des données de conversation dans le service Bot State, qui fait partie d’un ensemble plus vaste de services fournis par Bot Framework. Toutefois, ce service a été supprimé depuis le 31 mars 2018. À partir de v4, les considérations de conception sur la gestion de l’état sont les mêmes que pour n’importe quelle application web, avec un certain nombre d’options disponibles. Sa mise en cache en mémoire et dans le même processus est généralement le plus simple. Toutefois, pour les applications de production, vous devez stocker l’état plus durablement, par exemple dans une base de données SQL ou NoSQL, ou sous forme d’objets blob.

La v4 n’utilise ni les propriétés `UserData`, `ConversationData` et `PrivateConversationData` ni les conteneurs de données pour gérer l’état.
L’état est désormais géré par le biais d’objets de gestion d’état et d’accesseurs de propriété, comme décrit dans la [gestion de l’état](../bot-builder-concept-state.md).

La v4 définit des classes `UserState`, `ConversationState` et `PrivateConversationState` qui gèrent les données d’état pour le bot. Vous devez créer un accesseur de propriété d’état pour chaque propriété à rendre persistante, au lieu de simplement lire et écrire dans un conteneur de données prédéfini.

### <a name="setting-up-state"></a>Configuration de l’état

L’état doit être configuré dans le fichier de point d’entrée de l’application, généralement « index.js » ou « app.js » dans les applications NodeJS. 

1. Initialisez un ou plusieurs objets qui implémentent l’interface `Storage` fournie par botbuilder-core. Cela représente le magasin de stockage pour les données de votre bot.
    Le kit SDK v4 fournit quelques [couches de stockage](../bot-builder-concept-state.md#storage-layer).
    Vous pouvez également implémenter votre propre couche pour vous connecter à un autre type de magasin.
1. Ensuite, créez et inscrivez des objets de [gestion d’état](../bot-builder-concept-state.md#state-management) en fonction des besoins.
    Vous disposez des mêmes étendues que dans la v3 et vous pouvez en créer d’autres si nécessaire.
1. Enfin, créez et inscrivez des [accesseurs de propriété d’état](../bot-builder-concept-state.md#state-property-accessors) pour les propriétés dont votre bot a besoin.
    Au sein d’un objet de gestion d’état, chaque accesseur de propriété nécessite un nom unique.

### <a name="using-state"></a>Utilisation de l’état

Utilisez les accesseurs de propriété d’état pour obtenir et mettre à jour vos propriétés, et utilisez les objets de gestion d’état pour écrire tous les changements dans le stockage. Sachant que vous devez prendre en compte les problèmes de concurrence, voici comment accomplir quelques tâches courantes.

| Tâche|Description |
|:---|:---|
| Créer un accesseur de propriété d’état | Appelez la méthode `createProperty` de votre objet `BotState`. <br/>`BotState` est la classe de base abstraite pour la conversation, la conversation privée et l’état utilisateur. |
| Obtenir la valeur actuelle d’une propriété | Appelez `StatePropertyAccessor.get(TurnContext)`.<br/>Si aucune valeur n’a été définie précédemment, le paramètre d’usine par défaut est utilisé pour générer une valeur. |
| Rendre les changements d’état apportés au stockage persistants | Appelez `BotState.saveChanges(TurnContext, boolean)` pour tout objet de gestion d’état dans lequel l’état a changé avant la fermeture du gestionnaire de tour. |

### <a name="managing-concurrency"></a>Gérer l'accès concurrentiel

Votre bot peut avoir besoin de gérer la concurrence de l’état. Pour plus d’informations, consultez la section [Enregistrement de l’état](../bot-builder-concept-state.md#saving-state) dans **Gestion de l’état** et la section [Gérer la concurrence à l’aide des étiquettes d’entité](../bot-builder-howto-v4-storage.md#manage-concurrency-using-etags) dans **Écrire directement dans le stockage**.

## <a name="dialogs-library"></a>Bibliothèque des dialogues

Voici quelques-uns des principaux changements apportés aux dialogues :

- La bibliothèque de dialogues est désormais un package npm distinct : **botbuilder-dialogs**.
- L’état du dialogue est géré par le biais d’une classe d’état `DialogState` et d’accesseurs de propriété.
  - La propriété d’état de dialogue est maintenant persistante entre les tours, contrairement à l’objet de dialogue.
- Au cours d’un tour, vous créez un contexte de dialogue pour un _jeu de dialogues_.
  - Ce contexte de dialogue encapsule la pile de dialogues. Cette information est persistante dans la propriété d’état du dialogue.
- Les deux versions contiennent une classe `Dialog` abstraite. Toutefois, dans v3, elle étend la classe 'ActionSet' alors que, dans v4, elle étend 'Object'.

### <a name="defining-dialogs"></a>Définition des dialogues

Même si v3 offrait un moyen souple d’implémenter des dialogues avec la classe `Dialog`, cela signifiait que vous deviez implémenter votre propre code pour des fonctionnalités telles que la validation. Dans v4, il existe désormais des classes d’invite qui valident automatiquement l’entrée utilisateur pour vous, en la limitant à un type particulier (par exemple, un entier) et en invitant l’utilisateur à intervenir de nouveau jusqu’à ce qu’il renseigne une entrée valide. En règle générale, cela signifie moins de code à écrire pour le développeur.

Vous disposez à présent de plusieurs options pour définir des dialogues :

|Type de dialogue| Description |
|:---|:---|
| Dialogue composant (dérivé de la classe `ComponentDialog`) | Vous permet d’encapsuler le code du dialogue sans conflits de nommage avec les contextes externes. Consultez la rubrique sur la [réutilisation des dialogues](../bot-builder-concept-dialog.md).|
| Dialogue en cascade (instance de la classe `WaterfallDialog`) | Conçu pour bien fonctionner avec des dialogues d’invite, qui proposent et valident différents types d’entrées utilisateur. Une cascade automatise la majeure partie du processus pour vous, mais impose une certaine forme à votre code de dialogue (consultez la rubrique sur les [flux de conversation séquentiels](../bot-builder-dialog-manage-conversation-flow.md)). |
| Dialogue personnalisé (dérivé de la classe abstraite `Dialog`) | Cette option vous donne le plus de flexibilité dans la gestion du comportement de vos dialogues, mais vous oblige également à en savoir plus sur la manière dont la pile de dialogues est implémentée. |

Lorsque vous créez un dialogue en cascade, vous définissez les étapes du dialogue dans le constructeur. L’ordre des étapes exécutées suit exactement la progression selon laquelle vous les avez déclarées, et les fait se succéder automatiquement l’une après l’autre.

Vous pouvez également créer des flux de contrôle complexes avec plusieurs dialogues ; consultez la rubrique sur les [flux de conversation avancés](../bot-builder-dialog-manage-complex-conversation-flow.md).

Pour accéder à un dialogue, vous devez placer une instance de celui-ci dans un _jeu de dialogues_, puis générer un _contexte de dialogue_ pour ce jeu. Vous devez fournir un accesseur de propriété d’état de dialogue quand vous créez un jeu de dialogues. Le framework peut ainsi rendre l’état du dialogue persistant d’un tour à l’autre. [Gestion de l’état](../bot-builder-concept-state.md) décrit la façon dont l’état est géré dans la v4.

### <a name="using-dialogs"></a>Utilisation de dialogues

Voici une liste des opérations courantes dans la v3 et la procédure à suivre pour les accomplir dans un dialogue en cascade. Notez que chaque étape d’un dialogue en cascade est censée retourner une valeur `DialogTurnResult`. Si ce n’est pas le cas, la cascade peut se terminer plus tôt.

| Opération | v3 | v4 |
|:---|:---|:---|
| Gérer le début de votre dialogue | Appelez `session.beginDialog`, en passant l’ID du dialogue | appeler le `DialogContext.beginDialog` |
| Envoyer une activité | Appelez `session.send`. | Appelez `TurnContext.sendActivity`.<br/>Utilisez la propriété `Context` du contexte d’étape pour obtenir le contexte de tour (`step.context.sendActivity`).  |
| Attendre la réponse d’un utilisateur | Appelez une invite à partir de l’étape en cascade, par exemple `builder.Prompts.text(session, 'Please enter your destination')`. Récupérez la réponse à l’étape suivante. | Utilisez return await avec `TurnContext.prompt` pour commencer un dialogue d’invite. Ensuite, récupérez le résultat à l’étape suivante de la cascade. |
| Gérer la continuation de votre dialogue | Automatique | Ajoutez des étapes supplémentaires à un dialogue en cascade ou implémentez `Dialog.continueDialog` |
| Signaler la fin du traitement jusqu’au prochain message de l’utilisateur | Appelez `session.endDialog`. | Retournez `Dialog.EndOfTurn`. |
| Commencer un dialogue enfant | Appelez `session.beginDialog`. | Utilisez return await avec la méthode `beginDialog` du contexte d’étape.<br/>Si le dialogue enfant retourne une valeur, celle-ci est disponible à l’étape suivante de la cascade par le biais de la propriété `Result` du contexte d’étape. |
| Remplacer le dialogue actuel par un nouveau dialogue | Appelez `session.replaceDialog`. | Utilisez return await avec `ITurnContext.replaceDialog`. |
| Signaler que le dialogue en cours est terminé | Appelez `session.endDialog`. | Utilisez return await avec la méthode `endDialog` du contexte d’étape. |
| Échec d’un dialogue. | Appelez `session.pruneDialogStack`. | Levez une exception à intercepter à un autre niveau du bot, terminez l’étape avec l’état `Cancelled` ou appelez la méthode `cancelAllDialogs` du contexte d’étape ou de dialogue. |

Autres remarques concernant le code v4 :

- Les différentes classes dérivées de `Prompt` dans la v4 implémentent les invites utilisateur en tant que dialogues à deux étapes distinctes. Découvrez comment [implémenter des flux de conversation séquentiels][sequential-flow].
- Utilisez `DialogSet.createContext` pour créer un contexte de dialogue pour le tour actuel.
- Au sein d’un dialogue, utilisez la propriété `DialogContext.context` pour obtenir le contexte de tour actuel.
- Les étapes en cascade ont un paramètre `WaterfallStepContext`, qui dérive de `DialogContext`.
- Toutes les classes de dialogue et d’invite concrètes dérivent de la classe `Dialog` abstraite.
- Vous affectez un ID quand vous créez un dialogue composant. Chaque dialogue d’un jeu de dialogues doit recevoir un ID unique dans ce jeu.

### <a name="passing-state-between-and-within-dialogs"></a>Transmission de l’état entre dialogues et au sein d’entre eux

Les sections [État de dialogue](../bot-builder-concept-dialog.md#dialog-state), [Propriétés du contexte d’étape d’un dialogue en cascade](../bot-builder-concept-dialog.md#waterfall-step-context-properties) et [Utilisation de dialogues](../bot-builder-concept-dialog.md#using-dialogs) de l’article **Bibliothèque de dialogues** décrivent comment gérer l’état de dialogue dans la v4.

### <a name="get-user-response"></a>Obtenir la réponse de l’utilisateur

Pour obtenir l’activité de l’utilisateur dans un tour, obtenez-la à partir du contexte de tour.

Pour inviter l’utilisateur et recevoir le résultat d’une invite :

- Ajoutez une instance d’invite appropriée à votre jeu de dialogues.
- Appelez l’invite à partir d’une étape dans un dialogue en cascade.
- Récupérez le résultat à partir de la propriété `Result` du contexte d’étape à l’étape suivante.

## <a name="additional-resources"></a>Ressources supplémentaires

Reportez-vous aux ressources suivantes pour obtenir plus de détails et d’informations générales.

| Rubrique | Description |
| :--- | :--- |
|[Migrer un bot du kit SDK JavaScript v3 vers v4](https://docs.microsoft.com/azure/bot-service/migration/conversion-javascript?view=azure-bot-service-4.0)| Transfert d’un bot JavaScript v3 vers v4|
| [Nouveautés de Bot Framework](https://docs.microsoft.com/azure/bot-service/what-is-new?view=azure-bot-service-4.0) | Améliorations et fonctionnalités clés de Bot Framework et d’Azure Bot Service|
|[Fonctionnement des bots](../bot-builder-basics.md)|Mécanisme interne d’un bot|
|[Gestion de l’état](../bot-builder-concept-state.md)|Abstractions pour faciliter la gestion d’état|
|[Bibliothèque de boîtes de dialogues](../bot-builder-concept-dialog.md)| Concepts centraux pour gérer une conversation|
|[Envoyer et recevoir des SMS](../bot-builder-howto-send-messages.md)|Principal moyen de communication d’un bot avec les utilisateurs|
|[Envoyer des médias](../bot-builder-howto-add-media-attachments.md)|Pièces jointes multimédias, comme des images, des vidéos, du contenu audio et des fichiers| 
|[Flux de conversation séquentiels](../bot-builder-dialog-manage-conversation-flow.md)| Questionnement comme principal moyen d’interaction d’un bot avec les utilisateurs|
|[Enregistrer les données d’utilisateur et de conversation](../bot-builder-howto-v4-state.md)|Suivi d’une conversation en mode sans état|
|[Flux complexes](../bot-builder-dialog-manage-complex-conversation-flow.md)|Gérer des flux de conversation complexes |
|[Réutiliser des dialogues](../bot-builder-compositcontrol.md)|Créer des dialogues indépendants pour gérer des scénarios spécifiques|
|[Interruptions](../bot-builder-howto-handle-user-interrupt.md)| Gestion des interruptions pour créer un bot efficace|
|[Schéma d’activité](https://aka.ms/botSpecs-activitySchema)|Schéma pour des humains et des logiciels automatisés|
