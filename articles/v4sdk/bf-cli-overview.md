---
title: Vue d’ensemble de l’interface de ligne de commande (CLI) d’Azure Bot Framework | Microsoft Docs
description: Découvrez l’interface de ligne de commande (CLI) de Bot Framework.
keywords: Interface de ligne de commande de Bot Framework, interface CLI de Bot Framework
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/01/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 4780d5258af7d2c93fafece361326fd2b0f8df77
ms.sourcegitcommit: 4751c7b8ff1d3603d4596e4fa99e0071036c207c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2019
ms.locfileid: "73443187"
---
<!--TODO:
- [?] Add to TOC: Reference/Bot Framework CLI/Reference
- [?] Add other topics to the same node for each of the command groups
-->
# <a name="bot-framework-cli-overview"></a>Vue d’ensemble de l’interface CLI de Bot Framework

[!INCLUDE[applies-to](../includes/applies-to.md)]

L’interface de ligne de commande (CLI) de Bot Framework est un outil multiplateforme qui vous permet de gérer les bots et les services associés. Elle remplace un ensemble d’outils CLI autonomes plus anciens et les regroupe dans un seul et même outil. 

## <a name="prerequisites"></a>Prérequis

* [Node.js](https://nodejs.org/), version 10.14.1 ou ultérieure.

## <a name="installation"></a>Installation

Installez l’interface CLI BF à partir de la ligne de commande.

~~~cmd
npm i -g @microsoft/botframework-cli
~~~

## <a name="available-commands"></a>Commandes disponibles

Les commandes actuellement disponibles sont les suivantes.

| Ancien outil | Jeu de commandes BF | Description |
| :--- | :--- | :--- |
| ChatDown | [`bf chatdown`](bf-cli-reference.md#bf-chatdown) | Commandes permettant d’utiliser des fichiers de dialogue de conversation ( **.chat**). |
| na | [`bf config`](bf-cli-reference.md#bf-config) | Configure divers paramètres dans l’interface CLI. |
| LuDown, LuisGen | [`bf luis`](bf-cli-reference.md#bf-luis) | Commandes permettant d’utiliser des fichiers de ressources LUIS et de gérer des modèles LUIS. |
| QnAMaker | [`bf qnamaker`](bf-cli-reference.md#bf-qnamaker) | Commandes permettant d’utiliser des fichiers de ressources QnA Maker et de gérer des bases de connaissances. |

Les outils suivants seront portés dans les versions à venir :
- LUIS (API)
- Dispatch

Pour obtenir des informations de référence sur le mappage entre les anciens et nouveaux outils, consultez la page [Porting Map](https://github.com/microsoft/botframework-cli/blob/master/PortingMap.md).

_Remarque : Les anciens outils CLI seront dépréciés dans les versions à venir et leur prise en charge prendra fin à terme. Tous les nouveaux investissements, les correctifs de bogues et les nouvelles fonctionnalités dans ce domaine cibleront uniquement l’interface CLI BF._

## <a name="overview"></a>Vue d'ensemble

L’interface CLI BF gère les bots et les services associés. Elle fait partie intégrante de Microsoft Bot Framework, framework complet destiné à créer des expériences IA conversationnelles pour grandes entreprises. En plus de gérer les ressources liées aux bots, l’interface CLI BF peut être utilisée dans les pipelines d’intégration continue et de déploiement continu (CI/CD). Au moment de créer votre bot, vous aurez peut-être aussi besoin d’intégrer des services IA comme LUIS pour la compréhension de la langue, QnA Maker pour permettre à votre bot de réponde à des questions simples dans un format questions et réponses, etc. Pour intégrer des services IA dans votre bot, utilisez :

* La commande [`bf luis`](bf-cli-reference.md#bf-luis) pour utiliser des fichiers de ressources **. lu** LUIS et gérer les modèles LUIS. Elle peut aussi générer le code source (C# ou JavaScript) correspondant.
* L’[outil API LUIS](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS/readme.md) pour déployer, entraîner, tester et publier les fichiers locaux en tant que modèles Language Understanding dans le service LUIS.
* La commande [`bf qnamaker`](bf-cli-reference.md#bf-qnamaker) pour utiliser des bases de connaissances QnA Maker. Elle peut créer et gérer des ressources QnA Maker aussi bien localement que dans le service QnA Maker.

* Consultez la [documentation](https://github.com/microsoft/botframework-cli/tree/master/packages/lu/README.md) de la bibliothèque lu pour savoir comment utiliser les formats de fichiers **.lu** et **.qna**.

À mesure que votre bot devient de plus en plus sophistiqué, utilisez l’outil CLI [Dispatch](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) pour créer, évaluer et dispatcher des intentions entre plusieurs modèles LUIS et bases de connaissances QnA Maker.

Pour tester et affiner votre bot, vous pouvez utiliser le nouvel émulateur [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases). L’émulateur vous permet de tester et déboguer vos bots sur l’ordinateur local ou dans le cloud.

Au cours des premières phases de conception, vous souhaiterez peut-être créer des conversations fictives entre l’utilisateur et le bot pour les scénarios spécifiques que votre bot prendra en charge. Utilisez la commande [`bf chatdown`](bf-cli-reference.md#bf-chatdown) pour créer des fichiers de maquette de conversation **.chat**, convertissez-les en transcriptions enrichies, puis examinez les conversations dans l’émulateur.

Enfin, avec [Azure CLI](https://github.com/microsoft/botframework-cli/blob/master/AzureCli.md) (commande `az bot`), vous pouvez créer, télécharger, publier et configurer des canaux avec [Azure Bot Service](https://azure.microsoft.com/services/bot-service/). Il s’agit d’un plug-in qui étend les fonctionnalités d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour gérer vos ressources Azure Bot Service.

## <a name="privacy-and-instrumentation"></a>Confidentialité et instrumentation
L’interface CLI BF contient des options d’instrumentation conçues pour nous aider à améliorer l’outil à partir de modèles d’utilisation **anonymes**. __Elles sont désactivées, refusées par défaut__. Si vous choisissez de les accepter, Microsoft recueille certaines données d’utilisation :

* Appels de groupes de commandes
* Indicateurs utilisés, **à l’exclusion** de valeurs spécifiques. Par exemple, pour le paramètre `--folder:name`, seule l’utilisation de `--folder` est collectée, le nom du dossier ne l’est pas.

Pour modifier le comportement de collecte des données, utilisez la commande [`bf config`](bf-cli-reference.md#bf-config).

Pour plus d’informations, consultez [Déclaration de confidentialité de Microsoft](https://privacy.microsoft.com/privacystatement).  

## <a name="issues-and-feature-requests"></a>Problèmes et demandes de fonctionnalités
- Vous pouvez soumettre des problèmes et des demandes de fonctionnalités [ici](https://github.com/microsoft/botframework-cli/issues).
- Vous trouverez les problèmes connus [ici](https://github.com/microsoft/botframework-cli/labels/known-issues).

## <a name="next-steps"></a>Étapes suivantes
- [Informations de référence sur l’interface CLI BF](bf-cli-reference.md)
