---
title: Connecter un bot à Facebook Messenger | Microsoft Docs
description: Découvrez comment configurer la connexion d’un bot à Facebook Messenger.
keywords: Facebook Messenger, bot de canal, application Facebook, ID d’application, clé secrète d’application, bot Facebook, informations d’identification
manager: kamrani
ms.topic: article
ms.author: kamrani
ms.service: bot-service
ms.date: 08/03/2019
ms.openlocfilehash: a856e3cc578b8c73583126df9f670bfde68ec9dc
ms.sourcegitcommit: dd12ddf408c010182b09da88e2aac0de124cef22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70386107"
---
# <a name="connect-a-bot-to-facebook"></a>Connecter un bot à Facebook

Votre bot peut être connecté à Facebook Messenger et à Facebook Workplace, afin qu’il puisse communiquer avec les utilisateurs de ces deux plateformes. Le tutoriel suivant montre comment connecter un bot à ces deux canaux.

> [!NOTE]
> L’interface utilisateur de Facebook peut être légèrement différente selon la version que vous utilisez.

## <a name="connect-a-bot-to-facebook-messenger"></a>Connecter un bot à Facebook Messenger

Pour en savoir plus sur le développement pour Facebook Messenger, consultez la [documentation de la plate-forme Messenger](https://developers.facebook.com/docs/messenger-platform). Si vous le souhaitez consultez les [directives de prélancement](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public), le [guide de démarrage rapide](https://developers.facebook.com/docs/messenger-platform/guides/quick-start) et le [guide de configuration](https://developers.facebook.com/docs/messenger-platform/guides/setup) Facebook.

Pour configurer un bot de sorte qu’il communique à l’aide de Facebook Messenger, activez Facebook Messenger sur une page Facebook, puis connectez le bot.

### <a name="copy-the-page-id"></a>Copier l’ID de la page

Le bot est accessible par le biais d’une page Facebook.

1. [Créez une page Facebook](https://www.facebook.com/bookmarks/pages) ou accédez à une page existante.

1. Ouvrez la page **About** (À propos de) de Facebook, puis copiez et enregistrez la valeur **Page ID** (ID de la page).

### <a name="create-a-facebook-app"></a>Créer une application Facebook

1. Dans votre navigateur, accédez à [Create a new Facebook App](https://developers.facebook.com/quickstarts/?platform=web) (Créer une application Facebook).
1. Entrez le nom de votre application, puis cliquez sur le bouton **Create New Facebook App ID** (Créer un ID d’application Facebook).

    ![Créer une application](media/channels/fb-create-messenger-bot-app.png)

1. Dans la boîte de dialogue qui s’affiche, entrez votre adresse e-mail, puis cliquez sur le bouton **Create App ID** (Créer un ID d’application).

    ![Créer un ID d’application](media/channels/fb-create-messenger-bot-app-id.png)

1. Suivez les étapes de l’Assistant.

1. Entrez les informations de vérification demandées, puis cliquez sur le bouton **Skip Quick Start**(Ignorer le démarrage rapide) en haut à droite.

1. Dans le volet gauche de la fenêtre qui s’affiche ensuite, développez *Settings* (Paramètres), puis cliquez sur **Basic** (De base).

1. Dans le volet droit, copiez et enregistrez les valeurs **App ID** (ID d’application) et **App Secret** (Secret d’application).

    ![Copier App ID (ID d’application) et App Secret (Secret d’application)](media/channels/fb-messenger-bot-get-appid-secret.png)

1. Dans le volet gauche, sous *Settings* (Paramètres), cliquez sur **Advanced** (Avancés).

1. Dans le volet droit, positionnez le curseur **Allow API Access to App Settings** (Autoriser l’accès de l’API aux paramètres d’application) sur **Yes** (Oui).

    ![Copier App ID (ID d’application) et App Secret (Secret d’application)](media/channels/fb-messenger-bot-api-settings.png)

1. En bas à droite de la page, cliquez sur le bouton **Save changes** (Enregistrer les modifications).

### <a name="enable-messenger"></a>Activer Messenger

1. Dans le volet gauche, cliquez sur **Dashboard** (Tableau de bord).
1. Dans le volet droit, faites défiler la page, puis dans la zone **Messenger**, cliquez sur le bouton **Set Up** (Configurer). L’entrée Messenger est affichée sous la section *PRODUCTS* (Produits) dans le volet gauche.  

    ![Activer Messenger](media/channels/fb-messenger-bot-enable-messenger.png)

### <a name="generate-a-page-access-token"></a>Générer un jeton d’accès à la page

1. Dans le volet gauche, sous l’entrée Messenger, cliquez sur **Settings** (Paramètres).
1. Dans le volet droit, faites défiler la page, puis dans la section **Token Generation**, (Génération de jetons), sélectionnez la page cible.

    ![Activer Messenger](media/channels/fb-messenger-bot-select-messenger-page.png)

1. Cliquez sur le bouton **Edit Permissions** (Modifier les autorisations) pour octroyer pages_messaging à l’application afin de générer un jeton d’accès.
1. Suivez les instructions de l’Assistant. Dans la dernière étape, acceptez les paramètres par défaut et cliquez sur le bouton **Done** (Terminé). À la fin, un **jeton d’accès de page** est généré.

    ![Autorisations Messenger](media/channels/fb-messenger-bot-permissions.png)

1. Copiez et enregistrez le jeton sous **Page Access Token** (Jeton d’accès à la page).

### <a name="enable-webhooks"></a>Activer les webhooks

Pour envoyer des messages et d’autres événements à partir de votre bot à Facebook Messenger, vous devez activer l’intégration de webhooks. À ce stade, laissons les étapes de paramétrage de Facebook de côté. Nous y reviendrons plus tard.

1. Dans votre navigateur, ouvrez une nouvelle fenêtre et accédez au [Portail Azure](https://portal.azure.com/). 

1. Dans la liste des ressources, cliquez sur l’inscription aux ressources du bot puis, dans le panneau correspondant, cliquez sur **Canaux**.

1. Dans le volet droit, cliquez sur l’icône **Facebook**.

1. Dans l’Assistant, entrez les informations Facebook stockées dans les étapes précédentes. Si les informations sont correctes, vous devriez voir en bas de l’Assistant l’**URL de rappel** et le **jeton de vérification**. Copiez-les et stockez-les.  

    ![configuration du canal messenger dans fb](media/channels/fb-messenger-bot-config-channel.PNG)

1. Cliquez sur le bouton **Enregistrer** .


1. Revenons aux paramètres Facebook. Dans le volet droit, faites défiler la page jusqu’à la section **Webhooks**, puis cliquez sur le bouton **Subscribe To Events** (S’abonner à des événements). Cette opération a pour but de transférer les événements de messagerie de Facebook Messenger au bot.

    ![Activer les webhooks](media/channels/fb-messenger-bot-webhooks.PNG)

1. Dans la boîte de dialogue qui s’affiche, entrez les valeurs **Callback URL** (URL de rappel) et **Verify Token** (jeton de vérification) stockées précédemment. Sous **Subscription Fields** (Champs d’abonnement), sélectionnez *message\_deliveries* (remise des messages), *messages*, *messaging\_options* (options de messagerie) et *messaging\_postbacks* (publications de messagerie).

    ![Configurer les webhooks](media/channels/fb-messenger-bot-config-webhooks.png)

1. Cliquez sur le bouton **Verify and Save** (Vérifier et enregistrer).
1. Sélectionnez la page Facebook pour abonner le webhook. Cliquez sur le bouton **Subscribe** (S’abonner).

    ![Page de configuration des webhooks](media/channels/fb-messenger-bot-config-webhooks-page.PNG)

### <a name="submit-for-review"></a>Envoyer pour vérification

Facebook requiert une URL de politique de confidentialité et une URL de conditions d’utilisation sur sa page de paramètres d’application de base. La page [Code of Conduct](https://investor.fb.com/corporate-governance/code-of-conduct/default.aspx) (Code de conduite) contient des liens vers les ressources tierces, aidant à créer une politique de confidentialité. La page [Terms of Use](https://www.facebook.com/terms.php) (Conditions d’utilisation) contient des exemples de conditions aidant à créer un document de conditions d’utilisation approprié.

Une fois le bot terminé, Facebook applique son propre [processus de vérification](https://developers.facebook.com/docs/messenger-platform/app-review) pour les applications publiées sur Messenger. Facebook teste le bot pour vérifier qu’il est conforme aux [politiques de la plate-forme](https://developers.facebook.com/docs/messenger-platform/policy-overview) Facebook.

### <a name="make-the-app-public-and-publish-the-page"></a>Rendre l’application publique et publier la page

> [!NOTE]
> Une application reste en [mode de développement](https://developers.facebook.com/docs/apps/managing-development-cycle) jusqu’à sa publication. Les fonctionnalités de plug-in et d’API fonctionnent uniquement pour les administrateurs, les développeurs et les testeurs.

Une fois la vérification effectuée, dans le tableau de bord de l’application, sous App Review (Vérification de l’application), définissez l’application sur Publique.
Assurez-vous que la page Facebook associée à ce bot est publiée. L’état s’affiche dans les paramètres des pages.

## <a name="connect-a-bot-to-facebook-workplace"></a>Connecter un bot à Facebook Workplace

Facebook Workplace est une version orientée entreprise de Facebook, qui permet aux employés de se connecter et de collaborer facilement. Il contient des vidéos en direct, des flux d’actualités, des groupes, des messages, des réactions, des recherches et des publications populaires. Il prend également en charge :

- Analytique et intégrations. Tableau de bord avec analytique, intégration, authentification unique et fournisseurs d’identité que les entreprises utilisent pour intégrer Workplace à leurs systèmes informatiques existants.
- Groupes à plusieurs entreprises. Espaces partagés dans lesquels les employés de différentes organisations peuvent travailler ensemble et collaborer.

Consultez le [centre d’aide Workplace](https://workplace.facebook.com/help/work/) pour en savoir plus sur Facebook Workplace, et la [documentation du développeur Workplace](https://developers.facebook.com/docs/workplace) pour obtenir des instructions sur le développement pour Facebook Workplace.

Pour utiliser Facebook Workplace avec votre bot, vous devez créer un compte Workplace et une intégration personnalisée pour connecter le bot.

### <a name="create-a-workplace-premium-account"></a>Créer un compte Workplace Premium

1. Soumettez une demande à [Workplace](https://www.facebook.com/workplace) pour le compte de votre société.
1. Une fois votre demande approuvée, vous recevrez un e-mail vous invitant à participer. La réponse peut prendre un certain temps.
1. À partir de l’invitation par e-mail, cliquez sur **Get Started** (Prise en main).
1. Entrez vos informations de profil.
    > [!TIP]
    > Définissez-vous comme administrateur système. Gardez à l’esprit que seuls les administrateurs système peuvent créer des intégrations personnalisées.
1. Cliquez sur **Preview Profile** (Aperçu du profil) et vérifiez que les informations sont correctes.
1. Accédez à la *version d’évaluation gratuite*.
1. Créez un **mot de passe**.
1. Cliquez sur **Inviter des collègues**  pour inviter des employés à se connecter. Les employés que vous avez invités deviennent membres dès qu’ils se connectent. Ils passent par un processus de connexion similaire à celui décrit dans la présente procédure.

### <a name="create-a-custom-integration"></a>Créer une intégration personnalisée

Créez une [intégration personnalisée](https://developers.facebook.com/docs/workplace/custom-integrations-new) pour Workplace en suivant les étapes décrites ci-après. Lorsque vous créez une intégration personnalisée, une application avec des autorisations définies et une page de type « Bot », visible uniquement au sein de votre communauté Workplace, sont créées.

1. Dans le panneau d’administration (**Admin Panel**), ouvrez l’onglet **Integrations** (Integrations).
1. Cliquez sur le bouton **Create your own custom App** (Créer votre application personnalisée).

    ![Intégration avec Workplace](media/channels/fb-integration.png)

1. Choisissez un nom d’affichage et une image de profil pour l’application. Ces informations seront partagées avec la page de type « Bot ».
1. Positionnez le curseur **Allow API Access to App Settings** (Autoriser l’accès de l’API aux paramètres d’application) sur « Yes » (Oui).
1. Copiez et stockez en toute sécurité les valeurs App ID (ID d’application), App Secret (Clé secrète d’application) et App Token (Jeton d’application) qui s’affichent.

    ![Clés Workplace](media/channels/fb-keys.png)

1. Vous avez créé une intégration personnalisée. Vous trouverez la page de type « Bot » dans votre communauté Workplace, comme indiqué ci-dessous.

    ![Page Workplace](media/channels/fb-page.png)

### <a name="provide-facebook-credentials"></a>Fournir les informations d’identification Facebook

Dans le Portail Azure, collez les valeurs des champs **Facebook App ID** (ID d’application Facebook), **Facebook App Secret** (Secret d’application Facebook), et **Page Access Token** (Jeton d’accès à la page) que vous avez précédemment copiées à partir de Facebook Workplace. Au lieu d’un ID de page traditionnel, utilisez les chiffres qui suivent le nom d’intégration sur la page **À propos de**. Comme pour la connexion d’un bot à Facebook Messenger, les webhooks peuvent être connectés à l’aide des informations d’identification figurant dans Azure.

### <a name="submit-for-review"></a>Envoyer pour vérification
Reportez-vous à la section **Connecter un bot à Facebook Messenger** et la [documentation du développeur Workplace](https://developers.facebook.com/docs/workplace) pour plus d’informations.

### <a name="make-the-app-public-and-publish-the-page"></a>Rendre l’application publique et publier la page
Reportez-vous à la section **Connecter un bot à Facebook Messenger** pour plus d’informations.

## <a name="setting-the-api-version"></a>Définition de la version de l’API

Si vous recevez une notification de Facebook sur la dépréciation d’une certaine version de l’API Graph, accédez à la [page Développeurs Facebook](https://developers.facebook.com). Accédez aux **paramètres de l’application** de votre bot et à **Paramètres > Avancé > Upgrade API version** (Mettre à niveau la version de l’API), puis faites passer **Upgrade All Calls**  (Mettre à niveau tous les appels) sur 3.0.

![Mise à niveau de la version de l’API](media/channels/fb-version-upgrade.png)

## <a name="see-also"></a>Voir aussi

- **Exemple de code**. Utilisez l’exemple de bot <a href="https://aka.ms/facebook-events" target="_blank">Facebook-events</a> pour explorer la communication du bot avec Facebook Messenger.

- **Disponible en tant qu’adaptateur**. Ce canal est également [disponible en tant qu’adaptateur](https://botkit.ai/docs/v4/platforms/facebook.html). Pour vous aider à choisir entre un adaptateur et un canal, consultez [Adaptateurs disponibles actuellement](bot-service-channel-additional-channels.md#currently-available-adapters).
