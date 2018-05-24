---
title: Приступая к работе с Azure AD для Android | Документы Майкрософт
description: Практическое руководство по созданию приложения для Android, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, с помощью протокола OAuth2.0.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155700"
---
# <a name="azure-ad-android-getting-started"></a>Начало работы с Azure AD для Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Корпорация Майкрософт предлагает простые и понятные средства регистрации пользователей Azure Active Directory (Azure AD) для разработчиков приложений для Android. Azure AD позволяет вашему приложению обращаться к данным пользователя через Microsoft Graph или с помощью собственного защищенного веб-API. 

Библиотека проверки подлинности Azure AD (ADAL) для Android позволяет вашему приложению использовать [Microsoft Azure Cloud](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io) благодаря поддержке [учетных записей Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) с использованием отраслевых стандартов OAuth2 и OpenID Connect. В этом примере показан весь обычный жизненный цикл приложения, включая следующие действия:

* Получение маркера для Microsoft Graph
* Обновление маркера
* Вызов Microsoft Graph
* Выход пользователя из системы

Чтобы приступить к работе, вам потребуется клиент Azure AD, в котором вы сможете создать пользователей и зарегистрировать приложение. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Сценарий: вход пользователей и вызов Microsoft Graph

![Топология](./media/active-directory-android-topology.png)

Это приложение можно использовать для всех учетных записей Azure AD. Оно поддерживает сценарии как с одной, так и с несколькими организациями (как описано ниже). Этот сценарий показывает, как разработчик может создавать приложения для подключения к корпоративным пользователям и доступа к их данным в Azure и Office 365 с помощью Microsoft Graph. При проверке подлинности конечным пользователям потребуется войти и подтвердить разрешения для приложения, в некоторых случаях разрешения должен подтвердить системный администратор. Большая часть логики в этом примере иллюстрирует проверку подлинности конечного пользователя и простой вызов Microsoft Graph.

## <a name="example-code"></a>Пример кода

Полный исходный код примера можно найти на сайте [GitHub](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Действия для запуска

### <a name="register-and-configure-your-app"></a>Регистрация и настройка приложения 
Вам потребуется собственное клиентское приложение, зарегистрированное в Майкрософт с помощью [портала Azure](https://portal.azure.com). 

1. Регистрация приложения
    - Перейдите на [портал Azure](https://aad.portal.azure.com). 
    - Выберите ***Azure Active Directory*** > ***Регистрация приложений***. 

2. Создание приложения
    - Выберите **Регистрация нового приложения**. 
    - Введите имя приложения в поле **Имя**. 
    - В поле **Тип приложения** выберите **Собственное**. 
    - В поле **URI перенаправления** введите `http://localhost`. 

3. Настройка Microsoft Graph
    - Выберите **"Параметры" > "Необходимые разрешения"**.
    - Выберите **Добавить**, затем в разделе **Выберите API** укажите ***Microsoft Graph***. 
    - Выберите разрешение **Вход и чтение профиля пользователя**, затем нажмите кнопку **Выбрать**, чтобы сохранить разрешение. 
        - Это разрешение будет сопоставлено с областью `User.Read`. 
    - Необязательно: в разделе **"Требуемые разрешения" > "Microsoft Azure Active Directory"** снимите выбранное разрешение **Вход и чтение профиля пользователя**. Это позволит избежать того, чтобы разрешение на странице согласия пользователя отображалось два раза. 

4. Поздравляем! Ваше приложение настроено. В следующем разделе вам потребуется:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Получение кода примера

1. Скопируйте код.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Откройте пример в Android Studio.
    - Выберите **Открыть существующий проект Android Studio**.

### <a name="configure-your-code"></a>Настройка кода

Все настройки для этого примера кода можно найти в файле ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***. 

1. Замените константу `CLIENT_ID` на `ApplicationID`.
2. Замените константу `REDIRECT URI` на указанное ранее значение `Redirect URI` (`http://localhost`). 

### <a name="run-the-sample"></a>Запуск примера

1. Выберите **"Сборка" > "Очистить проект"**. 
2. Выберите **"Запуск" > "Запустить приложение"**. 
3. Будет выполнена сборка приложения. После этого откроется приложение с базовым пользовательским интерфейсом. При нажатии кнопки `Call Graph API` будет отправлен запрос на вход, а затем выполнен вызов API Microsoft Graph с новым маркером. 

## <a name="important-info"></a>Важно!

1. Дополнительные сведения о механизме работы библиотеки и настройке новых сценариев и возможностей см. на [вике-сайте ADAL для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 
2. В сценариях с машинным кодом приложение будет использовать встроенное веб-представление и не выйдет за пределы приложения. `Redirect URI` может быть произвольным. 
3. Возникли проблемы или вопросы? Сообщите о проблеме или разместите свой вопрос на сайте Stackoverflow с тегом `azure-active-directory`. 

### <a name="cross-app-sso"></a>Единый вход для нескольких приложений
Узнайте, [как включить единый вход для нескольких приложений Android с помощью ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Данные телеметрии для проверки подлинности
Библиотека ADAL предоставляет данные телеметрии для проверки подлинности, с помощью которых разработчики приложений могут понять, как работают приложения и как улучшить их работу. Это позволит вам определять успешные события входа, активных пользователей и другие важные сведения. Для использования данных телеметрии для проверки подлинности разработчики приложений должны создать службу телеметрии, которая будет собирать и хранить события.

Дополнительные сведения о данных телеметрии для проверки подлинности см. в разделе [Данные телеметрии для проверки подлинности в библиотеке ADAL для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
