---
title: Ajouter de la reconnaissance vocale aux messages (v3 JS) - Bot Service
description: Découvrez comment ajouter la reconnaissance vocale aux messages à l’aide du kit SDK Bot Framework pour Node.js.
author: v-ducvo
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: a5e97aa859f8b200fecb6df213bce1f9d9c1f912
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75790354"
---
# <a name="add-speech-to-messages"></a>Ajouter de la reconnaissance vocale aux messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-text-to-speech.md)
> - [Node.JS](../nodejs/bot-builder-nodejs-text-to-speech.md)
> - [REST](../rest-api/bot-framework-rest-connector-text-to-speech.md)

Si vous générez un bot pour un canal où sont activées les fonctionnalités vocales, tel que Cortana, vous pouvez créer des messages qui spécifient le texte que doit énoncer votre bot. Vous pouvez également tenter d’influencer l’état du microphone du client en spécifiant un [conseil de saisie](bot-builder-nodejs-send-input-hints.md) pour indiquer si votre bot accepte, attend ou ignore l’entrée de l’utilisateur.

## <a name="specify-text-to-be-spoken-by-your-bot"></a>Spécifier le texte à énoncer par votre bot

Avec le kit SDK Bot Framework pour Node.js, il existe plusieurs façons de spécifier le texte que doit prononcer le bot sur un canal à reconnaissance vocale. Vous pouvez définir la propriété `IMessage.speak` et envoyer le message à l’aide de la méthode `session.send()`, envoyer le message à l’aide de la méthode `session.say()` (en transmettant les paramètres qui spécifient le texte affiché, le texte parlé et les options), ou envoyer le message à l’aide d’une invite intégrée (en spécifiant les options `speak` et `retrySpeak`).

### <a id="message-speak"></a> IMessage.speak

Si vous créez un message qui sera envoyé à l’aide de la méthode `session.send()`, définissez la propriété `speak` afin qu’elle spécifie le texte à énoncer par votre bot. L’exemple de code suivant crée un message qui spécifie le texte à énoncer et indique que le bot [accepte l’entrée utilisateur](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[IMessage.speak](../includes/code/node-text-to-speech.js#IMessageSpeak)]

### <a id="session-say"></a> session.say()

Comme alternative à l’utilisation de `session.send()`, vous pouvez appeler la méthode `session.say()` pour créer et envoyer un message qui spécifie le texte à énoncer, en plus du texte à afficher et d’autres options. La méthode est définie comme suit :

`session.say(displayText: string, speechText: string, options?: object)`

| Paramètre | Description |
|----|----|
| `displayText` | Texte à afficher. |
| `speechText` | Texte (en texte brut ou au format <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">SSML</a>) à prononcer. |
| `options` | Un `IMessage`objet qui peut contenir une pièce jointe ou un [indicateur d’entrée](bot-builder-nodejs-send-input-hints.md). |

L’exemple de code suivant envoie un message qui spécifie le texte à afficher et le texte à énoncer et indique que le bot [ignore l’entrée utilisateur](bot-builder-nodejs-send-input-hints.md).

[!code-javascript[Session.say()](../includes/code/node-text-to-speech.js#SessionSay)]

### <a id="prompt-options"></a> Options d’invite

En utilisant l’une des invites intégrées, vous pouvez définir les options `speak` et `retrySpeak` afin de spécifier le texte à énoncer par votre bot. L’exemple de code suivant crée une invite qui spécifie le texte à afficher, le texte à énoncer initialement et le texte à énoncer après avoir attendu l’entrée utilisateur un certain temps. Il indique que le bot [attend des entrées utilisateur](bot-builder-nodejs-send-input-hints.md) et utilise la mise en forme [SSML](#ssml) pour spécifier que le mot « sure » doit être modérément appuyé.

[!code-javascript[Prompt](../includes/code/node-text-to-speech.js#Prompt)]

## <a id="ssml"></a> Speech Synthesis Markup Language (SSML)

Pour spécifier le texte à énoncer par votre bot, vous pouvez utiliser une chaîne de texte brut ou une chaîne mise en forme selon Speech Synthesis Markup Language (SSML), un langage de balisage basé sur XML qui vous permet de contrôler diverses caractéristiques du discours de votre bot telles que la voix, le débit, le volume, la prononciation, la hauteur et bien plus encore. Pour plus d’informations sur SSML, consultez <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language Reference</a> (Références sur Speech Synthesis Markup Language).

> [!TIP]
> Utilisez une <a href="https://www.npmjs.com/search?q=ssml" target="_blank">bibliothèque SSML</a> pour créer des fichiers SSML correctement mis en forme.

## <a name="input-hints"></a>Conseils de saisie

Lorsque vous envoyez un message sur le canal de reconnaissance vocale, vous pouvez tenter d’influencer l’état du microphone du client en spécifiant un conseil de saisie pour indiquer si votre bot accepte, attend ou ignore l’entrée de l’utilisateur. Pour plus d’informations, consultez [Ajouter des conseils de saisie aux messages](bot-builder-nodejs-send-input-hints.md).

## <a name="sample-code"></a>Exemple de code 

Pour obtenir un exemple complet qui montre comment créer un bot à reconnaissance vocale à l’aide du kit SDK Bot Framework pour Node.js, consultez l’<a href="https://github.com/microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-RollerSkill" target="_blank">exemple Roller</a> dans GitHub.

## <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://msdn.microsoft.com/library/hh378377(v=office.14).aspx" target="_blank">Speech Synthesis Markup Language (SSML)</a>
- <a href="https://github.com/microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/demo-RollerSkill" target="_blank">Exemple Roller (GitHub)</a>
