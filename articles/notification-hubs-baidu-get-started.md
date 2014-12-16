<properties urlDisplayName="Get Started" pageTitle="Начало работы с концентраторами уведомлений Azure "metaKeywords ="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="10/03/2014" ms.author="piyushjo" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows Store C#</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Push-облако Baidu является китайской облачной службой, которую вы можете использовать для отправки push-уведомлений на мобильные устройства. Эта служба особенно полезна в Китае, где доставка push-уведомлений на Android сложна в связи с наличием различных магазинов приложений, служб push-уведомлений и наличия устройств на Android, как правило, не подключенных к GCM (Google Cloud Messaging). 

Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается, что вы будете пользоваться Eclipse), который можно загрузить <a href="http://go.microsoft.com/fwlink/?LinkId=389797">здесь</a>
+ [Android SDK для мобильных служб]
+ [Baidu Push Android SDK]

>[WACOM.NOTE] Для работы с этим учебником требуется учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Создание учетной записи Baidu](#createBaiduAccount)
* [Зарегистрироваться в качестве разработчика Baidu](#registerBaiduDeveloper)
* [Создание push-проекта Baidu Cloud](#createBaiduPushProject)
* [Настройка концентратора уведомлений](#configure-hub)
* [Подключение приложения к концентратору уведомлений](#connecting-app)
* [Отправка уведомлений для приложения](#send)

##<a id="createBaiduAccount"></a>Создание учетной записи Baidu

Чтобы использовать Baidu, вам необходимо создать учетную запись. Если она у вас уже есть, выполните вход на [портал Baidu] со своей учетной записью Baidu и переходите к следующему шагу; в противном случае - ниже приведены инструкции по созданию новой учетной записи Baidu.  

1. Перейдите на [портал Baidu] и нажмите ссылку 登录 (Логин). Нажмите 立即注册, чтобы начать процедуру регистрации новой учетной записи. 

   	![][1]

2. Введите требуемые сведения - почтовый или электронный адрес, пароль и код проверки, затем нажмите кнопку "Зарегистрироваться".

   	![][2]

3. Вам придет электронная почта на указанный вами адрес электронной почты с письмом, содержащим ссылку для активации вашей учетной записи Baidu. 

   	![][3]

4. Зайдите на свою электронную почту, откройте письмо активации Baidu и перейдите по ссылке активации, чтобы активировать вашу учетную запись Baidu. 

   	![][4]

Активировав свою учетную запись Baidu, зайдите на [портал Baidu], введя данные вашей учетной записи. 

##<a id="registerBaiduDeveloper"></a>Зарегистрироваться в качестве разработчика Baidu

1. После входа на [портал Baidu] щелкните **更多>> (Подробнее)**.

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务 (Службы для веб-мастеров и разработчиков)** и нажмите **百度开放云平台 (Открытая платформа облака Baidu)**. 

  	![][6]

3. На следующей странице нажмите **开发者服务 (Службы разработчика)** в правом верхнем углу. 

  	![][7]

4. На следующей странице нажмите **注册开发者 (Зарегистрированные разработчики)** в правом верхнем углу меню. 

  	![][8]

5. Введите свое имя, описание и номер мобильного телефона для получения текста проверочного сообщения, затем нажмите **送验证码 (Отправить код проверки)**. Следует заметить, что для международных телефонных номеров необходимо заключить код страны в круглые скобки, например, для телефонных номеров в России это будет **(7)1234567890**.

  	![][9]

6. Затем следует получить текстовое сообщение с проверочным номером, как показано в следующем примере:

  	![][10] 

7. Введите проверочный номер из сообщения в поле **验证码 (Код подтверждения)**. 

8. Наконец завершите регистрацию разработчика, приняв соглашение Baidu и нажав **提交 (Отправить)**. При успешном завершении регистрации появится следующая страница:

  	![][11] 

##<a id="createBaiduPushProject"></a>Создание push-проекта Baidu Cloud

При создании вами облачного проекта push-уведомлений Baidu вы получите идентификатор вашего приложения, ключ API и секретный ключ.

1. После входа на [портал Baidu] щелкните **更多>> (Подробнее)**.

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务 (Службы для веб-мастеров и разработчиков)** и нажмите **百度开放云平台(Открытая платформа облака Baidu)**. 

  	![][6]

3. На следующей странице нажмите **开发者服务 (Службы разработчика)** в правом верхнем углу. 

  	![][7]

4. На следующей странице нажмите кнопку **云推送 (Облачные push-уведомления)** в разделе **云 服务 (Облачные службы)**. 

  	![][12]

5. Став зарегистрированным разработчиком, вы увидите **管理控制台 (Консоль управления)** в верхнем меню. Нажмите кнопку **开发者服务管理 (Управление службами разработчиков)**. 

  	![][13]

6. На следующей странице щелкните **创建工程 (Создать проект)**.

  	![][14]

7. Введите имя приложения и нажмите кнопку **创建 (Создать)**.

  	![][15]

8. После успешного создания вы увидите страницу с **Идентификатором приложения**, **Ключом API** и **Секретным ключом**. Запишите **Ключ API** и **Секретный ключ**, которыми мы воспользуемся позже. 

  	![][16]

9. Настройте проект для push-уведомлений, нажав **云推送 (Облачные push-уведомления)** на левой панели. 

  	![][31]

10. На следующей странице нажмите кнопку **推送设置 (Параметры push-уведомлений)**.

	![][32]  

11. На странице настроек добавьте имя пакета, который вы будете использовать в вашем Android-проекте в поле **应用包名 (Пакет приложений)** и нажмите **保存设置 (Сохранить)**  

	![][33]

Вы увидите сообщение **保存成功！ (Успешно сохранено!)**.

##<a id="configure-hub"></a>Настройка концентратора уведомлений

1. Выполните вход в [Портал управления Azure], затем нажмите **+NEW** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.
 
3. Задайте имя вашего **Концентратора уведомлений**, выберите **Регион** и **Пространство имен**, в которой этот концентратор уведомлений будет создан, затем нажмите **Создать новый концентратор уведомлений**  

  	![][17]

4. Нажмите пространство имен, в котором вы создали свой центр уведомлений и затем нажмите **Концентраторы уведомлений** вверху. 

  	![][18]

5. Выберите созданный вами концентратор уведомлений и нажмите **Настройка** в верхнем меню.

  	![][19]

6. Прокрутите вниз до раздела **Настройки уведомлений Baidu** и введите **Ключ API** и **Секретный ключ**, который вы ранее получили из консоли Baidu для своего проекта облачных push-уведомлений Baidu. Нажмите **Сохранить** после ввода этих значений. 

  	![][20]

7. Нажмите вкладку **Панель инструментов** вверху для концентратора уведомлений и нажмите **Просмотр строки соединения**.

  	![][21]

8. Запишите **DefaultListenSharedAccessSignature** и **DefaultFullSharedAccessSignature** из информационного окна соединения доступа. 

    ![][22]

##<a id="connecting-app"></a>Подключение приложения к концентратору уведомлений

1. В ADT Eclipse создайте новый проект Android (File (Файл) -> New (Создать) -> Android Application (Приложение Android)).

    ![][23]

2. Введите **Имя приложения** и убедитесь, что **Минимально необходимая версия SDK** задана равной **API 16: Android 4.1**.

    ![][24]

3. Нажмите **Далее** и продолжайте выполнять указания мастера до появления окна **Создать действие**. Убедитесь, что выбрано **Пустое действие** и, наконец, выберите **Готово**, чтобы создать новое приложение Android. 

    ![][25]

4. Убедитесь, что **Целевая сборка проекта** задана правильно.

    ![][26]

5. Загрузите и распакуйте [Мобильные службы Android SDK], откройте папку **notificationhubs**, скопируйте файл **notification-hubs-x.y.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

6. Загрузите и распакуйте [Push-уведомления Baidu Android SDK], откройте папку **libs** и скопируйте jar-файл *pushservice-x.y.z* и папки *armeabi* и *mips* в папку **libs** вашего Android-приложения. 

    ![][27]

7. Откройте **AndroidManifest.xml** вашего Android-проекта и добавьте разрешения, требующиеся для Baidu SDK.

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

8. Добавьте свойство *android:name* к вашему элементу *application* в **AndroidManifest.xml**, заменив *yourprojectname*, например, **com.example.BaiduTest**. Убедитесь, что это имя проекта совпадает с именем, заданным в консоли Baidu. 

		<application android:name="yourprojectname.DemoApplication"

9. Добавьте следующую конфигурацию в элементе приложения после элемента активности .MainActivity activity, заменив *yourprojectname*, например, **com.example.BaiduTest**:

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

9. Добавьте новый класс под названием **ConfigurationSettings.java** в проект. 

    ![][28]

    ![][29]

10. Добавьте следующий код к нему:

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	Присвойте значение *API_KEY* равное тому, которое вы получили ранее из облачного проекта Baidu, *NotificationHubName* присвойте имя вашего концентратора уведомлений с портала Azure, а *NotificationHubConnectionString* присвойте DefaultListenSharedAccessSignature также с портала Azure. 

11. Добавьте новый класс с именем **DemoApplication.java** и добавьте в него следующий код:

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Добавьте еще один новый класс с именем **MyPushMessageReceiver.java** и добавьте к нему приведенный ниже код. Это класс, который обрабатывает push-уведомления, полученные от push-сервера Baidu:

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

13. Откройте **MainActivity.java** и добавьте следующее к методу **onCreate**:

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

и добавьте следующие инструкции импорта в начало:
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##<a id="send"></a>Отправка уведомлений для приложения

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента, использующего <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx">интерфейс REST</a>. В этом учебнике демонстрируется использование консольного приложения .NET. 

1. Создайте новое консольное приложение Visual C#.

	![][30]

2. Добавьте ссылку на пакет Windows Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета NuGet WindowsAzure.ServiceBus</a>. В главном меню Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Затем в окне консоли введите следующее и нажмите клавишу Enter:

        Install-Package WindowsAzure.ServiceBus

3. Откройте файл Program.cs и добавьте следующий оператор using:

        using Microsoft.ServiceBus.Notifications;

4. В ваш класс `Program` добавьте следующий метод и замените *DefaultFullSharedAccessSignatureSASConnectionString* и *NotificationHubName* имеющимися у вас значениями. 

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

1. В верхней панели инструментов Eclipse нажмите кнопку "Выполнить" и выберите приложение. 

2. При этом ваше приложение загружается на подключенный телефон, либо запускается эмулятор, который загружает и запускает это приложение.

3. Приложение извлекает userId и channelId из службы push-уведомлений Baidu и регистрируется в центре уведомлений.
	
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
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Портал управления Azure]: https://manage.windowsazure.com/
[Портал Baidu]: http://www.baidu.com/








	
