---
title: Language Understanding | Microsoft Docs
description: Découvrez comment ajouter l’intelligence artificielle à vos bots avec Microsoft Cognitive Services, pour les rendre plus utiles et plus attrayants.
keywords: LUIS, intention, module de reconnaissance, outil dispatch, qna, qna maker
author: ivorb
ms.author: kamrani
manager: rstand
ms.topic: article
ms.service: bot-service
ms.date: 09/19/2018
ms.reviewer: ''
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 83d6a4b43cdd3132730ee78079260d2a2d79b228
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299485"
---
# <a name="language-understanding"></a>Language Understanding

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Les bots peuvent utiliser divers styles de conversation : structurée et guidée, jusqu’à forme libre et à durée indéterminée. Un bot doit déterminer la prochaine étape de son flux de conversation, selon ce que l’utilisateur a dit, et une conversation ouverte offre un plus large éventail de réponses d’utilisateur.

| Guidée | Ouverts |
|------|------|
| Je suis le bot de voyage. Sélectionnez une des options suivantes : rechercher des vols, rechercher des hôtels, rechercher une voiture de location. | Je vais vous aider à réserver votre voyage. Que voulez-vous faire ? |
| Avez-vous besoin d’autre chose ? Cliquez sur Oui ou Non. | Avez-vous besoin d’autre chose ? |

Souvent, les interactions entre les utilisateurs et les bots ne sont pas codifiées et les bots doivent comprendre le langage naturellement, en s’appuyant sur le contexte. Cet article explique comment Language Understanding (LUIS) vous permet de déterminer ce que souhaitent les utilisateurs, et d’identifier les concepts et les entités de différentes phrases, pour que les bots puissent répondre avec l’action appropriée.

## <a name="recognize-intent"></a>Reconnaître une intention

[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/home) vous aide en déterminant l’**intention** de l’utilisateur, c’est-à-dire ce qu’il veut faire, à partir de ce qu’il vous dit, pour permettre à votre bot de réagir de façon appropriée. LUIS est particulièrement utile lorsque ce que l’utilisateur dit à votre bot ne suit pas une structure prévisible ou un modèle spécifique. Si un bot dispose d’une interface utilisateur de conversation permettant à l’utilisateur d’énoncer ou de saisir une réponse, il peut y avoir des variations infinies d’*énoncés* (l’entrée énoncée ou textuelle de l’utilisateur).

Par exemple, considérez les nombreuses manières dont l’utilisateur d’un bot peut demander la réservation d’un vo²l.

![Différentes formes d’énoncés pour la réservation d’un vol](media/cognitive-services-add-bot-language/cognitive-services-luis-utterances.png)

Ces énoncés peuvent contenir différentes structures et avoir différents synonymes pour le terme « vol », auxquels vous n’avez pas pensé. Dans votre bot, il peut être difficile d’écrire la logique qui correspond à tous les énoncés et les différencie toujours des autres intentions contenant ces mêmes termes. En outre, votre bot doit extraire des *entités*, qui représentent d’autres termes importants comme des emplacements et des heures. LUIS facilite ce processus en identifiant pour vous les intentions et les entités en fonction du contexte.

Lorsque vous concevez votre bot pour l’entrée de langage naturel, vous déterminez les intentions et les entités que votre bot doit reconnaître, et comment ces éléments seront liés aux actions effectuées par votre bot. Dans [luis.ai](https://www.luis.ai), vous définissez des intentions et des entités personnalisées, puis vous spécifiez leur comportement en fournissant des exemples pour chaque intention et en étiquetant les entités qu’elles contiennent.

Votre bot utilise l’intention reconnue par LUIS pour déterminer le sujet de conversation ou pour lancer un flux de conversation. Par exemple, lorsqu’un utilisateur prononce « Je souhaiterais réserver un vol », votre bot détecte l’intention BookFlight et appelle le flux de conversation afin de lancer une recherche des vols. LUIS détecte des entités telles que la ville de destination et la date de départ, que ce soir dans l’énoncé d’origine qui déclenche l’intention ou ultérieurement dans le flux de la conversation. Une fois que le bot dispose de toutes les informations requises, il peut exécuter l’intention de l’utilisateur.

![Une conversation avec un bot est déclenchée par l’intention BookFlight](media/cognitive-services-add-bot-language/cognitive-services-luis-conversation-high-level.png)

### <a name="recognize-intent-in-common-scenarios"></a>Reconnaître une intention dans des scénarios courants

Pour accélérer le développement, LUIS fournit des modèles de langage préformés qui reconnaissent les énoncés courants pour les principales catégories de bots. 

Les **domaines prédéfinis** sont des collections préformées et prêtes à l’emploi d’intentions et d’entités qui fonctionnent bien ensemble pour des scénarios courants tels que les rendez-vous, les rappels, la gestion, le fitness, le divertissement, les communications, les réservations, etc. Le domaine prédéfini **Utilities** aide votre bot à gérer les tâches courantes comme Annuler, Confirmer, Aide, Répéter et Arrêter. Observez les [domaines prédéfinis](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-use-prebuilt-domains) proposés par LUIS.

Les **entités prédéfinies** aide votre bot à reconnaître les types d’informations courants comme les dates, les heures, les nombres, la température, la devise, la situation géographie et l’âge. Consultez [Entités prédéfinies pour reconnaître les types de données courants](https://docs.microsoft.com/azure/cognitive-services/LUIS/pre-builtentities) pour plus d’informations sur les types que LUIS peut reconnaître.

## <a name="how-your-bot-gets-messages-from-luis"></a>Comment votre bot obtient des messages à partir de LUIS

Une fois que vous avez configuré et connecté LUIS, votre bot peut envoyer le message à votre application LUIS, qui renvoie une réponse JSON contenant les intentions et les entités. Vous pouvez ensuite utiliser le [contexte de tour](~/v4sdk/bot-builder-basics.md#defining-a-turn) du _gestionnaire de tours_ de votre bot pour acheminer le flux de conversation en fonction de l’intention dans la réponse LUIS. 

![Comment les intentions et les entités sont transmises à votre bot](./media/cognitive-services-add-bot-language/cognitive-services-luis-message-flow-bot-code.png)

Pour commencer à utiliser une application LUIS avec votre bot, consultez [Utilisation de LUIS pour la compréhension langagière](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-luis?view=azure-bot-service-4.0).

## <a name="best-practices-for-language-understanding"></a>Meilleures pratiques pour la reconnaissance vocale

Tenez compte des pratiques suivantes lorsque vous concevez un modèle de langage pour votre bot.

### <a name="consider-the-number-of-intents"></a>Prendre en compte le nombre d’intentions

Les applications LUIS reconnaissent une intention en classant un énoncé dans une des différentes catégories disponibles. La détermination de la catégorie appropriée parmi un grand nombre d’intentions peut réduire la capacité d’une application LUIS à distinguer ces intentions.

Une façon de réduire le nombre d’intentions consiste à utiliser un modèle hiérarchique. Prenons le cas d’un bot de type assistant personnel comportant trois intentions liées à la météo, trois intentions liées à la domotique, et trois autres intentions d’utilitaire : Help (aide), Cancel (annuler) et Greeting (saluer). Si vous placez toutes les intentions dans la même application LUIS, vous en obtenez déjà 9, et lorsque vous ajoutez des fonctionnalités au bot, vous pouvez vous retrouver avec plusieurs dizaines. Au lieu de cela, vous pouvez utiliser une application LUIS de type répartiteur pour déterminer si la requête de l’utilisateur concerne la météo, la domotique ou un utilitaire, puis appeler l’application LUIS pour la catégorie déterminée par le répartiteur. Dans ce cas, chacune des applications LUIS démarre uniquement avec 3 intentions.

### <a name="use-a-none-intent"></a>Utiliser une intention None

Souvent, les utilisateurs de votre bot formuleront une requête inattendue ou non liée au flux de conversation en cours. L’intention _None_ (aucune) permet de gérer ces messages.

Si vous ne formez pas une intention pour gérer les cas liés aux messages de secours, par défaut ou de type « aucun des éléments ci-dessus », votre application LUIS peut uniquement classer les messages selon les intentions qu’il a définies. Par exemple, supposons que votre application LUIS comporte deux intentions : `HomeAutomation.TurnOn` et `HomeAutomation.TurnOff`. Si ce sont les seules intentions et que l’entrée n’est pas liée, par exemple « planifier un rendez-vous vendredi », votre application LUIS n’a pas d’autre choix que de classer ce message comme HomeAutomation.TurnOn ou HomeAutomation.TurnOff. Si votre application LUIS contient une intention `None` et quelques exemples, vous pouvez intégrer une logique de secours à votre bot pour gérer les énoncés inattendus.

L’intention `None` se révèle d’une grande utilité pour améliorer les résultats de la reconnaissance. Dans ce scénario d’application domotique, l’énoncé « planifier un rendez-vous vendredi » peut produire l’intention `HomeAutomation.TurnOn` avec un faible niveau de confiance, et votre bot doit donc le rejeter. Vous pouvez ajouter des expressions de ce type à votre modèle sous l’intention `None`, afin qu’elles se résolvent correctement en `None`.

### <a name="review-the-utterances-that-luis-app-receives"></a>Passer en revue les énoncés reçus par les applications LUIS

Les applications LUIS intègrent une fonctionnalité permettant d’améliorer les performances de votre application, en examinant les messages que les utilisateurs lui ont envoyés. Consultez [Réviser les énoncés de point de terminaison](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances) pour obtenir une procédure pas à pas.


## <a name="integrate-multiple-luis-apps-and-qna-services-with-the-dispatch-tool"></a>Intégrer plusieurs applications LUIS et services QnA à l’outil Dispatch

Lorsque vous créez un bot polyvalent qui comprend plusieurs sujets de conversation, vous pouvez commencer à développer des services pour chaque fonction séparément, avant de les intégrer. Ces services peuvent inclure des applications de reconnaissance vocale (LUIS) et les services QnAMaker. Voici quelques exemples de scénarios dans lesquels un bot peut combiner plusieurs applications LUIS, plusieurs services QnAMaker ou une combinaison des deux :

* Un bot de type assistant personnel permet à l’utilisateur d’appeler diverses commandes. Chaque catégorie de commandes forme une « compétence » qui peut être développée séparément, et chaque compétence contient une application LUIS.
* Un bot recherche les réponses aux questions fréquentes (FAQ) dans de nombreuses bases de connaissances.
* Un bot d’une entreprise dispose d’applications LUIS pour créer des comptes clients, passer des commandes, et comporte également un service QnA Maker pour sa rubrique FAQ.  

### <a name="the-dispatch-tool"></a>Outil Dispatch

L’outil Dispatch vous permet d’intégrer plusieurs applications LUIS et services QnA Maker à votre bot, en créant une *application de répartition*, c’est-à-dire une nouvelle application LUIS qui achemine les messages vers les services LUIS et QnA Maker appropriés. Consultez le [didacticiel dédié à l’outil Dispatch](./bot-builder-tutorial-dispatch.md) pour apprendre à combiner plusieurs applications LUIS et une fonction QnA Maker dans un bot.

## <a name="use-luis-to-improve-speech-recognition"></a>Utiliser LUIS pour améliorer la reconnaissance vocale

Dans le cas d’un bot avec lequel les utilisateurs dialogueront, l’intégration avec LUIS permet à votre bot d’identifier les mots qui peuvent être mal compris lorsqu’il convertit la parole en texte.  Par exemple, dans un scénario de jeu d’échecs, un utilisateur peut dire : « Déplacer cavalier en A7 ». Sans contexte concernant l’intention de l’utilisateur, l’énoncé peut être interprété comme : « Déplacer cavalier en ascète ». En créant des entités qui représentent les pièces d’échecs et leurs positions, puis en les étiquetant dans des énoncés, vous fournissez au bot le contexte de reconnaissance vocale qui l’aidera à identifier ces termes. Vous pouvez [préparer la reconnaissance vocale](https://docs.microsoft.com/azure/bot-service/bot-service-manage-speech-priming?view=azure-bot-service-4.0) à l’aide de canaux Bot Framework intégrés à l’API Reconnaissance vocale Bing, par exemple Web Chat, l’émulateur Bot Framework et Cortana.  

## <a name="additional-resources"></a>Ressources supplémentaires
Pour plus d’informations, consultez la documentation dédiée à [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).
