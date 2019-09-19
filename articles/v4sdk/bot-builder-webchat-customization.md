---
title: Personnalisation de Web Chat | Microsoft Docs
description: Découvrez comment configurer le Web Chat Bot Framework.
keywords: bot framework, conversation web, conversation, exemples, react, référence
author: ivorb
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/07/2019
ms.openlocfilehash: 9310bdf8cab253cd379b7b49daa18deed79f27ae
ms.sourcegitcommit: 96d26f6b0576747fa62767710af922ace0ff0e3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71060342"
---
# <a name="web-chat-customization"></a>Personnalisation de Web Chat

Cet article explique comment adapter les exemples Web Chat à votre bot.

## <a name="integrate-web-chat-into-your-website"></a>Intégrer Web Chat à votre site web

Suivez les instructions de la [page de présentation](bot-builder-webchat-overview.md) pour intégrer le contrôle Web Chat à votre site web.

## <a name="customizing-styles"></a>Personnalisation des styles

La dernière version du contrôle Web Chat fournit des options de personnalisation enrichies : vous pouvez modifier les couleurs, les tailles, le positionnement des éléments, ajouter des éléments personnalisés et interagir avec la page web hôte. Voici plusieurs exemples montrant comment personnaliser les éléments de l’interface utilisateur Web Chat.

Le [fichier `defaultStyleOptions.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/defaultStyleOptions.js) inclut la liste complète de tous les paramètres que vous pouvez facilement modifier dans Web Chat.

Ces paramètres vont permettre de générer un _ensemble de styles_, qui est un ensemble de règles CSS amélioré avec [glamor](https://github.com/threepointone/glamor). Vous trouverez la liste complète des styles CSS générés dans l’ensemble de styles dans le [fichier`createStyleSet.js`](https://github.com/Microsoft/BotFramework-WebChat/blob/master/packages/component/src/Styles/createStyleSet.js).

## <a name="set-the-size-of-the-web-chat-container"></a>Définir la taille du conteneur Web Chat

Il est à présent possible d’ajuster la taille du conteneur Web Chat à l’aide de `styleSetOptions`. L’exemple suivant comprend un `body` de couleur d’arrière-plan de `paleturquoise` pour afficher le conteneur Web Chat (section avec un arrière-plan blanc).

```js
…
<head>
  <style>
    html, body { height: 100% }
    body {
      margin: 0;
      background-color: paleturquoise;
    }

    #webchat {
      height: 100%;
      width: 100%;
    }
  </style>
</head>
<body>
  <div id="webchat" role="main"></div>
  <script>
    (async function () {
    window.WebChat.renderWebChat({
      directLine: window.WebChat.createDirectLine({ token }),
      styleOptions: {
        rootHeight: '100%',
        rootWidth: '50%'
      }
    }, document.getElementById('webchat'));
    })()
  </script>
…
```

Voici le résultat :

<img alt="Web Chat with root height and root width set" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/rootHeightWidth.png" width="600"/>

## <a name="change-font-or-color"></a>Modifier la police ou la couleur

Au lieu d’utiliser la couleur d’arrière-plan par défaut et les polices utilisées dans les bulles du chat, vous pouvez personnaliser ces éléments en fonction du style de la page web cible. L’extrait de code ci-dessous vous permet de modifier la couleur d’arrière-plan des messages de l’utilisateur et du bot.

<img alt="Screenshot with custom style options" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-style-options.png" width="396" />

Si vous devez définir des styles simples, vous pouvez le faire via `styleOptions`. Les options de style sont des ensembles de styles prédéfinis que vous pouvez modifier directement, et Web Chat calcule la feuille de style entière en fonction de ces options.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         const styleOptions = {
            bubbleBackground: 'rgba(0, 0, 255, .1)',
            bubbleFromUserBackground: 'rgba(0, 255, 0, .1)'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),

               // Passing 'styleOptions' when rendering Web Chat
               styleOptions
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

## <a name="change-the-css-manually"></a>Modifier la feuille CSS manuellement

En plus des couleurs, vous pouvez modifier les polices utilisées pour afficher des messages :

<img alt="Screenshot with custom style set" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-style-set.png" width="396" />

Pour définir un style plus approfondi, vous pouvez également modifier le style défini manuellement en définissant les règles CSS directement.

> Étant donné que les règles CSS sont étroitement associées à la structure de l’arborescence DOM, il est possible que ces règles doivent être mises à jour pour fonctionner avec la version la plus récente de Web Chat.

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         // "styleSet" is a set of CSS rules which are generated from "styleOptions"
         const styleSet = window.WebChat.createStyleSet({
            bubbleBackground: 'rgba(0, 0, 255, .1)',
            bubbleFromUserBackground: 'rgba(0, 255, 0, .1)'
         });

         // After generated, you can modify the CSS rules
         styleSet.textContent = {
            ...styleSet.textContent,
            fontFamily: "'Comic Sans MS', 'Arial', sans-serif",
            fontWeight: 'bold'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),

               // Passing 'styleSet' when rendering Web Chat
               styleSet
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

## <a name="change-the-avatar-of-the-bot-within-the-dialog-box"></a>Modifier l’avatar du bot au sein de la boîte de dialogue

La dernière version de Web Chat prend en charge les avatars, que vous pouvez personnaliser en définissant `botAvatarInitials` et `userAvatarInitials` dans la propriété `styleOptions`.

<img alt="Screenshot with avatar initials" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-avatar-initials.png" width="396" />

```html
<!DOCTYPE html>
<html>
   <body>
      <div id="webchat" role="main"></div>
      <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
      <script>
         const styleOptions = {
            botAvatarInitials: 'BF',
            userAvatarInitials: 'WC'
         };

         window.WebChat.renderWebChat(
            {
               directLine: window.WebChat.createDirectLine({
                  secret: 'YOUR_BOT_SECRET'
               }),
               styleOptions
            },
            document.getElementById('webchat')
         );
      </script>
   </body>
</html>
```

Dans la propriété `styleOptions` de Web Chat, nous avons ajouté `botAvatarInitials` et `userAvatarInitials` :

```js
botAvatarInitials: 'BF',
userAvatarInitials: 'WC'
```

`botAvatarInitials` définit le texte à l’intérieur de l’avatar sur le côté gauche. S’il est défini sur une valeur erronée, l’avatar côté bot est masqué. En revanche, `userAvatarInitials` définit le texte de l’avatar sur le côté droit.

## <a name="custom-rendering-activity-or-attachment"></a>Rendu personnalisé d’activités ou de pièces jointes

Avec la dernière version de Web Chat, vous pouvez également restituer les activités ou pièces jointes que Web Chat ne prend pas en charge de manière native. Le rendu des activités et des pièces jointes est envoyé via un pipeline personnalisable modélisé d’après le [middleware Redux](https://redux.js.org/api/applymiddleware). Le pipeline est suffisamment flexible pour que vous puissiez facilement effectuer les tâches suivantes :

-  Décorer les activités et pièces jointes existantes
-  Ajouter de nouvelles activités et pièces jointes
-  Remplacer les activités et pièces jointes existantes (ou les supprimer)
-  Connecter les middleware ensemble

### <a name="show-github-repository-as-an-attachment"></a>Afficher le dépôt GitHub en tant que pièce jointe

Si vous souhaitez afficher un jeu de cartes de dépôt GitHub, vous pouvez créer un composant React pour le dépôt GitHub et l’ajouter en tant que middleware pour la pièce jointe.

<img alt="Screenshot with custom GitHub repository attachment" src="https://raw.githubusercontent.com/Microsoft/BotFramework-WebChat/master/media/sample-custom-github-repository-attachment.png" width="396" />

```jsx
import ReactWebChat from 'botframework-webchat';
import ReactDOM from 'react-dom';

// Create a new React component that accept render a GitHub repository attachment
const GitHubRepositoryAttachment = props => (
   <div
      style={{
         fontFamily: "'Calibri', 'Helvetica Neue', Arial, sans-serif",
         margin: 20,
         textAlign: 'center'
      }}
   >
      <svg
         height="64"
         viewBox="0 0 16 16"
         version="1.1"
         width="64"
         aria-hidden="true"
      >
         <path
            fillRule="evenodd"
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
         />
      </svg>
      <p>
         <a
            href={`https://github.com/${encodeURI(props.owner)}/${encodeURI(
               props.repo
            )}`}
            target="_blank"
         >
            {props.owner}/<br />
            {props.repo}
         </a>
      </p>
   </div>
);

// Creating a new middleware pipeline that will render <GitHubRepositoryAttachment> for specific type of attachment
const attachmentMiddleware = () => next => card => {
   switch (card.attachment.contentType) {
      case 'application/vnd.microsoft.botframework.samples.github-repository':
         return (
            <GitHubRepositoryAttachment
               owner={card.attachment.content.owner}
               repo={card.attachment.content.repo}
            />
         );

      default:
         return next(card);
   }
};

ReactDOM.render(
   <ReactWebChat
      // Prepending the new middleware pipeline
      attachmentMiddleware={attachmentMiddleware}
      directLine={window.WebChat.createDirectLine({ token })}
   />,
   document.getElementById('webchat')
);
```

Vous trouverez l’exemple complet dans [l’exemple de composants de carte de personnalisation](https://github.com/microsoft/BotFramework-WebChat/tree/master/samples/10.a.customization-card-components).

Dans cet exemple, nous ajoutons un nouveau composant React appelé `GitHubRepositoryAttachment` :

```jsx
const GitHubRepositoryAttachment = props => (
   <div
      style={{
         fontFamily: "'Calibri', 'Helvetica Neue', Arial, sans-serif",
         margin: 20,
         textAlign: 'center'
      }}
   >
      <svg
         height="64"
         viewBox="0 0 16 16"
         version="1.1"
         width="64"
         aria-hidden="true"
      >
         <path
            fillRule="evenodd"
            d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
         />
      </svg>
      <p>
         <a
            href={`https://github.com/${encodeURI(props.owner)}/${encodeURI(
               props.repo
            )}`}
            target="_blank"
         >
            {props.owner}/<br />
            {props.repo}
         </a>
      </p>
   </div>
);
```

Ensuite, nous créons un middleware qui affiche le nouveau composant React lorsque le bot envoie une pièce jointe du type de contenu `application/vnd.microsoft.botframework.samples.github-repository`. Sinon, il continue sur le middleware en appelant `next(card)`.

```jsx
const attachmentMiddleware = () => next => card => {
   switch (card.attachment.contentType) {
      case 'application/vnd.microsoft.botframework.samples.github-repository':
         return (
            <GitHubRepositoryAttachment
               owner={card.attachment.content.owner}
               repo={card.attachment.content.repo}
            />
         );

      default:
         return next(card);
   }
};
```

L’activité envoyée à partir du bot ressemble à ceci :

```json
{
   "type": "message",
   "from": {
      "role": "bot"
   },
   "attachmentLayout": "carousel",
   "attachments": [
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-WebChat"
         }
      },
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-Emulator"
         }
      },
      {
         "contentType": "application/vnd.microsoft.botframework.samples.github-repository",
         "content": {
            "owner": "Microsoft",
            "repo": "BotFramework-DirectLineJS"
         }
      }
   ]
}
```
