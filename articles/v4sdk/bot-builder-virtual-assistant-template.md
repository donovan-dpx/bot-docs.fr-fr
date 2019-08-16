---
title: Vue d’ensemble du modèle d’assistant virtuel | Microsoft Docs
description: Découvrir le modèle d’assistant virtuel
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6bf567729e0c4799672f773ddcfadb4fabfa36fc
ms.sourcegitcommit: 7b3d2b5b9b8ce77887a9e6124a347ad798a139ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991954"
---
# <a name="virtual-assistant---template-outline"></a>Assistant virtuel  : structure du modèle

> [!NOTE]
> Cet article s’applique à la version v4 du Kit de développement logiciel (SDK). 

Le modèle d’assistant virtuel permet de rassembler un certain nombre de bonnes pratiques que nous avons identifiées lors de la création d’expériences conversationnelles. Il automatise aussi l’intégration des composants, ce qui est particulièrement avantageux pour les développeurs Bot Framework. Dans cette section, vous découvrirez dans quel contexte les décisions clés ont été prises afin de mieux comprendre le fonctionnement du modèle.

Fonctionnalité      | Description |
------------ | -------------
Introduction | Message de présentation avec une [carte adaptative]() au début de la conversation
Indicateurs de saisie  | Indicateurs de saisie visuels automatisés durant les conversations et répétition pour les opérations de longue durée
Modèle LUIS de base  | Prise en charge des intentions courantes comme **Annuler**, **Aide**, **Escalader**, etc.
Dialogues de base | Flux de dialogues pour capturer les informations utilisateur de base ainsi que la logique d’interruption pour les intentions Annuler et Aide
Réponses de base  | Réponses par reconnaissance et synthèse vocales pour les intentions et dialogues de base
Forum Aux Questions | Intégration à [QnA Maker](https://www.qnamaker.ai) pour répondre aux questions générales à partir d’une base de connaissances 
Échange de conversation (chit-chat) | Modèle d’échange de conversation professionnel pour fournir des réponses standard aux demandes courantes ([en savoir plus](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/chit-chat-knowledge-base))
Répartiteur | Modèle [Dispatch](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) intégré pour déterminer si un énoncé donné doit être traité par LUIS ou QnA Maker
Support multilingue | Disponible en anglais, français, italien, allemand, espagnol et chinois
Transcriptions | Transcriptions de toutes les conversations enregistrées dans Stockage Azure
Télémétrie  | Intégration à [Application Insights](https://azure.microsoft.com/services/application-insights/) pour collecter des données de télémétrie sur toutes les conversations
Analytics | Exemple de tableau de bord Power BI pour vous aider à démarrer avec les insights dans vos expériences de conversation
Déploiement automatisé | Déploiement facile de tous les services mentionnés ci-dessus à l’aide des modèles Azure ARM

## <a name="introduction-card"></a>Carte de présentation

De nombreuses expériences conversationnelles font face à un problème de taille : les utilisateurs finaux ne savent pas comment commencer la discussion et posent des questions d’ordre général. De son côté, le bot n’est pas le mieux placé pour répondre à ce type de questions. La première impression est importante ! Avec une carte de présentation, le bot a la possibilité de présenter ses fonctionnalités à l’utilisateur final et il lui suggère quelques questions dont l’utilisateur peut se servir pour lancer la conversation. C’est également un excellent moyen de faire apparaître la personnalité de votre bot.

Une simple carte de présentation est fournie en standard, que vous pouvez adapter en fonction des besoins ; une carte utilisateur récurrente est affichée à l’occasion des interactions suivantes quand un utilisateur a terminé le dialogue d’intégration (déclenché par le bouton de prise en main sur la carte de présentation)

![Exemple de carte de présentation](./media/enterprise-template/vabotintrocard.png)

## <a name="basic-language-understanding-luis-intents"></a>Intentions Language Understanding (LUIS) de base

Chaque bot doit disposer d’un niveau de base pour la compréhension du langage conversationnel. Par exemple, tous les bots doivent pouvoir traiter les messages d’accueil sans difficulté. En règle générale, les développeurs ont besoin de créer ces intentions de base et de fournir des données d’apprentissage initiales pour commencer. Le modèle d’assistant virtuel contient des exemples de fichiers .lu pour vous aider à démarrer. Ainsi, vous n’avez pas besoin de les créer pour chaque projet et vous avez l’assurance de disposer, par défaut, d’un niveau de fonctionnalité de base.

Les fichiers .lu fournissent les intentions suivantes en allemand, anglais, chinois, espagnol, français et italien.

Intention       | Exemples d’énoncés |
-------------|-------------|
Annuler       |*cancel*, *nevermind*|
Escalader     |*puis-je parler à quelqu’un ?*|
FinishTask   |*terminé*, *tout fait*|
GoBack       |*retour*|
Aide         |*pouvez-vous m’aider ?*|
Répéter       |*pouvez-vous répéter ?*|
SelectAny    |*n’importe lequel*|
SelectItem   |*le premier*|
SelectNone   |*aucun*|
ShowNext     |*afficher plus*|
ShowPrevious |*afficher le précédent*|
StartOver    |*restart*|
Arrêter         |*stop*|

Le format [.lu](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) ressemble au format Markdown. Par conséquent, la modification et le contrôle de la source sont facilités. L’outil [LuDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) est ensuite utilisé pour convertir les fichiers .lu dans les modèles LUIS. Vous pouvez ensuite publier ces modèles dans votre abonnement LUIS via le portail ou l’outil de ligne de commande [LUIS](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) associé.

## <a name="telemetry"></a>Télémétrie

Fournir des insights sur l’engagement des utilisateurs de votre bot s’est révélé être un avantage de taille. Ces insights peuvent vous aider à comprendre les niveaux d’engagement utilisateur, les fonctionnalités du bot qui sont sollicitées (intentions), ainsi que les questions auxquelles le bot ne peut pas répondre. Vous identifiez ainsi les lacunes du bot et pouvez les combler à l’aide de nouveaux articles QnA Maker par exemple.

L’intégration d’Application Insights apporte des informations opérationnelles et techniques importantes de manière prédéfinie. Cette fonctionnalité peut également servir à capturer des événements spécifiques associés au bot (des messages envoyés et reçus avec les opérations LUIS et QnA Maker).

Intrinsèquement, le niveau de télémétrie du bot est lié aux données de télémétrie techniques et opérationnelles, ce qui vous permet d’examiner la façon dont est traitée la question d’un utilisateur et vice versa.

Un composant de middleware associé à une classe wrapper autour des classes SDK QnA Maker et LuisRecognizer offre un bon moyen de collecter un ensemble cohérent d’événements. Ces événements cohérents peuvent ensuite être utilisés par les outils Application Insights et des outils comme Power BI.

Un exemple de tableau de bord Power BI est fourni dans le cadre du dépôt github des solutions Bot Framework et peut être exploité immédiatement avec chaque modèle d’assistant virtuel. Consultez la section [Analytics](https://aka.ms/bfsanalytics) pour plus d’informations.

![Exemple de données analytiques](./media/enterprise-template/powerbi-conversationanalytics-luisintents.png)

## <a name="dispatcher"></a>Répartiteur

Pour obtenir un bon rendu lors de la première vague d’expériences conversationnelles, le modèle de conception clé tire parti de Language Understanding (LUIS) et de QnA Maker. LUIS est entraîné avec des tâches que votre bot peut effectuer pour un utilisateur final et QnA Maker est entraîné avec des connaissances plus générales.

Tous les énoncés entrants (questions) sont acheminés vers LUIS pour l’analyse. Si l’intention d’un énoncé donné n’a pas été identifiée, ce dernier ne porte aucune intention (*None*). Ensuite, QnA Maker est utilisé pour tenter de trouver une réponse à donner à l’utilisateur final.

Ce schéma fonctionne bien. Toutefois, il rencontre des problèmes dans deux scénarios principaux.

- Parfois, les énoncés du modèle LUIS et de QnA Maker se recoupent légèrement, ce qui peut donner un comportement étrange où LUIS essaie de traiter une question qui aurait dû être dirigée vers QnA Maker.
- Quand deux modèles LUIS ou plus coexistent, le bot doit appeler chacun d’eux et effectuer une comparaison pour évaluer les intentions afin de déterminer où envoyer tel ou tel énoncé. Comme il n’existe aucune comparaison commune des scores de base entre les modèles, ce schéma ne fonctionne pas efficacement et offre une expérience utilisateur de qualité médiocre.

[Dispatch](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csaddref%2Ccsbotconfig) remédie efficacement à ce problème en extrayant des énoncés de chaque modèle LUIS configuré et des questions de QnA Maker, puis en créant un modèle LUIS de répartition centrale.

Ainsi, le bot peut rapidement identifier le modèle LUIS ou le composant qui doit gérer un énoncé spécifique et garantit que les données QnA Maker sont placées au plus haut niveau d’intention, pas seulement une intention neutre (*None*) comme avant.

Cet outil de répartition offre également une fonctionnalité d’évaluation qui met en évidence les confusions, les problèmes et les recoupements entre les modèles LUIS et les bases de connaissances QnA Maker avant le déploiement.

Le répartiteur est utilisé au cœur de chaque projet créé à l’aide du modèle. Le modèle de répartition est utilisé dans la classe `MainDialog` pour déterminer si la cible correspond à un modèle LUIS ou QnA. Pour LUIS, le modèle LUIS secondaire est appelé et renvoie les intentions et les entités. Le répartiteur est également utilisé pour la détection d’interruption.

![Exemple de répartition](./media/enterprise-template/dispatchexample.png)

## <a name="qna-maker"></a>QnA Maker

[QnA Maker](https://www.qnamaker.ai/) offre la possibilité aux non-développeurs d’organiser des connaissances générales sous la forme de paires de questions-réponses. Ces connaissances peuvent être importées depuis des sources de données (FAQ, manuels), mais aussi de manière interactive au sein du portail QnaMaker.

Deux exemples de modèles QnA Maker sont fournis au format de fichier [.lu](https://github.com/Microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) dans le dossier QnA de CognitiveModels, un pour les FAQ et un pour les échanges. [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) est ensuite utilisé dans le cadre du script de déploiement pour créer un fichier JSON QnA Maker, dont se servira par la suite l’outil de ligne de commande [QnA Maker](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/QnAMaker) pour publier des éléments dans la base de connaissances QnA Maker.

![Exemple de conversation (chit-chat) QnA](./media/enterprise-template/qnachitchatexample.png)

## <a name="content-moderator"></a>Content Moderator

Content Moderator est un composant facultatif qui permet de détecter l’utilisation potentielle d’injures ainsi que la présence d’informations d’identification personnelle (PII). Par exemple, un bot peut s’excuser et passer la main à un humain en cas d’injures ou ne pas stocker les données de télémétrie s’il a détecté des informations d’identification personnelle.

Grâce au composant d’intergiciel fourni, les textes sont filtrés et analysés par le biais de ```TextModeratorResult``` sur l’objet TurnState.

## <a name="next-steps"></a>Étapes suivantes
Reportez-vous aux [tutoriels](https://aka.ms/bfstutorials) pour savoir comment créer et déployer votre assistant virtuel. 

## <a name="additional-resources"></a>Ressources supplémentaires
Le code source complet du modèle d’assistant virtuel est disponible sur [GitHub](https://aka.ms/bfsolutions).

