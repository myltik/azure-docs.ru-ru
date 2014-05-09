<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Проверка подлинности при едином входе" pageTitle="Проверка подлинности приложения с использованием Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, sso мобильных служб, sso приложения Магазина Windows, Azure Javascript SSO" description="Использование единого входа Live Connect в мобильных службах Azure в приложении Магазина Windows." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Проверка подлинности приложения Магазина Windows с использованием единого входа Live Connect" authors="" solutions="" manager="" editor="" />




# Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>	


В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах приложения Магазина Windows.  В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.  

<div class="dev-callout"><b>Примечание.</b>
	<p>Этот учебник демонстрирует преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений Магазина Windows. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-js/">Приступая к работе с проверкой подлинности</a>. </p>
</div>

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Для работы с данным учебником требуется следующее:

+ [Live SDK для Windows]
+ Microsoft Visual Studio 2012 Express для Windows 8 RC или более поздней версии

Этот учебник основан на кратком руководстве по использованию мобильных служб. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для Магазина Windows</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо отправить свое приложение в Магазин Windows. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Отправить страницу приложения</a> в центре разработчиков для приложений Магазина Windows, выполните вход с использованием вашей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![][1]

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. В Visual Studio 2012 Express для Windows 8 откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами].

4. В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**. 

  	![][2]

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

6. Выберите приложение, которое зарегистрировано на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

   	![][3]

   	Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.    

7. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

8. Откройте вкладку **Панель мониторинга** и запишите значение **URL-адреса сайта**.

   	![][5]

    Это значение будет использоваться для определения домена перенаправления.

9. Перейдите на страницу <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Мои приложения</a> центра разработчиков Live Connect и щелкните свое приложение в списке **Мои приложения**.

   	![][6] 

10. Щелкните элемент **Изменить параметры**, затем **Параметры API** и запишите значения **Идентификатор клиента** и **Секрет клиента**. 

   	![][7]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе с вашим приложением.</p>
    </div>

11. В поле **Домен перенаправления** введите URL-адрес мобильной службы, указанный на шаге 8, и щелкните **Сохранить**.

12. На портале управления откройте вкладку **Удостоверение**, введите **Секрет клиента**, полученный из Магазина Windows, и нажмите кнопку **Сохранить**.

   	![][13]

Мобильная служба и приложение теперь настроены для работы с Live Connect.

<h2><a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений зарегистрированными пользователями</h2>

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Откройте вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает сценарии в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. В Visual Studio 2012 Express для Windows 8 откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами]. 

4. Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что возникает исключение с кодом состояния 401 (неавторизованный). 
   
   	Это происходит, потому что приложение обращается к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей с помощью Live Connect, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

1. Загрузите и установите пакет [Live SDK для Windows].

2. В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните узел **Windows**, щелкните элемент **Расширения**, установите флажок **Live SDK** и нажмите кнопку **ОК**. 

  	![][16]

  	Это добавляет в проект ссылку на Live SDK.

3. Откройте файл проекта default.html и добавьте следующий элемент &lt;скрипта&gt; в &lt;главный&gt; элемент. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Это включит Microsoft IntelliSense в файле default.html.


4. Откройте файл проекта default.js и добавьте следующий комментарий в начало файла. 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Это включит Microsoft IntelliSense в файле default.js.

5. В перезагрузке метода **app.OnActivated** замените вызов метода **refreshTodoItems** следующим кодом: 
	
        var session = null;   

        var logout = function () {
            return new WinJS.Promise(function (complete) {
                WL.getLoginStatus().then(function () {
                    if (WL.canLogout()) {
                        WL.logout(complete);                            
                    }
                    else {
                        complete();
                    }
                });
            });
        };                  

        var login = function () {
            return new WinJS.Promise(function (complete) {                    
                WL.login({ scope: "wl.basic"}).then(function (result) {
                    session = result.session;

                    WinJS.Promise.join([
                        WL.api({ path: "me", method: "GET" }),
                        client.login(result.session.authentication_token)
                    ]).done(function (results) {
                        var profile = results[0];
                        var mobileServicesUser = results[1];
                        refreshTodoItems();
                        var title = "Welcome " + profile.first_name + "!";
                        var message = "You are now logged in as: " + mobileServicesUser.userId;
                        var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                        dialog.showAsync().done(complete);                                
                    });                       
                }, function (error) {                        
                    session = null;
                    var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                    dialog.showAsync().done(complete);                        
                });
            });
        }

        var authenticate = function () {
            // Force a logout to make it easier to test with multiple Microsoft Accounts
            logout().then(login).then(function () {
                if (session === null) {
                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        WL.init({
            redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
        });           
            
        authenticate();

    При этом инициализируется клиент Live Connect, осуществляется принудительный выход из системы, в Live Connect высылается новый запрос на вход, в мобильные службы высылается возвратный маркер проверки подлинности, а затем отображается информация о вошедшем пользователе. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>Этот код выполняет по возможности принудительный выход, чтобы пользователь должен был вводить свои учетные данные при каждом запуске приложения. Это облегчает тестирование приложения с разными учетными записями Майкрософт в целях обеспечения надлежащей работы проверки подлинности. Этот механизм будет работать только в том случае, если у пользователя нет подключенной учетной записи Майкрософт.</p>
    </div>
	
7. Обновите строку _<< INSERT REDIRECT DOMAIN HERE >>_ из предыдущего шага с помощью домена перенаправления, который был указан при настройке приложения в Live Connect, используя для этого формат **https://_service-name_.azure-mobile.net/**.
		
8. Нажмите клавишу F5 для запуска приложения и войдите в Live Connect с использованием своей учетной записи Майкрософт. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью сценариев], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с аутентификацией].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js

[Портал управления Azure]: https://manage.windowsazure.com/

