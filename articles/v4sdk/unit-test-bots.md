---
title: Test unitaire de robots | Microsoft Docs
description: Décrit comment procéder à un test unitaire de robots à l’aide d’infrastructures de test.
keywords: robot, test de robots, infrastructure de test de robot
author: gabog
ms.author: ggilaber
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7e85f7ebe5a39145314f10b3b0378c2c66dc361d
ms.sourcegitcommit: 23a1808e18176f1704f2f6f2763ace872b1388ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68484396"
---
# <a name="how-to-unit-test-bots"></a>Comment procéder à un test unitaire de robots

[!INCLUDE[applies-to](../includes/applies-to.md)]

Dans cette rubrique, nous vous expliquons comment :

- créer des tests unitaires sur des robots
- utiliser l’assertion pour vérifier les activités retournées par une boîte de dialogue par rapport à des valeurs attendues
- utiliser l’assertion pour vérifier les résultats retournés par une boîte de dialogue
- créer différents types de tests pilotés par les données
- créer des objets fictifs pour les différentes dépendances d’une boîte de dialogue (c.-à-d., les modules de reconnaissance LUIS, etc.)

## <a name="prerequisites"></a>Prérequis

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

L'exemple [Tests CoreBot](https://aka.ms/cs-core-test-sample) utilisé dans cette rubrique fait référence au package [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/), [XUnit](https://xunit.net/) et [Moq](https://github.com/moq/moq) pour créer des tests unitaires.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple [Tests CoreBot](https://aka.ms/js-core-test-sample) utilisé dans cette rubrique fait référence au package [botbuilder-testing](https://www.npmjs.com/package/botbuilder-testing), [Mocha](https://mochajs.org/)pour créer des tests unitaires et [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter) pour visualiser des résultats de test dans VS Code.

---

## <a name="testing-dialogs"></a>Tests des boîtes de dialogue

Dans l’exemple CoreBot, les boîtes de dialogue sont testées par unité via la classe `DialogTestClient` qui fournit un mécanisme permettant de les tester de manière isolée en dehors d’un robot et sans avoir à déployer votre code sur un service Web.

À l’aide de cette classe, vous pouvez écrire des tests unitaires qui valident les réponses de boîtes de dialogue sur une base étape par étape. Les tests unitaires utilisant la classe `DialogTestClient` doivent fonctionner avec d’autres boîtes de dialogue créées à l’aide de la bibliothèque de boîtes de dialogue botbuilder.

L’exemple suivant illustre les tests dérivés de `DialogTestClient` :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut);

var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("Seattle");
Assert.Equal("Where are you traveling from?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("New York");
Assert.Equal("When would you like to travel?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("tomorrow");
Assert.Equal("OK, I will book a flight from Seattle to New York for tomorrow, Is this Correct?", reply.Text);

reply = await testClient.SendActivityAsync<IMessageActivity>("yes");
Assert.Equal("Sure thing, wait while I finalize your reservation...", reply.Text);

reply = testClient.GetNextReply<IMessageActivity>();
Assert.Equal("All set, I have booked your flight to Seattle for tomorrow", reply.Text);
```

La classe `DialogTestClient` est définie dans l'espace de noms `Microsoft.Bot.Builder.Testing` et incluse dans le package NuGet [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/).

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut);

let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');

reply = await testClient.sendActivity('Seattle');
assert.strictEqual(reply.text, 'Where are you traveling from?');

reply = await testClient.sendActivity('New York');
assert.strictEqual(reply.text, 'When would you like to travel?');

reply = await testClient.sendActivity('tomorrow');
assert.strictEqual(reply.text, 'OK, I will book a flight from Seattle to New York for tomorrow, Is this Correct?');

reply = await testClient.sendActivity('yes');
assert.strictEqual(reply.text, 'Sure thing, wait while I finalize your reservation...');

reply = testClient.getNextReply();
assert.strictEqual(reply.text, 'All set, I have booked your flight to Seattle for tomorrow');
```

La classe `DialogTestClient` est incluse dans le package npm [botbuilder-testing]().

---

### <a name="dialogtestclient"></a>DialogTestClient

Le premier paramètre de `DialogTestClient` est le canal cible. Cela vous permet de tester différentes logiques de rendu en fonction du canal cible pour votre robot (Teams, Slack, Cortana, etc.). Si vous n’êtes pas certain de votre canal cible, vous pouvez utiliser les ID de canal `Emulator` ou `Test`, mais gardez à l’esprit que certains composants peuvent se comporter différemment selon le canal actuel, par exemple, `ConfirmPrompt` rend les options Oui/Non différemment pour les canaux `Test` et `Emulator`. Vous pouvez également utiliser ce paramètre pour tester la logique de rendu conditionnel de test dans votre boîte de dialogue en fonction de l’ID du canal.

Le deuxième paramètre est une instance de la boîte de dialogue en cours de test (Remarque : **St** signifie « Système sous test », nous utilisons cet acronyme dans les extraits de code dans cet article).

Le constructeur `DialogTestClient` fournit des paramètres supplémentaires qui vous permettent de personnaliser davantage le comportement du client ou de passer des paramètres à la boîte de dialogue en cours de test, si nécessaire. Vous pouvez transmettre des données d’initialisation pour la boîte de dialogue, ajouter un intergiciel personnalisé ou utiliser vos propres TestAdapter et instance `ConversationState`.

### <a name="sending-and-receiving-messages"></a>Envoi et réception des messages

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

La méthode `SendActivityAsync<IActivity>` vous permet d’envoyer un énoncé de SMS ou une `IActivity` à votre boîte de dialogue et retourne le premier message qu’il reçoit. Le paramètre `<T>` est utilisé pour retourner une instance fortement typée de la réponse afin que vous puissiez la déclarer sans avoir à la caster.

```csharp
var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);
```

Dans certains scénarios, votre robot peut envoyer plusieurs messages en réponse à une seule activité. Dans ces cas, `DialogTestClient` met en file d’attente les réponses et vous pouvez utiliser la méthode `GetNextReply<IActivity>` pour dépiler le message suivant de la file de réponse.

```csharp
reply = testClient.GetNextReply<IMessageActivity>();
Assert.Equal("All set, I have booked your flight to Seattle for tomorrow", reply.Text);
```

`GetNextReply<IActivity>` retourne la valeur null s’il n’y a plus de messages dans la file de réponse.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

La méthode `sendActivity` vous permet d’envoyer un énoncé de SMS ou une `Activity` à votre boîte de dialogue et retourne le premier message qu’il reçoit.

```javascript
let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');
```

Dans certains scénarios, votre robot peut envoyer plusieurs messages en réponse à une seule activité. Dans ces cas, `DialogTestClient` met en file d’attente les réponses et vous pouvez utiliser la méthode `getNextReply` pour dépiler le message suivant de la file de réponse.

```javascript
reply = testClient.getNextReply();
assert.strictEqual(reply.text, 'All set, I have booked your flight to Seattle for tomorrow');
```

`getNextReply` retourne la valeur null s’il n’y a plus de messages dans la file de réponse.

---

### <a name="asserting-activities"></a>Assertion des activités

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le code de l’exemple CoreBot déclare uniquement la propriété `Text` des activités retournées. Dans les robots plus complexes, vous souhaiterez peut-être déclarer d'autres propriétés telles que `Speak`, `InputHint`, `ChannelData`, etc.

```csharp
Assert.Equal("Sure thing, wait while I finalize your reservation...", reply.Text);
Assert.Equal("One moment please...", reply.Speak);
Assert.Equal(InputHints.IgnoringInput, reply.InputHint);
```

Pour ce faire, vous pouvez vérifier chaque propriété individuellement comme indiqué ci-dessus, vous pouvez écrire vos propres utilitaires d’assistance pour déclarer des activités ou vous pouvez utiliser d'autres infrastructures comme [FluentAssertions](https://fluentassertions.com/) pour écrire des assertions personnalisées et simplifier votre code de test.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le code de l’exemple CoreBot déclare uniquement la propriété `text` des activités retournées. Dans les robots plus complexes, vous souhaiterez peut-être déclarer d'autres propriétés telles que `speak`, `inputHint`, `channelData`, etc.

```javascript
assert.strictEqual(reply.text, 'Sure thing, wait while I finalize your reservation...');
assert.strictEqual(reply.speak, 'One moment please...');
assert.strictEqual(reply.inputHint, InputHints.IgnoringInput);
```

Pour ce faire, vous pouvez vérifier chaque propriété individuellement comme indiqué ci-dessus, vous pouvez écrire vos propres utilitaires d’assistance pour déclarer des activités ou vous pouvez utiliser d'autres bibliothèques comme [Chai](https://www.chaijs.com/) pour écrire des assertions personnalisées et simplifier votre code de test.

---

### <a name="passing-parameters-to-your-dialogs"></a>Transmission de paramètres vers votre boîte de dialogue

Le constructeur `DialogTestClient` a un `initialDialogOptions` qui peut être utilisé pour passer des paramètres à votre boîte de dialogue. Par exemple, la `MainDialog` de cet exemple, Initialise un objet `BookingDetails` à partir des résultats LUIS avec les entités qu’il résout à partir de l’énoncé de l’utilisateur et passe cet objet dans l’appel pour invoquer `BookingDialog`.

Vous pouvez implémenter cela dans un test comme suit :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
var inputDialogParams = new BookingDetails()
{
    Destination = "Seattle",
    TravelDate = $"{DateTime.UtcNow.AddDays(1):yyyy-MM-dd}"
};

var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut, inputDialogParams);

```

`BookingDialog` reçoit ce paramètre et y accède dans le test de la même façon que lors de son appel à partir de `MainDialog`.

```csharp
private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
{
    var bookingDetails = (BookingDetails)stepContext.Options;
    ...
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const inputDialogParams = {
    destination: 'Seattle',
    travelDate: formatDate(new Date().setDate(now.getDate() + 1))
};

const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut, inputDialogParams);
```

`BookingDialog` reçoit ce paramètre et peut y accèder dans le test de la même façon que s’il avait été appelé à partir de `MainDialog`.

```javascript
async destinationStep(stepContext) {
    const bookingDetails = stepContext.options;
    ...
}
```

---

### <a name="asserting-dialog-turn-results"></a>L’assertion de la boîte de dialogue transformer les résultats

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Certaines boîtes de dialogue telles que `BookingDialog` ou `DateResolverDialog` retournent une valeur à la boîte de dialogue appelante. L’objet `DialogTestClient` expose une propriété `DialogTurnResult` qui peut être utilisée pour analyser et déclarer les résultats retournés par la boîte de dialogue.

Par exemple :

```csharp
var sut = new BookingDialog();
var testClient = new DialogTestClient(Channels.Msteams, sut);

var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
Assert.Equal("Where would you like to travel to?", reply.Text);

...

var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
Assert.Equal("New York", bookingResults?.Origin);
Assert.Equal("Seattle", bookingResults?.Destination);
Assert.Equal("2019-06-21", bookingResults?.TravelDate);
```

La propriété `DialogTurnResult` peut également être utilisée pour inspecter et déclarer les résultats intermédiaires retournés par les étapes d’une cascade.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Certaines boîtes de dialogue telles que `BookingDialog` ou `DateResolverDialog` retournent une valeur à la boîte de dialogue appelante. L'objet `DialogTestClient` expose une propriété `dialogTurnResult` qui peut être utilisée pour analyser et déclarer les résultats retournés par la boîte de dialogue.

Par exemple :

```javascript
const sut = new BookingDialog();
const testClient = new DialogTestClient('msteams', sut);

let reply = await testClient.sendActivity('hi');
assert.strictEqual(reply.text, 'Where would you like to travel to?');

...

const bookingResults = client.dialogTurnResult.result;
assert.strictEqual('New York', bookingResults.destination);
assert.strictEqual('Seattle', bookingResults.origin);
assert.strictEqual('2019-06-21', bookingResults.travelDate);
```

La propriété `dialogTurnResult` peut également être utilisée pour inspecter et déclarer les résultats intermédiaires retournés par les étapes d’une cascade.

---

### <a name="analyzing-test-output"></a>Analyse de la sortie de test

Il est parfois nécessaire de lire une transcription de test unitaire pour analyser l’exécution de test sans avoir à déboguer le test.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le package [Microsoft.Bot.Builder.Testing](https://www.nuget.org/packages/Microsoft.Bot.Builder.Testing/) comprend un `XUnitDialogTestLogger` qui enregistre les messages envoyés et reçus par la boîte de dialogue dans la console.

Pour utiliser cet intergiciel, votre test doit exposer un constructeur qui reçoit un objet `ITestOutputHelper` fourni par le Test Runner XUnit et créer un `XUnitDialogTestLogger` qui sera passé à `DialogTestClient` par le biais du paramètre `middlewares`.

```csharp
public class BookingDialogTests
{
    private readonly IMiddleware[] _middlewares;

    public BookingDialogTests(ITestOutputHelper output)
        : base(output)
    {
        _middlewares = new[] { new XUnitDialogTestLogger(output) };
    }

    [Fact]
    public async Task SomeBookingDialogTest()
    {
        // Arrange
        var sut = new BookingDialog();
        var testClient = new DialogTestClient(Channels.Msteams, sut, middlewares: _middlewares);

        ...
    }
}
```

Voici un exemple de la façon dont `XUnitDialogTestLogger` se connecte à la fenêtre de sortie lorsqu’il est configuré :

![XUnitMiddlewareOutput](media/how-to-unit-test/cs/XUnitMiddlewareOutput.png)

Pour plus d’informations sur l’envoi d’une sortie de test à la console lors de l’utilisation de XUnit, consultez [Capture de la sortie](https://xunit.net/docs/capturing-output.html) dans la documentation XUnit.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le package [botbuilder-testing](https://www.npmjs.com/package/botbuilder-testing) inclut un `DialogTestLogger` qui connecte les messages envoyés et reçus par la boîte de dialogue à la console.

Pour utiliser cet intergiciel, transmettez-le simplement à `DialogTestClient` via le paramètre `middlewares`.

```javascript
const client = new DialogTestClient('msteams', sut, testData.initialData, [new DialogTestLogger()]);
```

Voici un exemple de la façon dont `DialogTestLogger` se connecte à la fenêtre de sortie lorsqu’il est configuré :

![DialogTestLoggerOutput](media/how-to-unit-test/js/DialogTestLoggerOutput.png)

---

Cette sortie sera également consignée sur le serveur de builds pendant que l’intégration continue génère et vous aide à analyser les échecs de build.

## <a name="data-driven-tests"></a>Tests pilotés par les données

Dans la plupart des cas, la logique de la boîte de dialogue ne change pas et les différents chemins d’exécution d’une conversation sont basés sur les énoncés utilisateur. Plutôt que d’écrire un seul test unitaire pour chaque variante de la conversation, il est plus facile d’utiliser des tests pilotés par les données (également appelés tests paramétrables).

Par exemple, l’exemple de test dans la section d’aperçu de ce document montre comment tester un seul flux d’exécution, mais que se passe-t-il si l’utilisateur répond non à la confirmation ? Que se passe-t-il s’il utilise une autre date ? etc.

Les tests pilotés par les données nous permettent de tester toutes ces permutations sans avoir à réécrire les tests.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans l’exemple CoreBot, nous utilisons des tests `Theory` de XUnit pour paramétrer des tests.

### <a name="theory-tests-using-inlinedata"></a>Tests de théorie utilisant InlineData

Le test suivant vérifie qu’une boîte de dialogue est annulée lorsque l’utilisateur dit « annuler ».

```csharp
[Fact]
public async Task ShouldBeAbleToCancel()
{
    var sut = new TestCancelAndHelpDialog();
    var testClient = new DialogTestClient(Channels.Test, sut);

    var reply = await testClient.SendActivityAsync<IMessageActivity>("Hi");
    Assert.Equal("Hi there", reply.Text);
    Assert.Equal(DialogTurnStatus.Waiting, testClient.DialogTurnResult.Status);

    reply = await testClient.SendActivityAsync<IMessageActivity>("cancel");
    Assert.Equal("Cancelling...", reply.Text);
}
```

Pour annuler une boîte de dialogue, les utilisateurs peuvent saisir « quitter », « ignorer » et « arrêter ». Au lieu d’écrire un nouveau cas de test pour chaque mot possible, écrivez une méthode de test `Theory` unique qui accepte des paramètres via une liste de valeurs `InlineData` pour définir les paramètres de chaque cas de test :

```csharp
[Theory]
[InlineData("cancel")]
[InlineData("quit")]
[InlineData("never mind")]
[InlineData("stop it")]
public async Task ShouldBeAbleToCancel(string cancelUtterance)
{
    var sut = new TestCancelAndHelpDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, middlewares: _middlewares);

    var reply = await testClient.SendActivityAsync<IMessageActivity>("Hi");
    Assert.Equal("Hi there", reply.Text);
    Assert.Equal(DialogTurnStatus.Waiting, testClient.DialogTurnResult.Status);

    reply = await testClient.SendActivityAsync<IMessageActivity>(cancelUtterance);
    Assert.Equal("Cancelling...", reply.Text);
}
```

Le nouveau test sera exécuté 4 fois avec les différents paramètres et chaque cas sera affiché en tant qu’élément enfant sous le test `ShouldBeAbleToCancel` dans l’Explorateur de tests Visual Studio. Si l’un d’eux échoue, comme indiqué ci-dessous, vous pouvez cliquer avec le bouton de droite et déboguer le scénario qui a échoué au lieu de réexécuter l’ensemble complet des tests.

![InlineDataTestResults](media/how-to-unit-test/cs/InlineDataTestResults.png)

### <a name="theory-tests-using-memberdata-and-complex-types"></a>Tests de théorie utilisant des types MemberData et complexes

`InlineData` est utile pour les tests pilotés par les données de petite taille qui reçoivent des paramètres de type valeur simples (chaîne, int, etc.).

La `BookingDetails` reçoit un objet `BookingDialog` et retourne un nouvel objet `BookingDetails`. Une version non paramétrable d’un test pour cette boîte de dialogue doit se présenter comme suit :

```csharp
[Fact]
public async Task DialogFlow()
{
    // Initial parameters
    var initialBookingDetails = new BookingDetails
    {
        Origin = "Seattle",
        Destination = null,
        TravelDate = null,
    };

    // Expected booking details
    var expectedBookingDetails = new BookingDetails
    {
        Origin = "Seattle",
        Destination = "New York",
        TravelDate = "2019-06-25",
    };

    var sut = new BookingDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, initialBookingDetails);

    // Act/Assert
    var reply = await testClient.SendActivityAsync<IMessageActivity>("hi");
    ...

    var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
    Assert.Equal(expectedBookingDetails.Origin, bookingResults?.Origin);
    Assert.Equal(expectedBookingDetails.Destination, bookingResults?.Destination);
    Assert.Equal(expectedBookingDetails.TravelDate, bookingResults?.TravelDate);
}
```

Pour paramétrer ce test, nous avons créé une classe `BookingDialogTestCase` qui contient les données de notre cas de test. Il contient l’objet `BookingDetails` initial, l’objet `BookingDetails` attendu et un tableau de chaînes contenant les énoncés envoyés par l’utilisateur et les réponses attendues de la boîte de dialogue pour chaque tour.

```csharp
public class BookingDialogTestCase
{
    public BookingDetails InitialBookingDetails { get; set; }

    public string[,] UtterancesAndReplies { get; set; }

    public BookingDetails ExpectedBookingDetails { get; set; }
}
```

Nous avons également créé une classe `BookingDialogTestsDataGenerator` d’assistance exposant une méthode `IEnumerable<object[]> BookingFlows()` qui retourne une collection des cas de test à utiliser par le test.

Pour afficher chaque cas de test sous la forme d’un élément distinct dans l’Explorateur de tests Visual Studio, le Test Runner XUnit requiert que les types complexes tels que `BookingDialogTestCase` implémentent `IXunitSerializable`, pour simplifier cela, l'infrastructure Bot.Builder.Testing fournit un classe `TestDataObject` qui implémente cette interface et qui peut être utilisée pour inclure dans un wrapper les données de cas de test sans avoir à implémenter `IXunitSerializable`. 

Voici un fragment de `IEnumerable<object[]> BookingFlows()` qui montre comment les deux classes sont utilisées :

```csharp
public static class BookingDialogTestsDataGenerator
{
    public static IEnumerable<object[]> BookingFlows()
    {
        // Create the first test case object
        var testCaseData = new BookingDialogTestCase
        {
            InitialBookingDetails = new BookingDetails(),
            UtterancesAndReplies = new[,]
            {
                { "hi", "Where would you like to travel to?" },
                { "Seattle", "Where are you traveling from?" },
                { "New York", "When would you like to travel?" },
                { "tomorrow", $"Please confirm, I have you traveling to: Seattle from: New York on: {DateTime.Now.AddDays(1):yyyy-MM-dd}. Is this correct? (1) Yes or (2) No" },
                { "yes", null },
            },
            ExpectedBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = $"{DateTime.Now.AddDays(1):yyyy-MM-dd}",
            }, 
        };
        // wrap the test case object into TestDataObject and return it.
        yield return new object[] { new TestDataObject(testCaseData) };

        // Create the second test case object
        testCaseData = new BookingDialogTestCase
        {
            InitialBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = null,
            },
            UtterancesAndReplies = new[,]
            {
                { "hi", "When would you like to travel?" },
                { "tomorrow", $"Please confirm, I have you traveling to: Seattle from: New York on: {DateTime.Now.AddDays(1):yyyy-MM-dd}. Is this correct? (1) Yes or (2) No" },
                { "yes", null },
            },
            ExpectedBookingDetails = new BookingDetails
            {
                Destination = "Seattle",
                Origin = "New York",
                TravelDate = $"{DateTime.Now.AddDays(1):yyyy-MM-dd}",
            },
        };
        // wrap the test case object into TestDataObject and return it.
        yield return new object[] { new TestDataObject(testCaseData) };
    }
}
```

Une fois que nous avons créé un objet pour stocker les données de test et une classe qui expose une collection de cas de test, nous utilisons l’attribut `MemberData` de XUnit au lieu de `InlineData` pour alimenter les données dans le test, le premier paramètre pour `MemberData` est le nom de la fonction statique qui retourne la collection de cas de test et le deuxième paramètre est le type de classe qui expose cette méthode.

```csharp
[Theory]
[MemberData(nameof(BookingDialogTestsDataGenerator.BookingFlows), MemberType = typeof(BookingDialogTestsDataGenerator))]
public async Task DialogFlowUseCases(TestDataObject testData)
{
    // Get the test data instance from TestDataObject
    var bookingTestData = testData.GetObject<BookingDialogTestCase>();
    var sut = new BookingDialog();
    var testClient = new DialogTestClient(Channels.Test, sut, bookingTestData.InitialBookingDetails);

    // Iterate over the utterances and replies array.
    for (var i = 0; i < bookingTestData.UtterancesAndReplies.GetLength(0); i++)
    {
        var reply = await testClient.SendActivityAsync<IMessageActivity>(bookingTestData.UtterancesAndReplies[i, 0]);
        Assert.Equal(bookingTestData.UtterancesAndReplies[i, 1], reply?.Text);
    }

    // Assert the resulting BookingDetails object
    var bookingResults = (BookingDetails)testClient.DialogTurnResult.Result;
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.Origin, bookingResults?.Origin);
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.Destination, bookingResults?.Destination);
    Assert.Equal(bookingTestData.ExpectedBookingDetails?.TravelDate, bookingResults?.TravelDate);
}
```

Voici un exemple des résultats des tests `DialogFlowUseCases` dans l’Explorateur de tests Visual Studio lorsque le test est exécuté :

![BookingDialogTests](media/how-to-unit-test/cs/BookingDialogTestsResults.png)

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

### <a name="simple-data-driven-tests"></a>Tests simples pilotés par les données

Le test suivant vérifie qu’une boîte de dialogue est annulée lorsque l’utilisateur dit « annuler ».

```javascript
describe('ShouldBeAbleToCancel', () => {
    it('Should cancel', async () => {
        const sut = new TestCancelAndHelpDialog();
        const client = new DialogTestClient('test', sut, null, [new DialogTestLogger()]);

        // Execute the test case
        let reply = await client.sendActivity('Hi');
        assert.strictEqual(reply.text, 'Hi there');
        assert.strictEqual(client.dialogTurnResult.status, 'waiting');

        reply = await client.sendActivity('cancel');
        assert.strictEqual(reply.text, 'Cancelling...');
    });
});
```

Supposons que, plus tard, nous devons être en mesure de gérer d’autres énoncés d’annulation, comme « quitter », « ignorer » et « arrêter ». Au lieu d’écrire 3 tests plus répétitifs pour chaque nouvel énoncé, nous pouvons refactoriser le test pour utiliser une liste d’énoncés afin de définir les paramètres de chaque cas de test :

```javascript
describe('ShouldBeAbleToCancel', () => {
    const testCases = ['cancel', 'quit', 'never mind', 'stop it'];

    testCases.map(testData => {
        it(testData, async () => {
            const sut = new TestCancelAndHelpDialog();
            const client = new DialogTestClient('test', sut, null, [new DialogTestLogger()]);

            // Execute the test case
            let reply = await client.sendActivity('Hi');
            assert.strictEqual(reply.text, 'Hi there');
            assert.strictEqual(client.dialogTurnResult.status, 'waiting');

            reply = await client.sendActivity(testData);
            assert.strictEqual(reply.text, 'Cancelling...');
        });
    });
});
```

Le nouveau test sera exécuté 4 fois avec les différents paramètres et chaque cas sera affiché en tant qu’élément enfant sous le test `ShouldBeAbleToCancel` dans [l’Explorateur de tests Mocha](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter). Si l’un d’eux échoue, comme indiqué ci-dessous, vous pouvez exécuter et déboguer le scénario qui a échoué au lieu de réexécuter l’ensemble complet des tests.

![SimpleCancelTestResults](media/how-to-unit-test/js/SimpleCancelTestResults.png)

### <a name="data-driven-tests-with-complex-types"></a>Tests pilotés par les données avec des types complexes

L’utilisation d’une liste d’énoncés simple est utile pour les tests pilotés par les données de petite taille qui reçoivent des paramètres de type valeur simples (chaîne, int, etc.) ou des petits objets.

La `BookingDetails` reçoit un objet `BookingDialog` et retourne un nouvel objet `BookingDetails`. Une version non paramétrable d’un test pour cette boîte de dialogue doit se présenter comme suit :

```javascript
describe('BookingDialog', () => {
    it('Returns expected booking details', async () => {
        // Initial parameters
        const initialBookingDetails = {
            origin: 'Seattle',
            destination: undefined,
            travelDate: undefined
        };

        // Expected booking details
        const expectedBookingDetails = {
            origin: 'Seattle',
            destination: 'New York',
            travelDate: '2019-06-25'
        };

        const sut = new BookingDialog('bookingDialog');
        const client = new DialogTestClient('test', sut, initialBookingDetails, [new DialogTestLogger()]);

        // Execute the test case
        const reply = await client.sendActivity('Hi');
        ...

        // Check dialog results
        const result = client.dialogTurnResult.result;
        assert.strictEqual(result.destination, expectedBookingDetails.destination);
        assert.strictEqual(result.origin, expectedBookingDetails.origin);
        assert.strictEqual(result.travelDate, expectedBookingDetails.travelDate);
    });
});
```

Pour paramétrer ce test, nous avons créé un module `bookingDialogTestCases` qui renvoie les données du cas de test. Chaque élément contient un nom de cas de test, l’objet « BookingDetails » initial, le « BookingDetails » attendu et un tableau de chaînes avec les énoncés envoyés depuis l’utilisateur et les réponses attendues de la boîte de dialogue pour chaque tour.

```javascript
module.exports = [
    // Create the first test case object
    {
        name: 'Full flow',
        initialData: {},
        steps: [
            ['hi', 'To what city would you like to travel?'],
            ['Seattle', 'From what city will you be travelling?'],
            ['New York', 'On what date would you like to travel?'],
            ['tomorrow', `Please confirm, I have you traveling to: Seattle from: New York on: ${ tomorrow }. Is this correct? (1) Yes or (2) No`],
            ['yes', null]
        ],
        expectedResult: {
            destination: 'Seattle',
            origin: 'New York',
            travelDate: tomorrow
        }
    },
    // Create the second test case object
    {
        name: 'Destination and Origin provided',
        initialData: {
            destination: 'Seattle',
            origin: 'New York'
        },
        steps: [
            ['hi', 'On what date would you like to travel?'],
            ['tomorrow', `Please confirm, I have you traveling to: Seattle from: New York on: ${ tomorrow }. Is this correct? (1) Yes or (2) No`],
            ['yes', null]
        ],
        expectedStatus: 'complete',
        expectedResult: {
            destination: 'Seattle',
            origin: 'New York',
            travelDate: tomorrow
        }
    }
];
```

Une fois que nous avons créé la liste qui contient les données de test, nous pouvons refactoriser notre test pour mapper cette liste à des cas de test individuels.

```javascript
describe('DialogFlowUseCases', () => {
    const testCases = require('./testData/bookingDialogTestCases.js');

    testCases.map(testData => {
        it(testData.name, async () => {
            const sut = new BookingDialog('bookingDialog');
            const client = new DialogTestClient('test', sut, testData.initialData, [new DialogTestLogger()]);

            // Execute the test case
            for (let i = 0; i < testData.steps.length; i++) {
                const reply = await client.sendActivity(testData.steps[i][0]);
                assert.strictEqual((reply ? reply.text : null), testData.steps[i][1]);
            }

            // Check dialog results
            const actualResult = client.dialogTurnResult.result;
            assert.strictEqual(actualResult.destination, testData.expectedResult.destination);
            assert.strictEqual(actualResult.origin, testData.expectedResult.origin);
            assert.strictEqual(actualResult.travelDate, testData.expectedResult.travelDate);
        });
    });
});
```

Voici un exemple des résultats de la suite de tests `DialogFlowUseCases` dans l’Explorateur de tests Mocha lorsque le test est exécuté :

![BookingDialogTests](media/how-to-unit-test/js/BookingDialogTestsResults.png)

---

## <a name="using-mocks"></a>Utilisation des objets fictifs

Vous pouvez utiliser des éléments fictifs pour tout ce qui n’est pas en cours de test. À titre de référence, ce niveau peut généralement être assimilé à un test d’unité et d’intégration.

En simulant autant d’éléments que vous le souhaitez, vous pouvez mieux isoler la partie à tester. Les éléments fictifs possibles sont le stockage, l’adaptateur, l’intergiciel, le pipeline d’activités, les canaux et tout ce qui ne fait pas directement partie de votre robot. Il est également possible d’impliquer la suppression temporaire de certains éléments, par exemple un intergiciel non concerné par ce que vous testez dans votre robot, et ce, afin d’isoler chaque partie. Si vous testez votre intergiciel, vous pouvez cependant vouloir simuler votre robot à la place.

Les éléments fictifs peuvent prendre toute une série de formes, du remplacement d’un élément par un autre objet connu à la mise en place d’une fonctionnalité de type hello world. L’élément peut aussi être simplement retiré s’il n’est pas nécessaire ou être forcé de rester inactif.

Les objets fictifs nous permettent de configurer les dépendances d’une boîte de dialogue et de s’assurer qu’elles sont dans un état connu pendant l’exécution du test sans avoir à s’appuyer sur des ressources externes telles que des bases de données, des modèles LUIS ou d’autres objets.

Pour faciliter le test de votre boîte de dialogue et réduire ses dépendances sur des objets externes, vous devrez peut-être injecter les dépendances externes dans le constructeur de boîte de dialogue.

Par exemple, au lieu d'instancier `BookingDialog` dans `MainDialog` :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public MainDialog()
    : base(nameof(MainDialog))
{
    ...
    AddDialog(new BookingDialog());
    ...
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor() {
    super('MainDialog');
    ...
    this.addDialog(new BookingDialog('bookingDialog'));
    ...
}
```

---

Nous passons une instance de `BookingDialog` en tant que paramètre de constructeur :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
public MainDialog(BookingDialog bookingDialog)
    : base(nameof(MainDialog))
{
    ...
    AddDialog(bookingDialog);
    ...
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
constructor(bookingDialog) {
    super('MainDialog');
    ...
    this.addDialog(bookingDialog);
    ...
}
```

---

Cela nous permet de remplacer l'instance `BookingDialog` par un objet fictif et d’écrire des tests unitaires pour la `MainDialog` sans avoir à appeler la classe `BookingDialog` réelle.

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the mock object
var mockDialog = new Mock<BookingDialog>();

// Use the mock object to instantiate MainDialog
var sut = new MainDialog(mockDialog.Object);

var testClient = new DialogTestClient(Channels.Test, sut);
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Create the mock object
const mockDialog = new MockBookingDialog();

// Use the mock object to instantiate MainDialog
const sut = new MainDialog(mockDialog);

const testClient = new DialogTestClient('test', sut);
```

---

### <a name="mocking-dialogs"></a>Simulation de boîtes de dialogue

Comme décrit ci-dessus, `MainDialog` appelle `BookingDialog` pour obtenir l’objet `BookingDetails`. Nous implémentons et configurons une instance fictive de `BookingDialog` comme suit :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
// Create the mock object for BookingDialog.
var mockDialog = new Mock<BookingDialog>();
mockDialog
    .Setup(x => x.BeginDialogAsync(It.IsAny<DialogContext>(), It.IsAny<object>(), It.IsAny<CancellationToken>()))
    .Returns(async (DialogContext dialogContext, object options, CancellationToken cancellationToken) =>
    {
        // Send a generic activity so we can assert that the dialog was invoked.
        await dialogContext.Context.SendActivityAsync($"{mockDialogNameTypeName} mock invoked", cancellationToken: cancellationToken);

        // Create the BookingDetails instance we want the mock object to return.
        var expectedBookingDialogResult = new BookingDetails()
        {
            Destination = "Seattle",
            Origin = "New York",
            TravelDate = $"{DateTime.UtcNow.AddDays(1):yyyy-MM-dd}"
        };

        // Return the BookingDetails we need without executing the dialog logic.
        return await dialogContext.EndDialogAsync(expectedBookingDialogResult, cancellationToken);
    });

// Create the sut (System Under Test) using the mock booking dialog.
var sut = new MainDialog(mockDialog.Object);
```

Dans cet exemple, nous avons utilisé [Moq](https://github.com/moq/moq) pour créer la boîte de dialogue fictive et les méthodes `Setup` et `Returns` pour configurer son comportement.

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
class MockBookingDialog extends BookingDialog {
    constructor() {
        super('bookingDialog');
    }

    async beginDialog(dc, options) {
        // Send a generic activity so we can assert that the dialog was invoked.
        await dc.context.sendActivity(`${ this.id } mock invoked`);

        // Create the BookingDetails instance we want the mock object to return.
        const bookingDetails = {
            origin: 'New York',
            destination: 'Seattle',
            travelDate: '2025-07-08'
        };

        // Return the BookingDetails we need without executing the dialog logic.
        return await dc.endDialog(bookingDetails);
    }
}
...
// Create the sut (System Under Test) using the mock booking dialog.
const sut = new MainDialog(new MockBookingDialog());
...

```

Dans cet exemple, nous implémentons la boîte de dialogue fictive en dérivant de `BookingDialog` et en remplaçant la méthode `beginDialog` pour contourner la logique de la boîte de dialogue sous-jacente.

---

### <a name="mocking-luis-results"></a>Résultats de la simulation LUIS

Dans des scénarios simples, vous pouvez implémenter des résultats LUIS fictifs à l’aide du code comme suit :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
var mockRecognizer = new Mock<IRecognizer>();
mockRecognizer
    .Setup(x => x.RecognizeAsync<FlightBooking>(It.IsAny<ITurnContext>(), It.IsAny<CancellationToken>()))
    .Returns(() =>
    {
        var luisResult = new FlightBooking
        {
            Intents = new Dictionary<FlightBooking.Intent, IntentScore>
            {
                { FlightBooking.Intent.BookFlight, new IntentScore() { Score = 1 } },
            },
            Entities = new FlightBooking._Entities(),
        };
        return Task.FromResult(luisResult);
    });
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript

// Create a mock class for the recognizer that overrides executeLuisQuery.
class MockFlightBookingRecognizer extends FlightBookingRecognizer {
    constructor(mockResult) {
        super();
        this.mockResult = mockResult;
    }

    async executeLuisQuery(context) {
        return this.mockResult;
    }
}
...
// Create a mock result from a string
const mockLuisResult = JSON.parse(`{"intents": {"BookFlight": {"score": 1}}, "entities": {"$instance": {}}}`);
// Use the mock result with the mock recognizer.
const mockRecognizer = new MockFlightBookingRecognizer(mockLuisResult);
...
```

---

Toutefois, les résultats LUIS peuvent être complexes et dans ce cas, il est plus simple de capturer le résultat souhaité dans un fichier jason, de l’ajouter en tant que ressource à votre projet et de le désérialiser en un résultat LUIS. Voici un exemple :

## <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
var mockRecognizer = new Mock<IRecognizer>();
mockRecognizer
    .Setup(x => x.RecognizeAsync<FlightBooking>(It.IsAny<ITurnContext>(), It.IsAny<CancellationToken>()))
    .Returns(() =>
    {
        // Deserialize the LUIS result from embedded json file in the TestData folder.
        var bookingResult = GetEmbeddedTestData($"{GetType().Namespace}.TestData.FlightToMadrid.json");

        // Return the deserialized LUIS result.
        return Task.FromResult(bookingResult);
    });
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
// Create a mock result from a json file
const mockLuisResult = require(`./testData/FlightToMadrid.json`);
// Use the mock result with the mock recognizer.
const mockRecognizer = new MockFlightBookingRecognizer(mockLuisResult);
```

---

## <a name="additional-information"></a>Informations supplémentaires

- [Exemple de test CoreBot (C#)](https://aka.ms/cs-core-test-sample)
- [Exemple de test CoreBot (JavaScript)](https://aka.ms/js-core-test-sample)
- [Tests de robots](https://github.com/microsoft/botframework-sdk/blob/master/specs/testing/testing.md)
