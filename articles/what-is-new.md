---
title: Nouveautés | Microsoft Docs
description: Découvrez les nouveautés dans Bot Framework.
keywords: Bot Framework, Azure Bot Service
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 12/10/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f7f3509bfd3a8488be9277188018da3193b8b29a
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491624"
---
# <a name="whats-new-december-2019"></a>Nouveautés de décembre 2019

[!INCLUDE[applies-to](includes/applies-to.md)]

Le kit SDK Bot Framework v4 est un [kit SDK open source](https://github.com/microsoft/botframework-sdk/#readme) qui permet aux développeurs de modéliser et de créer une conversation sophistiquée à l’aide de leur langage de programmation préféré.

Cet article résume les nouvelles fonctionnalités et les améliorations clés dans Bot Framework et Azure Bot Service.

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Libérer |[GA][1] | [GA][2] | [GA][3] | [Préversion 3][3a]|
|Docs | [docs][5] |[docs][5] |[docs][5]  | |
|Exemples |[.NET Core][6], [WebAPI][10] |[Node.js][7], [TypeScript][8], [es6][9]  | [Python][11a] | | 

#### <a name="python-sdk-ga"></a>Kit SDK Python (disponibilité générale)
Nous avons le plaisir d’annoncer la disponibilité générale du kit SDK Python. Le kit SDK prend en charge le runtime de bot principal, les connecteurs, le middleware (intergiciel), les dialogues, les invites, LUIS (Language Understanding) et QnA Maker. 

#### <a name="bot-framework-sdk-for-skills-ga"></a>Kit SDK Bot Framework pour Skills (disponibilité générale)

- **Compétences pour bots** : créez des compétences conversationnelles réutilisables pour ajouter des fonctionnalités à un bot. Tirez profit des compétences prédéfinies, notamment Calendrier, E-mail, Tâche, Point d’intérêt, Automobile, Météo et Actualités. Les compétences comprennent des modèles de langage, des dialogues, des questions et réponses et du code d’intégration destinés à personnaliser et étendre, selon les besoins. [[Docs](https://aka.ms/skills-docs)]

- **Skills for Power Virtual Agent – À venir !**  : pour les bots créés avec Power Virtual Agents, vous pouvez créer des compétences pour ces bots à l’aide de Bot Framework et d’Azure Cognitive Services sans avoir à créer entièrement un nouveau bot. 

#### <a name="bot-framework-for-power-virtual-agent-ga"></a>Bot Framework pour Power Virtual Agent (disponibilité générale)

Power Virtual Agent a été conçu pour permettre aux utilisateurs professionnels de créer des bots dans le cadre d’une expérience SaaS de création de bot basée sur l’interface utilisateur, sans avoir à programmer ni gérer des services IA spécifiques. Power Virtual Agents peut être étendu avec Microsoft Bot Framework, ce qui permet aux développeurs et aux utilisateurs professionnels de collaborer à la création de bots pour leur organisation. [[Docs](https://docs.microsoft.com/dynamics365/ai/customer-service-virtual-agent/overview)]

#### <a name="adaptive-dialogs-preview"></a>Adaptive Dialogs (préversion)
Adaptive Dialogs permet aux développeurs de mettre à jour dynamiquement le flux de conversation en fonction du contexte et des événements. Cela s’avère particulièrement pratique quand vous faites face à des changements de contexte de conversation et à des interruptions au milieu d’une conversation. [[Docs][48] | [Exemples C#][49]] 

#### <a name="language-generation-preview"></a>Language Generation (préversion)
Language Generation permet aux développeurs de séparer la logique utilisée pour générer les réponses du bot, notamment la capacité à définir plusieurs variantes dans une expression, à exécuter des expressions simples basées sur le contexte et à se reporter à la mémoire conversationnelle. [[Docs][44] | [Exemples C#][45]]

#### <a name="common-expression-language-preview"></a>Common Expression Language (préversion)
Common Expression Language vous permet d’évaluer le résultat d’une logique basée sur une condition au moment de l’exécution. Le langage commun peut être utilisé dans le SDK Bot Framework et les composants d’IA conversationnelle, comme Adaptative Dialogs et Language Generation. [[Docs][40] | [API][41]]

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
[11a]:https://aka.ms/python-sample-repo


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
