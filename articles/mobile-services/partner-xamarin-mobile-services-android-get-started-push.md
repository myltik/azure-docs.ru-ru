<properties 
	pageTitle="Добавление push-уведомлений в приложение Xamarin Android|мобильные службы Android" 
	description="Вы узнаете, как настраивать push-уведомления в Google Cloud Messaging для ваших приложений Xamarin.Android с помощью мобильных служб Azure и концентраторов уведомлений Azure." 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="glenga"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Обзор
В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Xamarin.Android. В этом учебнике вам предстоит добавить push-уведомления в проект [Приступая к работе с мобильными службами] с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

Для работы с данным учебником требуется следующее:

+ Активная учетная запись Google.
+ [Компонент клиента Google Cloud Messaging]. Этот компонент будет добавлен при прохождении учебника.

В вашем проекте уже должны быть компоненты [Xamarin.Android] и [мобильные службы Azure], которые вы установили после прохождения учебника [Приступая к работе с мобильными службами] или [Добавление мобильных служб в существующее приложение].

##<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Настройка мобильной службы для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Обновление зарегистрированного скрипта вставки для отправки уведомлений

>[AZURE.TIP]Ниже показано, как обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem на портале управления Azure. Вы также можете вызвать и изменить этот скрипт мобильной службы непосредственно в Visual Studio на узле Azure в обозревателе сервера.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>Настройка существующего проекта для push-уведомлений

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Добавление кода push-уведомлений в приложение

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Вы успешно завершили ознакомление с данным учебником.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Добавление мобильных служб в существующее приложение] <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности](mobile-services-android-get-started-users.md) <br/>Узнайте, как проверить подлинность пользователей приложения с разными типами учетных записей при помощи мобильных служб.

* [Что такое концентраторы уведомлений?](../notification-hubs-overview.md) <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений.

* [Использование клиентской библиотеки .NET для мобильных служб](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>Узнайте, как использовать мобильные службы с кодом Xamarin C#.

* [Справочник серверных скриптов мобильных служб](mobile-services-how-to-use-server-scripts.md) <br/>Узнайте, как реализовать бизнес-логику в мобильной службе.

<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-ios-get-started.md
[Добавление мобильных служб в существующее приложение]: mobile-services-android-get-started-data.md

[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[мобильные службы Azure]: http://components.xamarin.com/view/azure-mobile-services/
 

<!---HONumber=August15_HO6-->