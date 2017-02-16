---
title: "Приступая к работе с Магазином Windows в Azure AD | Документация Майкрософт"
description: "Практическое руководство по созданию приложения для Магазина Windows, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищаемые системой Azure AD с помощью OAuth."
services: active-directory
documentationcenter: windows
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: f1757379bdb8e04d3302dbd8a4528a1b8ed00f65


---
# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Интеграция Azure AD в приложения Магазина Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

При разработке приложения для магазина Windows система Azure AD позволяет легко и просто осуществлять проверку подлинности пользователей с помощью их учетных записей в Active Directory.  Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Классическим приложениям для Магазина Windows, которым необходим доступ к защищенным ресурсам, Azure AD предоставляет библиотеку проверки подлинности Active Directory (ADAL).  Единственное предназначение ADAL — упростить процесс получения маркеров доступа.  Чтобы продемонстрировать, насколько простой является данная процедура, мы выполним сборку приложения DirectorySearcher для Магазина Windows, которое:

* получает маркеры доступа для вызова интерфейса Graph API Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx);
* осуществляет поиск пользователей в каталоге с помощью заданного UPN;
* Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо:

1. Зарегистрировать приложение в Azure AD.
2. установить и настроить ADAL;
3. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [скачайте проект схемы](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Каждый из них является решением Visual Studio 2015.  Вам также потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение.  Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>1. Регистрация приложения Directory Searcher
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На верхней панели щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В левой области навигации щелкните **Другие службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
5. Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**.
  * **Имя** приложения отображает его описание конечным пользователям.
  * **Uri перенаправления** представляет собой комбинацию схемы и строки, которую Azure AD будет использовать для возврата ответов на маркеры.  Введите значение заполнителя, например, `http://DirectorySearcher`.  Это значение мы заменим позже.
6. После завершения регистрации служба Azure AD присваивает приложению уникальный идентификатор приложения.  Это значение вам понадобится в следующих разделах, поэтому скопируйте его с вкладки приложения.
7. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**.  Для приложения Azure Active Directory выберите в качестве интерфейса API **Microsoft Graph** и добавьте разрешение **Access the directory as the signed-in user** (Доступ к каталогу в качестве пользователя, выполнившего вход) в списке **Делегированные разрешения**.  Это позволит приложению запрашивать интерфейс Graph API для пользователей.

## <a name="2-install--configure-adal"></a>2) Установка и настройка ADAL
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением.  Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.

* Сначала добавьте ADAL в проект DirectorySearcher с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* В проекте DirectorySearcher откройте `MainPage.xaml.cs`.  Замените значения в разделе `Config Values` , чтобы отразить значения, введенные на портале Azure.  Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
  * `tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
  * `clientId` — это идентификатор clientId приложения, скопированный с портала.
* Теперь необходимо обнаружить uri обратного вызова для вашего приложения Windows Phone.  Установите точку останова в этой строке в методе `MainPage` :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
* Выполните сборку решения, убедившись, что все ссылки на пакет восстановлены.  Если пакеты отсутствуют, откройте диспетчер пакетов Nuget и восстановите пакеты.
* Запустите приложение и скопируйте значение `redirectUri` при попадании в точку останова.  Оно должно иметь примерно следующий вид:

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

* Вернитесь на вкладку **Параметры** своего приложения на портале Azure и добавьте **RedirectUri** с этим значением.  

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Использование библиотеки ADAL для получения маркеров из AAD
Основной принцип ADAL состоит в следующем: каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать `authContext.AcquireToken(…)`, а ADAL сделает все остальное.  

* Первый шаг состоит в инициализации `AuthenticationContext` приложения, что является основным классом ADAL.  Здесь вы отправляете в ADAL координаты, которые ему требуются для взаимодействия с Azure AD, и сообщаете о способе кэширования маркеров.

```C#
public MainPage()
{
    ...

    authContext = new AuthenticationContext(authority);
}
```

* Теперь найдите метод `Search(...)` , который будет вызываться при нажатии кнопки "Поиск" в пользовательском интерфейсе приложения.  Этот метод выполняет запрос GET в интерфейс Graph API службы Azure AD для запроса списка пользователей, чьи UPN начинаются с данного слова поиска.  Но для отправки запросов в Graph API необходимо включить access_token в заголовок `Authorization` запроса — именно отсюда ADAL начинает свою работу.

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
* Когда приложение запрашивает маркер путем вызова `AcquireTokenAsync(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя.  Если ADAL решит, что пользователь должен войти в систему для получения маркера, то служба отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной проверки подлинности.  Если ADAL по какой-то причине не сможет вернуть маркер, статус `AuthenticationResult` будет представлять собой ошибку.
* Теперь настало время использовать только что полученный маркер access_token.  Также в методе `Search(...)` введите маркер в запрос GET интерфейса Graph API в заголовке авторизации:

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
* Для отображения сведений о пользователе в вашем приложении, например идентификатора пользователя, также можно использовать объект `AuthenticationResult` .

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
* Наконец, для выхода пользователя из приложения также можно использовать ADAL.  Когда пользователь нажимает кнопку "Выход", необходимо убедиться, что при следующем вызове `AcquireTokenAsync(...)` появится окно входа.  Благодаря применению ADAL это будет так же просто, как и очистка кэша маркера:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Поздравляем! Теперь у нас есть рабочее приложение для Магазина Windows, которое позволяет проверять подлинность пользователей, безопасно вызывать веб-интерфейсы API с помощью OAuth 2.0 и получать основные сведения о пользователе.  Если вы это еще не сделали, сейчас можно добавить несколько пользователей вашему клиенту.  Запустите свое приложение DirectorySearcher и войдите под именем одного из таких пользователей.  Осуществите поиск других пользователей по их имени участника-пользователя.  Закройте приложение и снова его запустите.  Обратите внимание на то, что пользовательский сеанс не изменяется.  Выйдите (щелкните правой кнопкой мыши, чтобы отобразить нижнюю панель) и снова выполнить вход под именем другого пользователя.

ADAL упрощает процесс включения всех этих общих возможностей идентификации в приложение.  Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое.  Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireToken*(…)`.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Теперь можно приступить к дополнительным сценариям идентификации.  Можно попробовать:

[Защита веб-API с помощью Azure AD для .NET >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


