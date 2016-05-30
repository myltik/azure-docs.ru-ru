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
	ms.date="05/02/2016"
	ms.author="glenga"/>

# Добавление push-уведомлений в приложение Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Обзор

В этом учебнике описывается добавление push-уведомлений в [ознакомительный проект Apache Cordova], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал [ознакомительного проекта Apache Cordova], инструкции из которого необходимо выполнить в первую очередь. Если у вас есть серверная часть ASP.NET и вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure].

##<a name="prerequisites"></a>Предварительные требования

В этом учебнике используется приложение Apache Cordova, разработанное в Visual Studio 2015 и выполняемое в эмуляторе Google Android. Инструкции учебника можно также выполнять на других эмуляторах или устройствах, а также на других платформах разработки.

Для работы с этим учебником требуется:

* [Учетная запись Google] с подтвержденным адресом электронной почты.
* Компьютер с [Visual Studio Community 2015] или более поздней версии.
* [Средства Visual Studio для Apache Cordova].
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).
* Выполненный [ознакомительный проект Apache Cordova]. Другие учебники (например по [проверке подлинности]) могут быть пройдены раньше, но это не является обязательным условием.
* Устройство Android.

Несмотря на то, что эмуляторы Android поддерживают push-уведомления, они работают нестабильно, поэтому тестирование push-уведомлений на эмуляторах не рекомендуется.

##<a name="create-hub"></a> Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Включение Google Cloud Messaging

Поскольку проект предназначен для платформы Google Android, необходимо включить Google Cloud Messaging. Если проект предназначен для устройств Apple iOS, включите поддержку APNS. Если проект предназначен для устройств Microsoft Windows, включите поддержку WNS или MPNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Настройка серверной части мобильного приложения для отправки push-запросов

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a> Настройка устройства Android для отладки USB

Перед развертыванием приложения на устройстве Android необходимо включить отладку USB. На телефоне Android выполните следующие действия:

1. Последовательно выберите пункты **Параметры** > **О телефоне**, коснитесь пункта **Номер сборки** и дождитесь включения режима разработчика (примерно 7 секунд).
 
2. Вернитесь в раздел **Параметры** > **Параметры разработчика**, включите **отладку USB**, а затем подключите телефон Android к компьютеру разработки с помощью USB-кабеля.

Мы проверили этот способ, используя устройство Google Nexus 5X с Android 6.0 (Marshmallow). Все описанные приемы подходят для любой современной версии платформы Android.

##<a name="add-push-to-app"></a>Добавление push-уведомлений в приложение

Необходимо убедиться в том, что проект приложения Apache Cordova готов для обработки push-уведомлений. Необходимо установить подключаемый модуль push-уведомлений Cordova, а также все службы push-уведомлений для конкретной платформы.

### Установка подключаемого модуля push-уведомлений

Приложения Apache Cordova не обрабатывают возможностей устройства или сети изначально. Эти возможности обеспечиваются подключаемыми модулями, которые публикуются в [npm](https://www.npmjs.com/) или GitHub. Подключаемый модуль `phonegap-plugin-push` используется для обработки push-уведомлений сети.

Подключаемый модуль push-уведомлений можно установить одним из следующих способов:

**из командной строки:**

Выполните следующую команду.

    cordova plugin add phonegap-plugin-push

**в Visual Studio:**

1.  В обозревателе решений откройте файл `config.xml`, щелкните **Подключаемые модули** > **Настраиваемые**, выберите **Git** как источник установки, затем в качестве источника введите `https://github.com/phonegap/phonegap-plugin-push`.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Щелкните стрелку рядом с источником установки, а затем щелкните **Добавить**.

Теперь подключаемый модуль push-уведомлений установлен.

### Установка служб Google Play

Подключаемый модуль push-уведомлений передает push-уведомления через службы Google Play Android.

1.  В **Visual Studio** щелкните **Инструменты** > **Android** > **Android SDK Manager**, разверните папку **Дополнения** и убедитесь, что задана установка всех следующих пакетов SDK.    
    * Библиотека поддержки Android версии 23 или более поздней
    * Репозиторий поддержки Android версии 20 или более поздней
    * Службы Google Play версии 27 или более поздней
    * Репозиторий Google версии 22 или более поздней
     
2.  Щелкните **Установить пакеты** и дождитесь завершения установки.

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

	В этом примере показан вызов **registerForPushNotifications** после успешного прохождения проверки подлинности, что рекомендуется при использовании в приложении push-уведомлений и проверки подлинности.

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

## Тестирование push-уведомлений в приложении 

Теперь вы можете проверить работу push-уведомлений путем запуска приложения и вставки элементов в таблицу TodoItem. Это делается на том же (или другом) устройстве при условии, что используется одна серверная часть. Проверьте работу приложения Cordova на платформе Android одним из следующих способов.

- **На физическом устройстве**. Подключите устройство Android к компьютеру разработчика с помощью кабеля USB. Вместо **Эмулятор Google Android** выберите **Устройство**. Visual Studio выполнит развертывание приложения на устройстве и запустит его. После этого вы сможете работать с приложением на устройстве. Усовершенствуйте интерфейс разработки. В разработке приложения Android вам помогут приложения для демонстрации экрана, например [Mobizen], транслирующие экран Android в веб-браузер на компьютере.

- **В эмуляторе Android**. Прежде чем вы сможете получать push-уведомления, необходимо добавить учетную запись Google в эмулятор.

##<a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о push-уведомлениях см. в статье о [центрах уведомлений].
* Продолжите работу с учебником [Добавление проверки подлинности в приложение Apache Cordova], если вы этого еще не сделали.

Подробнее об использовании пакетов SDK.

* [Пакет SDK для Apache Cordova]
* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

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
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Средства Visual Studio для Apache Cordova]: https://www.visualstudio.com/ru-RU/features/cordova-vs.aspx
[центрах уведомлений]: ../notification-hubs/notification-hubs-overview.md
[Пакет SDK для Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0518_2016-->