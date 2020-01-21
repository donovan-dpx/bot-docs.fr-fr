---
title: Instructions relatives au débogage - Bot Service
description: Découvrez comment déboguer votre robot.
keywords: robots de débogage, débogage botframework
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 07/17/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: d10f60bb4291cbd53eb526423e5bdcd711afa8f2
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791181"
---
# <a name="debugging-guidelines"></a>Instructions relatives au débogage

[!INCLUDE[applies-to](../includes/applies-to.md)]

Les bots sont des applications complexes composées de nombreux éléments différents qui fonctionnent ensemble. Comme pour toutes les applications complexes, des bogues intéressants ou des comportements inattendus peuvent se produire.

Il peut parfois s’avérer difficile de déboguer votre robot. Chaque développeur effectue cette tâche comme il le souhaite. Les conseils que nous vous présentons ci-dessous sont des recommandations d’utilisation qui s’appliquent à la plupart des robots.

Après avoir vérifié que votre robot semble fonctionner comme prévu, l’étape suivante consiste à le connecter à un canal. Pour ce faire, vous pouvez déployer votre bot sur un serveur intermédiaire et créer votre propre client DirectLine sur lequel votre bot peut se connecter.
<!--IBTODO [Direct Line client](bot-builder-howto-direct-line.md)-->

La création de votre propre client vous permet de définir le fonctionnement interne du canal et de tester de façon précise la réponse de votre robot à certains échanges d’activités. Une fois que la connexion à votre client est établie, exécutez vos tests pour configurer l’état de votre robot et vérifier vos fonctionnalités. Si votre robot utilise une fonctionnalité comme des données vocales, il est possible de la vérifier à l’aide de ces canaux.

L’utilisation de l’émulateur et de la discussion en ligne via le portail Azure permet de mieux comprendre le fonctionnement de votre robot lorsqu’il interagit avec différents canaux.

Le débogage de votre robot fonctionne de la même manière que les autres applications multithreads en permettant de définir des points d’arrêt ou d’utiliser des fonctions telles que la fenêtre immédiate. 

Les robots suivent un paradigme de programmation par événement qui peut être difficile à rationaliser si vous ne le connaissez pas bien. Des bogues inattendus peuvent se produire lorsque votre robot est sans état, en multithreads et qu’il traite des appels async/await. Bien que le débogage de votre robot fonctionne de la même manière que les autres applications multithreads, nous vous proposerons quelques recommandations, outils et ressources pour vous aider.

## <a name="understanding-bot-activities-with-the-emulator"></a>Comprendre les activités du bot à l’aide de l’émulateur

Votre robot traite différents types [d’activités](bot-builder-basics.md#the-activity-processing-stack)en plus de l’activité normale de _message_. L’[émulateur](../bot-service-debug-emulator.md) vous permettra de savoir quelles sont ces activités, quand elles se produisent et quelles sont les informations qu’elles contiennent. Le fait de comprendre ces activités vous aidera à coder votre robot de façon efficace et vous permettra de vérifier que les activités envoyées et reçues par votre robot correspondent bien à ce qu’elles devraient être.

## <a name="saving-and-retrieving-user-interactions-with-transcripts"></a>Enregistrer et récupérer des interactions utilisateur avec les transcriptions

Le stockage de transcriptions blob Azure fournit une ressource spécialisée dans laquelle vous pouvez à la fois [stocker et récupérer des transcriptions](bot-builder-howto-v4-storage.md) qui contiennent des interactions entre vos utilisateurs et votre bot.  

De plus, une fois que les interactions d’entrée de l’utilisateur ont été stockées, vous pouvez utiliser l’_explorateur du stockage_ Azure pour afficher manuellement les données contenues dans les transcriptions qui sont stockées dans votre magasin d’objets blob. L’exemple suivant ouvre l’_explorateur du stockage_ à partir des paramètres pour _mynewtestblobstorage_. Pour ouvrir une entrée utilisateur enregistrée, sélectionnez :    Conteneur d’objets Blob > ChannelId > TranscriptId > ConversationId

![Examine_stored_transcript_text](./media/examine_transcript_text_in_azure.png)

Cette opération ouvre l’entrée de la conversation utilisateur stockée au format JSON. L’entrée de l’utilisateur est conservée avec la clé _texte_.

## <a name="how-middleware-works"></a>Fonctionnement du middleware

L’[intergiciel](bot-builder-concept-middleware.md) peut ne pas être intuitif lors de la première tentative d’utilisation, en particulier en ce qui concerne la poursuite ou le court-circuitage de l’exécution. L’intergiciel peut s’exécuter en début ou en fin de tour en appelant le délégué `next()` qui détermine le moment où l’exécution est transmise à la logique du robot. 

Si vous utilisez plusieurs intergiciels, le délégué peut transmettre l’exécution à un autre intergiciel si votre pipeline est orienté de la sorte. Des détails sur [le pipeline de l’intergiciel du robot](bot-builder-concept-middleware.md#the-bot-middleware-pipeline) permettent de clarifier cette idée.

Lorsque le délégué `next()` n’est pas appelé, il s’agit d’un [routage en court-circuit ](bot-builder-concept-middleware.md#short-circuiting). Cette situation se produit lorsque l’intergiciel répond à l’activité en cours et détermine qu’il n’est pas nécessaire de transmettre l’exécution. 

Le fait de comprendre quand et pourquoi l’intergiciel effectue des courts-circuits permet de savoir quelle partie de l’intergiciel doit se trouver en premier dans votre pipeline. De plus, il est particulièrement important de savoir à quoi s’attendre avec l’intergiciel intégré fourni par le kit de développement logiciel ou d’autres développeurs. Certaines personnes trouvent utile de créer leur propre intergiciel afin de gagner en pratique avant de se lancer avec l’intergiciel intégré.

<!-- Snip: QnA was once implemented as middleware.
For example [QnA maker](bot-builder-howto-qna.md) is designed to handle certain interactions and short-circuit the pipeline when it does, which can be confusing when first learning how to use it.
-->

## <a name="understanding-state"></a>Comprendre l’état

La conservation de la trace de l’état est une partie importante de votre robot, en particulier pour les tâches complexes. En règle générale, la bonne pratique consiste à traiter les activités le plus rapidement possible et à laisser le traitement se terminer afin de conserver l’état. Les activités peuvent être envoyées à votre robot presque en même temps, ce qui peut provoquer des bogues très déroutants en raison de l’architecture asynchrone.

Plus important encore, assurez-vous que la conservation de l’état correspond à vos attentes. Selon l’endroit où se trouve votre état conservé, les émulateurs de stockage pour [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator)et [Azure Table Storage](https://docs.microsoft.com/azure/storage/common/storage-use-emulator) vous permettent de le vérifier avant d’utiliser le stockage en production.

## <a name="how-to-use-activity-handlers"></a>Comment utiliser les gestionnaires d’activités

Les gestionnaires d’activités peuvent ajouter une couche de complexité supplémentaire, notamment parce que chaque activité s’exécute sur un thread indépendant (ou web worker selon le langage utilisé). Selon les activités de vos gestionnaires, des problèmes peuvent se produire si l’état actuel ne correspond pas à l’état attendu.

L’état intégré est enregistré en fin de tour, mais toutes les activités générées par ce tour sont exécutées indépendamment de son pipeline. Cela ne nous affecte généralement pas, mais si un gestionnaire d’activité change d’état, nous avons besoin de l’état enregistré pour maîtriser cette modification. Dans ce cas, le pipeline du tour peut rester en attente sur l’activité pour que le traitement se termine avant la fin de façon à s’assurer qu’il enregistre le bon état pour ce tour.

La méthode _send activity_ et ses gestionnaires posent un problème unique. Le simple fait d’appeler _send activity_ à partir d’un gestionnaire _on send activities_ provoque une duplication infinie des threads. Il est possible de contourner ce problème, notamment en ajoutant des messages aux données sortantes ou en les écrivant dans un autre emplacement, tel que la console ou un fichier, afin d’éviter que votre bot ne plante.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment procéder à un test unitaire de robots](unit-test-bots.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Débogage dans Visual Studio](https://docs.microsoft.com/visualstudio/debugger/index)
* [Débogage, traçage et profilage](https://docs.microsoft.com/dotnet/framework/debug-trace-profile/) de l’infrastructure du robot
* Utilisez l’attribut [ConditionalAttribute](https://docs.microsoft.com/dotnet/api/system.diagnostics.conditionalattribute?view=netcore-2.0) pour les méthodes que vous ne souhaitez pas inclure dans le code de production
* Utilisez des outils comme [Fiddler](https://www.telerik.com/fiddler) pour afficher le trafic réseau
* [Résoudre les problèmes généraux](../bot-service-troubleshoot-bot-configuration.md) ainsi que les autres articles de résolution des problèmes de cette section.
