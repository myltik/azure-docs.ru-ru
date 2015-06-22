
<properties 
	pageTitle="Приступая к работе с push-уведомлениями (Android JavaScript) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение JavaScript для Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	writer="ricksal" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal"/>


# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

## Сводка

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android с помощью Google Cloud Messaging (GCM). В этом учебнике объясняется включение push-уведомлений с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->

> [AZURE.NOTE]Чтобы просмотреть версию этого учебника для Eclipse, перейдите к следующей статье: [Приступая к работе с push-уведомлениями (Eclipse)].


##Предварительные требования

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Настройка мобильных служб для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[AZURE.INCLUDE [Проверка пакета SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Google Play](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###Настройка эмулятора Android для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, щелкните **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Проект будет предназначен для API-интерфейсов Google.

2. В **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, щелкните **Изменить**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Выберите **API-интерфейсы Google** в **Цель**, а затем нажмите кнопку ОК.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

###Выполнение теста

1. В меню **Выполнить** в Eclipse выберите **Выполнить** для запуска приложения.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_, а затем нажмите кнопку **Добавить**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.


## <a name="next-steps"> </a>Дальнейшие действия

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными] <br/>Узнайте больше о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности] <br/>Узнайте, как проверять подлинность пользователей приложения с разными типами учетных записей, используя мобильные службы.

* [Что такое концентраторы уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Получите руководство по устранению неполадок и отладке решений концентраторов уведомлений.

* [Использование клиентской библиотеки Android для мобильных служб] <br/>Узнайте, как использовать мобильные службы с Android.

* [Справочник серверных скриптов мобильных служб] <br/>Узнайте, как реализовать бизнес-логику в мобильной службе.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Приступая к работе с push-уведомлениями (Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Приступая к работе с данными]: mobile-services-android-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Использование клиентской библиотеки Android для мобильных служб]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[Что такое концентраторы уведомлений?]: notification-hubs/notification-hubs-overview.md
[Send broadcast notifications to subscribers]: notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: notification-hubs-android-send-localized-breaking-news.md

<!--HONumber=54-->