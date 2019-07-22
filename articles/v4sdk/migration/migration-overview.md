---
title: Vue d’ensemble de la migration du kit SDK Bot Framework | Microsoft Docs
description: Propose une vue d’ensemble des changements apportés au kit SDK et indique comment passer de v3 à v4.
keywords: migration de bot
author: mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 06/11/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: b1082e16933da1fb4c20f51d4764ec1774aabdb6
ms.sourcegitcommit: 4f78e68507fa3594971bfcbb13231c5bfd2ba555
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68292189"
---
# <a name="migration-overview"></a>Vue d’ensemble de la migration

Le kit SDK Bot Framework v4 s’appuie sur l’expérience d’apprentissage et les commentaires des clients issus des kits SDK précédents. La nouvelle version introduit les niveaux d’abstraction appropriés et assure une architecture flexible des composants de bot. Vous pouvez ainsi, par exemple, créer un bot simple qui deviendra de plus en plus sophistiqué avec la modularité et l’extensibilité du kit SDK Bot Framework v4.

> [!NOTE]
> Le kit SDK Bot Framework v4 s’efforce de conserver le caractère simple des opérations simples et de rendre possibles celles qui sont complexes.

Une approche ouverte a été adoptée de sorte que le kit SDK Bot Framework v4 a été généré avec la coopération de la communauté. Quand vous envoyez une demande de tirage (pull request) pour la première fois, un contrat CLA [Contributor License Agreement](https://cla.opensource.microsoft.com/) détermine automatiquement si vous avez besoin d’une licence. Cette opération n’est à effectuer qu’une seule fois sur tous les dépôts. En règle générale, il existe un intervalle de temps pour établir un ensemble d’objectifs à atteindre.

## <a name="what-happens-to-bots-built-using-sdk-v3"></a>Qu’advient-il des bots générés avec le kit SDK v3 ?

Le kit SDK Bot Framework v3 sera supprimé, mais les charges de travail du bot v3 existantes continueront de fonctionner sans interruption. Pour plus d'informations, consultez les pages suivantes : [Prise en charge de la durée de vie du kit SDK Bot Framework version 3](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-resources-bot-framework-faq?view=azure-bot-service-4.0#bot-framework-sdk-version-3-lifetime-support).

Nous vous recommandons vivement de commencer la migration de vos bots v3 vers v4. Afin de prendre en charge cette migration, nous avons élaboré une documentation connexe et nous assurerons une prise en charge étendue des initiatives de migration via les canaux standard.

## <a name="advantages"></a>Avantages

- Architecture plus riche, flexible et ouverte : permet une conception plus souple des conversations
- Disponibilité : offre des scénarios supplémentaires avec de nouvelles fonctionnalités de canal
- Experts techniques supplémentaires dans le cycle de développement : le nouveau concepteur d’interface graphique utilisateur permet à des utilisateurs autres que des développeurs de collaborer sur la conception des conversations
- Vitesse de développement : nouveaux outils de développement pour le débogage et les tests
- Insights des performances : nouvelles fonctionnalités de télémétrie pour évaluer et améliorer la qualité des conversations
- Intelligence : fonctionnalités améliorées des services cognitifs

## <a name="why-migrate"></a>Pourquoi migrer ?
<!-- [!] The declarative model introduced with Adaptive Dialogs would go great here (when ready).  -->
- Gestion des conversations flexible et améliorée
  - Adaptateur de bot pour le traitement d’activité
  - Gestion de l’état refactorisée
  - Nouvelle bibliothèque de dialogues
  - Middleware pour les conceptions composables et extensibles : Raccordements normaux et cohérents pour personnaliser le comportement
- Conception pour .NET Core
  - performances améliorées
  - Compatibilité multiplateforme (Windows/Mac/Linux)
- Modèle de programmation cohérent entre plusieurs langages de programmation
- Documentation améliorée
- Bot Inspector assure des fonctionnalités de débogage étendues
- Assistant virtuel
  - Solution complète qui simplifie la création de bots avec des intentions conversationnelles simples, l’intégration de Dispatch, QnA Maker, Application Insights et un déploiement automatisé.
  - Compétences extensibles. Il est possible de composer des expériences conversationnelles en combinant des fonctionnalités conversationnelles réutilisables, appelées « compétences ».
- Framework de test : fonctionnalités de test prêtes à l’emploi avec la nouvelle architecture de l’adaptateur indépendante du transport
- Télémétrie : obtenez des insights importants sur la santé de votre bot et son comportement avec les solutions AI Analytics conversationnelles
- Prochainement (préversion)
  - Dialogues adaptatifs : créez des conversations qui peuvent être modifiées dynamiquement à mesure que la conversation progresse
  - Génération de langage : définissez plusieurs variantes d’une expression
- À l’avenir
  - Une conception déclarative permet un niveau d’abstraction pour les concepteurs
  - Concepteur de dialogues d’interface graphique utilisateur
- Azure Bot Service 
  - Canal Direct Line Speech. Nous avons rassemblé Bot Framework et les services de reconnaissance vocale de Microsoft. Cela fournit un canal permettant le streaming bidirectionnel de la parole et du texte, du client vers l’application de bot

## <a name="whats-changed"></a>Changements apportés

Le kit SDK Bot Framework v4 prend en charge le même service Bot Framework sous-jacent que v3. Toutefois, la version v4 est une refactorisation des kits SDK précédents pour offrir davantage de flexibilité et de contrôle sur la création d’un bot. Les éléments suivants sont inclus :

- Un adaptateur de bot a été introduit
  - L’adaptateur fait partie de la pile de traitement d’activité
  - Il gère l’authentification Bot Framework et initialise le contexte pour chaque tour
  - Il gère le trafic entrant et sortant entre un canal et le gestionnaire de tour de votre bot, encapsulant les appels au service Bot Framework Connector
  - Pour plus d’informations, consultez [Fonctionnement des bots](../bot-builder-basics.md)
- Gestion de l’état refactorisée
  - Les données d’état ne sont plus automatiquement disponibles au sein d’un bot
  - L’état est désormais géré par le biais d’objets de gestion d’état et d’accesseurs de propriété
  - Pour plus d’informations, consultez [Gestion de l’état](../bot-builder-concept-state.md)
- Nouvelle bibliothèque de dialogues introduite
  - Les dialogues v3 doivent être réécrits pour la nouvelle bibliothèque de dialogues
  - Pour plus d’informations, consultez [Bibliothèque des dialogues](../bot-builder-concept-dialog.md)

## <a name="whats-involved-in-migration-work"></a>Éléments impliqués dans le travail de migration

- Mettre à jour la logique d’installation
- Transférer tout état utilisateur critique
  - Remarque : un état utilisateur sensible ne peut pas demeurer dans un état de bot ; placez-le plutôt dans un stockage distinct sous votre contrôle
- Transférer la logique de bot et de dialogue (consultez les rubriques spécifiques au langage pour plus de détails)

### <a name="migration-estimation-worksheet"></a>Feuille de calcul d’estimation de la migration

La feuille de calcul suivante peut vous guider dans l’estimation de votre charge de travail de migration. Dans la colonne **Occurrences**, remplacez *count* par votre valeur numérique réelle. Dans la colonne **T Shirt**, entrez des valeurs telles que : *Petit*, *Moyen*, *Grand* selon votre estimation.

Étape | V3 | V4 | Occurrences | Complexité | T Shirt
-- | -- | -- | -- | -- | --
Pour obtenir l’activité entrante | IDialogContext.Activity | ITurnContext.Activity | count | Petite  
Pour créer une activité et l’envoyer à l’utilisateur | activity.CreateReply(“text”) IDialogContext.PostAsync | MessageFactory.Text(“text”) ITurnContext.SendActivityAsync | count | Petite |
Gestion de l'état | UserData, ConversationData et PrivateConversationData context.UserData.SetValue context.UserData.TryGetValue botDataStore.LoadAsyn | UserState, ConversationState et PrivateConversationState, avec accesseurs de propriété | context.UserData.SetValue - count context.UserData.TryGetValue - count botDataStore.LoadAsyn - count | Moyen à Grand (consultez la [gestion de l’état utilisateur](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0#state-management) disponible) |
Gérer le début de votre dialogue | Implémentez IDialog.StartAsync | Définissez ceci comme première étape d’un dialogue en cascade. | count | Petite |  
Envoyer une activité | IDialogContext.PostAsync. | Appelez ITurnContext.SendActivityAsync. | count | Petite |  
Attendre la réponse d’un utilisateur | Utilisez un IAwaitable<IMessageActivity>parameter et appelez IDialogContext.Wait | Utilisez return await avec ITurnContext.PromptAsync pour commencer un dialogue d’invite. Ensuite, récupérez le résultat à l’étape suivante de la cascade. | count | Moyen (selon le flux) |  
Gérer la continuation de votre dialogue | IDialogContext.Wait | Ajoutez des étapes supplémentaires à un dialogue en cascade ou implémentez Dialog.ContinueDialogAsync | count | grand |  
Signaler la fin du traitement jusqu’au prochain message de l’utilisateur | IDialogContext.Wait | Retournez Dialog.EndOfTurn. | count | Moyenne |  
Commencer un dialogue enfant | IDialogContext.Call | Utilisez return await avec la méthode BeginDialogAsync du contexte d’étape. Si le dialogue enfant retourne une valeur, celle-ci est disponible à l’étape suivante de la cascade via la propriété Resultproperty du contexte d’étape. | count | Moyenne |  
Remplacer le dialogue actuel par un nouveau dialogue | IDialogContext.Forward | Utilisez return await avec ITurnContext.ReplaceDialogAsync. | count | grand |  
Signaler que le dialogue en cours est terminé | IDialogContext.Done | Utilisez return await avec la méthode EndDialogAsync du contexte d’étape. | count | Moyenne |  
Échec d’un dialogue. | IDialogContext.Fail | Levez une exception à intercepter à un autre niveau du bot, terminez l’étape avec l’état Annulé ou appelez la méthode CancelAllDialogsAsync du contexte d’étape ou de dialogue. | count | Petite |  

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

Le kit SDK Bot Framework v4 est basé sur la même API REST sous-jacente que le kit SDK v3. Toutefois, la version v4 est une refactorisation de la version précédente du kit SDK pour offrir davantage de flexibilité et de contrôle sur les bots.

Nous vous recommandons de migrer vers .NET Core, dans la mesure où les performances ont été considérablement améliorées. Toutefois, certains bots v3 existants utilisent des bibliothèques externes qui n’ont pas d’équivalent .NET Core. Dans ce cas, le kit SDK Bot Framework v4 peut être utilisé avec .NET Framework version 4.6.1 ou ultérieure. Vous trouverez un exemple à l’emplacement [corebot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi).

Lors de la migration d’un projet à partir de v3 vers v4, vous pouvez choisir l’une des options suivantes : convertir sur place pour **.NET Framework** ou transférer vers un nouveau projet pour **.NET Core**.

#### <a name="net-framework"></a>.NET Framework

- Mettre à jour et installer les packages NuGet
- Mettre à jour votre fichier Global.asax.cs
- Mettre à jour votre classe MessagesController
- Convertir vos dialogues

Pour plus d’informations, consultez [Migrer un bot .NET v3 vers un bot .NET Framework v4](conversion-framework.md).

#### <a name="net-core"></a>.NET Core

- Créer le projet à l’aide d’un modèle
- Installer d’autres packages NuGet en fonction des besoins
- Personnaliser votre bot, mettre à jour votre fichier Startup.cs et mettre à jour votre classe de contrôleur
- Mettre à jour de votre classe de bot
- Copier et mettre à jour vos dialogues et modèles

Pour plus d’informations, consultez [Migrer un bot .NET v3 vers un bot .NET Core v4](conversion-core.md).

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Le **kit SDK JavaScript Bot Framework v4** présente des changements fondamentaux concernant la façon dont les bots sont créés. Ces changements affectent la syntaxe de développement des bots dans JavaScript, notamment au niveau de la création d’objets bot, de la définition de dialogues et de l’encodage d’une logique de gestion des événements. Le kit SDK Bot Framework v4 est basé sur la même API REST sous-jacente que le kit SDK v3. Toutefois, la version v4 est une refactorisation de la version précédente du kit SDK pour offrir davantage de flexibilité et de contrôle sur les bots, en particulier :

- Les dialogues et les instances de bot ont fait l’objet d’un découplage supplémentaire. Dans la version v3, les dialogues étaient enregistrés directement dans le constructeur du bot. Dans la version v4, vous passez désormais les dialogues dans des instances de bot en tant qu’arguments, ce qui permet une plus grande flexibilité de composition
- La version v4 fournit une classe `ActivityHandler`, qui aide à automatiser la gestion des différents types d’activités, comme les activités de message, de mise à jour de conversation et d’événement
- Pour la migration de bot NodeJS, vous devez créer un bot NodeJS v4 en JavaScript ou TypeScript. Recréez la logique de bot à l’aide des nouvelles constructions v4 décrites dans la documentation liée à la migration

#### <a name="migrate-from-v3-to-v4"></a>Migrer de v3 vers v4

- Créer le projet et ajouter des dépendances
- Mettre à jour le point d’entrée et définir des constantes
- Créer les dialogues en les implémentant de nouveau à l’aide du SDK v4
- Mettre à jour le code du bot pour exécuter les dialogues
- Transférer le fichier utilitaire `store.js`

Pour plus d’informations, consultez [Migrer un bot du kit SDK JavaScript v3 vers v4](conversion-javascript.md).

---

## <a name="additional-resources"></a>Ressources supplémentaires

Les ressources supplémentaires suivantes fournissent d’autres informations qui peuvent être utiles lors de la migration.  

### <a name="ctabcsharp"></a>[C#](#tab/csharp)

<!-- _Mini-TOC with explainer for .NET topics_ -->
Les rubriques suivantes décrivent les différences entre les kits SDK .NET Bot Framework v3 et v4, les principales modifications entre les deux versions et les étapes pour migrer un bot de v3 vers v4.

| Rubrique | Description |
| :--- | :--- |
| [Différences entre le SDK .NET v3 et v4](migration-about.md) |Différences courantes entre les kits SDK v3 et v4 |
| [Aide-mémoire sur la migration .NET](net-migration-quickreference.md) |Modifications majeures dans les kits SDK v3 et v4 |
| [Migrer un bot .NET v3 vers un bot Framework v4](conversion-framework.md) |Migrer un bot v3 vers un bot v4 à l’aide du même type de projet |
| [Migrer un bot .NET v3 vers un bot .NET Core v4](conversion-core.md) | Migrer un bot v3 vers un bot v4 dans un nouveau projet .NET Core|

### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!-- _Mini-TOC with explainer for JavaScript topics_ -->
Les rubriques suivantes décrivent les différences entre les kits SDK JavaScript Bot Framework v3 et v4, les principales modifications entre les deux versions et les étapes pour migrer un bot de v3 vers v4.

| Rubrique | Description |
| :--- | :--- |
| [Différences entre les kits SDK JavaScript v3 et v4](migration-about-javascript.md) | Différences courantes entre les kits SDK v3 et v4 |
| [Aide-mémoire sur la migration JavaScript](javascript-migration-quickreference.md)| Modifications majeures dans les kits SDK v3 et v4|
| [Migrer un bot JavaScript v3 vers v4](conversion-javascript.md) |Migrer un bot v3 vers un bot v4 |

---

### <a name="code-samples"></a>Exemples de code

Voici des exemples de code que vous pouvez utiliser pour découvrir le kit SDK Bot Framework v4 ou commencer rapidement votre projet.

| Exemples | Description |
| :--- | :--- |
| [Exemples de migration d’un kit SDK Bot Framework v3 vers v4](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4) <img width="200">| Exemples de migration du kit SDK Bot Framework v3 vers v4 |
| [Exemples .NET Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore) | Exemples C# .NET Core Bot Builder |
| [Exemples JavaScript Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs) | Exemples JavaScript (node.js) Bot Builder |
| [Tous les exemples Bot Builder](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples) | Tous les exemples Bot Builder |

### <a name="getting-help"></a>Obtenir de l’aide

Les ressources suivantes fournissent des informations et un support supplémentaires pour le développement de bots.

[Ressources supplémentaires pour Bot Framework](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-resources-links-help?view=azure-bot-service-4.0)

### <a name="references"></a>Références

Reportez-vous aux ressources suivantes pour obtenir plus de détails et d’informations générales.

| Rubrique | Description |
| :--- | :--- |
| [Nouveautés de Bot Framework](https://docs.microsoft.com/en-us/azure/bot-service/what-is-new?view=azure-bot-service-4.0) | Améliorations et fonctionnalités clés de Bot Framework et d’Azure Bot Service|
|[Fonctionnement des bots](../bot-builder-basics.md)|Mécanisme interne d’un bot|
|[Gestion de l’état](../bot-builder-concept-state.md)|Abstractions pour faciliter la gestion d’état|
|[Bibliothèque de boîtes de dialogues](../bot-builder-concept-dialog.md)| Concepts centraux pour gérer une conversation|
|[Envoyer et recevoir des SMS](../bot-builder-howto-send-messages.md)|Principal moyen de communication d’un bot avec les utilisateurs|
|[Envoyer des médias](../bot-builder-howto-add-media-attachments.md)|Pièces jointes multimédias, comme des images, des vidéos, du contenu audio et des fichiers| 
|[Flux de conversation séquentiels](../bot-builder-dialog-manage-conversation-flow.md)| Questionnement comme principal moyen d’interaction d’un bot avec les utilisateurs|
|[Enregistrer les données d’utilisateur et de conversation](../bot-builder-howto-v4-state.md)|Suivi d’une conversation en mode sans état|
|[Flux complexes](../bot-builder-dialog-manage-complex-conversation-flow.md)|Gérer des flux de conversation complexes |
|[Réutiliser des dialogues](../bot-builder-compositcontrol.md)|Créer des dialogues indépendants pour gérer des scénarios spécifiques|
|[Interruptions](../bot-builder-howto-handle-user-interrupt.md)| Gestion des interruptions pour créer un bot efficace|
|[Schéma d’activité](https://aka.ms/botSpecs-activitySchema)|Schéma pour des humains et des logiciels automatisés|
