<properties 
	pageTitle="Аутентификация приложения для Магазина Windows с помощью Live Connect" 
	description="Узнайте, как использовать единый вход Live Connect в мобильных службах Azure из приложения Магазина Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# Управляемая клиентом проверка подлинности приложения Магазина Windows с использованием учетной записи Майкрософт

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##Обзор
В этом разделе показано, как получить маркер проверки подлинности для учетной записи Майкрософт, используя пакет Live SDK из универсального приложения Windows. Затем этот маркер используется для проверки подлинности пользователей с помощью мобильных служб Azure. В этом учебнике возможности проверки подлинности учетных записей Майкрософт добавляются в существующий проект с помощью пакета Live SDK. После успешного прохождения проверки подлинности вошедший в систему пользователь приветствуется по имени. Кроме того, отображается значение идентификатора пользователя.

>[AZURE.NOTE]В этом учебнике показаны преимущества использования контролируемой клиентом проверки подлинности и пакета Live SDK. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Можно также запросить дополнительные области, чтобы приложение могло получить доступ к таким ресурсам, как OneDrive. Контролируемая службой проверка подлинности поддерживает несколько поставщиков проверки подлинности, поэтому отличается большей универсальностью. Дополнительные сведения о контролируемой службой проверке подлинности см. в статье [Добавление проверки подлинности в приложение](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Для работы с данным учебником требуется следующее:

+ [Пакет Live SDK]
+ Microsoft Visual Studio 2013 с обновлением 3 или более поздней версии
+ Предварительное выполнение заданий учебника [Приступая к работе с мобильными службами](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) или [Добавление мобильных служб в существующее приложение]

##Регистрация приложения для использования учетной записи Майкрософт с целью проверки подлинности.

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать свое приложение в центре разработчиков учетных записей Майкрософт. Затем зарегистрированное приложение необходимо подключить к своей мобильной службе. Выполните действия, указанные в следующем разделе, чтобы зарегистрировать приложение для использования учетной записи Майкрософт и подключить его к своей мобильной службе.

+ [Регистрация приложения для входа с использованием учетной записи Майкрософт](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

Далее необходимо ограничить доступ к ресурсу (в этом случае — таблице *TodoItems*), чтобы доступ к ней могли получать только пользователи, которые выполнили вход.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

Наконец, добавьте пакет Live SDK и используйте его для проверки подлинности пользователей в приложении.

1. Щелкните решение правой кнопкой мыши в **обозревателе решений**, а затем выберите пункт **Управление пакетами NuGet**.

2. В левой области выберите категорию **В сети**, найдите пункт **LiveSDK**, щелкните **Установить** для пакета **Live SDK**, выберите все проекты, а затем примите условия лицензионных соглашений.

  	Пакет Live SDK будет добавлен в решение.

3. Откройте общий файл проекта MainPage.xaml.cs и добавьте следующую инструкцию using:

        using Microsoft.Live;        

4. Добавьте в класс **MainPage** следующий фрагмент кода:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    Это создает переменную-член для хранения текущего сеанса Live Connect и метод для обработки процесса проверки подлинности.

	>[AZURE.NOTE]Попробуйте использовать кэшированные маркеры проверки подлинности Live Connect или маркеры проверки подлинности мобильных служб, прежде чем запрашивать новые маркеры из служб. Если не сделать этого, вы можете столкнуться с проблемами, связанными с использованием приложения при его одновременном запуске большим количеством клиентов. Пример кэширования маркера см. в разделе [Приступая к работе с аутентификацией](../mobile-services-windows-store-dotnet-get-started-users.md#tokens).

5. Закомментируйте или удалите вызов метода **RefreshTodoItems** в существующем переопределении метода **OnNavigatedTo**.

	Это позволяет предотвратить загрузку данных до того, как пользователь прошел проверку подлинности. Далее добавим кнопку, чтобы начать процесс входа в систему.

6. Добавьте в класс MainPage следующий фрагмент кода:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. В проекте приложения Магазина Windows откройте файл проекта MainPage.xaml и добавьте следующий элемент **Button** непосредственно перед элементом, определяющим кнопку **Сохранить**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Повторите предыдущий шаг для проекта приложения Магазина Windows Phone, но на этот раз добавьте элемент **Button** в **TitlePanel** после элемента **TextBlock**.
		
9. Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect с учетной записи Майкрософт.

	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

10. Щелкните правой кнопкой мыши проект приложения Магазина Windows Phone, выберите пункт **Назначить запускаемым проектом**, а затем повторите предыдущий шаг, чтобы проверить, правильно ли запускается приложение Магазина Windows Phone.

Теперь любой пользователь, который прошел проверку подлинности с использованием одного из зарегистрированных поставщиков удостоверений, может получить доступ к таблице *TodoItem*. Для более надежной защиты пользовательских данных необходимо также реализовать авторизацию. Для этого нужно получить идентификатор конкретного пользователя, который затем можно использовать для определения уровня доступа пользователя к указанному ресурсу.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности]. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Добавление мобильных служб в существующее приложение]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Приступая к работе с проверкой подлинности]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Авторизация пользователей с помощью скриптов]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Пакет Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=August15_HO6-->