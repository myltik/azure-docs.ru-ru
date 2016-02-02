<properties
	pageTitle="Приступая к работе с Центрами уведомлений Azure с помощью Baidu | Microsoft Azure"
	description="Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений на устройства Android с помощью Baidu."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="mobile-baidu"
	ms.workload="mobile"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Приступая к работе с Центрами уведомлений с помощью Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

Push-облако Baidu — это китайская облачная служба, которую можно использовать для отправки push-уведомлений на мобильные устройства. Эта служба особенно полезна в Китае, где доставка push-уведомлений на устройства под управлением Android усложняется наличием различных магазинов приложений и служб передачи объектов, а также доступностью устройств Android, которые обычно не подключены к службе GCM (Google Cloud Messaging).

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ Пакет SDK для Android (предполагается, что вы будете использовать Eclipse), который можно скачать с <a href="http://go.microsoft.com/fwlink/?LinkId=389797">сайта Android</a>.
+ [Пакет Android SDK для мобильных служб]
+ [Пакет Android SDK для Baidu Push]

>[AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##Создание учетной записи Baidu

Чтобы использовать Baidu, необходимо иметь учетную запись Baidu. Если она у вас уже есть, войдите на [портал Baidu] и перейдите к следующему шагу. В противном случае создайте учетную запись Baidu, следуя приведенным ниже инструкциям.

1. Откройте [портал Baidu] и перейдите по ссылке **登录** (**Вход**). Щелкните **立即注册**, чтобы начать процесс регистрации учетной записи.

   	![][1]

2. Введите требуемые сведения — номер телефона и/или адрес электронной почты, пароль и код подтверждения, — а затем нажмите кнопку **Signup** (Зарегистрироваться).

   	![][2]

3. На указанный вами адрес электронной почты будет отправлено сообщение со ссылкой для активации учетной записи Baidu.

   	![][3]

4. Войдите в свой почтовый ящик, откройте это сообщение и перейдите по ссылке, чтобы активировать учетную запись Baidu.

   	![][4]

После активации войдите на [портал Baidu] с помощью созданной учетной записи.

##Регистрация в качестве разработчика Baidu

1. После входа на [портал Baidu] щелкните **更多>>** (**Дополнительно**).

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务 (Службы для веб-мастеров и разработчиков)** и щелкните **百度开放云平台** (**Открытая облачная платформа Baidu**).

  	![][6]

3. В правом верхнем углу следующей страницы щелкните пункт **开发者服务** (**Службы для разработчиков**).

  	![][7]

4. На следующей странице в правом верхнем углу меню щелкните пункт **注册开发者** (**Зарегистрированные разработчики**).

  	![][8]

5. Введите свое имя, описание и номер мобильного телефона, чтобы получить текстовое сообщение с подтверждением, а затем щелкните **送验证码** (**Отправить код подтверждения**). Обратите внимание, что код страны для международных номеров необходимо заключить в круглые скобки. Например, номер телефона в США будет выглядеть так: **(1)1234567890**.

  	![][9]

6. После этого вы должны получить текстовое сообщение с номером проверки, как показано в следующем примере.

  	![][10]

7. Введите код подтверждения из сообщения в поле **验证码** (**Код подтверждения**).

8. Наконец, для завершения регистрации разработчика примите соглашение Baidu и щелкните **提交** (**Отправить**). При успешном завершении регистрации появится следующая страница:

  	![][11]

##Создание облачного push-проекта Baidu

Во время создания push-проекта Baidu вы получите код приложения, ключ API и секретный ключ.

1. После входа на [портал Baidu] щелкните **更多>>** (**Дополнительно**).

  	![][5]

2. Прокрутите вниз раздел **站长与开发者服务** (**Службы для веб-мастеров и разработчиков**) и щелкните **百度开放云平台** (**Открытая облачная платформа Baidu**).

  	![][6]

3. В правом верхнем углу следующей страницы щелкните пункт **开发者服务** (**Службы для разработчиков**).

  	![][7]

4. На следующей странице щелкните **云推送** (**Отправка данных в облако**) в разделе **云服务** (**Облачные службы**).

  	![][12]

5. По завершении регистрации разработчика в верхнем меню появится **管理控制台** (**Консоль управления**). Щелкните **开发者服务管理** (**Управление службой разработки**).

  	![][13]

6. На следующей странице щелкните **创建工程** (**Создать проект**).

  	![][14]

7. Введите имя приложения и щелкните **创建** (**Создать**).

  	![][15]

8. После успешного создания проекта передачи данных в облако Baidu отобразится страница с **идентификатором приложения**, **ключом API** и **секретным ключом**. Запишите ключ API и секретный ключ, которые будут использоваться позже.

  	![][16]

9. Настройте проект для отправки push-уведомлений, щелкнув **云推送** (**Отправка данных в облако**) в области слева.

  	![][31]

10. На следующей странице нажмите кнопку **推送设置** (**Параметры отправки**).

	![][32]

11. На странице настроек добавьте имя пакета, который вы будете использовать в своем проекте Android, в поле **应用包名** (**Пакет приложения**) и щелкните **保存设置** (**Сохранить**).

	![][33]

Появится сообщение **保存成功！** (**Успешно сохранено!)**.

##Настройка концентратора уведомлений

1. Войдите на [классический портал Azure] и в нижней части экрана нажмите кнопку **+Создать**.

2. Щелкните **Службы приложений**, выберите **Служебная шина**, затем щелкните **Концентратор уведомлений** и нажмите кнопку **Быстрое создание**.

3. Введите имя **Концентратора уведомлений**, затем выберите **Регион** и **Пространство имен**, в котором будет создан этот концентратор уведомлений. После этого щелкните **Создать новый концентратор уведомлений**.

  	![][17]

4. Выберите пространство имен, в котором создан концентратор уведомлений, и щелкните вверху **Концентраторы уведомлений**.

  	![][18]

5. Выделите созданный концентратор уведомлений и в верхнем меню выберите пункт **Настройка**.

  	![][19]

6. Прокрутите вниз до раздела **Параметры уведомлений Baidu**, а затем введите ключ API и секретный ключ, полученные ранее для службы передачи данных в облако Baidu из консоли Baidu. Щелкните **Сохранить**.

  	![][20]

7. Откройте вкладку **Панель мониторинга** в верхней части меню концентратора уведомлений и щелкните **Просмотреть строку подключения**.

  	![][21]

8. Запишите значения **DefaultListenSharedAccessSignature** и **DefaultFullSharedAccessSignature**, отображенные в окне **Сведения по доступу к подключению**.

    ![][22]

##Подключение приложения к концентратору уведомлений

1. В ADT Eclipse создайте новый проект Android (**Файл** > **Создать** > **Проект приложения Android**).

    ![][23]

2. Введите **Имя приложения** и убедитесь, что для параметра **Минимальная требуемая версия пакета SDK** указано значение **API 16: Android 4.1**.

    ![][24]

3. Щелкните **Далее** и следуйте указаниям мастера до появления окна **Создать действие**. Выберите элемент **Новое действие** и нажмите кнопку **Готово**, чтобы создать новое приложение Android.

    ![][25]

4. Убедитесь, что значение параметра **Целевая сборка проекта** задано правильно.

    ![][26]

5. На странице [Notification-Hubs-Android-SDK сайта Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) на вкладке **Files** (Файлы) скачайте файл notification-hubs-0.4.jar. Добавьте файл в папку **libs** проекта Eclipse и обновите папку *libs*.

6. Скачайте и распакуйте [пакет Android SDK для службы рush-уведомлений Baidu], откройте папку **libs** и скопируйте JAR-файл **pushservice-x.y.z**, а также папки **armeabi** и **mips** в папке **libs** приложения Android.

7. Откройте файл **AndroidManifest.xml** проекта Android и добавьте разрешения, требуемые пакетом SDK для Baidu.

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

8. Добавьте свойство **android:name** в элемент **application** файла **AndroidManifest.xml**, заменив *yourprojectname* (например, **com.example.BaiduTest**). Убедитесь, что имя этого проекта соответствует имени, заданному в консоли Baidu.

		<application android:name="yourprojectname.DemoApplication"

9. Добавьте следующую конфигурацию в элементе приложения после элемента действия **.MainActivity**, заменив *yourprojectname* (например, **com.example.BaiduTest**).

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

	Присвойте параметру **API\_KEY** значение, которое вы получили ранее из облачного проекта Baidu, а параметрам **NotificationHubName** и **NotificationHubConnectionString** — имя вашего центра уведомлений и значение DefaultListenSharedAccessSignature соответственно, полученные на классическом портале Azure.

11. Добавьте новый класс с именем **DemoApplication.java**, затем добавьте в него следующий код:

		import com.baidu.frontia.FrontiaApplication;

		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. Добавьте еще один новый класс с именем **MyPushMessageReceiver.java** и добавьте в него приведенный ниже код. Это класс, который обрабатывает push-уведомления, полученные от push-сервера Baidu.

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

13. Откройте класс **MainActivity.java** и добавьте следующий код в метод **onCreate**.

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Затем добавьте следующие инструкции импорта в начало.

			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

##Отправка уведомлений в приложение


Получение уведомлений в приложении можно проверить, отправив уведомления на классическом портале Azure на вкладке отладки центра уведомлений, как показано на следующем рисунке.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-debug.png)

Push-уведомления обычно отправляются в серверной службе, например мобильными службами или ASP.NET, с помощью совместимой библиотеки. Также можно напрямую использовать REST API для отправки уведомлений, если для серверной части библиотека недоступна.

В этом учебнике мы пойдем по простому пути и продемонстрируем тестирование клиентского приложения, отправляя уведомления с помощью пакета SDK для .NET для центров уведомлений не в серверную службу, а в консольное приложение. В качестве следующего шага по отправке уведомлений с сервера ASP.NET рекомендуем ознакомиться с руководством [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md). Можно использовать следующие способы отправки уведомлений.

* **Интерфейс REST**. [Интерфейс REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) поддерживает уведомления на любой серверной платформе.

* **Пакет SDK .NET для концентраторов уведомлений Microsoft Azure**. В диспетчере пакетов NuGet для Visual Studio выполните команду [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js**. [Использование Центров уведомлений из Node.js](notification-hubs-nodejs-how-to-use-notification-hubs.md).

* **Мобильные службы Azure**. Примеры отправки уведомлений из интерфейса мобильных служб Azure, интегрированного с концентраторами уведомлений, см. в статье «Добавление push-уведомлений к приложению мобильных служб» ([серверная часть .NET](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [серверная часть JavaScript](../mobile-services/mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)).

* **Java или PHP**. Примеры отправки уведомлений с использованием REST API см. в статье «Использование концентраторов уведомлений из Java/PHP» ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

##(Необязательно) Отправление уведомлений из консольного приложения .NET

В этом разделе мы будем отправлять уведомление, используя консольное приложение .NET.

1. Создайте новое консольное приложение Visual C#.

	![][30]

2. В окне консоли диспетчера пакетов задайте свойство **Проект по умолчанию** для нового проекта консольного приложения, а затем в окне консоли выполните следующую команду:

        Install-Package Microsoft.Azure.NotificationHubs

	После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета NuGet Microsoft.Azure.Notification Hubs</a>.

	![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Откройте файл **Program.cs** и добавьте следующую инструкцию using:

        using Microsoft.Azure.NotificationHubs;

4. В классе `Program` добавьте следующий метод, а затем замените *DefaultFullSharedAccessSignatureSASConnectionString* и *NotificationHubName* своими значениями.

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. Добавьте в метод **Main** следующие строки:

         SendNotificationAsync();
		 Console.ReadLine();

##Тестирование приложения

Чтобы проверить работу этого приложения на реальном устройстве, просто подключите телефон к компьютеру с помощью кабеля USB. Приложение загрузится на подключенный телефон.

Чтобы протестировать это приложение с помощью эмулятора, на верхней панели инструментов Eclipse нажмите кнопку **Run** (Запуск) и выберите приложение. Это действие запускает эмулятор и выполняет приложение.

Приложение получает значения userId и channelId из службы push-уведомлений Baidu с последующей регистрацией в концентраторе уведомлений.

Тестовые уведомления можно отправлять на вкладке отладки на классическом портале Azure. Если вы собрали консольное приложение .NET для Visual Studio, нажмите клавишу F5 в Visual Studio для запуска приложения. Приложение отправит уведомление, которое появится в верхней части области уведомлений устройства или эмулятора.


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
[Пакет Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Пакет Android SDK для Baidu Push]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[пакет Android SDK для службы рush-уведомлений Baidu]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[классический портал Azure]: https://manage.windowsazure.com/
[портал Baidu]: http://www.baidu.com/

<!---HONumber=AcomDC_0128_2016-->