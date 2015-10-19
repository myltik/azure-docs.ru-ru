<properties
	pageTitle="Приступая к работе с push-уведомлениями (Android) | Центр разработчиков мобильных устройств | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение .NET для Android."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="ricksal"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

Этот учебник создан на основе краткого руководства по мобильным службам. Прежде чем приступить к изучению данного учебника, необходимо выполнить задания учебника [Приступая к работе с мобильными службами], чтобы подключить свой проект к мобильной службе. Таким образом, для работы с этим учебником требуется Visual Studio 2013.

## Пример кода
Просмотреть полный исходный код можно [здесь](https://github.com/RickSaling/mobile-services-samples/tree/push/GettingStartedWithPush).

## Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Настройка мобильной службы для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Обновление мобильной службы для отправки push-уведомлений

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Play](../../includes/mobile-services-add-google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

## Тестирование приложения с помощью опубликованной мобильной службы

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

### Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Вы успешно завершили ознакомление с данным учебником.

## Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Android возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Отправка push-уведомлений прошедшим проверку пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку.

+ [Рассылка широковещательных уведомлений подписчикам] <br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

+ [Отправка подписчикам уведомлений на основе шаблонов] <br/>Узнайте о том, как использовать шаблоны для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Что такое Центры уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений Центров уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Устранение неполадок и отладка решений на основе Центров уведомлений.

* [Использование библиотеки клиента Android для мобильных служб] <br/>Дополнительные сведения об использовании мобильных служб с Android.

<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-ec.md
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-android-get-started.md
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Использование библиотеки клиента Android для мобильных служб]: mobile-services-android-how-to-use-client-library.md

[Что такое Центры уведомлений?]: ../notification-hubs-overview.md
[Рассылка широковещательных уведомлений подписчикам]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Отправка подписчикам уведомлений на основе шаблонов]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO2-->