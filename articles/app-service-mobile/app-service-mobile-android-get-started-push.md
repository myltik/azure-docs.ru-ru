<properties
	pageTitle="Добавление push-уведомлений в приложение Android с помощью мобильных приложений Azure"
	description="Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение Android."
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="yuaxu"/>

# Добавление push-уведомлений в приложение Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Обзор
В этом учебнике описывается добавление push-уведомлений в [ознакомительный проект для платформы Android], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал [ознакомительного проекта для платформы Android], инструкции из которого необходимо выполнить в первую очередь. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

Для работы с этим учебником требуется:

* [Учетная запись Google](http://go.microsoft.com/fwlink/p/?LinkId=268302) с подтвержденным адресом электронной почты.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) не требуется для серверного проекта Node.js.
* Изучение [краткого руководства](app-service-mobile-android-get-started.md).

##<a name="create-hub"></a>Создание центра уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Настройка серверной части мобильного приложения для отправки push-запросов

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Добавление push-уведомлений в приложение

Необходимо убедиться в том, что проект приложения Android готов для обработки push-уведомлений.

###Проверка версии Android SDK

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Добавление служб Play](../../includes/app-service-mobile-add-google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Тестирование приложения с помощью опубликованной мобильной службы

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

##<a id="more"></a>Дополнительные сведения

* Теги позволяют отправлять push-уведомления клиентам в зависимости от их сегмента. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) показано, как добавлять теги для установки устройства.

<!-- URLs -->
[ознакомительного проекта для платформы Android]: app-service-mobile-android-get-started.md
[ознакомительный проект для платформы Android]: app-service-mobile-android-get-started.md

[Настройка пакета SDK служб Google Play]: https://developers.google.com/android/guides/setup

<!---HONumber=AcomDC_0211_2016-->