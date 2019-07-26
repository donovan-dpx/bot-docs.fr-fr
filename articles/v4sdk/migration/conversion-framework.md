---
title: Migrer un bot existant au sein du même projet .NET Framework | Microsoft Docs
description: Nous prenons un bot .NET v3 existant que nous migrons vers le kit SDK .NET v4, à l’aide du même projet.
keywords: migration de bot, formflow, dialogues, bot v3
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 519515a2174a7028af7bc170ca8a7c40f7d48c52
ms.sourcegitcommit: b053c0ca7f2e9e60679f7e82e583c57ae83fcb50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336734"
---
# <a name="migrate-a-net-v3-bot-to-a-net-framework-v4-bot"></a>Migrer un bot .NET v3 vers un bot .NET Framework v4

Dans cet article, nous allons convertir le bot [ContosoHelpdeskChatBot v3](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V3) en un bot v4, _sans convertir le type de projet_. Il restera un projet .NET Framework.
Cette conversion est décomposée en plusieurs étapes :

1. Mettre à jour et installer les packages NuGet
1. Mettre à jour votre fichier Global.asax.cs
1. Mettre à jour votre classe MessagesController
1. Convertir vos dialogues

Le résultat de cette conversion est le bot [ContosoHelpdeskChatBot .NET Framework v4](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework).
Pour migrer vers un bot v4 .NET Core dans un nouveau projet, consultez [Migrer un bot .NET v3 vers un bot .NET Core v4](conversion-core.md).

Le kit SDK Bot Framework v4 est basé sur la même API REST sous-jacente que le kit SDK v3. Toutefois, le kit SDK v4 est une refactorisation de la version précédente du kit pour offrir aux développeurs plus de flexibilité et de contrôle sur leurs bots. Les principaux changements du kit SDK sont notamment les suivants :

- L’état est géré via des objets de gestion d’état et des accesseurs de propriété.
- La configuration du gestionnaire de tour et la transmission des activités à ce dernier ont changé.
- Les dialogues scorables n’existent plus. Vous pouvez rechercher des commandes « globales » dans le gestionnaire de tour, avant de passer le contrôle à vos dialogues.
- Nouvelle bibliothèque de dialogues qui est très différente de celle de la version précédente. Vous devez convertir les anciens dialogues dans le nouveau système de dialogues à l’aide de dialogues composants et en cascade ainsi que de l’implémentation de la communauté de dialogues Formflow pour v4.

Pour plus d’informations sur des changements spécifiques, consultez [Différences entre le SDK .NET v3 et v4](migration-about.md).

## <a name="update-and-install-nuget-packages"></a>Mettre à jour et installer les packages NuGet

1. Mettez à jour **Microsoft.Bot.Builder.Azure** et **Microsoft.Bot.Builder.Integration.AspNet.WebApi** vers la dernière version stable.

    Vous mettez ainsi également à jour les packages **Microsoft.Bot.Builder** et **Microsoft.Bot.Connector**, dans la mesure où ce sont des dépendances.

1. Supprimez le package **Microsoft.Bot.Builder.History**. Il ne fait pas partie du kit SDK v4.
1. Ajoutez **Autofac.WebApi2**

    Nous l’utiliserons pour faciliter l’injection de dépendances dans ASP.NET.

1. Ajoutez **Bot.Builder.Community.Dialogs.Formflow**

    Il s’agit d’une bibliothèque de communauté pour la génération de dialogues v4 à partir de fichiers de définition Formflow v3. Comme **Microsoft.Bot.Builder.Dialogs** est l’une de ses dépendances, elle est également installée automatiquement.

> [!TIP]
> Si votre projet cible .NET Framework 4.6, vous devez le mettre à jour vers la version 4.6.1 ou ultérieure, car **Bot.Builder.Community.Dialogs.Formflow** est une bibliothèque .NET Standard 2.0.
> Pour plus d’informations, consultez la page sur le [support de l’implémentation .NET](https://docs.microsoft.com/en-us/dotnet/standard/net-standard#net-implementation-support).

Si vous procédez à la génération à ce stade, vous obtenez des erreurs du compilateur. Vous pouvez les ignorer. Une fois la conversion terminée, nous obtenons un code fonctionnel.

## <a name="update-your-globalasaxcs-file"></a>Mettre à jour votre fichier Global.asax.cs

Une partie de la génération de modèles automatique a changé et nous devons configurer des composants de l’infrastructure de [gestion d’état](../bot-builder-concept-state.md) nous-mêmes dans v4. Par exemple, v4 utilise un adaptateur de bot pour gérer l’authentification et transférer des activités au code de votre bot, et nous devons déclarer les propriétés d’état en amont.

Nous allons créer une propriété d’état pour `DialogState`, dont nous avons maintenant besoin pour la prise en charge des dialogues dans v4. Nous allons utiliser l’injection de dépendances pour faire parvenir les informations nécessaires au contrôleur et au code de bot.

Dans **Global.asax.cs** :

1. Mettez à jour les instructions `using` :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=4-13)]

1. Supprimez ces lignes de la méthode `Application_Start` :  
    [!code-csharp[Removed lines](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V3/ContosoHelpdeskChatBot/Global.asax.cs?range=23-24)]

    Ensuite, insérez cette ligne :  
    [!code-csharp[Reference BotConfig.Register](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=22)]

1. Supprimez la méthode `RegisterBotModules` qui n’est plus référencée.

1. Remplacez la méthode `BotConfig.UpdateConversationContainer` par cette méthode `BotConfig.Register`, où nous allons inscrire les objets nécessaires pour prendre en charge l’injection de dépendances. Ce bot n’utilise ni l’état _user_ ni l’état _private conversation_.Ainsi, nous créons uniquement l’objet de gestion de l’état de la conversation.  
    [!code-csharp[Define BotConfig.Register](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Global.asax.cs?range=31-61)]

## <a name="update-your-messagescontroller-class"></a>Mettre à jour votre classe MessagesController

C’est là que le bot démarre un tour dans la version v4, le changement est donc important. À l’exception du gestionnaire de tours du bot, la majeure partie du code peut être considérée comme réutilisable. Dans votre fichier **Controllers\MessagesController.cs** :

1. Mettez à jour les instructions `using` :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=4-8)]

1. Supprimez l’attribut `[BotAuthentication]` de la classe. Dans v4, l’adaptateur du bot gère l’authentification.

1. Ajoutez ces champs et un constructeur pour les initialiser. ASP.NET et Autofac utilisent l’injection de dépendances pour obtenir les valeurs de paramètres (pour ce faire, nous avons inscrit des objets adapter et bot dans **Global.asax.cs**)  
    [!code-csharp[Fields and constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=14-21)]

1. Remplacez le corps de la méthode `Post`. Nous utilisons l’adaptateur pour appeler la boucle de messages de notre bot (gestionnaire de tours).  
    [!code-csharp[Post method](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Controllers/MessagesController.cs?range=23-31)]

### <a name="delete-the-cancelscorable-and-globalmessagehandlersbotmodule-classes"></a>Supprimer les classes CancelScorable et GlobalMessageHandlersBotModule

Comme les dialogues scorables n’existent pas dans la v4 et que nous avons mis à jour le gestionnaire de tour pour réagir à un message `cancel`, nous pouvons supprimer les classes `CancelScorable` (dans **Dialogs\CancelScorable.cs**) et `GlobalMessageHandlersBotModule`.

## <a name="create-your-bot-class"></a>Créer votre classe de bot

Dans la version v4, la logique du gestionnaire de tours ou de la boucle de messages se trouve principalement dans un fichier de bot. Nous dérivons de `ActivityHandler`, qui définit des gestionnaires pour les types d’activités les plus courants.

1. Créez un fichier **Bots\DialogBots.cs**.

1. Mettez à jour les instructions `using` :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=4-8)]

1. Dérivez `DialogBot` de `ActivityHandler`, puis ajoutez un paramètre générique pour le dialogue.  
    [!code-csharp[Class definition](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=19)]

1. Ajoutez ces champs et un constructeur pour les initialiser. ASP.NET et Autofac utilisent l’injection de dépendances pour obtenir les valeurs de paramètres.  
    [!code-csharp[Fields and constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=21-28)]

1. Substituez `OnMessageActivityAsync` pour appeler le dialogue principal (nous allons bientôt définir la méthode d’extension `Run`)  
    [!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=38-47)]

1. Substituez `OnTurnAsync` pour enregistrer l’état de la conversation à la fin du tour. Dans la version v4, nous devons effectuer cette opération explicitement pour écrire l’état dans la couche de persistance. La méthode `ActivityHandler.OnTurnAsync` appelle des méthodes de gestionnaire d’activités spécifiques, selon le type d’activité reçu. Nous enregistrons donc l’état après l’appel à la méthode de base.  
    [!code-csharp[OnTurnAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Bots/DialogBot.cs?range=30-36)]

### <a name="create-the-run-extension-method"></a>Créer la méthode d’extension Run

Nous allons créer une méthode d’extension pour centraliser le code nécessaire à l’exécution d’un dialogue de composant système à partir de notre bot.

Créez un fichier **DialogExtensions.cs** et implémentez une méthode d’extension `Run`.  
[!code-csharp[The extension](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/DialogExtensions.cs?range=4-41)]

## <a name="convert-your-dialogs"></a>Convertir vos dialogues

Nous allons apporter de nombreux changements aux dialogues d’origine pour les migrer vers le kit SDK v4. Ne vous souciez pas des erreurs du compilateur pour l’instant. Elles seront résolues une fois la conversion terminée.
Dans l’intérêt de ne pas modifier le code d’origine plus que nécessaire, il restera des avertissements du compilateur une fois la migration terminée.

Tous nos dialogues dérivent de `ComponentDialog`, au lieu d’implémenter l’interface `IDialog<object>` de v3.

Ce bot contient quatre dialogues que nous devons convertir :

| | |
|---|---|
| [RootDialog](#update-the-root-dialog) | Présente les options et démarre les autres dialogues. |
| [InstallAppDialog](#update-the-install-app-dialog) | Gère les demandes d’installation d’une application sur un ordinateur. |
| [LocalAdminDialog](#update-the-local-admin-dialog) | Gère les demandes de droits d’administrateur sur l’ordinateur local. |
| [ResetPasswordDialog](#update-the-reset-password-dialog) | Gère les demandes de réinitialisation de votre mot de passe. |

Ces dialogues recueillent des entrées, mais n’effectuent aucune de ces opérations sur votre ordinateur.

### <a name="make-solution-wide-dialog-changes"></a>Apporter des changements de dialogue à l’échelle de la solution

1. Pour l’ensemble de la solution, remplacez toutes les occurrences de `IDialog<object>` par `ComponentDialog`.
1. Pour l’ensemble de la solution, remplacez toutes les occurrences de `IDialogContext` par `DialogContext`.
1. Pour chaque classe de dialogue, supprimez l’attribut `[Serializable]`.

Le flux de contrôle et la messagerie au sein des dialogues n’étant plus gérés de la même façon, nous allons devoir examiner ce point lors de la conversion de chaque dialogue.

| Opération | Code v3 | Code v4 |
| :--- | :--- | :--- |
| Gérer le début de votre dialogue | Implémentez `IDialog.StartAsync` | Définissez ceci comme première étape d’un dialogue en cascade ou implémentez `Dialog.BeginDialogAsync` |
| Gérer la continuation de votre dialogue | Appelez `IDialogContext.Wait` | Ajoutez des étapes supplémentaires à un dialogue en cascade ou implémentez `Dialog.ContinueDialogAsync` |
| Envoyer un message à l’utilisateur | Appelez `IDialogContext.PostAsync` | Appelez `ITurnContext.SendActivityAsync` |
| Démarrer un dialogue enfant | Appelez `IDialogContext.Call` | Appelez `DialogContext.BeginDialogAsync` |
| Signaler que le dialogue en cours est terminé | Appelez `IDialogContext.Done` | Appelez `DialogContext.EndDialogAsync` |
| Obtenir l’entrée de l’utilisateur | Utilisez un paramètre `IAwaitable<IMessageActivity>` | Utilisez une invite à partir d’une cascade, ou `ITurnContext.Activity` |

Remarques concernant le code v4 :

- Dans le code d’un dialogue, utilisez la propriété `DialogContext.Context` pour obtenir le contexte de tour actuel.
- Les étapes en cascade ont un paramètre `WaterfallStepContext`, qui dérive de `DialogContext`.
- Toutes les classes de dialogue et d’invite concrètes dérivent de la classe `Dialog` abstraite.
- Vous affectez un ID quand vous créez un dialogue composant. Chaque dialogue d’un jeu de dialogues doit recevoir un ID unique dans ce jeu.

### <a name="update-the-root-dialog"></a>Mettre à jour le dialogue racine

Dans ce bot, le dialogue racine invite l’utilisateur à choisir parmi un ensemble d’options, puis démarre un dialogue enfant en fonction de ce choix. Celui-ci effectue ensuite une boucle pendant la durée de vie de la conversation.

- Nous pouvons définir le flux principal opérationnel comme dialogue en cascade, ce qui est un nouveau concept dans le kit SDK v4. Il s’exécute via un ensemble fixe d’étapes dans l’ordre. Pour plus d’informations, consultez [Implémenter des flux de conversation séquentiels](~/v4sdk/bot-builder-dialog-manage-conversation-flow.md).
- L’invite est désormais gérée par le biais de classes d’invite, qui sont des dialogues enfants courts qui demandent une entrée, effectuent un traitement minimal et une validation, et retournent une valeur. Pour plus d’informations, consultez [Collecter les entrées utilisateur avec une invite de dialogue](~/v4sdk/bot-builder-prompts.md).

Dans le fichier **Dialogs/RootDialog.cs** :

1. Mettez à jour les instructions `using` :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=4-10)]

1. Nous devons convertir les options `HelpdeskOptions` à partir d’une liste de chaînes vers une liste de choix. Celle-ci sera utilisée avec une invite de choix, qui accepte le numéro du choix (dans la liste), la valeur du choix ou l’un des synonymes du choix en tant qu’entrée valide.  
    [!code-csharp[HelpDeskOptions](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=28-33)]

1. Ajoutez un constructeur. Ce code effectue les actions suivantes :
   - Un ID est affecté à chaque instance d’un dialogue lors de sa création. L’ID de dialogue fait partie du jeu de dialogues auquel le dialogue est ajouté. Rappelez-vous que le bot a été initialisé avec un objet de dialogue dans la classe `MessageController`. Chaque `ComponentDialog` a son propre jeu de dialogues interne, avec son propre ensemble d’ID de dialogue.
   - Il ajoute les autres dialogues, dont l’invite de choix, comme dialogues enfants. Ici, nous utilisons simplement le nom de classe pour chaque ID de dialogue.
   - Il définit un dialogue en cascade en trois étapes. Nous allons les implémenter dans un instant.
     - Le dialogue invite tout d’abord l’utilisateur à choisir une tâche à effectuer.
     - Il démarre ensuite le dialogue enfant associé à ce choix.
     - Pour finir, il redémarre.
   - Chaque étape de la cascade est un délégué et nous allons les implémenter ensuite, en prenant le code existant du dialogue d’origine là où nous pouvons.
   - Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_. Par défaut, il s’agit du premier dialogue enfant ajouté à un dialogue composant. Nous définissons explicitement la propriété `InitialDialogId`, ce qui signifie que le dialogue en cascade principal n’a pas à être le premier que vous ajoutez au jeu. Par exemple, si vous préférez ajouter d’abord les invites, cela vous permet de le faire sans provoquer un problème d’exécution.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=35-49)]

1. Nous pouvons supprimer la méthode `StartAsync`. Quand un dialogue composant commence, il démarre automatiquement son dialogue _initial_. Dans ce cas, il s’agit du dialogue en cascade que nous avons défini dans le constructeur. Il démarre aussi automatiquement à sa première étape.

1. Nous allons supprimer les méthodes `MessageReceivedAsync` et `ShowOptions`, puis les remplacer par la première étape de notre dialogue en cascade. Ces deux méthodes accueillent l’utilisateur et lui demandent de choisir l’une des options disponibles.
   - Vous pouvez voir ici que la liste de choix ainsi que les messages d’accueil et d’erreur sont fournis en tant qu’options dans l’appel à l’invite de nos choix.
   - Nous n’avons pas à spécifier la méthode suivante à appeler dans le dialogue, car la cascade passe à l’étape suivante à l’issue de l’invite de choix.
   - L’invite de choix effectue une boucle jusqu’à ce qu’elle reçoive une entrée valide ou que toute la pile de dialogues soit annulée.  
    [!code-csharp[PromptForOptionsAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=51-65)]

1. Nous pouvons remplacer `OnOptionSelected` par la première étape de notre dialogue en cascade. Nous commençons toujours un dialogue enfant basé sur l’entrée de l’utilisateur.
   - L’invite de choix retourne une valeur `FoundChoice`. Cela s’affiche dans la propriété `Result` du contexte d’étape. La pile de dialogues traite toutes les valeurs de retour comme des objets. Si la valeur de retour provient d’un de vos dialogues, vous connaissez le type de valeur de l’objet. Consultez [Types d’invites](../bot-builder-concept-dialog.md#prompt-types) pour obtenir une liste ce que chaque type d’invite retourne.
   - Étant donné que l’invite de choix ne va pas lever d’exception, le bloc try-catch peut être supprimé.
   - Nous devons ajouter un passage afin que cette méthode retourne toujours une valeur appropriée. Ce code ne doit jamais être atteint mais, si c’est le cas, il permettra au dialogue d’« échouer de manière appropriée ».  
    [!code-csharp[ShowChildDialogAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=67-102)]

1. Enfin, remplacez l’ancienne méthode `ResumeAfterOptionDialog` par la dernière étape de notre cascade.
    - Au lieu de mettre fin au dialogue et de retourner le numéro de ticket comme nous l’avons fait dans le dialogue d’origine, nous redémarrons la cascade en remplaçant l’instance d’origine sur la pile par une nouvelle instance de lui-même. Cette opération est possible, car l’application d’origine a toujours ignoré la valeur de retour (le numéro de ticket) et redémarré le dialogue racine.  
    [!code-csharp[ResumeAfterAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/RootDialog.cs?range=104-138)]

### <a name="update-the-install-app-dialog"></a>Mettre à jour le dialogue d’installation d’une application

Le dialogue d’installation d’une application effectue quelques tâches logiques que nous allons configurer comme un dialogue en cascade en 4 étapes. La façon dont vous factorisez le code existant en étapes en cascade est un exercice logique pour chaque dialogue. Pour chaque étape, la méthode d’origine de laquelle provient le code est indiquée.

1. Demande à l’utilisateur une chaîne à rechercher.
1. Recherche des correspondances potentielles dans une base de données.
   - S’il existe une correspondance, il la sélectionne et continue.
   - S’il existe plusieurs correspondances, il invite l’utilisateur à en choisir une.
   - S’il n’existe aucune correspondance, le dialogue s’arrête.
1. Demande à l’utilisateur un ordinateur sur lequel installer l’application.
1. Écrit les informations dans une base de données et envoie un message de confirmation.

Dans le fichier **Dialogs/InstallAppDialog.cs** :

1. Mettez à jour les instructions `using` :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=4-11)]

1. Définissez une constante pour la clé que nous allons utiliser en vue d’effectuer le suivi des informations collectées.  
    [!code-csharp[Key ID](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=17-18)]

1. Ajoutez un constructeur et initialisez le jeu de dialogues du composant.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=20-33)]

1. Nous pouvons remplacer `StartAsync` par la première étape de notre dialogue en cascade.
    - Comme nous devons gérer l’état nous-mêmes, nous allons suivre l’objet d’installation d’une application dans l’état du dialogue.
    - Le message demandant à l’utilisateur de fournir une entrée devient une option dans l’appel à l’invite.  
    [!code-csharp[GetSearchTermAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=35-50)]

1. Nous pouvons remplacer `appNameAsync` et `multipleAppsAsync` par la seconde étape de notre dialogue en cascade.
    - Nous obtenons le résultat de l’invite maintenant, au lieu de regarder simplement le dernier message de l’utilisateur.
    - La requête de base de données et les instructions if sont organisées de la même façon que dans `appNameAsync`. Le code dans chaque bloc de l’instruction if a été mis à jour pour fonctionner avec les dialogues v4.
        - S’il existe une correspondance, nous allons mettre à jour l’état du dialogue et passer à l’étape suivante.
        - S’il existe plusieurs correspondances, nous allons utiliser l’invite de nos choix pour demander à l’utilisateur de choisir parmi la liste des options. Cela signifie que nous pouvons simplement supprimer `multipleAppsAsync`.
        - S’il n’existe aucune correspondance, nous allons mettre fin à ce dialogue et retourner la valeur null au dialogue racine.  
    [!code-csharp[ResolveAppNameAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=52-91)]

1. `appNameAsync` demande également à l’utilisateur le nom de son ordinateur une fois la requête résolue. Nous allons capturer cette partie de la logique à l’étape suivante de la cascade.
    - Là encore, dans v4, nous devons gérer l’état nous-mêmes. La seule difficulté ici est que nous pouvons parvenir à cette étape via deux branches logiques différentes de l’étape précédente.
    - Nous allons demander à l’utilisateur un nom d’ordinateur à l’aide de la même invite de texte que précédemment, en fournissant simplement différentes options cette fois.  
    [!code-csharp[GetMachineNameAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=93-114)]

1. La logique de `machineNameAsync` est encapsulée dans la dernière étape de notre cascade.
    - Nous récupérons le nom de l’ordinateur à partir du résultat de l’invite de texte et mettons à jour l’état du dialogue.
    - Nous supprimons l’appel pour mettre à jour la base de données, comme le code de prise en charge est dans un autre projet.
    - Nous envoyons ensuite le message de réussite à l’utilisateur et mettons fin au dialogue.  
    [!code-csharp[SubmitRequestAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=116-135)]

1. Pour simuler l’appel de la base de données, nous simulons un `getAppsAsync` pour interroger une liste statique au lieu de la base de données.  
    [!code-csharp[GetAppsAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/InstallAppDialog.cs?range=137-200)]

### <a name="update-the-local-admin-dialog"></a>Mettre à jour le dialogue d’administrateur local

Dans v3, ce dialogue accueille l’utilisateur, démarre le dialogue Formflow, puis enregistre le résultat dans une base de données. Cela se traduit facilement en une cascade en deux étapes.

1. Mettez à jour les instructions `using`. Notez que ce dialogue inclut un dialogue Formflow v3. Dans v4, nous pouvons utiliser la bibliothèque Formflow de communauté.  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=4-8)]

1. Nous pouvons supprimer la propriété d’instance pour `LocalAdmin`, comme le résultat sera disponible dans l’état du dialogue.

1. Ajoutez un constructeur et initialisez le jeu de dialogues du composant. Le dialogue Formflow est créé de la même façon. Nous allons simplement l’ajouter au jeu de dialogues de notre composant dans le constructeur.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=14-23)]

1. Nous pouvons remplacer `StartAsync` par la première étape de notre dialogue en cascade. Nous avons déjà créé le Formflow dans le constructeur et les deux autres instructions sont traduites comme suit. Notez que `FormBuilder` attribue le nom de type du modèle comme l’ID du dialogue généré, qui est `LocalAdminPrompt` pour ce modèle.  
    [!code-csharp[BeginFormflowAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=25-35)]

1. Nous pouvons remplacer `ResumeAfterLocalAdminFormDialog` par la seconde étape de notre dialogue en cascade. Nous devons obtenir la valeur de retour à partir du contexte d’étape et non à partir d’une propriété d’instance.  
    [!code-csharp[SaveResultAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=37-50)]

1. `BuildLocalAdminForm` reste en grande partie identique, à ceci près que Formflow ne met pas à jour la propriété d’instance.  
    [!code-csharp[BuildLocalAdminForm](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/LocalAdminDialog.cs?range=52-76)]

### <a name="update-the-reset-password-dialog"></a>Mettre à jour le dialogue de réinitialisation du mot de passe

Dans v3, ce dialogue accueille l’utilisateur, autorise l’utilisateur avec un code d’accès, échoue ou démarre le dialogue Formflow, puis réinitialise le mot de passe. Cela se traduit néanmoins bien en une cascade.

1. Mettez à jour les instructions `using`. Notez que ce dialogue inclut un dialogue Formflow v3. Dans v4, nous pouvons utiliser la bibliothèque Formflow de communauté.  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=4-9)]

1. Ajoutez un constructeur et initialisez le jeu de dialogues du composant. Le dialogue Formflow est créé de la même façon. Nous allons simplement l’ajouter au jeu de dialogues de notre composant dans le constructeur.  
    [!code-csharp[Constructor](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=15-25)]

1. Nous pouvons remplacer `StartAsync` par la première étape de notre dialogue en cascade. Nous avons déjà créé le Formflow dans le constructeur. Sinon, nous conservons la même logique, en traduisant uniquement les appels v3 en leurs équivalents v4.  
    [!code-csharp[BeginFormflowAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=27-45)]

1. `sendPassCode` est considéré principalement comme un exercice. Le code d’origine est commenté et la méthode retourne simplement la valeur true. En outre, nous pouvons là encore supprimer l’adresse e-mail, car elle n’a pas été utilisée dans le bot d’origine.  
    [!code-csharp[SendPassCode](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=47-81)]

1. `BuildResetPasswordForm` ne contient aucune modification.

1. Nous pouvons remplacer `ResumeAfterResetPasswordFormDialog` par la deuxième étape de notre cascade et nous allons obtenir la valeur de retour à partir du contexte d’étape. Nous avons supprimé l’adresse e-mail que le dialogue d’origine n’a pas utilisée et nous avons fourni un résultat factice au lieu d’interroger la base de données. Nous conservons la même logique, en traduisant uniquement les appels v3 en leurs équivalents v4.  
    [!code-csharp[ProcessRequestAsync](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Dialogs/ResetPasswordDialog.cs?range=90-113)]

### <a name="update-models-as-necessary"></a>Mettre à jour les modèles en fonction des besoins

Nous devons mettre à jour les instructions `using` dans certains des modèles qui font référence à la bibliothèque Formflow.

1. Dans `LocalAdminPrompt`, remplacez-les par ceci :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Models/LocalAdminPrompt.cs?range=4)]

1. Dans `ResetPasswordPrompt`, remplacez-les par ceci :  
    [!code-csharp[Using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/ContosoHelpdeskChatBot-V4NetFramework/ContosoHelpdeskChatBot/Models/ResetPasswordPrompt.cs?range=4-5)]

## <a name="update-webconfig"></a>Mettre à jour Web.config

Commentez les clés de configuration pour **MicrosoftAppId** et **MicrosoftAppPassword**. Cela vous permettra de déboguer le bot localement sans avoir à fournir ces valeurs à l’émulateur.

## <a name="run-and-test-your-bot-in-the-emulator"></a>Exécuter et tester votre bot dans l’émulateur

À ce stade, nous devrions être en mesure d’exécuter le bot localement dans IIS et de s’y attacher avec l’émulateur.

1. Exécutez le bot dans IIS.
1. Démarrez l’émulateur et connectez-vous au point de terminaison du bot (par exemple, **http://localhost:3978/api/messages** ).
    - Si vous exécutez le bot pour la première fois, cliquez sur **Fichier > Nouveau bot** et suivez les instructions à l’écran. Sinon, cliquez sur **Fichier > Ouvrir un bot** pour ouvrir un bot existant.
    - Vérifiez bien vos paramètres de port dans la configuration. Par exemple, si le bot est ouvert dans votre navigateur sur `http://localhost:3979/`, définissez dans l’émulateur le point de terminaison du bot sur `http://localhost:3979/api/messages`.
1. Les quatre dialogues doivent tous fonctionner et vous pouvez définir des points d’arrêt dans les étapes en cascade pour vérifier le contexte et l’état des dialogues à ces points.

## <a name="additional-resources"></a>Ressources supplémentaires

Rubriques conceptuelles v4 :

- [Fonctionnement des bots](../bot-builder-basics.md)
- [Gestion de l’état](../bot-builder-concept-state.md)
- [Bibliothèque de boîtes de dialogues](../bot-builder-concept-dialog.md)

Rubriques de procédures v4 :

- [Envoyer et recevoir des SMS](../bot-builder-howto-send-messages.md)
- [Enregistrer les données d’utilisateur et de conversation](../bot-builder-howto-v4-state.md)
- [Implémenter des flux de conversation séquentiels](../bot-builder-dialog-manage-conversation-flow.md)
- [Déboguer avec l’émulateur](../../bot-service-debug-emulator.md)
- [Ajouter la télémétrie à votre bot](../bot-builder-telemetry.md)
