---
title: Demandes User-Agent de Bot Framework - Bot Service
description: Présentation des demandes de Bot Framework à destination de votre serveur web.
author: JohnD-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
ms.openlocfilehash: e6b0df6384b6ecc1ffa8922e574c6b161db8369e
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75794127"
---
# <a name="bot-framework-user-agent-requests"></a>Demandes User-Agent de Bot Framework

Si vous êtes en train de lire ce message, vous avez probablement reçu une demande d’un service Microsoft Bot Framework. Ce guide vous aide à comprendre la nature de ces demandes et présente les étapes permettant de les arrêter, si vous le souhaitez.

Si vous avez reçu une demande de notre service, celle-ci avait probablement un en-tête User-Agent dont la mise en forme était similaire à la chaîne ci-dessous :

```User-Agent: BF-DirectLine/3.0 (Microsoft-BotFramework/3.0 +https://botframework.com/ua)```

La partie la plus importante de cette chaîne est l’identificateur **Microsoft-BotFramework**, qui est utilisé par Microsoft Bot Framework, collection d’outils et de services permettant aux développeurs de logiciels indépendants de créer et d’utiliser leurs propres bots.

Si vous êtes développeur de bot, vous savez peut-être déjà pourquoi ces demandes sont transmises à votre service. Sinon, poursuivez votre lecture pour en savoir plus.

## <a name="why-is-microsoft-contacting-my-service"></a>Pourquoi Microsoft contacte-t-il mon service ?

Le Bot Framework connecte les utilisateurs des services de chat tels que Skype et Facebook Messenger aux bots, serveurs web dotées d’API REST s’exécutant sur des points de terminaison accessibles sur Internet. Les appels HTTP à ces bots (également appelés appels webhook) sont uniquement envoyés aux URL spécifiées par un développeur de bot qui s’est inscrit auprès du portail des développeurs Bot Framework.

Si votre service web reçoit des demandes non sollicitées de la part de services Bot Framework, cela est probablement dû au fait qu’un développeur a intentionnellement ou accidentellement entré votre URL en tant que rappel webhook pour son bot.

## <a name="to-stop-these-requests"></a>Pour mettre fin à ces demandes

Pour obtenir une assistance afin que votre service web ne reçoive plus de demandes indésirables, veuillez contacter [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).
