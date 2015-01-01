<properties pageTitle="Приступая к работе с push-уведомлениями (Appcelerator) | Центр мобильных разработок" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="11/24/2014" ms.author="Appcelerator team;mahender" />

# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# в Магазине Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript в Магазине Windows</a>
	<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
	<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
	<a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

В этом разделе показано, как использовать мобильные службы Windows Azure для отправки push-уведомлений в приложения на платформах iOS и Android, разработанные в Appcelerator Titanium Studio. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS) и Google Cloud Messaging. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

>[WACOM.NOTE] Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и лучшее масштабирование. В этом разделе рассматривается поддержка существующих мобильных служб, которые еще не были обновлены для использования интеграции с центрами уведомлений. При создании новой мобильной службы эта встроенная функция включается автоматически. По возможности необходимо обновить службу для использования центров уведомлений. **Мы подготавливаем к выпуску учебник по использованию push-уведомлений в центрах уведомлений с Appcelerator.**

1.	[Создание запроса подписи сертификата]
2.	[Регистрация приложения и включение push-уведомлений]
3.	[Создание профиля подготовки для приложения]
4.	[Включение Google Cloud Messaging]
5.  [Создание модуля GCM для Titanium]
6.	[Настройка мобильных служб]
7.	[Добавление push-уведомлений в приложение]
8.	[Обновление сценариев для отправки push-уведомлений]
9.	[Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

* Модуль мобильных служб Appcelerator Azure
* Appcelerator Titanium Studio 3.2.1 или выше
* Устройство с поддержкой iOS 7.0 (или более поздней версии) и Android 4.3 (или более поздней версии)
* Участие в программе для разработчиков на платформе iOS
* Активная учетная запись Google

> [WACOM.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с этим учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами].

[WACOM.INCLUDE [Включение службы push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Включение Google Cloud Messaging

>[WACOM.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[WACOM.INCLUDE [Включение GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##  <a name="gcm-module"></a>Создание модуля GCM для Titanium

### Подготовка Appcelerator Titanium Studio для создания модуля

Для создания модулей Android необходимо установить поддержку Java в Appcelerator Titanium Studio. См. раздел [Installing the Java Development Tools] (Установка инструментов разработки Java) для Appcelerator с кратким описанием необходимых действий, если вы этого еще не сделали.

Кроме этого, понадобится установить пакет Android NDK. Скачайте соответствующий ZIP-файл, перейдя по ссылке [http://developer.android.com/sdk/ndk/index.html],(http://developer.android.com/sdk/ndk/index.html) и распакуйте его в любое место на диске. Запомните это местоположение. 

### Создание нового модуля

1. Откройте Appcelerator Titanium Studio.

2. Щелкните File -> New -> Mobile Module Project (Файл -> Создать -> Проект мобильного модуля).

    ![][0]

3. В следующем окне введите данных параметров проекта: 

    * **Имя проекта:** в нашем случае используется "notificationhub" (можно его оставить).

    * **Идентификатор модуля:** в данном случае используется com.winwire.notificationhub. Он должен совпадать с "идентификатором приложения".

    * **Цели развертывания:** в нашем примере выбираем Android.

    > [WACOM.NOTE] Важно, чтобы имя рабочей области не содержало пробелы, в противном случае у нас будут проблемы при создании компиляции.

    ![][1]

4. Нажмите кнопку "Далее" и введите информацию для модуля.

    ![][2]

5. Наконец, нажмите кнопку "Готово".

6. Откройте файл timodule.xml. И добавьте следующие изменения в тег Android.


        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>




> [WACOM.NOTE] В указанном выше коде необходимо заменить все вхождения текста "com.winwire.notificationhub" на имя пакета приложения (идентификатор модуля).

7. В модуле центра уведомлений щелкните правой кнопкой мыши папку src, перейдите в раздел "Создать" и выберите "папка". Присвойте папке имя com.google.android.gcm.

> [WACOM.NOTE] Если вы не видите элемент "папка" в разделе параметров "Создать", выберите "Другие", разверните "Общие" и выберите "Папка".

8. Теперь скачайте отсюда JAVA-файлы (gcm.zip) и скопируйте эти файлы во вновь созданную папку (com.google.android.gcm).

9. Далее найдите папку с именем идентификатора модуля и разверните ее. В этой папке можно увидеть список файлов ".java". Среди этих файлов откройте файл в именем вашего проекта +module.java (например, если имя проекта notificationhub, то это будет выглядеть как "NotificationhubModule.java") и добавьте приведенные ниже строки кода в верхней части.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

10. В том же файле найдите конструктор и замените его указанным ниже кодом.

        public NotificationhubModule() {
	        super();
	        _THIS = this;
        }

11. В этом же файле добавьте следующие строки кода в его нижней части.

        @Kroll.method
        public void registerC2dm(HashMap options) {
	        successCallback = (KrollFunction) options.get("success");
	        errorCallback = (KrollFunction) options.get("error");
	        messageCallback = (KrollFunction) options.get("callback");
	        String registrationId = getRegistrationId();
	        if (registrationId != null && registrationId.length() > 0) {
		        sendSuccess(registrationId);
	        } else {
		        GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
	        }
        }
        @Kroll.method
        public void unregister() {
	        GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
	        return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
	        return TiApplication.getInstance().getAppProperties()
	        .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
	        if (successCallback != null) {
		        HashMap data = new HashMap();
		        data.put("registrationId", registrationId);
		        successCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendError(String error) {
	        if (errorCallback != null) {
		        HashMap data = new HashMap();
		        data.put("error", error);
		        errorCallback.callAsync(getKrollObject(), data);
	        }
        }

        public void sendMessage(HashMap messageData) {
	        if (messageCallback != null) {
		        HashMap data = new HashMap();
		        data.put("data", messageData);
		        messageCallback.call(getKrollObject(), data);
	        }
        }
        public static NotificationhubModule getInstance() {
	        return _THIS;
        }

12. Теперь загрузите module.zip и скопируйте файлы в папку с идентификатором модуля в качестве имени.

> [WACOM.NOTE] В указанных выше файлах необходимо заменить все вхождения текста "com.winwire.notificationhub" на имя пакета приложения (идентификатор модуля). Также замените NotificationhubModule на значение, состоящее из имени проекта и слова Module (например, NotificationhubModule).

### Создание/ упаковка модуля

Выберите **Развернуть > Упаковать - Модуль Android**. Модуль BlackBerry с помощью Studio создать нельзя. Используйте средства BlackBerry NDK CLI либо Momentics IDE. 

![][3]


Далее можно выбрать развертывание модуля для все проектов или для определенного проекта. Эта процедура выполняется согласно правилам установки, приведенным в разделе [Использование модулей Titanium]. Тем не менее, кратко их обобщим:

- Для всех проектов: ZIP-файл модуля вставляется в корневую папку местоположения установки Titanium SDK. 

- Для конкретного проекта: ZIP-файл модуля вставляется в корневую папку вашего проекта. 


## <a name="configure"></a>Настройка мобильных служб для отправки push-запросов


[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

5.	Введите значение ключа API, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку Сохранить.

    ![][4]

Ваша мобильная служба теперь настроена для работы с GCM и службой APNS.

## <a name="add-push"></a>Добавление push-уведомлений в приложение

1.	В tiapp.xml перейдите на вкладку tiapp.xml (эту вкладку можно найти внизу рядом с вкладкой "Обзор") и найдите тег <android>. Ниже тега добавьте следующий код:

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

>[WACOM.NOTE] В указанном выше коде необходимо заменить **ModuleId** и **ApplicationId**. Идентификатор модуля получен во время создания модуля GCM, а идентификатор приложения вводится во время создания проекта.  Значения **ModuleId** и **ApplicationId** должны быть одинаковыми. Кроме того, необходимо изменить **ApplicationId.AppNameActivity**. Оно должно выглядеть так : com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.


2.	Скопируйте созданный ранее модуль GCM и разместите его в указанном ниже месте.	

    <table><tr>
    <td>OSX
    </td>
    <td>/Library/Application Support/Titanium or  ~/Library/Application Support/Titanium
    </td>
    </tr>
    <td>Windows 7
    </td>
    <td>C:\Users\username\AppData\Roaming (or C:\ProgramData\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Windows XP
    </td>
    <td>C:\Documents and Settings\username\Application Data (or C:\Documents and Settings\All Users\Application Data\Titanium on Titanium Studio 1.0.1 and earlier)
    </td>
    </tr><td>Linux
    </td>
    <td>~/.titanium
    </td>
    </tr>
    </tr>
    
    </table>

>[WACOM.NOTE]  В Mac OS библиотека представляет собой скрытую папку. Чтобы сделать ее видимой, необходимо выполнить следующую команду, а затем перезапустить поисковик. "**defaults write com.apple.finder AppleShowAllFiles TRUE**"

3.	В Appcelerator Titanium Studio откройте файл index.js и добавьте следующий код в нижней части. Этот код выполняет регистрацию устройства для получения push-уведомлений.

            Alloy.Globals.tempRegId = '';
    	        if (OS_ANDROID) {
    	        var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }
   

4.	Теперь откройте файл TableData.js и найдите следующие строки в функции **addOrUpdateDataFromAndroid**

        var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
  

    Замените указанный выше код только в условии "else" (для вставки нового элемента).

5.	Для Android замените указанный выше код на следующий:

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'handle' : Alloy.Globals.tempRegId
    }; 
    
    

6.	Теперь найдите следующие строки в функции **updateOrAddData**

           var request = {
    	    'text' : alertTextField.getValue(),
    	    'complete' : false
        };
    
    Замените указанный выше код только в условии "else" (для вставки нового элемента).

7.	Для iOS замените указанный выше код на следующий:

       var request = {
    	'text' : alertTextField.getValue(),
    	'complete' : false,
    	'deviceToken' : Alloy.Globals.tempRegId
    };
    
 
Приложение теперь обновлено и поддерживает push-уведомления на платформах iOS и Android.


## <a name="update-scripts"></a>Обновление зарегистрированных сценариев вставки на портале управления

1.	На портале управления щелкните вкладку Данные, а затем щелкните таблицу TodoItem.

    ![][5]

2.	В таблице todoitem щелкните вкладку Скрипт и выберите Вставить.

    ![][6]

    При этом отображается функция, вызываемая при выполнении вставки в таблице TodoItem.

3.	Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

**Для iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }
  
   	> [WACOM.NOTE] Этот сценарий задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения push-уведомления.  

**Для Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }



При этом регистрируется новый сценарий вставки, в котором используется [push-объект мобильных служб] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.


<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Включение Google Cloud Messaging]: #register-gcm
[Создание модуля GCM для Titanium]: #gcm-module
[Настройка мобильных служб]: #configure
[Добавление push-уведомлений в приложение]: #add-push
[Обновление сценариев для отправки push-уведомлений]: #update-scripts
[Вставка данных для получения уведомлений]: #test

<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[Использование модулей Titanium]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
[Портал управления Microsoft Azure]: https://manage.windowsazure.com/
[Push-объект мобильных служб]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
[Установка инструментов разработки Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools

<!--HONumber=35_1-->
