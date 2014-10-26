<properties linkid="" urlDisplayName="" pageTitle="How to use multiple clients with a single mobile service backend | Azure Mobile" metaKeywords="mobile services single backend" description="Learn how to use a single mobile service back-end from multiple client apps that target different mobile platforms, including Windows Store and Windows Phone." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" manager="" editor="mollybos" />

# Поддержка нескольких платформ устройств из одной мобильной службы

Одно из основных преимуществ мобильных служб Microsoft Azure при разработке мобильных приложений — возможность использовать единую серверную службу, в которой обеспечивается поддержка приложений на нескольких клиентских платформах. В эти мобильные службы встроены клиентские библиотеки всех основных платформ устройств, что упрощает разработку приложений за счет использования единой серверной службы и кросслатформенных средств разработчика. В этом разделе рассматриваются следующие вопросы выполнения приложения на нескольких клиентских платформах с помощью одной серверной части мобильной службы:

-   [Кроссплатформенные push-уведомления][Кроссплатформенные push-уведомления]
-   [Разработка кроссплатформенных приложений][Разработка кроссплатформенных приложений]
-   [Совместное использование кода в проектах Visual Studio][Совместное использование кода в проектах Visual Studio]

Общую информацию о мобильных службах см. в разделе [Центр разработчика для мобильных служб][Центр разработчика для мобильных служб].

## <span id="push"></span></a>Кроссплатформенные push-уведомления

Для отправки push-уведомлений клиентским приложениям на всех основных платформах устройств в мобильных службах используются центры уведомлений Azure. В них реализована единая согласованная инфраструктура регистрации устройств, управления этой регистрацией и отправки кроссплатформенных push-уведомлений. Отправка push-уведомлений осуществляется в центрах уведомлений с помощью следующих специализированных платформенных служб уведомлений:

-   служба push-уведомлений (APNS) для приложений iOS;
-   служба Google Cloud Messaging (GCM) для приложений Android;
-   служба уведомлений Windows (WNS) для приложений Магазина Windows, приложений Магазина Windows Phone 8.1 и универсальных приложений Windows;
-   служба push-уведомлений Майкрософт (MPNS) для приложений Windows Phone Silverlight.

> [WACOM.NOTE]В настоящее время в центрах уведомлений не поддерживается отправка push-уведомлений в приложения Windows Phone Silverlight 8.1 с помощью службы WNS. Для отправки уведомлений в приложения Silverlight и Windows Phone 8.0 и 7.0 необходимо использовать службу MPNS.

Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure][Концентраторы уведомлений Azure].

Регистрации клиентов создаются функцией регистрации в специализированной платформенной клиентской библиотеке мобильных служб или с помощью программных интерфейсов API REST для мобильных служб. В центрах уведомлений поддерживается регистрация устройств двух видов.

-   **Собственная регистрация**
    Каждая собственная регистрация соответствует определенной службе уведомлений, специализированной для той или иной платформы. При отправке уведомлений на устройства, зарегистрированные с помощью собственной регистрации, необходимо вызвать на мобильном устройстве специализированный программный интерфейс соответствующей платформы, а при отправке на устройства на нескольких платформах — несколько специализированных программных интерфейсов.

-   **Регистрация шаблона**
    В центрах уведомлений также поддерживается специализированная платформенная регистрация шаблонов. При регистрации этого типа можно отправить уведомление в приложение, которое работает на любой зарегистрированной платформе, вызвав один программный интерфейс. Дополнительные сведения см. в разделе [Отправка пользователям уведомлений между различными платформами][Отправка пользователям уведомлений между различными платформами].

> [WACOM.NOTE]Возникает ошибка при попытке отправить сообщение на собственную платформу устройства, для которого отсутствуют регистрации устройств. При отправке шаблонных уведомлений подобная ошибка не возникает.

В следующих разделах приведены таблицы со ссылками на учебники по определенным клиентам. В этих учебниках описано, как реализовать push-уведомления для серверных мобильных служб .NET и JavaScript.

### Серверная служба .NET

В серверной мобильной службе .NET для отправки уведомлений нужно вызвать метод [SendAsync][SendAsync] объекта [PushClient][PushClient], полученного из свойства [ApiServices.Push][ApiServices.Push]. Вид отправленного push-уведомления (собственное или шаблонное) зависит от того, какой объект, производный от [IPushMessage][IPushMessage], передается в метод [SendAsync][SendAsync] (см. таблицу ниже).

| платформа   | [APNS][APNS]             | [GCM][GCM]               | [WNS][WNS]                | [MPNS][MPNS]            |
|-------------|----------------------|-----------------------|------------------------|---------------------|
| Собственное | [ApplePushMessage][ApplePushMessage] | [GooglePushMessage][GooglePushMessage] | [WindowsPushMessage][WindowsPushMessage] | [MpnsPushMessage][MpnsPushMessage] |

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

Если вместо собственной регистрации клиента используется регистрация шаблона, такое же уведомление можно отправить одним вызовом метода [SendAsync][SendAsync], указав в качестве аргумента объект [TemplatePushMessage][TemplatePushMessage]:

    // Create a new template message and add the 'message' parameter.    
    var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);
    
    // Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload); 

### Серверная служба JavaScript

В мобильной серверной службе JavaScript для отправки уведомления нужно вызвать метод **send** специализированного объекта платформы, полученного из глобального [объекта push][объекта push] (см. таблицу ниже).

| платформа   | [APNS][1]       | [GCM][2]       | [WNS][3]       | [MPNS][4]       |
|-------------|-----------------|----------------|----------------|-----------------|
| Собственное | [объект apns][объект apns] | [Объект gcm][Объект gcm] | [Объект wns][Объект wns] | [Объект mpns][Объект mpns] |

Следующий код отправляет push-уведомления на все устройства с регистрацией Android и Windows Phone:

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

Если вместо собственной регистрации клиента используется регистрация шаблона, такое же уведомление можно отправить одним вызовом функции **send** глобального [объекта push][объекта push], указав в качестве аргумента содержательную часть сообщения:

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

## <span id="xplat-app-dev"></span></a>Разработка кроссплатформенных приложений

Чтобы разрабатывать собственные приложения для всех основных платформ мобильных устройств, необходим опыт работы хотя бы с одним из следующих языков программирования: Objective-C, Java, C# или JavaScript. Из-за разнообразия платформ некоторые разработчики предпочитают создавать полностью браузерные приложения, но при таком подходе нет доступа к большинству собственных ресурсов мобильных устройств, и приложение не будет обладать теми богатыми возможностями, какими оно могло бы радовать своих пользователей.

В распоряжении разработчиков имеются кроссплатформенные инструменты, которые позволяют гораздо эффективнее использовать ресурсы любого мобильного устройства на основе одного и того же кода (обычно — JavaScript). Мобильные службы упрощают создание серверных служб для платформ разработки кроссплатформенных приложений и управление этими службами. Это возможно благодаря кратким учебникам для следующих платформ разработки:

-   [**Appcelerator**][**Appcelerator**]
    Appcelerator позволяет разработать на JavaScript единое приложение, которое после компиляции будет работать на всех платформах мобильных устройств как собственное. Это дает возможность обеспечить эффективное взаимодействие с пользователем через пользовательский интерфейс, получить доступ ко всем ресурсам устройства и создать высокопроизводительное приложение. Дополнительную информацию см. в [учебнике по платформе Appcelerator][учебнике по платформе Appcelerator].

-   [**PhoneGap**][**PhoneGap**]**/**[**Cordova**][**Cordova**]
    PhoneGap (распространение проекта Apache Cordova) — бесплатная платформа с открытым исходным кодом. Позволяет использовать стандартизированные веб-API, HTML и JavaScript для разработки единого приложения, которое будет работать на устройствах Android, iOS и Windows. PhoneGap обладает пользовательским интерфейсом, основанным на веб-представлениях. Взаимодействие с пользователем в этой платформе улучшается за счет ускорения собственных ресурсов устройства, таких как push-уведомления, датчик ускорения, камера, память, функция определения географического положения и встроенный в приложение браузер. Дополнительную информацию см. в [кратком учебнике по платформе PhoneGap][кратком учебнике по платформе PhoneGap].

    Текущая версия Visual Studio также позволяет создавать кроссплатформенные приложения Cordova с помощью расширения Multi-Device Hybrid Apps к Visual Studio, которое находится на стадии предварительного выпуска. Дополнительную информацию см. в статье [Getting Started with Multi-Device Hybrid Apps Using HTML and JavaScript][Getting Started with Multi-Device Hybrid Apps Using HTML and JavaScript] (Приступая к работе с расширением Multi-Device Hybrid Apps с помощью HTML и JavaScript).

-   [**Sencha Touch**][**Sencha Touch**]
    В платформе Sencha Touch разработчику предоставляется набор элементов управления, оптимизированных для сенсорных экранов. Это позволяет писать код приложений на HTML и JavaScript для различных устройств, который по эффективности был бы близок к машинному. Sencha Touch можно использовать совместно с библиотеками PhoneGap или Cordova, чтобы предоставлять пользователям доступ к собственным ресурсам устройств. Дополнительную информацию см. в [кратком учебнике по платформе Sencha Touch][кратком учебнике по платформе Sencha Touch].

-   [**Xamarin**][**Xamarin**]
    Платформа Xamarin позволяет создавать максимально эффективные приложения для устройств iOS и Android. В этих приложениях можно использовать все собственные элементы пользовательского интерфейса и обеспечить доступ ко всем ресурсам устройств. В Xamarin код приложений пишется на языке C#, а не на Objective-C и Java. Это позволяет разработчикам на платформе .NET публиковать приложения для iOS и Android и совместно использовать код проектов Windows. Благодаря применению кода C# в платформе Xamarin обеспечивается максимально эффективное взаимодействие с пользователем на устройствах iOS и Android. Это позволяет повторно использовать на этих устройствах часть кода мобильных служб для приложений Windows. Дополнительную информацию см. в разделе [Разработка в Xamarin][Разработка в Xamarin] ниже.

    Сборку приложений Xamarin можно выполнять в Xamarin Studio или Visual Studio 2013. Дополнительную информацию см. в разделе [Кроссплатформенная разработка в Visual Studio][Кроссплатформенная разработка в Visual Studio].

## <span id="shared-vs"></span></a>Совместное и повторное использование кода в проектах Visual Studio

В состав мобильных служб входит клиентская библиотека .NET, переносимая библиотека классов (PCL) .NET Framework, в которой поддерживается разработка на всех платформах Windows. Дополнительную информацию см. в разделе [Как использовать клиент .NET с мобильными службами][Как использовать клиент .NET с мобильными службами]. Это упрощает повторное использование в различных проектах C# кода мобильных служб, предназначенного, например, для доступа к данным или аутентификации.

Одним из общих подходов к совместному и повторному использованию кода C# в нескольких проектах является реализация шаблона «модель-представление-представление модели» (MVVM) и совместное использование сборок на нескольких платформах. Можно реализовать классы модели и представления модели в проекте переносимой библиотеки классов в Visual Studio, а затем создать представления, настроенные для различных платформ. Код модели, общий для платформ, может (например) извлекать данные из источника, такого как мобильная служба, безотносительно к платформе. Библиотека MSDN предоставляет [описание и пример][описание и пример], обсуждение [отличий API][отличий API], пример [использования библиотек переносимого класса для реализации шаблона MVVM][использования библиотек переносимого класса для реализации шаблона MVVM], дополнительные [инструкции][инструкции] и сведения об [управлении ресурсами][управлении ресурсами] в проектах библиотеки переносимого класса.

В дополнение к этой общей инструкции Visual Studio предоставляет особые средства для повторного использования кода мобильных служб в нескольких проектах клиентских приложений. Этот вопрос обсуждается в следующих разделах. Общую информацию о выполнении сборки кроссплатформенных приложений в Visual Studio 2013 см. в статье [Cross-Platform Development in Visual Studio][Кроссплатформенная разработка в Visual Studio] (Кроссплатформенная разработка в Visual Studio).

### Универсальные приложения Windows

В обновлении 2 для Visual Studio 2013 добавлена поддержка проектов универсальных приложений Windows. Универсальные приложения — это решения, которые включают проекты приложений Магазина Windows 8.1 и Магазина Windows Phone 8.1 в проект с общим кодом. В таком проекте общий код рассматривается как часть проекта Магазина Windows и проекта Windows Phone. Дополнительную информацию см. в статье [Разработка универсальных приложений для Windows для всех устройств с Windows][Разработка универсальных приложений для Windows для всех устройств с Windows]. Универсальные приложения Windows можно писать на C#/XAML и JavaScript/HTML.

По умолчанию на вкладке быстрого запуска мобильных служб на [портале управления Azure][портале управления Azure] в изначально создается версия универсального приложения Windows примера приложения TodoList. На выбор можно скачать версию C#/XAML или JavaScript/HTML проекта. Дополнительную информацию см. в разделе [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

> [WACOM.NOTE]В версии C# примера проекта приложения, полученного с этого портала, совместно используется код MainPage.xaml.cs страницы, но не используется модель представлений. Пример того, как на платформе TodoList создать проект универсального приложения Windows на языке C#, используя MVVM, см. в статье [Universal Windows app project for Azure Mobile Services using MVVM][Universal Windows app project for Azure Mobile Services using MVVM] (Проект универсального приложения Windows для мобильных служб Azure с использованием MVVM).

### <span id="xamarin"></span></a>Разработка в Xamarin

Свой опыт разработки в Visual Studio и C# можно применить для разработки приложений для iOS и Android с помощью Xamarin и Visual Studio или Xamarin Studio. В Xamarin используется кроссплатформенная реализация платформы .NET Framework, что позволяет применять код C# для разработки приложений для iOS и Android. В Xamarin можно использовать существующий код проектов Windows, в котором доступ к мобильным службам осуществляется с помощью клиентской библиотеки .NET для мобильных служб. Дополнительную информацию см. в разделе [Кроссплатформенная разработка в Visual Studio][Кроссплатформенная разработка в Visual Studio].

Описание начальных этапов создания приложений Xamarin, в которых используются мобильные службы, см. в кратких учебниках по Xamarin (для [iOS][iOS] или [Android][Android]).

### Приложения для Магазина Windows и Windows Phone Silverlight

В Windows Phone 8.1 для разработки приложений можно использовать ранее созданный код XAML на основе Silverlight. Можно также использовать код XAML на основе среды выполнения Windows, что позволяет разрабатывать универсальные приложения Windows. Дополнительную информацию о приложениях для Windows Phone 8.1 Silverlight и Магазина Windows Phone 8.1 см. в статье [What's next for Windows Phone 8 developers][What's next for Windows Phone 8 developers] (Новинки для разработчиков приложений для Windows Phone 8).

Клиентская библиотека .NET для мобильных служб поддерживает и приложения для Магазина Windows Phone 8.1, и приложения для Windows Phone Silverlight 8.1. Поскольку приложения среды выполнения Windows и Windows Phone Silverlight нельзя создавать в рамках одного и того же проекта, в качестве стратегии повторного использования кода следует рассмотреть использование PCL и MVVM, как описано выше.

> [WACOM.NOTE]Чтобы использовать аутентификацию клиента при едином входе через учетную запись Майкрософт в приложения среды выполнения Windows и Windows Phone Silverlight, необходимо сначала зарегистрировать приложение среды выполнения Windows на панели мониторинга Магазина Windows. Это связано с тем, что после создания регистрации Live Connect для Windows Phone невозможно создать регистрацию для Магазина Windows. Дополнительную информацию о том, как это сделать, см. в разделе **Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect** ([Магазин Windows][Магазин Windows]/[Windows Phone][Windows Phone]).

<!-- URLs -->

  [Кроссплатформенные push-уведомления]: #push
  [Разработка кроссплатформенных приложений]: #xplat-app-dev
  [Совместное использование кода в проектах Visual Studio]: #shared-vs
  [Центр разработчика для мобильных служб]: /ru-ru/documentation/services/mobile-services/
  [Концентраторы уведомлений Azure]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
  [Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [SendAsync]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
  [PushClient]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx
  [ApiServices.Push]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx
  [IPushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx
  [APNS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
  [GCM]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
  [WNS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [MPNS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [ApplePushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx
  [GooglePushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx
  [WindowsPushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx
  [MpnsPushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx
  [TemplatePushMessage]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
  [объекта push]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj554217.aspx
  [1]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push
  [2]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push
  [3]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [4]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [объект apns]: http://msdn.microsoft.com/ru-ru/library/azure/jj839711.aspx
  [Объект gcm]: http://msdn.microsoft.com/ru-ru/library/azure/dn126137.aspx
  [Объект wns]: http://msdn.microsoft.com/ru-ru/library/azure/jj860484.aspx
  [Объект mpns]: http://msdn.microsoft.com/ru-ru/library/azure/jj871025.aspx
  [**Appcelerator**]: http://go.microsoft.com/fwlink/p/?LinkId=509987
  [учебнике по платформе Appcelerator]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [**PhoneGap**]: https://go.microsoft.com/fwLink/p/?LinkID=390707
  [**Cordova**]: http://cordova.apache.org/
  [кратком учебнике по платформе PhoneGap]: /ru-ru/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/
  [Getting Started with Multi-Device Hybrid Apps Using HTML and JavaScript]: http://msdn.microsoft.com/ru-ru/library/dn771545.aspx
  [**Sencha Touch**]: http://go.microsoft.com/fwlink/p/?LinkId=509988
  [кратком учебнике по платформе Sencha Touch]: /ru-ru/documentation/articles/partner-sencha-mobile-services-get-started/
  [**Xamarin**]: https://go.microsoft.com/fwLink/p/?LinkID=330242
  [Разработка в Xamarin]: #xamarin
  [Кроссплатформенная разработка в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/dn771552.aspx
  [Как использовать клиент .NET с мобильными службами]: ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
  [описание и пример]: http://msdn.microsoft.com/ru-ru/library/gg597391(v=vs.110)
  [отличий API]: http://msdn.microsoft.com/ru-ru/library/gg597392(v=vs.110)
  [использования библиотек переносимого класса для реализации шаблона MVVM]: http://msdn.microsoft.com/ru-ru/library/hh563947(v=vs.110)
  [инструкции]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/jj714086(v=vs.105).aspx
  [управлении ресурсами]: http://msdn.microsoft.com/ru-ru/library/hh871422(v=vs.110)
  [Разработка универсальных приложений для Windows для всех устройств с Windows]: http://go.microsoft.com/fwlink/p/?LinkId=509905
  [портале управления Azure]: https://manage.windowsazure.com
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
  [Universal Windows app project for Azure Mobile Services using MVVM]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de
  [iOS]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started
  [Android]: /ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started
  [What's next for Windows Phone 8 developers]: http://msdn.microsoft.com/ru-ru/library/windows/apps/dn655121(v=vs.105).aspx
  [Магазин Windows]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
  [Windows Phone]: /ru-ru/develop/mobile/tutorials/single-sign-on-wp8/
