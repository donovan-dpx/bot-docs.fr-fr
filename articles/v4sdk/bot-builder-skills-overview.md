---
title: Vue d’ensemble des compétences Bot Framework | Microsoft Docs
description: Découvrir les compétences Bot Framework
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/06/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7ce7556b58c047e20f1b32597c896baa802968aa
ms.sourcegitcommit: b94c4286f6f64955fd51ccf4a68109c43db0e47d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65083766"
---
# <a name="virtual-assistant---skills-overview"></a>Assistant virtuel : vue d’ensemble des compétences

> [!NOTE]
> Cet article s’applique à la version v4 du Kit de développement logiciel (SDK). 

## <a name="overview"></a>Vue d’ensemble

Les développeurs peuvent composer des expériences conversationnelles en combinant des fonctionnalités conversationnelles réutilisables, appelées compétences.

Au sein d’une entreprise, cela peut se traduire par la création d’un bot parent rassemblant plusieurs sous-bots détenus par différentes équipes ou plus largement tirant parti de fonctionnalités courantes fournies par d’autres développeurs. Avec cette préversion des compétences, les développeurs peuvent créer un bot (généralement par le biais du modèle d’assistant virtuel) et ajouter/supprimer des compétences au moyen d’une seule opération de ligne de commande englobant toutes les modifications Dispatch et de configuration.     

Les compétences sont elles-mêmes des bots, appelés à distance, et un modèle de développement de compétences (.NET, TS) est disponible pour faciliter leur création.

Un objectif fondamental de la conception des compétences consistait à maintenir le protocole d’activité cohérent et à s’assurer que l’expérience de développement était aussi proche que possible de tout bot SDK V4 ordinaire. 

![Scénarios de compétences](./media/enterprise-template/skills-scenarios.png)

## <a name="bot-framework-skills"></a>Compétences Bot Framework

À ce stade, nous avons mis à disposition les compétences Bot Framework suivantes, qui bénéficient de la technologie Microsoft Graph et sont disponibles dans plusieurs langues.

![Scénarios de compétences](./media/enterprise-template/skills-at-build.png)

| Nom | Description |
| ---- | ----------- |
|[Compétence Calendrier](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-calendar.md)|Ajoutez des fonctionnalités de calendrier à votre assistant. Technologie Microsoft Graph et Google.|
|[Compétence E-mail](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-email.md)|Ajoutez des fonctionnalités d’e-mail à votre assistant. Technologie Microsoft Graph et Google.|
|[Compétence ToDo](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-todo.md)|Ajoutez des fonctionnalités de gestion des tâches à votre assistant. Technologie Microsoft Graph.|
|[Compétence Point d’intérêt](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/productivity-pointofinterest.md)|Trouvez des points d’intérêt et des directions. Technologie Azure Maps et FourSquare.|
|[Compétence Secteur automobile](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/automotive.md)|Compétence verticale du secteur présentant l’activation du contrôle des fonctionnalités d’un véhicule.|
|[Compétences expérimentales](https://github.com/Microsoft/AI/blob/master/docs/reference/skills/experimental.md)|Actualités, réservation de restaurant et météo.|

## <a name="getting-started"></a>Mise en route

Reportez-vous aux [informations de prise en main](https://github.com/Microsoft/AI/tree/master/docs#tutorials) pour apprendre à tirer parti des compétences existantes et à créer les vôtres.
