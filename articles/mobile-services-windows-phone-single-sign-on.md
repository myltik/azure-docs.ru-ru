<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Проверка подлинности с помощью единого входа" pageTitle="Проверка подлинности приложения с помощью Live Connect (Windows Phone) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как использовать единый вход Live Connect в мобильных службах Azure из приложения Windows Phone." metaCanonical="" services="" documentationCenter="Mobile" title="Проверка подлинности приложения Windows Phone 8 с помощью единого входа Live Connect" authors="glenga" solutions="" manager="" editor="" />




# Проверка подлинности приложения Windows Phone 8 с помощью единого входа Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

В этом разделе показано, как использовать единый вход Live Connect для проверки подлинности пользователей в мобильных службах Azure из приложения Windows Phone 8.  В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.  

<div class="dev-callout"><b>Примечание.</b>
	<p>Этот учебник демонстрирует преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений Windows Phone. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8/">Приступая к работе с аутентификацией</a>. </p>
</div>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Для работы с данным учебником требуется следующее:

+ [Live SDK для Windows и Windows Phone]
+ Microsoft Visual Studio 2012 Express для Windows Phone

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения с помощью Live Connect</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать свое приложение в центре разработчиков Live Connect. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

2. Откройте вкладку **Панель мониторинга** и запишите значение **URL-адрес сайта**.

   	![][5]

    Это значение будет использоваться для определения домена перенаправления.

3. Перейдите на страницу <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Мои приложения</a> центра разработчиков Live Connect и при необходимости войдите в систему с использованием своей учетной записи Майкрософт. 

4. Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

   	![][1] 

   	Эта процедура регистрирует приложение в Live Connect.

5. Щелкните элемент **Страница параметров приложения**, затем **Параметры API** и запишите значения **Идентификатор клиента** и **Секрет клиента**. 

   	![][2]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе с вашим приложением.</p>
    </div>

6. В поле **Домен перенаправления** введите URL-адрес мобильной службы из шага 2, нажмите кнопку **Да** в области **Мобильное клиентское приложение** и нажмите кнопку **Сохранить**.

7. На портале управления откройте вкладку **Удостоверение**, введите **Секрет клиента**, полученный из Live Connect, и нажмите кнопку **Сохранить**.

   	![][13]

Мобильная служба и приложение теперь настроены для работы с Live Connect.

<h2><a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений зарегистрированными пользователями</h2>

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Откройте вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает скрипты в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. Откройте проект, созданный при прохождении учебника [Приступая к работе с мобильными службами], в Visual Studio 2012 Express для Windows Phone. 

4. Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что возникает исключение с кодом состояния 401 (неавторизованный). 
   
   	Это происходит, потому что приложение обращается к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей с помощью Live Connect, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

1. Загрузите и установите [Live SDK для Windows и Windows Phone]

2. В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните узел **Сборки**, щелкните элемент **Расширения**, установите флажок **Microsoft.Live** и нажмите кнопку **ОК**. 

   	![][16]

  	Это добавляет в проект ссылку на Live SDK.

5. Откройте файл проекта mainpage.xaml.cs и добавьте следующие операторы "using":

        using Microsoft.Live;      

6. Добавьте в класс MainPage следующий фрагмент кода:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

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

    Это создает переменную-член для хранения текущего сеанса Live Connect и метод для обработки процесса проверки подлинности.

7. Обновите строку _<< INSERT CLIENT ID HERE >>_ из предыдущего шага со значением идентификатора клиента, созданным при регистрации приложения в Live Connect.

    <div class="dev-callout"><b>Примечание.</b>
	<p>В приложении Windows Phone 8 экземпляр класса <strong>LiveAuthClient</strong> создается путем передачи значения идентификатора клиента в конструктор классов. В <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">приложении Магазина Windows</a> экземпляр того же класса создается посредством передачи URI домена перенаправления.</p>
    </div>

8. Удалите или закомментируйте существующее переопределение метода **OnNavigatedTo** и замените его на следующий метод, который обрабатывает событие **Loaded** для страницы. 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	Этот метод вызывает новый метод **Authenticate**. 

9. Замените конструктор MainPage следующим кодом:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	Этот конструктор также регистрирует обработчик для события Loaded.
		
9. Нажмите клавишу F5 для запуска приложения и войдите в Live Connect с использованием своей учетной записи Майкрософт. 

   	После успешного входа в систему приложение работает без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с аутентификацией]. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png





[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows и Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Портал управления Azure]: https://manage.windowsazure.com/

