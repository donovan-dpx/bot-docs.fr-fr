---
title: Implémenter des flux de conversation séquentiels | Microsoft Docs
description: Découvrez comment gérer un flux de conversation simple avec des dialogues dans le kit SDK Bot Framework.
keywords: flux de conversation simple, flux de conversation séquentiel, dialogues, invites, cascades, ensemble de dialogues
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4030a1498be321757b8a25bbd9b8bcde29bb9e5d
ms.sourcegitcommit: eacf1522d648338eebefe2cc5686c1f7866ec6a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70167192"
---
# <a name="implement-sequential-conversation-flow"></a>Implémenter des flux de conversation séquentiels

[!INCLUDE[applies-to](../includes/applies-to.md)]

Pour interagir avec les utilisateurs, un bot collecte généralement des informations en posant des questions. La bibliothèque de dialogues fournit des fonctionnalités intégrées telles que les classes d’*invite* qui vous permettent de poser des questions facilement et de valider la réponse pour vous assurer qu’elle correspond à un type de données spécifique ou répond aux règles de validation personnalisées.

Vous pouvez gérer des flux de conversation simples et complexes avec la bibliothèque de dialogues. Dans une interaction simple, le bot s’exécute via une séquence fixe d’étapes, et la conversation se termine. En règle générale, un dialogue est utile quand le bot doit recueillir des informations auprès de l’utilisateur. Cette rubrique explique comment implémenter un flux de conversation simple en créant des invites et en les appelant à partir d’un dialogue en cascade.

> [!TIP]
> Pour obtenir des exemples montrant comment écrire vos propres invites sans utiliser la bibliothèque de dialogues, consultez l’article [Créer vos propres invites pour collecter des entrées utilisateur](bot-builder-primitive-prompts.md).

## <a name="prerequisites"></a>Prérequis

- Connaissances des [concepts de base des bots][concept-basics], de la [gestion des états][concept-state] et de la [bibliothèque de dialogues][concept-dialogs].
- Une copie de l’exemple d’**invite multitour** en [**C#** ][cs-sample] ou [**JavaScript**][js-sample].

## <a name="about-this-sample"></a>À propos de cet exemple

Dans l’exemple d’invite multitour, nous utilisons un dialogue en cascade, quelques invites et un dialogue de composant pour créer une interaction simple qui pose à l’utilisateur une série de questions. Le code utilise un dialogue pour suivre ces étapes :

| Étapes        | Type d’invite  |
|:-------------|:-------------|
| Demander à l’utilisateur son mode de transport | Invite de choix |
| Demander son nom à l’utilisateur | Invite de texte |
| Demander à l’utilisateur s’il souhaite indiquer son âge | Invite de confirmation |
| S’il a répondu « oui », demander son âge  | Invite de nombre avec validation pour accepter uniquement des âges compris entre 0 et 150. |
| Demander si les informations collectées sont correctes | Invite de réutilisation de la confirmation |

Enfin, si l’utilisateur a répondu « oui », afficher les informations collectées ; sinon, lui indiquer que ses informations ne seront pas conservées.

## <a name="create-the-main-dialog"></a>Créer le dialogue principal

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour utiliser les dialogues, installez le package NuGet **Microsoft.Bot.Builder.Dialogs**.

Le bot interagit avec l’utilisateur par le biais du `UserProfileDialog`. Quand nous créons la classe `DialogBot` du bot, nous définissons le `UserProfileDialog` en tant que dialogue principal. Le bot utilise ensuite une méthode d’assistance `Run` pour accéder au dialogue.

![Dialogue de profil utilisateur](media/user-profile-dialog.png)

**Dialogs\UserProfileDialog.cs**

Nous commençons par créer le `UserProfileDialog` qui dérive de la classe `ComponentDialog`, et qui a 6 étapes.

Dans le constructeur `UserProfileDialog`, créez les étapes en cascade, les invites et le dialogue en cascade, puis ajoutez-les au jeu de dialogues. Les invites doivent se trouver dans le jeu de dialogues où elles sont utilisées.

[!code-csharp[Constructor snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=22-41)]

Ensuite, nous implémentons les étapes qu’utilise le dialogue. Pour utiliser une invite, appelez-la à partir d’une étape dans votre dialogue et récupérez le résultat de l’invite à l’étape suivante avec `stepContext.Result`. Dans les coulisses, les invites constituent une boîte de dialogue en deux étapes. Tout d’abord, l’invite demande une entrée. Ensuite, elle retourne la valeur valide ou redémarre depuis le début avec une nouvelle invite jusqu’à ce qu’elle reçoive une entrée valide.

Vous devez toujours retourner une valeur `DialogTurnResult` non Null à partir d’une étape en cascade. Si vous ne le faites pas, votre dialogue risque de ne pas fonctionner comme prévu. Vous pouvez voir ici l’implémentation pour `NameStepAsync` dans le dialogue en cascade.

[!code-csharp[Name step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=56-61)]

Dans `AgeStepAsync`, nous spécifions une nouvelle invite au cas où la validation de l’entrée de l’utilisateur échouerait, soit parce que son format ne peut pas être analysé par l’invite, soit parce que l’entrée ne remplit pas un critère de validation. Dans ce cas, si aucune nouvelle invite n’a été fournie, l’invite utilise le texte d’invite initial pour redemander une saisie à l’utilisateur.

[!code-csharp[Age step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=74-93&highlight=10)]

**UserProfile.cs**

Le mode de transport, le nom et l’âge de l’utilisateur sont enregistrés dans une instance de la classe `UserProfile`.

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/UserProfile.cs?range=9-16)]

**Dialogs\UserProfileDialog.cs**

Au cours de la dernière étape, nous vérifions le `stepContext.Result` retourné par le dialogue appelé à l’étape précédente en cascade. Si la valeur retournée est true, nous utilisons l’accesseur de profil utilisateur pour obtenir et mettre à jour le profil utilisateur. Pour obtenir le profil utilisateur, nous appelons la méthode `GetAsync`, puis nous définissons les valeurs des propriétés `userProfile.Transport`, `userProfile.Name` et `userProfile.Age`. Enfin, nous récapitulons les informations à l’attention de l’utilisateur avant d’appeler `EndDialogAsync`, qui met fin au dialogue. La fin du dialogue se traduit par son retrait de la pile des dialogues et le retour d’un résultat facultatif à son parent. Le parent est le dialogue ou la méthode ayant démarré le dialogue qui vient de prendre fin.

[!code-csharp[SummaryStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=108-134&highlight=5-10,25-26)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des dialogues, votre projet doit installer le package npm **botbuilder-dialogs**.

Le bot interagit avec l’utilisateur par le biais du `UserProfileDialog`. Quand nous créons le `DialogBot` du bot, nous définissons le `UserProfileDialog` en tant que dialogue principal. Le bot utilise ensuite une méthode d’assistance `run` pour accéder au dialogue.

![Dialogue de profil utilisateur](media/user-profile-dialog-js.png)

**dialogs\userProfileDialog.js**

Nous commençons par créer le `UserProfileDialog` qui dérive de la classe `ComponentDialog`, et qui a 6 étapes.

Dans le constructeur `UserProfileDialog`, créez les étapes en cascade, les invites et le dialogue en cascade, puis ajoutez-les au jeu de dialogues. Les invites doivent se trouver dans le jeu de dialogues où elles sont utilisées.

[!code-javascript[Constructor snippet](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=25-45)]

Ensuite, nous implémentons les étapes qu’utilise le dialogue. Pour utiliser une invite, appelez-la à partir d’une étape dans votre dialogue et récupérez le résultat de l’invite à l’étape suivante à partir du contexte de l’étape, en l’occurrence avec `step.result`. Dans les coulisses, les invites constituent une boîte de dialogue en deux étapes. Tout d’abord, l’invite demande une entrée. Ensuite, elle retourne la valeur valide ou redémarre depuis le début avec une nouvelle invite jusqu’à ce qu’elle reçoive une entrée valide.

Vous devez toujours retourner une valeur `DialogTurnResult` non Null à partir d’une étape en cascade. Si vous ne le faites pas, votre dialogue risque de ne pas fonctionner comme prévu. Vous pouvez voir ici l’implémentation pour `nameStep` dans le dialogue en cascade.

[!code-javascript[name step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=73-76)]

Dans `ageStep`, nous spécifions une nouvelle invite au cas où la validation de l’entrée de l’utilisateur échouerait, soit parce que son format ne peut pas être analysé par l’invite ou parce que l’entrée ne remplit pas un critère de validation, spécifié dans le constructeur ci-dessus. Dans ce cas, si aucune nouvelle invite n’a été fournie, l’invite utilise le texte d’invite initial pour redemander une saisie à l’utilisateur.

[!code-javascript[age step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=88-99&highlight=5)]

**userProfile.js**

Le mode de transport, le nom et l’âge de l’utilisateur sont enregistrés dans une instance de la classe `UserProfile`.

[!code-javascript[user profile](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/userProfile.js?range=4-10)]

**dialogs\userProfileDialog.js**

Au cours de la dernière étape, nous vérifions le `step.result` retourné par le dialogue appelé à l’étape précédente en cascade. Si la valeur retournée est true, nous utilisons l’accesseur de profil utilisateur pour obtenir et mettre à jour le profil utilisateur. Pour obtenir le profil utilisateur, nous appelons la méthode `get`, puis nous définissons les valeurs des propriétés `userProfile.transport`, `userProfile.name` et `userProfile.age`. Enfin, nous récapitulons les informations à l’attention de l’utilisateur avant d’appeler `endDialog`, qui met fin au dialogue. La fin du dialogue se traduit par son retrait de la pile des dialogues et le retour d’un résultat facultatif à son parent. Le parent est le dialogue ou la méthode ayant démarré le dialogue qui vient de prendre fin.

[!code-javascript[summary step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=113-134&highlight=4-8,20-21)]

**Créer la méthode d’extension pour exécuter le dialogue en cascade**

Nous avons défini une méthode d’assistance `run` dans `userProfileDialog` que nous allons utiliser pour créer le contexte du dialogue et y accéder. Ici, `accessor` est l’accesseur de la propriété d’état du dialogue, et `this` est le dialogue composant du profil utilisateur. Comme les dialogues composant définissent un jeu de dialogues interne, nous devons créer un jeu de dialogues externe qui est visible par le code du gestionnaire de messages et utiliser cela pour créer un contexte de dialogue.

Vous créez le contexte du dialogue en appelant la méthode `createContext` et l’utilisez pour interagir avec le jeu de dialogues à partir du gestionnaire de tours du bot. Le contexte du dialogue inclut le contexte du tour actuel, le dialogue parent et l’état du dialogue, ce qui offre une méthode permettant de conserver les informations au sein du dialogue.

Le contexte du dialogue vous permet de démarrer un dialogue avec l’ID de chaîne ou de continuer le dialogue actuel (par exemple, un dialogue en cascade qui comporte plusieurs étapes). Le contexte du dialogue est transmis à la totalité des dialogues et des étapes en cascade du bot.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=53-62)]

---

## <a name="run-the-dialog"></a>Exécuter le dialogue

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

Le gestionnaire `OnMessageActivityAsync` utilise la méthode `RunAsync` pour démarrer ou continuer le dialogue. Dans `OnTurnAsync`, nous utilisons des objets de gestion d’état du bot pour conserver les changements d’état dans le stockage. (La méthode `ActivityHandler.OnTurnAsync` appelle les différentes méthodes de gestionnaire d’activité, telles que `OnMessageActivityAsync`. De cette façon, nous enregistrons l’état une fois que le gestionnaire de messages se termine, mais avant que le tour lui-même ne prenne fin.)

[!code-csharp[overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le gestionnaire `onMessage` utilise la méthode d’assistance pour démarrer ou continuer le dialogue. Dans `onDialog`, nous utilisons des objets de gestion d’état du bot pour conserver les changements d’état dans le stockage. (La méthode `onDialog` est appelée en dernier après l’exécution des autres gestionnaires définis, tels que `onMessage`. De cette façon, nous enregistrons l’état une fois que le gestionnaire de messages se termine, mais avant que le tour lui-même ne prenne fin.)

**bots/dialogBot.js**

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=24-38&highlight=11-13)]

---

## <a name="register-services-for-the-bot"></a>Inscrire les services pour le bot

Ce bot utilise les _services_ suivants.

- Les services de base pour un bot sont : un fournisseur d’informations d’identification, un adaptateur et l’implémentation de bot.
- Services pour gérer l’état : le stockage, l’état utilisateur et l’état de conversation.
- Le dialogue que le bot utilisera.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Startup.cs**

Nous enregistrons des services pour le bot dans `Startup`. Ces services sont disponibles dans d’autres parties du code par le biais de l’injection de dépendances.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Startup.cs?range=17-39)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Nous inscrivons les services pour le bot dans `index.js`.

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/index.js?range=18-46)]

---

> [!NOTE]
> Le stockage mémoire est utilisé uniquement à des fins de test et n’est pas destiné à une utilisation en production.
> Veillez à utiliser un type de stockage permanent pour un bot de production.

## <a name="to-test-the-bot"></a>Pour tester le bot

1. Si ce n’est déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur, connectez-vous à votre bot et envoyez des messages, comme indiqué ci-dessous.

![Exemple d’exécution du dialogue d’invites multitours](../media/emulator-v4/multi-turn-prompt.png)

## <a name="additional-information"></a>Informations supplémentaires

### <a name="about-dialog-and-bot-state"></a>Au sujet du dialogue et de l’état du bot

Dans ce bot, nous avons défini deux accesseurs de propriété d’état :

- Un créé dans l’état de conversation pour la propriété d’état de dialogue. L’état de dialogue suit l’endroit où se trouve l’utilisateur dans les dialogues d’un ensemble de dialogues, et il est mis à jour par le contexte de dialogue, de la même façon que nous appelons les méthodes de début de dialogue ou de suite de dialogue.
- Un créé dans l’état de l’utilisateur de la propriété de profil utilisateur. Le bot utilise cet élément pour suivre les informations qu’il détient sur l’utilisateur, et nous gérons explicitement cet état dans le code de notre dialogue.

Les méthodes _obtenir_ et _définir_ d’un accesseur de propriété d’état obtiennent et définissent la valeur de la propriété dans le cache de l’objet de gestion d’états. Le cache est rempli la première fois que la valeur d’une propriété d’état est demandée dans un tour, mais il doit être conservé explicitement. Afin de conserver les modifications apportées à ces propriétés d’état, nous appelons la méthode _enregistrer les modifications_ de l’objet de gestion d’états correspondant.

Cet exemple met à jour l’état du profil utilisateur dans le dialogue. Cette pratique peut fonctionner pour un bot simple, mais elle ne fonctionnera pas si vous souhaitez réutiliser un dialogue pour des bots.

Il existe différentes options pour séparer les étapes de dialogue de l’état du bot. Par exemple, une fois que votre dialogue a collecté des informations complètes, vous pouvez effectuer les actions suivantes :

- Utiliser la méthode permettant de terminer un dialogue pour fournir les données collectées en tant que valeurs retournées au contexte parent. Il peut s’agir du gestionnaire de tours du bot ou d’un dialogue précédemment actif sur la pile du dialogue. C’est de cette façon que les classes d’invite sont conçues.
- Générer une demande vers un service approprié. Cela peut fonctionner correctement si votre bot agit en tant que serveur frontal vers un service plus volumineux.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter la compréhension du langage naturel à votre bot](bot-builder-howto-v4-luis.md)

<!-- Footnote-style links -->

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[prompting]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-sample]: https://aka.ms/cs-multi-prompts-sample
[js-sample]: https://aka.ms/js-multi-prompts-sample
