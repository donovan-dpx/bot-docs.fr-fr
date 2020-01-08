---
title: Résoudre les erreurs HTTP 500 de bot | Microsoft Docs
description: Comment résoudre les erreurs HTTP 500 dans un bot déployé.
keywords: résoudre les problèmes, dépanner, HTTP 500, problèmes.
author: jonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/19/2019
ms.openlocfilehash: bed93200a7ea79f5d5fd8c4e698445b3f431e56e
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75491581"
---
# <a name="troubleshoot-http-500-errors"></a>Résoudre les erreurs HTTP 500

La première étape de résolution des erreurs 500 active Application Insights.

<!-- TODO: Add links back in once there's a fresh AppInsights sample.
The luis-with-appinsights ([C# sample](https://aka.ms/cs-luis-with-appinsights-sample) / [JS sample](https://aka.ms/js-luis-with-appinsights-sample)) and qna-with-appinsights ([C# sample](https://aka.ms/qna-with-appinsights) / [JS sample](https://aka.ms/js-qna-with-appinsights-sample)) samples demonstrate bots that support Azure Application Insights.
-->
Consultez [Télémétrie d’analyse de conversation](https://aka.ms/botframeworkanalytics) pour obtenir des informations sur la façon d’ajouter Application Insights à un bot existant.

## <a name="enable-application-insights-on-aspnet"></a>Activer Application Insights sur ASP.Net

Pour une prise en charge de base d’Application Insights, consultez [Configurer Application Insights pour votre site web ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net). Le Bot Framework (à compter de v4.2) offre un niveau supérieur de télémétrie Application Insights, mais il n’est pas obligatoire pour diagnostiquer les erreurs HTTP 500.

## <a name="enable-application-insights-on-nodejs"></a>Activer Application Insights sur Node.js

Pour une prise en charge de base d’Application Insights, consultez [Surveiller vos services et applications Node.js avec Application Insights](https://docs.microsoft.com/azure/azure-monitor/learn/nodejs-quick-start). Le Bot Framework (à compter de v4.2) offre un niveau supérieur de télémétrie Application Insights, mais il n’est pas obligatoire pour diagnostiquer les erreurs HTTP 500.

## <a name="query-for-exceptions"></a>Rechercher les exceptions

La méthode la plus simple pour analyser des erreurs 500 de code d’état HTTP consiste à commencer par les exceptions.

Les requêtes suivantes vous donneront les exceptions les plus récentes :

```sql
exceptions
| order by timestamp desc
| project timestamp, operation_Id, appName
```

Avec la première requête, sélectionnez quelques-uns des ID d’opération et recherchez plus d’informations :

```sql
let my_operation_id = "d298f1385197fd438b520e617d58f4fb";
let union_all = () {
    union
    (traces | where operation_Id == my_operation_id),
    (customEvents | where operation_Id == my_operation_id),
    (requests | where operation_Id == my_operation_id),
    (dependencies | where operation_Id  == my_operation_id),
    (exceptions | where operation_Id == my_operation_id)
};

union_all
    | order by timestamp desc
```

Si vous avez seulement `exceptions`, analysez les détails et regardez s’ils correspondent aux lignes de votre code. Si vous voyez seulement des exceptions provenant du connecteur de canal (`Microsoft.Bot.ChannelConnector`), consultez [Aucun événement Application Insights](#no-application-insights-events) pour vérifier qu’Application Insights est correctement configuré et que votre code journalise les événements.

## <a name="no-application-insights-events"></a>Aucun événement Application Insights

Si vous recevez des erreurs 500 sans aucun autre événement de votre bot signalé dans Application Insights, vérifiez les points suivants :

### <a name="ensure-bot-runs-locally"></a>Vérifier que le bot s’exécute localement

Vérifiez que votre bot s’exécute d’abord localement avec l’émulateur.

### <a name="ensure-configuration-files-are-being-copied-net-only"></a>Assurez-vous que les fichiers de configuration sont en cours de copie (.NET uniquement)

Vérifiez que le fichier `appsettings.json` et tous les autres fichiers de configuration sont correctement empaquetés pendant le processus de déploiement.

#### <a name="application-assemblies"></a>Assemblys d’application

Vérifiez que les assemblys Application Insights sont correctement empaquetés pendant le processus de déploiement.

- Microsoft.ApplicationInsights
- Microsoft.ApplicationInsights.TraceListener
- Microsoft.AI.Web
- Microsoft.AI.WebServer
- Microsoft.AI.ServeTelemetryChannel
- Microsoft.AI.PerfCounterCollector
- Microsoft.AI.DependencyCollector
- Microsoft.AI.Agent.Intercept

Vérifiez que le fichier `appsettings.json` et tous les autres fichiers de configuration sont correctement empaquetés pendant le processus de déploiement.

#### <a name="appsettingsjson"></a>appsettings.json

Dans votre fichier `appsettings.json`, vérifiez que la clé d'instrumentation est définie.

## <a name="aspnet-web-apitabdotnetwebapi"></a>[API web ASP.NET](#tab/dotnetwebapi)

```json
{
    "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
            "Default": "Debug",
            "System": "Information",
            "Microsoft": "Information"
        },
        "Console": {
            "IncludeScopes": "true"
        }
    }
}
```

## <a name="aspnet-coretabdotnetcore"></a>[ASP.NET Core](#tab/dotnetcore)

```json
{
    "ApplicationInsights": {
        "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
}
```

---

### <a name="verify-config-file"></a>Vérifier le fichier de configuration

Vérifiez qu’une clé Application Insights se trouve dans votre fichier de configuration.

```json
{
    "ApplicationInsights": {
        "type": "appInsights",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "resourceGroup": "my resource group",
        "name": "my appinsights name",
        "serviceName": "my service name",
        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "applicationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "apiKeys": {},
        "id": ""
    }
},
```

### <a name="check-logs"></a>Inspecter les journaux d’activité

ASP.Net et Node émettent des journaux d’activité au niveau serveur que vous pouvez inspecter.

#### <a name="set-up-a-browser-to-watch-your-logs"></a>Configurer un navigateur pour consulter vos journaux d’activité

1. Ouvrez votre bot dans le [portail Azure](https://portal.azure.com/).
1. Ouvrez la page **Paramètres App Service / Tous les paramètres App Service** pour voir tous les paramètres de service.
1. Ouvrez la page **Supervision / Journaux de diagnostic** du service d’application.
   - Vérifiez que **Journal des applications (Filesystem)** est activé. Veillez à cliquer sur **Enregistrer** si vous changez ce paramètre.
1. Basculez sur la page **Supervision / Flux de journaux**.
   - Sélectionnez **Journaux d’activité du serveur web** et vérifiez qu’un message vous dit que vous êtes connecté. La commande doit ressembler à ceci :

     ```bash
     Connecting...
     2018-11-14T17:24:51  Welcome, you are now connected to log-streaming service.
     ```

     Gardez cette fenêtre ouverte.

#### <a name="set-up-browser-to-restart-your-bot-service"></a>Configurer un navigateur pour redémarrer votre service de bot

1. À l’aide d’un autre navigateur, ouvrez votre bot dans le portail Azure.
1. Ouvrez la page **Paramètres App Service / Tous les paramètres App Service** pour voir tous les paramètres de service.
1. Basculez sur la page **Vue d’ensemble** du service d’application et cliquez sur **Redémarrer**.
   - Un message vous demande si vous êtes sûr, sélectionnez **Oui**.
1. Revenez dans la première fenêtre du navigateur et consultez les journaux d’activité.
1. Vérifiez que vous recevez de nouveaux journaux d’activité.
   - Si vous ne voyez aucune activité, redéployez votre bot.
   - Basculez ensuite sur la page **Journaux des applications** et recherchez les erreurs.
