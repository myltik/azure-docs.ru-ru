---
title: "Отправка push-уведомлений в приложения Windows Phone с помощью центров уведомлений Azure | Документация Майкрософт"
description: "Из этого учебника вы узнаете, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение Silverlight для Windows Phone 8 или Windows Phone 8.1."
services: notification-hubs
documentationcenter: windows
keywords: "push-уведомление, push-уведомление, push-уведомление windows phone"
author: ysxu
manager: erikre
editor: erikre
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: f0bfe81f849813d146d644b32490af657b1071b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Отправка push-уведомлений в приложения Windows Phone с помощью центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).
> 
> 

В этом учебнике показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение Silverlight для Windows Phone 8 или Windows Phone 8.1. Если вы ориентируетесь на Windows Phone 8.1 (не Silverlight), см. версию [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
В этом учебнике вам предстоит создать пустое приложение Windows Phone 8, получающее push-уведомления с помощью службы push-уведомлений Майкрософт (MPNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений.

> [!NOTE]
> Центры уведомлений Windows Phone SDK не поддерживают использование службы push-уведомлений Windows (WNS) с приложениями Windows Phone 8.1 Silverlight. Использование WNS (вместо MPNS) с приложениями Windows Phone 8.1 Silverlight описано в статье [Центр уведомлений: учебник для Windows Phone Silverlight], в котором используются API REST.
> 
> 

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений.

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным учебником требуется следующее:

* [Microsoft Visual Studio 2012 Express для Windows Phone]или более поздняя версия.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными Центрам уведомлений для приложений Windows Phone 8.

## <a name="create-your-notification-hub"></a>Создание центра уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Щелкните раздел <b>Службы уведомлений</b> (в колонке <i>Параметры</i>), выберите пункт <b>Windows Phone (MPNS)</b> и установите флажок <b>Включить push-уведомления без проверки подлинности</b>.</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal — включение push-уведомлений без проверки подлинности](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Концентратор будет создан и настроен для отправки уведомлений без проверки подлинности для Windows Phone.

> [!NOTE]
> В этом учебнике используется MPNS в режиме без проверки подлинности. Режим MPNS без проверки подлинности налагает ограничения на использование уведомлений, отправляемых для каждого канала. Центры уведомлений поддерживают [Режим работы MPNS с проверкой подлинности](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) , позволяя отправить ваш сертификат.
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Подключение приложения к центру уведомлений
1. В Visual Studio создайте новое консольное приложение Windows Phone 8.
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    В Visual Studio 2013 с обновлением 2 вместо этого вы создаете приложение Windows Phone Silverlight.
   
    ![Visual Studio — новый проект — пустое приложение — Windows Phone Silverlight][11]
2. В Visual Studio щелкните решение правой кнопкой мыши, а затем выберите пункт **Управление пакетами NuGet**.
   
    Откроется диалоговое окно **Управление пакетами NuGet** .
3. Выполните поиск по запросу `WindowsAzure.Messaging.Managed` , щелкните **Установить**, а затем примите условия использования.
   
    ![Visual Studio — диспетчер пакетов NuGet][20]
   
    После этого будут выполнены скачивание, установка и добавление ссылки на библиотеку Azure Messaging для Windows с помощью <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета NuGet WindowsAzure.Messaging.Managed</a>.
4. Откройте файл App.xaml.cs и добавьте следующие инструкции `using` :
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. Добавьте следующий код в верхнюю часть метода **Application_Launching** в файле App.xaml.cs:
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > Значение **MyPushChannel** — это индекс, который используется для поиска существующего канала в коллекции [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Если канал отсутствует, создайте новую запись с таким именем.
   > 
   > 
   
    Обязательно вставьте имя концентратора и строку подключения с именем **DefaultListenSharedAccessSignature** , полученные в предыдущем разделе.
    Этот код получает универсальный код ресурса (URI) канала (ChannelURI) для приложения из MPNS, а затем регистрирует ChannelURI в вашем центре уведомлений. Он также гарантирует регистрацию ChannelURI в центре уведомлений при каждом запуске приложения.
   
   > [!NOTE]
   > В этом учебнике на устройство отправляется всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале. Чтобы поддерживать одновременно и всплывающие уведомления, и уведомления на плитке, следует вызвать оба метода: **BindToShellTile** и **BindToShellToast**.
   > 
   > 
6. В обозревателе решений разверните меню **Свойства**, откройте файл `WMAppManifest.xml`, перейдите на вкладку **Capabilities** (Возможности) и убедитесь, что установлен флажок **ID_CAP_PUSH_NOTIFICATION**.
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. Нажмите клавишу `F5` , чтобы запустить приложение.
   
    В приложении отобразится сообщение о регистрации.
8. Закройте приложение.  
   
   > [!NOTE]
   > Для получения всплывающего push-уведомления приложение не должно выполняться в фоновом режиме.
   > 
   > 

## <a name="send-push-notifications-from-your-backend"></a>Отправка push-уведомлений из серверной части
Push-уведомления можно отправлять с помощью центров уведомлений с любого сервера через общедоступный <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейс REST</a>. В этом учебнике мы будем отправлять push-уведомления с помощью консольного приложения .NET. 

Пример отправки push-уведомлений из серверной части веб-API ASP.NET, интегрированной с центрами уведомлений, см. в статье [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Примеры отправки push-уведомлений с использованием [REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx) см. в статье [How to use Notification Hubs from Java](notification-hubs-java-push-notification-tutorial.md) (Использование центров уведомлений из Java) и [How to use Notification Hubs from PHP](notification-hubs-php-push-notification-tutorial.md) (Использование центров уведомлений из PHP).

1. Щелкните решение правой кнопкой мыши, выберите пункты **Добавить** и **Создать проект...**. После этого в разделе **Visual C#** выберите **Windows** и **Консольное приложение**, а затем нажмите кнопку **ОК**.
   
       ![Visual Studio - New Project - Console Application][6]
   
    При этом в решение добавляется новое консольное приложение Visual C#. Это можно сделать также и в отдельном решении.
2. Щелкните **Сервис**, **Library Package Manager** (Диспетчер пакетов библиотеки), а затем — **Консоль диспетчера пакетов**.
   
    Отобразится консоль диспетчера пакетов.
3. В окне **консоли диспетчера пакетов** задайте свойство **Default project** (Проект по умолчанию) для нового проекта консольного приложения, а затем в окне консоли выполните следующую команду:
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета NuGet Microsoft.Azure.Notification Hubs</a>.
4. Откройте файл `Program.cs` и добавьте следующую инструкцию `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Добавьте в класс `Program` следующий метод:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    Обязательно замените заполнитель `<hub name>` именем центра уведомлений, которое отображается на портале. Кроме того, замените заполнитель строки подключения строкой подключения с именем **DefaultFullSharedAccessSignature** , полученной в разделе «Настройка концентратора уведомлений».
   
   > [!NOTE]
   > Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. У строки с доступом к прослушиванию отсутствуют разрешения для отправки push-уведомлений.
   > 
   > 
6. Добавьте следующую строку в метод `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Когда эмулятор Windows Phone будет запущен, а приложение будет закрыто, установите проект приложения консоли в качестве начального проекта по умолчанию, после чего нажмите клавишу `F5` , чтобы запустить приложение.
   
    Вы получите всплывающее push-уведомление. Приложение загружается при касании этого всплывающего баннера.

В [каталоге всплывающих уведомлений] и [каталоге уведомлений на плитке] на сайте MSDN можно найти самые разнообразные полезные данные.

## <a name="next-steps"></a>Дальнейшие действия
В этом простом примере осуществляется широковещательная рассылка push-уведомлений на все устройства Windows Phone 8. 

Сведения о том, как рассылать уведомления определенным пользователям, см. в руководстве [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. 

Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. 

Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Microsoft Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[каталоге всплывающих уведомлений]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[каталоге уведомлений на плитке]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Центр уведомлений: учебник для Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

