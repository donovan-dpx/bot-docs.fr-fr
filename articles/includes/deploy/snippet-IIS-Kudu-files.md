---
ms.openlocfilehash: bbe74a9a82d3bd04593384d825d373bfab35e3db
ms.sourcegitcommit: 7e901f5f39a0cfb0d37e532321b90a1dcf4baadd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039783"
---
Vous devez préparer vos fichiers projet avant de pouvoir déployer votre bot. 
<!-- **C# bots** -->
##### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cmd
az bot prepare-deploy --lang Csharp --code-dir "." --proj-file-path "MyBot.csproj"
```

Vous devez fournir le chemin du fichier .csproj par rapport à --code-dir. Vous pouvez, pour cela, utiliser l’argument --proj-file-path. La commande résoudrait --code-dir et --proj-file-path en « ./MyBot.csproj ».

<!-- **JavaScript bots** -->
##### <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```cmd
az bot prepare-deploy --code-dir "." --lang Javascript
```

Cette commande récupère un fichier web.config qui est nécessaire au fonctionnement des applications Node.js avec IIS sur Azure App Services. Vérifiez que le fichier web.config est enregistré à la racine de votre bot.

<!-- **TypeScript bots** -->
##### <a name="typescripttabtypescript"></a>[TypeScript](#tab/typescript)

```cmd
az bot prepare-deploy --code-dir "." --lang Typescript
```

Cette commande fonctionne de manière similaire à la commande pour JavaScript ci-dessus, mais pour un bot Typescript.

---

> [!NOTE]
>  Pour les bots C# et les bots JavaScript, la commande `az bot prepare-depoloy` doit générer un fichier `.deployment` dans le dossier du projet de votre bot.
> Pour les bots TypeScript, la commande doit générer deux fichiers `web.config`. L’une se trouve dans votre dossier de projet et l’autre dans le dossier **src** de votre dossier de projet. 
