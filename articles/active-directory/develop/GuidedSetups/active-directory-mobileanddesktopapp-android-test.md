---
title: "Приступая к работе с Azure AD версии 2 для Android. Тестирование | Документация Майкрософт"
description: "Получение маркера доступа для приложения Android и вызов API Microsoft Graph или API, которые требуют маркер доступа, из конечной точки Azure Active Directory версии 2."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 6df64f4820f8409bd8897d5ac24f81bffeeef102
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017

---
## <a name="test-your-code"></a>Тестирование кода

1. Разверните код на своем устройстве или эмуляторе.
2. Когда вы будете готовы к тестированию, используйте Microsoft Azure Active Directory (учетная запись организации) или учетную запись Майкрософт (live.com, outlook.com) для входа. 

![Пример снимка экрана](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![Вход](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>Согласие на предоставление разрешений
При первом входе пользователя в приложение появится экран согласия, как показано ниже, где необходимо явно дать согласие: 

![Согласие на предоставление разрешений](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>Ожидаемые результаты
Вы увидите результаты вызова конечной точки "me" API Microsoft Graph, используемой для получения профиля пользователя — https://graph.microsoft.com/v1.0/me. Список распространенных конечных точек Microsoft Graph см. в [этой статье](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область `user.read`. По умолчанию эта область автоматически добавляется в каждое приложение, регистрируемое на портале регистрации. Для некоторых других API Microsoft Graph, а также пользовательских API для вашего внутреннего сервера могут потребоваться дополнительные области. Например, для Microsoft Graph требуется область `Calendars.Read`, чтобы отобразить список календарей пользователя. Чтобы получить доступ к календарю пользователя в контексте приложения, в сведения о регистрации приложения необходимо добавить делегированное разрешение `Calendars.Read`, а затем добавить область `Calendars.Read` в вызов `acquireTokenSilentAsync`. При увеличении количества областей от пользователя может потребоваться дополнительное согласие.

<!--end-collapse-->

