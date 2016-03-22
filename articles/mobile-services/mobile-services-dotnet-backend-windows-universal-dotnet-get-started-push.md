<properties
	pageTitle="Добавление push-уведомлений в универсальное приложение для Windows 8.1 | Microsoft Azure"
	description="Информация об отправлении push-уведомлений в универсальное приложение для Windows 8.1 из мобильной службы с серверной частью .NET с помощью Центров уведомлений Azure."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="glenga"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Аналогичные сведения для мобильных приложений см. в статье [Добавление push-уведомлений в приложение Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

##Обзор
В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью центров уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами центр уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934).

##<a id="register"></a>Регистрация приложения для получения push-уведомлений

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

& nbsp; & nbsp; 6. Перейдите в папку проекта `\Services\MobileServices\your_service_name`, откройте созданный файл кода push.register.cs и просмотрите метод **UploadChannel**, который регистрирует URL-адрес канала устройства в концентраторе уведомлений.

&nbsp;&nbsp;7. Откройте общий файл кода App.xaml.cs и обратите внимание на то, что в обработчик событий **OnLaunched** добавлен вызов нового метода **UploadChannel**. Это гарантирует, что при каждом запуске приложения будет выполняться попытка регистрации устройства.

&nbsp;&nbsp;8. Повторите предыдущие шаги, чтобы добавить push-уведомления в проект приложения Магазина Windows Phone, после чего в общем файле App.xaml.cs удалите дополнительный вызов метода **UploadChannel** и оставшуюся оболочку условия `#if...#endif`. Теперь оба проекта могут использовать один вызов метода **UploadChannel**.

> [AZURE.NOTE] Вы также можете упростить созданный код, объединив определения [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) в оболочке `#if...#endif` в одно определение без оболочки, которое может использоваться обеими версиями приложения.

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления.

##<a id="update-service"></a>Обновление службы для отправки push-уведомлений

Описанные ниже шаги позволяют обновить существующий класс TodoItemController для отправки push-уведомления на все зарегистрированные устройства при вставке нового элемента. Подобный код можно реализовать в любом пользовательском классе: [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) и где-либо еще в серверных службах.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Остальные шаги, описанные в этом разделе, необязательны. С их помощью можно протестировать приложение с мобильной службой, запущенной на локальном компьютере. Если вы планируете тестировать push-уведомления с помощью мобильной службы, работающей в Azure, то можете перейти к последнему подразделу. Это связано с тем, что мастер добавления push-уведомлений уже настроил подключение приложения к службе, работающей в Azure.

>[AZURE.NOTE]Никогда не используйте рабочую мобильную службу для тестирования и разработки. Для тестирования всегда публикуйте проект мобильной службы в виде отдельной промежуточной службы.

&nbsp;&nbsp;5. Откройте общий файл проекта App.xaml.cs и найдите строки кода, в которых создается экземпляр класса [MobileServiceClient] для доступа к мобильной службе, работающей в Azure.

& nbsp; & nbsp; 6. Закомментируйте этот код и добавьте код, создающий экземпляр класса [MobileServiceClient] с тем же именем, но с использованием URL-адреса локального узла в конструкторе, как в следующем примере:

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;С помощью этого экземпляра [MobileServiceClient] приложение будет подключаться к локальной службе, а не к версии, размещенной в Azure. Если вы захотите снова подключить приложение к мобильной службе, размещенной в Azure, верните исходные определения [MobileServiceClient].

##<a id="test"></a> Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и центров уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Отправка push-уведомлений прошедшим проверку пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку.

Дополнительные сведения о мобильных службах и центрах уведомлений см. в следующих разделах.

* [Добавление мобильных служб в существующее приложение][Get started with data] <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Добавление проверки подлинности в приложение][Get started with authentication] <br/>Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей при помощи мобильных служб.

* [Что такое центры уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений.

* [Использование клиента .NET для мобильных служб Azure] <br/>Дополнительные сведения об использовании мобильных служб из приложений Windows на C#.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Отправка push-уведомлений прошедшим проверку пользователям]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Что такое центры уведомлений?]: ../notification-hubs-overview.md

[Использование клиента .NET для мобильных служб Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=AcomDC_0309_2016-->