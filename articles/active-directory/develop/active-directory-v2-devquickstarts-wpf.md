---
title: "Собственное приложение .NET версии 2.0 для Azure Active Directory | Документация Майкрософт"
description: "Как создать собственное приложение .NET, которое поддерживает вход пользователей с помощью личной учетной записи Майкрософт, а также рабочей или учебной учетных записей."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88679e7dd71011f767cbe4de295c284516375d20
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Добавление функции входа в классическое приложение для Windows
Точка доступа версии 2.0 позволяет быстро реализовать в классических приложениях аутентификацию с поддержкой личных учетных записей Майкрософт, а также рабочих или учебных учетных записей.  Она также позволяет приложению безопасно обмениваться данными с серверным веб-API, а также [Microsoft Graph](https://graph.microsoft.io) и несколькими [унифицированными API Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory (AD) поддерживаются конечной точкой версии 2.0.  Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 
> 

Для [собственных приложений .NET, запущенных на устройстве](active-directory-v2-flows.md#mobile-and-native-apps), в Azure AD предлагается использовать библиотеку проверки подлинности Майкрософт (MSAL).  MSAL помогает приложению быстро получать маркеры доступа и вызывать веб-службы.  Чтобы показать, насколько это просто, создадим приложение To Do List (ведение списка дел) для .NET WPF, которое:

* выполняет вход пользователя и получает маркеры доступа с помощью [протокола проверки подлинности OAuth 2.0](active-directory-v2-protocols.md);
* безопасно вызывает серверную веб-службу списка дел, которая также защищена OAuth 2.0.
* обеспечивает выход пользователя из приложения.

## <a name="download-sample-code"></a>Скачивание примера кода
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) или клонировать ее:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

Готовое приложение также приводится в конце этого руководства.

## <a name="register-an-app"></a>регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md).  Не забудьте:

* Запишите назначенный вашему приложению **идентификатор приложения**. Он вскоре вам понадобится.
* Добавьте для приложения **мобильную** платформу.

## <a name="install--configure-msal"></a>Установка и настройка MSAL
Ваше приложение зарегистрировано в Майкрософт. Теперь вы можете установить MSAL и написать собственный код для работы с удостоверением.  Чтобы библиотека MSAL могла обмениваться информацией с конечной точкой версии 2.0, вам нужно предоставить определенную информацию о регистрации приложения.

* Сначала добавьте MSAL в проект TodoListClient с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* В проекте TodoListClient откройте `app.config`.  Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале регистрации приложений.  Код будет использовать эти значения при каждом обращении к библиотеке MSAL.
  
  * `ida:ClientId` — это **идентификатор приложения** , скопированный с портала.
* В проекте TodoList-Service откройте `web.config` в корневой папке проекта.  
  
  * Замените значение `ida:Audience` на **идентификатор приложения** с портала.

## <a name="use-msal-to-get-tokens"></a>Использование MSAL для получения маркеров
MSAL используется следующим образом. Каждый раз, когда приложению требуется маркер доступа, оно будет просто вызывать `app.AcquireToken(...)`, а MSAL сделает все остальное.  

* В проекте `TodoListClient` откройте `MainWindow.xaml.cs` и найдите метод `OnInitialized(...)`.  Первый шаг — инициализация в приложении `PublicClientApplication` , основного класса MSAL, представляющего собственные приложения.  На этом этапе вы отправляете в MSAL координаты, необходимые для взаимодействия с Azure AD, а также сообщаете о способе кэширования маркеров.

```csharp
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* После запуска приложения необходимо проверить, выполнил ли пользователь вход в приложение.  Однако пока нет необходимости вызывать пользовательский интерфейс входа: пользователь должен нажать кнопку "Вход".  Также в методе `OnInitialized(...)` :

```csharp
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Если пользователь не выполнил вход и нажал кнопку "Вход", необходимо вызвать пользовательский интерфейс входа и запросить ввод учетных данных.  Реализуйте обработчик кнопки "Вход".

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

* Если пользователь успешно выполняет вход, MSAL получает и кэширует маркер, а вы можете вызвать метод `GetTodoList()` .  Осталось получить задачи пользователя — реализовать метод `GetTodoList()` .

```csharp
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Выполнить
Поздравляем! Теперь у вас есть работающее приложение .NET WPF, которое может проверять подлинность пользователей и выполнять безопасные вызовы веб-API при помощи OAuth 2.0.  Запустите оба проекта и выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью.  Добавьте задачи в список дел этого пользователя.  Выйдите из системы и выполните вход от имени других пользователей, чтобы просмотреть их списки дел.  Закройте приложение и снова его запустите.  Обратите внимание, что сеанс пользователя при этом не прерывается, так как приложение кэширует маркеры в локальный файл.

MSAL позволяет легко включать в приложение общие функции идентификации, позволяя использовать личные и рабочие учетные записи.  Методы этой библиотеки выполняют всю черновую работу — управление кэшем, поддержку протокола OAuth, предоставление пользователю диалогового окна входа, обновление маркеров с истекшим сроком действия и многое другое.  Все, что вам действительно нужно знать, — это вызов интерфейса API `app.AcquireTokenAsync(...)`.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip)или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Дополнительная информация
Теперь можно перейти к более сложным темам.  Можно попробовать:

* [Защита веб-API TodoListService с помощью конечной точки версии 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Дополнительные ресурсы:  

* [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
* [Тег "msal" на StackOverflow >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

