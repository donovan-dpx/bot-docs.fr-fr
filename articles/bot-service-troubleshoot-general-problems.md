---
title: Résolution des problèmes des robots | Microsoft Docs
description: Résoudre les problèmes généraux dans le développement des robots à l’aide des forums aux questions techniques.
author: DeniseMak
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 09/17/2019
ms.openlocfilehash: d958f2a9f85af4a42dab241264115050f1972d4d
ms.sourcegitcommit: a547192effb705e4c7d82efc16f98068c5ba218b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490844"
---
# <a name="troubleshooting-general-problems"></a>Résolution des problèmes généraux
Les forums aux questions permettent de résoudre les problèmes les plus courants de développement et de fonctionnement des robots.

## <a name="how-can-i-troubleshoot-issues-with-my-bot"></a>Comment puis-je résoudre les problèmes de mon robot ?

1. Déboguez le code source de votre bot avec [Visual Studio Code](debug-bots-locally-vscode.md) ou [Visual Studio](https://docs.microsoft.com/visualstudio/debugger/navigating-through-code-with-the-debugger?view=vs-2017).
1. Testez votre robot à l’aide de l’[émulateur](bot-service-debug-emulator.md) avant de le déployer sur le cloud.
1. Déployez votre bot sur une plateforme d’hébergement cloud comme Azure, puis testez la connexion à votre bot en utilisant le contrôle Web Chat intégré sur le tableau de bord de votre bot dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>. Si vous rencontrez des problèmes avec votre bot après l’avoir déployé dans Azure, consultez cet article de blog : [Understanding Azure troubleshooting and support](https://azure.microsoft.com/blog/understanding-azure-troubleshooting-and-support/).
1. Écartez l’[authentification][TroubleshootingAuth] comme problème possible.
1. Testez votre robot sur Skype. Vous pourrez ainsi valider l’expérience utilisateur de bout en bout.
1. Pensez à tester votre robot sur des canaux ayant des exigences d’authentification supplémentaires, par exemple Direct Line ou Web Chat.
1. Consultez la procédure [Déboguer un bot](bot-service-debug-bot.md) ainsi que les autres articles de débogage de cette section.

## <a name="how-can-i-troubleshoot-authentication-issues"></a>Comment puis-je résoudre les problèmes d’authentification ?

Pour en savoir plus sur la résolution des problèmes d’authentification de votre bot, consultez l’article sur la [résolution des problèmes][TroubleshootingAuth] d’authentification Bot Framework.

## <a name="im-using-the-bot-framework-sdk-for-net-how-can-i-troubleshoot-issues-with-my-bot"></a>J’utilise le kit SDK Bot Framework pour .NET. Comment puis-je résoudre les problèmes de mon robot ?

**Recherche des exceptions.**  
Dans Visual Studio 2017, accédez à **Débogage** > **Windows** > **Paramètres d’exception**. Dans la fenêtre **Paramètres des exceptions**, cochez la case **Pause au lancement** en regard de **Exceptions Common Language Runtime**. Vous pouvez également afficher le résultat du diagnostic dans votre fenêtre de sortie lorsque des exceptions sont lancées ou non prises en charge.

**Vérifier la pile d’appels.**  
Dans Visual Studio, vous pouvez choisir de déboguer [Uniquement mon code](https://msdn.microsoft.com/library/dn457346.aspx) ou non. L’examen de toute la pile d’appels peut fournir des renseignements supplémentaires sur les problèmes.

**Assurez-vous que toutes les méthodes de dialogue se terminent par un programme de traitement du message suivant.**  
Toutes les étapes du dialogue doivent alimenter l’étape suivante de la cascade ou mettre fin au dialogue actuel pour sortir de la pile. Si une étape n’est pas gérée correctement, la conversation ne continuera pas comme prévu. Consultez l’article de présentation de concept sur les [dialogues](v4sdk/bot-builder-concept-dialog.md) pour en savoir plus à ce sujet.

## <a name="why-doesnt-the-typing-activity-do-anything"></a>Pourquoi l’activité de saisie ne fait-elle rien ?
Certains canaux ne prennent pas en charge les mises à jour transitoires de la saisie dans leur client.

## <a name="what-is-the-difference-between-the-connector-library-and-builder-library-in-the-sdk"></a>Quelle est la différence entre la bibliothèque du connecteur et la bibliothèque Builder dans le kit de développement logiciel ?

La bibliothèque du connecteur correspond à la présentation de l’API REST. La bibliothèque Builder intègre le modèle de programmation du dialogue conversationnel et d’autres fonctions telles que les invites, les cascades, les chaînes et les formulaires guidés. La bibliothèque Builder donne également accès à des services cognitifs comme LUIS.

## <a name="what-causes-an-error-with-http-status-code-429-too-many-requests"></a>Qu’est-ce qui provoque une erreur avec le code d’état HTTP 429 « Trop de requêtes » ?

Une réponse d’erreur avec le code d’état HTTP 429 indique qu’un trop grand nombre de requêtes ont été envoyées dans un laps de temps donné. Le corps de la réponse doit comprendre une explication du problème. Il peut également préciser l’intervalle minimal requis entre les requêtes. Une source possible de cette erreur est [ngrok](https://ngrok.com/). Si vous avez un plan gratuit et que vous rencontrez les limites de ngrok, accédez à la page de la tarification et des limites sur leur site Web pour obtenir davantage d’[options](https://ngrok.com/product#pricing). 

## <a name="why-arent-my-bot-messages-getting-received-by-the-user"></a>Pourquoi les messages de mon bot ne sont pas bien reçus par l’utilisateur ?

L’activité de message générée en réponse doit être adressée correctement, sinon il n’arrivera pas à sa destination prévue. Dans la grande majorité des cas, vous ne devrez pas gérer cette situation explicitement ; le SDK s’occupe d’adresser l’activité de message pour vous. 

L’adressage correct d’une activité implique l’intégration des informations appropriées de *référence Conversation* ainsi que des informations sur l’expéditeur et le destinataire. Dans la plupart des cas, l’activité de message est envoyée en réponse à l’arrivée d’une autre activité. Par conséquent, les informations d’adressage peuvent être récupérées à partir de l’activité entrante. 

Si vous examinez les traces ou les journaux d’audit, vous pouvez vérifier que vos messages sont correctement adressés. Dans le cas contraire, définissez un point d’arrêt dans votre bot et vérifiez où les ID sont définis pour votre message.

## <a name="how-can-i-run-background-tasks-in-aspnet"></a>Comment puis-je exécuter des tâches en arrière-plan dans ASP.NET ? 

Dans certains cas, il peut arriver que vous souhaitiez lancer une tâche asynchrone qui attende quelques secondes avant d’exécuter un code afin d’effacer le profil de l’utilisateur ou de réinitialiser l’état de la conversation ou du dialogue. Pour en savoir plus sur la façon de procéder, consultez [Comment exécuter des tâches en arrière-plan dans ASP.NET ?](https://www.hanselman.com/blog/HowToRunBackgroundTasksInASPNET.aspx). Pensez notamment à utiliser [HostingEnvironment.QueueBackgroundWorkItem](https://msdn.microsoft.com/library/dn636893(v=vs.110).aspx). 


## <a name="how-do-user-messages-relate-to-https-method-calls"></a>Comment les messages utilisateur sont-ils liés aux appels de la méthode HTTPS ?

Lorsque l’utilisateur envoie un message sur un canal, le service web Bot Framework envoie une PUBLICATION HTTPS au point de terminaison du service web du robot. Le robot peut envoyer zéro, un ou plusieurs messages à l’utilisateur sur ce canal, en transmettant une PUBLICATION HTTPS distincte au Bot Framework pour chaque message envoyé.

## <a name="my-bot-is-slow-to-respond-to-the-first-message-it-receives-how-can-i-make-it-faster"></a>Mon robot met du temps à répondre au premier message qu’il reçoit. Comment le rendre plus rapide ?

Les robots sont des services web et certaines plates-formes d’hébergement comme Azure mettent automatiquement le service en veille lorsqu’il ne reçoit pas de trafic pendant un certain temps. Dans ce cas, votre robot doit repartir de zéro la prochaine fois qu’il reçoit un message, ce qui allonge considérablement son temps de réponse par rapport au cas où il est déjà en cours d’exécution.

Certaines plates-formes d’hébergement permettent de configurer le service de façon à ne pas le mettre en veille. Pour paramétrer cela dans Azure, accédez au service de votre robot dans le [portail Azure](https://portal.azure.com), sélectionnez **Paramètres d’application**, puis **Toujours activé**. Cette option est disponible dans la plupart des plans de service, mais pas tous.

## <a name="how-can-i-guarantee-message-delivery-order"></a>Comment puis-je garantir l’ordre de livraison des messages ?

Bot Framework conserve autant que possible l’ordre des messages. Par exemple, si vous envoyez le message A et que vous attendez la fin de cette opération HTTP avant de lancer une autre opération HTTP pour envoyer le message B, Bot Framework comprend automatiquement que le message A doit précéder le message B. Cependant, l’ordre de livraison du message ne peut généralement pas être garanti puisque le canal se charge en définitive de la livraison du message et peut réordonner les messages.

## <a name="how-can-i-intercept-all-messages-between-the-user-and-my-bot"></a>Comment puis-je intercepter tous les messages entre l’utilisateur et mon robot ?

Le kit SDK Bot Framework pour.NET permet de fournir des implémentations des interfaces `IPostToBot` et `IBotToUser` au conteneur d’injection de dépendances `Autofac`. En utilisant le kit SDK Bot Framework pour n’importe quel langage, vous pouvez utiliser le middleware pratiquement dans le même but. Le référentiel [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure) contient des bibliothèques C# et Node.js qui enregistreront ces données dans une table Azure.

## <a name="why-are-parts-of-my-message-text-being-dropped"></a>Pourquoi certaines parties du texte de mon message sont-elles supprimées ?

Bot Framework et de nombreux canaux interprètent le texte comme s’il était formaté avec [Markdown](https://en.wikipedia.org/wiki/Markdown). Vérifiez si votre texte contient des caractères susceptibles d'être interprétés comme de la syntaxe Markdown.

## <a name="how-can-i-support-multiple-bots-at-the-same-bot-service-endpoint"></a>Comment puis-je prendre en charge plusieurs robots sur un même point de terminaison de service de robot ? 

Cet [exemple](https://github.com/Microsoft/BotBuilder/issues/2258#issuecomment-280506334) montre comment configurer `Conversation.Container` avec le bon `MicrosoftAppCredentials` et utiliser un seul `MultiCredentialProvider` pour authentifier plusieurs identifiants d’applications et mots de passe.

## <a name="identifiers"></a>Identificateurs

## <a name="how-do-identifiers-work-in-the-bot-framework"></a>Comment fonctionnent les identificateurs dans Bot Framework ?

Pour en savoir plus sur les identificateurs dans Bot Framework, consultez le [guide Bot Framework des identificateurs][BotFrameworkIDGuide].

## <a name="how-can-i-get-access-to-the-user-id"></a>Comment puis-je accéder à l’identifiant utilisateur ?

Les SMS et les messages électroniques fournissent l’identifiant utilisateur brut dans la propriété `from.Id`. Dans les messages Skype, la propriété `from.Id` contient un identifiant utilisateur unique qui est différent de l’identifiant Skype de l’utilisateur . Si vous devez vous connecter à un compte existant, vous pouvez utiliser une carte de connexion et implémenter votre propre flux OAuth pour associer l’identifiant utilisateur à l’identifiant utilisateur de votre propre service.

## <a name="why-are-my-facebook-user-names-not-showing-anymore"></a>Pourquoi mes noms d’utilisateur Facebook ne s’affichent-ils plus ?

Avez-vous modifié votre mot de passe Facebook ? Le jeton d’accès sera alors invalidé, et vous devrez mettre à jour les paramètres de configuration de votre bot pour le canal Facebook Messenger dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

## <a name="why-is-my-kik-bot-replying-im-sorry-i-cant-talk-right-now"></a>Pourquoi mon robot Kik répond-il « Désolé, je ne peux pas parler maintenant » ?

Les robots en développement sur Kik ne peuvent compter qu’un maximum de 50 abonnés. Une fois que 50 utilisateurs différents sont connectés à votre robot, tout nouvel utilisateur qui essaie de discuter en ligne avec lui reçoit le message « Désolé, je ne peux pas parler maintenant ». Pour plus d’informations, consultez la [documentation de Kik](https://botsupport.kik.com/hc/articles/225764648-How-can-I-share-my-bot-with-Kik-users-while-in-development-).

## <a name="how-can-i-use-authenticated-services-from-my-bot"></a>Comment puis-je utiliser les services authentifiés de mon robot ?

Pour l’authentification Azure Active Directory, consultez l’ajout d’authentification [V3](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-3.0&tabs=csharp) | [V4](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-authentication?view=azure-bot-service-4.0&tabs=csharp). 

> [!NOTE] 
> Si vous ajoutez des fonctionnalités d’authentification et de sécurité à votre robot, vous devez vous assurer que les modèles que vous implémentez dans votre code respectent les normes de sécurité appropriées à votre application.

## <a name="how-can-i-limit-access-to-my-bot-to-a-pre-determined-list-of-users"></a>Comment puis-je limiter l’accès à mon robot à une liste prédéterminée d’utilisateurs ?

Certains canaux, comme les SMS et le courrier électronique, fournissent des adresses non limitées. Dans ces cas-là, les messages de l’utilisateur contiennent l’identifiant utilisateur brut dans la propriété `from.Id`.

D’autres canaux, comme Skype, Facebook et Slack, fournissent des adresses délimitées ou locataires de telle sorte qu’un robot ne peut pas connaître à l’avance l’identifiant des utilisateurs. Dans ces cas-là, vous devez authentifier l’utilisateur via un lien de connexion ou une clé secrète partagée afin de déterminer s’il est autorisé ou non à utiliser le robot.

## <a name="why-does-my-direct-line-11-conversation-start-over-after-every-message"></a>Pourquoi ma conversation Direct Line 1.1 recommence-t-elle après chaque message ?

Si votre conversation Direct Line semble recommencer après chaque message, la propriété `from` est probablement manquante ou à `null` dans les messages que votre client Direct Line envoie au robot. Lorsqu’un client Direct Line envoie un message avec la propriété `from` manquante ou à `null`, le service Direct Line attribue automatiquement un identifiant afin que chaque message envoyé par le client semble provenir d’un nouvel utilisateur.

Pour corriger cela, définissez la propriété `from` dans chaque message envoyé par le client Direct Line à une valeur constante qui identifie de façon unique l’utilisateur qui envoie le message. Par exemple, si un utilisateur est déjà connecté à une page web ou à une application, vous pouvez utiliser cet identifiant utilisateur existant comme valeur de la propriété `from` dans les messages qu’il envoie. Sinon, vous pouvez choisir de créer un identifiant utilisateur aléatoire sur la page de chargement ou sur l’application, de le stocker dans un témoin ou un état de l’appareil et de l’utiliser comme valeur de la propriété `from` dans les messages envoyés par l’utilisateur.

## <a name="what-causes-the-direct-line-30-service-to-respond-with-http-status-code-502-bad-gateway"></a>Pourquoi le service Direct Line 3.0 retourne le code d’état HTTP 502 « Passerelle incorrecte » ?
Direct Line 3.0 retourne le code d’état HTTP 502 lorsqu’il essaie de communiquer avec votre robot, mais que la requête n’aboutit pas. Cette erreur indique que le robot a renvoyé une erreur ou que la requête a expiré. Pour plus d’informations sur les erreurs générées par votre bot, accédez au tableau de bord du bot dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, puis cliquez sur le lien « Problèmes » correspondant au canal concerné. Si vous avez configuré Application Insights pour votre robot, vous pouvez également y trouver des informations détaillées concernant les erreurs. 

::: moniker range="azure-bot-service-3.0"

## <a name="what-is-the-idialogstackforward-method-in-the-bot-framework-sdk-for-net"></a>En quoi consiste la méthode IDialogStack.Forward dans le kit SDK Bot Framework pour .NET ?

Le but principal de `IDialogStack.Forward` est de réutiliser un dialogue enfant existant qui est souvent « réactif », c’est-à-dire que le dialogue enfant (dans `IDialog.StartAsync`) attend un objet `T` avec un certain gestionnaire `ResumeAfter`. En particulier, si un dialogue enfant attend `IMessageActivity``T`, vous pouvez transférer le `IMessageActivity` entrant (déjà reçu par un dialogue parent) à l’aide de la méthode `IDialogStack.Forward`. Par exemple, pour transférer un `IMessageActivity` entrant à un `LuisDialog`, appelez `IDialogStack.Forward` afin de pousser le `LuisDialog` sur la pile de dialogues, exécutez le code dans `LuisDialog.StartAsync` jusqu’à ce qu’il programme une attente pour le message suivant, puis répondez immédiatement à cette attente avec le `IMessageActivity` transféré.

`T` est habituellement un `IMessageActivity`, puisque `IDialog.StartAsync` est typiquement construit en vue d’attendre ce type d’activité. Vous pouvez utiliser `IDialogStack.Forward` vers `LuisDialog` comme mécanisme d’interception des messages de l’utilisateur pour un certain traitement avant de transmettre le message à un `LuisDialog` existant. Sinon, vous pouvez également utiliser `DispatchDialog` avec `ContinueToNextGroup` à cette fin.

Vous devriez vous attendre à trouver l’élément transféré dans le premier gestionnaire `ResumeAfter` (par exemple `LuisDialog.MessageReceived`) qui est programmé par `StartAsync`.

::: moniker-end

## <a name="what-is-the-difference-between-proactive-and-reactive"></a>Quelle est la différence entre « proactif » et « réactif » ?

Du point de vue de votre robot, « réactif » signifie que l’utilisateur lance la conversation en envoyant un message au robot, et le robot réagit en répondant au message. En revanche, « proactif » signifie que le robot entame la conversation en envoyant le premier message à l’utilisateur. Par exemple, un robot peut envoyer un message proactif pour avertir un utilisateur lorsqu’une temporisation se termine ou qu’un événement se produit.

## <a name="how-can-i-send-proactive-messages-to-the-user"></a>Comment puis-je envoyer des messages proactifs à l’utilisateur ?

Pour voir comment envoyer des messages proactifs, consultez les exemples [C# V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) et [Node.js V4](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) dans le référentiel BotBuilder-Samples sur GitHub.

## <a name="how-can-i-reference-non-serializable-services-from-my-c-dialogs"></a>Comment puis-je référencer des services non sérialisables à partir de mes dialogues C# ?

Il existe de multiples options :

* Résoudre la dépendance par le biais de `Autofac` et `FiberModule.Key_DoNotSerialize`. C’est la solution la plus propre.
* Utiliser les attributs [NonSerialized](https://msdn.microsoft.com/library/system.nonserializedattribute(v=vs.110).aspx) et [OnDeserialized](https://msdn.microsoft.com/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx) afin de restaurer la dépendance à la désérialisation. C’est la solution la plus simple.
* Ne stockez pas cette dépendance de telle sorte qu’elle ne soit pas sérialisée. Cette solution n’est pas recommandée, bien qu’elle soit techniquement réalisable.
* Utiliser le substitut de sérialisation par réflexion. Cette solution peut ne pas être réalisable dans certains cas et risque une sérialisation excessive.

::: moniker range="azure-bot-service-3.0"

## <a name="where-is-conversation-state-stored"></a>Où est stocké l’état de la conversation ?

Les données dans les conteneurs de propriétés de l’utilisateur, des conversations et des conversations privées sont stockées à l’aide de l’interface `IBotState` du connecteur. Chaque conteneur de propriétés est délimité par l’identifiant du robot. Le conteneur de propriétés de l’utilisateur est verrouillé par un identifiant utilisateur, le conteneur de propriétés de conversation est verrouillé par un identifiant de conversation, et le conteneur de propriétés de conversation privée est verrouillé par un identifiant utilisateur et un identifiant de conversation. 

Si vous utilisez le kit SDK Bot Framework pour.NET ou le kit SDK Bot Framework pour Node.js pour créer votre bot, la pile de dialogues et les données de dialogue sont automatiquement stockées sous forme d’entrées dans le conteneur de propriétés de conversation privée. L’implémentation C# utilise la sérialisation binaire, et l’implémentation Node.js utilise la sérialisation JSON.

L’interface REST `IBotState` est implémentée par deux services.

* Le connecteur de Bot Framework fournit un service de cloud qui implémente cette interface et stocke les données dans Azure.  Ces données sont chiffrées au repos et n’expirent pas de façon intentionnelle.
* L’émulateur de Bot Framework fournit une implémentation en mémoire de cette interface pour le débogage de votre robot. Ces données expirent lorsque le processus d’émulateur se termine.

Pour stocker ces données dans vos centres de données, vous pouvez fournir une implémentation personnalisée du service d’état. Pour ce faire, vous pouvez procéder de deux façons au minimum :

* Utiliser la couche REST pour assurer un service `IBotState` personnalisé.
* Utiliser les interfaces Builder dans la couche de langage (Node.js ou C#).

> [!IMPORTANT]
> L’API du service Bot Framework State n’est pas recommandée pour les environnements de production, et pourra être dépréciée dans une version ultérieure. Nous vous recommandons de mettre à jour le code de votre bot pour qu’il utilise le stockage en mémoire à des fins de test ou pour qu’il utilise l’une des **extensions Azure** pour les bots de production. Pour plus d’informations, consultez la rubrique **Gérer les données d’état** pour l’implémentation [.NET](~/dotnet/bot-builder-dotnet-state.md) ou [Node](~/nodejs/bot-builder-nodejs-state.md).

::: moniker-end

## <a name="what-is-an-etag--how-does-it-relate-to-bot-data-bag-storage"></a>Qu’est-ce qu’une étiquette d’entité ?  Quel est son rapport avec le stockage des conteneurs de données des robots ?

Une [étiquette d’entité](https://en.wikipedia.org/wiki/HTTP_ETag) est un mécanisme de [contrôle de l’accès concurrentiel optimiste](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). Le stockage des conteneurs de données des robots utilise des étiquettes d’entité pour éviter les conflits de mise à jour des données. Une erreur d’étiquette d’entité avec le code d’état HTTP 412 « Échec de la précondition » indique que plusieurs séquences « lecture-modification-écriture » se sont exécutées simultanément pour ce conteneur de données de robot.

La pile de dialogues et l’état sont stockés dans des conteneurs de données de robot. Par exemple, vous pouvez voir s’afficher l’erreur d’étiquette d’entité « Échec de la précondition » si votre robot est toujours en train de traiter un message précédent lorsqu’il reçoit un nouveau message pour la conversation en question.

## <a name="what-causes-an-error-with-http-status-code-412-precondition-failed-or-http-status-code-409-conflict"></a>Qu’est-ce qui provoque une erreur avec le code d’état HTTP 412 « Échec de la précondition » ou le code d’état HTTP 409 « Conflit » ?

Le service `IBotState` du connecteur permet de stocker les conteneurs de données du robot (c’est-à-dire ceux de l’utilisateur, de la conversation et du robot privé qui inclut l’état « flux de contrôle » de la pile de dialogues). Le contrôle de la concurrence dans le service `IBotState` est géré par une concurrence optimiste par le biais d’étiquettes d’entité. En cas de conflit de mise à jour (en raison d’une mise à jour simultanée d’un même conteneur de données de robot) lors d’une séquence « lecture-modification-écriture » :

* Si les étiquettes d’entité sont conservées, une erreur avec le code d’état HTTP 412 « Échec de la précondition » est lancée depuis le service `IBotState`. Il s’agit du comportement par défaut du kit SDK Bot Framework pour .NET.
* Si les étiquettes d’entité ne sont pas conservées (étiquette d’entité définie à `\*`), alors la stratégie « dernière écriture prévaut » est appliquée, ce qui évite l’erreur « Échec de la précondition », mais entraîne le risque de perdre des données. Il s’agit du comportement par défaut du kit SDK Bot Framework pour .Node.js.

## <a name="how-can-i-fix-precondition-failed-412-or-conflict-409-errors"></a>Comment puis-je corriger les erreurs « Échec de la précondition » (412) ou « Conflit » (409) ?

Ces erreurs indiquent que votre robot a traité plusieurs messages en même temps pour la même conversation. Si votre robot est connecté à des services qui nécessitent des messages ordonnés de façon précise, vous devriez penser à verrouiller l’état de la conversation pour vous assurer que les messages ne sont pas traités en parallèle. 

::: moniker range="azure-bot-service-3.0"

Le kit SDK Bot Framework pour .NET propose un mécanisme (classe `LocalMutualExclusion` qui implémente `IScope`) qui permet de sérialiser de façon pessimiste la gestion d’une même conversation avec un sémaphore en mémoire. Vous pouvez étendre cette implémentation à l’utilisation d’un bail Redis délimité par l’adresse de la conversation.

Si votre robot n’est pas connecté à des services externes ou si le traitement parallèle des messages d’une même conversation est acceptable, vous pouvez ajouter ce code pour ignorer les conflits qui se produisent dans l’API Bot Stat. La dernière réponse pourra ainsi définir l’état de la conversation.

```cs
var builder = new ContainerBuilder();
builder
    .Register(c => new CachingBotDataStore(c.Resolve<ConnectorStore>(), CachingBotDataStoreConsistencyPolicy.LastWriteWins))
    .As<IBotDataStore<BotData>>()
    .AsSelf()
    .InstancePerLifetimeScope();
builder.Update(Conversation.Container);
```

## <a name="how-do-i-version-the-bot-data-stored-through-the-state-api"></a>Comment faire pour adapter la version des données de robot stockées par le biais de l’API d’état ?

> [!IMPORTANT]
> L’API du service Bot Framework State n’est pas recommandée pour les environnements de production ou les bots v4, et peut être totalement dépréciée dans une version ultérieure. Nous vous recommandons de mettre à jour le code de votre bot pour qu’il utilise le stockage en mémoire à des fins de test ou pour qu’il utilise l’une des **extensions Azure** pour les bots de production. Pour plus d’informations, consultez [Manage state data](v4sdk/bot-builder-howto-v4-state.md) (Gérer les données d’état).

Le service d’état permet d’avancer dans les dialogues d’une conversation de sorte qu’un utilisateur peut revenir ultérieurement dans la conversation avec un robot sans perdre sa position. À cette fin, les conteneurs de propriétés des données de robot qui sont stockés via l’API d’état ne sont pas automatiquement effacés lorsque vous modifiez le code du robot. Vous devez déterminer si les données du robot doivent être effacées ou non selon que votre code modifié est compatible ou non avec les versions antérieures de vos données. 

* Si vous voulez réinitialiser manuellement la pile de dialogues et l’état de la conversation pendant le développement de votre robot, vous pouvez utiliser la commande `/deleteprofile` afin de supprimer les données d’état. Assurez-vous d’inclure l’espace initial dans cette commande pour éviter que le canal ne l’interprète.
* Une fois que votre robot est déployé en production, vous pouvez adapter la version de vos données de robot de façon à ce que les données d’état associées soient effacées si vous changez de version. Avec le kit SDK Bot Framework pour Node.js, vous pouvez le faire en utilisant un middleware. Avec le kit SDK Bot Framework pour.NET, cette opération est possible en utilisant une implémentation `IPostToBot`.

> [!NOTE]
> S’il n’est pas possible de désérialiser correctement la pile de dialogues en raison de modifications dans le format de sérialisation ou parce que le code a été trop modifié, l’état de la conversation est réinitialisé.

::: moniker-end

## <a name="what-are-the-possible-machine-readable-resolutions-of-the-luis-built-in-date-time-duration-and-set-entities"></a>Quelles sont les résolutions possibles que la machine peut lire pour la date, l’heure, la durée et les entités réglées de LUIS ?

Pour obtenir une liste d’exemples, consultez la section [Entités prédéfinies][LUISPreBuiltEntities] de la documentation LUIS.

## <a name="how-can-i-use-more-than-the-maximum-number-of-luis-intents"></a>Comment puis-je dépasser le nombre maximal d’intentions LUIS ?

Vous pouvez diviser votre modèle et appeler le service LUIS en série ou en parallèle.

## <a name="how-can-i-use-more-than-one-luis-model"></a>Comment puis-je utiliser plusieurs modèles LUIS ?

Le kit SDK Bot Framework pour Node.js et le kit SDK Bot Framework pour.NET peuvent appeler plusieurs modèles LUIS à partir d’un seul dialogue d’intention LUIS. Rappelez-vous des avertissements suivants :

* Pour utiliser plusieurs modèles LUIS, leurs ensembles d’intentions ne doivent pas se recouvrir.
* Pour utiliser plusieurs modèles LUIS, leurs scores doivent être comparables afin de sélectionner « l’intention la mieux adaptée » parmi les différents modèles.
* L’utilisation de plusieurs modèles LUIS implique que lorsqu’une intention correspond à un modèle, elle correspond aussi fortement à l’intention « aucune » des autres modèles. Dans ce cas, vous pouvez éviter de sélectionner l’intention « none » ; le kit SDK Bot Framework pour Node.js effectue alors un scale-down automatique du score pour les intentions « none » pour éviter ce problème.

## <a name="where-can-i-get-more-help-on-luis"></a>Où puis-je trouver de l’aide supplémentaire sur LUIS ?

* [Présentation du module Language Understanding (LUIS) - Microsoft Cognitive Services](https://www.youtube.com/watch?v=jWeLajon9M8) (vidéo)
* [Session d’apprentissage avancé du module Language Understanding (LUIS) ](https://www.youtube.com/watch?v=39L0Gv2EcSk) (vidéo)
* [Documentation LUIS](/azure/cognitive-services/LUIS/Home)
* [Forum sur le module Language Understanding](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 


## <a name="what-are-some-community-authored-dialogs"></a>Avez-vous des exemples de dialogues créés par la communauté ?

* [BotAuth](https://www.nuget.org/packages/BotAuth) : Authentification Azure Active Directory
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) : Envoi de texte utilisateur basé sur des expressions régulières vers des méthodes de dialogue

## <a name="what-are-some-community-authored-templates"></a>Avez-vous des exemples de modèles créés par la communauté ?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) : Modèle ES6 Bot Builder

## <a name="why-do-i-get-an-authorization_requestdenied-exception-when-creating-a-bot"></a>Pourquoi est-ce que j’obtiens l’exception Authorization_RequestDenied lors de la création d’un robot ?

La permission de créer des robots Azure Bot Service est gérée par le biais du portail Azure Active Directory (AAD). Si les permissions ne sont pas correctement configurées dans le portail [AAD](https://aad.portal.azure.com), les utilisateurs obtiennent l’exception **Authorization_RequestDenied** lors de la création d’un service de robot.

Vérifiez d’abord que vous êtes un « Invité » du répertoire :

1. Connectez-vous au [Portail Microsoft Azure](https://portal.azure.com).
2. Cliquez sur **Tous les services** et recherchez *actif*.
3. Sélectionnez **Azure Active Directory**.
4. Cliquez sur **Utilisateurs**.
5. Trouvez l’utilisateur dans la liste et assurez-vous que le **Type d’utilisateur** n’est pas **Invité**.

![Type d’utilisateur Azure Active Directory](~/media/azure-active-directory/user_type.png)

Après avoir vérifié que vous n’êtes pas un **Invité**, l’administrateur du répertoire doit configurer les paramètres suivants pour s’assurer que les utilisateurs d’un répertoire actif peuvent créer un service de robot :

1. Connectez-vous au [portail AAD](https://aad.portal.azure.com). Dans **Utilisateurs et groupes**, sélectionnez **Paramètres utilisateur**.
2. Dans la section **Inscription des applications** , mettez **Les utilisateurs peuvent enregistrer des applications** sur **Oui**. Les utilisateurs de votre répertoire pourront ainsi créer un service de robot.
3. Dans la section **Utilisateurs externes**, mettez **Les permissions des utilisateurs invités sont limitées** sur **Non**. Les utilisateurs invités de votre répertoire pourront ainsi créer un service de robot.

![Centre d’administration Azure Active Directory](~/media/azure-active-directory/admin_center.png)

## <a name="why-cant-i-migrate-my-bot"></a>Pourquoi est-ce que je ne peux pas migrer mon robot ?

Si vous rencontrez des problèmes lors de la migration de votre robot, c’est peut-être parce qu’il appartient à un autre répertoire que votre répertoire par défaut. Essayez d’effectuer les étapes suivantes :

1. Dans le répertoire cible, ajoutez un nouvel utilisateur (par adresse e-mail) qui ne fait pas partie du répertoire par défaut. Attribuez à l’utilisateur contributeur un rôle sur les abonnements qui constituent la cible de la migration.

2. Dans [Dev Portal](https://dev.botframework.com), ajoutez l’adresse e-mail de l’utilisateur en tant que copropriétaire du robot devant être migré. Ensuite, déconnectez-vous.

3. Connectez-vous au [Dev Portal](https://dev.botframework.com)en tant que nouvel utilisateur et passez à la migration du robot.

## <a name="where-can-i-get-more-help"></a>Où puis-je trouver de l’aide supplémentaire ?

* Exploitez les réponses aux questions précédentes sur le [dépassement de la capacité de la pile](https://stackoverflow.com/questions/tagged/botframework), ou publiez vos propres questions à l’aide de l’étiquette `botframework`. Veuillez noter que le dépassement de la capacité de la pile comporte certaines exigences telles qu’un titre descriptif, un énoncé complet et concis du problème et suffisamment de précisions pour pouvoir reproduire votre problème. Les requêtes de fonctionnalités ou les questions trop générales sont hors sujet ; les nouveaux utilisateurs doivent visiter le [centre d’aide sur les dépassements de capacité de la pile](https://stackoverflow.com/help/how-to-ask) pour obtenir de plus amples renseignements.
* Pour en savoir plus sur les problèmes connus du kit SDK Bot Framework ou pour signaler un nouveau problème, consultez [BotBuilder issues](https://github.com/Microsoft/BotBuilder/issues) dans GitHub.
* Exploitez les informations de la discussion de la communauté BotBuilder sur [Gitter](https://gitter.im/Microsoft/BotBuilder).




[LUISPreBuiltEntities]: /azure/cognitive-services/luis/pre-builtentities
[BotFrameworkIDGuide]: bot-service-resources-identifiers-guide.md
[StateAPI]: ~/rest-api/bot-framework-rest-state.md
[TroubleshootingAuth]: bot-service-troubleshoot-authentication-problems.md

