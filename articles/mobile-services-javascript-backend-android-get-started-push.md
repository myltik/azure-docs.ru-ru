<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Приступая к работе с push-уведомлениями (JS)" pageTitle="Приступая к работе с push-уведомлениями (Android JavaScript) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб Windows Azure для отправки push-уведомлений в приложение JavaScript для Android." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""  />


# <a name="getting-started-with-push"> </a>Приступая к работе с push-уведомлениями в мобильных службах


<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
</div>

<!--
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>		
-->

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

[WACOM.NOTE]В этом учебнике показана интеграция мобильных служб с концентраторами уведомлений, которая в настоящее время находится на этапе предварительной версии. По умолчанию отправка push-уведомлений с помощью концентраторов уведомлений из серверной части JavaScript не включена.  После создания нового концентратора уведомлений процесс интеграции необратим. Push-уведомления для iOS в настоящее время доступны только при использовании поддержки push-уведомлений по умолчанию, которая описана в [этой версии раздела](/ru-ru/documentation/articles/mobile-services-android-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Включение Google Cloud Messaging](#register)
2. [Настройка мобильных служб](#configure)
3. [Добавление push-уведомлений в приложение](#add-push)
4. [Обновление скриптов для отправки push-уведомлений](#update-scripts)
5. [Вставка данных для получения уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе.  

##<a id="register"></a>Включение Google Cloud Messaging


[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени вашего приложения.

##<a id="configure"></a>Настройка мобильных служб для отправки push-запросов

1. Выполните вход на [портал управления Windows Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Откройте вкладку **Push-уведомления**, щелкните элемент **Включение улучшенных push-уведомлений** и элемент **Да**, чтобы принять изменение конфигурации.


	![](./media/mobile-services-android-get-started-push/mobile-enable-enhanced-push.png)

	![](../includes/media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)


	При этом обновляется конфигурация мобильной службы для использования улучшенных функциональных возможностей push-уведомлений, предоставляемых концентраторами уведомлений. Некоторые концентраторы уведомлений можно бесплатно использовать с вашей платной мобильной службой. Дополнительную информацию см. в разделе [Сведения о ценах — мобильные службы](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    <div class="dev-callout"><b>Важно!</b>
	<p>Эта операция выполняет сброс учетных данных push-уведомлений и изменяет режим работы push-методов в скриптах. Эти изменения нельзя отменить. Не используйте этот метод для добавления концентратора уведомлений в рабочую мобильную службу. Инструкции по включению улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>.</p>
    </div>

3. Введите значение **ключа API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.



   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Важно!</b>
	<p>При задании учетных данных GCM для улучшенных push-уведомлений на вкладке Push-уведомления портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.</p>
    </div>


Мобильная служба и приложение теперь настроены для работы с GCM и концентраторами уведомлений.

##<a id="add-push"></a>Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте. 

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

1. Откройте файл `AndroidManifest.xml`. В коде в следующих двух действиях замените _`**my_app_package**`_ именем пакета приложения для вашего проекта, которое является значением атрибута `package` тега `manifest`. 

2. Добавьте следующие новые разрешения после существующего элемента `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Добавьте следующий код после открывающего тега `application`: 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Загрузите и распакуйте [Android SDK мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

    <div class="dev-callout"><b>Примечание.</b>
	<p>Числа в конце этого имени файла могут меняться в последующих выпусках SDK.</p>
    </div>

5. Откройте файл *ToDoItemActivity.java* и добавьте следующий оператор импорта:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

6. Добавьте в класс следующую закрытую переменную, где _`<PROJECT_NUMBER>`_ — номер проекта, назначенный Google вашему приложению в предыдущей процедуре:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. В методе **onCreate** перед созданием экземпляра MobileServiceClient добавьте следующий код, который регистрирует обработчик уведомлений для устройства:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

	Позднее мы определим класс MyHandler.class, на который ссылается этот код.
	

8. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), выберите **Создать** и **Класс**.

9. В поле **Имя** введите `MyHandler`, в поле **Суперкласс** введите `com.microsoft.windowsazure.notifications.NotificationsHandler` и нажмите кнопку **Готово**

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	При этом создается новый класс MyHandler.

10. Добавьте следующий оператор импорта:

		import android.content.Context;
		
11. Затем добавьте в класс следующий код:

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


12. Добавьте следующий код для переопределения метода **onRegistered**, который регистрирует устройство в концентраторе уведомлений мобильной службы.

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
	            @Override
	            public void onRegister(Registration registration, Exception exception) {
	                  if (exception != null) {
	                        // handle error
	                  }
	            }
	      	});
		}


13. Добавьте следующий код для переопределения метода **onReceive**, который отображает уведомление при его получении.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


Ваше приложение теперь обновлено для поддержки push-уведомлений.


##<a id="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. В **todoitem** откройте вкладку **Скрипт** и выберите **Вставка**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = 
		    '{"data":{"message" : "Hello from Mobile Services!"}}';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	При этом регистрируется новый скрипт вставки, в котором [объект gcm] используется для отправки push-уведомлений на все зарегистрированные устройства после успешной вставки данных. 

##<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###Настройка эмулятора для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, щелкните **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Проект будет предназначен для API-интерфейсов Google.

2. В разделе **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство и щелкните **Изменить**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Выберите **API-интерфейсы Google** в разделе **Цель**, а затем нажмите кнопку "ОК".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

###Выполнение теста

1. В меню **Выполнить** в Eclipse щелкните **Выполнить** для запуска приложения.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и нажмите кнопку **Добавить**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.


## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Windows Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении Android.

+ [Рассылка уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений пользователям]
	<br/>Дополнительные сведения о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

+ [Отправка пользователям уведомлений между различными платформами]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.  


<!-- Anchors. -->
[Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
[Обновление созданного кода push-уведомлений]: #update-scripts
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js/
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-js
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Портал управления Windows Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

