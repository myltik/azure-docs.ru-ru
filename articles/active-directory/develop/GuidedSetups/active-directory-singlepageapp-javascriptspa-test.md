---
title: "Интерактивная настройка Azure AD версии 2 для JS SPA. Тестирование | Документация Майкрософт"
description: "В этой статье описано, как приложения JavaScript SPA могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: e10e5bbb035878eb62d9295b91263602f8128230
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
## <a name="test-your-code"></a>Тестирование кода

> ### <a name="testing-with-visual-studio"></a>Тестирование с Visual Studio
> При использовании Visual Studio нажмите клавишу `F5` для запуска проекта. Откроется браузер и переместит вас на страницу по адресу *http://localhost:{port}*. На странице отобразится кнопка *Call Microsoft Graph API* (Вызвать Microsoft Graph API).

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Тестирование с помощью Python или другого веб-сервера
> Если вы не используете Visual Studio, убедитесь, что веб-сервер запущен и настроен для ожидания передачи данных с TCP-порта в папке с файлом *index.html*. Для Python можно запустить ожидание передачи данных на порте, выполнив команду в командной строке или в окне терминала из папки приложения:
> 
> ```bash
> python -m http.server 8080
> ```
>  Затем откройте браузер и введите *http://localhost:8080* или *http://localhost:{port}*, где *port* соответствует порту, с которого веб-сервер ожидает передачи данных. Появится содержимое страницы index.html с кнопкой *Call Microsoft Graph API* (Вызвать Microsoft Graph API).

## <a name="test-your-application"></a>Тестирование приложения

После загрузки *index.html* в браузере нажмите кнопку *Call Microsoft Graph API* (Вызвать Microsoft Graph API). Если вы делаете это впервые, браузер перенаправит вас в конечную точку Microsoft Azure Active Directory v2, где вам будет предложено войти.
 
![Пример снимка экрана](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>Согласие на предоставление разрешений
При первом входе в приложение появится экран согласия, как показано ниже, где необходимо дать согласие:

 ![Экран согласия](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>Ожидаемые результаты
Сведения о профиле пользователя должны отобразиться при ответе на вызов API Microsoft Graph.
 
 ![Результаты](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

Кроме того, должны отобразиться основные сведения о маркере, полученные с помощью полей *Маркер доступа* и *ID Token Claims* (Утверждения маркера идентификатора).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область `user.read`. По умолчанию эта область автоматически добавляется в каждое приложение, регистрируемое на портале регистрации. Для некоторых других API Microsoft Graph, а также пользовательских API для вашего внутреннего сервера могут потребоваться дополнительные области. Например, для Microsoft Graph требуется область `Calendars.Read`, чтобы отобразить список календарей пользователя. Чтобы получить доступ к календарю пользователя в контексте приложения, в сведения о регистрации приложения необходимо добавить делегированное разрешение `Calendars.Read`, а затем добавить область `Calendars.Read` в вызов `acquireTokenSilent`. При увеличении количества областей от пользователя может потребоваться дополнительное согласие.

Если для API серверной части не требуется область (не рекомендуется), то вы можете использовать `clientId` в качестве области в вызове `acquireTokenSilent` и (или) вызовах `acquireTokenRedirect`.

<!--end-collapse-->

[!INCLUDE  [Help and Support Options](../../../../includes/active-directory-develop-help-support-include.md)]
