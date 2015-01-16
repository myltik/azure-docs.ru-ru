<properties urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Приступая к работе с проверкой подлинности (HTML 5) | Центр мобильных разработок" metaKeywords="" description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения HTML с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Добавление проверки подлинности к приложению мобильных служб 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей мобильных служб Azure в приложениях HTML или PhoneGap.  В этом учебнике вы научитесь добавлять проверку подлинности в проект быстрого запуска с помощью поставщика удостоверений, поддерживаемого мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник создан на основе краткого руководства по мобильным службам. Необходимо прежде всего пройти учебник [Начало работы с мобильными службами]. 

##<a name="register"></a>Регистрация приложения для проверки подлинности и конфигурация мобильных служб

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Запустите в подпапке **server** каталога приложения один из следующих командных файлов.

	+ **launch-windows** (компьютеры с ОС Windows) 
	+ **launch-mac.command** (компьютеры с ОС Mac OS X)
	+ **launch-linux.sh** (компьютеры с ОС Linux)

	>[WACOM.NOTE]На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск скрипта. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку скрипта.

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> в веб-браузере, чтобы запустить приложение. 

	Данные не загружаются. Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

3. (Необязательно) Откройте отладчик сценариев для вашего веб-браузера и обновите страницу. Убедитесь, что возникает ошибка отказа в доступе. 

Далее необходимо обновить приложение для разрешения проверки подлинности до запроса ресурсов из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности к приложению

>[WACOM.NOTE]Так как вход выполняется во всплывающем окне, необходимо вызвать метод <strong>login</strong> по событию нажатия кнопки. В противном случае многие браузеры отключают окно входа.

1. Откройте файл index.html проекта, найдите элемент H1 и добавьте в него следующий фрагмент кода:

	    <div id="logged-in">
            Вы выполнили вход как <span id="login-name"></span>.
            <button id="log-out">Выход</button>
        </div>
        <div id="logged-out">
            Вы не выполнили вход.
            <button>Вход</button>
        </div>

	Это позволит выполнить вход в мобильные службы с этой страницы.

2. В файле app.js найдите строку кода, расположенную в конце файла, которая вызывает функцию refreshTodoItems, и замените ее на следующий код: 
	
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

    Это приведет к созданию набора функций для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод <strong>login</strong> выше, на одно из следующих: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> или <em>aad</em>.

	>[WACOM.NOTE]В приложении PhoneGap также необходимо добавить в проект следующие модули:
	><ul><li><code>добавление модуля phonegap: https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li>
	><li><code>добавление модуля phonegap: https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Вернитесь в браузер, где работает приложение, и обновите страницу. 

	   После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

	>[WACOM.NOTE]При использовании Internet Explorer после выполнения входа может появиться следующая ошибка. <code>Не удается подключиться к элементу window opener. Он может находиться в другой области Internet Explorer</code>. Это происходит из-за того, что всплывающие окна работают в другой зоне безопасности (Интернет), нежели чем локальный узел (интрасеть). Это влияет только на приложения во время разработки с использованием локального узла localhost. Чтобы избежать этого, откройте вкладку <strong>Безопасность</strong> в <strong>Свойствах обозревателя</strong>, щелкните <strong>Местная интрасеть</strong>, затем <strong>Узлы</strong> и отключите параметр <strong>Автоматически определять принадлежность к интрасети</strong> Не забудьте вернуть предыдущее значение для этого параметра после завершения тестирования.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение идентификатора пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью HTML/JavaScript см. в разделе [Справочник принципов использования мобильных служб HTML/JavaScript].

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-html-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-html-get-started-data
[Авторизация пользователей с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-html-authorize-users-in-scripts

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
