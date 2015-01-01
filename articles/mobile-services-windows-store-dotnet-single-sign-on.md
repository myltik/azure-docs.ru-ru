<properties urlDisplayName="Authenticate with single sign-on" pageTitle="Аутентификация приложения для Магазина Windows с помощью Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />

# Аутентификация приложения для Магазина Windows с помощью единого входа Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#" class="current">C# в Магазине Windows</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">JavaScript в Магазине Windows</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	

В этом разделе показано, как использовать процедуру единого вход Live Connect для аутентификации пользователей в мобильных службах Azure из приложения для Магазина Windows или Windows Phone 8.1.  В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.  

>[WACOM.NOTE]Этот учебник демонстрирует преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений Магазина Windows. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Информацию о более общей аутентификации, поддерживающей несколько поставщиков проверки подлинности, см. в разделе <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/">Приступая к работе с аутентификацией</a>. 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1. [Регистрация приложения для аутентификации и настройка мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими аутентификацию]
3. [Добавление аутентификации в приложение]

Для работы с данным учебником требуется следующее:

+ [Live SDK для Windows]
+ Microsoft Visual Studio 2012 Express для Windows 8 RC или более поздней версии.
+ Также предварительно необходимо завершить учебник [Добавление мобильных служб для существующего приложения].

##<a name="register"></a>Регистрация приложения для Магазина Windows

Чтобы иметь возможность проверять подлинность пользователей, необходимо отправить свое приложение в Магазин Windows. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Откройте в Visual Studio 2012 Express для Windows 8 проект, созданный при прохождении учебника <a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший аутентификацию, а <em>TodoItem</em> таблица теперь требует прохождения аутентификации.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление аутентификации в приложение

1. Скачайте и установите пакет [Live SDK для Windows].

2. В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните узел **Windows**, щелкните **Расширения**, установите флажок **Live SDK** и нажмите кнопку **ОК**. 

  	![][16]

  	Это добавляет в проект ссылку на Live SDK.

5. Откройте файл проекта MainPage.xaml.cs и добавьте следующий оператор using:

        using Microsoft.Live;        

6. Добавьте в класс MainPage следующий фрагмент кода:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

            while (session == null)
            {
                // Force a logout to make it easier to test with multiple Microsoft Accounts
                if (liveIdClient.CanLogout)
                    liveIdClient.Logout();
	
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

    Это создает переменную-член для хранения текущего сеанса Live Connect и метод для обработки процесса проверки подлинности. Этот код выполняет по возможности принудительный выход, чтобы пользователь должен был вводить свои учетные данные при каждом запуске приложения. Это облегчает тестирование приложение с разными учетными записями Майкрософт в целях обеспечение надлежащей работы проверки подлинности. Этот механизм будет работать только в том случае, если у пользователя нет подключенной учетной записи Майкрософт. 

	>[WACOM.NOTE]Не нужно запрашивать маркеры аутентификации Live Connection или маркеры авторизации мобильных служб при каждом запуске приложения. Мало того, что это неэффективно, вы можете столкнуться с проблемами, связанными с использованием приложения при его одновременном запуске большим количеством клиентов. Лучше кэшировать маркеры и сначала попробовать использовать кэшированный маркер мобильных служб, прежде чем вызывать метод **LoginWithMicrosoftAccountAsync**. Пример кэширования маркера см. в разделе [Приступая к работе с аутентификацией](/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/#tokens)
	

7. Введите в строке << INSERT REDIRECT DOMAIN HERE >> из предыдущего шага домен перенаправления, который был указан при настройке приложения в Live Connect, в формате **https://_service-name_.azure-mobile.net/**.

    <div class="dev-callout"><b>Примечание.</b>
	<p>В приложении для Магазина Windows, экземпляр класса <strong>LiveAuthClient</strong> создается путем передачи значения универсального кода ресурса домена перенаправления конструктору классов. В <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-wp8/">приложении Windows Phone 8</a>этот же экземпляр класса создается путем передачи идентификатора клиента.</p>
    </div>

8. Замените существующий обработчик событий **OnNavigatedTo** на обработчик, вызывающий новый метод **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
9. Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect, используя свою учетную запись Майкрософт. 

   После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, который называется [Авторизация пользователей с помощью сценариев], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего аутентификацию, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Информацию об использовании других поставщиков удостоверений для аутентификации см. в разделе [Приступая к работе с аутентификацией]. Дополнительную информацию об использовании мобильных служб с помощью .NET см. в [справочнике по принципам использования мобильных служб .NET.]

<!-- Anchors. -->
[Регистрация приложения для аутентификации и настройка мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими аутентификацию]: #permissions
[Добавление аутентификации в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png

<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Добавление мобильных служб в существующее приложение]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Авторизация пользователей с помощью сценариев]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=35_1-->
