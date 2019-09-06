---
title: Authentification | Microsoft Docs
description: Découvrez comment authentifier des demandes d’API dans l’API Direct Line v3.0.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/22/2019
ms.openlocfilehash: 37e02a34e7b8ecc4d501ed7330b6f374548fd5a0
ms.sourcegitcommit: 0b647dc6716b0c06f04ee22ebdd7b53039c2784a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076569"
---
# <a name="authentication"></a>Authentication

Un client peut authentifier les demandes adressées à l’API Direct Line 3.0 soit à l’aide d’un **secret** que vous [obtenez dans la page de configuration du canal Direct Line](../bot-service-channel-connect-directline.md) sur le portail Bot Framework, ou à l’aide d’un **jeton** que vous obtenez au moment de l’exécution. Le secret ou le jeton doivent être spécifiés dans l’en-tête `Authorization` de chaque demande, en utilisant le format suivant : 

```http
Authorization: Bearer SECRET_OR_TOKEN
```

## <a name="secrets-and-tokens"></a>Secrets et jetons

Un **secret** Direct Line est une clé principale qui peut être utilisée pour accéder à toute conversation appartenant au robot associé. Un **secret** peut également être utilisé pour obtenir un **jeton**. Les secrets n’expirent pas. 

Un **jeton** Direct Line est une clé qui peut être utilisée pour accéder à une seule conversation. Un jeton expire, mais peut être actualisé.

Pour déterminer quand ou s’il faut utiliser la clé **secrète** ou un **jeton**, vous devez vous baser sur des considérations relatives à la sécurité.
L’exposition de la clé secrète peut être acceptable si cela se fait intentionnellement et avec précaution. Il s’agit en fait du comportement par défaut, car cela permet à Direct Line de déterminer si le client est légitime.
D’une façon générale, la sécurité est cependant un problème si vous essayez de conserver des données utilisateur.
Pour plus d’informations, consultez la section [Considérations relatives à la sécurité](#security-considerations).

Si vous créez une application de service à service, l’approche la plus simple peut consister à spécifier le **secret** dans l’en-tête `Authorization` des demandes d’API Direct Line. Si vous écrivez une application où le client s’exécute dans un navigateur web ou une application mobile, vous souhaiterez peut-être échanger votre secret contre un jeton (qui fonctionne uniquement pour une conversation et expire sauf s’il est actualisé) et spécifier le **jeton** dans l’en-tête `Authorization` des demandes d’API Direct Line. Choisissez le modèle de sécurité qui vous convient le mieux.

> [!NOTE]
> Les informations d’identification de votre client Direct Line sont différentes de celles de votre robot. Cela vous permet de réviser vos clés de manière indépendante et de partager des jetons de client sans révéler le mot de passe de votre robot. 

## <a name="get-a-direct-line-secret"></a>Obtenir un secret Direct Line

Vous pouvez [obtenir un secret Direct Line](../bot-service-channel-connect-directline.md) à partir de la page de configuration de canal Direct Line pour votre robot dans le <a href="https://dev.botframework.com/" target="_blank">portail Bot Framework</a> :

![Configuration de Direct Line](../media/direct-line-configure.png)

## <a id="generate-token"></a> Générer un jeton Direct Line

Pour générer un jeton Direct Line qui peut être utilisé pour accéder à une conversation unique, commencez par obtenir le secret Direct Line à partir de la page de configuration de canal Direct Line dans le <a href="https://dev.botframework.com/" target="_blank">portail Bot Framework</a>. Ensuite, émettez la demande suivante pour échanger votre secret Direct Line contre un jeton Direct Line :

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer SECRET
```

Dans l’en-tête `Authorization` de cette demande, remplacez **SECRET** par la valeur de votre secret Direct Line.

Les extraits de code suivants illustrent la demande et la réponse dans le cadre de la génération de jeton.

### <a name="request"></a>Requête

```http
POST https://directline.botframework.com/v3/directline/tokens/generate
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
```

La charge utile de la demande, qui contient les paramètres de jeton, est facultative mais recommandée. Lorsque vous générez un jeton qui peut être renvoyé au service Direct Line, fournissez la charge utile suivante pour renforcer la sécurité de la connexion. Lorsque vous incluez ces valeurs, Direct Line peut effectuer une validation de sécurité supplémentaire du nom et de l’ID utilisateur, ce qui empêche la falsification de ces valeurs par des clients malveillants. L’ajout de ces valeurs améliore également la capacité de Direct Line à envoyer l’activité de _mise à jour de la conversation_, ce qui lui permet de générer la mise à jour de la conversation dès que l’utilisateur rejoint la conversation. Lorsque ces informations ne sont pas fournies, l’utilisateur doit envoyer du contenu pour que Direct Line puisse envoyer la mise à jour de la conversation.

```json
{
  "user": {
    "id": "string",
    "name": "string"
  },
  "trustedOrigins": [
    "string"
  ]
}
```

| Paramètre | type | Description |
| :--- | :--- | :--- |
| `user.id` | string | facultatif. ID spécifique au canal de l’utilisateur à encoder dans le jeton. Pour un utilisateur Direct Line, il doit commencer par `dl_`. Vous pouvez créer un ID utilisateur unique pour chaque conversation et, pour une meilleure sécurité, vous devez faire en sorte que cet ID ne puisse pas être deviné. |
| `user.name` | string | facultatif. Nom convivial complet de l’utilisateur à encoder dans le jeton. |
| `trustedOrigins` | tableau de chaînes | facultatif. Liste des domaines approuvés à incorporer dans le jeton. Ce sont les domaines qui peuvent héberger le client Web Chat du bot. Elle doit correspondre à la liste figurant dans la page de configuration Direct Line de votre bot. |

### <a name="response"></a>response

Si la demande réussit, la réponse contient un `token` valide pour une conversation, et une valeur `expires_in` qui indique le nombre de secondes avant l’expiration du jeton. Pour que le jeton reste valide, vous devez [l’actualiser](#refresh-token) avant son expiration.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800
}
```

### <a name="generate-token-versus-start-conversation"></a>Générer un jeton ou démarrer une conversation

L’opération de génération de jeton (`POST /v3/directline/tokens/generate`) est similaire à l’opération de [démarrage de conversation](bot-framework-rest-direct-line-3-0-start-conversation.md) (`POST /v3/directline/conversations`) dans la mesure où les deux opérations retournent un `token` qui peut être utilisé pour accéder à une seule conversation. Toutefois, contrairement à l’opération de démarrage de conversation, l’opération de génération de jeton ne démarre pas la conversation, ne contacte pas le robot, et ne crée pas d’URL WebSocket de diffusion en continu. 

Si vous envisagez de distribuer le jeton aux clients et souhaitez qu’ils initialisent la conversation, utilisez l’opération de génération de jeton. Si vous envisagez de démarrer la conversation immédiatement, utilisez plutôt l’opération de [démarrage de conversation](bot-framework-rest-direct-line-3-0-start-conversation.md).

## <a id="refresh-token"></a> Actualiser un jeton Direct Line

Un jeton Direct Line peut être actualisé un nombre illimité de fois, tant qu’il n’a pas expiré. Il n’est pas possible d’actualiser un jeton qui a expiré. Pour actualiser un jeton Direct Line, émettez la demande suivante : 

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer TOKEN_TO_BE_REFRESHED
```

Dans l’en-tête `Authorization` de cette demande, remplacez **TOKEN_TO_BE_REFRESHED** par le jeton Direct Line à actualiser.

Les extraits de code suivants illustrent la demande et la réponse dans le cadre de l’actualisation du jeton.

### <a name="request"></a>Requête

```http
POST https://directline.botframework.com/v3/directline/tokens/refresh
Authorization: Bearer CurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### <a name="response"></a>response

Si la demande réussit, la réponse contient un nouveau `token` valide pour la même conversation que le jeton précédent, et une valeur `expires_in` qui indique le nombre de secondes avant l’expiration du jeton. Pour que le nouveau jeton reste valide, vous devez [l’actualiser](#refresh-token) avant son expiration.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xniaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0",
  "expires_in": 1800
}
```

## <a name="azure-bot-service-authentication"></a>Authentification par Azure Bot Service

Les informations présentées dans cette section sont en lien avec l’article [Ajouter l’authentification à votre bot par le biais d’Azure Bot Service](../v4sdk/bot-builder-authentication.md).

L’**authentification par Azure Bot Service** vous permet d’authentifier les utilisateurs et d’obtenir des **jetons d’accès** de divers fournisseurs d’identité, comme *Azure Active Directory*, *GitHub*, *Uber*, etc. Vous pouvez également configurer l’authentification pour un fournisseur d’identité **OAuth2** personnalisé. Tout cela vous permet d’écrire **une partie de code d’authentification** utilisable dans tous les fournisseurs d’identité et canaux pris en charge. Pour utiliser ces fonctionnalités, vous devez effectuer les étapes suivantes :

1. Configurez `settings` de manière statique sur votre bot qui contient les détails de l’inscription de votre application auprès d’un fournisseur d’identité.
2. Utilisez un `OAuthCard`, basé sur les informations sur l’application que vous avez fournies à l’étape précédente, pour connecter un utilisateur.
3. Récupérez les jetons d’accès par le biais de l’**API Azure Bot Service**.

### <a name="security-considerations"></a>Considérations relatives à la sécurité

<!-- Summarized from: https://blog.botframework.com/2018/09/25/enhanced-direct-line-authentication-features/ -->

Quand vous utilisez l’*authentification Azure Bot Service* avec [Web Chat](../bot-service-channel-connect-webchat.md), gardez à l’esprit certaines considérations importantes en matière de sécurité.

1. **Emprunt d’identité**. Ici, l’emprunt d’identité signifie qu’un attaquant laisse croire au bot qu’il est quelqu’un d’autre. Dans Web Chat, un attaquant peut emprunter l’identité d’un utilisateur, **en changeant l’ID utilisateur** de son instance Web Chat. Pour éviter cela, les développeurs de bots doivent s’efforcer de rendre l’**ID utilisateur impossible à deviner**. Si vous activez les options d’**authentification avancée**, Azure Bot Service peut mieux détecter et rejeter les changements d’ID utilisateur. L’ID utilisateur (`Activity.From.Id`) sur les messages de Direct Line envoyés à votre bot sera donc toujours identique à celui avec lequel vous avez initialisé Web Chat. Notez que cette fonctionnalité nécessite que l’ID utilisateur commence par `dl_`
1. **Identités utilisateur**. Sachez qu’un utilisateur est associé à deux identités :

    1. Son identité dans un canal.
    1. Son identité dans un fournisseur d’identité souhaité par le bot.
  
    Quand un bot demande à l’utilisateur A dans un canal de se connecter à un fournisseur d’identité P, le processus de connexion doit vérifier que l’utilisateur A est bien celui qui se connecte à P. Si un utilisateur B est autorisé à se connecter, l’utilisateur A aura accès à la ressource de l’utilisateur B par le biais du bot. Web Chat a deux mécanismes qui garantissent que l’utilisateur approprié est connecté comme décrit ci-après.

    1. Auparavant, à la fin de la connexion, l’utilisateur recevait un code à 6 chiffres généré de manière aléatoire (un code magique). L’utilisateur devait taper ce code dans la conversation à l’origine de la connexion pour terminer le processus de connexion. Ce mécanisme avait tendance à dégrader l’expérience utilisateur. De plus, il restait vulnérable aux attaques par hameçonnage. Un utilisateur malveillant pouvait inciter un autre utilisateur à se connecter et à obtenir le code magique par le biais d’un hameçonnage.

    2. En raison des problèmes rencontrés avec l’approche précédente, Azure Bot Service ne requiert plus l’utilisation du code magique. Azure Bot Service garantit que le processus de connexion peut uniquement être effectué dans la **même session de navigateur** que Web Chat lui-même. 
    Pour activer cette protection, en tant que développeur de bot, vous devez démarrer Web Chat avec un **jeton Direct Line** qui contient une **liste des domaines approuvés pouvant héberger le client Web Chat du bot**. Auparavant, vous pouviez obtenir ce jeton uniquement en passant un paramètre facultatif non documenté à l’API de jeton Direct Line. Maintenant, grâce aux options d’authentification avancée, vous pouvez spécifier de façon statique la liste (d’origine) des domaines approuvés dans la page de configuration de Direct Line.

    Consultez aussi [Ajouter l’authentification à votre bot via Azure Bot Service](../v4sdk/bot-builder-authentication.md).

### <a name="code-examples"></a>Exemples de code

Le contrôleur .NET suivant utilise les options d’authentification avancée activées et retourne un jeton et un ID utilisateur Direct Line.

```csharp
public class HomeController : Controller
{
    public async Task<ActionResult> Index()
    {
        var secret = GetSecret();

        HttpClient client = new HttpClient();

        HttpRequestMessage request = new HttpRequestMessage(
            HttpMethod.Post,
            $"https://directline.botframework.com/v3/directline/tokens/generate");

        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", secret);

        var userId = $"dl_{Guid.NewGuid()}";

        request.Content = new StringContent(
            JsonConvert.SerializeObject(
                new { User = new { Id = userId } }),
                Encoding.UTF8,
                "application/json");

        var response = await client.SendAsync(request);
        string token = String.Empty;

        if (response.IsSuccessStatusCode)
        {
            var body = await response.Content.ReadAsStringAsync();
            token = JsonConvert.DeserializeObject<DirectLineToken>(body).token;
        }

        var config = new ChatConfig()
        {
            Token = token,
            UserId = userId  
        };

        return View(config);
    }
}

public class DirectLineToken
{
    public string conversationId { get; set; }
    public string token { get; set; }
    public int expires_in { get; set; }
}
public class ChatConfig
{
    public string Token { get; set; }
    public string UserId { get; set; }
}

```

Le contrôleur JavaScript suivant utilise les options d’authentification avancée activées et retourne un jeton et un ID utilisateur Direct Line.

```javascript
var router = express.Router(); // get an instance of the express Router

// Get a directline configuration (accessed at GET /api/config)
const userId = "dl_" + createUniqueId();

router.get('/config', function(req, res) {
    const options = {
        method: 'POST',
        uri: 'https://directline.botframework.com/v3/directline/tokens/generate',
        headers: {
            'Authorization': 'Bearer ' + secret
        },
        json: {
            User: { Id: userId }
        }
    };

    request.post(options, (error, response, body) => {
        if (!error && response.statusCode < 300) {
            res.json({ 
                    token: body.token,
                    userId: userId
                });
        }
        else {
            res.status(500).send('Call to retrieve token from Direct Line failed');
        } 
    });
});

```

## <a name="additional-resources"></a>Ressources supplémentaires

- [Concepts clés](bot-framework-rest-direct-line-3-0-concepts.md)
- [Connecter un robot à Direct Line](../bot-service-channel-connect-directline.md)
- [Ajouter l’authentification à votre bot via Azure Bot Service](../bot-builder-tutorial-authentication.md)
