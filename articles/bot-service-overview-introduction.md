---
title: Présentation d’Azure Bot Service | Microsoft Docs
description: Découvrez Bot Service, qui permet de créer, connecter, tester, déployer, surveiller et gérer des bots.
keywords: vue d’ensemble, introduction, SDK, présentation
author: Kaiqb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/05/2019
ms.openlocfilehash: 0206cf3381d95fbc58d49924aa19dadea5f226dc
ms.sourcegitcommit: dbc7eaee5c1f300b23c55abe6b60cd01c7408915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74415157"
---
# <a name="about-azure-bot-service"></a>À propos d’Azure Bot Service

[!INCLUDE [applies-to-both](includes/applies-to-both.md)]

Azure Bot Service et Bot Framework fournissent des outils pour créer, tester, déployer et gérer des bots intelligents, le tout en un même endroit. L’infrastructure modulable et extensible fournie par le Kit de développement logiciel (SDK), les outils, les modèles et les services d’intelligence artificielle permettent aux développeurs de créer des bots en mesure d’offrir des fonctionnalités vocales, de comprendre le langage naturel, de traiter les questions-réponses, et bien davantage.

## <a name="what-is-a-bot"></a>Qu’est-ce qu’un bot ?
Les bots offrent une expérience qui donne l’impression aux utilisateurs d’avoir moins à faire à un ordinateur, et plus à une personne, ou du moins à un robot intelligent. Les bots peuvent être utilisés pour faire passer des tâches répétitives et simples, comme la réservation de table ou le recueil d’informations de profil, vers des systèmes automatisés qui ne nécessitent plus d’intervention humaine directe. Les utilisateurs conversent avec le bot à l’aide de texte, de cartes interactives et avec la voix. Une interaction avec un bot peut se composer d’une question-réponse rapide, ou il peut s’agir d’une conversation plus sophistiquée fournissant un accès à des services de manière plus intelligente.

Les bots sont comparables à des applications web modernes. Ils résident sur Internet et utilisent des API pour envoyer et recevoir des messages. Le contenu d’un bot varie considérablement selon son type. Les logiciels de bots modernes s’appuient sur une pile de technologies et d’outils qui offrent des expériences de plus en plus complexes sur un large éventail de plateformes. Toutefois, un bot simple peut se contenter de recevoir un message et de répondre à l’utilisateur avec très peu de code. 

Les bots peuvent effectuer les mêmes opérations que d’autres types de logiciels : lire et écrire des fichiers, utiliser des bases de données et des API, et réaliser des tâches de calcul classiques. Ce qui rend les bots uniques, c’est leur utilisation de mécanismes généralement réservés à la communication entre humains. 

Azure Bot Service et Bot Framework offrent :
- Kit SDK Bot Framework pour le développement de bots
- Bot Framework Tools pour couvrir l’ensemble du workflow de développement de bot
- Bot Framework Service (BFS) pour l’envoi et la réception de messages et d’événements entre bots et canaux
- Déploiement de bot et configuration de canal dans Azure

Par ailleurs, les bots peuvent utiliser d’autres services Azure comme :
- Azure Cognitive Services pour créer des applications intelligentes 
- Stockage Azure comme solution de stockage cloud

## <a name="building-a-bot"></a>Création d’un bot 

Azure Bot Service et Bot Framework proposent un ensemble intégré d’outils et de services permettant de faciliter ce processus. Choisissez votre environnement de développement ou vos outils de ligne de commande favoris pour créer votre bot. Des kits de développement logiciel (SDK) existent pour C#, JavaScript et Typescript. (Les kits de développement logiciel pour Java et Python sont en cours de développement.) Nous proposons des outils pour différents stades de développement de bot, afin de vous aider à concevoir et créer des bots.

![Présentation des bots](media/bot-service-overview.png) 

### <a name="plan"></a>Planification
Comme avec n’importe quel type de logiciel, il est important pour le processus de création d’un bot réussi de disposer d’une compréhension approfondie des objectifs, des processus et des besoins des utilisateurs. Avant d’écrire votre code, consultez les [instructions relatives à la conception](bot-service-design-principles.md)  des bots, pour connaître les bonnes pratiques et déterminer les besoins de votre bot. Vous pouvez créer un bot simple ou inclure des fonctionnalités plus élaborées, telles que des fonctions vocales, la compréhension du langage naturel ou la capacité de répondre aux questions.

### <a name="build"></a>Créer
Votre bot est un service web qui implémente une interface de conversation et communique avec Bot Framework Service pour envoyer et recevoir des messages et des événements. Bot Framework Service est l’un des composants d’Azure Bot Service et de Bot Framework. Vous pouvez créer des bots dans divers environnements et langages. Vous pouvez commencer le développement de votre bot dans le [portail Azure](bot-service-quickstart.md), ou utiliser des modèles [[C#](dotnet/bot-builder-dotnet-sdk-quickstart.md) | [JavaScript](javascript/bot-builder-javascript-quickstart.md)] pour un développement local.

Dans le cadre d’Azure Bot Service et de Bot Framework, nous proposons des composants supplémentaires que vous pouvez utiliser pour étendre les fonctionnalités de votre bot :

| Fonctionnalité | Description | Lien |
| --- | --- | --- |
| Ajouter un traitement en langage naturel | Activez votre bot pour la compréhension du langage naturel, la détection des fautes d’orthographe, l’utilisation de la voix et la reconnaissance de l’intention de l’utilisateur | Utilisation de [LUIS](~/v4sdk/bot-builder-howto-v4-luis.md) 
| Répondre aux questions | Ajoutez une base de connaissances pour répondre aux questions des utilisateurs de manière plus naturelle et conversationnelle | Utilisation de [QnA Maker](~/v4sdk/bot-builder-howto-qna.md) 
| Gérer plusieurs modèles | Si vous utilisez plusieurs modèles, tels que LUIS et QnA Maker, déterminez de manière judicieuse le modèle qu’il convient d’utiliser au cours de la conversation avec votre bot | Outil [Dispatch](~/v4sdk/bot-builder-tutorial-dispatch.md)|
| Ajouter des cartes et des boutons | Améliorez l’expérience utilisateur à l’aide de médias autres que du texte, tels que des graphiques, des menus et des cartes | [Ajout de cartes](v4sdk/bot-builder-howto-add-media-attachments.md) |

> [!NOTE]
> Le tableau ci-dessus n’est pas exhaustif. Pour plus d’informations sur les fonctionnalités des bots, explorez les articles sur la gauche, en commençant par [Envoi de messages](~/v4sdk/bot-builder-howto-send-messages.md).

En outre, nous fournissons des outils en ligne de commande pour vous aider à créer, gérer et tester les ressources de bot. Ces outils peuvent configurer des applications LUIS, créer une base de connaissances QnA, créer des modèles à répartir entre les composants, simuler une conversation, etc. Vous trouverez plus d’informations dans le fichier [readme](https://aka.ms/botbuilder-tools-readme) des outils en ligne de commande.

Vous avez également accès à un large éventail d’[exemples](https://github.com/microsoft/botbuilder-samples) qui présentent un bon nombre de fonctionnalités disponibles avec le Kit de développement logiciel (SDK). Ils sont intéressants pour les développeurs qui souhaitent démarrer avec un plus grand ensemble de fonctionnalités.

### <a name="test"></a>Test 
Les bots sont des applications complexes composées de nombreux éléments différents qui fonctionnent ensemble. Comme pour toutes les applications complexes, des bogues intéressants ou des comportements inattendus peuvent se produire. Avant de le publier, testez votre bot. Nous proposons plusieurs moyens de tester les bots avant leur publication :

- Testez votre bot en local avec l’[émulateur](bot-service-debug-emulator.md). Bot Framework Emulator est une application autonome qui fournit non seulement une interface de conversation, mais également des outils de débogage et d’interrogation qui permettent de comprendre le comportement de votre bot.  L’émulateur peut être exécuté localement en même temps que votre application de bot en développement. 
 
- Testez votre bot sur le [Web](bot-service-manage-test-webchat.md). Une fois configuré via le portail Azure, votre bot peut également être accessible via une interface de conversation web. L’interface de conversation web constitue un excellent moyen d’accorder l’accès à votre bot à des testeurs et à d’autres personnes ne disposant pas d’un accès direct au code d’exécution du bot.

- Procédez à un [Test unitaire](https://docs.microsoft.com/azure/bot-service/unit-test-bots?view=azure-bot-service-4.0) de votre bot avec la mise à jour de juillet du kit SDK de Bot Framework.

### <a name="publish"></a>Publish 
Lorsque vous êtes prêt à rendre votre bot accessible sur le Web, publiez-le dans [Azure](bot-builder-howto-deploy-azure.md) ou dans votre propre centre de données ou service web. La possession d’une adresse sur l’Internet public est la première étape pour que votre bot prenne vie sur votre site, ou dans des canaux de conversation.

### <a name="connect"></a>Connecter          
Connectez votre bot aux canaux tels que Facebook, Messenger, Kik, Skype, Slack, Microsoft Teams, Telegram, SMS, Twilio, Cortana et Skype. Bot Framework effectue la plupart des opérations nécessaires pour envoyer et recevoir des messages de l’ensemble de ces différentes plateformes : l’application de votre bot reçoit un flux unifié et normalisé de messages, quels que soient le nombre et le type de canaux auxquels il est connecté. Pour plus d’informations sur l’ajout de canaux, consultez la rubrique [Canaux](bot-service-manage-channels.md).

### <a name="evaluate"></a>Évaluer 
Utilisez les données collectées dans le portail Azure pour identifier les opportunités d’améliorer les fonctionnalités et les performances de votre bot. Vous pouvez obtenir des données relatives au service et à l’instrumentation, comme le trafic, la latence et les intégrations. L’outil d’analyse permet également de créer des rapports de conversation à partir des données sur les utilisateurs, les messages et les canaux. Pour plus d’informations, consultez la rubrique expliquant [comment collecter des données d’analyse](bot-service-manage-analytics.md).


## <a name="next-steps"></a>Étapes suivantes
Découvrez des [études de cas](https://azure.microsoft.com/services/bot-service/) sur des bots ou cliquez sur le lien ci-dessous pour créer un bot.
> [!div class="nextstepaction"]
> [Créer un robot](bot-service-quickstart.md)
