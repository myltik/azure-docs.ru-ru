<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />


# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio Professional 2013 с обновлением 3 для отправки push-уведомлений из мобильных служб в универсальное приложение Windows. Эти же инструкции можно использовать для добавления push-уведомлений из мобильных служб в приложение Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как добавить push-уведомления в приложение Windows Phone 8 или Windows Phone Silverlight 8.1, см. в этой версии раздела [Приступая к работе с push-уведомлениями в мобильных службах][Приступая к работе с push-уведомлениями в мобильных службах].

> Если вы не можете выполнить обновление до Visual Studio Professional 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение Магазина Windows, см. [эту версию][эту версию] раздела.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений][Регистрация приложения для получения push-уведомлений]
2. [Обновление службы для отправки push-уведомлений][Обновление службы для отправки push-уведомлений]
3. [Включение push-уведомлений для локального тестирования][Включение push-уведомлений для локального тестирования]
4. [Тестирование push-уведомлений в приложении][Тестирование push-уведомлений в приложении]

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store][учетная запись Microsoft Store];
* [Visual Studio Professional 2013][Visual Studio Professional 2013] (с обновлением 3 или более поздней версии). <br/>Доступна бесплатная пробная версия.

## <a id="register"></a> Регистрация приложения для получения push-уведомлений

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Перейдите в папку проекта <code>\Services\MobileServices\your_service_name</code>, откройте созданный файл кода push.register.cs и просмотрите метод <strong>UploadChannel</strong>, который регистрирует URL-адрес канала устройства в концентраторе уведомлений.</p></li> 
<li><p>Откройте общий файл кода App.xaml.cs и обратите внимание на то, что в обработчик событий <strong>OnLaunched</strong> добавлен вызов нового метода <strong>UploadChannel</strong>.</p> <p>Это гарантирует, что при каждом запуске приложения будет выполняться попытка регистрации устройства.</p></li>
<li><p>Повторите предыдущие шаги, чтобы добавить push-уведомления в проект приложения Магазина Windows Phone, после чего в общем файле App.xaml.cs удалите дополнительный вызов метода <strong>UploadChannel</strong> и оставшуюся оболочку условия <code>#if...#endif</code>.</p> <p>Теперь оба проекта могут использовать один вызов метода <strong>UploadChannel</strong>.</p>
<div class="dev-callout"><strong>Примечание.</strong> <p>Вы также можете упростить созданный код, объединив определения <a href="http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> в оболочке <code>#if...#endif</code> в одно определение без оболочки, которое может использоваться обеими версиями приложения.</p></div></li>
</ol>

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления. 

## a id="update-service"></a> Обновление службы для отправки push-уведомлений

Описанные ниже шаги позволяют обновить существующий класс TodoItemController для отправки push-уведомления на все зарегистрированные устройства при вставке нового элемента. Подобный код можно реализовать в любом пользовательском классе ApiController, TableController или в другом месте в серверных службах.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Остальные шаги, описанные в этом разделе, необязательны. С их помощью можно протестировать приложение с мобильной службой, запущенной на локальном компьютере. Если вы планируете тестировать push-уведомления с помощью мобильной службы, работающей в Azure, то можете перейти к последнему подразделу. Это связано с тем, что мастер добавления push-уведомлений уже настроил подключение приложения к службе, работающей в Azure.

>[WACOM.NOTE]Никогда не используйте рабочую мобильную службу в целях тестирования и разработки. Для тестирования всегда публикуйте проект мобильной службы в виде отдельной промежуточной службы.

<ol start="5">
<li><p>Откройте общий файл проекта App.xaml.cs и найдите строки кода, в которых создается экземпляр класса [MobileServiceClient][MobileServiceClient] для доступа к мобильной службе, работающей в Azure.</p></li>
<li><p>Закомментируйте этот код и добавьте код, создающий экземпляр класса [MobileServiceClient][MobileServiceClient] с тем же именем, но с использованием URL-адреса локального узла в конструкторе, как в следующем примере:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>С помощью этого экземпляра [MobileServiceClient][MobileServiceClient] приложение будет подключаться к локальной службе, а не к версии, размещенной в Azure. Если вы захотите снова подключить приложение к мобильной службе, размещенной в Azure, верните исходные определения [MobileServiceClient][MobileServiceClient].</p></li>
</ol>

## <a id="test"></a> Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"></a> Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Отправка push-уведомлений прошедшим проверку пользователям][Отправка push-уведомлений прошедшим проверку пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными (][Приступая к работе с данными (]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

* [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Использование клиента .NET для мобильных служб Azure][Использование клиента .NET для мобильных служб Azure]
  <br/>Дополнительные сведения об использовании мобильных служб из приложений для Windows, созданных на языке C#.



 
 


[mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
[Приступая к работе с push-уведомлениями в мобильных службах]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
[эту версию]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Регистрация приложения для получения push-уведомлений]: #register
[Обновление службы для отправки push-уведомлений]: #update-service
[Включение push-уведомлений для локального тестирования]: #local-testing
[Тестирование push-уведомлений в приложении]: #test
[учетная запись Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
[mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
[MobileServiceClient]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
[mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
[mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
[Отправка push-уведомлений прошедшим проверку пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[Приступая к работе с данными (]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users
[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Использование клиента .NET для мобильных служб Azure]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
