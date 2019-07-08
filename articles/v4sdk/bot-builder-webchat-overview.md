---
title: Vue d’ensemble de Web Chat | Microsoft Docs
description: Découvrez comment configurer Bot Framework Web Chat.
keywords: bot framework, conversation web, conversation, exemples, react, référence
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 06/07/2019
ms.openlocfilehash: 4dab0a8f3dab2a87184a03fe25b2a9abeaff67a2
ms.sourcegitcommit: dbbfcf45a8d0ba66bd4fb5620d093abfa3b2f725
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67464655"
---
# <a name="web-chat-overview"></a>Vue d’ensemble de Web Chat

Cet article décrit en détail le composant [Bot Framework Web Chat](https://github.com/microsoft/BotFramework-WebChat). Le composant Bot Framework Web Chat est un client web hautement personnalisable pour le Kit de développement Bot Framework V4. Le kit SDK Bot Framework v4 permet aux développeurs de modéliser une conversation et de créer des applications de bot évoluées.

Si vous avez besoin de migrer de la version 3 vers la version 4 de Web Chat, passez directement à la [section consacrée à la migration](#migrating-from-web-chat-v3-to-v4).

## <a name="how-to-use"></a>Utilisation

> [!NOTE]
> Pour les versions précédentes de Web Chat (v3), rendez-vous dans la [branche Web Chat v3](https://github.com/Microsoft/BotFramework-WebChat/tree/v3).

Tout d’abord, créez un bot à l’aide d’[Azure Bot Service](https://azure.microsoft.com/services/bot-service/).
Une fois le bot créé, vous devez [obtenir la clé secrète Web Chat du bot](../bot-service-channel-connect-webchat.md#step-1) dans le Portail Azure. Utilisez ensuite la clé secrète pour [générer un jeton](../rest-api/bot-framework-rest-direct-line-3-0-authentication.md) et transmettez celui-ci à votre Web Chat.

Voici la façon dont vous pouvez ajouter le contrôle Web Chat à votre site web :

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  token: 'YOUR_DIRECT_LINE_TOKEN'
               }),
               userID: 'YOUR_USER_ID',
               username: 'Web Chat User',
               locale: 'en-US',
               botAvatarInitials: 'WC',
               userAvatarInitials: 'WW'
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

> `userID`, `username`, `locale`, `botAvatarInitials` et `userAvatarInitials` sont tous des paramètres facultatifs à transmettre à la méthode `renderWebChat`. Pour en savoir plus sur les propriétés Web Chat, consultez la section [Référence de l’API Web Chat](#web-chat-api-reference) dans ce fichier `README`.
> ![Capture d’écran de Web Chat](https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/weatherquery.png.jpg)

### <a name="integrate-with-javascript"></a>Intégration JavaScript

Web Chat est conçu pour s’intégrer à votre site web existant à l’aide de JavaScript ou React. L’intégration JavaScript vous donnera des possibilités de personnalisation et de mise en forme modérées.

Vous pouvez utiliser la version complète, le package webchat classique qui contient les fonctionnalités les plus utilisées.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  token: 'YOUR_DIRECT_LINE_TOKEN'
               }),
               userID: 'YOUR_USER_ID'
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

Consultez l’exemple fonctionnel de [l’offre groupée complète Web Chat](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.a.getting-started-full-bundle).

### <a name="integrate-with-react"></a>Intégration React

Pour une personnalisation complète, vous pouvez modifier les composants Web Chat à l’aide de React.

Pour installer la version de production à partir de NPM, exécutez `npm install botframework-webchat`.

```jsx
import { DirectLine } from 'botframework-directlinejs';
import React from 'react';
import ReactWebChat from 'botframework-webchat';

export default class extends React.Component {
  constructor(props) {
    super(props);

    this.directLine = new DirectLine({ token: 'YOUR_DIRECT_LINE_TOKEN' });
  }

  render() {
    return (
      <ReactWebChat directLine={ this.directLine } userID='YOUR_USER_ID' />
      element
    );
  }
}
```

> Vous pouvez également exécuter `npm install botframework-webchat@master` pour installer une version de développement qui est synchronisée avec la branche `master` GitHub de Web Chat.

Reportez-vous à un exemple fonctionnel d’[affichage de Web Chat via React](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.a.host-with-react/).

## <a name="customize-web-chat-ui"></a>Personnaliser l’interface utilisateur de Discussion Web

Web Chat est conçu pour être personnalisable sans duplication de code source. Le tableau ci-dessous décrit les différentes personnalisations vous pouvez obtenir selon les façons dont vous importez Web Chat. Cette liste n’est pas exhaustive.

|                               | Offre groupée CDN         | React              |
| ----------------------------- | ------------------ | ------------------ |
| Modifier les couleurs                 | :heavy_check_mark: | :heavy_check_mark: |
| Modifier les tailles                  | :heavy_check_mark: | :heavy_check_mark: |
| Mettre à jour/remplacer les styles CSS     | :heavy_check_mark: | :heavy_check_mark: |
| Écouter les événements              | :heavy_check_mark: | :heavy_check_mark: |
| Interagir avec la page web d’hébergement | :heavy_check_mark: | :heavy_check_mark: |
| Activités de rendu personnalisées      |                    | :heavy_check_mark: |
| Pièces jointes de rendu personnalisées     |                    | :heavy_check_mark: |
| Ajouter de nouveaux composants d’interface utilisateur         |                    | :heavy_check_mark: |
| Recomposer l’interface utilisateur entière        |                    | :heavy_check_mark: |

Reportez-vous à la [personnalisation de Web Chat](https://github.com/Microsoft/BotFramework-WebChat/blob/master/SAMPLES.md) pour en savoir plus sur la personnalisation.

## <a name="migrating-from-web-chat-v3-to-v4"></a>Migration de Web Chat v3 vers v4

La migration de la v3 vers la v4 peut s’effectuer de trois manières différentes. Tout d’abord, comparez votre scénario de début :

### <a name="my-current-website-integrates-web-chat-using-an-iframe-element-obtained-from-azure-bot-services-i-want-to-upgrade-to-v4"></a>Mon site web actuel intègre Web Chat à l’aide d’un élément `<iframe>` obtenu auprès d’Azure Bot Services. Je veux effectuer la mise à niveau vers la version 4.

Depuis mai 2019, nous déployons la version 4 sur les sites web intégrant Web Chat à l’aide de l’élément `<iframe>`. Reportez-vous à [la documentation sur l’incorporation](https://github.com/Microsoft/BotFramework-WebChat/tree/master/packages/embed) pour plus d’informations sur l’intégration de Web Chat par `<iframe>`.

### <a name="my-website-is-integrated-with-web-chat-v3-and-uses-customization-options-provided-by-web-chat-no-customization-at-all-or-very-little-of-my-own-customization-that-was-not-available-with-web-chat"></a>Mon site web intègre Web Chat v3 et utilise les options de personnalisation fournies par Web Chat, ou bien il n’est pas personnalisé du tout ou il intègre très peu d’éléments de personnalisation qui n’étaient pas disponibles avec Web Chat.

Veuillez suivre l’implémentation de l’exemple [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration) pour convertir votre page web de Web Chat v3 à Web Chat v4.

### <a name="my-website-is-integrated-with-a-fork-of-web-chat-v3-i-have-implemented-a-lot-of-customization-in-my-version-of-web-chat-and-i-am-concerned-v4-is-not-compatible-with-my-needs"></a>Mon site Web est intégré à une duplication de Web Chat v3. J’ai implémenté un grand nombre d’éléments de personnalisation dans ma version de Web Chat, et je crains à présent que la v4 ne soit pas compatible avec mes besoins.

Notre équipe a conçu la version 4 pour que la personnalisation soit possible **sans duplication de Web Chat**. Bien que cela crée une charge de travail pour les utilisateurs de la v3 qui ont déjà dupliqué Web Chat, nous ferons de notre mieux pour les aider à passer de la v3 à la v4. Implémentez les suggestions suivantes :

-  Examinez l’implémentation de l’exemple [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration). Il s’agit d’un point de départ idéal en vue d’exécuter Web Chat.
-  Ensuite, passez en revue la [liste d’exemples](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples) pour comparer vos besoins de personnalisation aux besoins que Web Chat couvre déjà. Ces exemples comportent des fonctionnalités couramment demandées pour Web Chat.
-  Si une ou plusieurs de vos fonctionnalités ne sont pas disponibles dans les exemples, veuillez consulter les [questions ouvertes et les questions fermées](https://github.com/Microsoft/BotFramework-WebChat/issues?utf8=%E2%9C%93&q=is%3Aissue+), [l’étiquette Exemples](https://github.com/Microsoft/BotFramework-WebChat/issues?utf8=%E2%9C%93&q=is%3Aissue+is%3Aopen+label%3ASample) et [l’étiquette Support de migration](https://github.com/Microsoft/BotFramework-WebChat/issues?q=is%3Aissue+migrate+label%3A%22Migration+Support%22) afin de rechercher une fonctionnalité dans les demandes d’exemple et/ou le support de personnalisation. L’ajout de vos commentaires aux questions ouvertes aidera l’équipe à hiérarchiser les demandes les plus urgentes. Nous vous encourageons à participer activement à notre communauté.
-  Si vous n’avez pas trouvé votre fonctionnalité dans la liste des demandes ouvertes, n’hésitez pas à [déposer votre propre demande](https://github.com/Microsoft/BotFramework-WebChat/issues/new). Tout comme pour l’élément ci-dessus, d’autres clients qui ajoutent des commentaires à votre problème nous aideront à hiérarchiser les fonctionnalités les plus couramment utilisées par les utilisateurs de Web Chat.
-  Enfin, si vous avez besoin de votre fonctionnalité le plus vite possible, nous vous invitons à effectuer des [demandes de tirage (pull requests)](https://github.com/Microsoft/BotFramework-WebChat/compare) sur Web Chat. Si vous avez l’expérience du codage pour implémenter la fonctionnalité vous-même, nous apprécierions l’aide supplémentaire que vous apporterez à la communauté ! Si vous créez la fonctionnalité vous-même, vous pourrez l’utiliser plus rapidement sur Web Chat, et d’autres clients à la recherche de la même fonctionnalité ou d'une fonctionnalité similaire pourront utiliser votre contribution.
-  Lisez ce `README` jusqu’à la fin pour en savoir plus sur la v4.

## <a name="samples-list"></a>Liste d’exemples

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Exemple&nbsp;Nom&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Description                                                                                                                                                                                                                         | Lien                                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| [`01.a.getting-started-full-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.a.getting-started-full-bundle)                                                                                       | Présente l'intégration de Web Chat à partir d'un CDN et présente un Web Chat simple et complet. Cela inclut les dépendances Adaptive Cards, Cognitive Services et Markdown-It.                                                            | [Démonstration d’offre groupée complète](https://microsoft.github.io/BotFramework-WebChat/01.a.getting-started-full-bundle)                               |
| [`01.b.getting-started-es5-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.b.getting-started-es5-bundle)                                                                                         | Présente l’intégration complète de Web Chat avec rétrocompatibilité pour les navigateurs ES5 utilisant la fonctionnalité de remplissage poneyfill ES5 de Web Chat.                                                                                                                | [Démonstration d’offre groupée ES5](https://microsoft.github.io/BotFramework-WebChat/01.b.getting-started-es5-bundle)                                 |
| [`01.c.getting-started-migration`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/01.c.getting-started-migration)                                                                                           | Montre comment migrer de votre bot Web Chat v3 vers la v4.                                                                                                                                                                        | [Démonstration de la migration](https://microsoft.github.io/BotFramework-WebChat/01.c.getting-started-migration)                                   |
| [`02.a.getting-started-minimal-bundle`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/02.a.getting-started-minimal-bundle)                                                                                 | Présente le CDN minimisé avec seulement les dépendances de base. Cela n’inclut PAS les dépendances Adaptive Cards, Cognitive Services et Markdown-It.                                                                      | [Démonstration d’offre groupée minimale](https://microsoft.github.io/BotFramework-WebChat/02.a.getting-started-minimal-bundle)                         |
| [`02.b.getting-started-minimal-markdown`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/02.b.getting-started-minimal-markdown)                                                                             | Démontre comment ajouter la dépendance CDN pour Markdown-It au-dessus de l’offre groupée minimale.                                                                                                                                            | [Démonstration minimale avec Markdown](https://microsoft.github.io/BotFramework-WebChat/02.b.getting-started-minimal-markdown)                |
| [`03.a.host-with-react`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.a.host-with-react)                                                                                                               | Montre comment créer un composant React qui héberge Web Chat en version complète.                                                                                                                                                 | [Démonstration d’hébergement avec React](https://microsoft.github.io/BotFramework-WebChat/03.a.host-with-react)                                       |
| [`03.b.host-with-Angular`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/03.b.host-with-angular)                                                                                                           | Montre comment créer un composant Angular qui héberge Web Chat en version complète.                                                                                                                                              | [Démonstration d’hébergement avec Angular](https://stackblitz.com/github/omarsourour/ng-webchat-example)                                              |
| [`04.a.display-user-bot-initials-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/04.a.display-user-bot-initials-styling)                                                                           | Montre comment afficher les initiales des deux participants à une conversation Web Chat.                                                                                                                                                                | [Démonstration d’affichage d’initiales à l’aide du bot](https://github.com/Microsoft/BotFramework-WebChat/blob/master/samples/04.a.display-user-bot-initials-styling/)  |
| [`04.b.display-user-bot-images-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/04.b.display-user-bot-images-styling)                                                                               | Montre comment afficher les photos et les initiales des deux participants à une conversation Web Chat.                                                                                                                                                     | [Démonstration d’affichage des photos des utilisateurs](https://microsoft.github.io/BotFramework-WebChat/04.b.display-user-bot-images-styling)                           |
| [`05.a.branding-webchat-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.a.branding-webchat-styling)                                                                                             | Permet de personnaliser le style de Web Chat pour qu’il corresponde à votre marque. Cette méthode de personnalisation n’est pas écrasée par les mises à jour de Web Chat.                                                                                                   | [Démonstration de personnalisation de Web Chat aux couleurs de votre entreprise](https://microsoft.github.io/BotFramework-WebChat/05.a.branding-webchat-styling)                            |
| [`05.b.idiosyncratic-manual-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.b.idiosyncratic-manual-styling/)                                                                                    | Montre comment personnaliser manuellement le style de Web Chat ; cette méthode de personnalisation est la plus compliquée et la plus longue. Les styles manuels risquent d’être écrasés par les mises à jour de Web Chat.                                                | [Démonstration de personnalisation idiosyncrasique](https://microsoft.github.io/BotFramework-WebChat/05.b.idiosyncratic-manual-styling)                    |
| [`05.c.presentation-mode-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.c.presentation-mode-styling)                                                                                           | Montre comment configurer le mode Présentation, qui affiche l'historique du chat mais masque la boîte d'envoi et désactive l’interactivité d’Adaptive Cards.                                                                         | [Démonstration du mode Présentation](https://microsoft.github.io/BotFramework-WebChat/05.c.presentation-mode-styling)                           |
| [`05.d.hide-upload-button-styling`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/05.d.hide-upload-button-styling)                                                                                         | Montre comment masquer le bouton de chargement des fichiers dans le cadre de la personnalisation.                                                                                                                                                                            | [Démonstration Masquer le bouton Chargement des fichiers](https://microsoft.github.io/BotFramework-WebChat/05.d.hide-upload-button-styling)                         |
| [`06.a.cognitive-services-bing-speech-js`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.a.cognitive-services-bing-speech-js)                                                                           | Présente la synthèse vocale dans le sens voix -> texte et texte -> voix à l’aide de l'API (obsolète) Cognitive Services Reconnaissance vocale Bing et de JavaScript.                                                                                                      | [Démonstration Reconnaissance vocale avec JS](https://microsoft.github.io/BotFramework-WebChat/06.a.cognitive-services-bing-speech-js)                 |
| [`06.b.cognitive-services-bing-speech-react`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.b.cognitive-services-bing-speech-react)                                                                     | Présente la synthèse vocale dans le sens voix -> texte et texte -> voix à l’aide de l'API (obsolète) Cognitive Services Reconnaissance vocale Bing et de React.                                                                                                           | [Démonstration Reconnaissance vocale Bing avec React](https://microsoft.github.io/BotFramework-WebChat/06.b.cognitive-services-bing-speech-react)           |
| [`06.c.cognitive-services-speech-services-js`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.c.cognitive-services-speech-services-js)                                                                   | Présente la synthèse vocale dans le sens voix -> texte et texte -> voix à l’aide de l’API Speech Services de Cognitive Services.                                                                                                                                  | [Démonstration de Speech Services avec JS](https://microsoft.github.io/BotFramework-WebChat/06.c.cognitive-services-speech-services-js)         |
| [`06.d.speech-web-browser`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.d.speech-web-browser)                                                                                                         | Montre comment implémenter la synthèse vocale à l’aide de l’API Web Speech basée sur le navigateur dans Web Chat. (exemple incluant un lien vers la norme W3C)                                                                                                    | [Démonstration de l’API Web Speech](https://microsoft.github.io/BotFramework-WebChat/06.d.speech-web-browser)                                     |
| [`06.e.cognitive-services-speech-services-with-lexical-result`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.e.cognitive-services-speech-services-with-lexical-result)                                 | Montre comment utiliser les résultats lexicaux à l’aide de l’API Speech Services de Cognitive Services.                                                                                                                                                 | [Démonstration de résultats lexicaux](https://microsoft.github.io/BotFramework-WebChat/06.e.cognitive-services-speech-services-with-lexical-result) |
| [`06.f.hybrid-speech`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/06.f.hybrid-speech)                                                                                                                   | Montre comment utiliser à la fois l’API Web Speech basée sur le navigateur pour la synthèse voix -> texte et l'API Speech Services de Cognitive Services pour la synthèse texte -> voix.                                                                                        | [Démonstration de synthèse vocale hybride](https://microsoft.github.io/BotFramework-WebChat/06.f.hybrid-speech)                                           |
| [`07.a.customization-timestamp-grouping`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/07.a.customization-timestamp-grouping)                                                                             | Démontre comment personnaliser les horodatages en affichant ou masquant les horodatages, et en modifiant le regroupement des messages par heure.                                                                                                             | [Démonstration de regroupement d’horodatages](https://microsoft.github.io/BotFramework-WebChat/07.a.customization-timestamp-grouping)                   |
| [`07.b.customization-send-typing-indicator`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/07.b.customization-send-typing-indicator)                                                                       | Montre comment envoyer une activité de saisie lorsque l’utilisateur commence à saisir du texte dans la boîte d’envoi.                                                                                                                                                | [Démonstration d’affichage d’un indicateur de saisie utilisateur](https://microsoft.github.io/BotFramework-WebChat/07.b.customization-send-typing-indicator)             |
| [`08.customization-user-highlighting`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/08.customization-user-highlighting)                                                                                   | Montre comment personnaliser le style des activités selon que le message provient de l’utilisateur ou du bot.                                                                                                                      | [Démonstration sur la personnalisation contextuelle de Web Chat](https://microsoft.github.io/BotFramework-WebChat/08.customization-user-highlighting)                       |
| [`09.customization-reaction-buttons`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/09.customization-reaction-buttons/)                                                                                    | Montre comment créer des composants personnalisés pour Web Chat qui sont propres aux besoins de votre bot. Ce didacticiel montre comment ajouter des émojis de réaction, tels que :thumbsup: et :thumbsdown:, aux activités de conversation. | [Démonstration d’insertion de boutons de réaction](https://microsoft.github.io/BotFramework-WebChat/09.customization-reaction-buttons)                         |
| [`10.a.customization-card-components`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/10.a.customization-card-components)                                                                                   | Montre comment créer des pièces jointes personnalisées pour les cartes d’activités, dans ce cas-ci des cartes du référentiel GitHub.                                                                                                                                  | [Démonstration de composants de carte](https://microsoft.github.io/BotFramework-WebChat/10.a.customization-card-components)                         |
| [`10.b.customization-password-input`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/10.b.customization-password-input)                                                                                     | Montre comment créer une activité personnalisée pour la saisie du mot de passe.                                                                                                                                                                      | [Démonstration de saisie du mot de passe](https://microsoft.github.io/BotFramework-WebChat/10.b.customization-password-input)                           |
| [`11.customization-redux-actions`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/11.customization-redux-actions)                                                                                           | Didacticiel avancé : montre comment incorporer des intergiciels (middleware) redux à votre application Web Chat en envoyant des actions redux via le bot. Cet exemple montre un style manuel basé sur les activités entre bot et utilisateur.             | [Démonstration d’actions redux](https://microsoft.github.io/BotFramework-WebChat/11.customization-redux-actions)                               |
| [`12.customization-minimizable-web-chat`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/12.customization-minimizable-web-chat)                                                                             | Didacticiel avancé : Montre comment ajouter l'interface Web Chat à votre site web en tant que boîte de discussion affichable et minimisable.                                                                                                              | [Démonstration de boîte de discussion minimisable Web Chat](https://microsoft.github.io/BotFramework-WebChat/12.customization-minimizable-web-chat)                 |
| [`13.customization-speech-ui`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/13.customization-speech-ui)                                                                                                   | Didacticiel avancé : montre comment personnaliser entièrement les composants clés de votre bot, de reconnaissance vocale dans le cas présent, qui remplace entièrement la transcription texte de l’interface utilisateur par un bouton vocal simple avec la réponse du bot.      | [Démonstration de l’interface utilisateur Speech](https://microsoft.github.io/BotFramework-WebChat/13.customization-speech-ui)                                       |
| [`14.customization-piping-to-redux`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/14.customization-piping-to-redux)                                                                                       | Didacticiel avancé : montre comment diriger les activités d’un bot vers votre propre magasin Redux et utiliser votre bot pour contrôler votre page grâce aux activités du bot et Redux.                                                                          | [Démonstration de branchement vers Redux](https://microsoft.github.io/BotFramework-WebChat/14.customization-piping-to-redux)                           |
| [`15.a.backchannel-piggyback-on-outgoing-activities`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.a.backchannel-piggyback-on-outgoing-activities)                                                     | Didacticiel avancé : montre comment ajouter des données personnalisées à chaque activité sortante.                                                                                                                                                | [Démonstration de superpositions Backchannel](https://microsoft.github.io/BotFramework-WebChat/15.a.backchannel-piggyback-on-outgoing-activities) |
| [`15.b.incoming-activity-event`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.b.incoming-activity-event)                                                                                               | Didacticiel avancé : montre comment transférer toutes les activités entrantes vers un événement JavaScript en vue d’un traitement ultérieur.                                                                                                                | [Démonstration d’activité entrante](https://microsoft.github.io/BotFramework-WebChat/15.b.incoming-activity-event)                             |
| [`15.c.programmatic-post-activity`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.c.programmatic-post-activity)                                                                                         | Didacticiel avancé : montre comment envoyer un message à l’aide d’un programme.                                                                                                                                                             | [Démonstration de publication par programme](https://microsoft.github.io/BotFramework-WebChat/15.c.programmatic-post-activity)                       |
| [`15.d.backchannel-send-welcome-event`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/15.d.backchannel-send-welcome-event)                                                                                 | Didacticiel avancé : montre comment envoyer un événement de bienvenue en tenant compte des fonctionnalités du client telles que la langue du navigateur.                                                                                                                        | [Démonstration d’événement de bienvenue](https://microsoft.github.io/BotFramework-WebChat/15.d.backchannel-send-welcome-event)                          |
| [`16.customization-selectable-activity`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/16.customization-selectable-activity)                                                                               | Didacticiel avancé : montre comment ajouter un comportement de clic personnalisé à chaque activité.                                                                                                                                                  | [Démonstration d’activité sélectionnable](https://microsoft.github.io/BotFramework-WebChat/16.customization-selectable-activity)                   |
| [`17.chat-send-history`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/17.chat-send-history)                                                                                                               | Didacticiel avancé : montre la capacité de sauvegarder les entrées de l’utilisateur et de lui permettre de revenir en arrière parmi les messages envoyés précédemment.                                                                                                      | [Démonstration de l’historique d’envoi Chat](https://microsoft.github.io/BotFramework-WebChat/17.chat-send-history)                                     |
| [`18.customization-open-url`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/18.customization-open-url)                                                                                                     | Didacticiel avancé : montre comment personnaliser le comportement de l’URL ouverte.                                                                                                                                                             | [Démonstration Personnaliser l’URL ouverte](https://microsoft.github.io/BotFramework-WebChat/18.customization-open-url)                               |
| [`19.a.single-sign-on-for-enterprise-apps`](https://github.com/Microsoft/BotFramework-WebChat/tree/master/samples/19.a.single-sign-on-for-enterprise-apps)                                                                         | Montre comment utiliser l'authentification unique pour les applications d'entreprise utilisant OAuth                                                                                                                                                              | [Démonstration de l’authentification unique à l’aide d’OAuth](https://microsoft.github.io/BotFramework-WebChat/19.a.single-sign-on-for-enterprise-apps)         |
 

## <a name="web-chat-api-reference"></a>Référence de l’API Web Chat

Il y existe plusieurs propriétés que vous pouvez passer dans votre composant Web Chat React (`<ReactWebChat>`) ou la méthode `renderWebChat()`. N’hésitez pas à examiner le code source, en commençant par [`packages/component/src/Composer.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Composer.js#L378). Voici une brève description des propriétés disponibles.

| Propriété                   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `activityMiddleware`       | Chaîne d’intergiciel, modélisée d'après l’[intergiciel Redux](https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6), qui permet au développeur d'ajouter de nouveaux composants DOM sur le DOM d'activités existant actuellement. La signature de l’intergiciel est la suivante : `options => next => card => children => next(card)(children)`.                                                                                                                                                                                                                                           |
| `activityRenderer`         | Version « aplatie » de `activityMiddleware`, similaire au concept Redux de [personnalisation de magasin](https://github.com/reduxjs/redux/blob/master/docs/Glossary.md#store-enhancer).                                                                                                                                                                                                                                                                                                                                                                                                                |
| `adaptiveCardHostConfig`   | Passez une configuration d'hôte Adaptive Cards personnalisée. Vérifiez la version d’Adaptive Cards utilisée avec votre configuration d'hôte. Consultez la [configuration de l’hôte personnalisé](https://github.com/microsoft/BotFramework-WebChat/issues/2034#issuecomment-501818238) pour plus d’informations.                                                                                                                                                                                                                                                                                                                                    |
| `attachmentMiddleware`     | Chaîne d’intergiciel qui permet au développeur d’ajouter ses propres éléments HTML personnalisés sur les pièces jointes. La signature est la suivante : `options => next => card => next(card)`.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `attachmentRenderer`       | Version « aplatie » de `attachmentMiddleware`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `cardActionMiddleware`     | Chaîne d’intergiciel qui permet au développeur de modifier les actions des cartes, comme Adaptive Cards ou les actions suggérées. La signature d’intergiciel est la suivante : `cardActionMiddleware: () => next => ({ cardAction, getSignInUrl }) => next(cardAction)`                                                                                                                                                                                                                                                                                                                                           |
| `createDirectLine`         | Méthode de fabrique permettant d’instancier l’objet Direct Line. Les utilisateurs Azure Government doivent utiliser `createDirectLine({ domain: 'https://directline.botframework.azure.us/v3/directline', token });` pour modifier le point de terminaison. La liste complète des paramètres sont : `conversationId`, `domain`, `fetch`, `pollingInterval`, `secret`, `streamUrl`, `token`, `watermark` `webSocket`.                                                                                                                                                                                                                         |
| `createStore`              | Chaîne d’intergiciel qui permet au développeur de modifier les actions de magasin. La signature de l’intergiciel est la suivante : `createStore: ({}, ({ dispatch }) => next => action => next(cardAction)`                                                                                                                                                                                                                                                                                                                                                                                                |
| `directLine`               | Spécifiez l’objet DirectLine avec le jeton DirectLine.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `disabled`                 | Désactivez l’interface utilisateur Web Chat (par exemple, pour le mode de présentation).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `grammars`                 | Spécifiez une liste de grammaire pour Speech (Reconnaissance vocale Bing ou services Speech de Cognitive Services).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `groupTimeStamp`           | Modifiez les paramètres par défaut pour les groupes d'horodatages.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `locale`                   | Indiquez la langue par défaut de Web Chat. Il est vivement recommandé d’utiliser un code à quatre lettres (tel que `en-US`).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `renderMarkdown`           | Modifiez l’objet Renderer Markdown par défaut.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `sendTypingIndicator`      | Affichez un signal de saisie utilisateur afin d’indiquer au bot que l’utilisateur est bien actif.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `store`                    | Spécifiez un magasin personnalisé, par exemple, pour l’ajout d’activité de programmation pour le robot.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `styleOptions`             | Objet qui stocke des valeurs de personnalisation pour l’application d’un style Web Chat. Pour obtenir la liste complète des options de style par défaut (fréquemment mise à jour), consultez le fichier [defaultStyleOptions.js](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/defaultStyleOptions.js).                                                                                                                                                                                                                                                                              |
| `styleSet`                 | Mode de substitution des styles non recommandé.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `userID`                   | Spécifiez un UserID. Il existe deux façons de spécifier le `userID` : dans les propriétés ou dans le jeton lors de la génération de l’appel de jeton (`createDirectLine()`). Si les deux méthodes sont utilisées pour spécifier le nom d’utilisateur, la propriété userID du jeton est utilisée, et un `console.warn` s’affiche pendant l’exécution. Si le `userID` est fourni par le biais de propriétés mais est préfixé avec `'dl'`, par exemple `'dl_1234'`, la valeur est émise et un nouveau `ID` généré. Si `userID` n’est pas spécifié, un ID utilisateur aléatoire est employé par défaut. Un même ID utilisateur ne doit pas être partagé par plusieurs utilisateurs, car leur statut est affiché publiquement. |
| `username`                 | Spécifiez un nom d’utilisateur.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `webSpeechPonyFillFactory` | Spécifiez l’objet Web Speech pour la synthèse vocale texte -> voix et voix -> texte.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
## <a name="browser-compatibility"></a>Compatibilité des navigateurs
Web Chat supporte les deux dernières versions des navigateurs modernes, tels que Chrome, Edge et FireFox.
Si vous avez besoin de Web Chat dans Internet Explorer 11, veuillez consulter la [démonstration de l’offre groupée ES5](https://microsoft.github.io/BotFramework-WebChat/01.b.getting-started-es5-bundle).

Notez toutefois ce qui suit :
- Web Chat ne prend pas en charge les versions d’Internet Explorer antérieures à la version 11
- La personnalisation, telle qu'indiquée dans les exemples non ES5, n’est pas prise en charge pour Internet Explorer. Comme IE11 n’est pas un navigateur récent, il ne prend pas en charge ES6, et de nombreux exemples qui utilisent des fonctions de flèches et des fonctionnalités récentes doivent être convertis manuellement en ES5.  Si vous avez besoin d’une personnalisation importante pour votre application, nous vous recommandons vivement de développer celle-ci pour un navigateur récent comme Google Chrome ou Edge.
- La prise en charge d’exemples pour IE11 (ES5) dans Web Chat n’est pas prévue.
   - Pour les clients qui souhaitent réécrire manuellement nos autres exemples en vue de les utiliser avec IE11, nous recommandons d’envisager la conversion du code ES6+ en ES5 en intégrant des polyfills et des transpilers tels que [`babel`](https://babeljs.io/docs/en/next/babel-standalone.html).

## <a name="how-to-test-with-web-chats-latest-bits"></a>Comment tester une application avec les dernières nouveautés de Web Chat

*Pour le moment, le test des fonctionnalités non encore publiées n'est disponible que par le biais du package MyGet.*

Si vous voulez tester une fonctionnalité ou une correction de bogue qui n'a pas encore été publiée, vous voudrez pointer votre package Web Chat sur le flux quotidien de Web Chat, et non pas sur le flux officiel npmjs.

Actuellement, vous pouvez accéder aux nouveautés quotidiennes de Web Chat en vous abonnant à notre flux MyGet. Pour ce faire, vous devez mettre à jour le registre dans votre projet. **Cette modification n’est pas définitive. Nos instructions expliquent comment se réabonner à la version officielle**.

### <a name="subscribe-to-latest-bits-on-mygetorg"></a>Abonnez-vous aux dernières informations disponibles sur `myget.org`

Pour ce faire, vous pouvez ajouter vos packages et modifier le registre de votre projet.

1. Ajoutez vos dépendances de projet autres que les dépendances Web Chat.
1. Dans le répertoire racine de votre projet, créez un fichier `.npmrc`
1. Ajoutez la ligne suivante à votre fichier : `registry=https://botbuilder.myget.org/F/botframework-webchat/npm/`
1. Ajoutez Web Chat à vos dépendances de projet `npm i botframework-webchat --save`
1. Notez que, dans votre `package-lock.json`, les registres désignés sont désormais des registres MyGet. Le projet Web Chat a un proxy source amont activé, ce qui permet de rediriger les packages non MyGet vers `npmjs.com`.

### <a name="re-subscribe-to-official-release-on-npmjscom"></a>Se réabonner à la version officielle sur `npmjs.com`
Le réabonnement requiert la réinitialisation de votre registre.

1. Supprimer votre `.npmrc file`
1. Supprimer votre `package-lock.json` racine
1. Supprimer votre répertoire `node_modules`
1. Réinstaller vos packages avec `npm i`
1. Notez que, dans votre `package-lock.json`, les registres pointent à nouveau vers https://npmjs.com/.


## <a name="contributing"></a>Contribution

Consultez notre [page de contribution](https://github.com/Microsoft/BotFramework-WebChat/tree/master/.github/CONTRIBUTING.md) pour plus d’informations sur la façon de générer le projet et les directives de notre référentiel pour les demandes de tirage.

Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/).
Pour plus d’informations, consultez la [FAQ sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="reporting-security-issues"></a>Signaler des problèmes de sécurité

Les bogues et problèmes de sécurité doivent être signalés en privé, par e-mail, à Microsoft Security Response Center (MSRC) à [secure@microsoft.com](mailto:secure@microsoft.com). Vous devez recevoir une réponse dans les 24 heures. Si, pour une raison quelconque vous ne le faites pas, veuillez nous adresse un e-mail de suivi pour vous assurer que nous avons bien reçu votre message original. Pour plus d’informations, notamment sur la clé [PGP MSRC](https://technet.microsoft.com/security/dn606155), reportez-vous au site web du [TechCenter sur la sécurité](https://technet.microsoft.com/security/default).

Copyright (c) Microsoft Corporation. Tous droits réservés.