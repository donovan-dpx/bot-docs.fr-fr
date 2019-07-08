---
title: Nouveautés | Microsoft Docs
description: Découvrez les nouveautés dans Bot Framework.
keywords: Bot Framework, Azure Bot Service
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: get-started-article
ms.service: bot-service
ms.subservice: abs
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 2894ed29aa6d17089b9287ba4c4819571d18befd
ms.sourcegitcommit: a295a90eac461f8b96770dd902ba44919acf33fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67405985"
---
# <a name="whats-new-in-bot-framework"></a>Nouveautés dans Bot Framework

[!INCLUDE[applies-to](includes/applies-to.md)]

Le kit SDK Bot Framework v4 est un [kit SDK open source][1a] qui permet aux développeurs de modéliser et de créer une conversation sophistiquée à l’aide de leur langage de programmation préféré.

Cet article résume les nouvelles fonctionnalités et les améliorations clés dans Bot Framework et Azure Bot Service.

|   | C#  | JS  | Python |  Java | 
|---|:---:|:---:|:------:|:-----:|
|Kit SDK |[4.4.3][1] | [4.4.0][2] | [4.4.0b1 (préversion)][3] | [4.0.0a6 (préversion)][3a]|
|Docs | [docs][5] |[docs][5] |  | |
|Exemples |[.Net Core][6], [WebAPI][10] |[Node.js][7] , [TypeScript][8], [es6][9]  | [Python][111] | | 

[1a]:https://github.com/microsoft/botframework-sdk/#readme
[1]:https://github.com/Microsoft/botbuilder-dotnet/#packages
[2]:https://github.com/Microsoft/botbuilder-js#packages
[3]:https://github.com/Microsoft/botbuilder-python#packages
[3a]:https://github.com/Microsoft/botbuilder-java#packages
[4]:https://github.com/Microsoft/botbuilder-java#packages
[5]:https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0
[6]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore
[7]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs
[8]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_typescript
[9]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_es6
[10]:https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi
[111]:https://github.com/Microsoft/botbuilder-python/tree/master/samples

<a name="V4-whats-new"></a>
## <a name="bot-framework-sdk-new-in-preview"></a>Kit SDK Bot Framework (Nouveau ! En préversion)

- [Boîte de dialogue adaptive][47] | [docs][48] | [Exemples C#][49] : Les dialogues adaptatifs permettent aux développeurs de générer des conversations qui peuvent être modifiées dynamiquement au fur et à mesure que la conversation progresse.  Traditionnellement, les développeurs ont mappé le flux entier d’une conversation en amont, ce qui limite la flexibilité de la conversation.  Les dialogues adaptatifs leur permettent d’être plus flexibles, pour répondre aux changements de contexte et insérer de nouvelles étapes ou des dialogues secondaires complets dans la conversation au fur et à mesure qu’elle progresse. 

- [Génération de langage][43] | [docs][44] | [Exemples C#][45] : La génération de langage permet au développeur d’extraire les chaînes incorporées à partir de leurs fichiers de code et de ressources, et de les gérer via un runtime de génération de langage et un format de fichier.  La génération de langage permet aux clients de définir plusieurs variantes d’une expression, d’exécuter des expressions simples en fonction du contexte, de faire référence à la mémoire conversationnelle et, au fil du temps, nous permettra d’apporter des capacités supplémentaires favorisant toutes une expérience de conversation plus naturelle.

- [Langage d’expression commun][40] | [api][41] : Les dialogues adaptatifs et la génération de langage s’appuient sur un langage d’expression commun pour dynamiser les conversations de bot.

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

## <a name="botkit"></a>Botkit
[Botkit][100] est un outil de développeur et un kit SDK permettant de créer des chatbots, des applications et des intégrations personnalisées pour les principales plateformes de messagerie. Les bots Botkit sont à l’écoute (`hear()`) des déclencheurs, posent (`ask()`) des questions et donnent (`say()`) des réponses. Les développeurs peuvent utiliser cette syntaxe pour créer des dialogues : désormais compatible avec la dernière version du kit SDK Bot Framework. 

De plus, Botkit est accompagné de 6 adaptateurs de plateforme, autorisant les applications bot Javascript à communiquer directement avec les plateformes de messagerie : [Slack][102], [Webex Teams][103], [Google Hangouts][104], [Facebook Messenger][105], [Twilio][106] et [Web Chat][107].

Botkit fait partie de Microsoft Bot Framework et est publié sous la [licence MIT open source][101]

[100]:https://github.com/howdyai/botkit#readme
[101]:https://github.com/howdyai/botkit/blob/master/LICENSE.md
[102]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-slack#readme
[103]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-webex#readme
[104]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-hangouts#readme
[105]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-facebook#readme
[106]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-twilio-sms#readme
[107]:https://github.com/howdyai/botkit/tree/master/packages/botbuilder-adapter-web#readme

## <a name="bot-framework-solutions-new-in-preview"></a>Bot Framework Solutions (Nouveauté ! En préversion)

Le [dépôt Bot Framework Solutions](https://github.com/Microsoft/AI#readme) propose un ensemble de modèles, d’accélérateurs de solutions et de compétences pour aider à créer des expériences conversationnelles avancées, de type assistant.

| Nom | Description |  
|:------------|:------------| 
|[**Assistant virtuel**](https://github.com/Microsoft/AI/tree/master/docs#virtual-assistant) | Les clients souhaitent ardemment être en mesure de fournir un assistant conversationnel adapté à leur marque et à leurs utilisateurs, et disponible sur un large éventail de canevas et d’appareils. <br/><br/> Le modèle d’entreprise simplifie considérablement la création d’un nouveau projet de bot, y compris : les intentions conversationnelles de base, l’intégration de Dispatch, QnA Maker, Application Insights et un déploiement automatisé.|
|[**Compétences**](https://github.com/Microsoft/AI/blob/master/docs/overview/skills.md)| Les développeurs peuvent composer des expériences conversationnelles en combinant des fonctionnalités conversationnelles réutilisables, appelées « compétences ». Les compétences sont elles-mêmes des bots appelés à distance et un modèle de développement de compétences (.NET, TS) est disponible pour faciliter la création de compétences. 
|[**Analytics**](https://github.com/Microsoft/AI/blob/master/docs/readme.md#analytics)| Obtenez des informations clés sur la santé de votre bot et son comportement avec les solutions AI Analytics conversationnelles. Passez en revue les données de télémétrie disponibles, les exemples de requêtes Application Insights et les tableaux de bord Power BI pour comprendre tout l’éventail de conversations de votre bot avec les utilisateurs. |

## <a name="azure-bot-service"></a>Azure Bot Service
Azure Bot Service vous permet d’héberger des bots intelligents de classe Entreprise avec la propriété et le contrôle complets de vos données. Les développeurs peuvent s’inscrire et connecter leurs bots aux utilisateurs Skype, Microsoft Teams, Cortana, Web Chat, etc. [Azure][27]  |  [docs][28] | [Se connecter aux canaux][29] 

* **Client Direct Line JS** : Si vous souhaitez utiliser le canal Direct Line dans Azure Bot Service et que vous n’utilisez pas le client WebChat, le client Direct Line JS peut être utilisé dans votre application personnalisée. Accédez à [GitHub][30] pour plus d’informations.

<a name="ABS-whats-new"></a>

* **Nouveau ! Canal Direct Line Speech** : Nous avons rassemblé Bot Framework et les services Microsoft Speech pour fournir un canal permettant le streaming bidirectionnel de la parole et du texte, du client vers l’application de bot.  Pour plus d’informations, consultez la façon d’ajouter un [canal vocal à votre bot](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0).

[27]:https://azure.microsoft.com/services/bot-service/
[28]:https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[29]:https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0
[30]:https://github.com/Microsoft/BotFramework-DirectLineJS/blob/master/README.md


## <a name="bot-framework-emulator"></a>Émulateur de Bot Framework
[Bot Framework Emulator][60] est une application de bureau multiplateforme qui permet aux développeurs de bots de tester et de déboguer les bots créés à l’aide du kit SDK Bot Framework. Vous pouvez utiliser Bot Framework Emulator pour tester des bots qui s’exécutent localement sur votre machine ou pour vous connecter à des bots qui s’exécutent à distance.

- [Télécharger le plus récent][61] | [Docs][62]

<a name="Emulator-whats-new"></a>
### <a name="bot-inspector-new-in-preview"></a>Bot Inspector (Nouveau ! En préversion)

Bot Framework Emulator a publié une version bêta de la nouvelle fonctionnalité d’inspecteur de bot. Elle fournit un moyen de déboguer et de tester vos bots de kit SDK Bot Framework v4 sur des canaux comme Microsoft Teams, Slack, Cortana, Facebook Messenger, Skype, etc. Pendant la conversation, les messages sont mis en miroir vers Bot Framework Emulator où vous pouvez inspecter les données de message que le bot a reçues. De plus, un instantané de l’état du bot pour n’importe quel tour donné entre le canal et le bot est également rendu. En savoir plus sur [Bot Inspector](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md)

[60]:https://github.com/Microsoft/BotFramework-Emulator#readme
[61]:https://github.com/Microsoft/BotFramework-Emulator/releases/latest
[62]:https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0


## <a name="related-services"></a>Services connexes

### <a name="language-understanding"></a>Language Understanding 
Service basé sur le machine learning permettant de générer des expériences de langage naturel. Créez rapidement des modèles personnalisés prêts pour l’entreprise qui s’améliorent continuellement. Le [service Language Understanding (LUIS)][30] permet à votre application de comprendre le sens des propos d’une personne.

<a name="LUIS-whats-new"></a>

- **Nouveau ! Rôles, entités externes et entités dynamiques** : Le service LUIS a ajouté plusieurs fonctionnalités permettant aux développeurs d’extraire des informations plus détaillées à partir du texte, de sorte que les utilisateurs peuvent désormais générer des solutions plus intelligentes avec moins d’efforts. Le service LUIS a également étendu les rôles à tous les types d’entités, ce qui permet à ces entités d’être classées avec différents sous-types en fonction du contexte. Les développeurs ont désormais un contrôle plus précis de ce qu’ils peuvent faire avec le service LUIS, y compris la possibilité d’identifier et de mettre à jour des modèles au moment de l’exécution, via les listes dynamiques et les entités externes. Les listes dynamiques sont ajoutées aux entités de liste au moment de la prédiction, permettant ainsi une correspondance exacte des informations spécifiques à l’utilisateur. Des extracteurs d’entités supplémentaires distincts sont exécutés avec des entités externes, et ces informations peuvent être ajoutées au service LUIS en tant que signaux forts pour les autres modèles.

- **Nouveau ! Tableau de bord d’analyse** : Le service LUIS publie un tableau de bord d’analyse plus détaillé, complet et visuellement riche. Sa conception conviviale met en évidence les problèmes courants auxquels la plupart des utilisateurs sont confrontés lors de la conception d’applications, en fournissant des explications simples sur la façon de les résoudre pour aider les utilisateurs à obtenir plus d’informations sur la qualité de leurs modèles, les problèmes potentiels liés aux données et des conseils pour adopter de bonnes pratiques.

[Docs][31] | [Add language understanding to your bot][32] 

[18]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS#readme
[19]:https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker#readme
[30]:https://www.luis.ai
[31]:https://docs.microsoft.com/azure/cognitive-services/LUIS/Home
[32]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=csharp

### <a name="qna-maker"></a>QnA Maker
[QnA Maker][33] est un service d’API cloud qui crée une couche conversationnelle de questions-réponses sur vos données. Avec QnA Maker, vous pouvez générer, entraîner et publier en quelques minutes un bot de questions-réponses simple reposant sur des URL de FAQ, des documents structurés, des manuels de produit ou des contenus éditoriaux.

<a name="QnA-whats-new"></a>

- **Nouveau ! Pipeline d’extraction** : À présent, vous pouvez extraire des informations hiérarchiques à partir d’URL, de fichiers et de sharepoint
- **Nouveau ! Intelligence** : Modèles de classement contextuel, suggestions d’apprentissage actif
- **Nouveau ! Conversation** : Conversations multitours dans QnA Maker.

[Docs][34]  | [add qnamaker to your bot][35] 

[33]:https://www.qnamaker.ai/
[34]:https://aka.ms/qnamaker-docs-home
[35]:https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&branch=pr-en-us-1325&tabs=cs

### <a name="speech-services"></a>Services Speech
Les [services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/) convertissent du contenu audio en texte, effectuent une traduction vocale et convertissent du texte en parole avec les services vocaux unifiés. Vous pouvez utiliser les services vocaux pour intégrer la parole dans votre bot, créer des mots déclencheurs personnalisés et créer du contenu dans plusieurs langues.

### <a name="adaptive-cards"></a>Cartes adaptatives
Les [cartes adaptatives](https://adaptivecards.io) sont un standard ouvert qui permet aux développeurs d’échanger le contenu de la carte de manière commune et cohérente. Elles sont utilisées par les développeurs de Bot Framework pour créer des expériences conversationnelles exceptionnelles via plusieurs canaux.

## <a name="additional-information"></a>Informations supplémentaires
- Consultez la page GitHub pour plus d’[informations](https://github.com/Microsoft/botframework/blob/master/whats-new.md#whats-new).
