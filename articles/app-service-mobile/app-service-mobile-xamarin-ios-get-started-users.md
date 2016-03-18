<properties
	pageTitle="Начало работы с проверкой подлинности для мобильных приложений в Xamarin iOS"
	description="Использование мобильных приложений для проверки подлинности пользователей приложения Xamarin iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Добавление проверки подлинности в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике приведены инструкции, позволяющие добавить проверку подлинности в ознакомительный проект Xamarin.Forms, используя поставщик удостоверений, поддерживаемый службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя, и вы сможете получить доступ к закрытым табличным данным.

Сначала необходимо выполнить инструкции из учебника [Создание приложения Xamarin.iOS]. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. В Visual Studio или Xamarin Studio запустите клиентский проект на устройстве или в эмуляторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). Ошибка записывается в консоль отладчика. Поэтому в Visual Studio вы увидите ошибку в окне вывода.

&nbsp;&nbsp;Эта нештатная ошибка происходит потому, что приложение пытается получить доступ к серверной части мобильного приложения от имени не прошедшего аутентификацию пользователя. Теперь для таблицы *TodoItem* требуется аутентификация.

Далее вы обновите клиентское приложение для запроса ресурсов из серверной части мобильного приложения прошедшим аутентификацию пользователем.

##Добавление проверки подлинности в приложение

В этом разделе предстоит изменить приложение для отображения экрана входа до отображения данных. При запуске приложение не подключится к службе приложений и не отобразит никаких данных. После первого обновления пользователем появится экран входа; список задач появится после успешного входа.

1. В проекте клиента откройте файл **QSTodoService.cs** и добавьте в QSTodoService следующий оператор using и объект `MobileServiceUser` с методом доступа к классу QSTodoService:

	```
		using UIKit;
	```

		// Logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Добавьте новый метод **Authenticate** в **QSTodoService** со следующим определением:


        public async Task Authenticate(UIViewController view)
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

	>[AZURE.NOTE] Если у вас поставщик удостоверений, отличный от Facebook, замените значение, передаваемое в метод **LoginAsync** выше, одним из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Откройте файл **QSTodoListViewController.cs**. Измените определение метода **ViewDidLoad**, удалив вызов **RefreshAsync()** в конце:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. Измените метод **RefreshAsync** для проверки подлинности, если свойство **User** равно NULL. Добавьте следующий код в верхнюю часть определения метода:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method

5. В среде Visual Studio или Xamarin Studio, подключенной к узлу сборки Xamarin на компьютере Macintosh, запустите клиентский проект, указав устройство или эмулятор. Убедитесь, что в приложении не отображаются данные.

	Обновите, потянув вниз список элементов, чтобы появился экран входа. После успешного ввода допустимых учетных данных в приложении отобразится список элементов задач и вы сможете внести изменения в данные.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Создание приложения Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->