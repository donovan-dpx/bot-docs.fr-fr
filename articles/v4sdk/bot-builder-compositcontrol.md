---
title: Réutiliser des dialogues | Microsoft Docs
description: Apprenez à modulariser votre logique de bot à l’aide des dialogues composants du kit SDK Bot Framework.
keywords: contrôle composite, logique de bot modulaire
author: v-ducvo
ms.author: v-ducvo
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 1f9070b0fd3b4e88eb7d752f5a73c4374bfe8415
ms.sourcegitcommit: ea64a56acfabc6a9c1576ebf9f17ac81e7e2a6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215522"
---
# <a name="reuse-dialogs"></a>Réutiliser des dialogues

[!INCLUDE[applies-to](../includes/applies-to.md)]

Avec les dialogues composants, vous pouvez créer des dialogues indépendants pour gérer des scénarios spécifiques, en divisant un jeu de dialogues en éléments plus petits et donc plus faciles à gérer. Chacun de ces éléments a son propre jeu de dialogues, ce qui permet d’éviter toute collision de nom avec les jeux de dialogues externes.

## <a name="prerequisites"></a>Prérequis

- Connaissances des [concepts de base des bots][concept-basics], de la [bibliothèque de dialogues][concept-dialogs] et de la façon dont [gérer les conversations][simple-flow].
- Une copie de l’exemple d’invite à plusieurs tours, au choix en [**CSharp**][cs-sample] ou en [**JavaScript**][js-sample].

## <a name="about-the-sample"></a>À propos de l’exemple

Dans l’exemple d’invite à plusieurs tours, nous utilisons un dialogue en cascade, quelques invites et un dialogue composant pour créer une interaction simple qui pose à l’utilisateur une série de questions. Le code utilise un dialogue pour suivre ces étapes :

| Étapes        | Type d’invite  |
|:-------------|:-------------|
| Demander à l’utilisateur son mode de transport | Invite de choix |
| Demander son nom à l’utilisateur | Invite de texte |
| Demander à l’utilisateur s’il souhaite indiquer son âge | Invite de confirmation |
| S’il a répondu « oui », demander son âge  | Invite de nombre avec validation pour accepter uniquement des âges compris entre 0 et 150. |
| Demander si les informations collectées sont correctes | Invite de réutilisation de la confirmation |

Enfin, si l’utilisateur a répondu « oui », afficher les informations collectées ; sinon, lui indiquer que ses informations ne seront pas conservées.

## <a name="implement-the-component-dialog"></a>Implémenter le dialogue composant

Dans l’exemple d’invite à plusieurs tours, nous utilisons un _dialogue en cascade_, quelques _invites_ et un _dialogue composant_ pour créer une interaction simple qui pose à l’utilisateur une série de questions.

Un dialogue composant encapsule un ou plusieurs dialogues. Le dialogue composant possède un jeu de dialogues interne défini ; les dialogues et les invites que vous ajoutez à ce jeu de dialogues interne possèdent leurs propres ID, visibles uniquement à partir du dialogue composant.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour utiliser les dialogues, installez le package NuGet **Microsoft.Bot.Builder.Dialogs**.

**Dialogs\UserProfileDialog.cs**

Ici, la classe `UserProfileDialog` dérive de la classe `ComponentDialog`.

[!code-csharp[Class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=13)]

Dans le constructeur, la méthode `AddDialog` ajoute des dialogues et des invites au dialogue composant. Le premier élément que vous ajoutez avec cette méthode est défini comme dialogue initial, mais vous pouvez le changer en définissant explicitement la propriété `InitialDialogId`. Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_.

[!code-csharp[Constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=17-42)]

Il s’agit de l’implémentation de la première étape du dialogue en cascade.

[!code-csharp[First step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=44-54)]

Pour plus d’informations sur l’implémentation des dialogues en cascade, consultez la rubrique sur l’[implémentation des flux de conversation séquentiels](bot-builder-dialog-manage-complex-conversation-flow.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des dialogues, votre projet doit installer le package npm **botbuilder-dialogs**.

**dialogs/userProfileDialog.js**

Ici la classe `UserProfileDialog` étend `ComponentDialog`.

[!code-javascript[Class](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=24)]

Dans le constructeur, la méthode `AddDialog` ajoute des dialogues et des invites au dialogue composant. Le premier élément que vous ajoutez avec cette méthode est défini comme dialogue initial, mais vous pouvez le changer en définissant explicitement la propriété `InitialDialogId`. Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_.

[!code-javascript[Constructor](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=25-47)]

Il s’agit de l’implémentation de la première étape du dialogue en cascade.

[!code-javascript[First step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=66-73)]

Pour plus d’informations sur l’implémentation des dialogues en cascade, consultez la rubrique sur l’[implémentation des flux de conversation séquentiels](bot-builder-dialog-manage-complex-conversation-flow.md).

---

Au moment de l’exécution, le dialogue composant gère sa propre pile de dialogues. Au démarrage du dialogue composant :

- Une instance est créée et ajoutée à la pile de dialogues externe
- Il crée une pile de dialogues interne qu’il ajoute à son état
- Il démarre son dialogue initial et l’ajoute à la pile de dialogues interne.

Sous l’angle du contexte parent, il ressemble au composant dans le dialogue actif. De l’intérieur du composant, il ressemble au dialogue initial dans le dialogue actif.

### <a name="implement-the-rest-of-the-dialog-and-add-it-to-the-bot"></a>Implémenter le reste du dialogue et l’ajouter au bot

Dans le jeu de dialogues externe, celui auquel vous ajoutez le dialogue composant, ce dialogue composant est doté de l’ID avec lequel vous l’avez créé. Dans le jeu externe, le composant ressemble à un dialogue unique, de façon très similaire aux invites.

Pour utiliser un dialogue composant, ajoutez une instance de celui-ci au jeu de dialogues du bot : il s’agit du jeu de dialogues externe.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**DialogExtensions.cs**

Dans l’exemple, cette opération s’effectue avec la méthode d’extension `Run`, comme indiqué ci-dessous.

[!code-csharp[Run method](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/DialogExtensions.cs?range=13-24)]

**Bots\DialogBot.cs**

La méthode `Run` est appelée depuis la méthode `OnMessageActivityAsync` du bot.

[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=42-48)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/userProfileDialog.js**

Dans l’exemple, nous avons ajouté une méthode `run` au dialogue du profil utilisateur.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=55-64)]

**bots/dialogBot.js**

La méthode `run` est appelée depuis la méthode `onMessage` du bot.

[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=30-37)]

---

## <a name="to-test-the-bot"></a>Pour tester le bot

1. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur, connectez-vous à votre bot et envoyez des messages, comme indiqué ci-dessous.

![Exemple d’exécution du dialogue d’invites multitours](../media/emulator-v4/multi-turn-prompt.png)

## <a name="additional-information"></a>Informations supplémentaires

### <a name="how-cancellation-works-for-component-dialogs"></a>Fonctionnement de l’annulation pour les dialogues composants

Si vous appelez _cancel all dialogs_ à partir du contexte du dialogue composant, le dialogue composant annule tous les dialogues sur sa pile interne, puis se termine en retournant le contrôle au dialogue suivant sur la pile externe.

Si vous appelez _cancel all dialogs_ à partir du contexte externe, le composant est annulé, ainsi que le reste des dialogues sur le contexte externe.

Gardez cela à l’esprit lorsque vous gérez des dialogues composants imbriqués dans votre bot.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez améliorer vos bots de manière à ce qu’il réagissent à des entrées supplémentaires, comme « aide » ou « annuler », qui peuvent interrompre le flux normal de la conversation.

> [!div class="nextstepaction"]
> [Gérer les interruptions de l’utilisateur](bot-builder-howto-handle-user-interrupt.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[simple-flow]: bot-builder-dialog-manage-conversation-flow.md
[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-multi-prompts-sample
[js-sample]: https://aka.ms/js-multi-prompts-sample
