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
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Аутентификация приложения для Windows Phone 8 с помощью единого входа Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Магазин Windows - C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript">Магазин Windows - JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

В этом разделе показано, как использовать единый вход Live Connect для проверки подлинности пользователей в мобильных службах Azure из приложения Windows Phone 8.  В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.  

>[AZURE.NOTE]В этом учебнике показаны преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений для Windows Phone. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users">Добавление проверки подлинности в приложение</a>. 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Для работы с данным учебником требуется следующее:

+ [Live SDK для Windows и Windows Phone]
+ Microsoft Visual Studio 2012 Express для Windows Phone.
+ Кроме того, предварительно необходимо завершить учебник [Добавление мобильных служб к существующему приложению].

<h2><a name="register"></a>Регистрация приложения в Live Connect</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать свое приложение в центре разработчиков Live Connect. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

2. Откройте вкладку **Панель мониторинга** и запишите значение **URL-адрес сайта**.

   	![][5]

    Это значение будет использоваться для определения домена перенаправления.

3. Перейдите к странице <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Мои приложения</a> в Центре разработки Live Connect и, если необходимо, выполните вход с использованием учетной записи Майкрософт. 

4. Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

   	![][1] 

   	Эта процедура регистрирует приложение в Live Connect.

5. Щелкните элемент **Страница параметров приложения**, затем **Параметры API** и запишите значения **Идентификатор клиента** и **Секрет клиента**. 

   	![][2]

 > [AZURE.NOTE] **Примечание по безопасности** Секрет клиента - это важные учетные данные безопасности.. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.

6. В поле **Домен перенаправления** введите URL-адрес мобильной службы из шага 2, нажмите кнопку **Да** в области **Мобильное клиентское приложение** и нажмите кнопку **Сохранить**.

7. На портале управления откройте вкладку **Удостоверение**, введите **Секрет клиента**, полученный из Live Connect, и нажмите кнопку **Сохранить**.

   	![][13]

Мобильная служба и приложение теперь настроены для работы с Live Connect.

<h2><a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности</h2>

1. На портале управления нажмите вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Щелкните вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает сценарии в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. В Visual Studio 2012 Express для Windows Phone откройте проект, созданный после завершения учебника [Добавление мобильных служб к существующему приложению]. 

4. Нажмите клавишу F5, чтобы запустить это приложение быстрого запуска. Убедитесь, что возникает исключение с кодом состояния "401 (не санкционировано)". 
   
   	Это происходит, потому что приложение обращается к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей с помощью Live Connect, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a>Добавление проверки подлинности в приложение</h2>

1. Загрузите и установите [Live SDK для Windows и Windows Phone].

2. В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните узел **Сборки**, щелкните элемент **Расширения**, установите флажок **Microsoft.Live** и нажмите кнопку **ОК**. 

   	![][16]

  	Это добавляет в проект ссылку на Live SDK.

5. Откройте файл проекта mainpage.xaml.cs и добавьте следующие инструкции using:

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

7. Укажите в строке _<< INSERT CLIENT ID HERE >>_ из предыдущего шага значение идентификатора клиента, созданное во время регистрации приложения в Live Connect.

    > [AZURE.NOTE] В приложении Windows Phone 8 экземпляр класса **LiveAuthClient** создается путем передачи значения идентификатора клиента в конструктор классов. В [приложении Магазина Windows](/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/) экземпляр того же класса создается посредством передачи URI домена перенаправления.

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
		
9. Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect, используя свою учетную запись Майкрософт. 

   	После успешного входа в систему приложение работает без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Информацию об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности]. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png

[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-per
	ms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows и Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Добавление мобильных служб к существующему приложению]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/

[Портал управления Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
