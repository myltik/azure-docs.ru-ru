<properties 
	pageTitle="Приступая к работе с проверкой подлинности в мобильных службах для приложений Xamarin iOS - мобильные службы Azure" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Xamarin для iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="donnam"/>

# Добавление проверки подлинности в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>В Visual Studio или Xamarin Studio запустите проект клиента на устройстве или симуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

В этом разделе предстоит изменить приложение для отображения экрана входа до отображения данных. При запуске приложение не подключится к вашей мобильной службе и не отобразит никаких данных. После первого обновления пользователем появится экран входа; список задач появится после успешного входа.

1. В клиентском проекте откройте файл **QSTodoService.cs** и добавьте следующие объявления в QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Добавьте новый метод **Authenticate** в **QSTodoService** со следующим определением:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE] Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод **LoginAsync** выше, на одно из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Откройте **QSTodoListViewController.cs**. Измените определение метода **ViewDidLoad**, удалив вызов **RefreshAsync()** в конце:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
		}


4. Измените метод **RefreshAsync** для проверки подлинности и отображения экрана входа, если свойство **User** равно NULL. В следующем коде, расположенном в верхней части определения метода, выполните следующие действия.

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Нажмите кнопку **Запустить**, чтобы создать проект и запустить приложение в симуляторе iPhone. Убедитесь, что в приложении не отображаются данные. 

	Обновите, потянув вниз список элементов, чтобы появился экран входа. После того, как вы успешно введете допустимые учетные данные, приложение отобразит список задач, и вы сможете обновить данные.

<!-- ## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб на стороне службы][Авторизация пользователей с помощью скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. 
 -->
 
<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push/
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript и HTML]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Портал управления Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
