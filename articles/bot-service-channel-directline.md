---
title: À propos du canal Direct Line
titleSuffix: Bot Service
description: Fonctionnalités du canal Direct Line
services: bot-service
author: ivorb
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: kamrani
ms.openlocfilehash: 3a1bf9ef71ec3121cc845a2a93fd191eacc28f8f
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70298191"
---
## <a name="about-direct-line"></a>À propos de Direct Line

Le canal Bot Framework Direct Line est un moyen simple d’intégrer votre bot dans votre application mobile, page web ou autre application.
Direct Line est disponible sous trois formes :
- Service Direct Line – service mondial robuste, destiné à la plupart des développeurs
- Extensions App Service Direct Line – fonctionnalités Direct Line dédiées pour la sécurité et les performances (disponibles en préversion privée à partir de mai 2019)
- Direct Line Speech – optimisé pour les fonctions vocales hautes performances (disponible en préversion privée à partir de mai 2019)

Vous pouvez choisir le produit Direct Line qui vous convient le mieux en évaluant les fonctionnalités offertes par chacun et les besoins de votre solution. Au fil du temps, ces produits seront simplifiés.

|                            | Ligne directe | Extension App Service Direct Line | Direct Line Speech |
|----------------------------|-------------|-----------------------------------|--------------------|
| Disponibilité et licences    | Disponibilité générale | Préversion privée, sans contrat SLA  | Préversion privée, sans contrat SLA |
| Performances de reconnaissance vocale et de synthèse vocale | standard | standard | Hautes performances |
| OAuth intégrée           | OUI | OUI | Non |
| Télémétrie intégrée       | OUI | OUI | Non |
| Prise en charge des navigateurs web hérités | OUI | Non | Non |
| Prise en charge du kit SDK Bot Framework | Toutes les versions v3 et v4 | v4.5+ obligatoire | v4.5+ obligatoire |
| Prise en charge du kit SDK client    | JS, C# | JS, C# | C++, C#, Unity |
| Fonctionne avec Web Chat  | OUI | OUI | Non|
| API d’historique des conversations | OUI | OUI| Non|
| Réseau virtuel | Non | Préversion* | Non |

_* Les extensions App Service Direct Line peuvent être utilisées sur les réseaux virtuels, mais ne permettent pas encore de limiter les appels sortants._

## <a name="addtional-resources"></a>Ressources supplémentaires
- [Connecter un robot à Direct Line](bot-service-channel-connect-directline.md)
- [Connecter un bot à Direct Line Speech](bot-service-channel-connect-directlinespeech.md)
