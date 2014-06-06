<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Приступая к работе с проверкой подлинности (HTML5)" pageTitle="Приступая к работе с проверкой подлинности (HTML 5) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Как использовать мобильные службы для проверки подлинности пользователей приложения HTML через различные поставщики удостоверений, включая Google, Facebook, Twitter и Майкрософт." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с проверкой подлинности в мобильных службах" authors=""  solutions="" writer="glenga" manager="" editor=""  />




# Приступая к работе с проверкой подлинности в мобильных службах
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-html" title="HTML" class="current">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>


В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах приложения HTML.  В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.  

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник основан на кратком руководстве по использованию мобильных служб. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

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

Мобильная служба и приложение теперь настроены для работы с выбранным поставщиком проверки подлинности.

<h2><a name="permissions"></a><span class="short-header">Ограничение разрешений</span>Ограничение разрешений зарегистрированными пользователями</h2>

1. На портале управления нажмите вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][14]

2. Щелкните вкладку **Разрешения**, установите все разрешения в состояние **Только прошедшие проверку пользователи** и нажмите кнопку **Сохранить**. Это обеспечит, что только прошедший проверку пользователь сможет производить все операции с таблицей **TodoItem**. Это также упрощает скрипты в следующем учебнике, так как в них не нужно учитывать возможность существования анонимных пользователей.

   	![][15]

3. Запустите в подпапке **server** каталога приложения один из следующих файлов команд.

	+ **launch-windows** (компьютеры Windows) 
	+ **launch-mac.command** (компьютеры Mac OS X)
	+ **launch-linux.sh** (компьютеры Linux)

	<div class="dev-callout"><b>Примечание.</b>
		<p>На компьютере Windows нажмите клавишу `R`, когда в PowerShell появится запрос на запуск сценария. Веб-браузер может предупредить о том, что не следует запускать этот сценарий, так как он был загружен из Интернета. В этом случае необходимо указать, что браузеру следует продолжить загрузку сценария.</p>
	</div>

	Это приведет к запуску веб-сервера на локальном компьютере, где и будет размещено новое приложение.

2. Откройте URL-адрес <a href="http://localhost:8000/" target="_blank">http://localhost: 8000/</a> в веб-браузере, чтобы запустить приложение. 

	Данные не загружаются. Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

3. (Необязательно) Откройте отладчик сценариев для вашего веб-браузера и обновите страницу. Убедитесь, что возникает ошибка отказа в доступе. 

Далее необходимо обновить приложение для разрешения проверки подлинности до запроса ресурсов из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

<div class="dev-callout"><b>Примечание.</b>
		<p>Так как вход выполняется во всплывающем окне, необходимо вызвать метод <strong>login</strong> по событию нажатия кнопки. В противном случае многие браузеры отключают окно входа.</p>
</div>

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

    Это приведет к созданию набора функций для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с помощью имени входа в Facebook.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик идентификаторов, отличный от Facebook, измените значение, переданное в методе <strong>login</strong> выше на одно из следующих: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, or <em>google</em>.</p>
    </div>

9. Вернитесь в браузер, где работает приложение, и обновите страницу. 

   После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

	<div class="dev-callout"><b>Примечание.</b>
		<p>При использовании Internet Explorer после входа может появиться следующая ошибка: <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code> Это происходит из-за того, что всплывающие окна работают в другой зоне безопасности (Интернет), нежели чем локальный узел (интрасеть). Это влияет только на приложения во время разработки с использованием локального узла localhost. Чтобы избежать этого, откройте вкладку <strong>Безопасность</strong> в <strong>Свойствах обозревателя</strong>, щелкните <strong>Местная интрасеть</strong>, затем <strong>Узлы</strong> и отключите параметр <strong>Автоматически определять принадлежность к интрасети</strong> Не забудьте вернуть предыдущее значение для этого параметра после завершения тестирования.</p>
	</div>

## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. Дополнительные сведения об использовании мобильных служб с помощью HTML/JavaScript см. в разделе [Справочник принципов использования мобильных служб HTML/JavaScript].

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
[Вход с использованием учетной записи Майкрософт]: /ru-ru/develop/mobile/how-to-guides/register-for-microsoft-authentication
[Вход в Facebook]: /ru-ru/develop/mobile/how-to-guides/register-for-facebook-authentication
[Вход в Twitter]: /ru-ru/develop/mobile/how-to-guides/register-for-twitter-authentication
[Вход в Google]: /ru-ru/develop/mobile/how-to-guides/register-for-google-authentication
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-html
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-html
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-html

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client

