<properties
	pageTitle="Приступая к работе с Xamarin Azure AD | Microsoft Azure"
	description="Практическое руководство по созданию приложения Xamarin, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, с помощью OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Интеграция Azure AD в приложение Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin позволяет создавать мобильные приложения на C#, которые могут работать в iOS, Android и Windows (на мобильных устройствах и ПК). При разработке приложения с помощью Xamarin система Azure AD позволяет легко и просто осуществлять проверку подлинности пользователей с помощью их учетных записей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Для приложений Xamarin, которым требуется доступ к защищенным ресурсам, система Azure AD предоставляет библиотеку проверки подлинности Active Directory (или ADAL). Единственное предназначение ADAL — упростить процесс получения маркеров доступа. Чтобы продемонстрировать, насколько простой является данная процедура, мы выполним сборку приложения DirectorySearcher, которое:

-	выполняется на платформах iOS, Android, Windows Desktop, Windows Phone и Магазина Windows;
- использует единую переносимую библиотеку классов (PCL) для проверки подлинности пользователей и получения маркеров для интерфейса API Graph в Azure AD;
-	осуществляет поиск пользователей в каталоге с помощью заданного UPN.

Для создания полного рабочего приложения необходимо:

2. настроить среду разработки Xamarin;
2. Зарегистрировать приложение в Azure AD.
3. установить и настроить ADAL;
5. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [загрузите проект схемы](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) или [загрузите готовый пример](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Каждый из них является решением Visual Studio 2013. Вам также потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение. Если клиента нет, [узнайте, как его получить](active-directory-howto-tenant.md).

## *0. Настройка среды разработки Xamarin*
Так как этот учебник содержит проекты для iOS, Android и Windows, вам потребуются Visual Studio и Xamarin. Для создания необходимой среды следуйте инструкциям в разделе [Настройка и установка](https://msdn.microsoft.com/library/mt613162.aspx) на сайте MSDN. Эти инструкции содержат материалы, которые можно просмотреть, чтобы больше узнать о Xamarin, пока вы ожидаете завершения работы установщиков.

После завершения процесса настройки откройте решение в Visual Studio, чтобы приступить к работе. Вы увидите шесть проектов: пять проектов для конкретной платформы и одну переносимую библиотеку классов, которая будет общей для всех платформ `DirectorySearcher.cs`

## *1. Регистрация приложения Directory Searcher*
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD.

-	Войдите на [Портал управления Azure](https://manage.windowsazure.com)
-	В левой панели навигации щелкните **Active Directory**.
-	Выберите клиента, в котором будет зарегистрировано приложение.
-	Перейдите на вкладку **Приложения** и нажмите кнопку **Добавить** в нижней панели.
-	Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**.
    -	**Имя** приложения отображает его описание конечным пользователям.
    -	**Uri перенаправления** представляет собой комбинацию схемы и строки, которую Azure AD будет использовать для возврата ответов на маркеры. Введите значение, например `http://DirectorySearcher`.
-	После завершения регистрации Azure AD назначает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому скопируйте его с вкладки **Настройка**.
- Также во вкладке **Настройка** найдите раздел "Разрешения для других приложений". Для приложения Azure Active Directory добавьте разрешение на **Допуск к каталогу вашей организации** в списке **Делегированные разрешения**. Это позволит приложению запрашивать интерфейс Graph API для пользователей.

## *2. Установка и настройка ADAL*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.
-	Начните с добавления ADAL в каждый из проектов в решении с помощью консоли диспетчера пакетов.

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

- Следует отметить, что в каждый проект добавляются две ссылки на библиотеку: часть PCL в ADAL и специфическая для платформы часть.

-	В проекте DirectorySearcherLib откройте `DirectorySearcher.cs`. Замените значения членов класса, чтобы отражать значения, введенные на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
    -	`tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
    -	`clientId` — это идентификатор clientId приложения, скопированный с портала.
    - `returnUri` — это redirectUri, который был введен на портале, например `http://DirectorySearcher`.

## *3. Использование ADAL для получения маркеров из Azure AD*
*Практически* все файлы логики проверки подлинности приложения находятся в `DirectorySearcher.SearchByAlias(...)`. Специфические для платформы проекты должны отправлять контекстный параметр в `DirectorySearcher` PCL.

- Сначала откройте `DirectorySearcher.cs` и добавьте новый параметр в метод `SearchByAlias(...)`. `IPlatformParameters` — это контекстный параметр, инкапсулирующий специфические для платформы объекты, которые нужны ADAL для проверки подлинности.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	Затем инициализируйте `AuthenticationContext` — основной класс ADAL. Здесь вы отправляете в ADAL координаты, которые ему требуются для взаимодействия с Azure AD. Затем вызовите `AcquireTokenAsync(...)`, который принимает объект `IPlatformParameters` и будет вызывать поток проверки подлинности, необходимый для возврата маркера в приложение.

```C#
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
- `AcquireTokenAsync(...)` сначала попытается вернуть маркер для запрошенного ресурса (в данном случае Graph API) и не будет предлагать пользователю вводить свои учетные данные (путем кэширования или обновления старых маркеров). Только при необходимости перед получением запрошенного токена для пользователя появится страница входа в Azure AD.


- Затем вы можете присоединить маркер доступа к запросу интерфейса Graph API в заголовке авторизации:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Это все, что нужно сделать для `DirectorySearcher` PCL и кода, связанного с идентификацией вашего приложения. Остается только вызвать метод `SearchByAlias(...)` во всех представлениях для платформы и при необходимости добавить код для правильной обработки жизненного цикла пользовательского интерфейса.

####Android:
- В `MainActivity.cs` добавьте вызов в `SearchByAlias(...)` в обработчике нажатия кнопки:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Также необходимо переопределить метод жизненного цикла `OnActivityResult` для пересылки операций перенаправления проверку подлинности обратно в соответствующий метод. Для этого ADAL предоставляет вспомогательный метод в Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Классическое приложение Windows:
- В `MainWindow.xaml.cs` просто отправьте вызов в `SearchByAlias(...)`, передавая `WindowInteropHelper` в объекте `PlatformParameters` классического приложения:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- В `DirSearchClient_iOSViewController.cs` объект `PlatformParameters` iOS просто принимает ссылку на контроллер представления:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Универсальное приложение Windows:
- В универсальном приложении Windows откройте `MainPage.xaml.cs` и реализуйте метод `Search`, который использует вспомогательный метод в общем проекте для обновления пользовательского интерфейса требуемым образом.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Поздравляем! Теперь у нас есть рабочее приложение Xamarin, которое позволяет проверять подлинность пользователей и безопасным образом вызывать веб-интерфейс API с помощью OAuth 2.0 на пяти различных платформах. Если вы это еще не сделали, сейчас можно добавить несколько пользователей вашему клиенту. Запустите свое приложение DirectorySearcher и войдите под именем одного из таких пользователей. Осуществите поиск других пользователей по их имени участника-пользователя.

ADAL упрощает процесс включения общих возможностей идентификации в приложение. Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireToken*(…)`.

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) находится [здесь](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Теперь можно приступить к дополнительным сценариям идентификации. Можно попробовать:

[Безопасность веб-API .NET с Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->