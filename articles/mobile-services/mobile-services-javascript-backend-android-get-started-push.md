
<properties
	pageTitle="Приступая к работе с push-уведомлениями (JavaScript — Android) | Microsoft Azure"
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
	ms.date="10/06/2015"
	ms.author="ricksal"/>


# Добавление push-уведомлений в приложение мобильных служб Android

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## Сводка

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android с помощью Google Cloud Messaging (GCM). Вы добавите push-уведомления в проект быстрого запуска, что является необходимым условием для этого руководства. Push-уведомления включаются с помощью центра уведомлений Azure, который включен мобильную службу. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

## Предварительные требования

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Пример кода
Просмотреть полный исходный код можно [здесь](https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPush).

## Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Настройка мобильных служб для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Добавление push-уведомлений в приложение



Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Play](../../includes/mobile-services-add-google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


## Обновление зарегистрированных сценариев вставки на портале управления

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


## Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###Настройка эмулятора Android для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. В правом конце панели инструментов выберите диспетчер виртуальных устройств Android, выберите свое устройство и щелкните значок изменения справа.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Выберите **Изменить** в строке описания устройства, выберите **Интерфейсы Google API** и нажмите кнопку «ОК».

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

###Выполнение теста

1. В меню **Запуск** выберите **Запуск приложения**, чтобы запустить приложение.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_, а затем нажмите кнопку **Добавить**.

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз из верхней части экрана, чтобы открыть панель уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.

## Устранение неполадок

### Проверка версии Android SDK

[AZURE.INCLUDE [Проверка пакета SDK](../../includes/mobile-services-verify-android-sdk-version.md)]

## Дальнейшие действия

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с проверкой подлинности] <br/>Узнайте, как проверять подлинность пользователей приложения с разными типами учетных записей, используя мобильные службы.

* [Что такое концентраторы уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений Центров уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Устранение неполадок и отладка решений на основе Центров уведомлений.

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
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
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
[Что такое концентраторы уведомлений?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md

<!---HONumber=Oct15_HO3-->