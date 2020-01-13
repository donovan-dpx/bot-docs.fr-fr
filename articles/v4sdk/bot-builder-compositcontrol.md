---
title: Réutiliser des dialogues | Microsoft Docs
description: Apprenez à modulariser votre logique de bot à l’aide des dialogues composants du kit SDK Bot Framework.
keywords: contrôle composite, logique de bot modulaire
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: c273b0c157abd40dd139739411b19656565fa7c7
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491527"
---
# <a name="reuse-dialogs"></a>Réutiliser des dialogues

[!INCLUDE[applies-to](../includes/applies-to.md)]

Avec les dialogues composants, vous pouvez créer des dialogues indépendants pour gérer des scénarios spécifiques, en divisant un jeu de dialogues en éléments plus petits et donc plus faciles à gérer. Chacun de ces éléments a son propre jeu de dialogues, ce qui permet d’éviter toute collision de nom avec les jeux de dialogues externes.

## <a name="prerequisites"></a>Conditions préalables requises

- Connaissances des [concepts de base des bots][concept-basics], de la [bibliothèque de dialogues][concept-dialogs] et de la façon de [gérer les conversations][simple-flow].
- Une copie de l’exemple d’invite multitour en [**C#** ][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-the-sample"></a>À propos de l’exemple

Dans l’exemple d’invite multitour, nous utilisons un dialogue en cascade, quelques invites et un dialogue de composant pour créer une interaction simple qui pose à l’utilisateur une série de questions. Le code utilise un dialogue pour suivre ces étapes :

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

[!code-csharp[Class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=17)]

Dans le constructeur, la méthode `AddDialog` ajoute des dialogues et des invites au dialogue composant. Le premier élément que vous ajoutez avec cette méthode est défini comme dialogue initial, mais vous pouvez le changer en définissant explicitement la propriété `InitialDialogId`. Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_.

[!code-csharp[Constructor](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=21-48)]

Il s’agit de l’implémentation de la première étape du dialogue en cascade.

[!code-csharp[First step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=50-60)]

Pour plus d’informations sur l’implémentation des dialogues en cascade, consultez la rubrique sur l’[implémentation des flux de conversation séquentiels](bot-builder-dialog-manage-complex-conversation-flow.md).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des dialogues, votre projet doit installer le package npm **botbuilder-dialogs**.

**dialogs/userProfileDialog.js**

Ici la classe `UserProfileDialog` étend `ComponentDialog`.

[!code-javascript[Class](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=24)]

Dans le constructeur, la méthode `AddDialog` ajoute des dialogues et des invites au dialogue composant. Le premier élément que vous ajoutez avec cette méthode est défini comme dialogue initial, mais vous pouvez le changer en définissant explicitement la propriété `InitialDialogId`. Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_.

[!code-javascript[Constructor](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=25-45)]

Il s’agit de l’implémentation de la première étape du dialogue en cascade.

[!code-javascript[First step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=64-71)]

Pour plus d’informations sur l’implémentation des dialogues en cascade, consultez la rubrique sur l’[implémentation des flux de conversation séquentiels](bot-builder-dialog-manage-complex-conversation-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

Pour utiliser les dialogues, installez les packages pypi **botbuilder-dialogs** et **botbuilder-ai** en exécutant `pip install botbuilder-dialogs` et `pip install botbuilder-ai` à partir d’un terminal.

**dialogs/user_profile_dialog.py**

Ici la classe `UserProfileDialog` étend `ComponentDialog`.

[!code-python[Class](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=25)]

Dans le constructeur, la méthode `add_dialog` ajoute des dialogues et des invites au dialogue composant. Le premier élément que vous ajoutez avec cette méthode est défini comme dialogue initial, mais vous pouvez le changer en définissant explicitement la propriété `initial_dialog_id`. Quand vous démarrez un dialogue composant, celui-ci démarre son _dialogue initial_.

[!code-python[Constructor](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=25-57)]

Il s’agit de l’implémentation de la première étape du dialogue en cascade.

[!code-python[First step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=59-71)]

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

**Bots\DialogBot.cs**

Dans l’exemple, c’est fait en utilisant la méthode `RunAsync` qui est appelée à partir de la méthode `OnMessageActivityAsync` du bot.

[!code-csharp[OnMessageActivityAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=42-48)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/userProfileDialog.js**

Dans l’exemple, nous avons ajouté une méthode `run` au dialogue du profil utilisateur.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=53-62)]

**bots/dialogBot.js**

La méthode `run` est appelée depuis la méthode `onMessage` du bot.

[!code-javascript[onMessage](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=24-31&highlight=5)]

# <a name="pythontabpython"></a>[Python](#tab/python)

**helpers/dialog_helper.py**

Dans l’exemple, nous avons ajouté une méthode `run_dialog` au dialogue du profil utilisateur.

[!code-python[First step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/helpers/dialog_helper.py?range=8-19)]

Il s’agit de la méthode `run_dialog`, qui est appelée à partir de la méthode `on_message_activity` du bot.

**bots/dialog_bot.py** [!code-python[First step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/bots/dialog_bot.py?range=46-51)]

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
[python-sample]: https://aka.ms/python-multi-prompts-sample
