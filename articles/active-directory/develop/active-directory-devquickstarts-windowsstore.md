---
title: "Azure AD универсальной платформы Windows (UWP и XAML) Приступая к работе | Документы Microsoft"
description: "Создание приложений Магазина Windows, которые интегрируются с Azure AD для входа в систему и вызывают интерфейсы API, защищенные Azure AD, с помощью OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2282a59c9dd5d5d76a5b3e19f602e9d3dcc0b4ef
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-windows-universal-platform-uwpxaml-getting-started"></a>Azure AD универсальной платформы Windows (UWP и XAML) Приступая к работе
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Проекты для Магазина Windows 8.1 и более ранних версий не поддерживаются в Visual Studio 2017.  Дополнительные сведения см. в статье [Целевая платформа и совместимость для Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

При разработке приложений для Магазина Windows система Azure Active Directory (Azure AD) позволяет легко и просто осуществлять аутентификацию пользователей с помощью их учетных записей в Active Directory. Благодаря интеграции с Azure AD приложение может безопасно использовать любые веб-API, защищаемые с помощью Azure AD, например интерфейсы API Office 365 или Azure.

Классическим приложениям для Магазина Windows, которым необходим доступ к защищенным ресурсам, Azure AD предоставляет библиотеку аутентификации Active Directory (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. В этой статье показано, насколько это легко, на примере создания приложения DirectorySearcher для Магазина Windows, которое обладает следующими преимуществами:

* получает маркеры доступа для вызова интерфейса API Graph Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx);
* осуществляет поиск пользователей в каталоге с помощью заданного имени участника-пользователя (UPN);
* Обеспечивает функцию выхода пользователя из приложения.

## <a name="before-you-get-started"></a>Необходимые условия
* Скачайте [схему проекта](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Каждая из этих загрузок является решением Visual Studio 2015.
* Вам также необходим клиент Azure AD для создания пользователей и регистрации приложения. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

Когда будете готовы, выполните процедуры, описанные в следующих трех разделах.

## <a name="step-1-register-the-directorysearcher-app"></a>Шаг 1. Регистрация приложения DirectorySearcher
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу API Graph для Azure AD. Этот процесс описывается далее.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. Затем в списке **Каталог** выберите клиент Active Directory для регистрации приложения.
3. В области слева щелкните **Больше служб** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Следуя инструкциям, создайте **собственное клиентское приложение**.
  * **Имя** — это описание приложения для пользователей.
  * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения ответов маркеров. Введите значение заполнителя (например, **http://DirectorySearcher**). Вы замените это значение позже.
6. После завершения регистрации Azure AD присваивает приложению уникальный идентификатор. Скопируйте значение на вкладке **Приложение**. Оно потребуется вам позже.
7. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.
8. Для приложения **Azure Active Directory** в качестве API выберите **Microsoft Graph**.
9. В разделе **Делегированные разрешения** добавьте разрешение **Access the directory as the signed-in user** (Доступ к каталогу от имени пользователя, выполнившего вход). Это действие позволяет приложению отправлять запросы в API Graph для пользователей.

## <a name="step-2-install-and-configure-adal"></a>Шаг 2. Установка и настройка ADAL
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы обеспечить взаимодействие библиотеки ADAL с Azure AD, необходимо указать определенные сведения о регистрации приложения.

1. Добавьте ADAL в проект DirectorySearcher с помощью консоли диспетчера пакетов.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. В проекте DirectorySearcher откройте файл MainPage.xaml.cs.
3. Замените значения в разделе **Config Values** (Значения конфигурации) значениями, введенными на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
  * *tenant* — это домен вашего клиента Azure AD (например, contoso.onmicrosoft.com).
  * *clientId* — идентификатор клиента приложения, скопированный с портала.
4. Теперь необходимо обнаружить URI обратного вызова для вашего приложения Магазина Windows. Установите точку останова в этой строке в методе `MainPage` :
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Выполните сборку решения, убедившись, что все ссылки на пакеты восстановлены. Если какие-то пакеты отсутствуют, то откройте диспетчер пакетов NuGet и восстановите их.
6. Запустите приложение и скопируйте значение `redirectUri` при попадании в точку останова. Это значение выглядит примерно так:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Вернитесь на вкладку **Параметры** приложения на портале Azure и добавьте вышеупомянутое значение **RedirectUri**.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Шаг 3. Использование ADAL для получения маркеров из Azure AD
Основной принцип ADAL состоит в следующем: каждый раз, когда приложению необходим маркер доступа, оно просто вызывает `authContext.AcquireToken(…)`, а ADAL делает все остальное.  

1. Инициализируйте класс `AuthenticationContext` приложения, который является основным классом ADAL. Это действие отправляет в библиотеку ADAL координаты, которые ей требуются для взаимодействия с Azure AD, и сообщает о способе кэширования маркеров.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Найдите метод `Search(...)`, который вызывается при нажатии кнопки **Поиск** в пользовательском интерфейсе приложения. Этот метод выполняет запрос GET в интерфейс API Graph службы Azure AD для запроса списка пользователей, чьи имена участника-пользователя начинаются с данного слова поиска. Для отправки запросов в API Graph включите маркер доступа в заголовок **авторизации** запроса. Вот где может пригодиться ADAL.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Когда приложение запрашивает маркер путем вызова `AcquireTokenAsync(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя. Если библиотека ADAL решит, что пользователь должен войти в систему для получения маркера, то она отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной аутентификации. Если ADAL по какой-то причине не сможет вернуть маркер, то статус *AuthenticationResult* будет представлять собой ошибку.
3. Теперь настало время использовать только что полученный маркер доступа. Также в методе `Search(...)` добавьте маркер в запрос GET API Graph в заголовке **авторизации**:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Для отображения сведений о пользователе в вашем приложении, например идентификатора пользователя, можно использовать объект `AuthenticationResult`:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. Также с помощью ADAL можно выполнить выход пользователей из приложения. Когда пользователь нажимает кнопку **Выход**, убедитесь, что при следующем вызове `AcquireTokenAsync(...)` отображается окно входа. С помощью ADAL это так же просто сделать, как и очистить кэш маркеров:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Что дальше?
Теперь у нас есть рабочее приложение для Магазина Windows, которое может выполнять аутентификацию пользователей, безопасно вызывать веб-API с помощью OAuth 2.0 и получать основные сведения о пользователе.

Если в клиент еще не добавлены пользователи, то сейчас самое время это сделать.
1. Запустите приложение DirectorySearcher и войдите как один из пользователей.
2. Осуществите поиск других пользователей по их имени участника-пользователя.
3. Закройте приложение и снова запустите его. Обратите внимание на то, что пользовательский сеанс не изменяется.
4. Выйдите (щелкните правой кнопкой мыши, чтобы отобразить нижнюю панель) и снова войдите под именем другого пользователя.

Библиотека ADAL упрощает процесс включения всех этих общих возможностей идентификации в приложение. Она отвечает за всю "грязную работу": управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа и обновление истекших маркеров. Вам нужно знать только один вызов API — `authContext.AcquireToken*(…)`.

Скачайте для справки [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (без ваших значений конфигурации).

Теперь можно приступить к дополнительным сценариям идентификации. Например, попробуйте использовать [защиту веб-API для .NET с помощью Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
