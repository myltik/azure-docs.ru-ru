<properties
	pageTitle="Приступая к работе с мобильными службами в приложениях Xamarin для Android | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы и центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin для Android."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="glenga"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Добавление push-уведомлений в приложение Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md).

##Обзор

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Xamarin.Android. В этом учебнике вам предстоит добавить push-уведомления в проект [Приступая к работе с мобильными службами] с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

Для работы с данным учебником требуется следующее:

+ Активная учетная запись Google.
+ [Компонент клиента Google Cloud Messaging]. Этот компонент будет добавлен при прохождении учебника.

У вас в проекте уже должны быть установлены компоненты [Xamarin.Android] и [мобильные службы Azure][Azure Mobile Services Component] после завершения учебника [Приступая к работе с мобильными службами].

##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [Включение GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Настройка мобильной службы для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Обновление мобильной службы для отправки push-уведомлений

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Настройка существующего проекта для push-уведомлений

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Добавление кода push-уведомлений в приложение

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Тестирование приложения с помощью опубликованной мобильной службы

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###<a id="local-testing"></a>Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0323_2016-->