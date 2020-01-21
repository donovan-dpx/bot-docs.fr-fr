---
title: Connecter un bot au courrier Office 365 - Bot Service
description: Découvrez comment configurer un bot pour l’envoi et la réception d’e-mails avec Office 365.
keywords: Office 365, canaux de bot, e-mail, informations d’identification de courrier, portail azure, e-mail personnalisé
author: kamrani
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 11/15/2019
ms.openlocfilehash: 8ca2735dac838982af3ba97221e07d80155153b7
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75792090"
---
# <a name="connect-a-bot-to-office-365-email"></a>Connecter un bot au courrier Office 365

Les bots peuvent communiquer avec les utilisateurs par le biais du courrier Office 365, en plus des autres [canaux](~/bot-service-manage-channels.md). Lorsqu’un bot est configuré pour accéder à un compte de courrier, il reçoit un message à l’arrivée d’un nouvel e-mail. Le bot peut alors répondre comme indiqué par sa logique métier. Par exemple, le bot peut accuser réception d’un e-mail en renvoyant le message « Bonjour ! Merci pour votre commande ! Nous allons la traiter immédiatement. »

> [!WARNING]
> La création de « spambots », et notamment de bots envoyant du courrier indésirable ou non sollicité en nombre, constitue une violation du [Code de conduite](https://www.botframework.com/Content/Microsoft-Bot-Framework-Preview-Online-Services-Agreement.htm) de Bot Framework.

> [!NOTE]
> Si vous utilisez Microsoft Exchange Server, assurez-vous que vous avez activé [Découverte automatique](https://docs.microsoft.com/exchange/client-developer/exchange-web-services/autodiscover-for-exchange) avant de configurer le canal E-mail. 

## <a name="configure-email-credentials"></a>Configuration des informations d’identification de courrier

Vous pouvez connecter un bot au canal E-mail en entrant les informations d’identification Office 365 dans la configuration de ce canal.
L’authentification fédérée à l’aide de n’importe quel autre fournisseur qu’AAD n’est pas prise en charge.

> [!NOTE]
> Vous ne devez pas utiliser vos comptes de courrier personnels pour les bots, car chaque message envoyé à ces comptes sera transféré vers le bot. Ce dernier risque alors d’envoyer une réponse inappropriée à un expéditeur. C’est la raison pour laquelle les bots doivent uniquement utiliser des comptes de courrier O365 dédiés.

Pour ajouter le canal E-mail, ouvrez le bot dans le [Portail Azure](https://portal.azure.com/), cliquez sur le panneau **Canaux**, puis cliquez sur **E-mail**. Entrez vos informations d’identification de courrier valides, puis cliquez sur **Enregistrer**.

![Entrée des informations d’identification de courrier](~/media/bot-service-channel-connect-email/bot-service-channel-connect-email-credentials.png)

Pour l’instant, le canal E-mail ne fonctionne qu’avec Office 365. Les autres services d’hébergement de courrier ne sont actuellement pas pris en charge.

## <a name="customize-emails"></a>Personnaliser les e-mails

Le canal E-mail prend en charge l’envoi de propriétés personnalisées permettant de créer des e-mails personnalisés plus avancés à l’aide de la propriété `channelData`.

[!INCLUDE [Email channelData table](~/includes/snippet-channelData-email.md)]

L’exemple de message ci-après présente un fichier JSON incluant ces propriétés `channelData`.

```json
{
    "type": "message",
    "locale": "en-Us",
    "channelID": "email",
    "from": { "id": "mybot@mydomain.com", "name": "My bot"},
    "recipient": { "id": "joe@otherdomain.com", "name": "Joe Doe"},
    "conversation": { "id": "123123123123", "topic": "awesome chat" },
    "channelData":
    {
        "htmlBody": "<html><body style = /"font-family: Calibri; font-size: 11pt;/" >This is more than awesome.</body></html>",
        "subject": "Super awesome message subject",
        "importance": "high",
        "ccRecipients": "Yasemin@adatum.com;Temel@adventure-works.com"
    }
}
```

::: moniker range="azure-bot-service-3.0"
Pour plus d’informations sur l’utilisation de `channelData`, consultez l’exemple [Node.js](https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/core-ChannelData) ou la documentation [.NET](~/dotnet/bot-builder-dotnet-channeldata.md).
::: moniker-end

::: moniker range="azure-bot-service-4.0"
Pour plus d’informations sur l’utilisation de `channelData`, consultez l’article portant sur [l’implémentation de fonctionnalités spécifiques du canal](~/v4sdk/bot-builder-channeldata.md).
::: moniker-end

## <a name="other-considerations"></a>Autres considérations

Si votre bot ne retourne pas un code d’état HTTP 200 OK dans les 15 secondes en réponse à un e-mail entrant, le canal de messagerie tente de renvoyer le message, et votre bot peut recevoir la même activité d’e-mail plusieurs fois. Pour plus d’informations, consultez la section [Détails HTTP](v4sdk/bot-builder-basics.md#http-details) dans **Fonctionnement des bots** et l’article de procédure de[Résolution des erreurs de dépassement de délai d’attente](https://github.com/daveta/analytics/blob/master/troubleshooting_timeout.md).

> [!NOTE]
> Si vous utilisez un compte Office 365 sur lequel l'authentification multifacteur est activée, désactivez-la pour le compte spécifié avant de configurer ce dernier pour le canal de messagerie. À défaut, la connexion échouera. 

## <a name="additional-resources"></a>Ressources supplémentaires

<!-- Put whole list in monikers, even though it's just the second item that needs to be different. -->
::: moniker range="azure-bot-service-3.0"
* Connectez un bot à des [canaux](~/bot-service-manage-channels.md)
* [Implémenter des fonctionnalités propres à un canal](dotnet/bot-builder-dotnet-channeldata.md) à l’aide du kit SDK Bot Framework pour .NET
* Pour plus d’informations sur les fonctionnalités prises en charge sur chaque canal, consultez l’article contenant des [informations de référence sur les canaux](bot-service-channels-reference.md).
::: moniker-end
::: moniker range="azure-bot-service-4.0"
* Connectez un bot à des [canaux](~/bot-service-manage-channels.md)
* [Implémenter des fonctionnalités propres à un canal](~/v4sdk/bot-builder-channeldata.md) à l’aide du kit SDK Bot Framework pour .NET
* Pour plus d’informations sur les fonctionnalités prises en charge sur chaque canal, consultez l’article contenant des [informations de référence sur les canaux](bot-service-channels-reference.md).
::: moniker-end

