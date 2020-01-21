---
title: Analyser les données de télémétrie à partir de votre service bot - Bot Service
description: Découvrez comment analyser le comportement d'un bot à l'aide de requêtes Kusto.
keywords: télémétrie, appinsights, bot monitor, Kusto, requêtes
author: WashingtonKayaker
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/10/2020
ms.openlocfilehash: a1762e79ab1524f05818d546d04c2e8a1df5fcdd
ms.sourcegitcommit: caaf394017dbdb1cfaba32e2d0a1e32c5ab71792
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75869829"
---
# <a name="analyze-your-bots-telemetry-data"></a>Analyser les données de télémétrie de votre bot 

## <a name="analyzing-bot-behavior"></a>Analyse du comportement du bot

La collection de requêtes suivante peut être utilisée pour analyser le comportement du bot. Vous pouvez utiliser la collection pour créer des requêtes personnalisées dans [Azure Monitor Log Analytics](https://aka.ms/log-analytics-azure-monitor), ainsi que des tableaux de bord de surveillance et de visualisation [PowerBI](https://aka.ms/power-bi-overview).

## <a name="prerequisites"></a>Conditions préalables requises
Il est utile de disposer de notions de base sur les concepts suivants :

* [Requêtes Kusto](https://aka.ms/Kusto-query-overview)

* Utiliser [Log Analytics](https://aka.ms/azure-monitor-log-queries-get-started) dans le portail Azure pour écrire des requêtes de journal Azure Monitor

* Concepts de base des [requêtes de journal](https://aka.ms/azure-monitor-log-queries-get-started) dans Azure Monitor

## <a name="dashboards"></a>Tableaux de bord
Les tableaux de bord Azure constituent un excellent moyen d’afficher et de partager les informations issues de vos requêtes.  Vous pouvez créer des tableaux de bord personnalisés pour surveiller l’activité de vos bots en associant vos requêtes aux vignettes que vous ajoutez à ces tableaux. Pour plus d’informations sur les tableaux de bord et la manière d’y associer vos requêtes, consultez [Créer et partager des tableaux de bord de données Log Analytics](https://aka.ms/log-analytics-create-share-dashboards). La suite de cet article présente des exemples de requêtes qui peuvent vous être utiles pour surveiller le comportement de vos bots.  

## <a name="example-kusto-queries"></a>Exemples de requêtes Kusto

> [!NOTE]
> Il est recommandé d'agir sur différentes dimensions telles que la période, le canal et les paramètres régionaux pour toutes les requêtes de cet article.

### <a name="number-of-users-per-period"></a>Nombre d’utilisateurs par période
Cet exemple produit un graphique en courbes qui montre le nombre d’utilisateurs distincts ayant communiqué quotidiennement avec votre bot au cours des 14 derniers jours.  La période peut être aisément modifiée en attribuant des valeurs différentes aux variables `queryStartDate`, `queryEndDate` et `interval`.

> [!IMPORTANT]
> Pour obtenir un nombre d'utilisateurs uniques correct dans cette requête, il doit s'agir d’utilisateurs authentifiés, et les résultats peuvent également dépendre des fonctionnalités du canal.

```Kusto
// number of users per period
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| summarize uc=dcount(user_Id) by bin(timestamp, groupByInterval)
| render timechart
```

> [!TIP]
> L'opérateur Kusto [summarize](https://aka.ms/kusto-summarize-operator) permet de générer une table qui agrège le contenu de la table d’entrée.
>
> La fonction [Bin](https://docs.microsoft.com/azure/kusto/query/binfunction) est une fonction scalaire Kusto qui, combinée à `summarize operator`, regroupe les résultats de la requête dans la valeur spécifiée. Dans l’exemple ci-dessus, elle est regroupée par jour. Kusto accepte également h = heures, m = minutes, s = secondes, ms = millisecondes, microseconde = microsecondes.
>
> L’opérateur [render](https://aka.ms/kusto-query-render-operator?pivots=Kusto) vous permet d’afficher facilement des graphiques, tels que le _graphique temporel_, un graphique en courbes où l’axe X correspond à DateHeure et où toute autre colonne numérique peut être utilisée pour l’axe Y. Il conserve automatiquement l'espacement de l'axe X, même si vos données n’ont pas été spécifiées à chaque fois.  Si aucune instruction render n’est utilisée, la valeur par défaut est `table`.

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

<!-- 

OPEN ISSUE: Is it possible to define a more descriptive title in the legend when drawing the graph (vs. “Count”)?  Or overall title on top or underneath?  

Use the render PropertyName parameter:  title, xtitle, ytitle,, legend

-->

![Nombre d’utilisateurs par période](./media/userscount.png)

<!--  

OPEN ISSUE 1:

    "Days with little interaction may indicate service health issues"

    This is an interesting statement – I’m not sure if there’s a way to overlay service health metrics over these metrics.  It should all be in App Insights.

OPEN ISSUE 2:

    Gary Pretty: Overlap for activity per period - Looks like we have a large overlap between ‘Activity per period’ and ‘Activity per user per period’ – given that activity per user is only really useful in some scenarios, such as authenticated, I think it might simplify things to update the ‘Activity per period’ query / instructions to include a note as to how to filter down per user?   Not sure about this one myself, but thought it was worth bringing up.

    < I agree with you.  One approach might be to include both in the same section, with the ‘Activity per period’ as the primary with a note about what might be considered more of a ‘special case’ (I will need input on more details of where this is most applicable – as I’ve commented in a previous email). >


QUESTION: What changes are required?

-->

### <a name="activity-per-period"></a>Activité par période
Cet exemple montre comment mesurer le volume d’activité par dimension souhaitée, qu’il s’agisse d’un nombre de conversations, de dialogues ou de messages par jour au cours des 14 derniers jours. La période peut être aisément modifiée en attribuant des valeurs différentes aux variables `querystartdate`, `queryEndDate` et `interval`. La dimension souhaitée est définie par la clause `extend` dans l’exemple suivant. Il est possible de définir `metric` sur _InstanceId_, _DialogId_ ou _ActivityId_.

Attribuez *metric* à la dimension que vous souhaitez afficher :
  * *InstanceId* mesure le nombre de [conversations](https://aka.ms/bot-builder-conversations)
  * *DialogId* mesure le nombre de [dialogues](https://aka.ms/bot-builder-concept-dialog)
  * *ActivityId* mesure le nombre de [messages](https://aka.ms/bot-rest-create-messages)

```Kusto
// measures the number of activity's (conversations, dialogs, messages) per period 
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['<InstanceId>'])
| extend DialogId = tostring(customDimensions['<DialogId>'])
| extend ActivityId = tostring(customDimensions['<activityId>'])
| where DialogId != '' and  InstanceId != '' and user_Id != ''
| extend metric = InstanceId // DialogId or ActivityId
| summarize Count=dcount(metric) by  bin(timestamp, groupByInterval)
| order by Count desc nulls last 
| render timechart
```

> [!TIP]
> L'opérateur Kusto [extend](https://aka.ms/kusto-extend-operator) permet de créer des colonnes calculées et de les ajouter au jeu de résultats.


#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![Activité par période](./media/convscount.PNG)


### <a name="activity-per-user-per-period"></a>Activité par utilisateur par période
Cet exemple montre comment compter le nombre d’activités par utilisateur par période. Il illustre l’exploration de la requête d'_activité par période_ pour se concentrer sur l’activité par utilisateur par période. Les activités incluent des dialogues, des conversations ou des messages.  Tous permettent de mesurer l’interaction de l’utilisateur avec votre bot et d'identifier d'éventuels problèmes, par exemple : 

- Les jours présentant un grand nombre d’activités effectuées par un seul utilisateur peuvent indiquer une attaque ou un test
- Les jours avec peu d’interaction peuvent indiquer des problèmes d’intégrité du service

> [!TIP]
> Vous pouvez supprimer _by user_Id_ pour obtenir le volume d'activité général du bot, qui peut être réorganisé sur le temps et les dialogues, les messages ou les conversations.

```Kusto
// number of users per period per dialogs
let queryStartDate = ago(14d);
let queryEndDate = now();
let interval = 6h;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['InstanceId'])
| extend DialogId = tostring(customDimensions['DialogId'])
| extend ActivityId = tostring(customDimensions['activityId'])
| where DialogId != '' and InstanceId != '' and user_Id != ''
| extend metric = ActivityId // InstanceId // DialogId // or InstanceId for conversation count
| summarize Count=dcount(metric) by user_Id, bin(timestamp, groupByInterval)
| order by Count desc nulls last 
```

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête 

| **user_Id**   | **timestamp**        | **Count** |
| ------------- | -------------------- | :------:  |
| User-8107ffd2 | 2019-09-03T00:00:00Z |    14     |
| User-75f2cc8f | 2019-08-30T00:00:00Z |    13     |
| User-75f2cc8d | 2019-09-03T00:00:00Z |    13     |
| User-3060aada | 2019-09-03T00:00:00Z |    10     |


### <a name="dialog-completion"></a>Achèvement de dialogue
Après avoir défini le client de télémétrie pour un dialogue, le dialogue (et ses enfants) émet des données de télémétrie, telles que _démarré_ et _terminé_. Cet exemple permet de mesurer les dialogues *terminés* par rapport aux dialogues *démarrés*.  Un nombre de dialogues démarrés supérieur au nombre de dialogues terminés indique que certains de vos utilisateurs ne terminent pas le flux de dialogue. Ce nombre peut servir de point de départ pour identifier et résoudre d'éventuels problèmes de logique de dialogue.  Ce nombre peut aussi être utilisé pour identifier les dialogues les plus populaires et les moins fréquents.

```Kusto
// % Completed Waterfall Dialog: shows completes relative to starts 
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name=="WaterfallStart"
| extend DialogId = customDimensions['DialogId']
| extend InstanceId = tostring(customDimensions['InstanceId'])
| join kind=leftouter (
    customEvents 
    | where name=="WaterfallComplete" 
    | extend InstanceId = tostring(customDimensions['InstanceId'])
  ) on InstanceId    
| summarize started=countif(name=='WaterfallStart'), completed=countif(name1=='WaterfallComplete') by tostring(DialogId)
| where started > 100  // filter for sample
// Show starts vs. completes
| project tostring(DialogId), started, completed
| order by started desc, completed asc  nulls last 
| render barchart  with (kind=unstacked, xcolumn=DialogId, ycolumns=completed, started, ysplit=axes) 
```

> [!TIP]
> L'opérateur Kusto [join](https://aka.ms/kusto-join-operator) permet de fusionner les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.
>
> L’opérateur [project](https://aka.ms/kusto-project-operator) permet de sélectionner les champs que vous souhaitez afficher dans votre sortie. À l’instar de `extend operator` qui ajoute un nouveau champ, `project operator` peut choisir parmi différents champs existants ou en ajouter un nouveau.

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![Achèvement de dialogue](./media/dialogwfratio.PNG)


### <a name="dialog-incompletion"></a>Non-achèvement de dialogue
Cet exemple peut être utilisé pour compter le nombre de flux de dialogue démarrés mais jamais terminés suite à une annulation ou un abandon au cours de la période spécifiée. Il permet de passer en revue les dialogues non achevés et de voir s’ils ont été annulés activement en raison de la confusion de l’utilisateur ou tout simplement abandonnés en raison d'un manque d'intérêt de celui-ci.

<!--  

OPEN ISSUE:

“If number of started dialogs is much greater than number of completed, users do not complete the dialog flow. Troubleshoot dialog logic.”

you can use the funnel view to understand where step dropoff is.  If there is a doc describing how to do that in our docs, point to it... 

QUESTION: Who can I talk to about a a doc describing this? 

ALSO: I removed what was line 6 in the example because it was a duplicate where statement: | where timestamp > period    // change timespan accordingly

-->

```Kusto
// show incomplete dialogs
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents 
| where timestamp > queryStartDate 
| where timestamp < queryEndDate
| where name == "WaterfallStart" 
| extend DialogId = customDimensions['DialogId']
| extend instanceId = tostring(customDimensions['InstanceId'])
| join kind=leftanti (
  customEvents
  | where name == "WaterfallComplete" 
  | extend instanceId = tostring(customDimensions['InstanceId'])
  ) on instanceId
| summarize cnt=count() by  tostring(DialogId)
| order by cnt
| render barchart
```

> [!TIP]
> L’opérateur Kusto [order](https://aka.ms/kusto-query-order-operator) (identique à `sort operator`) est utilisé pour trier les lignes de la table d’entrée sur une ou plusieurs colonnes.  Remarque : pour exclure les valeurs Null des résultats d’une requête, vous pouvez les filtrer dans votre instruction where. Par exemple, vous pouvez ajouter « and isnotnull(Timestamp) », ou pour renvoyer des valeurs Null au début ou à la fin, vous pouvez ajouter `nulls first` ou `nulls first` à la fin de l’instruction order. 
>

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![](./media/cancelleddialogs.PNG)





### <a name="dialog-sequence-drill-down"></a>Exploration de la séquence de dialogue 

#### <a name="waterfall-startstepcomplete-for-dialog-in-conversation"></a>Début/étape/fin en cascade d'un dialogue dans une conversation
Cet exemple illustre la séquence des étapes d'un dialogue, regroupées par conversation (instanceId). Il permet de déterminer les étapes entraînant une interruption du dialogue. 

Pour exécuter cette requête, entrez la valeur `DialogId` souhaitée à la place de \<SampleDialogId > 

```Kusto
// Drill down: Show waterfall start/step/complete for specific dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
let DialogActivity=(dlgid:string) {
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = customDimensions['DialogId']
| extend StepName = customDimensions['StepName']
| extend InstanceId = customDimensions['InstanceId']
| where DialogId == dlgid
| project timestamp, name, StepName, InstanceId 
| order by tostring(InstanceId), timestamp asc
};
// For example see SampleDialogId behavior
DialogActivity("<SampleDialogId>")
```

> [!TIP]
> Cette requête a été écrite à l’aide d’une [fonction définie par la requête](https://aka.ms/kusto-user-functions), à savoir une fonction définie par l’utilisateur et utilisée dans l’étendue d’une requête unique, et définie via une instruction let. Cette requête est écrite sans `query-defined function` :
>
> ```Kusto
> let queryStartDate = ago(14d);
> let queryEndDate = now();
> customEvents
> | where timestamp > queryStartDate
> | where timestamp < queryEndDate
> | extend DialogId = customDimensions['DialogId']
> | extend StepName = customDimensions['StepName']
> | extend InstanceId = customDimensions['InstanceId']
> | where DialogId == "<SampleDialogId>"
> | project timestamp, name, StepName, InstanceId 
> | order by tostring(InstanceId), timestamp asc
> ```

##### <a name="sample-query-results"></a>Résultats de l’exemple de requête

| **timestamp**       | **name**                   | **StepName**                    | **InstanceId**  |
| ------------------- | -------------------------- | ------------------------------- | --------------- |
| 2019-08-23T20:04... | WaterfallStart             | null                            | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | GetPointOfInterestLocations     | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | ProcessPointOfInterestSelection | ...79c0f03d8701 |
| 2019-08-23T20:04... | WaterfallStep              | GetRoutesToDestination          | ...79c0f03d8701 |
| 2019-08-23T20:05... | WaterfallStep              | ResponseToStartRoutePrompt      | ...79c0f03d8701 |
| 2019-08-23T20:05... | WaterfallComplete _<sup>1_ | null                            | ...79c0f03d8701 |
| 2019-08-28T23:35... | WaterfallStart             | null                            | ...6ac8b3211b99 |
| 2019-08-28T23:35... | WaterfallStep _<sup>2_     | GetPointOfInterestLocations     | ...6ac8b3211b99 |
| 2019-08-28T19:41... | WaterfallStart             | null                            | ...8137d76a5cbb |
| 2019-08-28T19:41... | WaterfallStep _<sup>2_     | GetPointOfInterestLocations     | ...8137d76a5cbb |
| 2019-08-28T19:41... | WaterfallStart             | null                            | ...8137d76a5cbb |

<sub>1</sup> _Terminé_ 

<sub>2</sup> _Abandonné_

_Interprétation : Les utilisateurs semblent abandonner la conversation à l’étape GetPointOfInterestLocations._ 

> [!NOTE] 
> Les dialogues en cascade exécutent une séquence (démarrer, plusieurs étapes, terminer). Une séquence affichant « start with no complete » indique que le dialogue a été interrompu suite à son abandon ou à son annulation par l'utilisateur. Ce comportement est visible dans l'analyse détaillée (voir étapes terminées et étapes abandonnées).

#### <a name="waterfall-startstepcompletecancel-steps-aggregate-totals"></a>Totaux agrégés des étapes en cascade démarrer/étape/terminer/annuler
Cet exemple montre les totaux agrégés du nombre total de démarrages d’une séquence de dialogue. Le nombre total combiné d’étapes en cascade, le nombre d'étapes terminées, annulées, ainsi que la différence entre _WaterfallStart_ et le total combiné _WaterfallComplete_ et _WaterfallCancel_ vous donne le nombre total d'étapes abandonnées.

```Kusto
// Drill down: Aggregate view of waterfall start/step/complete/cancel steps totals for specific dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
let DialogSteps=(dlgid:string) {
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = customDimensions['DialogId']
| where DialogId == dlgid
| project name
| summarize count() by name
};
// For example see SampleDialogId behavior
DialogSteps("<SampleDialogId>")
```

##### <a name="sample-query-results"></a>Résultats de l’exemple de requête

| **name**          | **count** |
| ----------------- | --------: |
| WaterfallStart    | 21        |
| WaterfallStep     | 47        |
| WaterfallComplete | 11        |
| WaterfallCancel   | 1         |

_Interprétation : Sur 21 appels de séquence de dialogue, seuls 11 ont été terminés, 9 ont été abandonnés et un a été annulé par l'utilisateur_



### <a name="average-duration-in-dialog"></a>Durée moyenne du dialogue
Cet exemple mesure la durée moyenne que les utilisateurs passent dans un dialogue donné. Un long laps de temps dans un dialogue peut suggérer des possibilités de simplification.

 ```Kusto
// Average dialog duration
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name=="WaterfallStart"
| extend DialogId = customDimensions['DialogId']
| extend instanceId = tostring(customDimensions['InstanceId'])
| join kind=leftouter (customEvents | where name=="WaterfallCancel" | extend instanceId = tostring(customDimensions['InstanceId'])) on instanceId 
| join kind=leftouter (customEvents | where name=="WaterfallComplete" | extend instanceId = tostring(customDimensions['InstanceId'])) on instanceId 
| extend duration = case(not(isnull(timestamp1)), timestamp1 - timestamp, 
not(isnull(timestamp2)), timestamp2 - timestamp, 0s) // Abandoned are not counted. Alternate: now()-timestamp)
| extend seconds = round(duration / 1s)
| summarize AvgSeconds=avg(seconds) by tostring(DialogId)
| order by AvgSeconds desc nulls last 
| render barchart with (title="Duration in Dialog")
 ```

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![dialogduration](./media/dialogduration.PNG)


### <a name="average-steps-in-dialog"></a>Étapes moyennes dans un dialogue
Cet exemple montre la « longueur » de chaque dialogue, calculée par écart moyen, min, max et standard. Il permet d'analyser la qualité du dialogue. Par exemple :

- les dialogues présentant de nombreuses étapes doivent être évalués à des fins de simplification
- Les dialogues présentant un écart important en termes de min/max/moyenne peuvent indiquer que les utilisateurs sont bloqués lorsqu'ils tentent d'effectuer des tâches. Vous devrez peut-être évaluer la possibilité de chemins plus courts pour effectuer les tâches ou trouver des moyens de réduire la complexité des dialogues.
- Les dialogues présentant d'importants écarts standard suggèrent des chemins complexes ou une expérience interrompue (abandon/annulation)
- Les dialogues présentant très peu d'étapes peuvent indiquer des dialogues jamais terminés. L’analyse des taux d’achèvement et d’abandon peut permettre de le déterminer.  

```Kusto
// min/max/std/avg steps per dialog
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend DialogId = tostring(customDimensions['DialogId'])
| extend StepName = tostring(customDimensions['StepName'])
| extend InstanceId = tostring(customDimensions['InstanceId'])
| where name == "WaterfallStart" or  name == "WaterfallStep" or  name == "WaterfallComplete" 
| order by InstanceId, timestamp asc
| project timestamp, DialogId, name, InstanceId, StepName 
| summarize cnt=count() by InstanceId, DialogId
| summarize avg=avg(cnt), minsteps=min(cnt),maxsteps=max(cnt), std=stdev(cnt) by DialogId
| extend avgsteps = round(avg, 1)
| extend avgshortbysteps=maxsteps-avgsteps
| extend avgshortbypercent=round((1.0 - avgsteps/maxsteps)*100.0, 1)
| project DialogId, avgsteps, minsteps, maxsteps, std, avgshortbysteps, avgshortbypercent
| order by std desc nulls last 
```

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

| Dialog Id               | avg steps | min steps | max steps | std  | avg short by steps | avg short by percent |
| ----------------------- | --------: | :-------: | :-------: | ---: | :----------------: | -------------------: |
| FindArticlesDialog      | 6.2       | 2         | 7         | 2,04 | 0,8                | 11,4 %                |
| CreateTicket            | 4.3       | 2         | 5         | 1.5  | 0,7                | 14 %                  |
| CheckForCurrentLocation | 3.9       | 2         | 5         | 1,41 | 1.1                | 22 %                  |
| BaseAuth                | 3.3       | 2         | 4         | 1,03 | 0,7                | 17,5 %                |
| intégration              | 2.7       | 2         | 4         | 0,94 | 1.3                | 32,5 %                |

__Interpretation : Par exemple, FindArticlesDialog présente une répartition étendue entre min/max et doit être examiné, voire repensé et optimisé.



### <a name="channel-activity-by-activity-metric"></a>Activité de canal par mesure d’activité
Cet exemple mesure la quantité d’activité que votre bot reçoit par canal au cours de la période donnée. Pour ce faire, il compte l’une des métriques suivantes : messages entrants, utilisateurs, conversations ou dialogues. Cela peut être utile pour analyser l’intégrité du service ou mesurer la popularité des canaux.


```Kusto
// number of metric: messages, users, conversations, dialogs by channel
let queryStartDate = ago(14d);
let queryEndDate = now();
let groupByInterval = 1d;
customEvents 
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| extend InstanceId = tostring(customDimensions['InstanceId'])
| extend DialogId = tostring(customDimensions['DialogId'])
| extend ActivityId = tostring(customDimensions['activityId'])
| extend ChannelId = tostring(customDimensions['channelId'])
| where DialogId != '' and  InstanceId != '' and user_Id != ''
| extend metric = user_Id // InstanceId or ActivityId or user_Id
| summarize Count=count(metric) by  ChannelId, bin(timestamp, groupByInterval)
| order by Count desc nulls last 
| render barchart with (title="Users", kind=stacked) // or Incoming Messages or Conversations or Users
```

> [!TIP]
> Vous pouvez envisager d’essayer ces variations :
> - Exécutez la requête sans compartimentage timestamp : `bin(timestamp, groupByInterval)`
> - Vous pouvez également utiliser `dcount` pour les utilisateurs distincts et `count` pour toutes les activités d’événements d'utilisateur.  Cela fonctionne également pour les utilisateurs récurrents.
> 

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![ChannelsUsage](./media/ChannelsUsage.PNG)

 _Interprétation : les tests de l’émulateur étaient les plus populaires, mais désormais, DirectLineSpeech est le canal le plus populaire._

<!--  

Open Issue: More interesting than the “certainty” score would be linking intent to dialog completion %. That infers “certainty” by user’s actions. 

QUESTION: What changes are required?

-->
### <a name="total-intents-by-popularity"></a>Nombre total d’intentions par popularité
Cet exemple s’applique aux bots activés par LUIS. Il affiche un résumé de toutes les [intentions](https://aka.ms/botbuilder-luis-concept#recognize-intent) par popularité, ainsi que le score de certitude avec détection d'intention correspondant.

* Dans la pratique, l'affichage doit être séparé pour chaque métrique.
* Les chemins d’intention populaires doivent être optimisés pour l’expérience utilisateur.
* Les scores moyens faibles indiquent une mauvaise reconnaissance et l'absence possible de réelle intention de l'utilisateur.

```Kusto
// show total intents
let queryStartDate = ago(14d);
let queryEndDate = now();
customEvents
| where timestamp > queryStartDate
| where timestamp < queryEndDate
| where name startswith "LuisResult" 
| extend intentName = tostring(customDimensions['intent'])
| extend intentScore = todouble(customDimensions['intentScore'])
| summarize ic=count(), ac=avg(intentScore)*100 by intentName
| project intentName, ic, ac
| order by ic desc nulls last 
| render barchart with (kind=unstacked, xcolumn=intentName, ycolumns=ic,ac, title="Intents Popularity")
```

#### <a name="sample-query-results"></a>Résultats de l’exemple de requête

![IntentPopularity](./media/Telemetry/IntentPopularity.PNG)

_Interprétation : par exemple, l’intention la plus populaire, confirm, est détectée avec une confiance moyenne de seulement 23 %._


> [!TIP]
> Les graphiques à barres font partie des différentes options disponibles avec les requêtes Kusto.  Parmi les autres options, figurent notamment : anomalychart, areachart, columnchart, linechart, scatterchart. Pour plus d’informations, consultez la rubrique [Opérateur render](https://aka.ms/kusto-query-render-operator?pivots=Kusto).


## <a name="schema-of-bot-analytics-instrumentation"></a>Schéma d'instrumentation d'analytique de bot
Les tableaux suivants présentent les champs les plus courants dans lesquels votre bot doit enregistrer les données de télémétrie.

### <a name="general-envelope"></a>Enveloppe générale

Champs d'analytique des journaux d'activité dans l'instrumentation Application Insights.

| **Champ**        | **Description**        | **Exemples de valeurs**                                            |
| ---------------- | ---------------------- | ------------------------------------------------------------ |
| name             | type de message           | BotMessageSend, BotMessageReceived, LuisResult, WaterfallStep, WaterfallStart, SkillWebSocketProcessRequestLatency, SkillWebSocketOpenCloseLatency, WaterfallComplete, QnaMessage, WaterfallCancel, SkillWebSocketTurnLatency, AuthPromptValidatorAsyncFailure |
| customDimensions | Analytique de bot - Kit de développement logiciel (SDK)      | activityId=\<id>,  activityType=message,  channelId=emulator,  fromId=\<id>,  fromName=User,  locale=en-us,  recipientId=\<id>,  recipientName=Bot,  text=find  a coffee shop |
| timestamp        | Heure de l’événement          | 2019-09-05T18:32:45.287082Z                                  |
| instance_Id      | ID de conversation        | f7b2c416-a680-4b2c-b4cc-79c0f03d8711                         |
| operation_Id     | ID d'activation                | 084b2856947e3844a5a18a8476d99aaa                             |
| user_Id          | ID utilisateur de canal unique | emulator7c259c8e-2f47...                                     |
| client_IP        | Adresse IP du client      | 127.0.0.1 (peut être absent en raison d'un blocage de confidentialité)               |
| client_City      | Ville du client            | Redmond (si détectée, peut être absente)                         |

### <a name="custom-dimensions"></a>Dimensions personnalisées

La plupart des données d’activité spécifiques au bot sont stockées dans le champ _customDimensions_.

| **Champ**     | **Description**      | **Exemples de valeurs**                                 |
| ------------- | -------------------- | ------------------------------------------------- |
| activityId    | ID du message           | \<id>: 8da6d750-d00b-11e9-80e0-c14234b3bc2a       |
| activityType  | Type de message      | message, conversationUpdate, event, invoke       |
| channelId     | Identificateur du canal   | emulator, directline, msteams, webchat           |
| fromId        | Identificateur de l'expéditeur      | \<id>                                             |
| fromName      | Nom d’utilisateur du client | John Bonham, Keith Moon, Steve Smith,  Steve Gadd |
| locale        | Paramètres régionaux d’origine du client | en-us, zh-cn, en-GB, de-de, zh-CN                |
| recipientId   | Identificateur du destinataire | \<id>                                             |
| recipientName | Nom du destinataire       | John Bonham, Keith Moon, Steve Smith,  Steve Gadd |
| text          | Texte du message      | trouver un café                                |

### <a name="custom-dimensions-luis"></a>Dimensions personnalisées : LUIS

L’instrumentation LUIS stocke ses données dans les champs Dimensions personnalisées suivants.

| **Champ**      | **Description**         | **Exemples de valeurs**                                       |
| -------------- | ----------------------- | ------------------------------------------------------- |
| intention         | Intention détectée par LUIS    | pointOfInterestSkill                                    |
| intentScore    | Score de reconnaissance LUIS  | 0.98                                                    |
| Entités       | Entités détectées par LUIS  | FoodOfGrocery =  [["coffee"]], KEYWORD= ["coffee shop"] |
| Question       | Question détectée par LUIS  | trouver un café                                      |
| sentimentLabel | Sentiment détecté par LUIS | positif                                                |

### <a name="custom-dimensions-qnamaker"></a>Dimensions personnalisées : QnAMaker

L’instrumentation QnAMaker stocke ses données dans les champs Dimensions personnalisées suivants.

| **Champ**       | **Description**            | **Exemples de valeurs**                                            |
| --------------- | -------------------------- | ------------------------------------------------------------ |
| question        | Question détectée par QnA      | que pouvez-vous faire ?                                             |
| répondre          | Réponse QnA                 | Vous avez des questions, j’ai peut-être des réponses.                     |
| articleFound    | QnA                        | true                                                         |
| questionId      | ID de la question QnA            | 488                                                          |
| knowledgeBaseId | ID de la base de connaissances QnA                  | 2a4936f3-b2c8-44ff-b21f-67bc413b9727                         |
| matchedQuestion | Tableau des questions correspondantes | [« Pouvez-vous m’expliquer votre rôle ? », « Pouvez-vous me parler un peu de vous ? », « Pouvez-vous me parler de vous ? », « Pourriez-vous m’aider », « Humm, que pouvez-vous faire ?», « En quoi pouvez-vous m'aider », « En quoi pouvez-vous m'aider ?». , « En quoi pouvez-vous m'être utile pour mes projets ? », « Parlez-vous de votre capacité », « De quoi êtes-vous capable ? »,... |

 

## <a name="see-also"></a>Voir aussi

* Pour un tutoriel sur l'écriture de requêtes de journal, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](https://aka.ms/azure-monitor-log-queries-get-started).
* [Visualisation des données à partir d’Azure Monitor](https://aka.ms/azure-monitor-visualize-data)
* Découvrez comment [Ajouter des données de télémétrie à votre bot](https://aka.ms/AddBotTelemetry)
* En savoir plus sur les [requêtes dans les journaux Azure Monitor](https://aka.ms/azure-monitor-log-queries)
* [Créer et partager des tableaux de bord de données Log Analytics](https://aka.ms/log-analytics-create-share-dashboards)
