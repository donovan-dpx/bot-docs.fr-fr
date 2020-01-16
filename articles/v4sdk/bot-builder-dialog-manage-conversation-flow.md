---
title: Implémenter des flux de conversation séquentiels - Bot Service
description: Découvrez comment gérer un flux de conversation simple avec des dialogues dans le kit SDK Bot Framework.
keywords: flux de conversation simple, flux de conversation séquentiel, dialogues, invites, cascades, ensemble de dialogues
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/05/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 9ee6e9445871445008f6ab406f3250af0226c0dd
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75798508"
---
# <a name="implement-sequential-conversation-flow"></a>Implémenter des flux de conversation séquentiels

[!INCLUDE[applies-to](../includes/applies-to.md)]

Pour interagir avec les utilisateurs, un bot collecte généralement des informations en posant des questions. La bibliothèque de dialogues fournit des fonctionnalités intégrées telles que les classes d’*invite* qui vous permettent de poser des questions facilement et de valider la réponse pour vous assurer qu’elle correspond à un type de données spécifique ou répond aux règles de validation personnalisées.

Vous pouvez gérer des flux de conversation simples et complexes avec la bibliothèque de dialogues. Dans une interaction simple, le bot s’exécute via une séquence fixe d’étapes, et la conversation se termine. En règle générale, un dialogue est utile quand le bot doit recueillir des informations auprès de l’utilisateur. Cette rubrique explique comment implémenter un flux de conversation simple en créant des invites et en les appelant à partir d’un dialogue en cascade.

> [!TIP]
> Pour obtenir des exemples montrant comment écrire vos propres invites sans utiliser la bibliothèque de dialogues, consultez l’article [Créer vos propres invites pour collecter des entrées utilisateur](bot-builder-primitive-prompts.md).

## <a name="prerequisites"></a>Conditions préalables requises

- Connaissances des [concepts de base des bots][concept-basics], de la [gestion des états][concept-state] et de la [bibliothèque de dialogues][concept-dialogs].
- Une copie de l’exemple d’**invite multitour** en [**C#** ][cs-sample], [**JavaScript**][js-sample] ou [**Python**][python-sample].

## <a name="about-this-sample"></a>À propos de cet exemple

Dans l’exemple d’invite multitour, nous utilisons un dialogue en cascade, quelques invites et un dialogue de composant pour créer une interaction simple qui pose à l’utilisateur une série de questions. Le code utilise un dialogue pour suivre ces étapes :

| Étapes        | Type d’invite  |
|:-------------|:-------------|
| Demander à l’utilisateur son mode de transport | Invite de choix |
| Demander son nom à l’utilisateur | Invite de texte |
| Demander à l’utilisateur s’il souhaite indiquer son âge | Invite de confirmation |
| S’il a répondu « oui », demander son âge | Invite de nombre avec validation pour accepter uniquement des âges compris entre 0 et 150 |
| S’il n’utilise pas Microsoft Teams, lui demander un avatar | Invite de pièce jointe avec validation pour autoriser une pièce jointe manquante |
| Demander si les informations collectées sont correctes | Invite de réutilisation de la confirmation |

Enfin, si l’utilisateur a répondu « oui », afficher les informations collectées ; sinon, lui indiquer que ses informations ne seront pas conservées.

## <a name="create-the-main-dialog"></a>Créer le dialogue principal

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour utiliser les dialogues, installez le package NuGet **Microsoft.Bot.Builder.Dialogs**.

Le bot interagit avec l’utilisateur par le biais du `UserProfileDialog`. Quand nous créons la classe `DialogBot` du bot, nous définissons le `UserProfileDialog` en tant que dialogue principal. Le bot utilise ensuite une méthode d’assistance `Run` pour accéder au dialogue.

![Dialogue de profil utilisateur](media/user-profile-dialog.png)

**Dialogs\UserProfileDialog.cs**

Nous commençons par créer le `UserProfileDialog` qui dérive de la classe `ComponentDialog` et comporte 7 étapes.

Dans le constructeur `UserProfileDialog`, créez les étapes en cascade, les invites et le dialogue en cascade, puis ajoutez-les au jeu de dialogues. Les invites doivent se trouver dans le jeu de dialogues où elles sont utilisées.

[!code-csharp[Constructor snippet](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=21-48)]

Ensuite, nous implémentons les étapes qu’utilise le dialogue. Pour utiliser une invite, appelez-la à partir d’une étape dans votre dialogue et récupérez le résultat de l’invite à l’étape suivante avec `stepContext.Result`. Dans les coulisses, les invites constituent une boîte de dialogue en deux étapes. Tout d’abord, l’invite demande une entrée. Ensuite, elle retourne la valeur valide ou redémarre depuis le début avec une nouvelle invite jusqu’à ce qu’elle reçoive une entrée valide.

Vous devez toujours retourner une valeur `DialogTurnResult` non Null à partir d’une étape en cascade. Si vous ne le faites pas, votre dialogue risque de ne pas fonctionner comme prévu. Vous pouvez voir ici l’implémentation pour `NameStepAsync` dans le dialogue en cascade.

[!code-csharp[Name step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=62-67)]

Dans `AgeStepAsync`, nous spécifions une nouvelle invite au cas où la validation de l’entrée de l’utilisateur échouerait, soit parce que son format ne peut pas être analysé par l’invite, soit parce que l’entrée ne remplit pas un critère de validation. Dans ce cas, si aucune nouvelle invite n’a été fournie, l’invite utilise le texte d’invite initial pour redemander une saisie à l’utilisateur.

[!code-csharp[Age step](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=80-99&highlight=10)]

**UserProfile.cs**

Le mode de transport, le nom et l’âge de l’utilisateur sont enregistrés dans une instance de la classe `UserProfile`.

[!code-csharp[UserProfile class](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/UserProfile.cs?range=11-20)]

**Dialogs\UserProfileDialog.cs**

Au cours de la dernière étape, nous vérifions le `stepContext.Result` retourné par le dialogue appelé à l’étape précédente en cascade. Si la valeur retournée est true, nous utilisons l’accesseur de profil utilisateur pour obtenir et mettre à jour le profil utilisateur. Pour obtenir le profil utilisateur, nous appelons la méthode `GetAsync`, puis nous définissons les valeurs des propriétés `userProfile.Transport`, `userProfile.Name`, `userProfile.Age` et `userProfile.Picture`. Enfin, nous récapitulons les informations à l’attention de l’utilisateur avant d’appeler `EndDialogAsync`, qui met fin au dialogue. La fin du dialogue se traduit par son retrait de la pile des dialogues et le retour d’un résultat facultatif à son parent. Le parent est le dialogue ou la méthode ayant démarré le dialogue qui vient de prendre fin.

[!code-csharp[SummaryStepAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=137-179&highlight=5-11,41-42)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des dialogues, votre projet doit installer le package npm **botbuilder-dialogs**.

Le bot interagit avec l’utilisateur par le biais du `UserProfileDialog`. Quand nous créons le `DialogBot` du bot, nous définissons le `UserProfileDialog` en tant que dialogue principal. Le bot utilise ensuite une méthode d’assistance `run` pour accéder au dialogue.

![Dialogue de profil utilisateur](media/user-profile-dialog-js.png)

**dialogs/userProfileDialog.js**

Nous commençons par créer le `UserProfileDialog` qui dérive de la classe `ComponentDialog` et comporte 7 étapes.

Dans le constructeur `UserProfileDialog`, créez les étapes en cascade, les invites et le dialogue en cascade, puis ajoutez-les au jeu de dialogues. Les invites doivent se trouver dans le jeu de dialogues où elles sont utilisées.

[!code-javascript[Constructor snippet](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=29-51)]

Ensuite, nous implémentons les étapes qu’utilise le dialogue. Pour utiliser une invite, appelez-la à partir d’une étape dans votre dialogue et récupérez le résultat de l’invite à l’étape suivante à partir du contexte de l’étape, en l’occurrence avec `step.result`. Dans les coulisses, les invites constituent une boîte de dialogue en deux étapes. Tout d’abord, l’invite demande une entrée. Ensuite, elle retourne la valeur valide ou redémarre depuis le début avec une nouvelle invite jusqu’à ce qu’elle reçoive une entrée valide.

Vous devez toujours retourner une valeur `DialogTurnResult` non Null à partir d’une étape en cascade. Si vous ne le faites pas, votre dialogue risque de ne pas fonctionner comme prévu. Vous pouvez voir ici l’implémentation pour `nameStep` dans le dialogue en cascade.

[!code-javascript[name step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=79-82)]

Dans `ageStep`, nous spécifions une nouvelle invite au cas où la validation de l’entrée de l’utilisateur échouerait, soit parce que son format ne peut pas être analysé par l’invite ou parce que l’entrée ne remplit pas un critère de validation, spécifié dans le constructeur ci-dessus. Dans ce cas, si aucune nouvelle invite n’a été fournie, l’invite utilise le texte d’invite initial pour redemander une saisie à l’utilisateur.

[!code-javascript[age step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=94-105&highlight=5)]

**userProfile.js**

Le mode de transport, le nom et l’âge de l’utilisateur sont enregistrés dans une instance de la classe `UserProfile`.

[!code-javascript[user profile](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/userProfile.js?range=4-11)]

**dialogs/userProfileDialog.js**

Au cours de la dernière étape, nous vérifions le `step.result` retourné par le dialogue appelé à l’étape précédente en cascade. Si la valeur retournée est true, nous utilisons l’accesseur de profil utilisateur pour obtenir et mettre à jour le profil utilisateur. Pour obtenir le profil utilisateur, nous appelons la méthode `get`, puis nous définissons les valeurs des propriétés `userProfile.transport`, `userProfile.name`, `userProfile.age` et `userProfile.picture`. Enfin, nous récapitulons les informations à l’attention de l’utilisateur avant d’appeler `endDialog`, qui met fin au dialogue. La fin du dialogue se traduit par son retrait de la pile des dialogues et le retour d’un résultat facultatif à son parent. Le parent est le dialogue ou la méthode ayant démarré le dialogue qui vient de prendre fin.

[!code-javascript[summary step](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=137-167&highlight=3-9,29-30)]

**Créer la méthode d’extension pour exécuter le dialogue en cascade**

Nous avons défini une méthode d’assistance `run` dans `userProfileDialog` que nous allons utiliser pour créer le contexte du dialogue et y accéder. Ici, `accessor` est l’accesseur de la propriété d’état du dialogue, et `this` est le dialogue composant du profil utilisateur. Comme les dialogues composant définissent un jeu de dialogues interne, nous devons créer un jeu de dialogues externe qui est visible par le code du gestionnaire de messages et utiliser cela pour créer un contexte de dialogue.

Vous créez le contexte du dialogue en appelant la méthode `createContext` et l’utilisez pour interagir avec le jeu de dialogues à partir du gestionnaire de tours du bot. Le contexte du dialogue inclut le contexte du tour actuel, le dialogue parent et l’état du dialogue, ce qui offre une méthode permettant de conserver les informations au sein du dialogue.

Le contexte du dialogue vous permet de démarrer un dialogue avec l’ID de chaîne ou de continuer le dialogue actuel (par exemple, un dialogue en cascade qui comporte plusieurs étapes). Le contexte du dialogue est transmis à la totalité des dialogues et des étapes en cascade du bot.

[!code-javascript[run method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=59-68)]

# <a name="pythontabpython"></a>[Python](#tab/python)

Pour utiliser les dialogues, installez les packages pypi **botbuilder-dialogs** et **botbuilder-ai** en exécutant `pip install botbuilder-dialogs` et `pip install botbuilder-ai` à partir d’un terminal.

Le bot interagit avec l’utilisateur par le biais du `UserProfileDialog`. Quand nous créons la classe `DialogBot` du bot, nous définissons le `UserProfileDialog` en tant que dialogue principal. Le bot utilise ensuite une méthode d’assistance `run_dialog` pour accéder au dialogue.

![Dialogue de profil utilisateur](media/user-profile-dialog-python.png)

**dialogs\user_profile_dialog.py**

Nous commençons par créer le `UserProfileDialog` qui dérive de la classe `ComponentDialog` et comporte 7 étapes.

Dans le constructeur `UserProfileDialog`, créez les étapes en cascade, les invites et le dialogue en cascade, puis ajoutez-les au jeu de dialogues. Les invites doivent se trouver dans le jeu de dialogues où elles sont utilisées.

[!code-python[Constructor snippet](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=25-57)]

Ensuite, nous implémentons les étapes qu’utilise le dialogue. Pour utiliser une invite, appelez-la à partir d’une étape dans votre dialogue et récupérez le résultat de l’invite à l’étape suivante avec `step_context.result`. Dans les coulisses, les invites constituent une boîte de dialogue en deux étapes. Tout d’abord, l’invite demande une entrée. Ensuite, elle retourne la valeur valide ou redémarre depuis le début avec une nouvelle invite jusqu’à ce qu’elle reçoive une entrée valide.

Vous devez toujours retourner une valeur `DialogTurnResult` non Null à partir d’une étape en cascade. Si vous ne le faites pas, votre dialogue risque de ne pas fonctionner comme prévu. Vous pouvez voir ici l’implémentation pour `name_step` dans le dialogue en cascade.

[!code-python[name step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=73-79)]

Dans `age_step`, nous spécifions une nouvelle invite au cas où la validation de l’entrée de l’utilisateur échouerait, soit parce que son format ne peut pas être analysé par l’invite ou parce que l’entrée ne remplit pas un critère de validation, spécifié dans le constructeur ci-dessus. Dans ce cas, si aucune nouvelle invite n’a été fournie, l’invite utilise le texte d’invite initial pour redemander à l’utilisateur d’effectuer une entrée

[!code-python[age step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=100-116)]

**data_models\user_profile.py**

Le mode de transport, le nom et l’âge de l’utilisateur sont enregistrés dans une instance de la classe `UserProfile`.

[!code-python[user profile](~/../botbuilder-python/samples/python/05.multi-turn-prompt/data_models/user_profile.py?range=7-16)]

**dialogs\user_profile_dialog.py**

Au cours de la dernière étape, nous vérifions le `step_context.result` retourné par le dialogue appelé à l’étape précédente en cascade. Si la valeur retournée est true, nous utilisons l’accesseur de profil utilisateur pour obtenir et mettre à jour le profil utilisateur. Pour obtenir le profil utilisateur, nous appelons la méthode `get`, puis nous définissons les valeurs des propriétés `user_profile.transport`, `user_profile.name` et `user_profile.age`. Enfin, nous récapitulons les informations à l’attention de l’utilisateur avant d’appeler `end_dialog`, qui met fin au dialogue. La fin du dialogue se traduit par son retrait de la pile des dialogues et le retour d’un résultat facultatif à son parent. Le parent est le dialogue ou la méthode ayant démarré le dialogue qui vient de prendre fin.

[!code-python[summary step](~/../botbuilder-python/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=166-204)]

**Créer la méthode d’extension pour exécuter le dialogue en cascade**

Nous avons défini une méthode d’assistance `run_dialog()` dans **helpers\dialog_helper.py**, que nous allons utiliser pour créer le contexte du dialogue et y accéder. Ici, `accessor` est l’accesseur de la propriété d’état du dialogue, et `dialog` est le dialogue composant du profil utilisateur. Comme les dialogues composant définissent un jeu de dialogues interne, nous devons créer un jeu de dialogues externe qui est visible par le code du gestionnaire de messages et utiliser cela pour créer un contexte de dialogue.

Vous créez le contexte du dialogue en appelant la méthode `create_context` et l’utilisez pour interagir avec le jeu de dialogues à partir du gestionnaire de tours du bot. Le contexte du dialogue inclut le contexte du tour actuel, le dialogue parent et l’état du dialogue, ce qui offre une méthode permettant de conserver les informations au sein du dialogue.

Le contexte du dialogue vous permet de démarrer un dialogue avec l’ID de chaîne ou de continuer le dialogue actuel (par exemple, un dialogue en cascade qui comporte plusieurs étapes). Le contexte du dialogue est transmis à la totalité des dialogues et des étapes en cascade du bot.

[!code-python[run method](~/../botbuilder-python/samples/python/05.multi-turn-prompt/helpers/dialog_helper.py?range=8-19)]

---

## <a name="run-the-dialog"></a>Exécuter le dialogue

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\DialogBot.cs**

Le gestionnaire `OnMessageActivityAsync` utilise la méthode `RunAsync` pour démarrer ou continuer le dialogue. Dans `OnTurnAsync`, nous utilisons des objets de gestion d’état du bot pour conserver les changements d’état dans le stockage. La méthode `ActivityHandler.OnTurnAsync` appelle les différentes méthodes de gestionnaire d’activités, par exemple `OnMessageActivityAsync`. De cette façon, nous enregistrons l’état après la fin de l’exécution du gestionnaire de messages mais avant la fin du tour lui-même.

[!code-csharp[overrides](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Bots/DialogBot.cs?range=33-48&highlight=5-7)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le gestionnaire `onMessage` utilise la méthode d’assistance pour démarrer ou continuer le dialogue. Dans `onDialog`, nous utilisons des objets de gestion d’état du bot pour conserver les changements d’état dans le stockage. La méthode `onDialog` est appelée en dernier après l’exécution des autres gestionnaires définis, par exemple `onMessage`. De cette façon, nous enregistrons l’état après la fin de l’exécution du gestionnaire de messages mais avant la fin du tour lui-même.

**bots/dialogBot.js**

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/bots/dialogBot.js?range=24-38&highlight=11-13)]

# <a name="pythontabpython"></a>[Python](#tab/python)

Le gestionnaire `on_message_activity` utilise la méthode d’assistance pour démarrer ou continuer le dialogue. Dans `on_turn`, nous utilisons des objets de gestion d’état du bot pour conserver les changements d’état dans le stockage. La méthode `on_message_activity` est appelée en dernier après l’exécution des autres gestionnaires définis, par exemple `on_turn`. De cette façon, nous enregistrons l’état après la fin de l’exécution du gestionnaire de messages mais avant la fin du tour lui-même.

**bots\dialog_bot.py** [!code-python[overrides](~/../botbuilder-python/samples/python/05.multi-turn-prompt/bots/dialog_bot.py?range=39-51&highlight=4-6)]

---

## <a name="register-services-for-the-bot"></a>Inscrire les services pour le bot

Ce bot utilise les _services_ suivants.

- Services de base pour un bot : un fournisseur d’informations d’identification, un adaptateur et l’implémentation du bot.
- Services pour gérer l’état : le stockage, l’état utilisateur et l’état de conversation.
- Le dialogue que le bot va utiliser.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Startup.cs**

Nous enregistrons des services pour le bot dans `Startup`. Ces services sont disponibles pour d’autres parties du code par le biais de l’injection de dépendances.

[!code-csharp[ConfigureServices](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Startup.cs?range=17-39)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

Nous enregistrons des services pour le bot dans `index.js`.

[!code-javascript[overrides](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/index.js?range=19-59)]


# <a name="pythontabpython"></a>[Python](#tab/python)

Nous enregistrons des services pour le bot dans `app.py`.

[!code-python[configure services](~/../botbuilder-python/samples/python/05.multi-turn-prompt/app.py?range=27-75)]

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

### <a name="definition-of-a-prompt-validator-method"></a>Définition d'une méthode de validation d'invite

# <a name="ctabcsharp"></a>[C#](#tab/csharp) 

**UserProfileDialog.cs**

Vous trouverez ci-dessous un exemple de code de validation pour la définition de la méthode `AgePromptValidatorAsync`. `promptContext.Recognized.Value` contient la valeur analysée, qui est ici un entier pour l'invite numérique. `promptContext.Recognized.Succeeded` indique si l'invite a pu analyser l'entrée de l'utilisateur. Le validateur doit renvoyer false pour indiquer que la valeur n'a pas été acceptée et une nouvelle invite doit être adressée à l'utilisateur ; sinon, renvoyez true pour accepter l'entrée et revenir à la fenêtre d'invite. Notez que vous pouvez modifier la valeur figurant dans le validateur selon votre scénario. 

[!code-csharp[prompt validator method](~/../botbuilder-samples/samples/csharp_dotnetcore/05.multi-turn-prompt/Dialogs/UserProfileDialog.cs?range=181-185)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs\userProfileDialog.js**

Vous trouverez ci-dessous un exemple de code de validation pour la définition de la méthode `agePromptValidator`. `promptContext.recognized.value` contient la valeur analysée, qui est ici un entier pour l'invite numérique. `promptContext.recognized.succeeded` indique si l'invite a pu analyser l'entrée de l'utilisateur. Le validateur doit renvoyer false pour indiquer que la valeur n'a pas été acceptée et une nouvelle invite doit être adressée à l'utilisateur ; sinon, renvoyez true pour accepter l'entrée et revenir à la fenêtre d'invite. Notez que vous pouvez modifier la valeur figurant dans le validateur selon votre scénario. 

[!code-javascript[prompt validator method](~/../botbuilder-samples/samples/javascript_nodejs/05.multi-turn-prompt/dialogs/userProfileDialog.js?range=169-172)]

# <a name="pythontabpython"></a>[Python](#tab/python)

**dialogs/user_profile_dialog.py**

Vous trouverez ci-dessous un exemple de code de validation pour la définition de la méthode `age_prompt_validator`. `prompt_context.recognized.value` contient la valeur analysée, qui est ici un entier pour l'invite numérique. `prompt_context.recognized.succeeded` indique si l'invite a pu analyser l'entrée de l'utilisateur. Le validateur doit renvoyer false pour indiquer que la valeur n'a pas été acceptée et une nouvelle invite doit être adressée à l'utilisateur ; sinon, renvoyez true pour accepter l'entrée et revenir à la fenêtre d'invite. Notez que vous pouvez modifier la valeur figurant dans le validateur selon votre scénario. 

[!code-python[prompt validator method](~/../botbuilder-samples/samples/python/05.multi-turn-prompt/dialogs/user_profile_dialog.py?range=207-212)]

---

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
[python-sample]: https://aka.ms/python-multi-prompts-sample

