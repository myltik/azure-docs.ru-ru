<properties 
	pageTitle="Начало работы с push-уведомлением с помощью серверной мобильной службы JavaScript" 
	description="Узнайте, как использовать мобильные службы и центры уведомлений Azure для отправки push-уведомлений в универсальное приложение для Windows." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>


# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией JavaScript для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части JavaScript во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[AZURE.NOTE]В этом разделе показано, как использовать средства Visual Studio 2013 с обновлением 3 для отправки push-уведомлений из мобильных служб в универсальное приложение Windows. Эти же инструкции можно использовать для добавления push-уведомлений из мобильных служб в приложение Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как добавить push-уведомления в приложение Windows Phone 8 или Windows Phone Silverlight 8.1, см. в этой версии раздела [Приступая к работе с push-уведомлениями в мобильных службах](mobile-services-javascript-backend-windows-phone-get-started-push.md).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений](#register)
2. [Обновление службы для отправки push-уведомлений](#update-service)
3. [Тестирование push-уведомлений в приложении](#test)

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* [Visual Studio Express 2013 для Windows](http://go.microsoft.com/fwlink/?LinkId=257546) с обновлением 3 или более поздней версии.

##<a id="register"></a>Регистрация приложения для получения push-уведомлений

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Перейдите в папку проекта <code>\Services\MobileServices\имя_вашей_службы</code>, откройте созданный файл кода push.register.cs и просмотрите метод <strong>UploadChannel</strong>, который регистрирует URL-адрес канала устройства в концентраторе уведомлений.</p></li> 
<li><p>Откройте общий файл кода App.xaml.cs и обратите внимание на то, что в обработчик событий <strong>OnLaunched</strong> добавлен вызов нового метода <strong>UploadChannel</strong>.</p> <p>Это гарантирует, что при каждом запуске приложения будет выполняться попытка регистрации устройства.</p></li>
<li><p>Повторите предыдущие шаги, чтобы добавить push-уведомления в проект приложения Магазина Windows Phone, а затем в общем файле App.xaml.cs удалите дополнительный вызов метода <strong>UploadChannel</strong> и оставшуюся условную оболочку <code>#if...#endif</code>.</p> <p>Теперь оба проекта могут использовать один вызов метода <strong>UploadChannel</strong>.</p>
<p>Вы также можете упростить созданный код, объединив определения <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> в оболочке <code>#if...#endif</code> в одно определение без оболочки, которое может использоваться обеими версиями приложения.</p></li>
</ol>

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления.

##<a id="update-service"></a>Обновление службы для отправки push-уведомлений

Ниже показано, как обновить скрипт вставки, зарегистрированный в таблице TodoItem. Подобный код можно реализовать в любом серверном скрипте или в другом месте в серверных службах.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Отправка push-уведомлений пользователям, прошедшим проверку подлинности](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, которые прошли проверку подлинности.

+ [Рассылка широковещательных уведомлений подписчикам](notification-hubs/notification-hubs-windows-store-dotnet-send-breaking-news.md) <br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

+ [Отправка подписчикам уведомлений, независимых от платформы ](notification-hubs/notification-hubs-aspnet-cross-platform-notify-users.md) <br/>Узнайте, как использовать шаблоны для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Концентраторы уведомлений Azure — рекомендации по диагностике](notification-hubs/notification-hubs-diagnosing.md) <br/>Узнайте, как устранять неполадки, связанные с push-уведомлениями.

* [Приступая к работе с проверкой подлинности] <br/>Узнайте, как проверять подлинность пользователей приложения с разными типами учетных записей, используя мобильные службы.

* [Что такое концентраторы уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Использование клиента .NET для мобильных служб Azure] <br/>Узнайте, как использовать мобильные службы из приложений для Windows, созданных на языке C#.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[Что такое концентраторы уведомлений?]: notification-hubs/notification-hubs-overview.md

[Использование клиента .NET для мобильных служб Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54-->