---
title: Vue d’ensemble des compétences Bot Framework - Bot Service
description: Découvrir les compétences Bot Framework
author: darrenj
ms.author: darrenj
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 10/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 7d7c4271fd03100c24ba7dd774dea93909bdd8a0
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791231"
---
# <a name="virtual-assistant---skills-overview"></a>Assistant virtuel : vue d’ensemble des compétences

> [!NOTE]
> Cet article s’applique à la version v4 du Kit de développement logiciel (SDK). 

## <a name="overview"></a>Vue d’ensemble

Les développeurs peuvent composer des expériences conversationnelles en combinant des fonctionnalités conversationnelles réutilisables, appelées « compétences ».

Au sein d’une entreprise, cela peut se traduire par la création d’un bot parent rassemblant plusieurs sous-bots détenus par différentes équipes ou plus largement tirant parti de fonctionnalités courantes fournies par d’autres développeurs. Avec cette préversion des compétences, les développeurs peuvent créer un bot (généralement par le biais du modèle d’assistant virtuel) et ajouter/supprimer des compétences au moyen d’une seule opération de ligne de commande englobant toutes les modifications Dispatch et de configuration.     

Les compétences sont elles-mêmes des bots, appelés à distance, et un modèle de développement de compétences (.NET, TS) est disponible pour faciliter leur création.

Un objectif fondamental de la conception des compétences consistait à maintenir le protocole d’activité cohérent et à s’assurer que l’expérience de développement était aussi proche que possible de tout bot SDK V4 ordinaire. 

![Scénarios de compétences](./media/enterprise-template/skills-scenarios.png)

## <a name="bot-framework-skills"></a>Compétences Bot Framework

À ce stade, nous avons mis à disposition les compétences Bot Framework suivantes, qui bénéficient de la technologie Microsoft Graph et sont disponibles dans plusieurs langues.

![Scénarios de compétences](./media/enterprise-template/skills-at-build.png)

| Name | Description |
| ---- | ----------- |
|[Compétence Calendrier](https://aka.ms/bf-calendar-skill)|Ajoutez des fonctionnalités de calendrier à votre assistant. Technologie Microsoft Graph et Google.|
|[Compétence E-mail](https://aka.ms/bf-email-skill)|Ajoutez des fonctionnalités d’e-mail à votre assistant. Technologie Microsoft Graph et Google.|
|[Compétence ToDo](https://aka.ms/bf-todo-skill)|Ajoutez des fonctionnalités de gestion des tâches à votre assistant. Technologie Microsoft Graph.|
|[Compétence Point d’intérêt](https://aka.ms/bf-poi-skill)|Trouvez des points d’intérêt et des directions. Technologie Azure Maps et FourSquare.|
|[Compétence Secteur automobile](https://aka.ms/bf-auto-skill)|Compétence verticale du secteur présentant l’activation du contrôle des fonctionnalités d’un véhicule.|
|[Compétences expérimentales](https://aka.ms/bf-experimental-skills)|Actualités, réservation de restaurant et météo.|

## <a name="getting-started"></a>Mise en route

Reportez-vous aux [tutoriels](https://aka.ms/bfs-tutorials) pour apprendre à tirer parti des compétences existantes et à créer les vôtres.
