<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Аутентификация приложения для Магазина Windows с помощью единого входа Live Connect

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Магазин Windows &mdash; C#">Магазин Windows &mdash; C#</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Магазин Windows &mdash; JavaScript" class="current">Магазин Windows &mdash; JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах приложения Магазина Windows. В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью Live Connect. После успешного прохождения проверки подлинности в Live Connect вошедший в систему пользователь приветствуется по имени и отображается значение идентификатора пользователя.

> [WACOM.NOTE]В этом учебнике показаны преимущества использования процедуры единого входа, предоставляемой Live Connect для приложений для Магазина Windows. Это позволяет легче проверять подлинность уже вошедшего в систему пользователя с помощью мобильной службы. Сведения о более общей проверке подлинности, поддерживающей несколько поставщиков услуг проверки подлинности, см. в разделе [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности].

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности Live Connect:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]

Для работы с данным учебником требуется следующее:

-   [Live SDK для Windows][Live SDK для Windows]
-   Microsoft Visual Studio 2012 Express для Windows 8 RC или более поздней версии.

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

> [WACOM.NOTE]В этом учебнике будет использоваться серверная мобильная служба JavaScript. Серверная мобильная служба .NET на данный момент не поддерживает управляемую клиентом аутентификацию с помощью Live Connect, а также других клиентов для проверки подлинности.

## <a name="register"></a>Регистрация приложения для Магазина Windows

Чтобы иметь возможность проверять подлинность пользователей, необходимо отправить свое приложение в Магазин Windows. Затем необходимо зарегистрировать секрет клиента для интеграции Live Connect с мобильными службами.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Откройте проект, созданный после завершения обучения, в Visual Studio 2012 Express для Windows 8 [Приступая к работе с мобильными службами][1].

2.  Нажмите клавишу F5 для запуска этого приложения на основе быстрого запуска; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (неавторизованный).

    Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

1.  Скачайте и установите пакет [Live SDK для Windows][Live SDK для Windows].

2.  В меню **Проект** в Visual Studio выберите пункт **Добавить ссылку**, разверните раздел **Windows**, щелкните пункт **Расширения**, установите флажок возле параметра **Live SDK** и нажмите кнопку **OK**.

    ![][0]

    Это добавляет в проект ссылку на Live SDK.

3.  Откройте файл проекта default.html и добавьте следующий элемент \<script\> в элемент \<head\>.

        <script src="///LiveSDKHTML/js/wl.js"></script>

    Это включит Microsoft IntelliSense в файле default.html.

4.  Откройте файл проекта default.js и добавьте следующий комментарий в начало файла.

        /// <reference path="///LiveSDKHTML/js/wl.js" />

    Это включит Microsoft IntelliSense в файле default.js.

5.  В перезагрузке метода **app.OnActivated** замените вызов метода **refreshTodoItems** следующим кодом:

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

    > [WACOM.NOTE]Не нужно запрашивать маркеры аутентификации Live Connection или маркеры авторизации мобильных служб при каждом запуске приложения. Мало того, что это неэффективно, вы можете столкнуться с проблемами, связанными с использованием приложения при его одновременном запуске большим количеством клиентов. Лучше кэшировать маркеры и сначала попробовать использовать кэшированный маркер мобильных служб, прежде чем вызывать метод **LoginWithMicrosoftAccountAsync**. Пример кэширования маркера см. в разделе [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

6.  Введите в строке *\<\< INSERT REDIRECT DOMAIN HERE \>\>* из предыдущего шага домен перенаправления, который был указан при настройке приложения в Live Connect, в формате **https://_service-name_.azure-mobile.net/**.

7.  Нажмите клавишу F5, чтобы запустить приложение, и войдите в Live Connect, используя свою учетную запись Майкрософт.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов][Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании других поставщиков удостоверений для проверки подлинности см. в разделе [Приступая к работе с проверкой подлинности][2].

 
 


  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started
  [1]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
  [0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/#tokens
  [Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
  [2]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
