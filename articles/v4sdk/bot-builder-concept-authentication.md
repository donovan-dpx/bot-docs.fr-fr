---
title: Authentification de l’utilisateur dans Azure Bot Service | Microsoft Docs
description: Découvrez les fonctionnalités d’authentification de l’utilisateur dans Azure Bot Service.
keywords: azure bot service, authentication, bot framework token service
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 05/31/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 6eea954f58096d89cd3278058146b93fa04f435f
ms.sourcegitcommit: e276008fb5dd7a37554e202ba5c37948954301f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693759"
---
# <a name="user-authentication-within-a-conversation"></a>Authentification de l’utilisateur au sein d’une conversation

Pour effectuer certaines opérations pour le compte d’un utilisateur, telles que consulter les messages, faire référence à un calendrier, vérifier l’état d’un vol ou passer une commande, le bot doit appeler un service externe, tel que Microsoft Graph, GitHub ou le service REST d’une société.
Chaque service externe dispose d’un moyen de sécuriser ces appels, et une méthode courante pour sécuriser un tel appel consiste à émettre ces demandes à l’aide d’un _jeton utilisateur_ qui identifie de façon unique l’utilisateur sur ce service externe (parfois appelé jeton JWT).

Pour sécuriser l’appel à un service externe, le bot doit demander à l’utilisateur de se connecter afin qu’il puisse obtenir le jeton de l’utilisateur pour ce service.
De nombreux services prennent en charge la récupération de jeton via le protocole OAuth ou OAuth2.
Azure Bot Service fournit des cartes de connexion spécialisées et des services qui fonctionnent avec le protocole OAuth et gèrent le cycle de vie des jetons et un bot peut utiliser ces fonctionnalités pour acquérir un jeton d’utilisateur.

- Dans le cadre de la configuration du bot, un _paramètre de connexion OAuth_ est inscrit au sein de la ressource Azure Bot Service dans Azure.

    Chaque paramètre de connexion contient des informations sur le service externe ou le fournisseur d’identité à utiliser, ainsi qu’un secret et un ID de client OAuth valide, les étendues OAuth à activer et d’autres métadonnées de connexion requises par ce service externe ou fournisseur d’identité.

- Dans le code du bot, un paramètre de connexion OAuth est utilisé pour aider à connecter un utilisateur et obtenir un jeton d’utilisateur.

Ces services sont impliqués dans le flux de travail de connexion.

![Vue d’ensemble de l’authentification](./media/bot-builder-concept-authentication.png)

## <a name="about-the-bot-framework-token-service"></a>À propos du service de jetons Bot Framework

Le service de jetons Bot Framework a les responsabilités suivantes :

- Faciliter l’utilisation du protocole OAuth avec un large éventail de services externes.
- Stocker de façon sécurisée des jetons pour un bot, canal, conversation et utilisateur particuliers.
- Gérer le cycle de vie des jetons, notamment actualiser les jetons.

Par exemple, un bot qui peut vérifier les e-mails récents d’un utilisateur à l’aide de l’API Microsoft Graph a besoin d’un jeton d’utilisateur Azure Active Directory. Au moment de la conception, le développeur du bot doit inscrire une application Azure Active Directory auprès du service de jetons Bot Framework (via le portail Azure), puis configurer un paramètre de connexion OAuth (nommé `GraphConnection`) pour le bot. Lorsqu’un utilisateur interagit avec le bot, le flux de travail est le suivant :

1. L’utilisateur demande au bot, « Merci de vérifier mes e-mails ».
1. Une activité avec ce message est envoyée de l’utilisateur au service de canal Bot Framework. Le service de canal garantit que le champ `userid` au sein de l’activité a été défini et que le message est envoyé au bot.

    Les ID d’utilisateur sont propres au canal. Il peut s’agir de l’ID Facebook de l’utilisateur ou de son numéro de téléphone pour l’envoi de SMS.

1. Le bot reçoit l’activité de message et détermine que l’intention est de vérifier les e-mails de l’utilisateur. Le bot demande au service de jetons Bot Framework s’il possède déjà un jeton pour l’ID d’utilisateur pour le paramètre de connexion OAuth `GraphConnection`.
1. Dans la mesure où il s’agit de la première fois où cet utilisateur a eu une interaction avec le bot, le service de jetons Bot Framework ne dispose pas encore d’un jeton pour cet utilisateur et retourne un résultat _NotFound_ au bot.
1. Le bot crée une carte OAuthCard avec le nom de connexion `GraphConnection` et répond à l’utilisateur en lui demandant de se connecter à l’aide de cette carte.
1. L’activité passe par le service de canal Bot Framework, qui appelle le service de jetons Bot Framework pour créer une URL de connexion OAuth valide pour cette demande. Cette URL de connexion est ajoutée à la carte OAuthCard et celle-ci est renvoyée à l’utilisateur.
1. L’utilisateur reçoit un message lui demandant de se connecter en cliquant sur le bouton de connexion de la carte OAuth.
1. Lorsque l’utilisateur clique sur le bouton de connexion, le service de canal ouvre un navigateur web et appelle le service externe pour charger sa page de connexion.
1. L’utilisateur se connecte à cette page pour le service externe. Une fois que ce flux de travail est effectué, le service externe termine l’échange de protocole OAuth avec le service de jetons Bot Framework, puis le service externe envoie au service de jetons Bot Framework le jeton de l’utilisateur. Le service de jetons Bot Framework stocke ce jeton de façon sécurisée et envoie une activité au bot avec ce jeton.
1. Le bot reçoit l’activité avec le jeton et peut ensuite l’utiliser pour effectuer des appels sur l’API Graph.

## <a name="securing-the-sign-in-url"></a>Sécurisation de l’URL de connexion

Lorsque Bot Framework facilite la connexion de l’utilisateur, il est important de prendre en considération comment sécuriser l’URL de connexion. Lorsqu’un utilisateur reçoit une URL de connexion, cette URL est associée à un ID de conversation et un ID d’utilisateur spécifiques pour ce bot. Cette URL ne doit pas être partagée, sans quoi une mauvaise connexion a lieu pour une conversation de bot particulière. Pour atténuer les attaques de sécurité concernant le partage de l’URL de connexion, vous devez vous assurer que l’ordinateur et la personne qui clique sur l’URL de connexion est la personne qui _possède_ la fenêtre de conversation.

Certains canaux tels que Cortana, Teams, Direct Line et WebChat sont en mesure de fonctionner ainsi sans que l’utilisateur s’en rende compte. Par exemple, WebChat utilise des cookies de session pour s’assurer que le flux de connexion a lieu dans le même navigateur que la conversation WebChat. Toutefois, pour les autres canaux, l’utilisateur reçoit souvent un _code magique_ à 6 chiffres. Ceci est similaire à l’authentification multifacteur intégrée, car le service de jetons Bot Framework ne transmet pas le jeton au bot, sauf si l’utilisateur termine l’authentification finale, ce qui prouve que la personne qui s’est connectée a accès à l’expérience de conversation en entrant le code à 6 chiffres.

## <a name="azure-activity-directory-application-registration"></a>Inscription d’application Azure Active Directory

Chaque bot qui est inscrit en tant que service Azure Bot Service utilise un ID d’application Azure Active Directory (AD). Il est important de ne **pas** utiliser cet ID d’application et ce mot de passe pour connecter des utilisateurs. L’ID d’application Azure AD d’Azure Bot Service sert à sécuriser la communication de service à service entre le bot et les services de canal Bot Framework. Si vous souhaitez connecter des utilisateurs à Azure AD, vous devez créer une inscription d’application Azure AD avec les autorisations et les étendues appropriées.

## <a name="configure-an-oauth-connection-setting"></a>Configurer un paramètre de connexion OAuth

Pour plus d’informations sur la façon d’inscrire et d’utiliser un paramètre de connexion OAuth, consultez [Ajouter l’authentification à votre bot](bot-builder-authentication.md).
