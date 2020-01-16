---
title: Connecter un bot à Facebook Messenger - Bot Service
description: Découvrez comment configurer la connexion d’un bot à Facebook Messenger.
keywords: Facebook Messenger, bot de canal, application Facebook, ID d’application, clé secrète d’application, bot Facebook, informations d’identification
manager: kamrani
ms.topic: article
ms.author: kamrani
ms.service: bot-service
ms.date: 10/28/2019
ms.openlocfilehash: c4c2d9fbc048524f7301aabb79e6524a6cd19021
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75792137"
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

> [!NOTE]
> Le 16 décembre 2019, Workplace by Facebook a changé le modèle de sécurité relatif aux intégrations personnalisées.  Les intégrations antérieures réalisées à l'aide de Microsoft Bot Framework v4 doivent être mises à jour pour utiliser les adaptateurs Facebook de Bot Framework conformément aux instructions ci-dessous avant le 28 février 2020.  
>
> Facebook prendra uniquement en compte les intégrations disposant d'un accès limité aux données de l'espace de travail (autorisations à faible sensibilité) et éligibles à une utilisation continue jusqu'au 31 décembre 2020 si ces intégrations ont passé et réussi les tests de sécurité RFI et si le développeur prend contact avec le [Support direct](https://my.workplace.com/work/admin/direct_support) avant le 15 janvier 2020 pour solliciter une utilisation continue de l'application.
> 
> Des adaptateurs Bot Framework sont disponibles pour les bots [JavaScript/Node.js](https://aka.ms/botframework-workplace-adapter) et [C#/.NET](https://aka.ms/bf-workplace-csharp).
 
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

### <a name="update-your-bot-code-with-facebook-adapter"></a>Mettre à jour le code de votre bot avec l'adaptateur Facebook

Le code source de votre bot doit être mis à jour pour inclure un adaptateur permettant de communiquer avec Workplace by Facebook. Des adaptateurs sont disponibles pour les bots [JavaScript/Node.js](https://aka.ms/botframework-workplace-adapter) et [C#/.NET](https://aka.ms/bf-workplace-csharp).

### <a name="provide-facebook-credentials"></a>Fournir les informations d’identification Facebook

Vous devrez mettre à jour le fichier appsettings.json de votre bot avec les valeurs des champs **Facebook App ID** (ID d'application Facebook), **Facebook App Secret** (Secret d'application Facebook), et **Page Access Token** (Jeton d'accès à la page) que vous avez précédemment copiées à partir de Facebook Workplace. Au lieu d’un ID de page traditionnel, utilisez les chiffres qui suivent le nom d’intégration sur la page **À propos de**. Suivez ces instructions pour mettre à jour le code source de votre bot [JavaScript/Node.js](https://aka.ms/botframework-workplace-adapter) ou [C#/.NET](https://aka.ms/bf-workplace-csharp).

### <a name="submit-for-review"></a>Envoyer pour vérification
Reportez-vous à la section **Connecter un bot à Facebook Messenger** et la [documentation du développeur Workplace](https://developers.facebook.com/docs/workplace) pour plus d’informations.

### <a name="make-the-app-public-and-publish-the-page"></a>Rendre l’application publique et publier la page
Reportez-vous à la section **Connecter un bot à Facebook Messenger** pour plus d’informations.

### <a name="setting-the-api-version"></a>Définition de la version de l’API

Si vous recevez une notification de Facebook sur la dépréciation d’une certaine version de l’API Graph, accédez à la [page Développeurs Facebook](https://developers.facebook.com). Accédez aux **paramètres de l’application** de votre bot et à **Paramètres > Avancé > Upgrade API version** (Mettre à niveau la version de l’API), puis faites passer **Upgrade All Calls**  (Mettre à niveau tous les appels) sur 3.0.

![Mise à niveau de la version de l’API](media/channels/fb-version-upgrade.png)

## <a name="connect-a-bot-to-facebook-using-the-facebook-adapter"></a>Connecter un bot à Facebook à l'aide de l'adaptateur Facebook

Outre le canal disponible dans Azure Bot Service pour connecter votre bot à Facebook, vous pouvez également utiliser l'adaptateur Facebook. Dans cet article, vous allez apprendre à connecter un bot à Facebook à l'aide de l'adaptateur.  Cet article vous guide tout au long de la procédure de modification de l'exemple EchoBot pour le connecter à Facebook.

> [!NOTE]
> Les instructions ci-dessous s'appliquent à l'implémentation C# de l'adaptateur Facebook. Pour en savoir plus sur l'utilisation de l'adaptateur JS, qui fait partie des bibliothèques BotKit, [consultez la documentation BotKit Facebook](https://botkit.ai/docs/v4/platforms/facebook.html).

### <a name="prerequisites"></a>Conditions préalables requises

* L'[exemple de code EchoBot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Un compte Facebook for Developers. Si vous n'avez pas de compte, vous pouvez [en créer un ici](https://developers.facebook.com).

### <a name="create-a-facebook-app-page-and-gather-credentials"></a>Créer une application et une page Facebook et collecter les informations d'identification

1. Connectez-vous à [https://developers.facebook.com](https://developers.facebook.com). Cliquez sur **Mes applications** dans le menu principal, puis sur **Créer une application** dans le menu déroulant. 

![Créer une application](media/bot-service-channel-connect-facebook/create-app-button.png)

2. Dans la fenêtre contextuelle qui apparaît, entrez un nom pour votre nouvelle application, puis cliquez sur **Créer un ID d'application**. 

![Définir le nom de l'application](media/bot-service-channel-connect-facebook/app-name.png)

#### <a name="set-up-messenger-and-associate-a-facebook-page"></a>Configurer Messenger et associer une page Facebook

1. Une fois votre application créée, vous verrez une liste de produits qui peuvent être configurés. Cliquez sur le bouton **Configurer** en regard du produit **Messenger**.

2. Vous devez alors associer votre nouvelle application à une page Facebook (si vous n'avez aucune page à utiliser, vous pouvez en créer une en cliquant sur **Créer une page** dans la section **Jetons d'accès**). Cliquez sur **Ajouter ou supprimer des pages**, sélectionnez la page que vous souhaitez associer à votre application et cliquez sur **Suivant**. Laissez le paramètre **Gérer et accéder aux conversations de page sur Messenger** activé et cliquez sur **Terminé**.

![Configurer Messenger](media/bot-service-channel-connect-facebook/app-page-permissions.png)

3. Après avoir associé votre page, cliquez sur le bouton **Générer un jeton** pour générer un jeton d'accès à la page.  Prenez note de ce jeton car vous en aurez besoin au moment de la configuration de votre application bot.

#### <a name="obtain-your-app-secret"></a>Récupérer la question secrète de votre application

1. Dans le menu de gauche, cliquez sur **Paramètres**, puis sur **De base** pour accéder à la page des paramètres de base de votre application.

2. Sur la page des paramètres de base, cliquez sur le bouton **Afficher** en regard de la **Question secrète de votre application**.  Prenez note de cette question secrète car vous en aurez besoin au moment de la configuration de votre application bot. 

### <a name="wiring-up-the-facebook-adapter-in-your-bot"></a>Connecter l'adaptateur Facebook dans votre bot

Maintenant que vous disposez de votre application Facebook, de votre page et de vos informations d'identification, vous devez configurer votre application bot.

#### <a name="install-the-facebook-adapter-nuget-package"></a>Installer le package NuGet de l'adaptateur Facebook

Ajoutez le package NuGet [Microsoft.Bot.Builder.Adapters.Facebook](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Facebook/). Pour plus d'informations sur l'utilisation de NuGet, consultez [Installer et gérer des packages dans Visual Studio](https://aka.ms/install-manage-packages-vs).

#### <a name="create-a-facebook-adapter-class"></a>Créer une classe d'adaptateur Facebook

Créer une classe qui hérite de la classe ***FacebookAdapter***. Cette classe sera notre adaptateur pour le canal Facebook et inclura des fonctionnalités de traitement des erreurs (comme pour la classe ***BotFrameworkAdapterWithErrorHandler*** déjà présente dans l'exemple et utilisée pour traiter d'autres requêtes d'Azure Bot Service).

```csharp
public class FacebookAdapterWithErrorHandler : FacebookAdapter
{
    public FacebookAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
            : base(configuration, logger)
        {
            OnTurnError = async (turnContext, exception) =>
            {
                // Log any leaked exception from the application.
                logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");

                // Send a message to the user
                await turnContext.SendActivityAsync("The bot encountered an error or bug.");
                await turnContext.SendActivityAsync("To continue to run this bot, please fix the bot source code.");

                // Send a trace activity, which will be displayed in the Bot Framework Emulator
                await turnContext.TraceActivityAsync("OnTurnError Trace", exception.Message, "https://www.botframework.com/schemas/error", "TurnError");
            };
        }
}
```

#### <a name="create-a-new-controller-for-handling-facebook-requests"></a>Créer un contrôleur pour traiter les requêtes Facebook

Créez un contrôleur qui traitera les requêtes de Facebook, sur un nouveau point de terminaison « api/facebook » au lieu du point de terminaison « api/messages » par défaut utilisé pour les requêtes des canaux Azure Bot Service.  L'ajout d'un point de terminaison supplémentaire à votre bot vous permettra d'accepter les requêtes des canaux Bot Service, ainsi que de Facebook, en utilisant le même bot.

```csharp
[Route("api/facebook")]
[ApiController]
public class FacebookController : ControllerBase
{
    private readonly FacebookAdapter _adapter;
    private readonly IBot _bot;

    public FacebookController(FacebookAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost]
    [HttpGet]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

#### <a name="inject-the-facebook-adapter-in-your-bot-startupcs"></a>Injecter l'adaptateur Facebook dans le fichier startup.cs de votre bot

Dans votre fichier startup.cs, ajoutez la ligne suivante à la méthode ***ConfigureServices***. Cela permettra d'enregistrer votre adaptateur Facebook et de le mettre à la disposition de votre nouvelle classe de contrôleur.  Les paramètres de configuration que vous avez ajoutés à l'étape précédente seront automatiquement utilisés par l'adaptateur.

```csharp
services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();
```

Une fois ajoutée, votre méthode ***ConfigureServices*** doit être semblable à ce qui suit.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Facebook Adapter
    services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

#### <a name="obtain-a-url-for-your-bot"></a>Récupérer l'URL de votre bot

Maintenant que vous avez connecté l'adaptateur dans votre projet de bot, vous devez fournir à Facebook le point de terminaison qui convient à votre application, afin que votre bot puisse recevoir des messages. Vous aurez également besoin de cette URL pour terminer la configuration de votre application bot.

Pour accomplir cette étape, [déployez votre bot sur Azure](https://aka.ms/bot-builder-deploy-az-cli) et notez l'URL du bot déployé.

> [!NOTE]
> Si vous n'êtes pas prêt à déployer votre bot sur Azure, ou si vous souhaitez le déboguer lors de l'utilisation de l'adaptateur Facebook, vous pouvez utiliser un outil tel que [ngrok](https://www.ngrok.com) (que vous aurez probablement déjà installé si vous avez précédemment utilisé l'émulateur Bot Framework) pour passer par le bot local et obtenir une URL publiquement accessible. 
> 
> Si vous souhaitez créer un tunnel ngrok et obtenir une URL d'accès à votre bot, utilisez la commande suivante dans une fenêtre de terminal (cela suppose que votre bot local s'exécute sur le port 3978 ; si ce n'est pas le cas, modifiez les numéros de port dans la commande).
> 
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### <a name="add-facebook-app-settings-to-your-bots-configuration-file"></a>Ajouter les paramètres de l'application Facebook au fichier de configuration de votre bot

Ajoutez les paramètres ci-dessous au fichier appSettings.json de votre projet de bot. Vous devez renseigner les champs **FacebookAppSecret** et **FacebookAccessToken** à l'aide des valeurs que vous avez recueillies lors de la création et de la configuration de votre application Facebook. **FacebookVerifyToken** doit être une chaîne aléatoire que vous créez et qui sera utilisée pour vérifier que le point de terminaison de votre bot est authentique lorsqu'il sera appelé par Facebook.


```json
  "FacebookVerifyToken": "",
  "FacebookAppSecret": "",
  "FacebookAccessToken": ""
```

Après avoir renseigné les champs ci-dessus, vous devez redéployer votre bot (ou le redémarrer en cas d'exécution locale avec ngrok).

### <a name="complete-configuration-of-your-facebook-app"></a>Terminer la configuration de votre application Facebook

La dernière étape consiste à configurer le point de terminaison Messenger de votre application Facebook pour veiller à ce que votre bot reçoive les messages.

1. Dans le tableau de bord de votre application, cliquez sur **Messenger** à partir du menu de gauche, puis sur **Paramètres**.

2. Dans la section **Webhooks**, cliquez sur **Ajouter une URL de rappel**.

3. Dans la zone de texte **URL de rappel**, entrez l'URL de votre bot, ainsi que le point de terminaison `api/facebook` que vous avez spécifié dans le contrôleur nouvellement créé. Par exemple : `https://yourboturl.com/api/facebook`. Dans la zone de texte **Jeton de vérification**, entrez le jeton que vous avez précédemment créé et utilisé dans le fichier appSettings.json de votre application bot.

![Modifier l'URL de rappel](media/bot-service-channel-connect-facebook/edit-callback-url.png)

4. Cliquez sur **Verify and Save** (Vérifier et enregistrer). Assurez-vous que votre bot est en cours d'exécution, car Facebook adressera une requête au point de terminaison de votre application et procédera à une vérification à l'aide de votre **jeton de vérification**.

5. Une fois votre URL de rappel vérifiée, cliquez sur le bouton **Ajouter des abonnements** désormais affiché.  Dans la fenêtre contextuelle, sélectionnez les abonnements suivants, puis cliquez sur **Enregistrer**.

* **messages**
* **messaging_postbacks**
* **messaging_optins**
* **messaging_deliveries**

![Abonnements webhook](media/bot-service-channel-connect-facebook/webhook-subscriptions.png)

### <a name="test-your-bot-with-adapter-in-facebook"></a>Tester votre bot avec l'adaptateur Facebook

Vous pouvez maintenant vérifier que votre bot est correctement connecté à Facebook en envoyant un message via la page Facebook que vous avez associée à votre nouvelle application Facebook.  

1. Accédez à votre page Facebook.

2. Cliquez sur le bouton **Ajouter un bouton**.

![Ajouter un bouton](media/bot-service-channel-connect-facebook/add-button.png)

3. Sélectionnez **Vous contacter** et **Envoyer un message**, puis cliquez sur **Suivant**.

![Ajouter un bouton](media/bot-service-channel-connect-facebook/button-settings.png)

4. Lorsque la question **Où voulez-vous que ce bouton envoie les utilisateurs ?** s'affiche, sélectionnez **Messenger** et cliquez sur **Terminer**.

![Ajouter un bouton](media/bot-service-channel-connect-facebook/button-settings-2.png)

5. Pointez sur le nouveau bouton **Envoyer un message** qui apparaît maintenant sur votre page Facebook et cliquez sur **Tester le bouton** dans le menu contextuel.  Cela permet d'entamer une nouvelle conversation avec votre application via Facebook Messenger, que vous pouvez utiliser pour tester les échanges de messages avec votre bot. Une fois le message reçu par votre bot, il vous renvoie un message contenant le même texte.

Vous pouvez également tester cette fonctionnalité à l'aide de l'[exemple de bot relatif à l'adaptateur Facebook](https://aka.ms/csharp-61-facebook-adapter-sample) en renseignant le fichier appSettings.json avec les mêmes valeurs que celles décrites dans les étapes ci-dessus.

## <a name="see-also"></a>Voir aussi

- **Exemple de code**. Utilisez l’exemple de bot <a href="https://aka.ms/facebook-events" target="_blank">Facebook-events</a> pour explorer la communication du bot avec Facebook Messenger.
