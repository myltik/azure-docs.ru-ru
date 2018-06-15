---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 794fd51c38f66b24193c7da7a145d58f7a225b30
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202542"
---
## <a name="setting-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

> Хотите скачать этот пример проекта вместо указанного выше проекта? 
> - [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> или
> - [Скачайте файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) для локального веб-сервера, например Python.
>
> Затем перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>предварительным требованиям
Для выполнения этой пошаговой установки требуется локальный веб-сервер, такой как [http.server Python](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core) или интеграция IIS Express с [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Инструкции в этом руководстве основаны на Python и Visual Studio 2017, но вы можете использовать любую другую среду разработки или веб-сервер.

## <a name="create-your-project"></a>Создание проекта 

> ### <a name="option-1-visual-studio"></a>Вариант 1. Visual Studio 
> Если вы создаете новый проект, используя Visual Studio, то выполните следующие действия, чтобы создать новое решение Visual Studio:
> 1.    В Visual Studio: `File` > `New` > `Project`.
> 2.    В разделе `Visual C#\Web` выберите `ASP.NET Web Application (.NET Framework)`.
> 3.    Присвойте имя приложению и нажмите кнопку *ОК*.
> 4.    В разделе `New ASP.NET Web Application` выберите `Empty`.

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Вариант 2. Python или другие веб-серверы
> Убедитесь, что вы установили [Python](https://www.python.org/downloads/), а затем выполните следующие шаги:
> - Создайте папку для размещения приложения.


## <a name="create-your-single-page-applications-ui"></a>Создание пользовательского интерфейса одностраничного приложения
1.  Создайте файл *index.html* для своего одностраничного приложения JavaScript. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите `Add` > `New Item` > `HTML page`. Присвойте странице имя index.html.
2.  Добавьте в страницу следующий код:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
