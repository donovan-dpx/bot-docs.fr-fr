---
title: Ajouter l’authentification à votre bot par le biais d’Azure Bot Service | Microsoft Docs
description: Découvrez comment utiliser les fonctionnalités d’authentification d’Azure Bot Service pour ajouter l’authentification unique à votre bot.
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/22/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: da7c9f76c65a70acc8cbb4a12ee93fc8f99de53b
ms.sourcegitcommit: a6d02ec4738e7fc90b7108934740e9077667f3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70299565"
---
<!-- 

ms.author: kamrani

Related TODO:
- Check code in [Web Chat channel](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat?view=azure-bot-service-4.0)
- Check guidance in [DirectLine authentication](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0)
-->

<!-- General TODO: (Feedback from CSE (Nafis))
- Add note that: token management is based on user ID
- Explain why/how to share existing website authentication with a bot.
- Risk: Even people who use a DirectLine token can be vulnerable to user ID impersonation.
    Docs/samples that show exchange of secret for token don't specify a user ID, so an attacker can impersonate a different user by modifying the ID client side. There's a [blog post](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fblog.botframework.com%2F2018%2F09%2F01%2Fusing-webchat-with-azure-bot-services-authentication%2F&data=02%7C01%7Cv-jofing%40microsoft.com%7Cee005e1c9d2c4f4e7ea508d6b231b422%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636892323874079713&sdata=b0DWMxHzmwQvg5EJtlqKFDzR7fYKmg10fXma%2B8zGqEI%3D&reserved=0) that shows how to do this properly.
"Major issues":
- This doc is a sample walkthrough, but there's no deeper documentation explaining how the Azure Bot Service is handling tokens. How does the OAuth flow work? Where is it storing my users' access tokens? What's the security and best practices around using it?

"Minor issues":
- AAD v2 steps tell you to add delegated permission scopes during registration, but this shouldn't be necessary in AAD v2 due to dynamic scopes. (Ming, "This is currently necessary because scopes are not exposed through our runtime API. We don’t currently have a way for the developer to specify which scope he wants at runtime.")

- "The scope of the connection setting needs to have both openid and a resource in the Azure AD graph, such as Mail.Read." Unclear if I need to take some action at this point to make happen. Kind of out of context. I'm registering an AAD application in the portal, there's no connection setting
- Does the bot need all of these scopes for the samples? (e.g. "Read all users' basic profiles")
-->

# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Ajouter l’authentification à votre bot par le biais d’Azure Bot Service

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Azure Bot Service et le SDK v4 incluent de nouvelles fonctionnalités d’authentification de bot, facilitant le développement d’un bot qui authentifie les utilisateurs auprès de divers fournisseurs d’identité tels qu’Azure AD (Azure Active Directory), GitHub ou Uber, entre autres. Ces fonctionnalités peuvent améliorer l’expérience utilisateur en supprimant la _vérification du code magique_ pour certains clients.

Auparavant, votre bot devait inclure des contrôleurs OAuth et des liens de connexion, stocker les ID et secrets clients cibles et effectuer la gestion des jetons d’utilisateur. Le bot demandait à l’utilisateur de se connecter à un site web, ce qui générait alors un _code magique_ que l’utilisateur pouvait utiliser pour vérifier son identité.

Désormais, les développeurs de bots n’ont plus besoin d’héberger des contrôleurs OAuth ou de gérer le cycle de vie des jetons, ces tâches pouvant maintenant être effectuées par Azure Bot Service.

Cette API offre les fonctionnalités suivantes :

- Des améliorations des canaux pour prendre en charge de nouvelles fonctionnalités d’authentification, telles que les nouvelles bibliothèques WebChat et DirectLineJS visant à éliminer la nécessité de la vérification du code magique à 6 chiffres.
- Des améliorations du portail Azure pour ajouter, supprimer et configurer les paramètres de connexion à divers fournisseurs d’identité OAuth.
- La prise en charge d’une variété de fournisseurs d’identité prêts à l’emploi, dont Azure AD (points de terminaison v1 et v2) et GitHub.
- Des mises à jour des SDK Bot Framework C# et Node.js permettant de récupérer des jetons, de créer des cartes OAuthCards et de gérer les événements TokenResponse.
- Des exemples montrant comment créer un bot qui s’authentifie auprès d’Azure AD.

Pour plus d’informations sur la façon dont Azure Bot Service gère l’authentification, consultez [Authentification des utilisateurs au sein d’une conversation](bot-builder-concept-authentication.md).

Vous pouvez vous appuyer sur les étapes décrites dans cet article pour ajouter des fonctionnalités à un bot existant. Ces exemples de bots illustrent les nouvelles fonctionnalités d’authentification.

> [!NOTE]
> Les fonctionnalités d’authentification fonctionnent également avec BotBuilder v3. Toutefois, cet article traite uniquement l’exemple de code v4.

### <a name="about-this-sample"></a>À propos de cet exemple

Vous devez créer une ressource de bot Azure et vous avez besoin des éléments suivants :

1. Une inscription d’application Azure AD pour autoriser votre bot à accéder à une ressource externe, telle qu’Office 365.
1. Une ressource de bot distincte. La ressource de bot inscrit les informations d’identification de votre bot. De plus, vous avez besoin de ces dernières pour tester les fonctionnalités d’authentification, même lors de l’exécution de votre code de bot localement.

> [!IMPORTANT]
> Chaque fois que vous inscrivez un bot dans Azure, une application Azure AD lui est affectée. Toutefois, cette application sécurise l’accès du canal vers le bot. Vous avez besoin d’une application AAD supplémentaire pour chaque application que le bot doit être en mesure d’authentifier au nom de l’utilisateur.

Cet article décrit un exemple de bot qui se connecte à Microsoft Graph à l’aide d’un jeton Azure AD v1 ou v2. Il explique également comment créer et inscrire l’application Azure AD associée. Dans le cadre de ce processus, vous allez utiliser du code issu du dépôt GitHub [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples). Cet article aborde ces processus.

- **Créer votre ressource de bot**
- **Créer une application Azure AD**
- **Inscrire votre application Azure AD auprès de votre bot**
- **Préparer l’exemple de code du bot**

Une fois que vous aurez terminé, vous aurez un bot s’exécutant localement et pouvant répondre à quelques tâches simples par rapport à une application Azure AD, telles que la vérification et l’envoi d’un e-mail ou l’affichage de vos coordonnées et de celles de votre responsable. Pour ce faire, votre bot utilise un jeton à partir d’une application Azure AD par rapport à la bibliothèque Microsoft.Graph. Vous n’avez pas besoin de publier votre bot pour tester les fonctionnalités de connexion OAuth. Par contre, votre bot aura besoin d’un ID d’application Azure et d’un mot de passe valides.

### <a name="web-chat-and-direct-line-considerations"></a>Considérations relatives à Web Chat et à Direct Line

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

> [!IMPORTANT]
> Gardez à l’esprit ces importantes [considérations de sécurité](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md#security-considerations).

## <a name="prerequisites"></a>Prérequis

- Connaissance des [fonctions de base des robots][concept-basics], de la [gestion de l’état][concept-state], des [bibliothèques des boîtes de dialogue][concept-dialogs], de la façon d’[implémenter un flux de conversation séquentiel][simple-dialog] et de la façon de [réutiliser des boîtes de dialogues][component-dialogs].
- Connaissances du développement Azure et OAuth 2.0.
- Visual Studio version 2017 ou ultérieure, Node.js, npm et git.
- Un de ces exemples.

| Exemple | Version de Bot Builder | Illustre le |
|:---|:---:|:---|
| **Authentification de robots** dans [**CSharp**][cs-auth-sample]ou [**JavaScript**][js-auth-sample] | v4 | Prise en charge d’OAuthCard |
| **Authentification de robots MSGraph** dans [**CSharp**][cs-msgraph-sample]ou [**JavaScript**][js-msgraph-sample] | v4 |  Prise en charge de l’API Microsoft Graph avec OAuth 2 |

## <a name="create-your-bot-resource-on-azure"></a>Créer votre ressource de bot sur Azure

Créer une **ressource de robot** en utilisant le [Portail Azure](https://portal.azure.com/).

Pour plus d’informations, consultez [Créer un robot avec Azure Bot Service](./abs-quickstart.md).

## <a name="create-and-register-an-azure-ad-application"></a>Créer et inscrire une application Azure AD

Vous avez besoin d’une application Azure AD que votre bot peut utiliser pour se connecter à l’API Microsoft Graph.

Pour ce bot, vous pouvez utiliser des points de terminaison Azure AD v1 ou v2.
Pour plus d’informations sur les différences entre les points de terminaison v1 et v2, consultez la [comparaison v1-v2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) et la [vue d’ensemble du point de terminaison Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

### <a name="create-your-azure-ad-application"></a>Créer votre application Azure AD

Suivez ces étapes pour créer une application Azure AD. Vous pouvez utiliser les points de terminaison v1 ou v2 avec l’application que vous créez.

> [!TIP]
> Vous devrez créer et enregistrer l’application Azure AD dans un locataire dans lequel vous pouvez consentir à déléguer les autorisations demandées par une application.

1. Ouvrez le panneau [Azure Active Directory][azure-aad-blade] dans le portail Azure.
    Si vous n’êtes pas dans le locataire approprié, cliquez sur **Changer de répertoire** pour basculer vers le bon locataire. (Pour obtenir des instructions sur la création d’un locataire, consultez [Accéder au portail et créer un locataire](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).)
1. Ouvrez le panneau **Inscriptions d’applications**.
1. Dans le panneau **Inscriptions des applications**, cliquez sur **Nouvelle inscription**.
1. Renseignez les champs obligatoires et créez l’inscription d’application.

   1. Donnez un nom à votre application.
   1. Sélectionnez les **Types de comptes pris en charge** par votre application. (Toutes ces options fonctionnent avec cet exemple.)
   1. Pour **URI de redirection**
       1. Sélectionnez **Web**.
       1. Définissez l’URL sur `https://token.botframework.com/.auth/web/redirect`.
   1. Cliquez sur **S'inscrire**.

      - Une fois que sa création est effectuée, Azure affiche la page **Présentation** de l’application.
      - Enregistrez la valeur **ID d’application (client)** . Vous utiliserez cette valeur ultérieurement en tant qu’_ID client_ quand vous inscrirez votre application Azure AD auprès de votre bot.
      - Enregistrez également la valeur **ID de l’annuaire (locataire)** . Vous utiliserez également ces valeurs pour inscrire cette application avec votre bot.

1. Dans le volet de navigation, cliquez sur **Certificats et secrets** pour créer un secret pour votre application.

   1. Sous **Secrets client**, cliquez **+ Nouveau secret client**.
   1. Ajoutez une description pour différencier ce secret des autres secrets que vous allez peut-être créer pour cette application, par exemple `bot login`.
   1. Définissez **Expire** sur **Jamais**.
   1. Cliquez sur **Add**.
   1. Avant de quitter cette page, enregistrez le secret. Vous utiliserez cette valeur ultérieurement en tant que _Secret du client_ quand vous inscrirez votre application Azure AD auprès de votre bot.

1. Dans le volet de navigation, cliquez sur **API autorisées** pour ouvrir le panneau **API autorisées**. Il est recommandé de définir explicitement les autorisations d’API pour l’application.

   1. Cliquez sur **Ajouter une autorisation** pour afficher le volet **Demander des autorisations d’API**.
   1. Pour cet exemple, sélectionnez **API Microsoft** et **Microsoft Graph**.
   1. Choisissez **Autorisations déléguées** et vérifiez que les autorisations nécessaires sont sélectionnées. Cet exemple nécessite ces autorisations.

      > [!NOTE]
      > Pour votre bot, évitez les autorisations qui sont marquées comme **Consentement administrateur requis**, car elles nécessitent la connexion d’un utilisateur et d’un administrateur de locataires.

      - **openid**
      - **profile**
      - **Mail.Read**
      - **Mail.Send**
      - **User.Read**
      - **User.ReadBasic.All**

   1. Cliquez sur **Ajouter des autorisations**. (La première fois qu’un utilisateur accède à cette application par le biais du bot, il doit donner son consentement.)

Vous disposez maintenant d’une application Azure AD configurée.

### <a name="register-your-azure-ad-application-with-your-bot"></a>Inscrire votre application Azure AD auprès de votre bot

L’étape suivante consiste à inscrire auprès de votre bot l’application Azure AD que vous venez de créer.

#### <a name="azure-ad-v1"></a>Azure AD v1

1. Accédez à la page de ressources de votre bot sur le [portail Azure](http://portal.azure.com/).
1. Cliquez sur **Settings**.
1. Sous **Paramètres de connexion OAuth** près du bas de la page, cliquez sur **Ajouter un paramètre**.
1. Remplissez le formulaire comme suit :

    1. Pour **Nom**, entrez un nom pour votre connexion. Vous utiliserez ce nom dans le code de votre bot.
    1. Sous **Fournisseur de services**, sélectionnez **Azure Active Directory**. Une fois que vous avez sélectionné cette option, les champs propres à Azure AD sont affichés.
    1. Pour **ID client**, entrez l’ID d’application (client) que vous avez enregistré pour votre application Azure AD v1.
    1. Pour **Secret du client**, entrez le secret que vous avez créé pour accorder au bot l’accès à l’application Azure AD.
    1. Pour **Type d’autorisation**, entrez `authorization_code`.
    1. Pour **URL de connexion**, entrez `https://login.microsoftonline.com`.
    1.Pour **ID de locataire**, entrez l’**ID d’annuaire (locataire)** que vous avez enregistré plus tôt pour votre application AAD ou **commun** en fonction des types de compte pris en charge sélectionnés quand vous avez créé l’application ADD. Pour déterminer la valeur à attribuer, suivez ces critères :

        - Lors de la création de l’application AAD si vous avez sélectionné *Comptes dans cet annuaire d’organisation uniquement (Microsoft uniquement - Locataire unique)*  ou *Comptes dans cet annuaire organisationnel uniquement (Annuaire Microsoft AAD - Multilocataire)* , entrez l’**ID de locataire** que vous avez enregistré précédemment pour l’application AAD.

        - Toutefois, si vous avez sélectionné *Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire) et comptes Microsoft personnels (par exemple, Skype, Xbox, Outlook.com)* , entrez le mot **commun** au lieu d’un ID de locataire. Dans le cas contraire, l’application AAD vérifie par le biais du locataire dont l’ID a été sélectionné et exclut les comptes MS personnels.

       Il s’agit du locataire associé aux utilisateurs qui peuvent être authentifiés.

    1. Pour **URL de la ressource**, entrez `https://graph.microsoft.com/`.
    1. Laissez **Étendues** vide.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Ces valeurs permettent à votre application d’accéder aux données Office 365 via l’API Microsoft Graph.

#### <a name="azure-ad-v2"></a>Azure AD v2

1. Accédez à la page de l’inscription Bot Channels Registration de votre bot dans le [Portail Azure](http://portal.azure.com/).
1. Cliquez sur **Settings**.
1. Sous **Paramètres de connexion OAuth** près du bas de la page, cliquez sur **Ajouter un paramètre**.
1. Remplissez le formulaire comme suit :

    1. Pour **Nom**, entrez un nom pour votre connexion. Vous l’utiliserez dans le code de votre bot.
    1. Sous **Fournisseur de services**, sélectionnez **Azure Active Directory v2**. Une fois que vous avez sélectionné cette option, les champs propres à Azure AD sont affichés.
    1. Pour **ID client**, entrez l’ID d’application (client) que vous avez enregistré pour votre application Azure AD v1.
    1. Pour **Secret du client**, entrez le secret que vous avez créé pour accorder au bot l’accès à l’application Azure AD.
    1. Pour **ID de locataire**, entrez l’**ID d’annuaire (locataire)** que vous avez enregistré plus tôt pour votre application AAD ou **commun** en fonction des types de compte pris en charge sélectionnés quand vous avez créé l’application ADD. Pour déterminer la valeur à attribuer, suivez ces critères :

        - Lors de la création de l’application AAD si vous avez sélectionné *Comptes dans cet annuaire d’organisation uniquement (Microsoft uniquement - Locataire unique)*  ou *Comptes dans cet annuaire organisationnel uniquement (Annuaire Microsoft AAD - Multilocataire)* , entrez l’**ID de locataire** que vous avez enregistré précédemment pour l’application AAD.

        - Toutefois, si vous avez sélectionné *Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire) et comptes Microsoft personnels (par exemple, Skype, Xbox, Outlook.com)* , entrez le mot **commun** au lieu d’un ID de locataire. Dans le cas contraire, l’application AAD vérifie par le biais du locataire dont l’ID a été sélectionné et exclut les comptes MS personnels.

       Il s’agit du locataire associé aux utilisateurs qui peuvent être authentifiés.

    1. Pour **Étendues**, entrez les noms des autorisations que vous avez choisies au moment de l’inscription de l’application : `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Pour Azure AD v2, **Étendues** prend une liste de valeurs séparées par des espaces et respectant la casse.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Ces valeurs permettent à votre application d’accéder aux données Office 365 via l’API Microsoft Graph.

### <a name="test-your-connection"></a>Tester votre connexion

1. Cliquez sur l’entrée de connexion pour ouvrir la connexion que vous venez de créer.
1. En haut du volet **Paramètre de connexion du fournisseur de service**, cliquez sur **Tester la connexion**.
1. La première fois, cette action doit ouvrir un nouvel onglet de navigateur répertoriant les autorisations que votre application demande et vous invite à accepter.
1. Cliquez sur **Accepter**.
1. Cette action doit vous rediriger vers une page indiquant que la **connexion de test à \<votre_nom_de_connexion> a réussi**.

Vous pouvez maintenant utiliser ce nom de connexion dans le code de votre bot pour récupérer des jetons utilisateur.

## <a name="prepare-the-bot-code"></a>Préparer le code du bot

Vous avez besoin de l’ID d’application de votre bot et du mot de passe associé pour effectuer cette procédure.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

<!-- TODO: Add guidance (once we have it) on how not to hard-code IDs and ABS auth. -->

1. Clonez à partir du dépôt GitHub l’exemple que vous souhaitez utiliser : [**Authentification de robots**][cs-auth-sample] ou [**Authentification de robots avec MSGraph**][cs-msgraph-sample].
1. Mettez à jour **appsettings.json** :

    - Définissez `ConnectionName` sur le nom du paramètre de connexion OAuth que vous avez ajouté à votre bot.
    - Définissez `MicrosoftAppId` et `MicrosoftAppPassword` sur l’ID d’application et le secret d’application de votre bot.

      En fonction des caractères contenus dans le secret de votre bot, vous pouvez être amené à placer le mot de passe dans une séquence d’échappement XML. Par exemple, les esperluettes (&) doivent être encodées sous la forme `&amp;`.

    [!code-json[appsettings](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/appsettings.json)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

1. Clonez à partir du dépôt github ce que vous souhaitez utiliser : [**Authentification de robots**][js-auth-sample] ou [**Authentification de robots avec MSGraph**][js-msgraph-sample].
1. Mettez à jour **.env** :

    - Définissez `connectionName` sur le nom du paramètre de connexion OAuth que vous avez ajouté à votre bot.
    - Définissez les valeurs `MicrosoftAppId` et `MicrosoftAppPassword` sur l’ID d’application et le secret d’application de votre bot.

      En fonction des caractères contenus dans le secret de votre bot, vous pouvez être amené à placer le mot de passe dans une séquence d’échappement XML. Par exemple, les esperluettes (&) doivent être encodées sous la forme `&amp;`.

    [!code-txt[.env](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/.env)]

---

Si vous ne savez pas comment récupérer votre **ID d’application Microsoft** et votre **mot de passe d’application Microsoft**, vous pouvez créer un nouveau mot de passe [comme décrit ici](../bot-service-quickstart-registration.md#get-registration-password).

> [!NOTE]
> Vous pourriez dès à présent publier ce code de bot sur votre abonnement Azure (cliquer avec le bouton droit sur le projet et choisir **Publier**), mais cela n’est pas nécessaire pour cet article. Vous devriez définir une configuration de publication qui utilise l’application et le plan d’hébergement que vous avez utilisés quand vous avez configuré le bot dans le portail Azure.

## <a name="test-the-bot"></a>Tester le bot

1. Si ce n’est pas déjà fait, installez [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).
1. Exécutez l’exemple en local sur votre machine.
1. Démarrez l’émulateur, connectez-vous à votre bot et envoyez des messages.

    - Vous devez fournir l’ID et le mot de passe d’application de votre bot lorsque vous vous connectez à celui-ci.

        - Si vous deviez ajouter une séquence d’échappement XML à votre mot de passe dans le code de votre bot, vous devez également le faire ici.

    - Tapez `help` pour afficher la liste des commandes disponibles pour le bot, puis testez les fonctionnalités d’authentification.
    - Une fois connecté, vous n’avez pas besoin de refournir vos informations d’identification jusqu’à ce que vous vous déconnectiez.
    - Pour vous déconnecter et annuler votre authentification, tapez `logout`.

> [!NOTE]
> L’authentification de bot nécessite l’utilisation du service Bot Connector. Le service accède aux informations d’inscription aux canaux de bot pour votre bot.

# <a name="bot-authenticationtabbot-oauth"></a>[Authentification du bot](#tab/bot-oauth)

Dans l’exemple **Authentification du bot**, le dialogue est conçu pour récupérer le jeton utilisateur après la connexion de l’utilisateur.

![Exemple de sortie](media/how-to-auth/auth-bot-test.png)

# <a name="bot-authentication-msgraphtabbot-msgraph-auth"></a>[Authentification du bot avec MSGraph](#tab/bot-msgraph-auth)

Dans l’exemple **Authentification du bot avec MSGraph**, le dialogue est conçu pour accepter un ensemble limité de commandes après la connexion de l’utilisateur.

![Exemple de sortie](media/how-to-auth/msgraph-bot-test.png)

---

## <a name="additional-information"></a>Informations supplémentaires

Quand un utilisateur demande au bot de faire quelque chose qui nécessite sa connexion au bot, celui-ci peut utiliser `OAuthPrompt` pour lancer la récupération d’un jeton pour une connexion donnée. L’élément `OAuthPrompt` crée un flux de récupération de jeton qui comprend les étapes suivantes :

1. Vérifier si Azure Bot Service possède déjà un jeton pour l’utilisateur et la connexion actuels. S’il existe un jeton, il est retourné.
1. Si Azure Bot Service ne dispose pas de jeton mis en cache, un élément `OAuthCard` est créé. Il s’agit d’un bouton de connexion sur lequel l’utilisateur peut cliquer.
1. Une fois que l’utilisateur clique sur le bouton de connexion `OAuthCard`, Azure Bot Service envoie au bot le jeton de l’utilisateur directement ou il présente à l’utilisateur un code d’authentification à 6 chiffres dans la fenêtre de conversation.
1. Si un code d’authentification est présenté à l’utilisateur, le bot l’échange contre le jeton de l’utilisateur.

Les sections suivantes décrivent la façon dont l’exemple implémente certaines tâches courantes de l’authentification.

### <a name="use-an-oauth-prompt-to-sign-the-user-in-and-get-a-token"></a>Utiliser une invite OAuth pour connecter l’utilisateur et obtenir un jeton

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

![Architecture de bot](media/how-to-auth/architecture.png)

<!-- The two authentication samples have nearly identical architecture. Using 18.bot-authentication for the sample code. -->

**Dialogs\MainDialog.cs**

Ajoutez une invite OAuth à **MainDialog** dans son constructeur. Ici, la valeur du nom de connexion a été récupérée dans le fichier **appsettings.json**.

[!code-csharp[Add OAuthPrompt](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=23-31)]

Dans une étape de dialogue, utilisez `BeginDialogAsync` pour démarrer l’invite OAuth qui demande à l’utilisateur de se connecter.

- Si l’utilisateur est déjà connecté, un événement de réponse de jeton est généré, sans que l’utilisateur soit sollicité.
- Sinon, l’utilisateur est invité à se connecter. Azure Bot Service envoie l’événement de réponse de jeton après la tentative de connexion de l’utilisateur.

[!code-csharp[Use the OAuthPrompt](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=49)]

À l’étape de dialogue suivante, vérifiez la présence d’un jeton dans le résultat de l’étape précédente. S’il n’est pas null, l’utilisateur est connecté.

[!code-csharp[Get the OAuthPrompt result](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/MainDialog.cs?range=54-56)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

![Architecture du bot](media/how-to-auth/architecture-js.png)

**dialogs/mainDialog.js**

Ajoutez une invite OAuth à **MainDialog** dans son constructeur. Ici, la valeur du nom de connexion a été récupérée dans le fichier **.env**.

[!code-javascript[Add OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=24-29)]

Dans une étape de dialogue, utilisez `beginDialog` pour démarrer l’invite OAuth qui demande à l’utilisateur de se connecter.

- Si l’utilisateur est déjà connecté, un événement de réponse de jeton est généré, sans que l’utilisateur soit sollicité.
- Sinon, l’utilisateur est invité à se connecter. Azure Bot Service envoie l’événement de réponse de jeton après la tentative de connexion de l’utilisateur.

[!code-javascript[Use OAuthPrompt](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=57)]

À l’étape de dialogue suivante, vérifiez la présence d’un jeton dans le résultat de l’étape précédente. S’il n’est pas null, l’utilisateur est connecté.

[!code-javascript[Get OAuthPrompt result](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/mainDialog.js?range=62-63)]

---

### <a name="wait-for-a-tokenresponseevent"></a>Attendre un TokenResponseEvent

Lorsque vous démarrez une invite OAuth, un événement de réponse de jeton est attendu à partir duquel elle va récupérer le jeton de l’utilisateur.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Bots\AuthBot.cs**

**AuthBot** dérive de `ActivityHandler` et gère explicitement les activités d’événement de réponse de jeton. Ici, nous poursuivons le dialogue actif, ce qui permet à l’invite OAuth de traiter l’événement et de récupérer le jeton.

[!code-csharp[OnTokenResponseEventAsync](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Bots/AuthBot.cs?range=32-38)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**bots/authBot.js**

**AuthBot** dérive de `ActivityHandler` et gère explicitement les activités d’événement de réponse de jeton. Ici, nous poursuivons le dialogue actif, ce qui permet à l’invite OAuth de traiter l’événement et de récupérer le jeton.

[!code-javascript[onTokenResponseEvent](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/bots/authBot.js?range=29-31)]

---

### <a name="log-the-user-out"></a>Déconnecter l’utilisateur

Donner la possibilité aux utilisateurs de se déconnecter explicitement plutôt que de compter sur l’expiration de la connexion constitue une bonne pratique.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

**Dialogs\LogoutDialog.cs**

[!code-csharp[Allow logout](~/../botbuilder-samples/samples/csharp_dotnetcore/18.bot-authentication/Dialogs/LogoutDialog.cs?range=44-61&highlight=11)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**dialogs/logoutDialog.js**

[!code-javascript[Allow logout](~/../botbuilder-samples/samples/javascript_nodejs/18.bot-authentication/dialogs/logoutDialog.js?range=31-42&highlight=7)]

---

### <a name="adding-teams-authentication"></a>Ajout de Teams Authentication

Les équipes se comportent différemment des autres canaux en ce qui concerne OAuth et nécessitent quelques modifications pour implémenter correctement l’authentification. Nous allons ajouter du code à partir de l’exemple de bot Teams Authentication ([C#][cs-teams-auth-sample]/[JavaScript][js-teams-auth-sample]).
 
L’une des différences entre les autres canaux et Teams est que Teams envoie une activité d’appel (*invoke*) et non une activité d’événement (*event*). 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
**Bots/TeamsBot.cs** [!code-csharp[Invoke Activity](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/TeamsBot.cs?range=34-42&highlight=1)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
**bots/teamsBot.js** [!code-javascript[Invoke Activity](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/teamsBot.js?range=27-32&highlight=3)]

---

Si vous utilisez une *invite OAuth*, cette activité d’appel doit être transférée à la boîte de dialogue. Nous allons le faire dans `TeamsActivityHandler`. Ajoutez le code suivant à votre fichier de boîte de dialogue principal. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
**Bots/DialogBot.cs** [!code-csharp[Dialogs Handler](~/../botbuilder-samples/samples/csharp_dotnetcore/46.teams-auth/Bots/DialogBot.cs?range=18)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
**Bots/dialogBot.js** [!code-javascript[Dialogs Handler](~/../botbuilder-samples/samples/javascript_nodejs/46.teams-auth/bots/dialogBot.js?range=4-6)]

---
Enfin, veillez à ajouter un fichier `TeamsActivityHandler` approprié (`TeamsActivityHandler.cs` pour les bots C# et `teamsActivityHandler.js` pour les bots JavaScript) au niveau le plus élevé du dossier de votre bot.

`TeamsActivityHandler` envoie également des activités de *réaction à un message*. Une activité de réaction à un message fait référence à l’activité d’origine à l’aide du champ *replyToId* Cette activité doit également être visible dans le [Flux d’activités][teams-activity-feed] de Microsoft Teams.

> [!NOTE]
> Vous devez créer un manifeste et inclure `token.botframework.com` dans la section `validDomains` ; sinon, le bouton **Se connecter** d’OAuthCard n’ouvre pas la fenêtre d’authentification. Utilisez [App Studio](https://docs.microsoft.com/en-us/microsoftteams/platform/get-started/get-started-app-studio) pour générer votre manifeste.

### <a name="further-reading"></a>Pour aller plus loin

- La rubrique [Ressources supplémentaires pour Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help) inclut des liens pour obtenir une aide supplémentaire.
- Le dépôt [SDK Bot Framework](https://github.com/microsoft/botbuilder) contient des informations supplémentaires sur les dépôts, des exemples, des outils et des spécifications associées au SDK Bot Builder.

<!-- Footnote-style links -->

[Azure portal]: https://ms.portal.azure.com
[azure-aad-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview
[aad-registration-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview

[concept-basics]: bot-builder-basics.md
[concept-state]: bot-builder-concept-state.md
[concept-dialogs]: bot-builder-concept-dialog.md

[simple-dialog]: bot-builder-dialog-manage-conversation-flow.md
[dialog-prompts]: bot-builder-prompts.md
[component-dialogs]: bot-builder-compositcontrol.md

[cs-auth-sample]: https://aka.ms/v4cs-bot-auth-sample
[js-auth-sample]: https://aka.ms/v4js-bot-auth-sample
[cs-msgraph-sample]: https://aka.ms/v4cs-auth-msgraph-sample
[js-msgraph-sample]: https://aka.ms/v4js-auth-msgraph-sample
[cs-teams-auth-sample]:https://aka.ms/cs-teams-auth-sample
[js-teams-auth-sample]:https://aka.ms/js-teams-auth-sample
[teams-activity-feed]:[https://aka.ms/teams-activity-feed
