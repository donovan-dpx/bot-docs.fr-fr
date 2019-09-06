---
title: Créer des flux de conversation avancés à l’aide de branches et de boucles | Microsoft Docs
description: Découvrez comment gérer un flux de conversation complexe avec des dialogues dans le kit SDK Bot Framework.
keywords: flux de conversation complexe, répétition, boucle, menu, dialogues, invites, cascades, jeu de dialogues
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b6bff3cd3d1245556b86a0d20845872d6074c104
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70167394"
---
# <a name="create-advanced-conversation-flow-using-branches-and-loops"></a>Créer des flux de conversation avancés à l’aide de branches et de boucles

[!INCLUDE[applies-to](../includes/applies-to.md)]

Vous pouvez gérer des flux de conversation simples et complexes avec la bibliothèque de dialogues.
Dans cet article, nous allons vous montrer comment gérer des conversations complexes qui forment des branches et des boucles.
Nous vous montrerons également comment passer des arguments entre différentes parties du dialogue.

## <a name="prerequisites"></a>Prérequis

- Connaissances des [concepts de base des bots][concept-basics], de la [gestion de l’état][concept-state], de la [bibliothèque de dialogues][concept-dialogs] et de la façon d’[implémenter les flux de conversation séquentiels][simple-dialog].
- Une copie de l’exemple de dialogue complexe en [**C#** ][cs-sample] ou [**JavaScript**][js-sample].

## <a name="about-this-sample"></a>À propos de cet exemple

Dans cet exemple, un bot inscrit des utilisateurs qui sont ensuite invités à évaluer jusqu’à deux entreprises parmi celles figurant dans une liste.

`DialogAndWelcomeBot` étend `DialogBot`, ce qui définit les gestionnaires des différentes activités, et le gestionnaire de tours du bot. `DialogBot` exécute les dialogues :

- La méthode _run_ est utilisée par `DialogBot` pour lancer le dialogue.
- `MainDialog` est le parent des deux autres dialogues, qui sont appelés à certains moments dans les dialogues. Des informations détaillées sur ces dialogues sont fournies dans cet article.

Les dialogues sont divisés en dialogues composants `MainDialog`, `TopLevelDialog` et `ReviewSelectionDialog` qui, ensemble, procèdent comme suit :

- Ils demandent le nom et l’âge de l’utilisateur, puis _choisissent une branche_ en fonction de l’âge de l’utilisateur.
  - Si l’utilisateur est trop jeune, ils ne lui demandent pas d’évaluer une entreprise.
  - Si l’utilisateur a l’âge requis, ils commencent à collecter les préférences d’évaluation de l’utilisateur.
    - Ils permettent à l’utilisateur de sélectionner une entreprise à évaluer.
    - Si l’utilisateur choisit une entreprise, ils s’_exécutent en boucle_ pour permettre à une deuxième entreprise d’être sélectionnée.
- Enfin, ils remercient l’utilisateur pour sa participation.

Ils utilisent deux dialogues en cascade, et quelques invites pour gérer une conversation complexe.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

![Flux de bot complexes](./media/complex-conversation-flow.png)

Pour utiliser des dialogues, votre projet doit installer le package NuGet **Microsoft.Bot.Builder.Dialogs**.

**Startup.cs**

Nous enregistrons des services pour le bot dans `Startup`. Ces services sont disponibles pour d’autres parties du code par le biais de l’injection de dépendances.

- Services de base pour un bot : un fournisseur d’informations d’identification, un adaptateur et l’implémentation du bot.
- Services pour gérer l’état : le stockage, l’état utilisateur et l’état de conversation.
- Le dialogue que le bot va utiliser.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Startup.cs?range=22-36)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![Flux de bot complexes](./media/complex-conversation-flow-js.png)

Pour utiliser des dialogues, votre projet doit installer le package npm **botbuilder-dialogs**.

**index.js**

Nous créons des services pour le bot que d’autres parties du code demandent.

- Services de base pour un bot : un adaptateur et l’implémentation du bot.
- Services pour gérer l’état : le stockage, l’état utilisateur et l’état de conversation.
- Le dialogue que le bot va utiliser.

[!code-javascript[ConfigureServices](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/index.js?range=25-55)]

---

> [!NOTE]
> Le stockage mémoire est utilisé uniquement à des fins de test et n’est pas destiné à une utilisation en production.
> Veillez à utiliser un type de stockage permanent pour un bot de production.

## <a name="define-a-class-in-which-to-store-the-collected-information"></a>Définissez une classe dans laquelle stocker les informations collectées

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**UserProfile.cs**

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/UserProfile.cs?range=8-16)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**userProfile.js**

[!code-javascript[UserProfile class](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/userProfile.js?range=4-12)]

---

## <a name="create-the-dialogs-to-use"></a>Créer les dialogues à utiliser

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\MainDialog.cs**

Nous avons défini un dialogue composant, `MainDialog`, qui contient deux étapes principales et dirige les dialogues et les invites. L’étape initiale appelle `TopLevelDialog`, ce qui est expliqué ci-dessous.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/MainDialog.cs?range=31-50&highlight=3)]

**Dialogs\TopLevelDialog.cs**

Le dialogue initial de niveau supérieur comporte quatre étapes :

1. Demander le nom de l’utilisateur.
1. Demander l’âge de l’utilisateur.
1. Choisir une branche en fonction de l’âge de l’utilisateur.
1. Enfin, remercier l’utilisateur pour sa participation et retourner les informations collectées.

À la première étape, nous effaçons le profil de l’utilisateur, afin que le dialogue démarre chaque fois avec un profil vide. Dans la mesure où la dernière étape retourne des informations lorsqu’elle se termine, `AcknowledgementStepAsync` conclut en les enregistrant dans l’état de l’utilisateur, puis en retournant ces informations au dialogue principal pour qu’elles soient utilisées dans l’étape finale.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/TopLevelDialog.cs?range=39-96&highlight=3-4,47-49,56-57)]

**Dialogs\ReviewSelectionDialog.cs**

Le dialogue de sélection de l’évaluation est démarré à partir de `StartSelectionStepAsync` dans le dialogue du niveau supérieur, et comporte deux étapes :

1. Demander à l’utilisateur de choisir une entreprise à évaluer ou choisir `done` pour terminer.
1. Répéter ce dialogue ou en sortir selon le cas.

Dans cette conception, le dialogue de niveau supérieur précède toujours le dialogue de sélection de l’évaluation sur la pile, sachant que le dialogue de sélection de l’évaluation peut être considéré comme un enfant du dialogue de niveau supérieur.

[!code-csharp[step implementations](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/ReviewSelectionDialog.cs?range=42-106)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Nous avons défini un dialogue composant, `MainDialog`, qui contient deux étapes principales et dirige les dialogues et les invites. L’étape initiale appelle `TopLevelDialog`, ce qui est expliqué ci-dessous.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/mainDialog.js?range=43-55&highlight=2)]

**dialogs/topLevelDialog.js**

Le dialogue initial de niveau supérieur comporte quatre étapes :

1. Demander le nom de l’utilisateur.
1. Demander l’âge de l’utilisateur.
1. Choisir une branche en fonction de l’âge de l’utilisateur.
1. Enfin, remercier l’utilisateur pour sa participation et retourner les informations collectées.

À la première étape, nous effaçons le profil de l’utilisateur, afin que le dialogue démarre chaque fois avec un profil vide. Dans la mesure où la dernière étape retourne des informations lorsqu’elle se termine, `acknowledgementStep` conclut en les enregistrant dans l’état de l’utilisateur, puis en retournant ces informations au dialogue principal pour qu’elles soient utilisées dans l’étape finale.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/topLevelDialog.js?range=32-76&highlight=2-3,37-39,43-44)]

**dialogs/reviewSelectionDialog.js**

Le dialogue de sélection de l’évaluation est démarré à partir de `startSelectionStep` dans le dialogue du niveau supérieur, et comporte deux étapes :

1. Demander à l’utilisateur de choisir une entreprise à évaluer ou choisir `done` pour terminer.
1. Répéter ce dialogue ou en sortir selon le cas.

Dans cette conception, le dialogue de niveau supérieur précède toujours le dialogue de sélection de l’évaluation sur la pile, sachant que le dialogue de sélection de l’évaluation peut être considéré comme un enfant du dialogue de niveau supérieur.

[!code-javascript[step implementations](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/reviewSelectionDialog.js?range=33-78)]

---

## <a name="implement-the-code-to-manage-the-dialog"></a>Implémenter le code pour gérer le dialogue

Le gestionnaire de tour du bot répète le flux de conversation défini par ces dialogues.
Quand nous recevons un message de l’utilisateur :

1. Continuer le dialogue actif, le cas échéant.
   - Si aucun dialogue n’est actif, nous effaçons le profil utilisateur et démarrons le dialogue de niveau supérieur.
   - Si le dialogue actif est terminé, nous collectons et enregistrons les informations retournées, puis affichons un message d’état.
   - Sinon, le dialogue actif est toujours en cours de traitement, et il n’y a rien d’autre à faire pour le moment.
1. Enregistrer l’état de la conversation pour rendre persistante toute mise à jour de l’état du dialogue.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

<!-- **DialogExtensions.cs**

In this sample, we've defined a `Run` helper method that we will use to create and access the dialog context.
Since component dialog defines an inner dialog set, we have to create an outer dialog set that's visible to the message handler code, and use that to create a dialog context.

- `dialog` is the main component dialog for the bot.
- `turnContext` is the current turn context for the bot.

[!code-csharp[Run method](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/DialogExtensions.cs?range=13-24)]

-->

**Bots\DialogBot.cs**

Le gestionnaire de messages appelle la méthode `RunAsync` pour gérer le dialogue, et nous avons remplacé le gestionnaire de tours pour enregistrer dans l’état de conversation et celui d’utilisateur les modifications pouvant se produire durant le tour. `OnTurnAsync` de base appellera la méthode `OnMessageActivityAsync`, garantissant ainsi que les appels d’enregistrement se produisent à la fin de ce tour.

[!code-csharp[Overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

**Bots\DialogAndWelcome.cs**

`DialogAndWelcomeBot` étend `DialogBot` ci-dessus pour fournir un message de bienvenue quand l’utilisateur rejoint la conversation ; c’est ce qui est créé dans `Startup.cs`.

[!code-csharp[On members added](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Bots/DialogAndWelcome.cs?range=21-38)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/mainDialog.js**

Dans cet exemple, nous avons défini une méthode `run` que nous allons utiliser pour créer le contexte du dialogue et y accéder.
Comme le dialogue composant définit un jeu de dialogues interne, nous devons créer un jeu de dialogues externe qui est visible par le code du gestionnaire de messages, et nous en servir pour créer un contexte de dialogue.

- `turnContext` est le contexte de tour actuel pour le bot.
- `accessor` est un accesseur que nous avons créé pour gérer l’état de dialogue.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/mainDialog.js?range=32-41)]

**bots/dialogBot.js**

Le gestionnaire de messages appelle la méthode d’assistance `run` pour gérer le dialogue, et nous implémentons un gestionnaire de tours pour enregistrer dans l’état de conversation et celui d’utilisateur les modifications pouvant se produire durant le tour. `next` de base appellera la méthode `onDialog`, garantissant ainsi que les appels d’enregistrement se produisent à la fin de ce tour.

[!code-javascript[Overrides](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogBot.js?range=24-41)]

**bots/dialogAndWelcomeBot.js**

`DialogAndWelcomeBot` étend `DialogBot` ci-dessus pour fournir un message de bienvenue quand l’utilisateur rejoint la conversation ; c’est ce qui est créé dans `index.js`.

[!code-javascript[On members added](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/bots/dialogAndWelcomeBot.js?range=10-21)]

---

## <a name="branch-and-loop"></a>Branche et boucle

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\TopLevelDialog.cs**

Voici un exemple de logique de branche à partir d’une étape dans le dialogue de _niveau supérieur_ :

[!code-csharp[branching logic](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/TopLevelDialog.cs?range=68-80)]

**Dialogs\ReviewSelectionDialog.cs**

Voici un exemple de logique de boucle à partir d’une étape dans le dialogue _vérifier la sélection_ :

[!code-csharp[looping logic](~/../botbuilder-samples/samples/csharp_dotnetcore/43.complex-dialog/Dialogs/ReviewSelectionDialog.cs?range=96-105)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/topLevelDialog.js**

Voici un exemple de logique de branche à partir d’une étape dans le dialogue de _niveau supérieur_ :

[!code-javascript[branching logic](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/topLevelDialog.js?range=56-64)]

**dialogs/reviewSelectionDialog.js**

Voici un exemple de logique de boucle à partir d’une étape dans le dialogue _vérifier la sélection_ :

[!code-javascript[looping logic](~/../botbuilder-samples/samples/javascript_nodejs/43.complex-dialog/dialogs/reviewSelectionDialog.js?range=71-77)]

---

## <a name="to-test-the-bot"></a>Pour tester le bot

1. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur, connectez-vous à votre bot et envoyez des messages, comme indiqué ci-dessous.

![exemple de dialogue complexe à des fins de test](~/media/emulator-v4/test-complex-dialog.png)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour obtenir une introduction sur la façon d’implémenter un dialogue, consultez [Implémenter un flux de conversation séquentiel][simple-dialog]. Cet article utilise un dialogue unique en cascade et quelques invites pour créer une interaction simple qui pose à l’utilisateur une série de questions.

La bibliothèque de dialogues inclut la validation de base des invites. Vous pouvez également ajouter une validation personnalisée. Pour plus d’informations, consultez [Collecter les entrées utilisateur avec une invite de dialogue][dialog-prompts].

Pour simplifier votre code de dialogue et le réutiliser dans plusieurs bots, vous pouvez définir chaque partie d’un ensemble de dialogues comme une classe distincte.
Pour plus d’informations, consultez [Réutiliser des dialogues][component-dialogs].

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Réutiliser des boîtes de dialogue](bot-builder-compositcontrol.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[simple-dialog]: bot-builder-dialog-manage-conversation-flow.md
[dialog-prompts]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-complex-dialog-sample
[js-sample]: https://aka.ms/js-complex-dialog-sample
