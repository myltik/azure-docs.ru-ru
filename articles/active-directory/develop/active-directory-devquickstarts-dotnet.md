---
title: Начало работы с Azure AD для классических приложений .NET (WPF) | Документация Майкрософт
description: Практическое руководство по созданию классического приложения .NET Windows, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1097d4b1f7d8c4feae500bf46b40e61029781d68
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156268"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Начало работы с Azure AD для классических приложений .NET (WPF)
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

При разработке классического приложения служба Microsoft Azure Active Directory позволяет разработчику упростить проверку подлинности учетных записей пользователей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Клиентские нативные приложения для .NET, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить для разработчика процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (ведение списка дел) для .NET WPF, которое:

* Получает маркеры доступа для вызова интерфейса Graph API Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Осуществляет поиск пользователей в каталоге по псевдониму.
* Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо:

1. Зарегистрировать приложение в Azure AD.
2. установить и настроить ADAL;
3. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [скачайте схему приложения](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) или [скачайте готовый пример](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Вам также потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Регистрация приложения DirectorySearcher
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD:

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В меню навигации слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
5. Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**.
  * **Имя** приложения отображает его описание конечным пользователям.
  * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых маркеров, запрошенных приложением. Укажите значение, специфичное для вашего приложения, например `http://DirectorySearcher`.
6. После завершения регистрации служба Azure AD присваивает приложению уникальный идентификатор приложения. Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.
7. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**. Выберите **Microsoft Graph** в качестве интерфейса API и добавьте разрешение **Чтение данных каталога** в списке **Делегированные разрешения**. Это позволит приложению запрашивать интерфейс Graph API для пользователей.

## <a name="2-install--configure-adal"></a>2. Установка и настройка ADAL
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.

* Сначала добавьте ADAL в проект DirectorySearcher с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* В проекте DirectorySearcher откройте `app.config`. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале Azure. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
  * `ida:Tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
  * `ida:ClientId` — это идентификатор clientId приложения, скопированный с портала.
  * `ida:RedirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Использование библиотеки ADAL для получения маркеров из AAD
Основной принцип ADAL состоит в следующем: каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать `authContext.AcquireTokenAsync(...)`, а ADAL сделает все остальное. 

* В проекте `DirectorySearcher` откройте `MainWindow.xaml.cs` и найдите метод `MainWindow()`. Первый шаг состоит в инициализации `AuthenticationContext` — основного класса ADAL. Здесь вы отправляете в библиотеку ADAL координаты, которые ей требуются для взаимодействия с Azure AD, и сообщаете о способе кэширования маркеров.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Теперь найдите метод `Search(...)` , который будет вызываться при нажатии кнопки "Поиск" в пользовательском интерфейсе приложения. Этот метод выполняет запрос GET в интерфейс Graph API службы Azure AD для запроса списка пользователей, чьи UPN начинаются с данного слова поиска. Но для отправки запросов в Graph API необходимо включить access_token в заголовок `Authorization` запроса — именно отсюда ADAL начинает свою работу.

```csharp
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Когда приложение запрашивает маркер путем вызова `AcquireTokenAsync(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя. Если ADAL решит, что пользователь должен войти в систему для получения маркера, то служба отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной проверки подлинности. Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.
* Обратите внимание, что объект `AuthenticationResult` содержит объект `UserInfo`, который может использоваться для сбора сведений, необходимых приложению. В DirectorySearcher объект `UserInfo` используется для настройки пользовательского интерфейса приложения на основе идентификатора пользователя.
* Когда пользователь нажимает кнопку выхода, необходимо, чтобы при следующем вызове `AcquireTokenAsync(...)` пользователю было предложено войти. С ADAL это так же просто, как и очистка кэша маркера:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Если пользователь не нажимает кнопку выхода, то можно реализовать восстановление данных сеанса пользователя при следующем запуске приложения DirectorySearcher. При запуске приложения можно проверить кэш маркеров библиотеки ADAL на наличие соответствующего маркера и соответственно обновить пользовательский интерфейс. В методе `CheckForCachedToken()` выполните другой вызов `AcquireTokenAsync(...)`, на этот раз передав параметр `PromptBehavior.Never`. `PromptBehavior.Never` сообщает ADAL, что пользователю не следует предлагать войти, и вместо этого библиотека ADAL должна породить исключение, если не удается вернуть маркер.

```csharp
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Поздравляем! Теперь у нас есть рабочее приложение .NET WPF, которое позволяет проверять подлинность пользователей, безопасно вызывать методы веб-интерфейсов API по протоколу OAuth 2.0 и получать основные сведения о пользователе. Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей. Запустите приложение DirectorySearcher и войдите как один из этих пользователей. Осуществите поиск других пользователей по их имени участника-пользователя. Закройте приложение и снова запустите его. Обратите внимание на то, что пользовательский сеанс остался без изменений. Выйдите и снова войдите от имени другого пользователя.

Библиотека ADAL упрощает включение в приложение всех этих типичных функций работы с удостоверением. Методы этой библиотеки выполняют всю черновую работу — управление кэшем, поддержку протокола OAuth, предоставление пользователю диалогового окна входа, обновление маркеров с истекшим сроком действия и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireTokenAsync(...)`.

Завершенный образец (без ваших настроек) вы можете загрузить [отсюда](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Теперь можно приступить к изучению других сценариев. Можно попробовать:

[Защита веб-API с помощью Azure AD для .NET >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

