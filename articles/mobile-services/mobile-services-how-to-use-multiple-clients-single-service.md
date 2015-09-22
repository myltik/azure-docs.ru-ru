<properties
	pageTitle="Работа с несколькими клиентами на одном внутреннем сервере мобильной службы | Microsoft Azure"
	description="Узнайте, как использовать серверную часть одной мобильной службы из нескольких клиентских приложений, предназначенных для различных мобильных платформ, включая Магазин Windows и Windows Phone."
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
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Поддержка нескольких платформ устройств с помощью одной мобильной службы

Одно из основных преимуществ мобильных служб Microsoft Azure при разработке мобильных приложений — возможность использовать единую серверную службу, в которой обеспечивается поддержка приложений на нескольких клиентских платформах. В эти мобильные службы встроены клиентские библиотеки всех основных платформ устройств, что упрощает разработку приложений за счет использования единой серверной службы и кроссплатформенных средств разработчика. В этом разделе рассматриваются вопросы выполнения приложения на нескольких клиентских платформах с помощью одной серверной части мобильной службы.

##<a id="push"></a>Кроссплатформенные push-уведомления

Для отправки push-уведомлений клиентским приложениям на всех основных платформах устройств в мобильных службах используются центры уведомлений Azure. В них реализована единая согласованная инфраструктура регистрации устройств, управления этой регистрацией и отправки кроссплатформенных push-уведомлений. Отправка push-уведомлений осуществляется в центрах уведомлений с помощью следующих специализированных платформенных служб уведомлений:

+ Службы push-уведомлений Apple (APNS) для приложений iOS
+ Служба отправки облачных сообщений Google (GCM) для приложений Android
+ служба уведомлений Windows (WNS) для приложений Магазина Windows, приложений Магазина Windows Phone 8.1 и универсальных приложений Windows;
+ Служба push-уведомлений Microsoft (MPNS) для приложений Windows Phone Silverlight

>[AZURE.NOTE]Концентраторы уведомлений на данный момент не поддерживают использование WNS для отправки push-уведомлений в приложения Windows Phone Silverlight 8.1. Для отправки уведомлений в приложения Silverlight и Windows Phone 8.0 и 7.0 необходимо использовать службу MPNS.

Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure].

Регистрации клиентов создаются функцией регистрации в специализированной платформенной клиентской библиотеке мобильных служб или с помощью программных интерфейсов API REST для мобильных служб. В центрах уведомлений поддерживается регистрация устройств двух видов.

+ **Собственная регистрация**<br/>Каждая собственная регистрация соответствует определенной службе уведомлений, специализированной для той или иной платформы. При отправке уведомлений на устройства, зарегистрированные с помощью собственной регистрации, необходимо вызвать на мобильном устройстве специализированный программный интерфейс соответствующей платформы, а при отправке на устройства на нескольких платформах — несколько специализированных программных интерфейсов.

+ **Регистрация шаблона**<br/>В центрах уведомлений также поддерживается специализированная платформенная регистрация шаблонов. При регистрации этого типа можно отправить уведомление в приложение, которое работает на любой зарегистрированной платформе, вызвав один программный интерфейс. Дополнительные сведения см. в разделе [Отправка пользователям уведомлений между различными платформами].

>[AZURE.NOTE]Ошибка возникает при попытке отправить сообщения на исходную платформу устройства, для которой не существует регистрации устройства. При отправке шаблонных уведомлений подобная ошибка не возникает.

Таблицы в следующих разделах привязаны к учебникам, которые относятся к конкретным клиентам, и демонстрируют отправку push-уведомлений из мобильных служб внутренних серверов, как .NET, так и JavaScript.

###Серверная служба .NET

В серверной мобильной службе .NET для отправки уведомлений нужно вызвать метод [SendAsync] объекта [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx), полученного из свойства [ApiServices.Push](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx). Вид отправленного push-уведомления (собственное или шаблонное) зависит от того, какой объект, производный от [IPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx), передается в метод [SendAsync] (см. таблицу ниже).

|платформа |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
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

В мобильной серверной службе JavaScript для отправки уведомления нужно вызвать метод **send** специализированного объекта платформы, полученного из глобального [объекта push] (см. таблицу ниже).

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

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator позволяет разработать на JavaScript единое приложение, которое после компиляции будет работать на всех платформах мобильных устройств как собственное. Это дает возможность обеспечить эффективное взаимодействие с пользователем через пользовательский интерфейс, получить доступ ко всем ресурсам устройства и создать высокопроизводительное приложение. Дополнительную информацию см. в [учебнике по платформе Appcelerator][Appcelerator].

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707) **или** [**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (распространение проекта Apache Cordova) — бесплатная платформа с открытым исходным кодом. Позволяет использовать стандартизированные веб-API, HTML и JavaScript для разработки единого приложения, которое будет работать на устройствах под управлением Android, iOS и Windows. PhoneGap обладает пользовательским интерфейсом, основанным на веб-представлениях. Взаимодействие с пользователем в этой платформе улучшается за счет ускорения собственных ресурсов устройства, таких как push-уведомления, датчик ускорения, камера, память, функция определения географического положения и встроенный в приложение браузер. Дополнительную информацию см. в [кратком учебнике по платформе PhoneGap][PhoneGap].

	Теперь Visual Studio также позволяет строить кросс-платформенные приложения Cordova, используя расширение гибридных приложений для нескольких устройств для Visual Studio, что является предварительной версией ПО. Дополнительную информацию см. в статье [Getting Started with Multi-Device Hybrid Apps Using HTML and JavaScript](http://msdn.microsoft.com/library/dn771545.aspx) (Приступая к работе с расширением Multi-Device Hybrid Apps с помощью HTML и JavaScript).

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>В платформе Sencha Touch разработчику предоставляется набор элементов управления, оптимизированных для сенсорных экранов. Это позволяет писать код приложений на HTML и JavaScript для различных устройств, который по эффективности был бы близок к машинному. Sencha Touch можно использовать совместно с библиотеками PhoneGap или Cordova, чтобы предоставлять пользователям доступ к собственным ресурсам устройств. Дополнительную информацию см. в [кратком учебнике по платформе Sencha Touch][Sencha].

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Платформа Xamarin позволяет создавать максимально эффективные приложения для устройств iOS и Android. В этих приложениях можно использовать все собственные элементы пользовательского интерфейса и обеспечить доступ ко всем ресурсам устройств. В Xamarin код приложений пишется на языке C#, а не на Objective-C и Java. Это позволяет разработчикам на платформе .NET публиковать приложения для iOS и Android и совместно использовать код проектов Windows. Благодаря применению кода C# в платформе Xamarin обеспечивается максимально эффективное взаимодействие с пользователем на устройствах iOS и Android. Это позволяет повторно использовать на этих устройствах часть кода мобильных служб для приложений Windows. Дополнительную информацию см. в разделе [Разработка в Xamarin](#xamarin) ниже.

	Вы можете строить приложения Xamarin с помощью Xamarin Studio или Visual Studio 2013. Дополнительную информацию см. в разделе [Кроссплатформенная разработка в Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).


##<a id="shared-vs"></a>Совместное и повторное использование кода в проектах Visual Studio

В состав мобильных служб входит клиентская библиотека .NET, переносимая библиотека классов (PCL) .NET Framework, в которой поддерживается разработка на всех платформах Windows. Дополнительную информацию см. в разделе [Как использовать клиент .NET с мобильными службами]. Это упрощает повторное использование в различных проектах C# кода мобильных служб, предназначенного, например, для доступа к данным или аутентификации.

Одним из общих подходов к совместному и повторному использованию кода C# в нескольких проектах является реализация шаблона «модель-представление-представление модели» (MVVM) и совместное использование сборок на нескольких платформах. Можно реализовать классы модели и представления модели в проекте переносимой библиотеки классов в Visual Studio, а затем создать представления, настроенные для различных платформ. Код модели, общий для платформ, может (например) извлекать данные из источника, такого как мобильная служба, безотносительно к платформе. В библиотеке MSDN можно найти <a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">описание и пример</a>, обсуждение <a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">отличий API</a>, пример <a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">использования библиотек переносимого класса для реализации шаблона MVVM</a>, дополнительные <a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">инструкции</a> и сведения об <a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">управлении ресурсами</a> в проектах библиотеки переносимого класса.

В дополнение к этой общей инструкции Visual Studio предоставляет особые средства для повторного использования кода мобильных служб в нескольких проектах клиентских приложений. Этот вопрос обсуждается в следующих разделах. Общую информацию о выполнении сборки кроссплатформенных приложений в Visual Studio 2013 см. в статье [Cross-Platform Development in Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx) (Кроссплатформенная разработка в Visual Studio).

### Универсальные приложения Windows

В обновлении 2 для Visual Studio 2013 добавлена поддержка проектов универсальных приложений Windows. Универсальные приложения — это решения, которые включают проекты приложений Магазина Windows 8.1 и Магазина Windows Phone 8.1 в проект с общим кодом. В таком проекте общий код рассматривается как часть проекта Магазина Windows и проекта Windows Phone. Дополнительную информацию см. в статье [Разработка универсальных приложений для Windows для всех устройств с Windows]. Универсальные приложения Windows можно писать на C#/XAML и JavaScript/HTML.

По умолчанию на вкладке быстрого запуска мобильных служб на [портале управления Azure] в изначально создается версия универсального приложения Windows примера приложения TodoList. На выбор можно скачать версию C#/XAML или JavaScript/HTML проекта. Дополнительную информацию см. в разделе [Приступая к работе с мобильными службами](../mobile-services-windows-store-get-started.md).

>[AZURE.NOTE]Версия проекта приложения быстрого запуска, написанного на C#, с портала, использует общую страницу с фоновым кодом MainPage.xaml.cs, но не модель представления. Пример того, как на платформе TodoList создать проект универсального приложения Windows на языке C#, используя MVVM, см. в статье [Universal Windows app project for Azure Mobile Services using MVVM] (Проект универсального приложения Windows для мобильных служб Azure с использованием MVVM).

###<a id="xamarin"></a>Разработка в Xamarin

Свой опыт разработки в Visual Studio и C# можно применить для разработки приложений для iOS и Android с помощью Xamarin и Visual Studio или Xamarin Studio. В Xamarin используется кроссплатформенная реализация платформы .NET Framework, что позволяет применять код C# для разработки приложений для iOS и Android. В Xamarin можно использовать существующий код проектов Windows, в котором доступ к мобильным службам осуществляется с помощью клиентской библиотеки .NET для мобильных служб. Дополнительную информацию см. в разделе [Кроссплатформенная разработка в Visual Studio](http://msdn.microsoft.com/library/dn771552.aspx).

Описание начальных этапов создания приложений Xamarin, в которых используются мобильные службы, см. в кратких учебниках по Xamarin (для [iOS](mobile-services-ios-get-started.md) или [Android](mobile-services-android-get-started.md)).


### Приложения для Магазина Windows и Windows Phone Silverlight

В Windows Phone 8.1 для разработки приложений можно использовать ранее созданный код XAML на основе Silverlight. Можно также использовать код XAML на основе среды выполнения Windows, что позволяет разрабатывать универсальные приложения Windows. Дополнительную информацию о приложениях для Windows Phone 8.1 Silverlight и Магазина Windows Phone 8.1 см. в статье [What's next for Windows Phone 8 developers] (Новинки для разработчиков приложений для Windows Phone 8).

Клиентская библиотека .NET для мобильных служб поддерживает и приложения для Магазина Windows Phone 8.1, и приложения для Windows Phone Silverlight 8.1. Поскольку приложения среды выполнения Windows и Windows Phone Silverlight нельзя создавать в рамках одного и того же проекта, в качестве стратегии повторного использования кода следует рассмотреть использование PCL и MVVM, как описано выше.

>[AZURE.NOTE]Чтобы использовать единую проверку подлинности при входе для клиентов с учетной записью Microsoft, на приложениях Windows Runtime и Windows Phone Silverlight, необходимо предварительно зарегистрировать ваше приложение Windows Runtime на панели мониторинга Магазина Windows. Это связано с тем, что после создания регистрации Live Connect для Windows Phone невозможно создать регистрацию для Магазина Windows. Дополнительную информацию о том, как это сделать, см. в разделе **Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect** ([Магазин Windows][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).


<!-- URLs -->
[портале управления Azure]: https://manage.windowsazure.com
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
[Как использовать клиент .NET с мобильными службами]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[объекта push]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[Разработка универсальных приложений для Windows для всех устройств с Windows]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!----HONumber=September15_HO1-->