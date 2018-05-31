---
title: Начало работы с Xamarin и Azure AD | Документация Майкрософт
description: Создание приложений Xamarin, которые интегрируются с Azure AD для входа в систему и вызывают программные интерфейсы, защищенные Azure AD, с помощью OAuth.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ac04cddc00bf76bb366a249a5a2ec4c56d5212c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156693"
---
# <a name="azure-ad-xamarin-getting-started"></a>Начало работы с Xamarin и Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Среда Xamarin позволяет создавать мобильные приложения на C#, которые могут работать в iOS, Android и Windows (на мобильных устройствах и ПК). При разработке приложения с помощью Xamarin служба Azure Active Directory (Azure AD) позволяет легко и просто осуществлять проверку подлинности пользователей с помощью их учетных записей Azure AD. Кроме того, приложение может безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например API-интерфейсы Office 365 или Azure.

Для приложений Xamarin, которым требуется доступ к защищенным ресурсам, Azure AD предоставляет библиотеку проверки подлинности Active Directory (ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. В этой статье показано, насколько это легко, на примере создания приложений DirectorySearcher, которые обладают следующими преимуществами:

* выполняются в iOS, Android, Windows Desktop, Windows Phone и Магазине Windows;
* используют единую переносимую библиотеку классов (PCL) для проверки подлинности пользователей и получения маркеров для интерфейса API Graph в Azure AD;
* осуществляют поиск пользователей в каталоге с помощью заданного имени участника-пользователя.

## <a name="before-you-get-started"></a>Необходимые условия
* Скачайте [схему проекта](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Каждая из этих загрузок является решением Visual Studio 2013.
* Вам также необходим клиент Azure AD для создания пользователей и регистрации приложения. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

Когда будете готовы, выполните процедуры, описанные в следующих четырех разделах.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Шаг 1. Настройка среды разработки Xamarin
Это руководство содержит проекты для iOS, Android и Windows, поэтому вам потребуются Visual Studio и Xamarin. Для создания необходимой среды следуйте инструкциям в разделе [Настройка и установка](https://msdn.microsoft.com/library/mt613162.aspx) на сайте MSDN. Эти инструкции содержат материалы, которые можно просмотреть, чтобы больше узнать о Xamarin, пока вы ожидаете завершения процессов установки.

После завершения настройки откройте решение в Visual Studio. Вы увидите шесть проектов: пять проектов для конкретной платформы и одну переносимую библиотеку классов DirectorySearcher.cs, которая будет общей для всех платформ.

## <a name="step-2-register-the-directorysearcher-app"></a>Шаг 2. Регистрация приложения DirectorySearcher
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу API Graph для Azure AD. Этот процесс описывается далее.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните свою учетную запись. Затем в списке **Каталог** выберите клиент Active Directory для регистрации приложения.
3. В области слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.
5. Следуйте инструкциям на экране, чтобы создать **собственное клиентское приложение**.
  * **Имя** — описание приложения для пользователей.
  * **URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения ответов маркеров. Введите значение (например, http://DirectorySearcher).
6. После завершения регистрации Azure AD присваивает приложению уникальный идентификатор. Скопируйте значение на вкладке **Приложение**. Оно потребуется вам позже.
7. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.
8. Выберите API **Microsoft Graph**. В разделе **Делегированные разрешения** добавьте разрешение **Чтение данных каталога**. Это действие позволяет приложению отправлять запросы в API Graph для пользователей.

## <a name="step-3-install-and-configure-adal"></a>Шаг 3. Установка и настройка ADAL
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы обеспечить взаимодействие библиотеки ADAL с Azure AD, необходимо указать определенные сведения о регистрации приложения.

1. Добавьте ADAL в проект DirectorySearcher с помощью консоли диспетчера пакетов.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Обратите внимание, что в каждый проект добавляются две ссылки на библиотеку: часть PCL в ADAL и специфическая для платформы часть.
2. В проекте DirectorySearcherLib откройте файл DirectorySearcher.
3. Замените значения членов класса значениями, введенными на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.

  * *tenant* — это домен вашего клиента Azure AD (например, contoso.onmicrosoft.com).
  * *clientId* — идентификатор клиента приложения, скопированный с портала.
  * *returnUri* — это универсальный код ресурса (URI) для перенаправления, который вы указали на портале (например, http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Шаг 4. Использование ADAL для получения маркеров из Azure AD
Практически вся логика для проверки подлинности приложения находится в `DirectorySearcher.SearchByAlias(...)`. Специфические для платформы проекты должны отправлять контекстный параметр в PCL `DirectorySearcher`.

1. Откройте файл DirectorySearcher.cs, а затем добавьте новый параметр в метод `SearchByAlias(...)`. `IPlatformParameters` — это контекстный параметр, инкапсулирующий специфические для платформы объекты, которые нужны ADAL для проверки подлинности.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Инициализируйте `AuthenticationContext` — основной класс ADAL. Это действие отправляет в библиотеку ADAL координаты, которые требуются ей для взаимодействия с Azure AD.
3. Вызовите метод `AcquireTokenAsync(...)`, который принимает объект `IPlatformParameters` и будет вызывать последовательность проверки подлинности, необходимую для возврата маркера в приложение.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` сначала попытается вернуть маркер для запрошенного ресурса (в данном случае API Graph) и не будет предлагать пользователю вводить учетные данные (путем кэширования или обновления старых маркеров). При необходимости перед получением запрошенного маркера для пользователя отображается страница входа в Azure AD.
4. Присоедините маркер доступа к запросу API Graph в заголовке **авторизации**:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Это все, что нужно сделать для PCL `DirectorySearcher` и кода, связанного с удостоверением приложения. Остается только вызвать метод `SearchByAlias(...)` во всех представлениях для платформы и при необходимости добавить код для правильной обработки жизненного цикла пользовательского интерфейса.

### <a name="android"></a>Android
1. В файле MainActivity.cs добавьте вызов в `SearchByAlias(...)` в обработчике нажатия кнопки:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Переопределите метод жизненного цикла `OnActivityResult` для пересылки операций перенаправления проверки подлинности обратно в соответствующий метод. Для этого ADAL предоставляет вспомогательный метод в Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Классические приложения
В файле MainWindow.xaml.cs отправьте вызов в `SearchByAlias(...)`, передав `WindowInteropHelper` в объекте `PlatformParameters` классического приложения:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
В файле DirSearchClient_iOSViewController.cs объект iOS `PlatformParameters` принимает ссылку на контроллер представления:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
В универсальной платформе Windows откройте файл MainPage.xaml.cs, а затем реализуйте метод `Search`. Этот метод использует вспомогательный метод в общем проекте, чтобы обновлять пользовательский интерфейс при необходимости.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Что дальше?
Теперь у нас есть рабочее приложение Xamarin, которое позволяет проверять подлинность пользователей и безопасным образом вызывать веб-интерфейсы API с помощью OAuth 2.0 на пяти различных платформах.

Если в клиент еще не добавлены пользователи, сейчас самое время это сделать.

1. Запустите приложение DirectorySearcher и войдите как один из пользователей.
2. Осуществите поиск других пользователей по их имени участника-пользователя.

ADAL упрощает процесс включения общих возможностей идентификации в приложение. Эта библиотека отвечает за всю "грязную работу": управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа и обновление истекших маркеров. Вам нужно знать только один вызов API — `authContext.AcquireToken*(…)`.

Скачайте для справки [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (без ваших значений конфигурации).

Теперь можно приступить к дополнительным сценариям идентификации. Например, попробуйте использовать [защиту веб-API для .NET с помощью Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
