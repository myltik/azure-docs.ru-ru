<properties
	pageTitle="Модель приложений 2.0 | Microsoft Azure"
	description="Как создать собственное приложение .NET, которое поддерживает вход пользователей с помощью личной учетной записи Майкрософт, а также рабочей или учебной учетных записей."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Предварительная версия модели приложений 2.0: реализация входа в классическом приложении Windows

Модель приложений версии 2.0 позволяет быстро реализовать проверку подлинности для классического приложения с поддержкой личных учетных записей Майкрософт, а также рабочих и учебных учетных записей. Она также позволяет приложению безопасно обмениваться данными с серверным веб-API, а также несколькими [объединенными API-интерфейсами Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]Эти сведения относятся к общедоступной предварительной версии модели приложений 2.0. Инструкции по интеграции с общедоступной службой Azure AD см. в статье [Руководство разработчика Azure Active Directory](active-directory-developers-guide.md).

[Собственным приложениям .NET, которые работают на устройстве](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD предлагает использовать библиотеку проверки подлинности Active Directory (ADAL). Единственная задача ADAL — упрощение процесса получения приложением маркеров доступа и вызова веб-служб. Чтобы показать, насколько это просто, создадим приложение To Do List (ведение списка дел) для .NET WPF, которое:

-	выполняет вход пользователя и получает маркеры доступа с помощью [протокола проверки подлинности OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow);
-	безопасно вызывает серверную веб-службу списка дел, которая также защищена OAuth 2.0.
-	выполняет выход пользователя из приложения.

Для создания полноценного рабочего приложения необходимо:

2. зарегистрировать приложение.
3. установить и настроить ADAL;
5. Использовать ADAL для получения маркеров из Azure AD.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.

## 2. Install & Configure ADAL
Now that you have an app registered with Microsoft, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate the v2.0 endpoint, you need to provide it with some information about your app registration.

-	Begin by adding ADAL to the TodoListClient project using the Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease 
```

-	В проекте TodoListClient откройте `app.config`. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале регистрации приложений. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
    -	`ida:ClientId` — это **идентификатор приложения**, скопированный с портала.
    -	`ida:RedirectUri` — это **URI перенаправления** с портала.
- В проекте TodoList-Service откройте `web.config` в корневой папке проекта.  
    - Замените значение `ida:Audience` на **идентификатор приложения** с портала.

## 3\. Использование ADAL для получения маркеров
Основной принцип ADAL состоит в следующем: каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать `authContext.AcquireToken(...)`, а ADAL сделает все остальное.

-	В проекте `TodoListClient` откройте `MainWindow.xaml.cs` и найдите метод `OnInitialized(...)`. Первый шаг состоит в инициализации `AuthenticationContext` — основного класса ADAL. Здесь вы отправляете в ADAL координаты, которые ему требуются для взаимодействия с Azure AD, и сообщаете о способе кэширования маркеров.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- После запуска приложения необходимо проверить, выполнил ли пользователь вход в приложение. Однако пока нет необходимости вызывать пользовательский интерфейс входа: пользователь должен нажать кнопку "Вход". В методе `OnInitialized(...)`:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Если пользователь не выполнил вход и нажал кнопку "Вход", необходимо вызвать пользовательский интерфейс входа и запросить ввод учетных данных. Реализуйте обработчик кнопки "Вход".

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
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

- Если пользователь успешно выполняет вход, ADAL получит и кэширует маркер, а вы сможете вызвать метод `GetTodoList()`. Осталось получить задачи пользователя — реализовать метод `GetTodoList()`.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
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

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null) { // Если пользователь нажал кнопку "очистить кэш", // очистите кэш маркеров ADAL и покажите пользователя как вышедшего из системы. // Также необходимо удалить файлы cookie браузера, // чтобы следующий пользователь смог войти.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Congratulations! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0.  Run your both projects, and sign in with either a personal Microsoft account or a work or school account.  Add tasks to that user's To-Do list.  Sign out, and sign back in as another user to view their To-Do list.  Close the app, and re-run it.  Notice how the user's session remains intact - that is becuase the app caches tokens in a local file.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts.  It takes care of all the dirty work for you - cache management, OAuth protocol support, presenting the user with a login UI, refreshing expired tokens, and more.  All you really need to know is a single API call, `authContext.AcquireTokenAsync(...)`.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Дальнейшие действия

Теперь можно перейти к более сложным темам. Можно попробовать:

- [Защита веб-API TodoListService с помощью модели приложений версии 2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Дополнительные ресурсы: -[Предварительная версия модели приложений 2.0 >>](active-directory-appmodel-v2-overview.md) -[Тег StackOverflow "adal" >>](http://stackoverflow.com/questions/tagged/adal)

<!----HONumber=August15_HO7-->