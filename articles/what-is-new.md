---
title: Nouveautés | Microsoft Docs
description: Découvrez les nouveautés dans Bot Framework.
keywords: Bot Framework, Azure Bot Service
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 11/04/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 8cb03814a9fbb7fdbbfb457400eca3cf6a67ec17
ms.sourcegitcommit: 490810d278d1c8207330b132f28a5eaf2b37bd07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73592239"
---
# <a name="whats-new-november-2019-ignite"></a>Nouveautés de novembre 2019 (Ignite)

[!INCLUDE[applies-to](includes/applies-to.md)]

Le kit SDK Bot Framework v4 est un [kit SDK open source](https://github.com/microsoft/botframework-sdk/#readme) qui permet aux développeurs de modéliser et de créer une conversation sophistiquée à l’aide de leur langage de programmation préféré.

Cet article résume les nouvelles fonctionnalités et les améliorations clés dans Bot Framework et Azure Bot Service.


|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Libérer |[4.6 GA][1] | [4.6 GA][2] | [Bêta 4][3] | [Préversion 3][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemples |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | | | 


[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/typescript_nodejs
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi

#### <a name="bot-framework-sdk-for-microsoft-teams-ga"></a>SDK Bot Framework pour Microsoft Teams (GA)
La version du kit SDK Bot Framework v4.6 intègre entièrement la prise en charge de la création de bots Teams, ce qui permet aux utilisateurs de s’en servir dans les conversations de canal ou de groupe. En ajoutant un bot à une équipe ou à une conversation, tous les utilisateurs de la conversation peuvent profiter de la fonctionnalité de bot directement dans la conversation.  [[Docs](https://docs.microsoft.com/azure/bot-service/bot-builder-basics-teams)]

#### <a name="bot-framework-for-power-virtual-agent-preview"></a>Bot Framework for Power Virtual Agent (préversion)

Power Virtual Agent a été conçu pour permettre aux utilisateurs professionnels de créer des bots dans le cadre d’une expérience SaaS de création de bot basée sur l’interface utilisateur, sans avoir à programmer ni gérer des services IA spécifiques. Power Virtual Agents peut être étendu avec Microsoft Bot Framework, ce qui permet aux développeurs et aux utilisateurs professionnels de collaborer à la création de bots pour leur organisation. [[Docs](https://docs.microsoft.com/dynamics365/ai/customer-service-virtual-agent/overview)]


#### <a name="bot-framework-sdk-for-skills-preview"></a>SDK Bot Framework for Skills (préversion)

- **Compétences pour bots** : créez des compétences conversationnelles réutilisables pour ajouter des fonctionnalités à un bot. Tirez parti des compétences prédéfinies comme Calendrier, E-mail, Tâche, Point d’intérêt, Automobile, Météo et Actualités. Les compétences comprennent des modèles de langage, des dialogues, des questions et réponses et du code d’intégration destinés à personnaliser et étendre, selon les besoins. [[Docs](https://microsoft.github.io/botframework-solutions/overview/skills/)]

- **Skills for Power Virtual Agent – À venir !**  : pour les bots créés avec Power Virtual Agents, vous pouvez créer des compétences pour ces bots à l’aide de Bot Framework et d’Azure Cognitive Services sans avoir à créer entièrement un nouveau bot. 

#### <a name="adaptive-dialogs-preview"></a>Adaptive Dialogs (préversion)
Adaptive Dialogs permet aux développeurs de mettre à jour dynamiquement le flux de conversation en fonction du contexte et des événements. Cela s’avère particulièrement pratique quand vous faites face à des changements de contexte de conversation et à des interruptions au milieu d’une conversation. [[Docs][48] | [Exemples C#][49]] 

#### <a name="language-generation-preview"></a>Language Generation (préversion)
Language Generation permet aux développeurs de séparer la logique utilisée pour générer les réponses du bot, notamment la capacité à définir plusieurs variantes dans une expression, à exécuter des expressions simples basées sur le contexte et à se reporter à la mémoire conversationnelle. [[Docs][44] | [Exemples C#][45]]

#### <a name="common-expression-language-preview"></a>Common Expression Language (préversion)
Common Expression Language vous permet d’évaluer le résultat d’une logique basée sur une condition au moment de l’exécution. Le langage commun peut être utilisé dans le SDK Bot Framework et les composants d’IA conversationnelle, comme Adaptative Dialogs et Language Generation. [[Docs][40] | [API][41]]


[40]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/common-expression-language#readme
[41]:https://github.com/Microsoft/BotBuilder-Samples/blob/master/experimental/common-expression-language/api-reference.md
[43]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation#readme
[44]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/docs
[45]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/csharp_dotnetcore
[46]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/language-generation/javascript_nodejs/13.core-bot
[47]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme
[48]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs
[49]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore
[50]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/declarative

## <a name="additional-information"></a>Informations supplémentaires
- Vous pouvez voir les annonces précédentes [ici](what-is-new-archive.md).
