---
title: Ajouter des activités de trace à votre bot - Bot Service
description: Décrit l’activité de trace dans le SDK Bot Framework et explique comment l’utiliser.
keywords: trace, activité, bot, SDK Bot Framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/18/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 0a34e0d3afacaec0fcfd7b12aaa9b1b3a78f31c0
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791011"
---
# <a name="add-trace-activities-to-your-bot"></a>Ajouter des activités de trace à votre bot

[!INCLUDE[applies-to](../includes/applies-to.md)]

<!-- What is it and why use it -->

Une _activité de trace_ est une activité que votre bot peut envoyer à Bot Framework Emulator.
Les activités de trace s’avèrent utiles pour déboguer un bot de manière interactive dans le sens où elles permettent de voir des informations sur votre bot pendant qu’il s’exécute localement.

<!-- Details -->

Les activités de trace sont envoyées uniquement à l’émulateur et non à un autre client ou canal.
L’émulateur les affiche dans le journal, mais pas dans le panneau de conversation principal.

- Les activités de trace envoyées via le contexte de tour sont envoyées via les _gestionnaires d’activités d’envoi_ inscrits dans le contexte de tour.
- Les activités de trace envoyées via le contexte de tour sont associées à l’activité entrante, en appliquant la référence de la conversation, s’il en existait une.
  Pour un message proactif, _reply to ID_ (ID de réponse) est un nouveau GUID.
- Quelle que soit la façon dont elle est envoyée, une activité de trace ne définit jamais l’indicateur _responded_.

## <a name="to-use-a-trace-activity"></a>Pour utiliser une activité de trace

Pour voir une activité de trace dans l’émulateur, vous avez besoin d’un scénario dans lequel votre bot enverra une activité de trace, par exemple en levant une exception et en envoyant une activité de trace à partir du gestionnaire d’erreurs de tour de l’adaptateur.

Pour envoyer une activité de trace à partir de votre bot :

1. Créez une activité.
   - Affectez à sa propriété _type_ la valeur « trace ». Cela est nécessaire.
   - Si vous le souhaitez, définissez ses propriétés _name_, _label_, _value_ et _value type_ en fonction de la trace.
1. Pour envoyer l’activité de trace, utilisez la méthode _send activity_ (activité d’envoi) de l’objet _turn context_ (contexte de tour).
   - Cette méthode ajoute des valeurs pour les autres propriétés nécessaires de l’activité, en fonction de l’activité entrante.
     Il s’agit notamment des propriétés _channel ID_, _service URL_, _from_ et _recipient_.

Pour voir une activité de trace dans l’émulateur :

1. Exécutez le bot localement sur votre ordinateur.
1. Testez-le à l’aide de l’émulateur.
   - Interagissez avec le bot et suivez les étapes de votre scénario pour générer l’activité de trace.
   - Quand le bot émet l’activité de trace, celle-ci s’affiche dans le journal de l’émulateur.

Voici une activité de trace qui risque de s’afficher si vous exécutez le bot Core sans configurer au préalable la base de connaissances QnA Maker sur laquelle repose le bot.

![Capture d’écran de l’émulateur](./media/using-trace-activities.png)

## <a name="add-a-trace-activity-to-the-adapters-on-error-handler"></a>Ajouter une activité de trace au gestionnaire d’erreurs de l’adaptateur

Le gestionnaire d’_erreurs de tour_ de l’adaptateur intercepte les exceptions qui ne l’ont pas été et que le bot a levées pendant un tour.
Il s’agit de l’endroit idéal pour une activité de trace, car vous pouvez envoyer un message convivial à l’utilisateur et envoyer des informations de débogage à l’émulateur à propos de l’exception.

Cet exemple de code est tiré de l’exemple du **bot Core**. Consultez l’exemple complet en [**C#** ](https://aka.ms/cs-core-sample), [**JavaScript**](https://aka.ms/js-core-sample) ou [**Python**](https://aka.ms/py-core-sample).

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

La méthode d’assistance **SendTraceActivityAsync** définie dans cet exemple envoie des informations sur les exceptions à l’émulateur en tant qu’activité de trace.

**AdapterWithErrorHandler.cs**

[!code-csharp[SendTraceActivityAsync](~/../BotBuilder-Samples/samples/csharp_dotnetcore/13.core-bot/AdapterWithErrorHandler.cs?range=16-51&highlight=33-34)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le gestionnaire **onTurnError** de l’adaptateur crée l’activité de trace pour inclure les informations sur les exceptions et les envoie à l’émulateur.

**index.js**

[!code-javascript[onTurnError ](~/../BotBuilder-Samples/samples/javascript_nodejs/13.core-bot/index.js?range=35-57&highlight=8-14)]

# <a name="pythontabpython"></a>[Python](#tab/python)

Le gestionnaire **on_error** de l’adaptateur crée l’activité de trace pour inclure les informations relatives aux exceptions, avant de les envoyer à l’émulateur.
```python
async def on_error(context: TurnContext, error: Exception):
...

if context.activity.channel_id == "emulator":

# Create a trace activity that contains the error object
trace_activity = Activity(
    label="TurnError",
    name="on_turn_error Trace",
    timestamp=datetime.utcnow(),
    type=ActivityTypes.trace,
    value=f"{error}",
    value_type="https://www.botframework.com/schemas/error",
)

# Send a trace activity, which will be displayed in Bot Framework Emulator
await context.send_activity(trace_activity)

...
```
---

## <a name="additional-resources"></a>Ressources supplémentaires

- Le guide pratique pour [Déboguer un bot avec l’intergiciel d’inspection](../bot-service-debug-inspection-middleware.md) explique comment ajouter l’intergiciel (middleware) qui émet des activités de suivi.
- Pour déboguer un bot déployé, vous pouvez utiliser Application Insights. Pour plus d’informations, consultez [Ajouter la télémétrie à votre bot](bot-builder-telemetry.md).
- Pour obtenir des informations détaillées sur chaque type d’activité, consultez la rubrique sur le [schéma d’activité de Bot Framework](https://aka.ms/botSpecs-activitySchema).
