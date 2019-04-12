---
title: Ajouter l’authentification à votre bot par le biais d’Azure Bot Service | Microsoft Docs
description: Découvrez comment utiliser les fonctionnalités d’authentification d’Azure Bot Service pour ajouter l’authentification unique à votre bot.
author: JonathanFingold
ms.author: v-jofing
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: abs
ms.date: 04/09/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: 27c97d257261a6f3b9d867503aee40382b685e20
ms.sourcegitcommit: 562dd44e38abacaa31427da5675da556a970cf11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59477102"
---
# <a name="add-authentication-to-your-bot-via-azure-bot-service"></a>Ajouter l’authentification à votre bot par le biais d’Azure Bot Service

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

Azure Bot Service et le SDK v4 incluent de nouvelles fonctionnalités d’authentification de bot, facilitant le développement d’un bot qui authentifie les utilisateurs auprès de divers fournisseurs d’identité tels qu’Azure AD (Azure Active Directory), GitHub ou Uber, entre autres. Ces fonctionnalités peuvent améliorer l’expérience utilisateur en supprimant la _vérification du code magique_ pour certains clients.

Auparavant, votre bot devait inclure des contrôleurs OAuth et des liens de connexion, stocker les ID et secrets clients cibles et effectuer la gestion des jetons d’utilisateur. Le bot demandait à l’utilisateur de se connecter à un site web, ce qui générait alors un _code magique_ que l’utilisateur pouvait utiliser pour vérifier son identité.

Désormais, les développeurs de bots n’ont plus besoin d’héberger des contrôleurs OAuth ou de gérer le cycle de vie des jetons, ces tâches pouvant maintenant être effectuées par Azure Bot Service.

Cette API offre les fonctionnalités suivantes :

- Des améliorations des canaux pour prendre en charge de nouvelles fonctionnalités d’authentification, telles que les nouvelles bibliothèques WebChat et DirectLineJS visant à éliminer la nécessité de la vérification du code magique à 6 chiffres.
- Des améliorations du portail Azure pour ajouter, supprimer et configurer les paramètres de connexion à divers fournisseurs d’identité OAuth.
- La prise en charge d’une variété de fournisseurs d’identité prêts à l’emploi, dont Azure AD (points de terminaison v1 et v2) et GitHub.
- Des mises à jour des SDK Bot Framework C# et Node.js permettant de récupérer des jetons, de créer des cartes OAuthCards et de gérer les événements TokenResponse.
- Des exemples montrant comment créer un bot qui s’authentifie auprès d’Azure AD.

Vous pouvez vous appuyer sur les étapes décrites dans cet article pour ajouter des fonctionnalités à un bot existant. Ces exemples de bots illustrent les nouvelles fonctionnalités d’authentification.

> [!NOTE]
> Les fonctionnalités d’authentification fonctionnent également avec BotBuilder v3. Toutefois, cet article traite uniquement l’exemple de code v4.

### <a name="about-this-sample"></a>À propos de cet exemple

Vous devez créer une ressource de bot Azure, puis vous devez créer une application Azure AD (v1 ou v2) pour autoriser votre bot à accéder à Office 365. La ressource de bot inscrit les informations d’identification de votre bot. Vous avez besoin de ces dernières pour tester les fonctionnalités d’authentification, même lors de l’exécution de votre code de bot localement.

> [!IMPORTANT]
> Chaque fois que vous inscrivez un bot dans Azure, une application Azure AD lui est affectée. Toutefois, cette application sécurise l’accès du canal vers le bot.
Vous avez besoin d’une application AAD supplémentaire pour chaque application que le bot doit être en mesure d’authentifier au nom de l’utilisateur.

Cet article décrit un exemple de bot qui se connecte à Microsoft Graph à l’aide d’un jeton Azure AD v1 ou v2. Il explique également comment créer et inscrire l’application Azure AD associée. Dans le cadre de ce processus, vous allez utiliser du code issu du dépôt GitHub [Microsoft/BotBuilder-Samples](https://github.com/Microsoft/BotBuilder-Samples). Cet article aborde ces processus.

- **Créer votre ressource de bot**
- **Créer une application Azure AD**
- **Inscrire votre application Azure AD auprès de votre bot**
- **Préparer l’exemple de code du bot**

Une fois que vous aurez terminé, vous aurez un bot s’exécutant localement et pouvant répondre à quelques tâches simples par rapport à une application Azure AD, telles que la vérification et l’envoi d’un e-mail ou l’affichage de vos coordonnées et de celles de votre responsable. Pour ce faire, votre bot utilise un jeton à partir d’une application Azure AD par rapport à la bibliothèque Microsoft.Graph. Vous n’avez pas besoin de publier votre bot pour tester les fonctionnalités de connexion OAuth. Par contre, votre bot aura besoin d’un ID d’application Azure et d’un mot de passe valides.

Ces fonctionnalités d’authentification fonctionnent également avec d’autres types de bots. Toutefois, cet article utilise un bot d’inscription uniquement.

### <a name="web-chat-and-direct-line-considerations"></a>Considérations relatives à Web Chat et à Direct Line

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

Plusieurs problèmes de sécurité importants sont à prendre en compte quand vous utilisez l’authentification Azure Bot Service avec Web Chat.

1. Empêchez l’emprunt d’identité, qui présente le risque qu’un attaquant laisse croire au bot qu’il est quelqu’un d’autre. Dans Web Chat, un attaquant peut emprunter l’identité d’un utilisateur, en modifiant l’ID utilisateur de son instance Web Chat.

    Pour éviter cela, rendez l’ID utilisateur impossible à deviner. Quand vous activez les options d’authentification améliorées dans le canal Direct Line, Azure Bot Service peut détecter et rejeter tout changement d’ID utilisateur. L’ID utilisateur sur les messages de Direct Line à votre bot sera toujours identique à celui avec lequel vous avez initialisé Web Chat. Notez que cette fonctionnalité nécessite que l’ID utilisateur commence par `dl_`.

1. Vérifiez que le bon utilisateur est connecté. L’utilisateur a deux identités : son identité dans un canal et son identité avec le fournisseur d’identité. Dans Web Chat, Azure Bot Service peut garantir que le processus de connexion est effectuée dans la même session de navigateur que Web Chat lui-même.

    Pour activer cette protection, démarrez Web Chat avec un jeton Direct Line qui contient une liste de domaines approuvés pouvant héberger le client Web Chat du bot. Ensuite, spécifiez de façon statique la liste (d’origine) des domaines approuvés dans la page de configuration de Direct Line.

Utilisez le point de terminaison REST `/v3/directline/tokens/generate` de Direct Line pour générer un jeton pour la conversation, puis spécifiez l’ID utilisateur dans la charge utile des demandes. Pour obtenir un exemple de code, consultez le billet de blog sur les [fonctionnalités d’authentification Direct Line améliorées](https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/).

<!-- The eventual article about this should talk about the tokens/generate endpoint and its parameters: user, trustedOrigins, and [maybe] eTag.
Sample payload
{
  "user": {
    "id": "string",
    "name": "string",
    "aadObjectId": "string",
    "role": "string"
  },
  "trustedOrigins": [
    "string"
  ],
  "eTag": "string"
}
 -->

## <a name="prerequisites"></a>Prérequis

- Connaissance des [concepts de base des bots][concept-basics] et de la [gestion de l’état][concept-state].
- Connaissances du développement Azure et OAuth 2.0.
- Visual Studio version 2017 ou ultérieure, Node.js, npm et git.
- Un de ces exemples.

| Exemple | Version de Bot Builder | Illustre le |
|:---|:---:|:---|
| **Authentification de bot** dans [**CSharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] | v4 | Prise en charge d’OAuthCard |
| **Authentification de bot MSGraph** dans [**CSharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] | v4 |  Prise en charge de l’API Microsoft Graph avec OAuth 2 |

## <a name="create-your-bot-resource-on-azure"></a>Créer votre ressource de bot sur Azure

Créez une inscription **Bot Channels Registration** à l’aide du [portail Azure](https://portal.azure.com/).

## <a name="create-and-register-an-azure-ad-application"></a>Créer et inscrire une application Azure AD

Vous avez besoin d’une application Azure AD que votre bot peut utiliser pour se connecter à l’API Microsoft Graph.

Pour ce bot, vous pouvez utiliser des points de terminaison Azure AD v1 ou v2.
Pour plus d’informations sur les différences entre les points de terminaison v1 et v2, consultez la [comparaison v1-v2](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) et la [vue d’ensemble du point de terminaison Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-appmodel-v2-overview).

### <a name="create-your-azure-ad-application"></a>Créer votre application Azure AD

Suivez ces étapes pour créer une application Azure AD. Vous pouvez utiliser les points de terminaison v1 ou v2 avec l’application que vous créez.

> [!TIP]
> Vous devez créer et inscrire l’application Azure AD dans un locataire sur lequel vous disposez de droits d’administrateur.

1. Ouvrez le panneau [Azure Active Directory][azure-aad-blade] dans le portail Azure.
    Si vous n’êtes pas dans le locataire approprié, cliquez sur **Changer de répertoire** pour basculer vers le bon locataire. (Pour obtenir des instructions sur la création d’un locataire, consultez [Accéder au portail et créer un locataire](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).)
1. Ouvrez le panneau **Inscriptions d’applications**.
1. Dans le panneau **Inscriptions des applications**, cliquez sur **Nouvelle inscription d’application**.
1. Renseignez les champs obligatoires et créez l’inscription d’application.

   1. Donnez un nom à votre application.
   1. Définissez **Type d’application** sur **Application web/API**.
   1. Définissez **l’URL de connexion** sur `https://token.botframework.com/.auth/web/redirect`.
   1. Cliquez sur **Créer**.

      - Une fois l’application créée, elle apparaît dans un volet **Application inscrite**.
      - Récupérez la valeur **ID de l’application**. Vous utiliserez cette valeur ultérieurement en tant qu’_ID client_ quand vous inscrirez votre application Azure AD auprès de votre bot.

1. Cliquez sur **Paramètres** pour configurer votre application.
1. Cliquez sur **Clés** pour ouvrir le panneau **Clés**.

   1. Sous **Mots de passe**, créez une clé `BotLogin`.
   1. Définissez sa **Durée** sur **N’expire jamais**.
   1. Cliquez sur **Enregistrer** et récupérez la valeur de la clé. Vous utiliserez cette valeur ultérieurement en tant que _Secret du client_ quand vous inscrirez votre application Azure AD auprès de votre bot.
   1. Fermez le panneau **Clés**.

1. Cliquez sur **Autorisations requises** pour ouvrir le panneau **Autorisations requises**.

   1. Cliquez sur **Add**.
   1. Cliquez sur **Sélectionner une API**, puis sélectionnez **Microsoft Graph** et cliquez sur **Sélectionner**.
   1. Cliquez sur **Sélectionner les autorisations**. Choisissez les autorisations déléguées que votre application utilisera.

      > [!NOTE]
      > Pour votre bot, évitez les autorisations qui sont marquées comme **Exige un administrateur**, car elles nécessitent la connexion d’un utilisateur et d’un administrateur de locataires.

      Sélectionnez les autorisations déléguées pour Microsoft Graph suivantes :
      - Accéder en lecture aux profils de base de tous les utilisateurs
      - Accéder en lecture aux e-mails utilisateur
      - Envoi de messages en tant qu’utilisateur
      - Connecter et lire le profil utilisateur
      - Afficher le profil de base des utilisateurs
      - Afficher l’adresse e-mail des utilisateurs

   1. Cliquez sur **Sélectionner**, puis sur **Terminé**.
   1. Fermez le panneau **Autorisations requises**.

Vous disposez maintenant d’une application Azure AD v1 configurée.

### <a name="register-your-azure-ad-application-with-your-bot"></a>Inscrire votre application Azure AD auprès de votre bot

L’étape suivante consiste à inscrire auprès de votre bot l’application Azure AD que vous avez créé.

# [<a name="azure-ad-v1"></a>Azure AD v1](#tab/aadv1)

1. Accédez à la page de ressources de votre bot sur le [portail Azure](http://portal.azure.com/).
1. Cliquez sur **Settings**.
1. Sous **Paramètres de connexion OAuth** près du bas de la page, cliquez sur **Ajouter un paramètre**.
1. Remplissez le formulaire comme suit :

    1. Pour **Nom**, entrez un nom pour votre connexion. Vous utiliserez ce nom dans le code de votre bot.
    1. Sous **Fournisseur de services**, sélectionnez **Azure Active Directory**. Une fois que vous avez sélectionné cette option, les champs propres à Azure AD sont affichés.
    1. Pour **Id client**, entrez l’ID d’application que vous avez récupéré pour votre application Azure AD v1.
    1. Pour **Secret du client**, entrez la clé que vous avez récupérée pour la clé `BotLogin` de votre application.
    1. Pour **Type d’autorisation**, entrez `authorization_code`.
    1. Pour **URL de connexion**, entrez `https://login.microsoftonline.com`.
    1. Pour **ID de locataire**, entrez l’ID de locataire pour Azure Active Directory, par exemple `microsoft.com` ou `common`.

       Il s’agit du locataire associé aux utilisateurs qui peuvent être authentifiés. Pour autoriser tout le monde à s’authentifier par le biais du bot, utilisez le locataire `common`.

    1. Pour **URL de la ressource**, entrez `https://graph.microsoft.com/`.
    1. Laissez **Étendues** vide.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Ces valeurs permettent à votre application d’accéder aux données Office 365 via l’API Microsoft Graph.

# [<a name="azure-ad-v2"></a>Azure AD v2](#tab/aadv2)

1. Accédez à la page de l’inscription Bot Channels Registration de votre bot dans le [Portail Azure](http://portal.azure.com/).
1. Cliquez sur **Settings**.
1. Sous **Paramètres de connexion OAuth** près du bas de la page, cliquez sur **Ajouter un paramètre**.
1. Remplissez le formulaire comme suit :

    1. Pour **Nom**, entrez un nom pour votre connexion. Vous l’utiliserez dans le code de votre bot.
    1. Sous **Fournisseur de services**, sélectionnez **Azure Active Directory v2**. Une fois que vous avez sélectionné cette option, les champs propres à Azure AD sont affichés.
    1. Pour **Id client**, entrez l’ID d’application Azure AD v2 que vous avez récupéré au moment de l’inscription de l’application.
    1. Pour **Secret du client**, entrez le mot de passe d’application Azure AD v2 que vous avez récupéré au moment de l’inscription de l’application.
    1. Pour **ID de locataire**, entrez l’ID de locataire pour Azure Active Directory, par exemple `microsoft.com` ou `common`.

        Il s’agit du locataire associé aux utilisateurs qui peuvent être authentifiés. Pour autoriser tout le monde à s’authentifier par le biais du bot, utilisez le locataire `common`.

    1. Pour **Étendues**, entrez les noms des autorisations que vous avez choisies au moment de l’inscription de l’application : `Mail.Read Mail.Send openid profile User.Read User.ReadBasic.All`.

        > [!NOTE]
        > Pour Azure AD v2, **Étendues** prend une liste de valeurs séparées par des espaces et respectant la casse.

1. Cliquez sur **Enregistrer**.

> [!NOTE]
> Ces valeurs permettent à votre application d’accéder aux données Office 365 via l’API Microsoft Graph.

---

### <a name="test-your-connection"></a>Tester votre connexion

1. Cliquez sur l’entrée de connexion pour ouvrir la connexion que vous venez de créer.
1. En haut du volet **Paramètre de connexion du fournisseur de service**, cliquez sur **Tester la connexion**.
1. La première fois, cette action doit ouvrir un nouvel onglet de navigateur répertoriant les autorisations que votre application demande et vous invite à accepter.
1. Cliquez sur **Accepter**.
1. Cette action doit vous rediriger vers une page indiquant que la **connexion de test à \<votre_nom_de_connexion> a réussi**.

Vous pouvez maintenant utiliser ce nom de connexion dans le code de votre bot pour récupérer des jetons utilisateur.

## <a name="prepare-the-bot-sample-code"></a>Préparer l’exemple de code du bot

Selon l’exemple que vous avez choisi, vous allez travailler en C# ou en Node.

| Exemple | Version de Bot Builder | Illustre le |
|:---|:---:|:---|
| **Authentification de bot** dans [**CSharp**][cs-auth-sample] ou [**JavaScript**][js-auth-sample] | v4 | Prise en charge d’OAuthCard |
| **Authentification de bot MSGraph** dans [**CSharp**][cs-msgraph-sample] ou [**JavaScript**][js-msgraph-sample] | v4 |  Prise en charge de l’API Microsoft Graph avec OAuth 2 |

1. Cliquez sur l’un des liens d’exemple ci-dessus et clonez le référentiel github.
1. Suivez les instructions de la page readme de GitHub pour savoir comment exécuter ce bot particulier (C# ou Node).
1. Si vous utilisez l’exemple d’authentification de bot en C# :

    1. Définissez la variable `ConnectionName` du fichier `AuthenticationBot.cs` sur la valeur que vous avez utilisée quand vous avez configuré le paramètre de connexion OAuth 2.0 de votre bot.
    1. Définissez la valeur `appId` du fichier `BotConfiguration.bot` sur l’ID d’application de votre bot.
    1. Définissez la valeur `appPassword` du fichier `BotConfiguration.bot` sur le secret de votre bot.

1. Si vous utilisez l’exemple d’authentification de bot en Node/JS :

    1. Définissez la variable `CONNECTION_NAME` du fichier `bot.js` sur la valeur que vous avez utilisée quand vous avez configuré le paramètre de connexion OAuth 2.0 de votre bot.
    1. Définissez la valeur `appId` du fichier `bot-authentication.bot` sur l’ID d’application de votre bot.
    1. Définissez la valeur `appPassword` du fichier `bot-authentication.bot` sur le secret de votre bot.

    > [!IMPORTANT]
    > Selon les caractères de votre secret, vous pouvez être amené à placer le mot de passe dans une séquence d’échappement XML. Par exemple, les esperluettes (&) doivent être encodées sous la forme `&amp;`.

    ```json
    {
        "name": "BotAuthentication",
        "secretKey": "",
        "services": [
            {
            "appId": "",
            "id": "http://localhost:3978/api/messages",
            "type": "endpoint",
            "appPassword": "",
            "endpoint": "http://localhost:3978/api/messages",
            "name": "BotAuthentication"
            }
        ]
    }
    ```

    Si vous ne savez pas comment obtenir les valeurs de **l’ID d’application Microsoft** et du **mot de passe d’application Microsoft**, effectuez une recherche dans les **paramètres d’application** du service d’application Azure qui a été provisionné pour votre bot sur le portail Azure.

    > [!NOTE]
    > Maintenant, vous pouvez publier ce code de bot sur votre abonnement Azure (cliquez avec le bouton droit sur le projet et choisissez **Publier**), mais cela n’est pas nécessaire pour ce tutoriel. Vous devriez définir une configuration de publication qui utilise l’application et le plan d’hébergement que vous avez utilisés quand vous avez configuré le bot dans le portail Azure.

## <a name="use-the-emulator-to-test-your-bot"></a>Utiliser l’émulateur pour tester votre bot

Vous devez installer [l’émulateur de bot](https://github.com/Microsoft/BotFramework-Emulator) pour tester votre bot localement. Vous pouvez utiliser l’émulateur v3 ou v4.

1. Démarrez votre bot (avec ou sans débogage).
1. Notez le numéro de port localhost pour la page. Vous aurez besoin de ces informations pour interagir avec votre bot.
1. Démarrez l’émulateur.
1. Connectez-vous à votre bot. Vérifiez que la configuration du bot utilise l’**ID d’application Microsoft** et le **Mot de passe d’application Microsoft** en cas de recours à l’authentification.
1. Dans les paramètres de l’émulateur, vérifiez que la case **Use a sign-in verification code for OAuthCards** (Utiliser un code de vérification de la connexion pour cartes OAuthCards) est cochée et que **ngrok** est activé pour permettre à Azure Bot Service de retourner le jeton à l’émulateur quand il devient disponible.

   Si vous n’avez pas déjà configuré la connexion, indiquez l’adresse, ainsi que l’ID d’application Microsoft et le mot de passe de votre bot. Ajoutez `/api/messages` à l’URL du bot. Votre URL ressemble à ceci : `http://localhost:portNumber/api/messages`

1. Tapez `help` pour afficher la liste des commandes disponibles pour le bot, puis testez les fonctionnalités d’authentification.
1. Une fois connecté, vous n’avez pas besoin de refournir vos informations d’identification jusqu’à ce que vous vous déconnectiez.
1. Pour vous déconnecter et annuler votre authentification, tapez `signout`.

<!--To restart completely from scratch you also need to:
1. Navigate to the **AppData** folder for your account.
1. Go to the **Roaming/botframework-emulator** subfolder.
1. Delete the **Cookies** and **Coolies-journal** files.
-->

> [!NOTE]
> L’authentification de bot nécessite l’utilisation du service Bot Connector. Le service accède aux informations d’inscription de canaux de bot pour votre bot, raison pour laquelle vous devez définir le point de terminaison de messagerie de votre bot sur le portail. L’authentification requiert également l’utilisation du protocole HTTPS, raison pour laquelle vous avez dû créer une adresse de transfert HTTPS pour votre bot s’exécutant localement.

<!--The following is necessary for WebChat:
1. Use the **ngrok** command-line tool to get a forwarding HTTPS address for your bot.
   - For information on how to do this, see [Debug any Channel locally using ngrok](https://blog.botframework.com/2017/10/19/debug-channel-locally-using-ngrok/).
   - Any time you exit **ngrok**, you will need to redo this and the following step before starting the Emulator.
1. On the Azure Portal, go to the **Settings** blade for your bot.
   1. In the **Configuration** section, change the **Messaging endpoint** to the HTTPS forwarding address generated by **ngrok**.
   1. Click **Save** to save your change.
-->

## <a name="notes-on-the-token-retrieval-flow"></a>Remarques sur le flux de la récupération des jetons

Quand un utilisateur demande au bot de faire quelque chose qui nécessite sa connexion au bot, celui-ci peut utiliser `OAuthPrompt` pour lancer la récupération d’un jeton pour une connexion donnée. L’élément `OAuthPrompt` crée un flux de récupération de jeton qui comprend les étapes suivantes :

1. Vérifier si Azure Bot Service possède déjà un jeton pour l’utilisateur et la connexion actuels. S’il existe un jeton, il est retourné.
1. Si Azure Bot Service ne dispose pas de jeton mis en cache, un élément `OAuthCard` est créé. Il s’agit d’un bouton de connexion sur lequel l’utilisateur peut cliquer.
1. Une fois que l’utilisateur clique sur le bouton de connexion `OAuthCard`, Azure Bot Service envoie au bot le jeton de l’utilisateur directement ou il présente à l’utilisateur un code d’authentification à 6 chiffres dans la fenêtre de conversation.
1. Si un code d’authentification est présenté à l’utilisateur, le bot l’échange contre le jeton de l’utilisateur.

Les extraits de code suivants sont tirés de l’élément `OAuthPrompt`, et montrent le fonctionnement de ces étapes dans l’invite.

### <a name="check-for-a-cached-token"></a>Rechercher un jeton mis en cache

Dans ce code, le bot commence par effectuer une vérification rapide pour déterminer si Azure Bot Service a déjà un jeton pour l’utilisateur (identifié par l’expéditeur de l’activité actuelle) et pour le nom de connexion (ConnectionName) donné (nom de connexion utilisé dans la configuration). Azure Bot Service a déjà un jeton mis en cache ou n’en a pas. L’appel à GetUserTokenAsync effectue cette vérification rapide. Si Azure Bot Service dispose d’un jeton et le retourne, le jeton peut être utilisé immédiatement. Si Azure Bot Service n’a pas de jeton, cette méthode retourne Null. Dans ce cas, le bot peut envoyer une carte OAuthCard personnalisée pour que l’utilisateur se connecte.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
// First ask Bot Service if it already has a token for this user
var token = await adapter.GetUserTokenAsync(turnContext, connectionName, null, cancellationToken).ConfigureAwait(false);
if (token != null)
{
    // use the token to do exciting things!
}
else
{
    // If Bot Service does not have a token, send an OAuth card to sign in
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
public async getUserToken(context: TurnContext, code?: string): Promise<TokenResponse|undefined> {
    // Get the token and call validator
    const adapter: any = context.adapter as any; // cast to BotFrameworkAdapter
    return await adapter.getUserToken(context, this.settings.connectionName, code);
}
```

---

### <a name="send-an-oauthcard-to-the-user"></a>Envoyer une carte OAuthCard à l’utilisateur

Vous pouvez personnaliser la carte OAuthCard avec les texte et texte de bouton que vous souhaitez. Les points importants sont les suivants :

- Définissez `ContentType` sur `OAuthCard.ContentType`.
- Définissez la propriété `ConnectionName` sur le nom de la connexion que vous souhaitez utiliser.
- Incluez un bouton avec un `CardAction` ayant pour `Type` `ActionTypes.Signin` ; notez que vous n’avez pas besoin de spécifier de valeur pour le lien de connexion.

À la fin de cet appel, le bot doit « attendre le retour du jeton ». Cette attente s’effectue sur le flux d’activité principal, car l’utilisateur pourrait être amené à faire beaucoup de choses pour se connecter.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
private async Task SendOAuthCardAsync(ITurnContext turnContext, IMessageActivity message, CancellationToken cancellationToken = default(CancellationToken))
{
    if (message.Attachments == null)
    {
        message.Attachments = new List<Attachment>();
    }

    message.Attachments.Add(new Attachment
    {
        ContentType = OAuthCard.ContentType,
        Content = new OAuthCard
        {
            Text = "Please sign in",
            ConnectionName = connectionName,
            Buttons = new[]
            {
                new CardAction
                {
                    Title = "Sign In",
                    Text = "Sign In",
                    Type = ActionTypes.Signin,
                },
            },
        },
    });

    await turnContext.SendActivityAsync(message, cancellationToken).ConfigureAwait(false);
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
private async sendOAuthCardAsync(context: TurnContext, prompt?: string|Partial<Activity>): Promise<void> {
    // Initialize outgoing message
    const msg: Partial<Activity> =
        typeof prompt === 'object' ? {...prompt} : MessageFactory.text(prompt, undefined, InputHints.ExpectingInput);
    if (!Array.isArray(msg.attachments)) { msg.attachments = []; }

    const cards: Attachment[] = msg.attachments.filter((a: Attachment) => a.contentType === CardFactory.contentTypes.oauthCard);
    if (cards.length === 0) {
        // Append oauth card
        msg.attachments.push(CardFactory.oauthCard(
            this.settings.connectionName,
            this.settings.title,
            this.settings.text
        ));
    }

    // Send prompt
    await context.sendActivity(msg);
}
```

---

### <a name="wait-for-a-tokenresponseevent"></a>Attendre un TokenResponseEvent

Dans ce code, le bot attend un `TokenResponseEvent` (nous abordons plus bas son acheminement vers la pile de dialogue). La méthode `WaitForToken` détermine d’abord si cet événement a été envoyé. S’il a été envoyé, il peut être utilisé par le bot. Sinon, la méthode `RecognizeTokenAsync` prend le texte qui a été envoyé au bot et le transmet à `GetUserTokenAsync`. En effet, certains clients (tels qu’un webchat) n’ont pas besoin de code de vérification du code magique, ce qui leur permet d’envoyer directement le jeton dans le `TokenResponseEvent`. D’autres clients nécessitent toujours le code magique (tels que Facebook ou Slack). Azure Bot Service présente à ces clients un code magique à six chiffres et demande à l’utilisateur de le taper dans la fenêtre de conversation. Bien que cela ne soit pas l’idéal, il s’agit du comportement « de repli » ; ainsi, si `RecognizeTokenAsync` reçoit un code, le bot peut envoyer ce code à Azure Bot Service et obtenir un jeton. Si cet appel échoue également, vous pouvez décider de signaler une erreur ou faire autre chose. Cependant, dans la plupart des cas, le bot dispose désormais d’un jeton d’utilisateur.

Si vous regardez le code du bot de chaque exemple, vous verrez que les activités `Event` et `Invoke` sont également acheminées vers la pile de dialogue.

# [<a name="c"></a>C#](#tab/csharp)

```csharp
// This can be called when the bot receives an Activity after sending an OAuthCard
private async Task<TokenResponse> RecognizeTokenAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
{
    if (IsTokenResponseEvent(turnContext))
    {
        // The bot received the token directly
        var tokenResponseObject = turnContext.Activity.Value as JObject;
        var token = tokenResponseObject?.ToObject<TokenResponse>();
        return token;
    }
    else if (IsTeamsVerificationInvoke(turnContext))
    {
        var magicCodeObject = turnContext.Activity.Value as JObject;
        var magicCode = magicCodeObject.GetValue("state")?.ToString();

        var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, magicCode, cancellationToken).ConfigureAwait(false);
        return token;
    }
    else if (turnContext.Activity.Type == ActivityTypes.Message)
    {
        // make sure it's a 6-digit code
        var matched = _magicCodeRegex.Match(turnContext.Activity.Text);
        if (matched.Success)
        {
            var token = await adapter.GetUserTokenAsync(turnContext, _settings.ConnectionName, matched.Value, cancellationToken).ConfigureAwait(false);
            return token;
        }
    }

    return null;
}

private bool IsTokenResponseEvent(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Event && activity.Name == "tokens/response";
}

private bool IsTeamsVerificationInvoke(ITurnContext turnContext)
{
    var activity = turnContext.Activity;
    return activity.Type == ActivityTypes.Invoke && activity.Name == "signin/verifyState";
}
```

# [<a name="javascript"></a>JavaScript](#tab/javascript)

```javascript
private async recognizeToken(context: TurnContext): Promise<PromptRecognizerResult<TokenResponse>> {
    let token: TokenResponse|undefined;
    if (this.isTokenResponseEvent(context)) {
        token = context.activity.value as TokenResponse;
    } else if (this.isTeamsVerificationInvoke(context)) {
        const code: any = context.activity.value.state;
        await context.sendActivity({ type: 'invokeResponse', value: { status: 200 }});
        token = await this.getUserToken(context, code);
    } else if (context.activity.type === ActivityTypes.Message) {
        const matched: RegExpExecArray = /(\d{6})/.exec(context.activity.text);
        if (matched && matched.length > 1) {
            token = await this.getUserToken(context, matched[1]);
        }
    }

    return token !== undefined ? { succeeded: true, value: token } : { succeeded: false };
}

private isTokenResponseEvent(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Event && activity.name === 'tokens/response';
}

private isTeamsVerificationInvoke(context: TurnContext): boolean {
    const activity: Activity = context.activity;
    return activity.type === ActivityTypes.Invoke && activity.name === 'signin/verifyState';
}
```

---

### <a name="message-controller"></a>Contrôleur de message

À l’occasion des appels suivants du bot, le jeton n’est jamais mis en cache par cet exemple de bot. En effet, le bot peut toujours demander le jeton à Azure Bot Service. Ainsi le bot n’a pas besoin d’effectuer des opérations telles que la gestion du cycle de vie des jetons ou l’actualisation du jeton, car Azure Bot Service s’en charge.

### <a name="further-reading"></a>Pour aller plus loin

- La rubrique [Ressources supplémentaires pour Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help) inclut des liens pour obtenir une aide supplémentaire.
- Le dépôt [SDK Bot Framework](https://github.com/microsoft/botbuilder) contient des informations supplémentaires sur les dépôts, des exemples, des outils et des spécifications associées au SDK Bot Builder.

<!-- Footnote-style links -->

[Azure portal]: https://ms.portal.azure.com
[azure-aad-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview
[aad-registration-blade]: https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps

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
