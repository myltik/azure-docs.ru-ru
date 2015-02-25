<properties pageTitle="Аутентификация приложения с помощью Live Connect (JavaScript)" description="Узнайте, как использовать единый вход Live Connect в мобильных службах Azure из приложения Магазина Windows." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="11/22/2014" ms.author="glenga"/>

# Аутентификация приложения для Магазина Windows с помощью единого входа Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on/" title="Windows Store C#">Магазин Windows - C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-single-sign-on/" title="Windows Store JavaScript" class="current">Магазин Windows - JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-single-sign-on/" title="Windows Phone">Windows Phone</a>
</div>	


В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах приложения Магазина Windows.  В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.  

>[AZURE.NOTE]Этот учебник демонстрирует преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений Магазина Windows. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/">Приступая к работе с проверкой подлинности</a>.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1. [Регистрация приложения для проверки подлинности и настройка мобильных служб]
2. [Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]
3. [Добавление проверки подлинности в приложение]

Для работы с данным учебником требуется следующее:

+ [Live SDK для Windows]
+ Microsoft Visual Studio 2012 Express для Windows 8 RC или более поздней версии.
+ Кроме того, предварительно необходимо завершить учебник [Добавление мобильных служб к существующему приложению].

##<a name="register"></a>Регистрация приложения для Магазина Windows

Чтобы иметь возможность проверять подлинность пользователей, необходимо отправить свое приложение в Магазин Windows. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

##<a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 <a href="/ru-ru/documentation/articles/mobile-services-windows-store-get-started">Приступая к работе с мобильными службами</a>.</p></li> 
<li><p>Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).</p>
   
   	<p>Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица <em>TodoItem</em> теперь требует выполнения проверки подлинности.</p></li>
</ol>

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

1. Загрузите и установите пакет [Live SDK для Windows].

2. В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните узел **Windows**, щелкните элемент **Расширения**, установите флажок **Live SDK** и нажмите кнопку **ОК**. 

  	![][16]

  	Это добавляет в проект ссылку на Live SDK.

3. Откройте файл проекта default.html и добавьте следующий элемент &lt;script&gt; в элемент &lt;head&gt;. 

        <script src="///LiveSDKHTML/js/wl.js"></script>

   	Это включит Microsoft IntelliSense в файле default.html.


4. Откройте файл проекта default.js и добавьте следующий комментарий в начало файла. 

        /// <reference path="///LiveSDKHTML/js/wl.js" />

   	Это включит Microsoft IntelliSense в файле default.js.

5. В перегрузке метода **app.OnActivated** замените вызов метода **refreshTodoItems** следующим кодом: 
	
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

    При этом инициализируется клиент Live Connect, осуществляется принудительный выход из системы, в Live Connect отправляется новый запрос на вход, в мобильные службы отправляется возвращенный маркер аутентификации, а затем отображается информация о вошедшем пользователе. Этот код выполняет по возможности принудительный выход, чтобы пользователь должен был вводить свои учетные данные при каждом запуске приложения. Это облегчает тестирование приложение с разными учетными записями Майкрософт в целях обеспечение надлежащей работы проверки подлинности. Этот механизм будет работать только в том случае, если у пользователя нет подключенной учетной записи Майкрософт.

	>[AZURE.NOTE]Не нужно запрашивать маркеры аутентификации Live Connection или маркеры авторизации мобильных служб при каждом запуске приложения. Мало того, что это неэффективно, вы можете столкнуться с проблемами, связанными с использованием приложения при его одновременном запуске большим количеством клиентов. Лучше кэшировать маркеры и сначала попробовать использовать кэшированный маркер мобильных служб, прежде чем вызывать метод **LoginWithMicrosoftAccountAsync**. Пример кэширования маркера см. в разделе [Приступая к работе с проверкой подлинности](/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens)
	
7. Введите в строке _<< INSERT REDIRECT DOMAIN HERE >>_ из предыдущего шага домен перенаправления, который был указан при настройке приложения в Live Connect, в формате **https://_имя-службы_.azure-mobile.net/**.
		
8. Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect, используя свою учетную запись Майкрософт. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Информацию об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и настройка мобильных служб]: #register
[Ограничение разрешений таблицы для пользователей, прошедших проверку подлинности]: #permissions
[Добавление проверки подлинности в приложение]: #add-authentication
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
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Добавление мобильных служб к существующему приложению]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts/

[Портал управления Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
