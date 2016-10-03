<properties
	pageTitle="Приступая к работе с Azure AD для .NET | Microsoft Azure"
	description="Практическое руководство по созданию классического приложения .NET Windows, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Интеграция Azure AD в классическое приложение Windows WPF

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

При разработке классического приложения служба Microsoft Azure Active Directory позволяет разработчику упростить проверку подлинности учетных записей пользователей в Active Directory. Это также позволяет вашему приложению безопасно использовать любые веб-интерфейсы API, защищаемые с помощью Azure AD, например интерфейсы Office 365 API или Azure API.

Клиентские нативные приложения для .NET, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL). Единственное предназначение ADAL — упростить для разработчика процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (ведение списка дел) для .NET WPF, которое:

-	Получает маркеры доступа для вызова интерфейса Graph API Azure AD с помощью [протокола проверки подлинности OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Осуществляет поиск пользователей в каталоге по псевдониму.
-	Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо:

2. Зарегистрировать приложение в Azure AD.
3. установить и настроить ADAL;
5. использовать ADAL для получения маркеров из Azure AD.

Чтобы начать работу, [загрузите каркас приложения](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) или [завершенный пример](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Вам также потребуется клиент Azure AD, в котором можно будет создавать пользователей и зарегистрировать приложение. Если у вас еще нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## *1. Регистрация приложения DirectorySearcher*
Чтобы приложение могло получать маркеры, сначала необходимо его зарегистрировать в клиенте Azure AD и предоставить ему разрешение на доступ к интерфейсу Graph API Azure AD:

-	Войдите на портал управления Azure.
-	В левой панели навигации щелкните **Active Directory**.
-	Выберите клиента, в котором будет зарегистрировано приложение.
-	Перейдите на вкладку **Приложения** и нажмите кнопку **Добавить** в нижней панели.
-	Следуйте инструкциям на экране, а затем создайте новое **Собственное клиентское приложение**.
    -	**Имя** приложения отображает его описание конечным пользователям.
    -	**URI перенаправления** представляет собой сочетание схемы и строки, используемое Azure AD для возвращения любых маркеров, запрошенных приложением. Укажите значение, специфичное для вашего приложения, например `http://DirectorySearcher`.
-	После завершения регистрации служба Azure AD присваивает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому скопируйте его с вкладки **Настройка**.
- Также на вкладке **Настройка** найдите раздел "Разрешения для других приложений". Для приложения Azure Active Directory добавьте разрешение на **Допуск к каталогу вашей организации** в списке **Делегированные разрешения**. Это позволит приложению запрашивать интерфейс Graph API для пользователей.

## *2. Установка и настройка ADAL*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.
-	Сначала добавьте ADAL в проект DirectorySearcher с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	В проекте DirectorySearcher откройте `app.config`. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
    -	`ida:Tenant` — это домен вашего клиента Azure AD, например contoso.onmicrosoft.com
    -	`ida:ClientId` — это идентификатор clientId приложения, скопированный с портала.
    -	`ida:RedirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## *3. Использование библиотеки ADAL для получения маркеров из Azure AD*
Основной принцип ADAL состоит в следующем: каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать `authContext.AcquireTokenAsync(...)`, а библиотека ADAL сделает все остальное.

-	В проекте `DirectorySearcher` откройте `MainWindow.xaml.cs` и найдите метод `MainWindow()`. Первый шаг состоит в инициализации `AuthenticationContext` — основного класса ADAL. Здесь вы отправляете в библиотеку ADAL координаты, которые ей требуются для взаимодействия с Azure AD, и сообщаете о способе кэширования маркеров.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Теперь найдите метод `Search(...)`, который будет вызываться при нажатии кнопки "Поиск" в пользовательском интерфейсе приложения. Этот метод выполняет запрос GET в интерфейс Graph API службы Azure AD для запроса списка пользователей, чьи UPN начинаются с данного слова поиска. Но для отправки запросов в Graph API необходимо включить access\_token в заголовок `Authorization` запроса — именно отсюда ADAL начинает свою работу.

```C#
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
- Когда приложение запрашивает маркер путем вызова `AcquireTokenAsync(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя. Если ADAL решит, что пользователь должен выполнить вход для получения маркера, будет отображено диалоговое окно входа. Введенные учетные данные пользователя будут использованы для проверки подлинности, и в случае успешной проверки библиотека вернет маркер. Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.
- Обратите внимание, что объект `AuthenticationResult` содержит объект `UserInfo`, который может использоваться для сбора сведений, необходимых приложению. В DirectorySearcher объект `UserInfo` используется для настройки пользовательского интерфейса приложения на основе идентификатора пользователя.

- Когда пользователь нажимает кнопку выхода, необходимо, чтобы при следующем вызове `AcquireTokenAsync(...)` пользователю было предложено войти. С ADAL это так же просто, как и очистка кэша маркера:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Если пользователь не нажимает кнопку выхода, то можно реализовать восстановление данных сеанса пользователя при следующем запуске приложения DirectorySearcher. При запуске приложения можно проверить кэш маркеров библиотеки ADAL на наличие соответствующего маркера и соответственно обновить пользовательский интерфейс. В методе `CheckForCachedToken()` выполните другой вызов `AcquireTokenAsync(...)`, на этот раз передав параметр `PromptBehavior.Never`. `PromptBehavior.Never` сообщает ADAL, что пользователю не следует предлагать войти, и вместо этого библиотека ADAL должна породить исключение, если не удается вернуть маркер.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
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

ADAL упрощает процесс включения всех этих общих возможностей идентификации в приложение. Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireTokenAsync(...)`.

Завершенный образец (без ваших настроек) вы можете загрузить [отсюда](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Теперь можно приступить к изучению других сценариев. Можно попробовать:

[Безопасность веб-API .NET с Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->