---
title: Entités et types d’activités | Microsoft Docs
description: Entités et les types d’activités.
keywords: mentionner des entités, types d’activités, consommer des entités
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 03/01/2018
ms.openlocfilehash: 9fa9a23f4d14667aeb97d304498b415f2c8041d1
ms.sourcegitcommit: f84b56beecd41debe6baf056e98332f20b646bda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2019
ms.locfileid: "65033060"
---
# <a name="entities-and-activity-types"></a>Entités et types d’activités

Les entités font partie d’une activité et fournissent des informations supplémentaires sur l’activité ou la conversation.

[!include[Entity boilerplate](includes/snippet-entity-boilerplate.md)]

## <a name="entities"></a>Entités

La propriété *entités* d’un message est un tableau d’objets <a href="http://schema.org/" target="_blank">schema.org</a> de durée indéterminée, permettant l’échange de métadonnées contextuelles communes entre le canal et le bot.

### <a name="mention-entities"></a>Mentionner des entités

De nombreux canaux permettent à un utilisateur ou un bot de « mentionner » une personne dans le cadre d’une conversation.
Pour mentionner un utilisateur dans un message, remplissez la propriété Entities du message avec un objet *mention*.
L’objet mention contient ces propriétés :

| Propriété | Description |
|----|----|
| Type | type de l’entité (« mention ») |
| Mentionné | objet de compte de canal indiquant l’utilisateur qui a été mentionné | 
| Texte | texte de la propriété *activity.text* représentant la mention (peut être null ou vide) |

Cet exemple de code montre comment ajouter une entité mention à la collection d’entités.

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set Mention](includes/code/dotnet-create-messages.cs#setMention)]

> [!TIP]
> Lorsque vous tentez de déterminer l’intention de l’utilisateur, le bot peut ignorer la partie du message dans laquelle cette intention est mentionnée. Appelez la méthode `GetMentions` et évaluez les objets `Mention` retournés dans la réponse.

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const mention = {
    type: "Mention",
    text: "@johndoe",
    mentioned: {
        name: "John Doe",
        id: "UV341235"
    }
}

entity = [mention];
```

---

### <a name="place-objects"></a>Placer des objets

Les <a href="https://schema.org/Place" target="_blank">informations liées à l’emplacement</a> peuvent être transmises au sein d’un message en remplissant la propriété des entités du message à l’aide d’un objet *Place* ou *GeoCoordinates*.

L’objet Place contient les propriétés suivantes :

| Propriété | Description |
|----|----|
| Type | type de l’entité (« Place ») |
| Adresse | objet de description ou d’adresse postale (à venir) |
| Zone géographique | GeoCoordinates |
| HasMap | URL vers une carte ou un objet de carte (à venir) |
| Nom | nom du lieu |

L’objet geoCoordinates contient ces propriétés :

| Propriété | Description |
|----|----|
| Type | type de l’entité (« GeoCoordinates ») |
| Nom | nom du lieu |
| Longitude | longitude de l’emplacement (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Longitude | latitude de l’emplacement (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |
| Elevation | élévation de l’emplacement (<a href="https://en.wikipedia.org/wiki/World_Geodetic_System" target="_blank">WGS 84</a>) |

Cet exemple de code montre comment ajouter une entité place à la collection d’entités :

# <a name="ctabcs"></a>[C#](#tab/cs)
[!code-csharp[set GeoCoordinates](includes/code/dotnet-create-messages.cs#setGeoCoord)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)
```javascript
var entity = context.activity.entities;

const place = {
    elavation: 100,
    type: "GeoCoordinates",
    name : "myPlace",
    latitude: 123,
    longitude: 234
};

entity = [place];

```

---

### <a name="consume-entities"></a>Consommer des entités

# <a name="ctabcs"></a>[C#](#tab/cs)

Pour consommer des entités, utilisez le mot clé `dynamic` ou des classes fortement typées.

Cet exemple de code montre comment utiliser le mot clé `dynamic` pour traiter une entité dans la propriété `Entities` d’un message :

[!code-csharp[examine entity using dynamic keyword](includes/code/dotnet-create-messages.cs#examineEntity1)]

Cet exemple de code montre comment utiliser une classe fortement typée pour traiter une entité dans la propriété `Entities` d’un message :

[!code-csharp[examine entity using typed class](includes/code/dotnet-create-messages.cs#examineEntity2)]

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

Cet exemple de code montre comment traiter une entité dans la propriété `entity` d’un message :

```javascript
if (entity[0].type === "GeoCoordinates" && entity[0].latitude > 34) {
    // do something
}
```

---

## <a name="activity-types"></a>Types d'activités

Cet exemple de code montre comment traiter une activité de type **message** :

# <a name="ctabcs"></a>[C#](#tab/cs)

```cs
if (context.Activity.Type == ActivityTypes.Message){
    // do something
}
```

# <a name="javascripttabjs"></a>[JavaScript](#tab/js)

```js
if(context.activity.type === 'message'){
    // do something
}
```

---

Les activités peuvent être de différents types, au-delà du **message** le plus courant. Vous trouverez plus d’informations et des explications sur la [page de schéma d’activité](https://aka.ms/botSpecs-activitySchema).

::: moniker range="azure-bot-service-3.0"

## <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://docs.botframework.com/en-us/csharp/builder/sdkreference/dc/d2f/class_microsoft_1_1_bot_1_1_connector_1_1_activity.html" target="_blank">Classe Activité</a>
::: moniker-end
