---
title: "Azure Active Directory B2C | Документация Майкрософт"
description: "Инструкции по созданию приложений Windows, позволяющих пользователям выполнять вход, регистрироваться и управлять профилем, с помощью Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e2b5c704230ee2ba1395dc76a1551aaa8e7af7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: создание классического приложения Windows
Azure Active Directory (Azure AD) B2C позволяет добавлять в приложения мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье описывается, как создать приложение .NET WPF "Список дел", которое предусматривает регистрацию и вход пользователя, а также управление профилем. Приложение будет поддерживать регистрацию и вход в систему по имени пользователя или адресу электронной почты, а также по учетной записи в социальной сети, такой как Facebook или Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент.  Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md), если вы его еще не создали.

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md).  Не забудьте сделать следующее.

* Включите в приложение **собственный клиент** .
* Скопируйте **URI перенаправления** `urn:ietf:wg:oauth:2.0:oob`. Это URL-адрес по умолчанию для данного примера кода.
* Скопируйте **идентификатор приложения** , назначенный приложению. Оно понадобится вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам необходимо создать по одной политике для каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). При создании трех необходимых политик обязательно сделайте следующее:

* В колонке поставщиков удостоверений выберите **User ID sign-up** (Регистрация с помощью идентификатора пользователя) или **Email sign-up** (Регистрация по электронной почте).
* В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
* В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой созданной политики. У него должен быть префикс `b2c_1_`.  Эти имена политик понадобятся вам через некоторое время.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

## <a name="download-the-code"></a>Загрузка кода
Код для этого руководства размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Проект `TaskClient` — это классическое приложение WPF, с которым взаимодействует пользователь. В рамках этого руководства оно вызывает веб-API задачи серверной части (размещено в Azure) со списком дел для каждого пользователя.  Вам не нужно создавать веб-API — мы предлагаем готовое решение.

Сведения о том, как веб-API надежно выполняет проверку подлинности запросов с помощью Azure AD B2C, см. в [статье о начале работы с веб-API](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Выполнение политик
Приложение взаимодействует с Azure AD B2C, отправляя сообщения проверки подлинности. В этих сообщениях указана политика, которую необходимо выполнить как часть HTTP-запроса. Для классических приложений .NET можно использовать библиотеку проверки подлинности Майкрософт (MSAL), чтобы отправлять сообщения проверки подлинности OAuth 2.0, выполнять политики и получать маркеры для вызова веб-API.

### <a name="install-msal"></a>Установка MSAL
Добавьте MSAL в проект `TaskClient` с помощью консоли диспетчера пакетов Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Ввод данных B2C
Откройте файл `Globals.cs` и замените все значения свойств собственными. Этот класс применяется для создания ссылки на часто используемые значения во всем проекте `TaskClient` .

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Создание PublicClientApplication
Основной класс MSAL — это `PublicClientApplication`. Этот класс представляет приложение в системе Azure AD B2C. При запуске приложения создайте экземпляр `PublicClientApplication` в `MainWindow.xaml.cs`. Его можно использовать для выполнения всех действий в этом окне.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Запуск потока регистрации
Нам нужно, чтобы, когда пользователь нажимал кнопку регистрации, инициировался созданный нами поток регистрации. Для этого требуется только вызов метода `pca.AcquireTokenAsync(...)`с помощью MSAL. Параметры, передаваемые в метод `AcquireTokenAsync(...)` , определяют, какой маркер вы получите, какая политика будет использована в запросе проверки подлинности, а также другие данные.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Инициация потока входа
Поток входа инициируется таким же образом, как и поток регистрации. При входе пользователя будет выполнен тот же вызов в MSAL, но на этот раз с использованием политики входа:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Инициирование потока изменения профиля
Политика редактирования профиля реализуется аналогично.

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Во всех этих случаях MSAL либо возвращает маркер в `AuthenticationResult` , либо выдает исключение. Если MSAL выдает маркер, для обновления данных пользователя в приложении (например, пользовательского интерфейса) можно использовать объект `AuthenticationResult.User` . Кроме того, ADAL кэширует маркер для использования в других частях приложения.

### <a name="check-for-tokens-on-app-start"></a>Проверка наличия маркеров при запуске приложения
MSAL также можно использовать, чтобы отслеживать состояние входа пользователя.  В этом приложении мы хотим, чтобы пользователь оставался в системе даже после закрытия и повторного открытия приложения.  В переопределении `OnInitialized` используйте метод `AcquireTokenSilent` MSAL, чтобы проверить наличие кэшированных маркеров:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Вызов API задачи
С помощью MSAL вы выполнили политики и получили маркеры.  Если вы хотите использовать один из этих маркеров для вызова API задачи, можно снова использовать метод `AcquireTokenSilent` MSAL для проверки наличия кэшированных маркеров:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Если вызов `AcquireTokenSilentAsync(...)` завершается успешно, а в кэше есть маркер, можно добавить этот маркер в заголовок `Authorization` HTTP-запроса. Веб-API задачи будет использовать этот заголовок для проверки подлинности запроса на чтение списка дел пользователя:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Выход пользователя
Также MSAL можно использовать для завершения сеанса пользователя в приложении, когда пользователь нажимает кнопку **Выход**.  Для этого нужно удалить все маркеры из соответствующего кэша с помощью MSAL:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения
Теперь можно собрать и запустить пример.  Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите и снова войдите под именем того же пользователя. Измените профиль пользователя. Выйдите и зарегистрируйтесь от имени другого пользователя.

## <a name="add-social-idps"></a>Добавление поставщиков удостоверений социальных сетей
Сейчас приложение поддерживает регистрацию и вход пользователей только с использованием **локальных учетных записей**. Учетные записи хранятся в каталоге B2C, где применяется имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других поставщиков удостоверений (IDP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

* [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
* [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
* [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
* [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

Добавив поставщики удостоверений в каталог B2C, внесите соответствующие изменения в три политики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение. Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой MSAL.

Готовый пример [предоставляется в виде ZIP-файла](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
