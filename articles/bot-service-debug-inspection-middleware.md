---
title: Déboguer un robot avec un intergiciel d’inspection | Microsoft Docs
description: Découvrir comment déboguer un robot avec un intergiciel d’inspection
author: zxyanliu
ms.author: v-liyan
keywords: Kit de développement logiciel (SDK) Bot Framework, robot de débogage, intergiciel d’inspection, émulateur de robot, inscription aux canaux de robots Azure
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 7/9/2019
ms.openlocfilehash: fe96131a7087f3f2c4980fe4f2eacb94a4ae9e4a
ms.sourcegitcommit: c200cc2db62dbb46c2a089fb76017cc55bdf26b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70037509"
---
# <a name="debug-a-bot-with-inspection-middleware"></a>Déboguer un robot avec un intergiciel d’inspection
Cet article explique comment déboguer votre bot à l’aide d’un intergiciel d’inspection. Cette fonctionnalité permet à l’émulateur Bot Framework Emulator de déboguer le trafic transitant par le bot, en plus d’inspecter l’état actuel du bot. Vous pouvez utiliser un message de suivi pour envoyer des données au l’émulateur, puis inspecter l’état de votre robot dans n’importe quel tour de la conversation. 

Nous utilisons un EchoBot généré localement à l’aide de Bot Framework v4 ([C#](https://docs.microsoft.com/azure/bot-service/dotnet/bot-builder-dotnet-sdk-quickstart?view=azure-bot-service-4.0) | [JavaScript](https://docs.microsoft.com/azure/bot-service/javascript/bot-builder-javascript-quickstart?view=azure-bot-service-4.0)) pour montrer comment déboguer et inspecter l’état du message du bot. Vous pouvez aussi [Déboguer un bot à l’aide de l’IDE](./bot-service-debug-bot.md) ou le [Déboguer avec Bot Framework Emulator](./bot-service-debug-emulator.md), mais pour déboguer l’état, vous devez ajouter l’intergiciel d’inspection à votre bot. Les exemples de robot d’inspection sont disponibles ici : [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/47.inspection) et [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/47.inspection). 

## <a name="prerequisites"></a>Prérequis
- Téléchargez et installez [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started)
- Connaissances de l’[intergiciel](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-middleware?view=azure-bot-service-4.0) du robot
- Connaissances de l’[état de gestion](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0) du robot
- Télécharger et installer [ngrok](https://ngrok.com/) (si vous souhaitez déboguer un robot configuré dans Azure pour utiliser des canaux supplémentaires)

## <a name="update-your-emulator-to-the-latest-version"></a>Mettre à jour votre émulateur vers la dernière version 
Avant d’utiliser l’intergiciel d’inspection du robot pour déboguer votre robot, vous devez mettre à jour votre émulateur vers la version 4.5 ou ultérieure. Vérifiez la [dernière version](https://github.com/Microsoft/BotFramework-Emulator/releases) des mises à jour. 

Pour vérifier la version de votre émulateur, sélectionnez **Aide** -> **À propos de** dans le menu. La version actuelle de votre émulateur s’affiche. 

![version actuelle](./media/bot-debug-inspection-middleware/bot-debug-check-emulator-version.png) 

## <a name="update-your-bots-code"></a>Mettre à jour le code de votre robot

# <a name="ctabcsharp"></a>[C#](#tab/csharp)
Configurez l’état d’inspection dans le fichier **Démarrage**. Ajoutez l’intergiciel d’inspection à l’adaptateur. L’état d’inspection est fourni par injection de dépendances. Consultez la mise à jour du code ci-dessous ou reportez-vous à l’exemple d’inspection ici : [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/47.inspection). 

**Startup.cs** [!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/Startup.cs?range=17-37)]

**AdapterWithInspection.cs**  
[!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/AdapterwithInspection.cs?range=11-21)]

**EchoBot.cs** [!code-csharp [inspection bot sample](~/../botbuilder-samples/samples/csharp_dotnetcore/47.inspection/Bots/EchoBot.cs?range=14-43)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)
Avant de mettre à jour le code de votre robot, vous devez mettre à jour ses packages vers les versions les plus récentes en exécutant la commande suivante dans votre terminal : 
```cmd
npm install --save botbuilder@latest 
```  
Vous devez ensuite mettre à jour le code de votre bot JavaScript comme suit. Vous trouverez plus d’informations ici : [Mettez à jour le code de votre robot](https://github.com/Microsoft/BotFramework-Emulator/blob/master/content/CHANNELS.md#1-update-your-bots-code) ou reportez-vous à l’exemple d’inspection ici : [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/47.inspection). 

**index.js**

Configurez l’état d’inspection et ajoutez l’intergiciel d’inspection à l’adaptateur dans le fichier **index.js**. 

[!code-javascript [inspection bot sample](~/../botbuilder-samples/samples/javascript_nodejs/47.inspection/index.js?range=10-43)]

**bot.js**

Mettez à jour la classe de robot dans le fichier **bot.js**. 

[!code-javascript [inspection bot sample](~/../botbuilder-samples/samples/javascript_nodejs/47.inspection/bot.js?range=6-50)]

---

## <a name="test-your-bot-locally"></a>Tester votre bot localement 
Après la mise à jour du code, vous pouvez exécuter votre robot localement et tester la fonctionnalité de débogage à l’aide de deux émulateurs : un pour envoyer et recevoir des messages et l’autre pour inspecter l’état des messages en mode débogage. Pour tester votre robot localement, procédez comme suit : 

1. Accédez au répertoire de votre bot dans un terminal et exécutez la commande suivante pour exécuter votre robot localement : 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cmd
dotnet run
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```cmd
npm start 
```

---

2. Ouvrez votre émulateur. Cliquez sur **Ouvrir le robot**. Renseignez l’URL du robot avec http://localhost:3978/api/messages et les valeurs **MicrosoftAppId** et **MicrosoftAppPassword**. Si vous avez un robot JavaScript, vous pouvez trouver ces valeurs dans le fichier **.env** de votre robot. Si vous avez un robot C#, vous pouvez trouver ces valeurs dans le fichier **appSettings.json**. Cliquez sur **Connecter**. 

3. Ouvrez maintenant un autre émulateur. Ce deuxième émulateur fonctionnera comme un débogueur. Suivez les instructions décrites à l’étape précédente. Cochez **Ouvrir en mode débogage** et cliquez sur **Se connecter**. 

4. À ce stade, un UUID (`/INSPECT attach <identifier>`) s’affiche dans votre émulateur de débogage. Copiez l’UUID et collez-le dans la zone de discussion du premier émulateur. 

> [!NOTE]
> Un identificateur unique universel (UUID) est un ID unique pour les informations d’identification. Un UUID est généré chaque fois que l’émulateur est lancé en mode débogage après l’ajout de l’intergiciel d’inspection dans le code de votre robot. 

5. Vous pouvez désormais envoyer des messages dans la zone de discussion de votre premier émulateur et inspecter les messages dans l’émulateur de débogage. Pour inspecter l’état des messages, cliquez sur **Bot State** dans l’émulateur de débogage et dépliez les **valeurs** dans la fenêtre **JSON** appropriée. Vous serez en mesure d’afficher l’état de votre robot comme suit : ![Bot State](./media/bot-debug-inspection-middleware/bot-debug-bot-state.png)

## <a name="inspect-the-state-of-a-bot-configured-in-azure"></a>Inspecter l’état d’un bot configuré dans Azure 
Si vous souhaitez inspecter l’état de votre robot configuré dans Azure et connecté à des canaux (comme Teams), vous devez installer et exécuter[ngrok](https://ngrok.com/).

### <a name="run-ngrok"></a>Exécuter ngrok
À ce stade, vous avez mis à jour votre émulateur vers la dernière version et ajouté l’intergiciel d’inspection dans le code de votre robot. L’étape suivante consiste à exécuter ngrok et à configurer votre robot local sur l’inscription de vos canaux de robots Azure. Avant d’exécuter ngrok, vous devez exécuter votre robot localement. 

Pour développer votre robot localement, procédez comme suit : 
1. Accédez au dossier de votre robot dans un terminal et définissez votre inscription npm pour utiliser les [builds les plus récents](https://botbuilder.myget.org/feed/botbuilder-v4-js-daily/package/npm/botbuilder-azure)

2. Exécutez votre bot en local. Vous verrez que votre robot expose un numéro de port comme 3978. 

3. Ouvrez une autre invite de commandes et accédez au dossier du projet de votre robot. Exécutez la commande suivante :
```
ngrok http 3978
```
4. ngrok est maintenant connecté à votre robot s’exécutant localement. Copiez l’adresse IP publique. 
![ngrok-success](./media/bot-debug-inspection-middleware/bot-debug-ngrok.png)

### <a name="update-channel-registrations-for-your-bot"></a>Mettre à jour les inscriptions de canaux pour votre robot
Maintenant que votre robot local est connecté à ngrok, vous pouvez configurer votre robot local pour l’inscription de vos canaux de robots dans Azure.

1. Allez à l’inscription aux canaux pour votre robot dans Azure. Cliquez sur **Paramètres** dans le menu de gauche, puis, définissez le **Point de terminaison de messagerie** avec votre adresse IP ngrok. Si nécessaire, ajoutez **/api/messages** à la suite de votre adresse IP. (Par exemple, https://e58549b6.ngrok.io/api/messages). Cochez **Activer le point de terminaison de streaming** et **Enregistrer**.
![endpoint](./media/bot-debug-inspection-middleware/bot-debug-channels-setting-ngrok.png)
> [!TIP]
> Si **Enregistrer** n’est pas coché, vous pouvez décocher **Activer le point de terminaison de streaming** et cliquer sur **Enregistrer**, puis cocher **Activer le point de terminaison de streaming** et cliquez sur **Enregistrer** à nouveau. Vous devez vérifier que l’option **Activer le point de terminaison de streaming** est cochée et que la configuration du point de terminaison est enregistrée. 

2. Allez au groupe de ressources de votre robot, cliquez sur **Déploiement**, puis sélectionnez l’inscription de vos canaux de robots qui a été précédemment déployée avec succès. Cliquez sur **Entrées** à gauche pour accéder à **appId** et **appSecret**. Mettez à jour le fichier **.env** de votre robot (ou le fichier **appSettings.json** si vous avez un robot C#) avec  **appId** et **appSecret**. 
![get-inputs](./media/bot-debug-inspection-middleware/bot-debug-get-inputs-id-secret.png)

3. Démarrez votre émulateur, cliquez sur **Ouvrir le robot**, puis placez http://localhost:3978/api/messages dans l’**URL du robot**. Renseignez l’**ID de l’application Microsoft** et le **mot de passe de l’application Microsoft** avec le **appId** et **appSecret** ajouté au fichier **.env** (**appsettings.json**) de votre robot. Puis, cliquez sur **Se connecter**. 

4. Votre robot en cours d’exécution est maintenant connecté à l’inscription de vos canaux de robot dans Azure. Vous pouvez tester la conversation Web en cliquant sur **Tester dans la discussion Web** et en envoyant des messages dans la zone de discussion. 
![test-web-chat](./media/bot-debug-inspection-middleware/bot-debug-test-webchat.png)

5. Nous allons maintenant activer le mode débogage dans l’émulateur. Dans votre émulateur, sélectionnez **Déboguer** -> **Démarrer le débogage**. Renseignez l’adresse IP ngrok (n’oubliez pas d'ajouter **/api/messages**) dans l'**URL du robot** (par exemple, https://e58549b6.ngrok.io/api/messages). Renseignez l’**ID de l’application Microsoft** avec **appId** et le **mot de passe de l’application Microsoft** avec **appSecret**. Assurez-vous que la case **Ouvrir en mode débogage** est également cochée. Cliquez sur **Connecter**. 

6. Lorsque le mode débogage est activé, un UUID est généré dans votre émulateur. Un UUID est un ID unique généré chaque fois que vous démarrez le mode débogage dans votre émulateur. Copiez et collez l’UUID dans la zone de discussion **Tester dans une discussion Web** ou dans la zone de discussion de votre canal. Le message « joint à la session, tout le trafic est répliqué pour inspection » s’affiche dans la zone de discussion. 

 Vous pouvez démarrer le débogage de votre robot en envoyant des messages dans la zone de discussion du canal configuré. Votre émulateur local met automatiquement à jour les messages avec tous les détails pour le débogage. Pour inspecter l’état des messages de votre robot, cliquez sur **Bot State** et dépliez les **valeurs** dans la fenêtre JSON appropriée. 

 ![debug-inspection-middleware](./media/bot-debug-inspection-middleware/debug-state-inspection-channel-chat.gif)

## <a name="additional-resources"></a>Ressources supplémentaires
- Essayez les nouveaux exemples de robots d’inspection ici : [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/47.inspection) et [JavaScript](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/47.inspection). 
- Lisez [résolution des problèmes généraux](bot-service-troubleshoot-bot-configuration.md) ainsi que les autres articles de résolution des problèmes de cette section.
- Lisez l’article pour découvrir comment [Déboguer avec l’émulateur](bot-service-debug-emulator.md).
