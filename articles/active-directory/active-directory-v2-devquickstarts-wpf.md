<properties
	pageTitle="Собственное приложение .NET для Azure AD версии 2.0 | Microsoft Azure"
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
	ms.date="05/31/2016"
	ms.author="dastrock"/>

# Добавление функции входа в классическое приложение для Windows

Точка доступа версии 2.0 позволяет быстро реализовать в классических приложениях аутентификацию с поддержкой личных учетных записей Майкрософт, а также рабочих или учебных учетных записей. Она также позволяет приложению безопасно обмениваться данными с серверным веб-API, а также [Microsoft Graph](https://graph.microsoft.io) и несколькими [унифицированными API Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

[Собственным приложениям .NET, которые работают на устройстве](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD предлагает использовать библиотеку проверки подлинности Active Directory (ADAL). Единственная задача ADAL — упрощение процесса получения приложением маркеров доступа и вызова веб-служб. Чтобы показать, насколько это просто, создадим приложение To Do List (ведение списка дел) для .NET WPF, которое:

-	выполняет вход пользователя и получает маркеры доступа с помощью [протокола проверки подлинности OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow);
-	безопасно вызывает серверную веб-службу списка дел, которая также защищена OAuth 2.0.
-	выполняет выход пользователя из приложения.

## Скачивание примера кода

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

Готовое приложение также приводится в конце этого руководства.

## регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Добавьте для приложения **мобильную** платформу.
- Скопируйте с портала **универсальный код ресурса (URI) перенаправления**. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.

## Установка и настройка ADAL
Теперь ваше приложение зарегистрировано в Майкрософт, можно установить ADAL и написать свой собственный код для идентификации. Чтобы ADAL могла обмениваться информацией с конечной точкой v2.0, необходимо предоставить некоторую информацию о регистрации вашего приложения.

-	Сначала добавьте ADAL в проект TodoListClient с помощью консоли диспетчера пакетов.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease
```

-	В проекте TodoListClient откройте `app.config`. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале регистрации приложений. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
    -	`ida:ClientId` — это **идентификатор приложения**, скопированный с портала.
    -	`ida:RedirectUri` — это **универсальный код ресурса (URI) перенаправления** с портала.
- В проекте TodoList-Service откройте `web.config` в корневой папке проекта.  
    - Замените значение `ida:Audience` на **идентификатор приложения** с портала.

## Использование ADAL для получения маркеров
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

- После запуска приложения необходимо проверить, выполнил ли пользователь вход в приложение. Однако пока нет необходимости вызывать пользовательский интерфейс входа: пользователь должен нажать кнопку "Вход". Также в методе `OnInitialized(...)`:

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
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// If the user clicked the 'clear cache' button,
		// clear the ADAL token cache and show the user as signed out.
		// It's also necessary to clear the cookies from the browser
		// control so the next user has a chance to sign in.

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

## Выполнить

Поздравляем! Теперь у вас есть работающее приложение .NET WPF, которое может проверять подлинность пользователей и выполнять безопасные вызовы веб-API при помощи OAuth 2.0. Запустите оба проекта и выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью. Добавьте задачи в список дел этого пользователя.   Выйдите из системы и выполните вход от имени других пользователей, чтобы просмотреть их списки дел. Закройте приложение и снова его запустите. Обратите внимание, что сеанс пользователя при этом не прервался. Это связано с тем, что приложение кэширует маркеры в локальный файл.

ADAL позволяет легко реализовать в приложении функции идентификации, объединяющие для одного человека личные и рабочие учетные записи. Он отвечает за всю грязную работу: управление кэшем, поддержку протокола OAuth, предоставление пользователю пользовательского интерфейса для входа, обновление истекших маркеров и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireTokenAsync(...)`.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip) или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Дальнейшие действия

Теперь можно перейти к более сложным темам. Можно попробовать:

- [Защита веб-API TodoListService с помощью конечной точки версии 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Дополнительные ресурсы:
- [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Тег StackOverflow "adal" >>](http://stackoverflow.com/questions/tagged/adal)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0608_2016-->