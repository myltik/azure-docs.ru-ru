<properties 
	pageTitle="Приступая к работе с проверкой подлинности (HTML 5) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы для аутентификации пользователей приложения HTML с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# Добавление проверки подлинности к приложению мобильных служб 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure из приложения на HTML, включая приложения PhoneGap или Cordova. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

Этот учебник создан на основе краткого руководства по мобильным службам. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами].

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Запустите в подпапке **server** каталога приложения один из следующих файлов команд.

	+ **launch-windows** (компьютеры с ОС Windows) 
	+ **launch-mac.command** (компьютеры с ОС Mac OS X)
	+ **launch-linux.sh** (компьютеры с ОС Linux)

	>[AZURE.NOTE]На компьютере под управлением Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск сценария. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> в веб-браузере, чтобы запустить приложение.

	Данные не загружаются. Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

3. (Необязательно) Откройте отладчик сценариев для вашего веб-браузера и обновите страницу. Убедитесь, что возникает ошибка отказа в доступе.

Далее необходимо обновить приложение для разрешения проверки подлинности до запроса ресурсов из мобильной службы.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

>[AZURE.NOTE]Так как вход выполняется во всплывающем окне, необходимо вызвать метод <strong>login</strong> по событию нажатия кнопки. В противном случае многие браузеры отключают окно входа.

1. Откройте файл index.html проекта, найдите элемент H1 и добавьте в него следующий фрагмент кода:

	    <div id="logged-in">
            You are logged in as <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            You are not logged in.
            <button>Log in</button>
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

    Это приведет к созданию набора функций для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook. Если используется поставщик удостоверений, отличный от Facebook, измените значение, переданное в методе **login** выше, на одно из следующих: *microsoftaccount*, *facebook*, *twitter*, *google* или *aad*.

	>[AZURE.IMPORTANT]В приложении PhoneGap также необходимо добавить в проект следующие подключаемые модули. <ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li> <li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Вернитесь в браузер, где работает приложение, и обновите страницу.

	   После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

	>[AZURE.NOTE]Если используется браузер Internet Explorer, после входа может появиться такое сообщение об ошибке: <code>Не удается подключиться к элементу window opener. Он может находиться в другой области Internet Explorer</code>. Это происходит из-за того, что всплывающие окна работают в другой зоне безопасности (Интернет), нежели чем локальный узел (интрасеть). Это влияет только на приложения во время разработки с использованием локального узла localhost. Чтобы избежать этого, откройте вкладку <strong>Безопасность</strong> в разделе <strong>Свойства обозревателя</strong>, последовательно щелкните <strong>Местная интрасеть</strong> и <strong>Узлы</strong>, а затем отключите параметр <strong>Автоматически определять принадлежность к интрасети</strong>. После тестирования не забудьте вернуть предыдущее значение этого параметра.

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью HTML/JavaScript см. в разделе [Справочник принципов использования мобильных служб HTML/JavaScript].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-html-get-started.md
[Get started with data]: mobile-services-html-get-started-data.md
[Авторизация пользователей с помощью скриптов]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
 

<!---HONumber=July15_HO1-->