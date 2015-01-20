<properties urlDisplayName="Get Started" pageTitle="Приступая к работе с концентраторами уведомлений Azure" metaKeywords="" description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">C# в Магазине Windows</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Облако отправки Baidu - это китайская облачная служба, которая используется для отправки push-уведомлений на мобильные устройства. Эта служба особенно полезна в Китае, где доставка push-уведомлений для Android усложняется наличием различных магазинов приложений, push-служб и доступностью устройств с Android, обычно не подключенных к службе доставки GCM (Google Cloud Messaging). 

Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается использование Eclipse), который можно скачать <a href="http://go.microsoft.com/fwlink/?LinkId=389797">здесь</a>
+ [Android SDK для мобильных служб]
+ [Android SDK для Baidu Push]

>[WACOM.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатное пробное использование Azure</a>.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Создание учетной записи Baidu](#createBaiduAccount)
* [Регистрация в качестве разработчика Baidu](#registerBaiduDeveloper)
* [Создание облачного push-проекта Baidu](#createBaiduPushProject)
* [Настройка концентратора уведомлений](#configure-hub)
* [Подключение приложения к концентратору уведомлений](#connecting-app)
* [Отправка уведомлений в приложение](#send)

##<a id="createBaiduAccount"></a>Создание учетной записи Baidu

Чтобы использовать Baidu, необходимо создать учетную запись. Если вы уже это сделали, воспользуйтесь ей для входа в [портал Baidu] и перейдите к следующему шагу. В противном случае ознакомьтесь с приведенными ниже инструкциями по созданию учетной записи Baidu.  

1. Откройте [портал Baidu] и выберите ссылку 登录 (Вход). Щелкните 立即注册 для запуска регистрации учетной записи. 

   	![][1]

2. Введите требуемые сведения: номер телефона, адрес электронной почты, пароль и код проверки, и щелкните "Зарегистрировать".

   	![][2]

3. На указанный вами адрес электронной почты будет отправлено сообщение со ссылкой активации учетной записи Baidu. 

   	![][3]

4. Войдите в свою электронную почту, откройте это сообщение и выберите ссылку для активации учетной записи Baidu. 

   	![][4]

После завершения активации воспользуйтесь созданной учетной записью для входа на [портал Baidu]. 

##<a id="registerBaiduDeveloper"></a>Регистрация в качестве разработчика Baidu

1. После входа на [портал Baidu] щелкните **更多>> (Больше)**.

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务 (Службы веб-мастера и разработки)** и нажмите кнопку **百度开放云平台 (Открытая облачная платформа Baidu)**. 

  	![][6]

3. В правом верхнем углу следующей страницы щелкните пункт **开发者服务 (Службы разработки)**. 

  	![][7]

4. На следующей странице щелкните пункт **注册开发者 (Зарегистрированные разработчики)** в правом верхнем углу меню. 

  	![][8]

5. Введите имя, описание и номер мобильного телефона  для получения текстового сообщения проверки и нажмите кнопку **送验证码 (Отправить код проверки)**. Обратите внимание, что для международных номеров необходимо указать код страны в фигурных скобках. Например, номера для США должны выглядеть так: **(1)1234567890**.

  	![][9]

6. После этого вы должны получить текстовое сообщения с номером проверки, как показано в следующем примере:

  	![][10] 

7. Введите номер проверки из сообщения в поле **验证码 (Код подтверждения)**. 

8. Наконец, для завершения регистрации разработчика примите соглашение Baidu и щелкните **提交 (Отправить)**. При успешном завершении регистрации появится следующая страница:

  	![][11] 

##<a id="createBaiduPushProject"></a>Создание облачного push-проекта Baidu

Во время создания push-проекта Baidu вы получите код приложения, ключ API и секретный ключ.

1. После входа на [портал Baidu] щелкните **更多>> (Больше)**.

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务 (Службы веб-мастера и разработки)** и нажмите кнопку **百度开放云平台 (Открытая облачная платформа Baidu)**. 

  	![][6]

3. В правом верхнем углу следующей страницы щелкните пункт **开发者服务 (Службы разработки)**. 

  	![][7]

4. На следующей странице щелкните пункт **云推送 (Облако отправки)** в разделе **云服务 (Облачные службы)**. 

  	![][12]

5. После завершения регистрации как разработчика в верхнем меню появится **管理控制台 (Консоль управления)**. Щелкните **开发者服务管理 (Управление службой разработки)**. 

  	![][13]

6. На следующей странице щелкните **创建工程 (Создать проект)**.

  	![][14]

7. Введите имя приложения и нажмите кнопку **创建 (Создать)**.

  	![][15]

8. После успешного создания отобразится страница со значениями **AppID**, **Ключ API** и **Секретный ключ**. Запишите значения **Ключ API** и **Секретный ключ**, которые будут использоваться позже. 

  	![][16]

9. Настройте проект для push-уведомлений, щелкнув **云推送 (Облако отправки)** на левой панели. 

  	![][31]

10. На следующей странице нажмите кнопку **推送设置 (Push-параметры)**.

	![][32]  

11. On the configuration page, add the package name that you will be using in your Android project in the **应用包名 (Application package)** field and click **保存设置 (Save)**  

	![][33]

Появится сообщение **保存成功！(Успешно сохранено!)**.

##<a id="configure-hub"></a>Настройка концентратора уведомлений

1. Войдите на [Портал управления Azure], а затем нажмите кнопку **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Шина обслуживания**, **Концентратор уведомлений** и **Быстрое создание**.
 
3. Введите имя своего **Центра уведомлений**, затем выберите **Регион** и **Пространство имен**, в котором будет создан этот центр уведомлений. После этого щелкните **Создать новый концентратор уведомлений**.  

  	![][17]

4. Выберите пространство имен, в котором создан центр уведомлений и нажмите расположенный вверху элемент **Концентраторы уведомлений**. 

  	![][18]

5. Выделите созданный концентратор уведомлений и выберите пункт **Настройка** в верхнем меню.

  	![][19]

6. Прокрутите вниз до раздела **Параметры уведомлений baidu** и введите значения **Ключ API** и **Секретный ключ**, полученные ранее для облачного push-проекта Baidu из консоли Baidu. После ввода значений щелкните **Сохранить**. 

  	![][20]

7. Откройте вкладку **Панель мониторинга** в верхней части меню Центра уведомлений и нажмите кнопку **Просмотреть строку подключения**.

  	![][21]

8. Запишите значения **DefaultListenSharedAccessSignature** и **DefaultFullSharedAccessSignature** из окна "Сведения по доступу к подключению". 

    ![][22]

##<a id="connecting-app"></a>Подключение приложения к концентратору уведомлений

1. В ADT Eclipse создайте новый проект Android (File (Файл) -> New (Создать) -> Android Application (Приложение Android)).

    ![][23]

2. Введите **Имя приложения** и убедитесь, что для параметра **Минимальная требуемая версия SDK** указано значение **API 16: Android 4.1**.

    ![][24]

3. Щелкните **Далее** и следуйте указаниям мастера настройки до появления окна **Создать действие**. Убедитесь, что элемент **Новое действие** выбран и нажмите **Готово** для создания нового приложения Android. 

    ![][25]

4. Убедитесь, что значение параметра **сборки проекта для целевой платформы** задано правильно.

    ![][26]

5. Скачайте и распакуйте [Android SDK для мобильных служб], откройте папку **notificationhubs**, скопируйте файл **notification-hubs-x.y.jar** в папку *libs* вашего проекта Eclipse. Затем обновите папку *libs*.

6. Скачайте и распакуйте [Android SDK для Baidu Push], откройте папку **libs** и скопируйте файл *pushservice x.y.z* jar, а также папки * armeabi * и * mips * в папку **libs** приложения Android. 

7. Откройте файл **AndroidManifest.xml** своего проекта Android и добавьте разрешения, требуемые пакетом SDK для Baidu.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Добавьте свойство *android:name* в элементе *application* файла **AndroidManifest.xml** для замены значения *yourprojectname*, например, на **com.example.BaiduTest**. Убедитесь, что это имя проекта совпадает с настроенным в консоли Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Добавьте следующую конфигурацию в элементе приложения, расположенную за элементом действия .MainActivity, для замены значения *yourprojectname*, например, на **com.example.BaiduTest**:

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Добавьте в проект новый класс с именем **ConfigurationSettings.java**. 

    ![][28]

    ![][29]

10. Добавьте в класс следующий код:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Задайте значения *API_KEY* вместе с полученным ранее из облачного проекта, *NotificationHubName* с именем концентратора уведомлений на портале Azure и *NotificationHubConnectionString* с DefaultListenSharedAccessSignature на портале Azure. 

11. Добавьте новый класс с именем **DemoApplication.java**, затем добавьте в него следующий код:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Добавьте еще один новый класс с именем **MyPushMessageReceiver.java** и добавьте в него приведенный ниже код. Это класс, который обрабатывает извещающие уведомления, полученные от push-сервера Baidu:

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. Откройте класс **MainActivity.java** и добавьте следующий код к методу **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

и в начале добавьте следующие операторы импорта:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Отправка уведомлений в приложение

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента, использующего наш <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейс REST</a>. В этом учебнике демонстрируется использование консольного приложения .NET. 

1. Создайте новое консольное приложение Visual C#.

	![][30]

2. Добавьте ссылку на пакет Azure Service Bus SDK с помощью пакета <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите следующее и нажмите клавишу ВВОД:

        Install-Package WindowsAzure.ServiceBus

3. Откройте файл Program.cs и добавьте следующий оператор:

        using Microsoft.ServiceBus.Notifications;

4. Добавьте следующий метод в классе "Program", замените *DefaultFullSharedAccessSignatureSASConnectionString* и *NotificationHubName* имеющимися значениями. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Затем добавьте следующие строки в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="test-app"></a>Тестирование приложения

Для тестирования работы этого приложения на реальном телефоне просто подключите его к компьютеру с помощью кабеля USB.

Порядок тестирования этого приложения с помощью эмулятора:

1. В верхней панели инструментов Eclipse нажмите кнопку "Запуск" и выберите приложение. 

2. При этом ваше приложение будет скачано на подключенный телефон или запустится эмулятор, приложение загрузится туда и запустится.

3. Приложение получает значения "userId" и "channelId" из службы push-уведомлений Baidu и регистрируется в концентраторе уведомлений.
	
4.	Чтобы отправить контрольное уведомление при использовании консольного приложения .Net, просто нажмите клавишу F5 в Visual Studio, чтобы запустить приложение. Данное приложение отправит уведомление, которое отобразится в верхней области уведомлений вашего устройства или эмулятора. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Android SDK для Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Портал управления Azure]: https://manage.windowsazure.com/
[Портал Baidu]: http://www.baidu.com/


<!--HONumber=35.2-->
