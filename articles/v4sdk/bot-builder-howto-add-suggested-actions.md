---
title: Utiliser un bouton pour fournir une entrée | Microsoft Docs
description: Découvrez comment envoyer des actions suggérées dans des messages à l’aide du kit SDK Bot Framework pour JavaScript.
keywords: actions suggérées, boutons, entrée supplémentaire
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 603b6d6fe9fed99cb31649539e518651c135241a
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299305"
---
# <a name="use-button-for-input"></a>Utiliser un bouton pour fournir une entrée

[!INCLUDE[applies-to](../includes/applies-to.md)]

Vous pouvez permettre à votre bot de proposer des boutons sur lesquels l’utilisateur peut appuyer afin de fournir une entrée. Les boutons améliorent l’expérience utilisateur en lui permettant de répondre à une question ou d’effectuer une sélection en appuyant simplement sur un bouton, plutôt que d’avoir à taper une réponse avec un clavier. Contrairement aux boutons qui apparaissent dans les cartes enrichies (qui restent visibles et accessibles à l’utilisateur même après être touchées), les boutons qui apparaissent dans les volets des actions suggérées disparaissent une fois que l’utilisateur effectue une sélection. Cela empêche que l’utilisateur appuie sur les boutons périmés lors d’une conversation et simplifie le développement du bot (dans la mesure où il est inutile de prendre en compte ce scénario). 

## <a name="suggest-action-using-button"></a>Suggérer l’action d’utiliser un bouton

Les *actions suggérées* permettent à votre bot de présenter des boutons. Vous pouvez créer une liste d’actions suggérées (également appelée « réponses rapides ») qui s’affichera à l’utilisateur une seule fois pendant la conversation : 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le code source affiché ici repose sur l’[exemple de suggestions d’actions](https://aka.ms/SuggestedActionsCSharp).

[!code-csharp[suggested actions](~/../botbuilder-samples/samples/csharp_dotnetcore/08.suggested-actions/Bots/SuggestedActionsBot.cs?range=87-101)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le code source affiché ici repose sur l’[exemple d’actions suggérées](https://aka.ms/SuggestActionsJS).

[!code-javascript[suggested actions](~/../botbuilder-samples/samples/javascript_nodejs/08.suggested-actions/bots/suggestedActionsBot.js?range=61-64)]

---

## <a name="additional-resources"></a>Ressources supplémentaires

Vous pouvez accéder au code source complet affiché ici : l’[exemple CSharp](https://aka.ms/SuggestedActionsCSharp) ou l’[exemple JavaScript](https://aka.ms/SuggestActionsJS).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Enregistrer les données d’utilisateur et de conversation](./bot-builder-howto-v4-state.md)
