---
title: Ajouter des pièces jointes de cartes enrichies aux messages - Bot Service
description: Découvrez comment ajouter des cartes enrichies aux messages à l’aide du service Bot Connector.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: 64df798d363318c2368ea20f57a502c8cd26918c
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790090"
---
# <a name="add-rich-card-attachments-to-messages"></a>Ajouter des pièces jointes de cartes enrichies aux messages
> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-rich-card-attachments.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-send-rich-cards.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-rich-cards.md)

Les robots et les canaux échangent généralement des chaînes de texte, mais certains canaux prennent également en charge l’échange de pièces jointes, ce qui permet à votre robot d’envoyer des messages enrichis aux utilisateurs. Par exemple, votre bot peut envoyer des cartes enrichies et des pièces jointes multimédias (comme des images, des vidéos, des données audio ou des fichiers). Cet article vous expliquer comment ajouter des pièces jointes de cartes enrichies aux messages à l’aide du service Bot Connector.

> [!NOTE]
> Pour en savoir plus sur la façon d’ajouter des pièces jointes aux messages, consultez [Ajouter des pièces jointes aux messages](bot-framework-rest-connector-add-media-attachments.md).

## <a name="types-of-rich-cards"></a>Types de cartes enrichies

Une carte enrichie comporte un titre, une description, un lien et des images. Un message peut contenir plusieurs cartes enrichies, affichées au format liste ou au format carrousel.
Bot Framework prend actuellement en charge huit types de cartes enrichies : 

| Type de carte | Description |
|----|----|
| [AdaptiveCard](/adaptive-cards/get-started/bots) | Carte personnalisable pouvant inclure n’importe quelle combinaison de texte, données vocales, images, boutons et champs d’entrée. Consultez l’article sur la [prise en charge de ces cartes par canal](/adaptive-cards/get-started/bots#channel-status). |
| [AnimationCard][] | Carte pouvant lire des images GIF animées ou de courtes vidéos. |
| [AudioCard][] | Carte pouvant lire un fichier audio. |
| [HeroCard][] | Carte contenant généralement une grande image, un ou plusieurs boutons et du texte. |
| [ThumbnailCard][] | Carte contenant généralement une image miniature, un ou plusieurs boutons et du texte. |
| [ReceiptCard][] | Carte permettant à un robot de fournir un reçu à l’utilisateur. Elle contient généralement la liste des articles à inclure sur le reçu, la taxe et le total, ainsi que du texte. |
| [SignInCard][] | Carte permettant à un bot de demander à un utilisateur de se connecter. Elle contient généralement du texte et un ou plusieurs boutons sur lesquels l’utilisateur peut cliquer pour lancer le processus de connexion. |
| [VideoCard][] | Carte pouvant lire des vidéos. |

[!INCLUDE [Channel Inspector intro](~/includes/snippet-channel-inspector.md)]

## <a name="process-events-within-rich-cards"></a>Traiter des événements dans les cartes enrichies

Pour traiter les événements dans les cartes enrichies, utilisez les objets [CardAction][] afin de spécifier ce qui doit se produire quand l’utilisateur clique sur un bouton ou appuie sur une section de la carte. Chaque objet `CardAction` contient les propriétés suivantes :

| Propriété | Type | Description | 
|----|----|----|
| channelData | string | données spécifiques au canal associées à cette action |
| displayText | string | texte à afficher dans le flux de conversation si l’utilisateur clique sur le bouton | 
| text | string | texte de l’action | 
| type | string | type d’action (une des valeurs indiquées dans le tableau ci-dessous) |
| title | string | titre du bouton |
| image | string | URL d’image du bouton |
| value | string | valeur nécessaire pour effectuer le type d’action spécifié |

> [!NOTE]
> Les boutons des cartes adaptatives ne sont pas créés avec les objets `CardAction`, mais à l’aide du schéma défini par les cartes adaptatives. Pour obtenir un exemple illustrant comment ajouter des boutons à une carte adaptative, consultez la section [Ajouter une carte adaptative à un message](#add-an-adaptive-card-to-a-message).

Ce tableau répertorie les valeurs valides pour la propriété `type` d’un objet `CardAction` et décrit le contenu attendu de la propriété `value` pour chaque type :

| type | value | 
|----|----|
| openUrl | URL à ouvrir dans le navigateur intégré |
| imBack | Texte du message à envoyer au bot (de la part de l’utilisateur qui a cliqué sur le bouton ou appuyé sur la carte). Ce message (de l’utilisateur au bot) sera visible par tous les participants à la conversation par le biais de l’application cliente qui héberge la conversation. |
| postBack | Texte du message à envoyer au bot (de la part de l’utilisateur qui a cliqué sur le bouton ou appuyé sur la carte). Certaines applications clientes peuvent afficher ce texte dans le flux de messages, où il sera visible par tous les participants à la conversation. |
| appel | Destination d’un appel téléphonique au format suivant : **tel:123123123123** |
| playAudio | URL du fichier audio à lire. |
| playVideo | URL du fichier vidéo à lire. |
| showImage | URL de l’image à afficher. |
| downloadFile | URL du fichier à télécharger. |
| signin | URL du flux OAuth à démarrer. |

## <a name="add-a-hero-card-to-a-message"></a>Ajouter une carte de bannière à un message

Pour ajouter une pièce jointe de carte enrichie à un message, commencez par créer un objet qui correspond au [type de carte](#types-of-rich-cards) que vous souhaitez ajouter au message. Créez ensuite un objet [Attachment][], et définissez sa propriété `contentType` sur le type multimédia de la carte et sa propriété `content` sur l’objet que vous avez créé pour représenter la carte. Spécifiez votre objet `Attachment` dans le tableau `attachments` du message.

> [!TIP]
> Les messages qui contiennent des pièces jointes de cartes enrichies ne spécifient généralement pas d’éléments `text`.

Certains canaux vous permettent d’ajouter plusieurs cartes enrichies au tableau `attachments` dans un message. Cette fonctionnalité peut être utile dans les cas où vous souhaitez offrir plusieurs options aux utilisateurs. Par exemple, si votre bot permet aux utilisateurs de réserver des chambres d’hôtel, il peut leur présenter une liste de cartes enrichies affichant les types de chambres disponibles. Chacune de ces cartes peut contenir une photo, accompagnée de la liste des équipements correspondant au type de la chambre ; l’utilisateur peut alors sélectionner un type de chambre en appuyant sur une carte ou en cliquant sur un bouton.

> [!TIP]
> Pour afficher plusieurs cartes enrichies au format liste, définissez la propriété `attachmentLayout` de l’objet [Activité][] sur « list ». Pour afficher plusieurs cartes enrichies au format carrousel, définissez la propriété `attachmentLayout` de l’objet `Activity` sur « carousel ». Si le canal ne prend pas en charge le format carrousel, les cartes riches s’afficheront au format liste, bien que la propriété `attachmentLayout` indique « carrousel ».

L’exemple ci-après présente une requête qui envoie un message ne contenant qu’une seule pièce jointe de carte de bannière. Dans cet exemple de demande, `https://smba.trafficmanager.net/apis` représente l’URI de base. L’URI de base pour les demandes émises par votre robot peut être différente. Pour plus d’informations sur la définition de l’URI de base, voir [Informations de référence sur l’API](bot-framework-rest-connector-api-reference.md#base-uri).

```http
POST https://smba.trafficmanager.net/apis/v3/conversations/abcd1234/activities/5d5cdc723 
Authorization: Bearer ACCESS_TOKEN
Content-Type: application/json
```

```json
{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
    },
    "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.hero",
            "content": {
                "title": "title goes here",
                "subtitle": "subtitle goes here",
                "text": "descriptive text goes here",
                "images": [
                    {
                        "url": "https://aka.ms/DuckOnARock",
                        "alt": "picture of a duck",
                        "tap": {
                            "type": "playAudio",
                            "value": "url to an audio track of a duck call goes here"
                        }
                    }
                ],
                "buttons": [
                    {
                        "type": "playAudio",
                        "title": "Duck Call",
                        "value": "url to an audio track of a duck call goes here"
                    },
                    {
                        "type": "openUrl",
                        "title": "Watch Video",
                        "image": "https://aka.ms/DuckOnARock",
                        "value": "url goes here of the duck in flight"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

## <a name="add-an-adaptive-card-to-a-message"></a>Ajouter une carte adaptative à un message

La carte adaptive peut inclure n’importe quelle combinaison de texte, données vocales, images, boutons et champs d’entrée. Les cartes adaptatives sont créées à l’aide du format JSON spécifié sur le site [Adaptive Cards](http://adaptivecards.io) (Cartes adaptatives), ce qui vous donne un contrôle total sur le contenu et le format de la carte. 

Tirez profit des informations fournies par le site [Adaptive Cards](http://adaptivecards.io) (Cartes adaptatives) pour comprendre le schéma de carte adaptative, explorer les éléments de carte adaptative et découvrir des exemples JSON qui permettent de créer des cartes présentant différents types de compositions et niveaux de complexité. En outre, vous pouvez utiliser le visualiseur interactif pour concevoir des charges utiles de carte adaptative et afficher un aperçu de la sortie des cartes. L’exemple suivant correspond à une carte adaptative unique pour une affectation de travail.

```json
{
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "type": "AdaptiveCard",
  "version": "1.0",
  "body": [
    {
      "type": "Container",
      "items": [
        {
          "type": "TextBlock",
          "text": "Publish Adaptive Card schema",
          "weight": "bolder",
          "size": "medium"
        },
        {
          "type": "ColumnSet",
          "columns": [
            {
              "type": "Column",
              "width": "auto",
              "items": [
                {
                  "type": "Image",
                  "url": "https://pbs.twimg.com/profile_images/3647943215/d7f12830b3c17a5a9e4afcc370e3a37e_400x400.jpeg",
                  "size": "small",
                  "style": "person"
                }
              ]
            },
            {
              "type": "Column",
              "width": "stretch",
              "items": [
                {
                  "type": "TextBlock",
                  "text": "Matt Hidinger",
                  "weight": "bolder",
                  "wrap": true
                },
                {
                  "type": "TextBlock",
                  "spacing": "none",
                  "text": "Created {{DATE(2017-02-14T06:08:39Z, SHORT)}}",
                  "isSubtle": true,
                  "wrap": true
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "Container",
      "items": [
        {
          "type": "TextBlock",
          "text": "Now that we have defined the main rules and features of the format, we need to produce a schema and publish it to GitHub. The schema will be the starting point of our reference documentation.",
          "wrap": true
        },
        {
          "type": "FactSet",
          "facts": [
            {
              "title": "Board:",
              "value": "Adaptive Card"
            },
            {
              "title": "List:",
              "value": "Backlog"
            },
            {
              "title": "Assigned to:",
              "value": "Matt Hidinger"
            },
            {
              "title": "Due date:",
              "value": "Not set"
            }
          ]
        }
      ]
    }
  ],
  "actions": [
    {
      "type": "Action.ShowCard",
      "title": "Comment",
      "card": {
        "type": "AdaptiveCard",
        "body": [
          {
            "type": "Input.Text",
            "id": "comment",
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
    },
    {
      "type": "Action.OpenUrl",
      "title": "View",
      "url": "https://adaptivecards.io"
    }
  ]
}

```

La carte obtenue contient un titre, des informations sur la personne qui a créé la carte (nom et avatar), la date de création de la carte, une description de l'affectation de travail, ainsi que des informations relatives à cette affectation. Il existe également des boutons sur lesquels vous pouvez cliquer pour commenter l’affectation de travail ou l'afficher :

![Carte adaptative de rappel du Calendrier](../media/adaptive-card-reminder.png)


## <a name="additional-resources"></a>Ressources supplémentaires

- [Créer des messages](bot-framework-rest-connector-create-messages.md)
- [Envoyer et recevoir des messages](bot-framework-rest-connector-send-and-receive-messages.md)
- [Ajouter des pièces jointes multimédia aux messages](bot-framework-rest-connector-add-media-attachments.md)
- [Schéma d’activité Bot Framework](https://aka.ms/botSpecs-activitySchema)
- [Channel Inspector][ChannelInspector]

[ChannelInspector]: ../bot-service-channels-reference.md
[Activité]: bot-framework-rest-connector-api-reference.md#activity-object
[Attachment]: bot-framework-rest-connector-api-reference.md#attachment-object
[CardAction]: bot-framework-rest-connector-api-reference.md#cardaction-object
[AnimationCard]: bot-framework-rest-connector-api-reference.md#animationcard-object
[AudioCard]: bot-framework-rest-connector-api-reference.md#audiocard-object
[HeroCard]: bot-framework-rest-connector-api-reference.md#herocard-object
[ThumbnailCard]: bot-framework-rest-connector-api-reference.md#thumbnailcard-object
[ReceiptCard]: bot-framework-rest-connector-api-reference.md#receiptcard-object
[SignInCard]: bot-framework-rest-connector-api-reference.md#signincard-object
[VideoCard]: bot-framework-rest-connector-api-reference.md#videocard-object

