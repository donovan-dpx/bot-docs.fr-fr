---
title: Créer un bot avec le service de robot | Microsoft Docs
description: Découvrez comment créer un bot avec le service de robot, un environnement de développement de bots dédié intégré.
keywords: Démarrage rapide, créer un bot, service de robot, bot d’application web
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/15/2019
ms.openlocfilehash: b8307877bf08db170173486ec9df88fae4d27c29
ms.sourcegitcommit: 9e1034a86ffdf2289b0d13cba2bd9bdf1958e7bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69890619"
---
# <a name="create-a-bot-with-azure-bot-service"></a>Créer un bot avec Azure Bot Service

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Azure Bot Service fournit les principaux composants permettant de créer des bots, notamment le kit SDK Bot Framework pour le développement de bots et Bot Service pour la connexion des bots aux canaux. Dans cette rubrique, vous allez pouvoir choisir entre le modèle .NET ou Node.js pour créer un bot à l’aide du kit SDK Bot Framework v4.

>[!NOTE] 
> Le bot que vous créez est automatiquement inscrit auprès d’Azure Bot Service. Si vous souhaitez inscrire un bot existant hébergé ailleurs, consultez cet article : [Inscrire un bot auprès d’Azure Bot Service](../bot-service-quickstart-registration.md).

[!INCLUDE [Azure vs local development](~/includes/snippet-quickstart-paths.md)]

## <a name="prerequisites"></a>Prérequis

- Compte [Azure](http://portal.azure.com)

### <a name="create-a-new-bot-service"></a>Créer un service de robot

1. Connectez-vous au [portail Azure](http://portal.azure.com/).
1. Cliquez sur le lien **Créer une nouvelle ressource** dans le coin supérieur gauche du Portail Azure, puis sélectionnez **IA + Machine Learning**  >  **Robot de l’application web**. 

![créer un bot](../media/azure-bot-quickstarts/abs-create-blade.png)

2. Cette opération affiche un *nouveau panneau* fournissant des informations sur l’offre **Web App Bot**.  

3. Dans le panneau **Bot Service** (Service de robot), indiquez les informations demandées concernant votre bot, comme spécifié dans le tableau figurant sous l’image.  <br/>
 ![Panneau de création d’un bot Web App Bot](../media/azure-bot-quickstarts/sdk-create-bot-service-blade.png)

 | Paramètre | Valeur suggérée | Description |
 | ---- | ---- | ---- |
 | **Nom du bot** | Nom d’affichage du bot | Nom complet du bot qui s’affiche dans les canaux et les répertoires. Ce nom est modifiable à tout moment. |
 | **Abonnement** | Votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
 | **Groupe de ressources** | myResourceGroup | Vous pouvez créer un [groupe de ressources](/azure/azure-resource-manager/resource-group-overview#resource-groups) ou en choisir un. |
 | **Lieu** | Emplacement par défaut | Sélectionnez l’emplacement géographique de votre groupe de ressources. Vous pouvez choisir n’importe quel emplacement répertorié, même s’il est généralement préférable de sélectionner l’emplacement le plus proche de votre client. L’emplacement n’est plus modifiable une fois le bot créé. |
 | **Niveau tarifaire** | F0 | Sélectionnez un niveau tarifaire. Vous pourrez mettre à jour le niveau tarifaire à tout moment. Pour plus d’informations, consultez la page [Tarification Azure Bot Service](https://azure.microsoft.com/pricing/details/bot-service/). |
 | **Nom de l’application** | Un nom unique | Nom d’URL unique du bot. Par exemple, si vous nommez votre bot *myawesomebot*, l’URL de votre bot sera `http://myawesomebot.azurewebsites.net`. Ce nom doit uniquement comporter des caractères alphanumériques et des traits de soulignement. Ce champ est limité à 35 caractères. Le nom d’application n’est plus modifiable une fois le bot créé. |
 | **Modèle de bot** | Bot d’écho | Choisissez **SDK v4** (Kit SDK v4). Pour ce guide de démarrage rapide, sélectionnez C# ou Node.js, puis cliquez sur **Sélectionner**.  
 | **Plan/emplacement du service d’application** | Votre plan App Service  | Sélectionnez un emplacement de [plan App Service](https://azure.microsoft.com/pricing/details/app-service/plans/). Vous pouvez choisir n’importe quel emplacement répertorié, même s’il est généralement préférable de sélectionner le même emplacement que celui des services du bot.Vous pouvez choisir n’importe quel emplacement répertorié, même s’il est généralement préférable de sélectionner le même emplacement que celui des services du bot. |
 | **Application Insights** | Il en va | **Activez** ou **désactivez** [Application Insights](/bot-framework/bot-service-manage-analytics). Si vous sélectionnez **Activé**, spécifiez également un emplacement régional. Vous pouvez choisir n’importe quel emplacement répertorié, même s’il est généralement préférable de sélectionner le même emplacement que celui des services du bot.Vous pouvez choisir n’importe quel emplacement répertorié, même s’il est généralement préférable de sélectionner le même emplacement que celui des services du bot. |
 | **ID d’application et mot de passe Microsoft** | Création automatique de l’ID d’application et du mot de passe | Utilisez cette option si vous voulez entrer manuellement un ID d’application et un mot de passe Microsoft. Sinon, ces derniers seront créés automatiquement au cours du processus de création du bot. Lorsque vous créez une inscription d’application manuellement pour Bot Service, vérifiez que les types de comptes pris en charge sont définis sur « Comptes dans un annuaire organisationnel » ou « Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Outlook.com, Xbox, etc.) ». |

4. Cliquez sur **Créer** pour créer le service et déployer le bot dans le cloud. Ce processus peut prendre quelques minutes.

Vérifiez que le bot a été déployé en consultant les **Notifications**. La notification passera de **Déploiement en cours…** à **Déploiement réussi**. Cliquez sur le bouton **Accéder à la ressource** pour ouvrir le panneau des ressources du bot.

Maintenant que votre bot est créé, testez-le dans la discussion web.

## <a name="test-the-bot"></a>Tester le bot
Dans la section **Gestion du bot**, cliquez sur **Tester dans la Discussion Web**. Azure Bot Service charge le contrôle Discussion Web et se connecte à votre bot. 

![Test de discussion web Azure](../media/azure-bot-quickstarts/azure-webchat-test.png)

Entrez un message ; votre bot devrait répondre.

## <a name="manual-app-registration"></a>Inscription manuelle de l’application

Une inscription manuelle est nécessaire dans les situations suivantes :

- Vous ne pouvez pas effectuer les inscriptions dans votre organisation et devez demander à une tierce personne de créer l’ID d’application pour le bot que vous créez.
- Vous devez créer manuellement votre ID d’application (et le mot de passe).

Consultez [FAQ - Inscription d’application](../bot-service-resources-bot-framework-faq.md#app-registration).


## <a name="download-code"></a>Télécharger le code
Vous pouvez télécharger le code pour l’utiliser localement. 
1. Dans la section **Gestion du bot**, cliquez sur **Créer**. 
1. Cliquez sur le lien **Download Bot source code** (Télécharger le code source du bot) dans le volet droit. 
1. Suivez les invites pour télécharger le code, puis décompressez le dossier.
    1. [!INCLUDE [download keys snippet](../includes/snippet-abs-key-download.md)]

## <a name="next-steps"></a>Étapes suivantes
Après avoir téléchargé le code, vous pouvez continuer à développer le bot localement sur votre ordinateur. Une fois que vous avez testé votre bot et êtes prêt à charger le code du bot dans le portail Azure, suivez les instructions listées dans la rubrique [Configurer le déploiement continu](../bot-service-build-continuous-deployment.md) pour mettre à jour automatiquement le code après avoir apporté des modifications.
