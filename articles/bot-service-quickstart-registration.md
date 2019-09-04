---
title: Créer une inscription Bot Channels Registration auprès de Bot Service | Microsoft Docs
description: Découvrez comment inscrire un bot déjà créé auprès de Bot Service.
ms.author: kamrani
manager: kamrani
ms.topic: conceptual
ms.service: bot-service
ms.date: 12/13/2017
monikerRange: azure-bot-service-3.0
ms.openlocfilehash: 014f5c998fcb9d322439ca8b0e0bf2ba5f9f0679
ms.sourcegitcommit: 0b647dc6716b0c06f04ee22ebdd7b53039c2784a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076512"
---
# <a name="register-a-bot-with-azure-bot-service"></a>Inscrire un bot auprès d’Azure Bot Service

Cette rubrique vous montre comment créer une ressource **Azure Bot Service** pour inscrire votre bot. Cette étape est nécessaire si le bot est hébergé ailleurs et que vous souhaitez le rendre disponible dans Azure et le connecter à des canaux Azure Bot Service.

Cela vous permet de créer, connecter et gérer votre bot pour interagir avec les utilisateurs où qu’ils soient, par le biais de Cortana, Skype, Messenger et divers autres services.

> [!IMPORTANT] 
> L’inscription du bot n’est nécessaire que s’il n’est pas hébergé dans Azure. Si le bot [a été créé](v4sdk/abs-quickstart.md) dans le portail Azure, il est déjà inscrit auprès du service.

## <a name="create-a-registration-resource"></a>Créer une ressource d’inscription

1. Dans votre navigateur, accédez au [Portail Azure](https://ms.portal.azure.com).

    > [!TIP]
    > Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour obtenir un <a href="https://azure.microsoft.com/free/" target="_blank">compte gratuit</a>.

1. Dans le volet de gauche, cliquez sur **Créer une ressource**.
1. Dans la zone de sélection du volet de droite, entrez *bot*. Dans la liste déroulante, sélectionnez **Bot Channels Registration** (Inscription des canaux du bot) ou **Web App Bot** selon votre application.
Pour **Web Bot App**, suivez les étapes décrites dans l’article : [Créer un bot avec Azure Bot Service](v4sdk/abs-quickstart.md). Vous allez créer dans Azure un bot qui sera automatiquement inscrit auprès d’Azure Bot Service.
1. Cliquez sur le bouton **Créer** pour lancer le processus.
1. Dans le panneau **Bot Service** (Service de robot), indiquez les informations demandées concernant votre bot, comme spécifié dans le tableau figurant sous l’image.  

   ![Panneau Créer un bot d’inscription](media/azure-bot-quickstarts/registration-create-bot-service-blade.png)

   |Paramètre |Valeur suggérée|Description|
   |---|---|--|
   |**Nom du bot** <img width="300px">|Nom d’affichage du bot|Nom complet du bot qui s’affiche dans les canaux et les répertoires. Ce nom est modifiable à tout moment.|
   |**Abonnement**|Votre abonnement|Sélectionnez l’abonnement Azure à utiliser.|
   |**Groupe de ressources**|myResourceGroup|Vous pouvez créer un [groupe de ressources](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou en choisir un.|
   |**Lieu**|USA Ouest|Choisissez un emplacement proche de celui où est déployé votre bot ou proche d’autres services auxquels il accédera.|
   |**Niveau tarifaire**|F0|Sélectionnez un niveau tarifaire. Vous pourrez mettre à jour le niveau tarifaire à tout moment. Pour plus d’informations, voir [Prix de Bot Service](https://azure.microsoft.com/pricing/details/bot-service/).|
   |**Point de terminaison de messagerie**|URL|Entrez l’URL du point de terminaison de messagerie de votre bot.|
   |**Application Insights**|Il en va| **Activez** ou **désactivez** [Application Insights](bot-service-manage-analytics.md). Si vous sélectionnez **Activé**, spécifiez également un emplacement régional. |
   |**ID d'application et mot de passe Microsoft**| Création automatique de l’ID d’application et du mot de passe |Utilisez cette option si vous voulez entrer manuellement un ID d’application et un mot de passe Microsoft. Consultez la section suivante, [Inscription manuelle de l’application](#manual-app-registration). Sinon, un nouvel ID d’application et un nouveau mot de passe seront créés durant le processus d’inscription. |

    > [!IMPORTANT]
    > N’oubliez pas d’entrer l’URL du point de terminaison de messagerie de votre bot.

1. Cliquez sur le bouton **Créer**. Attendez que la ressource soit créée. La nouvelle ressource s’affiche dans votre liste de ressources.

### <a name="get-registration-password"></a>Obtenir le mot de passe de l’inscription

Une fois l’inscription terminée, Azure Active Directory affecte un ID d’application unique à votre inscription, puis vous êtes redirigé vers la page *Vue d’ensemble* de votre application.

Pour obtenir le mot de passe, suivez les étapes décrites ci-après.

1. Dans la liste des ressources, cliquez sur la ressource Azure App Service que vous venez de créer.
1. À droite, dans le panneau de la ressource, cliquez sur **Paramètres**. La page *Paramètres* de la ressource s’affiche.
1. Dans la page Paramètres, copiez l’**ID d’application Microsoft** généré et enregistrez-le dans un fichier.
1. Cliquez sur le lien **Gérer** par *ID d’application Microsoft*.

    ![Panneau Créer un bot d’inscription](media/azure-bot-quickstarts/bot-channels-registration-app-settings.png)

1. Dans la page *Certificats et secrets* qui s’affiche, cliquez sur le bouton **Nouveau secret client**.

    ![Panneau Créer un bot d’inscription](media/azure-bot-quickstarts/bot-channels-registration-app-secrets.png)

1. Ajoutez la description, sélectionnez le délai d’expiration, puis cliquez sur le bouton **Ajouter**.

    ![Panneau Créer un bot d’inscription](media/azure-bot-quickstarts/bot-channels-registration-app-secrets-create.png)

    pour générer un nouveau mot de passe pour votre bot. Copiez-le et enregistrez-le dans un fichier. C’est la seule fois où vous verrez ce mot de passe. Si vous n’enregistrez pas le mot de passe complet, vous devrez répéter le processus de création d’un mot de passe pour la suite.

## <a name="manual-app-registration"></a>Inscription manuelle de l’application

Une inscription manuelle est nécessaire dans les situations suivantes :

- Vous ne pouvez pas effectuer les inscriptions dans votre organisation et devez demander à une tierce personne de créer l’ID d’application pour le bot que vous créez.
- Vous devez créer manuellement votre ID d’application (et le mot de passe).

Consultez [FAQ - Inscription d’application](bot-service-resources-bot-framework-faq.md#app-registration).

> [!IMPORTANT]
> Dans la section *Supports account types* (Prend en charge les types de comptes), vous devez choisir l’un de ces deux types multilocataires : *Comptes dans un annuaire d’organisation (tout annuaire Azure AD - Multilocataire)* ou *Comptes dans un annuaire organisationnel (tout annuaire Azure AD - Multilocataire) et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)*  quand vous créez l’application ; sinon, le bot ne fonctionnera pas. Pour plus d’informations, consultez [Inscrire une nouvelle application à l’aide du portail Azure](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal).

## <a name="update-the-bot"></a>Mettre à jour le bot

Si vous utilisez le kit SDK Bot Framework pour .NET, définissez les valeurs de clé suivantes dans le fichier web.config :

- `MicrosoftAppId = <appId>`
- `MicrosoftAppPassword = <appSecret>`

Si vous utilisez le kit SDK Bot Framework pour Node.js, définissez les variables d’environnement suivantes :

- `MICROSOFT_APP_ID = <appId>`
- `MICROSOFT_APP_PASSWORD = <appSecret>`

## <a name="test-the-bot"></a>Tester le bot

Maintenant que votre service de bot est créé, [testez-le dans la Discussion Web](bot-service-manage-test-webchat.md). Entrez un message ; votre bot devrait répondre.

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez inscrit votre bot hébergé auprès de Bot Service. L’étape suivante consiste à apprendre à gérer votre Bot Service.

> [!div class="nextstepaction"]
> [Gérer un bot](bot-service-manage-overview.md)
