---
title: Tutoriel consacré à la création et au déploiement d'un bot de base - Bot Service
description: Apprenez à créer un bot de base et à le déployer dans Azure.
keywords: bot echo, déployer, azure, tutoriel
author: Ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 05/23/2019
monikerRange: azure-bot-service-4.0
ms.openlocfilehash: fadc7410925d337a518129736c9374035fe2114d
ms.sourcegitcommit: f8b5cc509a6351d3aae89bc146eaabead973de97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75791211"
---
# <a name="tutorial-create-and-deploy-a-basic-bot"></a>Tutoriel : Créer et déployer un bot de base

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

Ce tutoriel vous aide à créer pas à pas un bot de base avec le kit SDK Bot Framework et à le déployer dans Azure. Si vous avez déjà créé un bot de base et qu’il s’exécute localement, passez directement à la section [Déployer votre bot](#deploy-your-bot).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un bot Echo de base
> * L’exécuter et interagir avec lui localement
> * Publier votre bot

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [dotnet quickstart](~/includes/quickstart-dotnet.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!INCLUDE [javascript quickstart](~/includes/quickstart-javascript.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [python quickstart](~/includes/quickstart-python.md)]

---

## <a name="deploy-your-bot"></a>Déployer votre bot

### <a name="prerequisites"></a>Conditions préalables requises
[!INCLUDE [deploy prerequisite](~/includes/deploy/snippet-prerequisite.md)]

### <a name="prepare-for-deployment"></a>Préparer le déploiement
[!INCLUDE [deploy prepare intro](~/includes/deploy/snippet-prepare-deploy-intro.md)]

#### <a name="1-login-to-azure"></a>1. Connexion à Azure
[!INCLUDE [deploy az login](~/includes/deploy/snippet-az-login.md)]

#### <a name="2-set-the-subscription"></a>2. Définir l’abonnement
[!INCLUDE [deploy az subscription](~/includes/deploy/snippet-az-set-subscription.md)]

#### <a name="3-create-an-app-registration"></a>3. Créer une inscription d’application
[!INCLUDE [deploy create app registration](~/includes/deploy/snippet-create-app-registration.md)]

#### <a name="4-deploy-via-arm-template"></a>4. Déployer le bot avec un modèle ARM
Vous pouvez déployer votre bot dans un groupe de ressources nouveau ou existant. Choisissez l’option qui vous convient le mieux.

> [!NOTE]
> Vous ne pouvez pas déployer les bots Python sur un groupe de ressources qui contient des services/bots Windows.  Vous pouvez déployer plusieurs bots Python sur le même groupe de ressources, mais vous devez créer les autres services (LUIS, QnA, etc.) dans un autre groupe de ressources.
>

##### <a name="deploy-via-arm-template-with-new-resource-group"></a>**Déployer par le biais du modèle ARM avec un nouveau groupe de ressources**
[!INCLUDE [ARM with new resourece group](~/includes/deploy/snippet-ARM-new-resource-group.md)]

##### <a name="deploy-via-arm-template-with-existing-resource-group"></a>**Déployer par le biais du modèle ARM avec un groupe de ressources existant**
[!INCLUDE [ARM with existing resourece group](~/includes/deploy/snippet-ARM-existing-resource-group.md)]

#### <a name="5-prepare-your-code-for-deployment"></a>5. Préparer votre code en vue du déploiement
##### <a name="retrieve-or-create-necessary-iiskudu-files"></a>**Récupérer ou créer les fichiers IIS/Kudu nécessaires**
[!INCLUDE [retrieve or create IIS/Kudu files](~/includes/deploy/snippet-IIS-Kudu-files.md)]

##### <a name="zip-up-the-code-directory-manually"></a>**Compresser manuellement le répertoire du code**
[!INCLUDE [zip up code](~/includes/deploy/snippet-zip-code.md)]

### <a name="deploy-code-to-azure"></a>Déployer le code sur Azure
[!INCLUDE [deploy code to Azure](~/includes/deploy/snippet-deploy-code-to-az.md)]

### <a name="test-in-web-chat"></a>Tester dans la Discussion Web
[!INCLUDE [test in web chat](~/includes/deploy/snippet-test-in-web-chat.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

[!INCLUDE [additional resources snippet](~/includes/deploy/snippet-additional-resources.md)]

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Utiliser QnA Maker dans votre bot pour répondre à des questions](bot-builder-tutorial-add-qna.md)