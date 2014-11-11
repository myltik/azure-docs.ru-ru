<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Аутентификация приложения для Windows Phone 8 с помощью единого входа Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash; JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

В этом разделе показано, как использовать единый вход Live Connect для проверки подлинности пользователей в мобильных службах Azure из приложения Windows Phone 8. В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.

<div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике показаны преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений для Windows Phone. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8/">Приступая к работе с проверкой подлинности</a>. </p>
</div>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]

Для работы с данным учебником требуется следующее:

-   [Live SDK для Windows и Windows Phone][Live SDK для Windows и Windows Phone]
-   Microsoft Visual Studio 2012 Express для Windows Phone.

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения с помощью Live Connect

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать свое приложение в центре разработчиков Live Connect. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу.

    ![][0]

2.  Откройте вкладку **Панель мониторинга** и запишите значение **URL-адрес сайта**.

    ![][1]

    Это значение будет использоваться для определения домена перенаправления.

3.  Перейдите к странице [Мои приложения][Мои приложения] в Центре разработки Live Connect и, если необходимо, выполните вход с использованием учетной записи Майкрософт.

4.  Щелкните элемент **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Я принимаю**.

    ![][2]

    Эта процедура регистрирует приложение в Live Connect.

5.  Щелкните элемент **Страница параметров приложения**, затем **Параметры API** и запишите значения **Идентификатор клиента** и **Секрет клиента**.

    ![][3]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента &mdash; это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе со своим приложением.</p>
</div>

6.  В поле **Домен перенаправления**, введите URL-адрес мобильной службы из шага 2, нажмите кнопку **Да** в разделе **Мобильное клиентское приложение**, а затем нажмите кнопку **Сохранить**.

7.  На портале управления откройте вкладку **Удостоверение**, введите **Секрет клиента**, полученный из Live Connect, а затем нажмите кнопку **Сохранить**.

    ![][4]

Мобильная служба и приложение теперь настроены для работы с Live Connect.

## <a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений для аутентифицированных пользователей

1.  На портале управления нажмите вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][5]

2.  Щелкните вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает сценарии в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

    ![][6]

3.  Откройте в Visual Studio 2012 Express для Windows Phone проект, созданный во время прохождения учебника[Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

4.  Нажмите клавишу F5, чтобы запустить это приложение быстрого запуска. Убедитесь, что возникает исключение с кодом состояния «401 (не санкционировано)».

    Это происходит, потому что приложение обращается к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей с помощью Live Connect, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a><span class="short-header">Добавление аутентификации</span>Добавление аутентификации в приложение

1.  Скачайте и установите пакет [Live SDK для Windows и Windows Phone][Live SDK для Windows и Windows Phone].

2.  В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните раздел **Сборки**, щелкните пункт **Расширения**, установите флажок возле параметра **Microsoft.Live** и нажмите кнопку **OK**.

    ![][7]

    Это добавляет в проект ссылку на Live SDK.

3.  Откройте файл проекта mainpage.xaml.cs и добавьте следующие инструкции using:

        using Microsoft.Live;      

4.  Добавьте в класс MainPage следующий фрагмент кода:

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

5.  Укажите в строке *\<\< INSERT CLIENT ID HERE \>\>* из предыдущего шага значение идентификатора клиента, созданное во время регистрации приложения в Live Connect.

    <div class="dev-callout"><b>Примечание.</b>
<p>В приложении Windows Phone 8 экземпляр класса <strong>LiveAuthClient</strong> создается путем передачи значения идентификатора клиента в конструктор классов. В <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/">приложении для Магазина Windows</a> экземпляр того же класса создается путем передачи универсального кода ресурса (URI) домена перенаправления.</p>
</div>

6.  Удалите или закомментируйте существующее переопределение метода **OnNavigatedTo** и замените его на следующий метод, который обрабатывает событие **Loaded** для страницы.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Этот метод вызывает новый метод **Authenticate**.

7.  Замените конструктор MainPage следующим кодом:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Этот конструктор также регистрирует обработчик для события Loaded.

8.  Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect, используя свою учетную запись Майкрософт.

    После успешного входа в систему приложение работает без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов][Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности][8].

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8/
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Live SDK для Windows и Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
