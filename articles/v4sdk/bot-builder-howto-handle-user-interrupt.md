---
title: Gérer les interruptions par l’utilisateur | Microsoft Docs
description: Découvrez comment gérer les interruptions par l’utilisateur et diriger le flux de conversation.
keywords: interruption, interruptions, changement de sujet, arrêt
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1c75349605e7b142035112c84c2b8684fe78ca85
ms.sourcegitcommit: 312a4593177840433dfee405335100ce59aac347
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73933554"
---
# <a name="handle-user-interruptions"></a>Gérer les interruptions par l’utilisateur

[!INCLUDE[applies-to](../includes/applies-to.md)]

La gestion des interruptions représente un aspect essentiel d’un bot efficace. Les utilisateurs ne suivent pas toujours votre flux de conversation défini, étape par étape. Ils peuvent essayer de poser une question au milieu du processus ou souhaitez tout simplement l’annuler au lieu d’aller jusqu’à la fin. Dans cette rubrique, nous examinerons certaines méthodes courantes pour gérer les interruptions par l’utilisateur dans votre bot.

## <a name="prerequisites"></a>Prérequis

- Connaissances des [concepts de base des bots][concept-basics], de la [gestion de l’état][concept-state], de la [bibliothèque de dialogues][concept-dialogs] et de la façon de [réutiliser des dialogues][component-dialogs].
- Une copie de l’exemple de bot principal en [**CSharp**][cs-sample] ou en [**JavaScript**][js-sample].

## <a name="about-this-sample"></a>À propos de cet exemple

L’exemple utilisé dans cet article modélise un bot de réservation de vol d’avion qui utilise des dialogues pour obtenir des informations sur le vol auprès de l’utilisateur. À tout moment pendant la conversation avec le bot, l’utilisateur peut émettre des commandes d’_aide_ ou d’_annulation_ pour provoquer une interruption. Il existe deux types d’interruptions que nous gérons ici :

- **Niveau du tour** : Contournez le traitement au niveau du tour, mais laissez le dialogue sur la pile avec les informations qui ont été fournies. Au tour suivant, reprenez là où vous vous êtes arrêté. 
- **Niveau du dialogue** : Annulez complètement le traitement, pour que le bot puisse tout recommencer.

## <a name="define-and-implement-the-interruption-logic"></a>Définir et implémenter la logique d’interruption

Tout d’abord, nous définissons et implémentons les interruptions d’_aide_ et d’_annulation_.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour utiliser les dialogues, installez le package NuGet **Microsoft.Bot.Builder.Dialogs**.

**Dialogs\CancelAndHelpDialog.cs**

Nous commençons en implémentant la classe `CancelAndHelpDialog` pour gérer les interruptions par l’utilisateur.

[!code-csharp[Class signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=12)]

Dans la classe `CancelAndHelpDialog`, la méthode `OnContinueDialogAsync` appelle la méthode `InerruptAsync` pour vérifier si l’utilisateur a interrompu le flux normal ou pas. Si le flux est interrompu, les méthodes de classe de base sont appelées. Sinon, la valeur de retour de `InterruptAsync` est retourné.

[!code-csharp[Overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=22-31)]

Si l’utilisateur tape « aide », la méthode `InterrupAsync` envoie un message, puis appelle `DialogTurnResult (DialogTurnStatus.Waiting)` pour indiquer que le dialogue en haut de la pile attend une réponse de l’utilisateur. Ainsi, le flux de conversation est interrompu pour un seul tour, et au tour suivant, nous reprenons là où nous nous sommes arrêtés.

Si l’utilisateur tape « annuler », elle appelle `CancelAllDialogsAsync` sur son contexte de dialogue interne, ce qui efface sa pile de dialogues et entraîne sa fermeture avec un état annulé et aucune valeur de résultat. Pour `MainDialog` (illustré plus tard), il apparaît que le dialogue de réservation s’est terminé et a retourné Null, comme quand l’utilisateur choisit de ne pas confirmer sa réservation.

[!code-csharp[Interrupt](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/CancelAndHelpDialog.cs?range=33-56)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des dialogues, installez le package npm **botbuilder-dialogs**.

**dialogs/cancelAndHelpDialog.js**

Nous commençons en implémentant la classe `CancelAndHelpDialog` pour gérer les interruptions par l’utilisateur.

[!code-javascript[Class signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=11)]

Dans la classe `CancelAndHelpDialog`, la méthode `onContinueDialog` appelle la méthode `interrupt` pour vérifier si l’utilisateur a interrompu le flux normal ou pas. Si le flux est interrompu, les méthodes de classe de base sont appelées. Sinon, la valeur de retour de `interrupt` est retourné.

[!code-javascript[Overrides](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=12-18)]

Si l’utilisateur tape « aide », la méthode `interrupt` envoie un message, puis retourne un objet `{ status: DialogTurnStatus.waiting }` pour indiquer que le dialogue en haut de la pile attend une réponse de l’utilisateur. Ainsi, le flux de conversation est interrompu pour un seul tour, et au tour suivant, nous reprenons là où nous nous sommes arrêtés.

Si l’utilisateur tape « annuler », elle appelle `cancelAllDialogs` sur son contexte de dialogue interne, ce qui efface sa pile de dialogues et entraîne sa fermeture avec un état annulé et aucune valeur de résultat. Pour `MainDialog` (illustré plus tard), il apparaît que le dialogue de réservation s’est terminé et a retourné Null, comme quand l’utilisateur choisit de ne pas confirmer sa réservation.

[!code-javascript[Interrupt](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/cancelAndHelpDialog.js?range=20-39)]

---

## <a name="check-for-interruptions-each-turn"></a>Rechercher des interruptions à chaque tour

Maintenant que nous avons vu comment fonctionne la classe de gestion des interruptions, prenons du recul pour examiner ce qui se passe quand le bot reçoit un nouveau message de l’utilisateur.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\MainDialog.cs**

Au moment où la nouvelle activité de message arrive, le bot exécute `MainDialog`. `MainDialog` invite l’utilisateur à indiquer ce qu’il veut. Ensuite, `BookingDialog` est démarré dans la méthode `MainDialog.ActStepAsync`, avec un appel à `BeginDialogAsync` comme indiqué ci-dessous.

[!code-csharp[ActStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=58-101&highlight=6,26)]

Par la suite, dans la méthode `FinalStepAsync` de la classe `MainDialog`, le dialogue de réservation se termine et la réservation est considérée comme terminée ou annulée.

[!code-csharp[FinalStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=130-150)]

Le code dans `BookingDialog` n’apparaît pas ici car il n’est pas directement liée à la gestion des interruptions. Il est utilisé pour inviter les utilisateurs à donner des détails sur la réservation. Vous trouverez ce code dans **Dialogs\BookingDialogs.cs**.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Au moment où la nouvelle activité de message arrive, le bot exécute `MainDialog`. `MainDialog` invite l’utilisateur à indiquer ce qu’il veut. Ensuite, `bookingDialog` est démarré dans la méthode `MainDialog.actStep`, avec un appel à `beginDialog` comme indiqué ci-dessous.

[!code-javascript[Act step](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=71-115&highlight=6,27)]

Par la suite, dans la méthode `finalStep` de la classe `MainDialog`, le dialogue de réservation se termine et la réservation est considérée comme terminée ou annulée.

[!code-javascript[Final step](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=142-159)]

Le code dans `BookingDialog` n’apparaît pas ici car il n’est pas directement liée à la gestion des interruptions. Il est utilisé pour inviter les utilisateurs à donner des détails sur la réservation. Vous trouverez ce code dans **dialogs/bookingDialogs.js**.

---

## <a name="handle-unexpected-errors"></a>Gérer les erreurs inattendues

Ensuite, nous traitons les exceptions non gérées susceptibles de se produire.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**AdapterWithErrorHandler.cs**

Dans notre exemple, le gestionnaire `OnTurnError` de l’adaptateur reçoit toutes les exceptions levées par la logique de tour de votre bot. Si une exception est levée, le gestionnaire supprime l’état de la conversation actuelle pour empêcher le bot de rester bloqué dans une boucle d’erreur provoquée par un état incorrect.

[!code-csharp[AdapterWithErrorHandler](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/AdapterWithErrorHandler.cs?range=19-50)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Dans notre exemple, le gestionnaire `onTurnError` de l’adaptateur reçoit toutes les exceptions levées par la logique de tour de votre bot. Si une exception est levée, le gestionnaire supprime l’état de la conversation actuelle pour empêcher le bot de rester bloqué dans une boucle d’erreur provoquée par un état incorrect.

[!code-javascript[AdapterWithErrorHandler](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/index.js?range=35-57)]

---

## <a name="register-services"></a>Inscrire des services

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Startup.cs**

Enfin, dans `Startup.cs`, le bot est créé en tant que bot passager, et à chaque tour, une nouvelle instance du bot est créée.

[!code-csharp[Add transient bot](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Startup.cs?range=43-44)]

À titre de référence, voici les définitions de classe utilisées dans l’appel pour créer le bot ci-dessus.

[!code-csharp[MainDialog signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Dialogs/MainDialog.cs?range=17)]
[!code-csharp[DialogAndWelcomeBot signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Bots/DialogAndWelcomeBot.cs?range=16)]
[!code-csharp[DialogBot signature](~/../botbuilder-samples/samples/csharp_dotnetcore/13.core-bot/Bots/DialogBot.cs?range=18)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Enfin, dans `index.js`, le bot est créé.

[!code-javascript[Create bot](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/index.js?range=75-78)]

À titre de référence, voici les définitions de classe utilisées dans l’appel pour créer le bot ci-dessus.

[!code-javascript[MainDialog signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/dialogs/mainDialog.js?range=11)]
[!code-javascript[DialogAndWelcomeBot signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/bots/dialogAndWelcomeBot.js?range=8)]
[!code-javascript[DialogBot signature](~/../botbuilder-samples/samples/javascript_nodejs/13.core-bot/bots/dialogBot.js?range=6)]

---

## <a name="to-test-the-bot"></a>Pour tester le bot

1. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur, connectez-vous à votre bot et envoyez des messages, comme indiqué ci-dessous.

<!--![test dialog prompt sample](~/media/emulator-v4/test-dialog-prompt.png)-->

## <a name="additional-information"></a>Informations supplémentaires

- L’[exemple d’authentification](https://aka.ms/logout) montre comment gérer la déconnexion ; il utilise un modèle similaire indiqué ici pour gérer les interruptions.

- Envoyez une réponse par défaut au lieu de ne rien faire et de laisser l’utilisateur dans l’interrogation. La réponse par défaut doit indiquer à l’utilisateur les commandes comprises par le bot, de sorte qu’il puisse revenir sur la bonne voie.

- À tout moment dans le tour, la propriété _responded_ du contexte du tour indique si le bot a envoyé un message à l’utilisateur lors de ce tour. Avant la fin du tour, votre bot doit envoyer un message à l’utilisateur, même s’il s’agit d’un simple accusé de réception de son entrée.

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[using-luis]: bot-builder-howto-v4-luis.md
[using-qna]: bot-builder-howto-qna.md

[simple-flow]: bot-builder-dialog-manage-conversation-flow.md
[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-core-sample
[js-sample]: https://aka.ms/js-core-sample
