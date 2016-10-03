<properties
	pageTitle="Работа с несколькими клиентами на одном внутреннем сервере мобильной службы | Мобильные службы Azure"
	description="Узнайте, как использовать серверную часть одной мобильной службы из нескольких клиентских приложений, предназначенных для различных мобильных платформ."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# Поддержка нескольких платформ устройств с помощью одной мобильной службы

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Одно из основных преимуществ мобильных служб Microsoft Azure при разработке мобильных приложений — возможность использовать единую серверную службу, в которой обеспечивается поддержка приложений на нескольких клиентских платформах. В эти мобильные службы встроены клиентские библиотеки всех основных платформ устройств, что упрощает разработку приложений за счет использования единой серверной службы и кроссплатформенных средств разработчика. В этом разделе рассматриваются вопросы выполнения приложения на нескольких клиентских платформах с помощью одной серверной части мобильной службы.

##<a id="push"></a>Кроссплатформенные push-уведомления

Для отправки push-уведомлений клиентским приложениям на всех основных платформах устройств в мобильных службах используются центры уведомлений Azure. В них реализована единая согласованная инфраструктура регистрации устройств, управления этой регистрацией и отправки кроссплатформенных push-уведомлений. Отправка push-уведомлений осуществляется в центрах уведомлений с помощью следующих специализированных платформенных служб уведомлений:

+ Службы push-уведомлений Apple (APNS) для приложений iOS
+ Служба отправки облачных сообщений Google (GCM) для приложений Android
+ служба уведомлений Windows (WNS) для приложений Магазина Windows, приложений Магазина Windows Phone 8.1 и универсальных приложений Windows;
+ Служба push-уведомлений Microsoft (MPNS) для приложений Windows Phone Silverlight

Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure].

Регистрации клиентов создаются функцией регистрации в специализированной платформенной клиентской библиотеке мобильных служб или с помощью программных интерфейсов API REST для мобильных служб. В центрах уведомлений поддерживается регистрация устройств двух видов.

+ **Собственная регистрация**<br/>Каждая собственная регистрация соответствует определенной службе уведомлений, специализированной для той или иной платформы. При отправке уведомлений на устройства, зарегистрированные с помощью собственной регистрации, необходимо вызвать на мобильном устройстве специализированный программный интерфейс соответствующей платформы, а при отправке на устройства на нескольких платформах — несколько специализированных программных интерфейсов.

+ **Регистрация шаблона**<br/>В центрах уведомлений также поддерживается специализированная платформенная регистрация шаблонов. При регистрации этого типа можно отправить уведомление в приложение, которое работает на любой зарегистрированной платформе, вызвав один программный интерфейс. Дополнительные сведения см. в разделе [Отправка пользователям уведомлений между различными платформами].

Таблицы в следующих разделах привязаны к учебникам, которые относятся к конкретным клиентам, и демонстрируют отправку push-уведомлений из мобильных служб внутренних серверов, как .NET, так и JavaScript.

###Серверная служба .NET

В серверной мобильной службе .NET для отправки уведомлений нужно вызвать метод [SendAsync] объекта [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx), полученного из свойства [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). Вид отправленного push-уведомления (собственное или шаблонное) зависит от того, какой объект, производный от [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx), передается в метод [SendAsync] \(см. таблицу ниже).

|платформа |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) | MPNS
|-----|-----|----|----|-----|
|Собственное|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

В приведенном ниже коде отправляется push-уведомление из серверной службы .NET на все зарегистрированные устройства iOS и Магазина Windows:

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

Примеры отправки push-уведомлений на другие собственные клиентские платформы приведены по ссылкам в заголовке следующей таблицы.

Если вместо собственной регистрации клиента используется регистрация шаблона, такое же уведомление можно отправить одним вызовом метода [SendAsync], указав в качестве аргумента объект [TemplatePushMessage]\:

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###Серверная часть JavaScript

В мобильной серверной службе JavaScript для отправки уведомления нужно вызвать метод **send** специализированного объекта платформы, полученного из глобального [объекта push] \(см. таблицу ниже).

|платформа |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|Собственное|[Объект apns](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[Объект gcm](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[Объект wns](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [Объект mpns](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

Следующий код обеспечивает отправку push-уведомления на все регистрации Android и Windows Phone:

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

Примеры отправки push-уведомлений на другие собственные клиентские платформы приведены по ссылкам в заголовке следующей таблицы.

Если вместо собственной регистрации клиента используется регистрация шаблона, такое же уведомление можно отправить одним вызовом функции **send** глобального [объекта push], указав в качестве аргумента содержательную часть сообщения:

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>Разработка кроссплатформенных приложений
Чтобы разрабатывать собственные приложения для всех основных платформ мобильных устройств, необходим опыт работы хотя бы с одним из следующих языков программирования: Objective-C, Java, C# или JavaScript. Из-за разнообразия платформ некоторые разработчики предпочитают создавать полностью браузерные приложения, Однако такие веб-приложения не могут получить доступ к большинству исходных источников, предоставляющих более богатые возможности, чем пользователи ожидают получить при работе с мобильными устройствами.

В распоряжении разработчиков имеются кроссплатформенные инструменты, которые позволяют гораздо эффективнее использовать ресурсы любого мобильного устройства на основе одного и того же кода (обычно — JavaScript). Мобильные службы упрощают создание серверных служб для платформ разработки кроссплатформенных приложений и управление этими службами. Это возможно благодаря кратким учебникам для следующих платформ разработки:

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (распространение проекта Apache Cordova) — бесплатная платформа с открытым исходным кодом. Позволяет использовать стандартизированные веб-API, HTML и JavaScript для разработки единого приложения, которое будет работать на устройствах Android, iOS и Windows. PhoneGap обладает пользовательским интерфейсом, основанным на веб-представлениях. Взаимодействие с пользователем в этой платформе улучшается за счет ускорения собственных ресурсов устройства, таких как push-уведомления, датчик ускорения, камера, память, функция определения географического положения и встроенный в приложение браузер. Дополнительную информацию см. в [кратком учебнике по платформе PhoneGap][PhoneGap].

	Теперь Visual Studio также позволяет строить кросс-платформенные приложения Cordova, используя расширение гибридных приложений для нескольких устройств для Visual Studio, что является предварительной версией ПО. Дополнительную информацию см. в статье [Getting Started with Multi-Device Hybrid Apps Using HTML and JavaScript](http://msdn.microsoft.com/library/dn771545.aspx) (Приступая к работе с расширением Multi-Device Hybrid Apps с помощью HTML и JavaScript).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>В платформе Sencha Touch разработчику предоставляется набор элементов управления, оптимизированных для сенсорных экранов. Это позволяет писать код приложений на HTML и JavaScript для различных устройств, который по эффективности был бы близок к машинному. Sencha Touch можно использовать совместно с библиотеками PhoneGap или Cordova, чтобы предоставлять пользователям доступ к собственным ресурсам устройств. Дополнительную информацию см. в [кратком учебнике по платформе Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Платформа Xamarin позволяет создавать максимально эффективные приложения для устройств iOS и Android. В этих приложениях можно использовать все собственные элементы пользовательского интерфейса и обеспечить доступ ко всем ресурсам устройств. В Xamarin код приложений пишется на языке C#, а не на Objective-C и Java. Это позволяет разработчикам на платформе .NET публиковать приложения для iOS и Android и совместно использовать код проектов Windows. Благодаря применению кода C# в платформе Xamarin обеспечивается максимально эффективное взаимодействие с пользователем на устройствах iOS и Android. Это позволяет повторно использовать на этих устройствах часть кода мобильных служб для приложений Windows. Дополнительную информацию см. в разделе [Разработка в Xamarin](#xamarin) ниже.


<!-- URLs -->
[Концентраторы уведомлений Azure]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Отправка пользователям уведомлений между различными платформами]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[объекта push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=AcomDC_0727_2016-->