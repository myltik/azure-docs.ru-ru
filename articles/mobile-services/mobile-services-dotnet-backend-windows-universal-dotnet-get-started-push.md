<properties 
	pageTitle="Приступая к работе с push-уведомление с помощью серверной мобильной службы .NET" 
	description="Узнайте, как использовать мобильные службы и центры уведомлений Azure для отправки push-уведомлений в универсальное приложение для Windows." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]
#Обзор
В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[AZURE.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для отправки push-уведомлений из мобильных служб в универсальное приложение Windows. Эти же инструкции можно использовать для добавления push-уведомлений из мобильных служб в приложение Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как добавить push-уведомления в приложение Windows Phone 8 или Windows Phone Silverlight 8.1, см. в этой версии раздела [Приступая к работе с push-уведомлениями в мобильных службах](mobile-services-dotnet-backend-windows-phone-get-started-push.md).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для работы с push-уведомлениями](#register)
2. [Обновление службы для отправки push-уведомлений](#update-service)
3. [Включение push-уведомлений для локального тестирования](#local-testing)
4. [Тестирование push-уведомлений в приложении](#test)

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>. 

##<a id="register"></a>Регистрация приложения для получения push-уведомлений

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Перейдите в папку проекта <code>\Services\MobileServices\имя_вашей_службы</code>, откройте созданный файл кода push.register.cs и просмотрите метод <strong>UploadChannel</strong>, который регистрирует URL-адрес канала устройства в концентраторе уведомлений.</p></li> 
<li><p>Откройте общий файл кода App.xaml.cs и обратите внимание на то, что в обработчик событий <strong>OnLaunched</strong> добавлен вызов нового метода <strong>UploadChannel</strong>.</p> <p>Это гарантирует, что при каждом запуске приложения будет выполняться попытка регистрации устройства.</p></li>
<li><p>Повторите предыдущие шаги, чтобы добавить push-уведомления к проекту приложения Магазина Windows Phone, затем в общем файле App.xaml.cs удалите дополнительный вызов к каналу <strong>UploadChannel</strong> и оставшуюся условную оболочку <code>#if...#endif</code>.</p> <p>Теперь оба проекта могут использовать один вызов метода <strong>UploadChannel</strong>.</p>
</li>
</ol>

> [AZURE.NOTE]Вы также можете упростить созданный код, объединив оболочку <code>#if...#endif</code> для определений [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) в одно определение без оболочки, которое может использоваться обеими версиями приложения.

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления.

##<a id="update-service"></a>Обновление службы для отправки push-уведомлений

Описанные ниже шаги позволяют обновить существующий класс TodoItemController для отправки push-уведомления на все зарегистрированные устройства при вставке нового элемента. Подобный код можно реализовать в любом пользовательском классе: [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx) и где-либо еще в серверных службах.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Остальные шаги, описанные в этом разделе, необязательны. С их помощью можно протестировать приложение с мобильной службой, запущенной на локальном компьютере. Если вы планируете тестировать push-уведомления с помощью мобильной службы, работающей в Azure, то можете перейти к последнему подразделу. Это связано с тем, что мастер добавления push-уведомлений уже настроил подключение приложения к службе, работающей в Azure.

>[AZURE.NOTE]Никогда не используйте рабочую мобильную службу для тестирования и разработки. Для тестирования всегда публикуйте проект мобильной службы в виде отдельной промежуточной службы.

<ol start="5">
<li><p>Откройте общий файл проекта App.xaml.cs и найдите строки кода, в которых создается экземпляр класса <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> для доступа к мобильной службе, работающей в Azure.</p></li>
<li><p>Закомментируйте этот код и добавьте код, создающий экземпляр класса <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> с тем же именем, но с использованием URL-адреса локального узла в конструкторе, как в следующем примере:</p>
<pre><code>// Класс MobileServiceClient настроен на обмен данными с локальным
// тестовым проектом для отладки.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>С помощью этого экземпляра <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> приложение будет подключаться к локальной службе, а не к версии, размещенной в Azure. Если вы захотите снова подключить приложение к мобильной службе, размещенной в Azure, верните исходные определения <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>.</p></li>
</ol>

##<a id="test"></a> Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Отправка push-уведомлений прошедшим проверку пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Добавление мобильных служб в существующее приложение][Get started with data] <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Добавление проверки подлинности в приложение][Get started with authentication] <br/>Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей при помощи мобильных служб.

* [Что такое концентраторы уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

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

[Что такое концентраторы уведомлений?]: ../notification-hubs-overview.md

[Использование клиента .NET для мобильных служб Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md

<!--HONumber=54--> 