---
title: Nouveautés | Microsoft Docs
description: Découvrez les nouveautés dans Bot Framework.
keywords: Bot Framework, Azure Bot Service
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.subservice: abs
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: f7083c45e67d8731e25e14577f6b061732ffefd5
ms.sourcegitcommit: f3fda6791f48ab178721b72d4f4a77c373573e38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68671397"
---
# <a name="whats-new-in-bot-framework-july-2019"></a>Nouveautés de Bot Framework (juillet 2019)

[!INCLUDE[applies-to](includes/applies-to.md)]

Le kit SDK Bot Framework v4 est un [kit SDK open source][1a] qui permet aux développeurs de modéliser et de créer une conversation sophistiquée à l’aide de leur langage de programmation préféré.

Cet article résume les nouvelles fonctionnalités et les améliorations clés dans Bot Framework et Azure Bot Service.

|   | C#  | JS  | Python |   
|---|:---:|:---:|:------:|
|Kit SDK |[4.5][1] | [4.5][2] | [4.4.0b2 (préversion)][3] | 
|Docs | [docs][5] |[docs][5] |  | |
|Exemples |[.NET Core][6], [WebAPI][10] |[Node.js][7] , [TypeScript][8], [es6][9]  | [Python][111] | | 

[1a]:https://github.com/microsoft/botframework-sdk/#readme
[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_typescript
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[111]:https://github.com/Microsoft/botbuilder-python/tree/master/samples


## <a name="bot-framework-channels"></a>Canaux Bot Framework
- [Direct Line Speech (préversion publique)](https://aka.ms/streaming-extensions) | [Documentation](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0) : Bot Framework et les services Microsoft Speech fournissent un canal activant le streaming bidirectionnel de la parole et du texte, du client vers l’application de bot, à l’aide de WebStockets.  

- [Extension Direct Line App Service (préversion publique)](https://portal.azure.com) | [Documentation](https://aka.ms/directline-ase) : version de Direct Line qui permet aux clients de se connecter directement aux bots à l’aide de l’API Direct Line. Cela offre de nombreux avantages, notamment des performances et une isolation accrues. L’extension Direct Line App Service est disponible sur tous les services Azure App Services, notamment ceux hébergés dans un Azure App Service Environment. Un Azure App Service Environment fournit une isolation et est idéal pour travailler au sein d’un réseau virtuel. Un réseau virtuel vous permet de créer votre propre espace privé dans Azure et il est essentiel pour votre réseau cloud, car il offre isolation, segmentation et d’autres avantages clés. 

## <a name="bot-framework-sdk"></a>SDK Bot Framework
- [Adaptive Dialog (préversion du SDK v4.6)](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog#readme) | [Documentation](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/docs) | [Exemples C#](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/adaptive-dialog/csharp_dotnetcore) : Adaptive Dialog (Boîte de dialogue adaptative) permet maintenant aux développeurs de mettre à jour dynamiquement le workflow de conversation en fonction du contexte et des événements. Cela s’avère particulièrement utile lorsque vous traitez des changements de contexte de conversation et des interruptions au milieu d’une conversation. 
  
- [SDK Python Bot Framework (préversion 2)](https://github.com/microsoft/botbuilder-python) | [Exemples](https://github.com/Microsoft/botbuilder-python/tree/master/samples) : Le SDK Python prend maintenant en charge OAuth, Prompts, CosmosDB et inclut toutes les fonctionnalités majeures du SDK 4.5, ainsi que des exemples pour vous aider à découvrir les nouvelles fonctionnalités du SDK.

## <a name="bot-framework-testing"></a>Tests Bot Framework
- [Test unitaire](http://aka.ms/bot-test-package) | [Documentation](https://aka.ms/testing-framework) | [Exemple C#](https://aka.ms/corebot-test) | [Exemple JS](https://aka.ms/js-core-test-sample) : en réponse aux demandes des clients et développeurs souhaitant de meilleurs outils de test, la version de juillet du SDK introduit une nouvelle fonctionnalité de test unitaire. Le package Microsoft.Bot.Builder.testing simplifie le processus des dialogues de test unitaire dans votre bot. 

- [Tests de canaux](https://github.com/Microsoft/BotFramework-Emulator/releases) | [docs](https://aka.ms/channel-testing): 

introduit lors de Microsoft Build 2019, Bot Inspector est une nouvelle fonctionnalité de Bot Framework Emulator qui vous permet de déboguer et de tester des bots sur des canaux tels que Microsoft Teams, Slack, Cortana, etc. Lorsque vous utilisez le bot sur des canaux spécifiques, les messages sont mis en miroir dans Bot Framework Emulator où vous pouvez inspecter les données de message que le bot a reçues. De plus, un instantané de l’état de mémoire du bot pour un tour donné entre le canal et le bot est également restitué.

## <a name="web-chat"></a>Discussion Web
- En réponse aux demandes des clients d’entreprise, nous avons ajouté [un exemple de conversation web](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps#single-sign-on-demo-for-enterprise-apps-using-oauth) qui montre comment autoriser un utilisateur à accéder aux ressources d’une application métier avec un bot. Deux types de ressources sont utilisés pour illustrer l’interopérabilité d’OAuth avec Microsoft Graph et l’API GitHub.

## <a name="solutions"></a>solutions
- [Accélérateur de solution Assistant virtuel](https://github.com/Microsoft/botframework-solutions#readme) : propose un ensemble de modèles, d’accélérateurs de solutions et de compétences pour aider à créer des expériences conversationnelles sophistiquées. Le nouveau client d’application Android pour l’Assistant virtuel qui s’intègre à la reconnaissance vocale Direct Line Speech et à l’Assistant virtuel montrant comment un client d’appareil peut interagir avec votre Assistant virtuel et afficher des cartes adaptatives. Les mises à jour incluent également la prise en charge des fonctionnalités vocales Direct Line Speech et de Microsoft Teams.
  
- [Agent virtuel Dynamics 365 pour le service client (préversion publique)](https://dynamics.microsoft.com/en-us/ai/virtual-agent-for-customer-service/) : avec la préversion publique, vous pouvez fournir un service client exceptionnel avec des agents virtuels intelligents et adaptables. Les experts du service client peuvent facilement créer et améliorer les bots avec des insights pilotés par l’intelligence artificielle.
  
- [Chat pour Dynamics 365](https://www.powerobjects.com/powerpacks/powerchat/) : Chat pour Dynamics 365 offre plusieurs fonctionnalités permettant de garantir que les agents du support et les utilisateurs finaux peuvent interagir efficacement et rester très productifs. Discutez en direct et suivez les conversations des visiteurs de votre site web au sein de Microsoft Dynamics 365.

## <a name="additional-information"></a>Informations supplémentaires
- Vous pouvez voir les annonces précédentes [ici](what-is-new-archive.md).
