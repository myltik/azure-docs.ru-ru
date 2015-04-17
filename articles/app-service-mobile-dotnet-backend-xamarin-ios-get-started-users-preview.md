<properties 
	pageTitle="Начало работы с проверкой подлинности для мобильных приложений в Xamarin iOS" 
	description="Использование мобильных приложений для проверки подлинности пользователей приложения Xamarin iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Добавление проверки подлинности в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

В этом разделе показано, как выполнить проверку подлинности пользователей мобильного приложения службы приложений из клиентского приложения. В этом учебнике вы добавите проверку подлинности в проект краткого руководства, используя поставщика удостоверений, поддерживаемого службой приложений. После успешной проверки подлинности и авторизации мобильным приложением отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным приложениям. Необходимо также сначала пройти учебник [Создание приложения Xamarin.iOS]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка служб приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>В Visual Studio или Xamarin Studio запустите проект клиента на устройстве или имитаторе. Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это вызвано тем, что приложение пытается получить доступ к коду мобильного приложения от имени не прошедшего проверку подлинности пользователя, но таблице <em>TodoItem</em> теперь требуется проверка подлинности.</p></li>
</ol>

Далее вы обновите приложение, чтобы оно выполняло проверку подлинности пользователей перед запросом ресурсов из службы приложений.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

В этом разделе предстоит изменить приложение для отображения экрана входа до отображения данных. При запуске приложение не подключится к службе приложений и не отобразит никаких данных. После первого обновления пользователем появится экран входа; список задач появится после успешного входа.

1. В клиентском проекте откройте файл **QSTodoService.cs** и добавьте следующие объявления в QSTodoService:

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

> [AZURE.NOTE] Если вы используете поставщика удостоверений, отличного от Facebook, измените значение, передаваемое в **LoginAsync** выше, на одно из следующих: _MicrosoftAccount_, _Twitter_, _Google_ или _WindowsAzureActiveDirectory_.

3. Откройте **QSTodoListViewController.cs**. Измените определение метода **ViewDidLoad**, чтобы удалить вызов в **RefreshAsync()** в конце:

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


4. Измените метод **RefreshAsync**, чтобы выполнить проверку подлинности и отобразить экран входа, если свойство **User** равно NULL. В следующем коде, расположенном в верхней части определения метода, выполните следующие действия.

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Нажмите кнопку **Запустить**, чтобы создать проект и запустить приложение в имитаторе iPhone. Убедитесь, что в приложении не отображаются данные. 

	Обновите, потянув вниз список элементов, чтобы появился экран входа. После успешного ввода допустимых учетных данных в приложении отобразится список элементов задач, и вы сможете внести изменения в данные.

 
<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Создание приложения Xamarin.iOS]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Портал управления Azure]: https://portal.azure.com

<!--HONumber=49-->