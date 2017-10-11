---
title: "Приступая к работе с Azure AD версии 2 для Android. Настройка | Документация Майкрософт"
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
ms.openlocfilehash: c09937582118ebcc5b8cbc1f43a0a2019f2f7a89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации

На этом шаге вам нужно добавить идентификатор клиента в свой проект.

1.  Откройте `MainActivity` (выберите `app` > `java` > *`{host}.{namespace}`*).
2.  Замените строку, начинающуюся с `final static String CLIENT_ID`, следующей:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Откройте: `app` > `manifests` > `AndroidManifest.xml`.
4. Добавьте следующее действие в узел `manifest\application`. Так вы зарегистрируете `BrowserTabActivity`, чтобы позволить операционной системе возобновить ваше приложение после завершения аутентификации.

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>Дальнейшие действия

[Тестирование кода](active-directory-mobileanddesktopapp-android-test.md)
