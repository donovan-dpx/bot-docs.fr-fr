---
title: Ajouter des médias aux messages | Microsoft Docs
description: Découvrez comment ajouter des médias à l’aide du kit SDK Bot Framework.
keywords: médias, messages, images, audio, vidéo, fichiers, MessageFactory, cartes enrichies, messages, cartes adaptatives, carte de héros, actions suggérées
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4caa11221ed930a627b53d7f46107adb226a816d
ms.sourcegitcommit: e815e786413296deea0bd78e5a495df329a9a7cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876172"
---
# <a name="add-media-to-messages"></a>Ajouter des médias aux messages

[!INCLUDE[applies-to](../includes/applies-to.md)]

Les messages échangés entre l’utilisateur et le bot peuvent contenir des pièces jointes multimédia, comme des images, des vidéos, des pistes audio et des fichiers. Le kit SDK Bot Framework prend en charge l’envoi de messages enrichis à l’utilisateur. Pour déterminer le type des messages enrichis pris en charge sur un canal (Facebook, Skype, Slack, etc.), consultez la documentation associée pour en savoir plus sur les limitations.

## <a name="prerequisites"></a>Prérequis
- Connaissances des [concepts de base des bots](bot-builder-basics.md).
- Le code de cet article est basé sur les exemples suivants :

  | Exemple de code | C# | JS |
  | :------ | :----- | :---|
  | Cartes | [Exemple de code C#](https://aka.ms/bot-cards-sample-code) | [Exemple de code JS](https://aka.ms/bot-cards-js-sample-code) |
  | Pièces jointes | [Exemple de code C#](https://aka.ms/bot-attachments-sample-code) | [Exemple de code JS](https://aka.ms/bot-attachments-sample-code-js) |
  | Actions suggérées | [Exemple de code C#](https://aka.ms/SuggestedActionsCSharp) | [Exemple de code JS](https://aka.ms/SuggestedActionsJS) |

## <a name="send-attachments"></a>Envoyer des pièces jointes

Pour envoyer le contenu de l’utilisateur comme une image ou une vidéo, vous pouvez ajouter une pièce jointe ou une liste de pièces jointes à un message.

Pour des exemples de cartes disponibles, consultez [Concevoir une expérience utilisateur](../bot-service-design-user-experience.md).

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

La propriété `Attachments` de l’objet `Activity` contient un tableau d’objets `Attachment` qui représentent les pièces jointes multimédias et cartes enrichies attachées au message. Pour ajouter une pièce jointe multimédia à un message, créez un objet `Attachment` pour l’activité `reply` (qui a été créé à partir de l’activité avec `CreateReply()`) et définissez les propriétés `ContentType`, `ContentUrl` et `Name`.

Le code source affiché ici repose sur l’exemple [Gestion des pièces jointes](https://aka.ms/bot-attachments-sample-code).

Pour créer le message de réponse, définissez le texte, puis configurez les pièces jointes. L’affectation des pièces jointes à la réponse est identique pour chaque type de pièce jointe, cependant les pièces jointes diverses sont configurées et définies différemment, comme illustré dans les extraits de code suivants. Le code ci-dessous configure la réponse pour une pièce jointe insérée :

**Bots/AttachmentsBot.cs**  
[!code-csharp[inline attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=105-106)]

Il nous faut ensuite regarder les types de pièces jointes. Tout d’abord, la pièce jointe insérée :

**Bots/AttachmentsBot.cs**  
[!code-csharp[inline attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=167-178)]

Ensuite, en pièce jointe chargée :

**Bots/AttachmentsBot.cs**  
[!code-csharp[uploaded attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=181-214)]

Enfin, la pièce jointe internet :

**Bots/AttachmentsBot.cs**  
[!code-csharp[online attachment](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=217-226)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le code source affiché ici repose sur l’exemple [Gestion des pièces jointes JS](https://aka.ms/bot-attachments-sample-code-js).

Pour utiliser des pièces jointes, incluez les bibliothèques suivantes dans votre bot :

**bots/attachmentsBot.js**  
[!code-javascript[attachments libraries](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=4)]

Pour créer le message de réponse, définissez le texte, puis configurez les pièces jointes. L’affectation des pièces jointes à la réponse est identique pour chaque type de pièce jointe, cependant les pièces jointes diverses sont configurées et définies différemment, comme illustré dans les extraits de code suivants. Le code ci-dessous configure la réponse pour une pièce jointe insérée :

**bots/attachmentsBot.js**  
[!code-javascript[attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=119,128-129)]

Pour envoyer à l’utilisateur un seul élément de contenu, comme une image ou une vidéo, vous pouvez envoyer des actifs multimédias de différentes façons. Tout d’abord, en pièce jointe insérée :

**bots/attachmentsBot.js**  
[!code-javascript[inline attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=170-179)]

Ensuite, en pièce jointe chargée :

**bots/attachmentsBot.js**  
[!code-javascript[uploaded attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=197-215)]

Enfin, en pièce jointe internet contenue dans une URL :

**bots/attachmentsBot.js**  
[!code-javascript[internet attachments](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=184-191)]

---

Si la pièce jointe est une image, un contenu audio ou une vidéo, le service Connector communique les données de pièce jointe au canal de manière à permettre au [canal](bot-builder-channeldata.md) d’afficher cette pièce jointe dans la conversation. Si la pièce jointe est un fichier, l’URL du fichier s’affichera en tant que lien hypertexte dans la conversation.

## <a name="send-a-hero-card"></a>Envoyer une carte de héros

Outre les simples pièces jointes image ou vidéo, vous pouvez attacher une **carte de héros**, qui vous permet de combiner des images et des boutons dans un seul objet à envoyer à l’utilisateur. Markdown est pris en charge pour la plupart des champs de texte, mais sa prise en charge varie selon le canal.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour composer un message avec un bouton et une carte de héros, vous pouvez joindre `HeroCard` à un message. 

Le code source affiché ici repose sur l’exemple [Gestion des pièces jointes](https://aka.ms/bot-attachments-sample-code).

**Bots/AttachmentsBot.cs**  
[!code-csharp[Hero card](~/../botbuilder-samples/samples/csharp_dotnetcore/15.handling-attachments/Bots/AttachmentsBot.cs?range=39-58)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour composer un message avec un bouton et une carte de héros, vous pouvez joindre `HeroCard` à un message. 

Le code source affiché ici repose sur l’exemple [Gestion des pièces jointes JS](https://aka.ms/bot-attachments-sample-code-js).

**bots/attachmentsBot.js**  
[!code-javascript[hero card](~/../botbuilder-samples/samples/javascript_nodejs/15.handling-attachments/bots/attachmentsBot.js?range=148-164)]

---

## <a name="process-events-within-rich-cards"></a>Traiter des événements dans les cartes enrichies

Pour traiter les événements dans les cartes enrichies, utilisez les objets _card action_ pour spécifier ce qui doit se produire quand l’utilisateur clique sur un bouton ou appuie sur une section de la carte. Chaque action de la carte a un _type_ et une _valeur_.

Pour fonctionner correctement, assignez un type d’action à chaque élément interactif de la carte. Ce tableau liste et décrit les types d’actions disponibles, et ce qui doit se trouver dans la propriété de valeur associée.

| Type | Description | Valeur |
| :---- | :---- | :---- |
| openUrl | Ouvre une URL dans le navigateur intégré. | URL à ouvrir. |
| imBack | Envoie un message au bot et publie une réponse visible dans la conversation. | Texte du message à envoyer. |
| postBack | Envoie un message au bot et ne publie pas une réponse visible dans la conversation. | Texte du message à envoyer. |
| appel | Procède à un appel téléphonique. | Destination d’un appel téléphonique au format suivant : `tel:123123123123`. |
| playAudio | Lit le contenu audio. | URL du contenu audio à lire. |
| playVideo | Lit une vidéo. | URL de la vidéo à lire. |
| showImage | Affiche une image. | URL de l’image à afficher. |
| downloadFile | Télécharge un fichier. | URL du fichier à télécharger. |
| signin | Lance un processus de connexion OAuth. | URL du flux OAuth à lancer. |

## <a name="hero-card-using-various-event-types"></a>Carte de héros utilisant différents types d’événements

Le code suivant montre des exemples d’utilisation de différents événements de carte enrichie.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour obtenir des exemples de toutes les cartes disponibles, consultez l’[ exemple de cartes en C#](https://aka.ms/bot-cards-sample-code).

**Cards.cs**  
[!code-csharp[hero cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=27-40)]

**Cards.cs**  
[!code-csharp[cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=91-100)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour obtenir des exemples de toutes les cartes disponibles, consultez l’[exemple de cartes en JS](https://aka.ms/bot-cards-js-sample-code).

**dialogs/mainDialog.js**  
[!code-javascript[hero cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=213-225)]

**dialogs/mainDialog.js**  
[!code-javascript[sign in cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=266-272)]

---

## <a name="send-an-adaptive-card"></a>Envoyer une carte adaptative
Vous pouvez utiliser aussi bien les cartes adaptatives que MessageFactory pour envoyer des messages enrichis contenant du texte, des images, des vidéos, du contenu audio et des fichiers afin de communiquer avec les utilisateurs. Toutefois, il existe certaines différences entre ces deux méthodes. 

Pour commencer, seuls certains canaux prennent en charge les cartes adaptatives et ne le font parfois que partiellement. Par exemple, si vous envoyez une carte adaptative dans Facebook, les boutons sont inopérants, alors que les textes et images fonctionnent parfaitement. MessageFactory est une simple classe d’assistance au sein du kit SDK Bot Framework qui vise à automatiser la procédure de création et qui est prise en charge par la plupart des canaux. 

Ensuite, les cartes adaptatives remettent les messages au format carte, et le canal détermine la disposition de la carte. Le format des messages remis par MessageFactory dépend du canal et ne correspond pas nécessairement au format carte, sauf si la pièce jointe intègre une carte adaptative. 

Pour connaître les dernières informations sur la prise en charge du canal des cartes adaptatives, consultez <a href="http://adaptivecards.io/designer/">Concepteur de cartes adaptatives</a>.

Pour utiliser des cartes adaptatives, veillez à ajouter le package NuGet `AdaptiveCards`. 


> [!NOTE]
> Vous devez tester cette fonctionnalité avec les canaux que votre bot utilise pour déterminer s’ils prennent en charge les cartes adaptatives.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Pour utiliser des cartes adaptatives, veillez à ajouter le package NuGet `AdaptiveCards`.

Le code source affiché ici repose sur l’exemple [Utilisation des cartes](https://aka.ms/bot-cards-sample-code) :

**Cards.cs**  
[!code-csharp[adaptive cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Cards.cs?range=13-25)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Pour utiliser des cartes adaptatives, veillez à ajouter le package npm `adaptivecards`.

Le code source affiché ici repose sur l’exemple [Utilisation de cartes en JS](https://aka.ms/bot-cards-js-sample-code). 

Ici, la carte adaptative est stockée dans son propre fichier et incluse dans notre bot :

**resources/adaptiveCard.json**  
[!code-json[adaptive cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/resources/adaptiveCard.json)]

Ensuite, elle est créée avec CardFactory :

**dialogs/mainDialog.js**  
[!code-javascript[adaptive cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=177-179)]

---

## <a name="send-a-carousel-of-cards"></a>Envoyer un carrousel de cartes

Les messages peuvent également inclure plusieurs pièces jointes dans une disposition carrousel, qui place les pièces jointes côte à côte et permet à l’utilisateur de faire défiler latéralement.

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le code source affiché ici repose sur l’[Exemple de cartes](https://aka.ms/bot-cards-sample-code).

En premier lieu, créez la réponse et définissez les pièces jointes sous forme de liste.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=61-66)]

Ensuite, ajoutez les pièces jointes. Nous les ajoutons une à une ici, mais n’hésitez pas à arranger la liste pour ajouter les cartes à votre convenance.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=104-113)]

Une fois les pièces jointes ajoutées, vous pouvez envoyer la réponse comme n’importe quelle autre réponse.

**Dialogs/MainDialog.cs**  
[!code-csharp[carousel of cards](~/../botbuilder-samples/samples/csharp_dotnetcore/06.using-cards/Dialogs/MainDialog.cs?range=117-118)]

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le code source affiché ici repose sur l’[Exemple de cartes en JS](https://aka.ms/bot-cards-js-sample-code).

Pour envoyer un carrousel de cartes, envoyez une réponse avec les pièces jointes sous forme de tableau, et le type de disposition défini en tant que `Carousel`:

**dialogs/mainDialog.js**  
[!code-javascript[carousel of cards](~/../botbuilder-samples/samples/javascript_nodejs/06.using-cards/dialogs/mainDialog.js?range=104-116)]

---

<!-- TODO: Add a media card, such as video or audion. Revisit which examples we put here and link to the 06 through 08 samples. -->

## <a name="additional-resources"></a>Ressources supplémentaires

Pour des exemples de cartes disponibles, consultez [Concevoir une expérience utilisateur](../bot-service-design-user-experience.md).

Pour plus d’informations sur le schéma, consultez le [schéma de carte Bot Framework](https://aka.ms/botSpecs-cardSchema) et la [section sur l’activité de message](https://aka.ms/botSpecs-activitySchema#message-activity) dans le schéma d’activité Bot Framework.

### <a name="code-sample-for-processing-adaptive-card-input"></a>Exemple de code pour le traitement d’une entrée de carte adaptative

Cet exemple de code montre une des façons d’utiliser des entrées de carte adaptative dans une classe de dialogue de bot.
Il enrichit l’exemple actuel 06.using-cards en validant l’entrée reçue dans le champ de texte à partir du client qui répond.
Tout d’abord, nous avons ajouté des fonctionnalités d’entrée de texte et de bouton à la carte adaptative existante en ajoutant le code suivant juste avant la parenthèse finale du fichier adaptiveCard.json, disponible dans le dossier de ressources :

```json
  ,
  "actions": [
    {
      "type": "Action.ShowCard",
      "title": "Text",
      "card": {
      "type": "AdaptiveCard",
      "body": [
        {
          "type": "Input.Text",
          "id": "text",
          "isMultiline": true,
          "placeholder": "Enter your comment"
        }
      ],
      "actions": [
        {
          "type": "Action.Submit",
          "title": "OK"
        }
      ]
    }
  }
]

```

Notez que le champ d’entrée porte la mention « text » afin que notre carte adaptative attache les données de texte de commentaire en tant que Value.[text.]

### <a name="ctabcsharp"></a>[C#](#tab/csharp)
Notre validateur utilise Newtonsoft.json pour convertir ceci en JObject, puis créer une chaîne de texte tronquée pour comparaison. Par conséquent, ajoutez :
  ```csharp
  using Newtonsoft.Json.Linq;
  ```
à MainDialog.cs et installez le dernier package NuGet stable de Newtonsoft.Json.
Dans le code du validateur, nous avons ajouté le flux logique dans les commentaires du code. Ce code ChoiceValidator() est placé dans l’exemple 06.using-cards juste après l’accolade fermée publique pour la déclaration de MainDialog :

```csharp
private async Task ChoiceValidator(
  PromptValidatorContext promptContext,
  CancellationToken cancellationToken)
  {
    // Retrieves Adaptive Card comment text as JObject.
    // looks for JObject field "text" and converts that input into a trimmed text string.
    var jobject = promptContext.Context.Activity.Value as JObject;
    var jtoken = jobject?["text"];
    var text = jtoken?.Value().Trim();
    // Logic: 1. if succeeded = true, just return promptContext
    //        2. if false, see if JObject contained Adaptive Card input.
    //               No = (bad input) return promptContext
    //               Yes = update Value field with JObject text string, return "true".
    if (!promptContext.Recognized.Succeeded && text != null)
    {
       var choice = promptContext.Options.Choices.FirstOrDefault(
       c => c.Value.Equals(text, StringComparison.InvariantCultureIgnoreCase));
       if (choice != null)
       {
           promptContext.Recognized.Value = new FoundChoice
            {
               Value = choice.Value,
             };
            return true;
       }
    }
    return promptContext.Recognized.Succeeded;
  }
```

À présent, ci-dessus, dans la déclaration MainDialog, changez :
  ```csharp
  // Define the main dialog and its related components.
  AddDialog(new ChoicePrompt(nameof(ChoicePrompt)));
  ```
to:
  ```csharp
  // Define the main dialog and its related components.
  AddDialog(new ChoicePrompt(nameof(ChoicePrompt), ChoiceValidator));
  ```
Cela invite votre validateur à rechercher une entrée de carte adaptative chaque fois qu’un nouveau ChoicePrompt est créé.

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Ouvrez mainDialog.js et recherchez la méthode d’exécution _async run (turnContext, accessor)_ qui gère l’activité entrante.
Juste après l’appel _dialogSet.add(this) ;_ ajoutez le code suivant :
```JavaScript
  // The following check looks for a non-existant text input
  // plus Adaptive Card input in _activity.value.text
  // If both conditions exist, the Activity Card text 
  // is copied into the text input field.
  if(turnContext._activity.text == null
      && turnContext._activity.value.text != null)
   {
      this.logger.log('replacing null text with Activity Card text input');
      turnContext._activity.text = turnContext._activity.value.text;
   }
```
Si cette vérification détecte une entrée de texte inexistante à partir du client, elle vérifie s’il existe des entrées à partir d’une carte adaptative.
Si une entrée de carte adaptative existe à \_activity.value.text, elle copie ceci dans le champ d’entrée de texte normal.

---

Pour tester votre code, une fois qu’une carte adaptative a été affichée, cliquez sur le bouton « Text », entrez une sélection valide telle que « Hero Card » et cliquez sur le bouton « OK ».

![Tester la carte adaptative](media/adaptive-card-input.png)

1. La première entrée permet de lancer un nouveau dialogue.
2. Cliquez de nouveau sur le bouton « OK » et cette entrée permet de sélectionner une nouvelle carte.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des boutons pour guider l’action de l’utilisateur](./bot-builder-howto-add-suggested-actions.md)
