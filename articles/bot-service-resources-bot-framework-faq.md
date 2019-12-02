---
title: 'Service de bot : Forum Aux Questions | Microsoft Docs'
description: Une liste de questions fréquemment posées sur les éléments du Bot Framework et la disponibilité de nouvelles fonctionnalités.
author: scheyal
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/21/2019
ms.openlocfilehash: c0feda708be8ac384c458289884fddf6ee798790
ms.sourcegitcommit: a4a437a1d44137375ea044dcc11bccc8d004e3db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74479501"
---
# <a name="bot-framework-frequently-asked-questions"></a>Bot Framework : Forum Aux Questions

Cet article répond à certaines questions fréquemment posées sur le Bot Framework.

## <a name="background-and-availability"></a>Contexte et disponibilité

### <a name="why-did-microsoft-develop-the-bot-framework"></a>Pourquoi Microsoft a-t-il développé le Bot Framework ?

Alors que l’interface utilisateur conversationnelle est déjà à notre portée, peu de développeurs disposent actuellement de l’expertise et des outils nécessaires pour créer de nouvelles expériences conversationnelles ou doter les applications et services existants d’une interface conversationnelle pour les utilisateurs. Nous avons créé le Bot Framework pour aider les développeurs à générer des bots exceptionnels et à les connecter avec les utilisateurs, et ce, quel que soit le lieu de la conversation, y compris sur les canaux de haute qualité Microsoft.

### <a name="what-is-the-v4-sdk"></a>Qu’est-ce que le Kit de développement logiciel (SDK) v4 ?
Le kit SDK Bot Framework v4 s’appuie sur les commentaires et les enseignements tirés des précédents kits SDK Bot Framework. Il offre des niveaux d’abstraction appropriés et assure une grande modularité des blocs de construction de bot. Vous pouvez commencer avec un bot simple et le sophistiquer progressivement à l’aide d’une infrastructure modulaire et extensible. Vous trouverez un [FAQ](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) sur le Kit de développement logiciel (SDK) sur GitHub.

### <a name="running-a-bot-offline"></a>Exécution d’un bot hors connexion

<!-- WIP -->
Avant de parler de l’utilisation d’un bot hors connexion, c’est-à-dire un bot déployé non pas sur Azure ou un autre service hôte, mais localement, clarifions quelques points.

- Un bot est un service web sans interface utilisateur. L’utilisateur interagit donc avec le bot par d’autres moyens, en l’occurrence par des canaux qui utilisent le [service du connecteur Azure](rest-api/bot-framework-rest-connector-concepts.md#bot-connector-service). Le connecteur fonctionne comme un *proxy* pour relayer les messages entre un client et le bot.
- Le **connecteur** est une application globale hébergée sur des nœuds Azure et distribuée géographiquement pour assurer la disponibilité et l’évolutivité. 
- Vous utilisez l’[inscription du canal de bot](bot-service-quickstart-registration.md) pour inscrire le bot dans le connecteur.
    >[!NOTE]
    > Les points de terminaison du bot doivent être publiquement accessibles par le connecteur.

Vous pouvez exécuter un bot hors connexion avec des fonctionnalités limitées. Par exemple, si vous voulez utiliser hors connexion un bot avec la fonctionnalité LUIS, vous devez créer un conteneur pour le bot, les outils nécessaires et un conteneur pour LUIS. Les deux sont connectés via un réseau Docker Compose relié par un pont.

Il s’agit d’une solution hors connexion « partielle », car un conteneur Cognitive Services a besoin d’une connexion en ligne périodique.

> [!NOTE]
> Le service QnA n’est pas pris en charge dans un bot hors connexion.

Pour plus d'informations, consultez les pages suivantes :

- [Déployer le conteneur Language Understanding (LUIS) sur Azure Container Instances](https://docs.microsoft.com/azure/cognitive-services/luis/deploy-luis-on-container-instances)
- [Prise en charge des conteneurs dans Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)

## <a name="bot-framework-sdk-version-3-lifetime-support-and-deprecation-notice"></a>Notification de dépréciation et de prise en charge de la durée de vie du kit SDK Bot Framework version 3.
Le kit SDK Microsoft Bot Framework V4 a été publié en septembre 2018, depuis nous avons apporté quelques améliorations mineures à cette version. Comme annoncé précédemment, le kit SDK V3 est en cours de retrait. Par conséquent, il n’y aura plus de développement dans les dépôts V3. **Les charges de travail existantes des bots V3 continueront de s’exécuter sans interruption. Nous n’avons pas l’intention d’interrompre les charges de travail en cours d’exécution**.

Comme mentionné, les bots du kit SDK Bot Builder V3 continuent de s’exécuter et d’être pris en charge par Azure Bot Service. La version 3 du kit SDK Bot Builder est prise en charge uniquement pour les correctifs de bogues de sécurité critiques, le connecteur et les mises à jour de compatibilité de la couche protocole.  

Toutes les nouvelles fonctions et fonctionnalités sont développées exclusivement sur le [kit SDK Bot Framework V4](https://github.com/microsoft/botframework-sdk).  Nous encourageons les clients à migrer leurs bots vers la version 4 dès que possible.

Nous vous recommandons vivement de commencer la migration de vos bots v3 vers v4. Afin de prendre en charge cette migration, nous avons élaboré une documentation sur la migration et assurerons une prise en charge étendue des initiatives de migration (via les canaux standard, tels que Stack Overflow et le service clientèle Microsoft).

Pour plus d’informations, reportez-vous aux références suivantes :
* [Essential Migration Guidance ](https://aka.ms/bfv3v4migration)
* Principaux dépôts V4 pour développer des bots Bot Framework
  * [Botbuilder pour dotnet](https://github.com/microsoft/botbuilder-dotnet)
  * [Botbuilder pour JS](https://github.com/microsoft/botbuilder-js) 
* Les bibliothèques QnA Maker ont été remplacées par les bibliothèques V4 suivantes :
  * [Bibliothèques pour dotnet](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.AI.QnA)
  * [Bibliothèques pour JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-ai/src/qnaMaker.ts)
* Les bibliothèques Azure ont été remplacées par les bibliothèques V4 suivantes :
  * [Botbuilder pour JS avec Azure](https://github.com/Microsoft/botbuilder-js/tree/master/libraries/botbuilder-azure)
  * [Botbuilder pour dotnet avec Azure](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.Azure)

### <a name="v3-status-summary"></a>Récapitulatif de l’état V3

#### <a name="abs-service"></a>Service ABS
1.  Le service ABS continuera de prendre en charge les bots V3 s’exécutant sans fin de vie planifiée, et aucun bot en cours d’exécution ne sera interrompu. 
2.  Les canaux restent compatibles avec la version 3 sans aucune interruption ni plan de fin de vie.
3.  La création de bots V3 est désactivée sur le portail ; toutefois, les utilisateurs expérimentés qui souhaitent déployer leurs bots V3 indépendamment, et non sur ABS (par exemple, en tant que service d’application web) peuvent le faire.

#### <a name="sdk-and-tools"></a>Kit SDK et outils

1.  Concernant le kit SDK, nous n’investissons pas dans V3 et nous n’appliquerons que les correctifs de sécurité critiques aux branches du kit SDK pour l’instant (Exception : nous prévoyons d’ajouter un connecteur Skills pour permettre aux bots V4 d’appeler des bots V3 existants).
2.  Le développement des kits SDK et des outils se fait exclusivement sur V4, sans travail effectué ni planifié dans la version 3 (la transition est donc faite pour nous).
3.  Nous n’empêchons personne d’exécuter les anciens outils pour gérer leurs bots V3. 


### <a name="how-can-i-migrate-azure-bot-service-from-one-region-to-another"></a>Comment puis-je migrer Azure Bot Service d’une région à une autre ?

Azure Bot Service ne prend pas en charge le déplacement entre régions. Il s’agit d’un service mondial qui n’est lié à aucune région spécifique.

## <a name="channels"></a>Canaux
### <a name="when-will-you-add-more-conversation-experiences-to-the-bot-framework"></a>Quand allez-vous ajouter des expériences de conversation au Bot Framework ?

Nous prévoyons d’apporter des améliorations continues au Bot Framework et notamment d’ajouter des canaux. Cependant, nous ne pouvons pas fournir de calendrier pour l’instant.  
Si vous souhaitez que nous ajoutions un canal spécifique à l’infrastructure, [indiquez-le nous][Support].

### <a name="i-have-a-communication-channel-id-like-to-be-configurable-with-bot-framework-can-i-work-with-microsoft-to-do-that"></a>J’aimerais pouvoir configurer un canal de communication spécifique avec le Bot Framework. Puis-je travailler avec Microsoft à cette fin ?

Nous n’avons pas fourni de mécanisme général aux développeurs pour ajouter de nouveaux canaux au Bot Framework. Cependant, vous pouvez connecter votre robot à votre application par le biais de [Direct Line API][DirectLineAPI]. Si vous êtes un développeur d’un canal de communication et que vous souhaitez travailler avec nous pour activer votre canal dans le Bot Framework, [n’hésitez pas à nous contacter][Support].

### <a name="if-i-want-to-create-a-bot-for-microsoft-teams-what-tools-and-services-should-i-use"></a>Quels outils et services dois-je utiliser pour créer un bot pour Microsoft Teams ?

Le Bot Framework est conçu pour générer, connecter et déployer des bots réactifs, performants et évolutifs de haute qualité pour Teams et de nombreux autres canaux. Vous pouvez utiliser le Kit de développement logiciel (SDK) pour créer des bots prenant en charge le texte, les sms, les images, les boutons et les cartes (utilisés aujourd’hui pour la majorité des interactions de bot dans le cadre d’expériences conversationnelles), ainsi que des interactions de bot spécifiques à Teams, comme des expériences audio et vidéo enrichies.

Si vous disposez déjà d’un excellent bot et que vous souhaitez toucher les utilisateurs de Teams, vous pouvez le connecter facilement à Teams (ou à tout autre canal pris en charge) par le biais de l’API REST Bot Framework (dans la mesure où il dispose d’un point de terminaison REST accessible par Internet).

### <a name="how-do-i-create-a-bot-that-uses-the-us-government-data-center"></a>Comment créer un bot qui utilise le centre de données US Government ?

La création d’un bot utilisant un centre de données US Government doit comporter deux étapes principales.
1. Ajouter un paramètre « channel provider » dans votre fichier appsettings.json (ou les paramètres App Service). Cette opération doit être définie pour cette constante nom/valeur : ChannelService = "https://botframework.azure.us ". Vous trouverez ci-dessous un exemple d’utilisation du fichier appsetting.json.

```json
{
  "MicrosoftAppId": "", 
  "MicrosoftAppPassword": "",
  "ChannelService": "https://botframework.azure.us"
}
```
2. Si vous utilisez .NET Core, vous devez ajouter un ConfigurationChannelProvider dans votre fichier startup.cs. La procédure à suivre dépend de la version du Kit de développement que vous utilisez.

- Pour les versions 4.3 et ultérieures, vous devez créer une instance ConfigurationChannelProvider dans votre méthode ConfigureServices. Lorsque vous utilisez la classe BotFrameworkHttpAdapter, vous injectez ces informations en tant que singleton dans la collection de services comme suit :

```csharp  
services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();
```
- Pour les versions antérieures à la version 4.3, dans votre méthode ConfigureServices, recherchez la méthode AddBot. Lorsque vous définissez les options, assurez-vous d’ajouter ce qui suit :

```csharp
options.ChannelProvider = new ConfigurationChannelProvider();
```
Pour plus d’informations sur les services du secteur public, [cliquez ici](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#azure-bot-service)

## <a name="security-and-privacy"></a>Sécurité et confidentialité
### <a name="do-the-bots-registered-with-the-bot-framework-collect-personal-information-if-yes-how-can-i-be-sure-the-data-is-safe-and-secure-what-about-privacy"></a>Les bots inscrits auprès du Bot Framework collectent-ils des informations personnelles ? Si c’est le cas, comment puis-je m’assurer qu’elles sont protégées et sécurisées ? Qu’en est-il de la confidentialité ?

Chaque bot est un service propre. Dans le cadre du Code de conduite du développeur, les développeurs de ces services sont tenus de fournir des conditions d’utilisation et des déclarations de confidentialité.  Vous pouvez accéder à ces informations à partir de la carte du bot dans le répertoire du bot.

Pour fournir le service d’E/S, le Bot Framework transmet votre message et le contenu du message (y compris votre ID) du service de conversation utilisé au bot.

### <a name="can-i-host-my-bot-on-my-own-servers"></a>Puis-je héberger mon bot sur mes propres serveurs ?
Oui. Votre bot peut être hébergé n’importe où sur Internet. Sur vos propres serveurs, dans Azure, ou dans n’importe quel autre centre de données. La seule exigence à respecter réside dans le fait que le bot doit exposer un point de terminaison HTTPS accessible publiquement.

### <a name="how-do-you-ban-or-remove-bots-from-the-service"></a>Comment excluez-vous ou supprimez-vous des bots du service ?

Les utilisateurs peuvent signaler la présence d’un bot inapproprié en se reportant à la carte de contact du bot dans le répertoire. Les développeurs doivent se conformer aux conditions d’utilisation de Microsoft pour participer au service.

### <a name="which-specific-urls-do-i-need-to-whitelist-in-my-corporate-firewall-to-access-bot-framework-services"></a>Quelles URL dois-je mettre sur la liste verte de mon pare-feu d’entreprise pour accéder aux services Bot Framework ?
Si vous disposez d’un pare-feu pour le trafic sortant qui bloque le trafic vers Internet en provenance de votre bot, vous devrez mettre en liste verte les URL ci-après dans ce pare-feu :
- login.botframework.com (authentification des bots)
- login.microsoftonline.com (authentification des bots)
- westus.api.cognitive.microsoft.com (pour l’intégration du système de traitement du langage naturel Luis.ai)
- state.botframework.com (stockage de l’état des bots pour le prototypage)
- cortanabfchanneleastus.azurewebsites.net (canal Cortana)
- cortanabfchannelwestus.azurewebsites.net (canal Cortana)
- *.botframework.com (canaux)

### <a name="can-i-block-all-traffic-to-my-bot-except-traffic-from-the-bot-connector-service"></a>Puis-je bloquer la totalité du trafic en direction de mon bot, à l’exception du trafic émanant du service Bot Connector ?
Non. Ce type de mise en liste verte d’adresses IP ou de DNS est irréaliste. Le service Bot Framework Connector est hébergé dans les centres de données Azure du monde entier, et la liste des adresses IP Azure évolue constamment. La mise en liste verte de certaine adresses IP peut donc cesser de fonctionner du jour au lendemain lorsque les adresses IP Azure changent.
 
### <a name="what-keeps-my-bot-secure-from-clients-impersonating-the-bot-framework-connector-service"></a>De quelle manière mon bot est-il protégé contre les clients qui empruntent l’identité du service Bot Framework Connector ?
1. L’URL du service (ServiceUrl) est encodée dans le jeton de sécurité qui accompagne chaque requête adressée à votre bot ; autrement dit, même si un attaquant parvient à accéder au jeton, ils ne peut pas rediriger la conversation vers une nouvelle URL ServiceUrl. Cette approche est appliquée par toutes les implémentations du Kit de développement logiciel (SDK) et documentée dans nos articles de [référence](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) concernant l’authentification.

2. Si le jeton entrant est manquant ou incorrect, le Kit de développement logiciel (SDK) Bot Framework ne génère aucun jeton en réponse. Cela limite les dommages potentiels en cas de configuration incorrecte du bot.
3. À l’intérieur du bot, vous pouvez vérifier manuellement l’URL ServiceUrl fournie dans le jeton. Cette opération fragilise le bot en cas d’apport de modifications à la topologie du service ; elle est donc possible, mais déconseillée.


Notez que ceci concerne les connexions sortantes à partir du bot vers Internet. Il n’existe aucune liste d’adresses IP ou de noms DNS destinée à être utilisée par le service Bot Framework Connector pour communiquer avec le bot. La mise en liste verte d’adresses IP entrantes n’est pas prise en charge.

## <a name="rate-limiting"></a>Limitation du débit
### <a name="what-is-rate-limiting"></a>Qu’est-ce que la limitation du débit ?
Le service Bot Framework doit protéger ses clients (et se protéger lui-même) des modèles d’appels abusifs (attaques par déni de service, par exemple), de sorte qu’aucun bot ne puisse nuire aux performances des autres bots. Pour assurer ce type de protection, nous avons ajouté des limitations de débit (ou limitations de bande passante) à tous les points de terminaison. En appliquant une limitation de débit, nous pouvons limiter la fréquence à laquelle un bot peut effectuer un appel de spécifique. Par exemple, lorsque la limitation de débit est activée, si un bot veut envoyer un grand nombre d’activités, il devra les espacer dans le temps. Notez que la limitation de débit ne vise pas à limiter le volume total pour un bot. Elle est conçue pour éviter toute utilisation abusive de l’infrastructure de conversation, ne respectant pas les modèles de conversation humains.

### <a name="how-will-i-know-if-im-impacted"></a>Comment savoir si je suis concerné ?
Il est peu probable que vous soyez concerné par la limitation de débit, même si vous traitez des volumes élevés. Généralement, la limitation de débit s’applique en cas d’envoi massif d’activités (à partir d’un bot ou d’un client), de test de charge extrême ou de bogue. Lorsqu’une demande est limitée, une réponse HTTP 429 (Demandes trop nombreuses) est renvoyée avec un en-tête Retry-After qui indique le délai (en secondes) à l’issue duquel une nouvelle tentative de requête pourra aboutir. Vous pouvez collecter ces informations en activant l’analyse pour votre bot via Azure Application Insights. Vous pouvez également ajouter du code dans votre bot pour enregistrer les messages. 

### <a name="how-does-rate-limiting-occur"></a>Dans quels cas la limitation de débit s’applique-t-elle ?
La limitation de débit s’applique dans les cas suivants :
-   Un bot envoie des messages trop fréquemment.
-   Le client d’un bot envoie des messages trop fréquemment.
-   Des clients Direct Line demandent un nouveau WebSocket trop fréquemment.

### <a name="what-are-the-rate-limits"></a>Quelles sont les limitations de débit ?
Nous ajustons en permanence les limitations de débit et les modérons autant que possible, tout en protégeant notre service et nos utilisateurs. Étant donné que les seuils changent occasionnellement, nous ne publions aucun chiffre pour l’instant. Si vous êtes affecté par la limitation de débit, n’hésitez pas à nous contacter à l’adresse [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

## <a name="related-services"></a>Services connexes
### <a name="how-does-the-bot-framework-relate-to-cognitive-services"></a>Quel est le lien entre le Bot Framework et Cognitive Services ?

Le Bot Framework et [Cognitive Services](https://www.microsoft.com/cognitive) sont de nouvelles fonctionnalités introduites dans le cadre de [Microsoft Build 2016](https://build.microsoft.com), qui seront également intégrées à Cortana Intelligence Suite lors de sa mise à disposition générale. Ces deux services s’appuient sur des années de recherche et d’utilisation dans le cadre de produits Microsoft populaires. Grâce à ces fonctionnalités, combinées avec « Cortana Intelligence », toutes les organisations peuvent tirer parti de la puissance des données, du cloud et de l’intelligence pour générer leurs propres systèmes intelligents ; des systèmes ouvrant la voie à de nouvelles opportunités, accélérant leurs activités et leur permettant de dominer les secteurs dans lesquels elles servent leurs clients.

### <a name="what-is-cortana-intelligence"></a>Qu’est-ce que Cortana Intelligence ?

Cortana Intelligence est une suite entièrement gérée de traitement du Big Data et d’analyse avancée axée sur l’intelligence, qui convertit vos données en action intelligente.  
Cette suite complète regroupe des technologies issues de plusieurs années de recherche et d’innovation menées par Microsoft (couvrant l’analytique avancée, le Machine Learning, le stockage de Big Data et le traitement dans le cloud). Elle offre notamment les avantages suivants :

* Elle vous permet de collecter, gérer et stocker toutes vos données. Ces données peuvent gagner en volume de façon économique et transparente au fil du temps par le biais d’un processus évolutif et sécurisé.
* Elle fournit une analytique simple et actionnable s’appuyant sur le cloud, vous permettant de prévoir, prescrire et automatiser la prise de décision pour les problèmes les plus exigeants.
* Elle ouvre la voie à des systèmes intelligents reposant sur des agents et services cognitifs, qui vous permettent de voir, d’entendre, d’interpréter et de comprendre le monde qui vous entoure de façon plus naturelle et avec plus de contexte.

Avec Cortana Intelligence, notre ambition est d’aider nos clients d’entreprise à créer de nouvelles opportunités, à accélérer leurs activités et à s’imposer comme leaders dans leurs secteurs d’activité.

### <a name="what-is-the-direct-line-channel"></a>Qu’est-ce que le canal Direct Line ?

Direct Line est une API REST qui vous permet d’ajouter votre bot dans votre service, votre application mobile ou votre page web.

Vous pouvez écrire un client pour l’API Direct Line à l’aide de n’importe quel langage. Il suffit de coder conformément au protocole [Direct Line][DirectLineAPI], de générer une clé secrète dans la page de configuration Direct Line et de communiquer avec votre bot depuis l’emplacement d’exécution du code.

Direct Line est adapté aux éléments suivants :

* Applications mobiles sur iOS, Android, Windows Phone et d’autres systèmes d’exploitation
* Applications de bureau sur Windows, OSX et d’autres systèmes d’exploitation
* Pages web requérant plus de capacités de personnalisation que celles offertes par le [canal Web Chat incorporable][WebChat]
* Applications de service à service


## <a name="app-registration"></a>Inscription d’application

### <a name="i-need-to-manually-create-my-app-registration-how-do-i-create-my-own-app-registration"></a>Je dois créer l’inscription de mon application. Comment créer l’inscription de mon application ?

La création de l’inscription de votre application sera nécessaire pour des situations similaires :

- Vous avez créé votre robot dans le portail Bot Framework (https://dev.botframework.com/bots/new) ) 
- Vous ne parvenez pas à effectuer d’inscriptions d’application dans votre organisation et avez besoin d’un tiers afin de créer l’ID d’application pour le bot que vous générez
- Vous devez créer manuellement votre ID d’application (et le mot de passe)

Pour créer votre ID d’application, procédez comme suit.

1. Connectez-vous à votre [compte Azure](https://portal.azure.com). Si vous ne possédez pas encore un compte Azure, vous pouvez [vous inscrire pour en obtenir un gratuitement](https://azure.microsoft.com/free/).
2. Accédez au [panneau Inscriptions des applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) et cliquez sur **Nouvelle inscription** dans la barre d’action en haut.

    ![nouvelle inscription](media/app-registration/new-registration.png)

3. Entrez un nom d’affichage pour l’inscription de l’application dans le champ *Nom*, puis sélectionnez les types de comptes pris en charge. Le nom ne doit pas nécessairement correspondre à l’ID du bot.

    > [!IMPORTANT]
    > Dans la section *Types de comptes pris en charge*, sélectionnez le bouton d’option *Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)* . Si les autres options sont sélectionnées, **la création du bot échouera**.

    ![détails de l’inscription](media/app-registration/registration-details.png)

4. Cliquez sur **S’inscrire**

    Après quelques instants, l’inscription de l’application nouvellement créée doit ouvrir un panneau. Copie l’*ID d’Application (client)* dans le panneau Vue d’ensemble et collez-le dans le champ ID d’application.

    ![id d’application](media/app-registration/app-id.png)

Si vous créez votre bot via le portail Bot Framework, vous avez terminé la définition de votre inscription d’application ; la clé secrète est générée automatiquement. 

Si vous créez votre bot dans le Portail Azure, vous devez générer une clé secrète pour l’inscription de votre application. 

1. Cliquez sur **Certificats et clés secrètes** dans la colonne de navigation de gauche du panneau d’inscription de votre application.
2. Dans ce panneau, cliquez sur le bouton **Nouvelle clé secrète du client**. Dans la boîte de dialogue qui s’ouvre, entrez la description de la clé secrète si vous le souhaitez, puis sélectionnez **Jamais** dans le groupe de boutons d’option Expire. 

    ![nouvelle clé secrète](media/app-registration/new-secret.png)

3. Copiez la valeur de votre clé secrète figurant dans le tableau sous *Clés secrètes du client* et collez-la dans le champ *Mot de passe* pour votre application, puis cliquez sur **OK** en bas de ce panneau. Ensuite, passez à la création du bot. 

    > [!NOTE]
    > La clé secrète n’est visible que dans ce panneau et ne peut pas être récupéré une fois que vous avez quitté cette page. Veillez à la conserver dans un endroit sûr.

    ![nouvel id d’application](media/app-registration/create-app-id.png)


[DirectLineAPI]: https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts
[Support]: bot-service-resources-links-help.md
[WebChat]: bot-service-channel-connect-webchat.md
