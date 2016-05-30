<properties
	pageTitle="Предварительная версия Azure Active Directory B2C | Microsoft Azure"
	description="Инструкции по созданию приложений Windows, позволяющих пользователям выполнять вход, регистрироваться и управлять профилем, с помощью Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="dastrock"/>

# Предварительная версия Azure AD B2C: построение классического приложения Windows


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Azure Active Directory (Azure AD) B2C позволяет добавлять в приложения мощные функции для самостоятельного управления удостоверениями. Это можно сделать, выполнив несколько простых действий. В этой статье описывается, как создать приложение .NET WPF "Список дел", которое предусматривает регистрацию и вход пользователя, а также управление профилем. Приложение будет поддерживать регистрацию и вход в систему по имени пользователя или адресу электронной почты, а также по учетной записи в социальной сети, такой как Facebook или Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите **собственный клиент** в приложении.
- Скопируйте **URI перенаправления** `urn:ietf:wg:oauth:2.0:oob`. Это URL-адрес по умолчанию для данного примера кода.
- Скопируйте **идентификатор приложения**, назначенный приложению. Оно понадобится вам позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: регистрацию, вход в систему и изменение профиля. Вам необходимо создать по одной политике для каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех необходимых политик обязательно сделайте следующее:

- В колонке поставщиков удостоверений выберите **Регистрация с помощью идентификатора пользователя** или **Регистрация по электронной почте**.
- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике в качестве утверждения приложения выберите утверждения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

## Загрузка кода

Код, используемый в этом руководстве, размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Решение содержит два проекта: `TaskClient` и `TaskService`. `TaskClient` — это приложение MVC, с которым взаимодействует пользователь. `TaskService` — это веб-API серверной части приложения, в котором хранится список дел для каждого пользователя. В этом случае и `TaskClient`, и `TaskService` представлены одним идентификатором приложения, так как включают в себя одно приложение логики.

## Настройка службы задач

Когда проект `TaskService` получает запрос от `TaskClient`, он проверяет наличие допустимого токена доступа для проверки подлинности запроса. Чтобы проверить токен доступа, необходимо предоставить проекту `TaskService` определенные сведения о приложении. В корне проекта `TaskService` откройте файл `web.config` и замените значения в разделе `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Сведения о том, как веб-API надежно выполняет проверку подлинности запросов с помощью Azure AD B2C, см. в [статье о начале работы с веб-API](active-directory-b2c-devquickstarts-api-dotnet.md).

## Выполнение политик
Когда проект `TaskService` готов к проверке подлинности запросов, можно реализовать `TaskClient`. Приложение взаимодействует с Azure AD B2C, отправляя HTTP-запросы на проверку подлинности. Они указывают политику, которую необходимо выполнить как часть запроса. Для классических приложений .NET можно использовать библиотеку проверки подлинности Active Directory (ADAL), чтобы отправлять сообщения проверки подлинности OAuth 2.0, выполнять политики и получать маркеры для вызова веб-API.

### Установка ADAL
Добавьте ADAL в проект `TaskClient` с помощью консоли диспетчера пакетов Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### Ввод данных B2C
Откройте файл `Globals.cs` и замените все значения свойств на ваши собственные. Этот класс применяется для ссылки на часто используемые значения во всем проекте `TaskClient`.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### Создание класса AuthenticationContext
Основной класс ADAL — это `AuthenticationContext`. Он представляет подключение вашего приложения к каталогу B2C. При запуске приложения создайте экземпляр `AuthenticationContext` в `MainWindow.xaml.cs`. Его можно использовать для выполнения всех действий в этом окне.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Запуск потока регистрации
Нам нужно, чтобы, когда пользователь нажимал кнопку регистрации, инициировался созданный нами поток регистрации. Для этого требуется только вызов метода `authContext.AcquireTokenAsync(...)` с помощью ADAL. Параметры, передаваемые в метод `AcquireTokenAsync(...)`, определяют, какой маркер вы получите, какая политика будет использована в запросе проверки подлинности, а также другие данные.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

### Инициация потока входа
Поток входа инициируется таким же образом, как и поток регистрации. При нажатии пользователем кнопки входа должен выполняться тот же вызов в ADAL, но на этот раз с использованием политики входа.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### Инициирование потока изменения профиля
Политика редактирования профиля реализуется аналогично.

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

Во всех этих случаях ADAL либо возвращает маркер в `AuthenticationResult`, либо выдает исключение. Если ADAL выдает маркер, то для обновления данных пользователя в приложении (например, пользовательского интерфейса) можно использовать объект `AuthenticationResult.UserInfo`. Кроме того, ADAL кэширует маркер для использования в других частях приложения.

## Вызов API
Вы уже использовали ADAL для выполнения политик и получения маркеров. Однако во многих случаях можно не выполнять политику и проверить, не сохранился ли маркер в кэше. Один из таких случаев — попытка приложения получить список дел пользователя из `TaskService`. Для этого можно применить тот же метод `authContext.AcquireTokenAsync(...)`, а в качестве параметра области снова указать `clientId`, но на этот раз нужно также использовать `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
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

Если вызов `AcquireTokenAsync(...)` завершается успешно и в кэше имеется маркер, то можно добавить этот маркер в заголовок `Authorization` HTTP-запроса, чтобы `TaskService` мог проверить подлинность запроса на чтение списка дел пользователя.

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Этот шаблон можно использовать для проверки кэша на наличие маркеров в любое время даже без предложения пользователю выполнить вход в систему. Например, при каждом запуске приложения необходимо проверять `FileCache` на наличие маркеров, чтобы при этом возобновлялся сеанс входа пользователя. Событие `MainWindow` класса `OnInitialized` содержит тот же код, который выполняется при первом запуске `OnInitialized`.

## Выход пользователя
ADAL можно использовать для завершения сеанса пользователя в приложении, когда пользователь нажимает кнопку **Выход**. Для этого нужно удалить все маркеры из соответствующего кэша с помощью ADAL:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
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

## Запуск примера приложения

Наконец, выполните сборку и запуск `TaskClient` и `TaskService`. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите и снова войдите под именем того же пользователя. Измените профиль пользователя. Выйдите и зарегистрируйтесь от имени другого пользователя.

## Добавление поставщиков удостоверений социальных сетей

В настоящее время приложение поддерживает регистрацию и вход пользователей только с использованием **локальных учетных записей**. Учетные записи хранятся в каталоге B2C, где применяется имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других поставщиков удостоверений (IDP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

- [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
- [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
- [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
- [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

После добавления поставщиков удостоверений в каталог B2C необходимо внести изменения во все три указанные выше политики, указав в них эти новые поставщики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение. Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой ADAL.

Готовый пример в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0518_2016-->