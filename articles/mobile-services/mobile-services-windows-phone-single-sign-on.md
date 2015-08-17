<properties 
	pageTitle="Проверка подлинности приложения с помощью Live Connect (Windows Phone) | Центр мобильных разработок" 
	description="Узнайте, как использовать единый вход Live Connect в мобильных службах Azure из приложения Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Управляемая клиентом проверка подлинности приложения Windows Phone с использованием учетной записи Майкрософт

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##Обзор
В этом разделе показано, как получить маркер проверки подлинности для учетной записи Майкрософт, используя пакет Live SDK из приложения Silverlight для Windows Phone 8 или Windows Phone 8.1. Затем этот маркер используется для проверки подлинности пользователей с помощью мобильных служб Azure. В этом учебнике возможности проверки подлинности учетных записей Майкрософт добавляются в существующий проект с помощью пакета Live SDK. После успешного прохождения проверки подлинности вошедший в систему пользователь приветствуется по имени. Кроме того, отображается значение идентификатора пользователя.

>[AZURE.NOTE]В этом учебнике показаны преимущества использования управляемой клиентом проверки подлинности и пакета Live SDK. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Можно также запросить дополнительные области, чтобы приложение могло получить доступ к таким ресурсам, как OneDrive. Управляемая службой проверка подлинности поддерживает несколько поставщиков проверки подлинности, поэтому отличается большей универсальностью. Дополнительные сведения об управляемой службе проверки подлинности см. в статье [Добавление проверки подлинности в приложение](mobile-services-windows-phone-get-started-users.md).

Для работы с данным учебником требуется следующее:

+ [Пакет Live SDK]
+ Microsoft Visual Studio 2013 с обновлением 3 или более поздней версии
+ Предварительное выполнение заданий учебника [Добавление мобильных служб в существующее приложение]

##Регистрация приложения для использования учетной записи Майкрософт 

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

5. Откройте файл проекта mainpage.xaml.cs и добавьте следующие инструкции using:

        using Microsoft.Live;      

6. Добавьте в класс MainPage следующий фрагмент кода:
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    Это создает переменную-член для хранения текущего сеанса Live Connect и метод для обработки процесса проверки подлинности. LiveLoginResult содержит маркер проверки подлинности, отправляемый в мобильные службы для проверки подлинности пользователя.

7. В приведенном выше фрагменте кода замените заполнитель `<microsoft-account-client-id>` идентификатором клиента, полученным после регистрации учетной записи Майкрософт для вашего приложения.

5. Закомментируйте или удалите вызов метода **RefreshTodoItems** в существующем переопределении метода **OnNavigatedTo**.

	Это позволяет предотвратить загрузку данных до того, как пользователь прошел проверку подлинности. Далее добавим кнопку, чтобы начать процесс входа в систему.

6. Добавьте в класс MainPage следующий фрагмент кода:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. В проекте приложения откройте файл проекта MainPage.xaml и добавьте следующий элемент **Button** в **TitlePanel** после элемента **TextBlock**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. Нажмите клавишу F5, чтобы запустить приложение, и войдите в систему с помощью учетной записи Майкрософт.

   После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

Теперь любой пользователь, который прошел проверку подлинности с использованием одного из зарегистрированных поставщиков удостоверений, может получить доступ к таблице *TodoItem*. Для более надежной защиты пользовательских данных необходимо также реализовать авторизацию. Для этого нужно получить идентификатор конкретного пользователя, который затем можно использовать для определения уровня доступа пользователя к указанному ресурсу.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Пакет Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Добавление мобильных служб в существующее приложение]: mobile-services-windows-phone-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-windows-phone-get-started-users.md
[Авторизация пользователей с помощью скриптов]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->