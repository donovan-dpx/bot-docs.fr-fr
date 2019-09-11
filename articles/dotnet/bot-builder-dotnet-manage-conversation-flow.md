---
title: Gérer un flux de conversation avec des dialogues | Microsoft Docs
description: Découvrez comment modéliser les conversations et gérer un flux de conversation à l’aide des dialogues et du kit SDK Bot Framework pour .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 9e183c375e16a951ed77819ab982790944ac0c13
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298824"
---
# <a name="manage-conversation-flow-with-dialogs"></a>Gérer un flux de conversation avec des dialogues

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-manage-conversation-flow.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-dialog-manage-conversation-flow.md)

[!INCLUDE [Dialog flow example](../includes/snippet-dotnet-manage-conversation-flow-intro.md)]

Cet article vous explique comment modéliser ce flux de conversation en utilisant des [dialogues](bot-builder-dotnet-dialogs.md) et le kit SDK Bot Framework pour .NET. 

## <a name="invoke-the-root-dialog"></a>Appeler le dialogue racine

Le contrôleur de bot appelle le « dialogue racine ». L’exemple ci-après indique comment associer l’appel HTTP POST de base à un contrôleur, puis comment appeler le dialogue racine. 

```cs
public class MessagesController : ApiController
{
    public async Task<HttpResponseMessage> Post([FromBody]Activity activity)
    {
            // Redirect to the root dialog.
            await Conversation.SendAsync(activity, () => new RootDialog()); 
            ...
    }
}
```

## <a name="invoke-the-new-order-dialog"></a>Appeler le dialogue « New Order » (Nouvelle commande)

Ensuite, le dialogue racine appelle le dialogue « New Order » (Nouvelle commande). 

```cs
[Serializable]
public class RootDialog : IDialog<object>
{
    public async Task StartAsync(IDialogContext context)
    {
        // Root dialog initiates and waits for the next message from the user. 
        // When a message arrives, call MessageReceivedAsync.
        context.Wait(this.MessageReceivedAsync); 
    }

    public virtual async Task MessageReceivedAsync(IDialogContext context, IAwaitable<IMessageActivity> result)
    {
        var message = await result; // We've got a message!
        if (message.Text.ToLower().Contains("order"))
        {
            // User said 'order', so invoke the New Order Dialog and wait for it to finish.
            // Then, call ResumeAfterNewOrderDialog.
            await context.Forward(new NewOrderDialog(), this.ResumeAfterNewOrderDialog, message, CancellationToken.None);
        }
        // User typed something else; for simplicity, ignore this input and wait for the next message.
        context.Wait(this.MessageReceivedAsync);
    }

    private async Task ResumeAfterNewOrderDialog(IDialogContext context, IAwaitable<string> result)
    {
        // Store the value that NewOrderDialog returned. 
        // (At this point, new order dialog has finished and returned some value to use within the root dialog.)
        var resultFromNewOrder = await result;

        await context.PostAsync($"New order dialog just told me this: {resultFromNewOrder}");

        // Again, wait for the next message from the user.
        context.Wait(this.MessageReceivedAsync);
    }
}
```

## <a id="dialog-lifecycle"></a> Cycle de vie des dialogues

Lorsqu’un dialogue est appelé, il prend le contrôle du flux de conversation. Chaque nouveau message est traité par ce dialogue jusqu’à ce qu’il se ferme ou soit redirigé vers un autre dialogue. 

En C#, vous pouvez utiliser l’élément `context.Wait()` pour spécifier le rappel à appeler la prochaine fois que l’utilisateur enverra un message. Pour fermer un dialogue et le supprimer de la pile (de façon à renvoyer l’utilisateur au dialogue précédent de la pile), utilisez `context.Done()`. Vous devez terminer chaque méthode de dialogue avec `context.Wait()`, `context.Fail()`, `context.Done()` ou une directive de redirection telle que `context.Forward()` ou `context.Call()`. Une méthode de dialogue qui ne se termine pas par l’un de ces éléments entraîne une erreur (parce que l’infrastructure ne connaît pas l’action à entreprendre la prochaine fois que l’utilisateur enverra un message).

## <a name="passing-state-between-dialogs"></a>Transmission d’état entre les dialogues

Bien que vous puissiez stocker l’état dans Bot State, vous pouvez également transmettre des données entre différents dialogues en surchargeant votre constructeur de classe de dialogue.

```cs
[Serializable]
public class AgeDialog : IDialog<int>
{
    private string name;

    public AgeDialog(string name)
    {
        this.name = name;
    }
}
 ```

Code d’appel de dialogue transmettant la valeur de nom de l’utilisateur.

```cs
private async Task NameDialogResumeAfter(IDialogContext context, IAwaitable<string> result)
{
    try
    {
        this.name = await result;
        context.Call(new AgeDialog(this.name), this.AgeDialogResumeAfter);
    }
    catch (TooManyAttemptsException)
    {
        await context.PostAsync("I'm sorry, I'm having issues understanding you. Let's try again.");
        await this.SendWelcomeMessageAsync(context);
    }
}
```

## <a name="sample-code"></a>Exemple de code 

Pour un exemple complet qui montre comment gérer une conversation en utilisant des dialogues dans le kit SDK Bot Framework pour .NET, consultez l’[exemple Basic Multi Dialog](https://aka.ms/v3cs-MultiDialog-Sample) dans GitHub.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Dialogues](bot-builder-dotnet-dialogs.md)
- [Concevoir et contrôler un flux de conversation](../bot-service-design-conversation-flow.md)
- [Exemple Basic Multi Dialog (GitHub)](https://aka.ms/v3cs-MultiDialog-Sample)
- <a href="/dotnet/api/?view=botbuilder-3.11.0" target="_blank">Informations de référence sur le kit SDK Bot Framework pour .NET</a>
