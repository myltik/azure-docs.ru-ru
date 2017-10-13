---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Тестирование | Документация Майкрософт"
description: "Здесь описывается, как классические приложения для Windows .NET (XAML) могут вызвать API, требующий маркеры доступа от конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 972cc48057c13271d725b0c973c3ccf651ad27c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
## <a name="test-your-code"></a>Тестирование кода

Чтобы протестировать приложение, нажмите клавишу `F5` для запуска проекта в Visual Studio. Откроется главное окно.

![Пример снимка экрана](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

Когда вы будете готовы к тестированию, щелкните *Call Microsoft Graph API* (Вызвать API Microsoft Graph) и используйте Microsoft Azure Active Directory (учетная запись организации) или учетную запись Майкрософт (live.com, outlook.com) для входа. Если вход выполняется впервые, появится окно, предлагающее пользователю войти в систему:

![Вход](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>Согласие на предоставление разрешений
При первом входе в приложение появится экран согласия, как показано ниже, где необходимо явно дать согласие:

![Экран согласия](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>Ожидаемые результаты
На экране результатов вызова API должны отобразиться сведения о профиле пользователя, возвращенные вызовом API Microsoft Graph.

Вы также должны увидеть основные сведения о маркере, полученные с помощью `AcquireTokenAsync` или `AcquireTokenSilentAsync`, в соответствующем окне:

|Свойство  |Формат  |Описание |
|---------|---------|---------|
|Имя | {Полное имя пользователя} |Имя и фамилия пользователя|
|Имя пользователя |<span>user@domain.com</span> |Имя пользователя, используемое для его идентификации|
|Истечение срока действия маркера |{дата и время}         |Время окончания срока действия маркера. MSAL продлит срок действия, обновив маркер при необходимости|
|Маркер доступа |{Строка}         |Строка маркера, которая будет отправлена HTTP-запросам, требующим заголовка авторизации|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях
API Graph требуется область `user.read` для чтения профиля пользователя. По умолчанию эта область автоматически добавляется в каждое приложение, регистрируемое на портале регистрации. Некоторые другие API Graph, а также пользовательские API для вашего внутреннего сервера требуют дополнительные области. Например, для Graph требуется `Calendars.Read`, чтобы отобразить список календарей пользователя. Чтобы получить доступ к календарю пользователя в контексте приложения, необходимо добавить сведения о регистрации делегированного приложения `Calendars.Read`, а затем добавить `Calendars.Read` в вызов `AcquireTokenAsync`. При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

<!--end-collapse-->



