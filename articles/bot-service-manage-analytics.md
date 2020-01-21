---
title: Analyse de bot - Bot Service
description: Découvrez comment utiliser la collecte de données et l’analyse pour améliorer votre bot grâce à des analyses dans Bot Framework.
keywords: analyse de bot, application insights, trafic, latence, intégrations, AppInsights
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/04/2018
ms.openlocfilehash: 88f8c629bd907bf6f9af3ef189bfb6e329b36b02
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75793605"
---
# <a name="bot-analytics"></a>Analyse de bot

Analytics est une extension d’[Application Insights](/azure/application-insights/app-insights-analytics). Application Insights fournit des données de **niveau de service** et d’instrumentation relatives au trafic, à la latence et aux intégrations. Analytics permet de créer des **rapports de conversation** à partir des données sur les utilisateurs, les messages et les canaux.

## <a name="view-analytics-for-a-bot"></a>Afficher l’analyse d’un bot

Pour accéder à Analytics, ouvrez le bot dans le portail Azure et cliquez sur **Analytics**.

Trop de données ? Vous pouvez [activer et configurer l’échantillonnage](/azure/application-insights/app-insights-sampling) pour le service Application Insights lié à votre bot. Ainsi, le trafic de télémétrie et le stockage sont réduits tout en conservant un niveau d’analyse statistiquement correct.

### <a name="specify-channel"></a>Spécifier le canal

Choisissez les canaux qui apparaîtront dans les graphiques ci-dessous. Notez que si un bot n’est pas activé sur un canal, ce canal ne fournira pas de données.

![Sélectionnez le canal](~/media/analytics-channels.png)

* Cochez la case à cocher pour inclure un canal dans le graphique.
* Décochez la case à cocher pour supprimer un canal du graphique.

### <a name="specify-time-period"></a>Spécifier la période

L’analyse n’est disponible pour les 90 derniers jours. La collecte de données a commencé lorsqu’Application Insights a été activé.

![Sélectionnez la période](~/media/analytics-timepick.png)

Cliquez sur le menu déroulant, puis sur la période que les graphiques doivent afficher.
Notez que la modification de l’intervalle de temps global entraîne la modification en conséquence de l’incrément de temps (axe x) sur les graphiques.

### <a name="grand-totals"></a>Total général

Il s’agit du nombre total d’utilisateurs actifs et d’activités envoyées et reçues pendant le délai d’exécution spécifié.
Les tirets `--` indiquent qu’il n’y a aucune activité.

### <a name="retention"></a>Rétention

La rétention indique le nombre d’utilisateurs ayant envoyé un message qui sont revenus ultérieurement et en ont envoyé un autre.
Le graphique est une fenêtre cumulative de 10 jours ; les résultats ne sont pas affectés par la modification du laps de temps.

![Graphique de rétention](~/media/analytics-retention.png)

Notez que la date la plus récente possible est deux jours avant ; un utilisateur a envoyé des messages avant-hier, puis est *revenu* hier.

### <a name="user"></a>Utilisateur

Le graphique des utilisateurs indique le nombre d’utilisateurs ayant accédé au bot à l’aide de chaque canal pendant le laps de temps spécifié.

![Graphique des utilisateurs](~/media/analytics-users.png)

* Le graphique des pourcentages affiche le pourcentage d’utilisateurs ayant utilisé chaque canal.
* Le graphique linéaire indique le nombre d’utilisateurs qui avaient accès au bot à une heure donnée.
* La légende du graphique linéaire indique la couleur qui représente chaque canal et le nombre total d’utilisateurs pendant la période spécifiée.

### <a name="activities"></a>Activités

Le graphique des activités indique le nombre d’activités envoyées et reçues dans chaque canal au cours du délai d’exécution spécifié.

![Graphique des activités](~/media/analytics-activities.png)

* Le graphique des pourcentages indique le pourcentage d’activités communiquées par chaque canal.
* Le graphique linéaire indique le nombre d’activités envoyées et reçues au cours du délai d’exécution spécifié.
* La légende du graphique linéaire indique la couleur de ligne correspondant à chaque canal et le nombre total d’activités envoyées et reçues sur ces canaux pendant la période spécifiée.

## <a name="enable-analytics"></a>Activer l'analyse

Analytics n’est disponible que lorsqu’Application Insights a été activé et configuré. Application Insights commence à collecter des données dès qu’il est activé. Par exemple, si Application Insights a été activée il y a une semaine pour un bot datant de six mois, il aura collecté les données d’une semaine.

> [!NOTE]
> Analytics nécessite un abonnement Azure et la [ressource](/azure/application-insights/app-insights-create-new-resource) Application Insights.
Pour accéder à Application Insights, ouvrez le bot dans le [portail Azure](https://portal.azure.com/) et cliquez sur **Paramètres**.

Vous pouvez ajouter Application Insights lorsque vous créez la ressource de votre bot.

Vous pouvez également créer une ressource Application Insights plus tard et la connecter à votre bot.

1. Créez une [ressource](/azure/application-insights/app-insights-create-new-resource) Application Insights.
2. Ouvrez le bot dans le tableau de bord. Cliquez sur **Paramètres** et faites défiler jusqu'à la section **Analytics**.
3. Entrez les informations pour connecter le bot à Application Insights. Tous les champs sont obligatoires.

![Connectez-vous à Insights](~/media/analytics-enable.png)

<!--Snip: As of 12/04/2018, parts of this appear to be out of date. However, ~/bot-service-resources-app-insights-keys.md appears to be up to date.

### AppInsights Instrumentation Key

To find this value, open the Application Insights resource for your bot and navigate to **Configure** > **Properties**.

### AppInsights API key

Provide an Azure App Insights API key. Learn how to [generate a new API key](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID). Only **Read** permission is required.

### AppInsights Application ID

To find this value, open Application Insights and navigate to **Configure** > **API Access**.

/Snip-->

Pour plus d’informations sur la façon de localiser ces valeurs, consultez [clés d’Application Insights](~/bot-service-resources-app-insights-keys.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Clés Application Insights](~/bot-service-resources-app-insights-keys.md)