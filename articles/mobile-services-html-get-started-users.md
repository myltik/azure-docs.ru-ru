<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Приступая к работе с проверкой подлинности в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять аутентификацию пользователей в мобильных службах Azure из приложения на HTML или приложения PhoneGap. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1.  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб][Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2.  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности][Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3.  [Добавление проверки подлинности к приложению][Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>Ограничение разрешений для пользователей, прошедших проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Запустите в подпапке **server** каталога приложения один из следующих файлов команд.

    -   **launch-windows** (компьютеры с ОС Windows)
    -   **launch-mac.command** (компьютеры с ОС Mac OS X)
    -   **launch-linux.sh** (компьютеры с ОС Linux)

    > [WACOM.NOTE]На компьютере с операционной системой Windows в ответ на запрос PowerShell на запуск сценария нажмите клавишу `R`. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.

    Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2.  Откройте URL-адрес [http://localhost: 8000/][http://localhost: 8000/] в веб-браузере, чтобы запустить приложение.

    Данные не загружаются. Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

3.  (Необязательно) Откройте отладчик сценариев для вашего веб-браузера и обновите страницу. Убедитесь, что возникает ошибка отказа в доступе.

Далее необходимо обновить приложение для разрешения проверки подлинности до запроса ресурсов из мобильной службы.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение

> [WACOM.NOTE]Так как вход выполняется во всплывающем окне, необходимо вызвать метод **login** по событию нажатия кнопки. В противном случае многие браузеры отключают окно входа.

1.  Откройте файл index.html проекта, найдите элемент H1 и добавьте в него следующий фрагмент кода:

        <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
        </div>

    Это позволит выполнить вход в мобильные службы с этой страницы.

2.  В файле app.js найдите строку кода, расположенную в конце файла, которая вызывает функцию refreshTodoItems, и замените ее на следующий код:

        function refreshAuthDisplay() {
            var isLoggedIn = client.currentUser !== null;
            $("#logged-in").toggle(isLoggedIn);
            $("#logged-out").toggle(!isLoggedIn);
            if (isLoggedIn) {
                $("#login-name").text(client.currentUser.userId);
                refreshTodoItems();
            }
        }
        function logIn() {
            client.login("facebook").then(refreshAuthDisplay, function(error){
                alert(error);
            });
        }
        function logOut() {
            client.logout();
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');
        }
        // On page init, fetch the data and set up event handlers
        $(function () {
            refreshAuthDisplay();
            $('#summary').html('<strong>You must login to access data.</strong>');          
            $("#logged-out button").click(logIn);
            $("#logged-in button").click(logOut);
        });

    Это приведет к созданию набора функций для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в методе **login** выше, на одно из следующих: *microsoftaccount*, *facebook*, *twitter*, *google* или *aad*.

    > [WACOM.NOTE]В приложении PhoneGap также необходимо добавить в проект следующие модули:
    >
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git`
    > -   `phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git`
    >
    > </p>

3.  Вернитесь в браузер, где работает приложение, и обновите страницу.

    После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

    > [WACOM.NOTE]В Internet Explorer после входа может появиться следующая ошибка: `Cannot reach window opener. It may be on a different Internet Explorer zone`. Это происходит из-за того, что всплывающие окна работают в зоне безопасности «Интернет», отличной от зоны безопасности локального узла (интрасеть). Это влияет только на приложения во время разработки с использованием локального узла localhost. Чтобы избежать этого, откройте вкладку **Безопасность** в **Свойствах обозревателя**, щелкните **Местная интрасеть**, затем **Узлы** и отключите параметр **Автоматически определять принадлежность к интрасети** После тестирования не забудьте вернуть предыдущее значение этого параметра.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов][Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью HTML/JavaScript см. в разделе [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript].



  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
  [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
  [Добавление проверки подлинности к приложению]: #add-authentication
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-html-get-started
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [http://localhost: 8000/]: http://localhost:8000/
  [Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
