<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Приступая к работе с push-уведомлениями" pageTitle="Приступая к работе с push-уведомлениями — мобильные службы" metaKeywords="" description="Узнайте, как использовать push-уведомления в приложениях Xamarin.Android с помощью мобильных служб Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Приступая к работе с push-уведомлениями в мобильных службах" documentationCenter="Mobile" authors="" />
# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Xamarin.Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений]
2. [Настройка мобильных служб]
2. [Добавление push-уведомлений в приложение]
3. [Обновление скриптов для отправки push-уведомлений]
4. [Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

+ Активная учетная запись Google

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для получения push-уведомлений</h2>

<div class="dev-callout"><b>Примечание.</b>
<p>Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> и войдите с помощью учетной записи Google. Нажмите кнопку **Создать проект...**.

	![][1]   

	> [WACOM.NOTE]
	> При наличии существующего проекта после входа в систему открывается страница **Панель мониторинга**. Чтобы создать новый проект на странице панели мониторинга, разверните узел **Проект API<**, click **Create...** under **Other projects**, then enter a project name and click **Create project**.

2. Click the Overview button in the left column, and make a note of the Project Number in the Dashboard section. 

	Later in the tutorial you set this value as the **PROJECT_ID** variable in the client.

3. On the <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a> щелкните элемент **Службы**, щелкните переключатель, чтобы включить **Google Cloud Messaging for Android**, и примите условия соглашения. 

4. Щелкните элемент **Доступ к API** и затем **Создать новый ключ сервера...** 

	![][2]

5. В области **Настройка ключа сервера для проекта API** нажмите кнопку **Создать**.

	![][3]

6. Запишите значение **Ключ API**.

	![][4] 

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени вашего приложения.

<a name="configure"></a><h2><span class="short-header">Настройка службы</span>Настройка мобильных служб для отправки push-запросов</h2>

1. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение.

	![][18]

2. Перейдите на вкладку **Push-уведомления**, введите значение **Ключ API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

	![][19]

	Ваша мобильная служба теперь настроена для работы с GCM для отправки push-уведомлений.

<a name="add-push"></a><h2><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение</h2>

1. Сначала нужно добавить в проект **PushSharp** в качестве ссылки. Для этого нам следует скомпилировать последнюю версию PushSharp и добавить скомпилированную библиотеку DLL в качестве ссылки на наш проект Xamarin.Android.

2. Посетите [страницу PushSharp Github]и загрузите последнюю версию. После извлечения коллекции файлов перейдите к следующей папке с примером проекта:

	**/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

	.. и откройте файл проекта:
	
	**  PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3. Постройте образец клиента MonoForAndroid PushSharp в режиме **Выпуск**.

4. Создайте папку **_external** в папке проекта Xamarin.Android

5. Скопируйте следующий файл из образца клиента MonoForAndroid PushSharp в созданную папку **_external** в папке проекта Xamarin.Android:

	 **\bin\Release\PushSharp.Client.MonoForAndroid.dll**

6. Откройте проект Xamarin.Android в Xamarin Studio (или Visual Studio). 

7. Щелкните правой кнопкой мыши папку **Ссылки** проекта и выберите команду **Изменить ссылки...**

8. Откройте вкладку **Сборка .NET**, перейдите к папке своего проекта **_external**, выберите созданную ранее библиотеку **PushSharp.Client.MonoForAndroid.dll** и нажмите кнопку **Добавить**. Нажмите кнопку "ОК", чтобы закрыть диалоговое окно. 

9. Откройте **Constants.cs** и добавьте следующую строку, заменив **PROJECT\_ID** на проектом записанное ранее значение Google Project_ID:

		public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. Скопируйте файл **PushService.cs** из образца клиента MonoForAndroid PushSharp в папку своего проекта Xamarin.Android и добавьте его в проект.

11. Измените пространство имен, используемое в **PushService.cs**, чтобы оно совпадало с пространством имен в проект (например, XamarinTodoQuickStart).

12. Измените массив **SENDER_IDS** в файле **PushService.cs**, чтобы указать ссылку на созданную ранее константу **SenderID**:

		public static string[] SENDER_IDS = new string[] { Constants.SenderID };
		
13. Добавьте новое статическое свойство **PushHandlerService** в **PushService.cs** для отслеживания идентификатора регистрации устройства:

		public static string RegistrationID { get; private set; }
		
14. Обновите метод **OnRegistered** в **PushService.cs** для сохранения полученного идентификатора регистрации в локальной статической переменной:

		protected override void OnRegistered(Context context, string registrationId)
		{
			Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
		}

15. Обновите метод **OnMessage** в **PushService.cs** для отображения push-сообщения, полученного в составе уведомления (замените существующий вызов **createNotification**):

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);
       
16. Обратите внимание, что метод **OnMessage** по умолчанию использует следующий код для сохранения последнего полученного push-сообщения:

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Обновите метод **createNotification** в **PushService.cs**, чтобы указать ссылку на **TodoActivity** вместо **DefaultActivity**

18. Откройте **TodoActivity.cs** и добавьте следующий оператор "using":

        using PushSharp.Client;

19. Вставьте в **TodoActivity.cs** следующие строки прямо над тем местом, где создается **MobileServiceClient**:
	
        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Откройте **TodoItem.cs** и добавьте новое поле для отслеживания идентификатора зарегистрированного устройства для пользователя, который добавил TodoItem:

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. In **TodoActivity.cs** update the **AddItem** method to set the **RegistrationID** of the newly added **TodoItem** to the device's registration ID receieved during registration:

		// Create a new item
		var item = new TodoItem() {
			Text = textNewTodo.Text,
			Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
		};

Ваше приложение теперь обновлено для поддержки push-уведомлений.

<h2><a name="update-scripts"></a><span class="short-header">Обновление сценария вставки</span>Обновление зарегистрированного сценария вставки на портале управления</h2>

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

	![][21]

2. В **TodoItem** откройте вкладку **Скрипт** и выберите **Вставка**.
   
  	![][22]

	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.channel, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   При этом регистрируется новый скрипт вставки, в котором используется [объект gcm] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки. 

<h2><a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении</h2>

1. Запустите приложение и добавьте новый элемент Todo. Убедитесь в получении push-уведомления о добавлении нового элемента Todo.

2. Изучите вкладку **Журналы** своего мобильного приложения на портале управления Azure, чтобы просмотреть сообщения ведения журнала, которые мы ранее добавили в метод **Вставка** из таблицы **TodoItem**.

3. Откройте таблицу **TodoItem** в портале управления Azure для просмотра нового добавленного столбца **channel**, который содержит уникальные идентификаторы регистрации устройств.

Вы успешно завершили ознакомление с данным учебником.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **ApplicationURL**, **ApplicationKey** и **SenderID** с использованием своих параметров Azure. 

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере пользователь получает push-уведомление с только что вставленными данными. В следующем учебнике [Принудительная отправка уведомлений пользователям приложения] вы будете создавать отдельную таблицу "Устройства" для хранения маркеров устройств и отправлять push-уведомление всем сохраненным каналам при выполнении вставки. 

<!-- Anchors. -->
[Регистрация приложения для получения push-уведомлений]: #register
[Настройка мобильных служб]: #configure
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[18]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png







<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[API-интерфейсы Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Портал подготовки Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-android
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Принудительная отправка уведомлений пользователям приложения]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-android
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Принципы использования мобильных служб в приложениях Xamarin]: /ru-ru/develop/mobile/how-to-guides/work-with-xamarin-client-library
[Объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[страницу PushSharp Github]: https://github.com/Redth/PushSharp


