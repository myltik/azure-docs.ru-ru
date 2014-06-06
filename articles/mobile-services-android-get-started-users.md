<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Приступая к работе с проверкой подлинности" pageTitle="Приступая к работе с проверкой подлинности (Android) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб для проверки подлинности пользователей приложения Android с помощью различных поставщиков удостоверений, включая Google, Facebook, Twitter и Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Приступая к работе с проверкой подлинности в мобильных службах" authors=""  solutions="" writer="" manager="" editor=""  />





# Приступая к работе с аутентификацией в мобильных службах
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-android" title="Android" class="current">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, для этого используется поставщик удостоверений, который поддерживается мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение ИД пользователя.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a><span class="time">10:42</span></div>
</div> 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

1. [Регистрация приложения для проверки подлинности и конфигурация мобильных служб]
2. [Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]
3. [Добавление проверки подлинности к приложению]

Этот учебник основан на кратком руководстве по использованию мобильных служб. Вам также необходимо сначала ознакомиться с учебником [Приступая к работе с мобильными службами]. 

Для изучения этого учебника необходимы Eclipse и Android 4.2 или более поздней версии. 

<!--<div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике показан базовый метод, предоставляемый мобильными службами для проверки подлинности пользователей с помощью различных поставщиков удостоверений. Этот метод можно легко настроить, и он поддерживает нескольких поставщиков. В то же время этот метод также требует от пользователей осуществлять вход каждый раз при запуске приложения. Сведения о том, как вместо этого использовать Live Connect для предоставления единого входа в приложении магазина Windows, см. в разделе <a href="/ru-ru/develop/mobile/tutorials/single-sign-on-win8-dotnet">Единый вход для приложений магазина Windows с использованием Live Connect</a>.</p>
</div>-->

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для проверки подлинности и конфигурация мобильных служб</h2>

Чтобы иметь возможность проверять подлинность пользователей, необходимо зарегистрировать приложение у поставщика удостоверений. Затем необходимо зарегистрировать секрет клиента, созданный поставщиком, в мобильных службах.

1. Выполните вход на [Портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

   	![][4]

2. Откройте вкладку **Панель мониторинга** и запишите значение **URL-адрес сайта**.

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

3. В Eclipse откройте проект, созданный после завершения учебника [Приступая к работе с мобильными службами]. 

4. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 (Не санкционировано). 

	 Это происходит потому, что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица _TodoItem_ теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить проверку подлинности пользователей, прежде чем запрашивать ресурсы из мобильной службы.

<h2><a name="add-authentication"></a><span class="short-header">Добавление проверки подлинности</span>Добавление проверки подлинности для приложения</h2>

1. В обозревателе пакетов в Eclipse откройте файл ToDoActivity.java и добавьте следующие инструкции импорта.

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. Добавьте в класс **ToDoActivity** следующий метод: 
	
		private void authenticate() {
		
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
	
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {
	
							if (exception == null) {
								createAndShowDialog(String.format(
												"You are now logged in - %1$2s",
												user.getUserId()), "Success");
								createTable();
							} else {
								createAndShowDialog("You must log in. Login Required", "Error");
							}
						}
					});
		}

    При этом создается новый метод для обработки процесса проверки подлинности. Пользователь прошел проверку подлинности с использованием имени входа в Google. Открывается диалоговое окно, в котором отображается идентификатор пользователя, прошедшего проверку подлинности. Без успешной проверки подлинности продолжение невозможно.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Если используется поставщик удостоверений, отличный от Google, измените значение, передаваемое в метод <strong>login</strong> выше, на одно из следующих: <em>MicrosoftAccount</em>, <em>Facebook</em> или <em>Twitter</em>.</p>
    </div>

3. Добавьте в метод **onCreate** следующую строку после кода, который формирует экземпляр объекта `MobileServiceClient`.

		authenticate();

	Этот вызов запускает процесс проверки подлинности.

4. Переместите оставшийся код после `authenticate();` в методе **onCreate** в новый метод **createTable**, который выглядит следующим образом:

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. В меню **Запуск** щелкните **Запуск**, чтобы запустить приложение и выполнить вход с помощью выбранного поставщика удостоверений. 

   	После успешного входа в систему приложение должно работать без ошибок, а вы должны быть в состоянии выполнять запросы мобильных служб и обновлять данные.

## <a name="next-steps"></a>Дальнейшие действия

В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами. 

<!-- Anchors. -->
[Регистрация приложения для проверки подлинности и конфигурация мобильных служб]: #register
[Ограничение разрешений таблицы пользователями, прошедшими проверку подлинности]: #permissions
[Добавление проверки подлинности к приложению]: #add-authentication
[Дальнейшие действия]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-android
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-android

[Портал управления Azure]: https://manage.windowsazure.com/

