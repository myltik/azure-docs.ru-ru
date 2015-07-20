<properties
	pageTitle="Начало работы с Azure Mobile Engagement для Cordova (Phonegap)"
	description="Узнайте, как использовать Azure Mobile Engagement для аналитики и отправки push-уведомлений в приложения Cordova (Phonegap)."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Начало работы с Azure Mobile Engagement для Cordova (Phonegap)

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement для анализа использования приложений и отправки push-уведомлений определенным группам пользователей мобильного приложения, разработанного на платформе Cordova.

В этом учебнике вы создадим пустое приложение Cordova на компьютере Mac и интегрируем пакет Mobile Engagement SDK. Приложение будет собирать базовые данные аналитики и получать push-уведомления с помощью системы push-уведомлений Apple (APNS) для iOS и Google Cloud Messaging (GCM) для Android. Для тестирования мы развернем его на устройствах под управлением iOS или Android.

> [AZURE.IMPORTANT]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>;

Для работы с данным учебником требуется следующее:

+ Xcode, который можно установить из Mac App Store (для развертывания на iOS)
+ [Android SDK и эмулятор](http://developer.android.com/sdk/installing/index.html) (для развертывания на Android)
+ Сертификат push-уведомлений (P12), который можно получить в центре разработки Apple (для APNS)
+ Номер проекта GCM, который можно найти в консоли разработчиков Google (для GCM)
+ [Подключаемый модуль Cordova для Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Исходный код и файл сведений о подключаемом модуле Cordova можно найти на сайте [Github](https://github.com/Azure/azure-mobile-engagement-cordova).

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения

1. Выполните вход на портал управления Azure, затем нажмите **+NEW** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем **Создать**.

   	![][1]

3. Во всплывающем окне введите следующую информацию:

   	![][2]

	- **Имя приложения** — имя вашего приложения. 
	- **Платформа** — целевая платформа приложения (выберите **iOS** или **Android** в зависимости от того, где вы планируете развернуть приложение Cordova).
	- **Имя ресурса приложения** — имя, по которому приложение можно будет открывать через API или используя URL-адрес. 
	- **Расположение** — центр обработки данных, в котором будет размещаться приложение и коллекция приложений.
	- **Коллекция** — укажите существующую или создайте новую коллекцию.
	- **Имя коллекции** — имя вашей группы приложений. Все ваши приложения будут добавлены в одну группу, что позволит агрегировать метрики. Здесь следует указать имя вашей компании или отдела, если применимо.

4. На вкладке **Приложения** выберите только что созданное приложение.

5. Щелкните **Информация о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK в вашем мобильном приложении.

   	![][3]

6. Скопируйте **строку подключения**. Это требуется для того, чтобы обозначить данное приложение в коде приложения и подключиться к Mobile Engagement, используя для этого телефонное приложение.

   	![][4]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений.

Чтобы продемонстрировать интеграцию, мы создадим простое приложение при помощи Сordova.

###Создание нового проекта Cordova

1. Чтобы создать новый проект Cordova на основе стандартного шаблона, откройте *окно терминала* на компьютере Mac и введите следующую команду:

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]Убедитесь, что в профиле публикации, который вы впоследствии будете использовать для развертывания приложения iOS, используется идентификатор приложения "com.mycompany.myapp".

2. Выполните следующие команды, чтобы настроить проект для **iOS** и запустить его в эмуляторе iOS:

		$ cordova platform add ios 
		$ cordova run ios

3. Выполните следующие команды, чтобы настроить проект для **Android** и запустить его в эмуляторе Android:

		$ cordova platform add android
		$ cordova run android

4. 	Добавьте консольный подключаемый модуль Cordova.

		$ cordova plugin add cordova-plugin-console 

###Подключение приложения к серверной части Mobile Engagement

1. Установите подключаемый модуль Cordova для Azure Mobile Engagement и укажите значения переменных для настройки подключаемого модуля:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	Например:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]Значения параметров AppId, SDKKey и Collection можно получить из строки подключения **Endpoint={YourAppCollection.Domain};SdkKey={YourSDKKey};AppId={YourAppId}**.

##<a id="monitor"></a>Включение мониторинга в реальном времени

1. В проекте Cordova добавьте в файл **www/js/index.js** вызов Mobile Engagement, чтобы объявить новое действие после получения события *deviceReady*.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Запустите приложение:
		
	- **Для iOS:**
	
		Запустите приложение в новом экземпляре эмулятора, выполнив следующую команду в окне `Terminal`:

			cordova run ios

	- **Для Android:**
		
		Запустите приложение в новом экземпляре эмулятора, выполнив следующую команду в окне `Terminal`:

			cordova run android

3. В журнале консоли можно увидеть следующее:

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###Убедитесь, что приложение подключено с использованием функции мониторинга в реальном времени

В этом разделе показано, как сделать так, чтобы приложение подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement

	На портале Azure убедитесь, что вы находитесь в приложении, которое используется для данного проекта, а затем нажмите кнопку **Использовать** внизу.

	![][6]

2. Вы перейдете на страницу параметров вашего приложения на **портале Engagement**. Откройте вкладку **Монитор**:

	![][7]

3. Если запущенное в эмуляторе приложение настроено правильно, вы увидите, что, когда оно выполняется, запись в журнал ведется в реальном времени:

	![][8]

##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

###Настройка учетных данных для отправки push-уведомлений из Mobile Engagement

Чтобы разрешить службе Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить ей доступ к сертификату Apple iOS или ключу API сервера GCM.
	
1. Перейдите на портал Mobile Engagement. Убедитесь, что находитесь в приложении, которое мы используем для этого проекта, и нажмите кнопку **Использовать** внизу страницы.
	
	![][10]
	
2. Вы перейдете на страницу "Параметры на портале Mobile Engagement. На портале выберите раздел **Собственные push-уведомления**.
	
	![][11]

3. Настройте сертификат Apple iOS или ключ API сервера GCM.

	**[iOS]**

	а. Выберите сертификат P12, загрузите его и введите пароль:
	
	![][12]

	**[Android]**

	а. Щелкните значок правки перед **ключом API** в разделе параметров GCM, как показано ниже.
		
	![][20]
	
	b. Во всплывающем окне вставьте ключ сервера GCM, а затем нажмите **ОК**.
	
	![][21]

###Включение push-уведомлений в приложении Cordova

Добавьте в файл **www/js/index.js** вызов Mobile Engagement для запроса push-уведомлений и объявления обработчика:

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Запуск приложения

**[iOS]**

1. При тестировании push-уведомлений для сборки и развертывания приложения на устройстве мы будем использовать XCode, поскольку iOS разрешает вывод push-уведомлений только на реальных устройствах. Откройте каталог, в котором создан проект Cordova, и перейдите в папку **...\\platforms\\ios**. Откройте XCODEPROJ-файл в XCode. 
	
2. Скомпилируйте и разверните приложение Cordova на устройстве iOS, используя учетную запись с профилем подготовки, содержащим сертификат, который вы только что загрузили на портал Mobile Engagement, и идентификатором приложения, введенным при создании приложения Cordova. Можно найти и проверить значение *идентификатора набора* в файле **Resources*-info.plist** в XCode.

3. На устройстве iOS появится стандартное всплывающее окно с уведомлением о том, что приложение запрашивает разрешение на отправку уведомлений. Предоставьте это разрешение.

**[Android]**

Для запуска приложения на Android можно просто использовать эмулятор Android, так как он поддерживает уведомления GCM.

	cordova run android

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение, запущенное на устройстве.

1. Перейдите на вкладку "Охват" на портале Mobile Engagement,

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений.

	![][13]

3. Введите данные для новой кампании.

	![][14]

	- 	Задайте имя кампании. 
	- 	**[Android]**. Выберите для параметра **Тип доставки** значение *Системное уведомление* — *Простое*.
	- 	Для времени доставки выберите значение: 
		- 	Для **iOS** — *Только вне приложения*.
		- 	Для **Android** — *В любое время*
		
		Это простой тип push-уведомления с некоторым текстом.
	- 	В тексте уведомления введите сначала заголовок в первой строке push-уведомления.
	- 	Затем введите сообщение во второй строке.

4. Перейдите к разделу «Содержимое» и выберите тип **Только уведомления**.

	![][15]

5. [Необязательно] Можно также указать URL-адрес действия. Убедитесь, что используется та же схема URL, что и для переменной **URL-АДРЕС ПЕРЕНАПРАВЛЕНИЯ AZME** подключаемого модуля, например *myapp://test*. 

5. Вы завершили настройку самой простой кампании. Теперь снова прокрутите страницу вниз и нажмите кнопку **Создать**, чтобы сохранить кампанию.
	
	![][16]

6. Последний шаг — **активация** кампании.
	
	![][17]

7. Теперь push-уведомления в рамках этой кампании будут отображаться на устройстве или в эмуляторе.

##<a id="next-steps"></a>Дальнейшие действия
[Обзор методов, доступных в пакете Cordova SDK для Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO2-->