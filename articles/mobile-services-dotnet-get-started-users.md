<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Приступая к работе с пользователями" pageTitle="Приступая к работе с аутентификацией (магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб для проверки подлинности пользователей приложения магазина Windows с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с аутентификацией в мобильных службах" authors=""  solutions="" writer="" manager="" editor=""  />




# Приступая к работе с аутентификацией в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>
<p>Можно смотреть видеоверсию данного учебника, щелкнув клип справа.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">10:04</span></div>
</div> 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

<div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике показан базовый метод, предоставляемый мобильными службами для проверки подлинности пользователей с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. В то же время этот метод также требует от пользователей осуществлять вход каждый раз при запуске приложения. Сведения о том, как вместо этого использовать Live Connect для единого входа в приложение для магазина Windows, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet">Единый вход для приложений для магазина Windows с использованием Live Connect</a>.</p>
</div>

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать приложение у поставщика удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1. Выполните вход на [Портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

2. Щелкните вкладку **Панель мониторинга** и запишите значение **URL-адрес мобильной службы**.

   	![][5]

    От вас может потребоваться предоставить это значение поставщику удостоверений при регистрации приложения.

3. Выберите поддерживаемого поставщика удостоверений из приведенного ниже списка и выполните действия для регистрации приложения у этого поставщика:

 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Учетная запись Майкрософт</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Вход в Facebook</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Вход в Twitter</a>
 - <a href="/ru-ru/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Вход в Google</a>
 - <a href="/ru-ru/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    Не забудьте записать удостоверение клиента и значения секрета, созданные поставщиком.

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет, созданный поставщиком, входит в важные учетные данные безопасности. Не сообщайте никому этот секрет и не распространяйте его вместе с вашим приложением.</p>
    </div>

4. На портале управления нажмите вкладку **Удостоверение**, введите идентификатор приложения и общие значения секрета, полученные у поставщика удостоверений, а затем щелкните **Сохранить**.

   	![][13]

5. (Необязательно) Выполните шаги, указанные в пункте [Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft].

    <div class="dev-callout"><b>Примечание.</b>
	<p>Этот шаг является необязательным, так как он применяется только к поставщику входа учетной записи Майкрософт. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Выполните этот шаг, если будет использоваться поставщик удостоверений учетной записи Майкрософт.</p>
    </div>

Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

<h2><a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений зарегистрированными пользователями</h2>

1. На портале управления нажмите вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Щелкните вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает скрипты в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 [Приступая к работе с мобильными службами]. 

4. Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный). 
   
   	Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

5. Откройте файл проекта mainpage.xaml.cs и добавьте следующий оператор "using":

        using Windows.UI.Popups;

6. Добавьте в класс MainPage следующий фрагмент кода:
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Это создает переменную-член для хранения текущего пользователя и метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение **MobileServiceAuthenticationProvider** выше на значение для вашего поставщика.

    <div class="dev-callout"><b>Примечание.</b>
	<p>При регистрации данных пакета приложения магазина Windows с помощью мобильных служб необходимо вызвать метод <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>, указав значение <strong>true</strong> для параметра <em>useSingleSignOn</em>. Если этого не сделать, пользователям придется осуществлять вход в систему при каждом вызове метода входа в систему.</p>
    </div>

8. Замените существующий метод **OnNavigatedTo**, переопределенный с помощью следующего метода, который вызывает новый метод **Authenticate**:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
		
9. Нажмите клавишу F5 для запуска приложения и войдите в приложение с выбранным вами поставщиком удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью .NET см. в разделе [Справочник принципов использования мобильных служб .NET].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Следующие шаги]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
[Регистрация пакета приложения магазина Windows для проверки подлинности Microsoft]: /ru-ru/develop/mobile/how-to-guides/register-windows-store-app-package

