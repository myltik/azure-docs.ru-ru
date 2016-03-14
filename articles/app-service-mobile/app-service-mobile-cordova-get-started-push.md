<properties
	pageTitle="Добавление push-уведомлений в приложение Apache Cordova с помощью мобильных приложений Azure | Служба приложений Azure"
	description="Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение Apache Cordova."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Добавление push-уведомлений в приложение Apache Cordova.

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Обзор

В этом учебнике описывается добавление push-уведомлений в [ознакомительный проект Apache Cordova], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал [ознакомительного проекта Apache Cordova], инструкции из которого необходимо выполнить в первую очередь. Если у вас есть серверная часть ASP.NET и вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure].

##<a name="prerequisites"></a>Предварительные требования

В этом учебнике используется приложение Apache Cordova, разработанное в Visual Studio 2015 и выполняемое в эмуляторе Google Android. Инструкции учебника можно также выполнять на других эмуляторах или устройствах, а также на других платформах разработки.

Для работы с этим учебником требуется:

* [Учетная запись Google] с подтвержденным адресом электронной почты.
* Компьютер с [Visual Studio Community 2015] или более поздней версии.
* [Средства Visual Studio для Apache Cordova].
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).
* Выполненный [ознакомительный проект Apache Cordova]. Другие учебники (например по [проверке подлинности]) могут быть пройдены раньше, но это не является обязательным условием.
* Устройство Android.

Несмотря на то, что эмуляторы Android поддерживают push-уведомления, они работают нестабильно, поэтому тестирование push-уведомлений на эмуляторах не рекомендуется.

##<a name="create-hub"></a>Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Включение Google Cloud Messaging

Поскольку проект предназначен для платформы Google Android, необходимо включить Google Cloud Messaging. Если проект предназначен для устройств Apple iOS, включите поддержку APNS. Если проект предназначен для устройств Microsoft Windows, включите поддержку WNS или MPNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Настройка серверной части мобильного приложения для отправки push-запросов

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>Настройка устройства Android для отладки USB

Перед развертыванием приложения на устройстве Android необходимо включить отладку USB. На телефоне Android выполните следующие действия:

1. Последовательно выберите пункты **Параметры** > **О телефоне**.
2. Коснитесь **номера сборки**, чтобы включить режим разработчика.
3. Вернитесь в раздел **Параметры**.
4. Выберите **Параметры разработчика**.
5. Включите **отладку USB**.
6. Подключите телефон Android к компьютеру разработчика с помощью кабеля USB.

В этом учебнике для целей тестирования используется устройство Google Nexus 5X под управлением Android 6.0 (Marshmallow). Все описанные приемы подходят для любой современной версии платформы Android.

##<a name="add-push-to-app"></a>Добавление push-уведомлений в приложение

Необходимо убедиться в том, что проект приложения Apache Cordova готов для обработки push-уведомлений.

### Установка подключаемого модуля Apache Cordova для push-уведомлений

Приложения Apache Cordova не обрабатывают возможностей устройства или сети изначально. Эти возможности обеспечиваются подключаемыми модулями, которые публикуются в [npm](https://www.npmjs.com/) или GitHub. Подключаемый модуль `phonegap-plugin-push` используется для обработки push-уведомлений сети.

Подключаемый модуль push-уведомлений можно установить одним из следующих способов:

**из командной строки:**

    cordova plugin add phonegap-plugin-push

**в Visual Studio:**

1.  Откройте файл `config.xml` в обозревателе решений.
2.  Щелкните **Подключаемые модули** > **Настраиваемые**, выберите **Git** как источник установки, затем в качестве источника введите `https://github.com/phonegap/phonegap-plugin-push`.
	
	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)
	
4.  Щелкните стрелку рядом с источником установки, а затем щелкните **Добавить**.

Теперь подключаемый модуль push-уведомлений установлен.

### Установка служб Android Google Play

Подключаемый модуль PhoneGap передает push-уведомления через службы Google Play. Для установки:

1.  Откройте **Visual Studio**.
2.  Щелкните **Сервис** > **Android** > **Android SDK Manager** (Диспетчер пакетов SDK для Android).
3.  Установите флажок рядом с каждым необходимым пакетом SDK в папке Extras, который не был установлен. Требуются следующие пакеты:
    * Библиотека поддержки Android версии 23 или более поздней
    * Репозиторий поддержки Android версии 20 или более поздней
    * Службы Google Play версии 27 или более поздней
    * Репозиторий Google версии 22 или более поздней
4.  Щелкните **Install Packages** (Установить пакеты).
5.  Дождитесь завершения процесса установки.

Необходимые на данный момент библиотеки перечислены в [документации по установке подключаемого модуля push-уведомлений PhoneGap].

### Регистрация устройства для получения push-уведомлений при запуске

1. Добавьте вызов метода **registerForPushNotifications** в обратный вызов при входе в систему или в конец метода **onDeviceReady**:

 
		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');
		
		        // Refresh the todoItems
		        refreshDisplay();
		
		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);
		
				// Added to register for push notifications.
		        registerForPushNotifications();
		
		    }, handleError);

	В этом примере показан вызов **registerForPushNotifications** после успешного прохождения аутентификации, что рекомендуется при использовании в приложении push-уведомлений и аутентификации.

2. Добавьте новый метод `registerForPushNotifications()` следующим образом:

	    // Register for Push Notifications.
		// Requires that phonegap-plugin-push be installed.
	    var pushRegistration = null;
	    function registerForPushNotifications() {
	        pushRegistration = PushNotification.init({
	            android: {
	                senderID: 'Your_Project_ID'
	            },
	            ios: {
	                alert: 'true',
	                badge: 'true',
	                sound: 'true'
	            },
	            wns: {
	
	            }
	        });
	
	        pushRegistration.on('registration', function (data) {
	            client.push.register('gcm', data.registrationId);
	        });
	
	        pushRegistration.on('notification', function (data, d2) {
	            alert('Push Received: ' + data.message);
	        });
	
	        pushRegistration.on('error', handleError);
	    }

3. В приведенном выше коде замените значение `Your_Project_ID` числовым идентификатором проекта своего приложения, полученным на сайте [Google Developer Console].

## Тестирование приложения с помощью опубликованной мобильной службы

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля. Вместо ** Эмулятор Google Android** выберите **Устройство**. Visual Studio загрузит приложение на устройство и запустит приложение. После этого вы будете работать с приложением на устройстве.

Усовершенствуйте интерфейс разработки. В разработке приложения Android вам помогут приложения для демонстрации экрана, например [Mobizen], транслирующие экран Android в веб-браузер на компьютере.

Можно также проверить приложение Android в эмуляторе Android. Не забудьте сначала добавить учетную запись Google в эмулятор.

##<a name="next-steps"></a>Дальнейшие действия

* Прочитайте о [центрах уведомлений], чтобы получить дополнительные сведения о push-уведомлениях.
* Продолжите работу с учебником [Добавление проверки подлинности в приложение Apache Cordova], если вы этого еще не сделали.

<!-- URLs -->
[Добавление проверки подлинности в приложение Apache Cordova]: app-service-mobile-cordova-get-started-users.md
[ознакомительного проекта Apache Cordova]: app-service-mobile-cordova-get-started.md
[ознакомительный проект Apache Cordova]: app-service-mobile-cordova-get-started.md
[проверке подлинности]: app-service-mobile-cordova-get-started-users.md
[Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Учетная запись Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[документации по установке подключаемого модуля push-уведомлений PhoneGap]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Средства Visual Studio для Apache Cordova]: https://www.visualstudio.com/ru-RU/features/cordova-vs.aspx
[центрах уведомлений]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0302_2016-->