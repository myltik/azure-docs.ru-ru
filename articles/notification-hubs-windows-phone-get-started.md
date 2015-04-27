<properties 
	pageTitle="Приступая к работе с концентраторами уведомлений Azure" 
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# Приступая к работе с концентраторами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом разделе показано, как использовать центры уведомлений Windows Azure для отправки push-уведомлений в приложение Sliverlight для Windows Phone 8 или Windows Phone 8.1. Если вы намерены использовать Windows Phone 8.1 (без Silverlight), см. вариант статьи [для всех устройств Windows](notification-hubs-windows-store-dotnet-get-started.md)
В этом учебнике вам предстоит создать пустое приложение Windows Phone 8, получающее push-уведомления с помощью службы push-уведомлений Майкрософт (MPNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений.

> [AZURE.NOTE] Центры уведомлений Windows Phone SDK не поддерживают использование WNS с приложениями Windows Phone 8.1 Silverlight. Чтобы использовать WNS (вместо MPNS) с приложениями на основе Silverlight для Windows Phone 8.1, воспользуйтесь примером из статьи [Концентратор уведомлений - учебник WP Silverlight]. В нем используются интерфейсы API REST.

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. 

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ [Microsoft Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными центрам уведомлений для приложений Windows Phone 8. 

> [AZURE.NOTE] Для работы с этим учебником требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Создание концентратора уведомлений

1. Войдите на [портал управления Azure] и щелкните пункт **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Шина обслуживания**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый центр уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните вверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Центры уведомлений** и выберите недавно созданный центр уведомлений.

   	![][10]

6. Щелкните расположенный внизу элемент **Информация о подключении**. Запишите значения двух строк подключения.

   	![][12]

7. Откройте вкладку **Настройка**, а затем установите флажок **Включить push-уведомления без аутентификации** в разделе **Параметры уведомлений Windows Phone**.

   	![][15]

Теперь у вас есть строки подключения, чтобы зарегистрировать приложение Windows Phone 8 и отправлять уведомления.

> [AZURE.NOTE] В этом учебнике используется MPNS в режиме без проверки подлинности. Режим MPNS без проверки подлинности налагает ограничения на использование уведомлений, отправляемых для каждого канала. Концентраторы уведомлений поддерживают [режим работы MPNS с проверкой подлинности](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Подключение приложения к концентратору уведомлений

1. В Visual Studio создайте новое консольное приложение Windows Phone 8.

   	![][13]

	В Visual Studio 2013 с обновлением 2 вместо этого вы создаете приложение Windows Phone Silverlight.
	
	![][11]	

2. В Visual Studio щелкните правой кнопкой по решению, после чего щелкните **Управление пакетами NuGet**. 

	При этом отобразится диалоговое окно "Управление пакетами NuGet".

3. Выполните поиск `WindowsAzure.Messaging.Managed` и щелкните **Установить**, после чего примите условия использования. 

	![][20]

	После этого будет выполнено скачивание, установка и добавление ссылки на библиотеку Azure Messaging для Windows при помощи <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета NuGet WindowsAzure.Messaging.Managed</a>. 

4. Откройте файл App.xaml.cs и добавьте следующие операторы `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. При возникновении следующего кода в верхней части метода **Application_Launching** в App.xaml.cs:
	
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
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Обязательно вставьте имя центра и строку подключения с именем **DefaultListenSharedAccessSignature**, полученные в предыдущем разделе.
    Этот код получает универсальный код ресурса (URI) канала (ChannelURI) для приложения из MPNS, а затем регистрирует ChannelURI в вашем центре уведомлений. Он также гарантирует регистрацию ChannelURI в центре уведомлений при каждом запуске приложения.

	>[AZURE.NOTE]В этом учебнике на устройство отправляется всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале. Чтобы поддерживать одновременно и всплывающие уведомления и уведомления на плитке, следует вызвать оба метода - **BindToShellTile** и **BindToShellToast**. 
    
6. В обозревателе решений разверните меню **Свойства**, откройте файл WMAppManifest.xml, щелкните вкладку **Возможности** и убедитесь, что установлен флажок **ID___CAP___PUSH_NOTIFICATION**.

   	![][14]

   	Это гарантирует, что приложение сможет получать push-уведомления.
	
7. Нажмите клавишу F5, чтобы запустить приложение.

	Отобразится сообщение о регистрации сообщения.

##Отправка уведомления из серверной части

Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Пример отправки уведомлений с сервера мобильных служб Azure, интегрированного с концентраторами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([сервер .NET](mobile-services-javascript-backend-windows-phone-get-started-push.md) | [Серверная часть JavaScript](mobile-services-javascript-backend-windows-phone-get-started-push.md)).  Пример отправки уведомлений с помощью API REST см. в статье **Использование концентраторов уведомлений из Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Щелкните правой кнопкой решение, выберите **Добавить**, затем **Новый проект...**, затем в разделе **Visual C#** щелкните **Windows** и **Консольное приложение**, после чего щелкните **ОК**. 

   	![][6]

	При этом в решение добавляется новое консольное приложение Visual C#. Это можно также сделать отдельным решением. 

4. Щелкните правой кнопкой мыши, затем **Сервис**, затем щелкните **Диспетчер пакетов библиотеки**, после чего щелкните **Консоль диспетчера пакетов**. 

	Отобразится консоль диспетчера пакетов.

6. В окне консоли установите свойство **Проект по умолчанию** для вашего нового проекта консольного приложения, после чего в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
	После этого будет добавлена ссылка на пакет SDK для Azure Service Bus с помощью пакета NuGet <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

5. Откройте файл Program.cs и добавьте следующие операторы `using`:

        using Microsoft.ServiceBus.Notifications;

6. В классе **Program** добавьте следующий метод:

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

	Обязательно замените заполнитель "имя центра" именем центра уведомлений, отображаемым на вкладке **Центры уведомлений** портала: Замените заполнитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка центра уведомлений". 

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не с доступом для **прослушивания**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

4. Затем добавьте следующую строку в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Когда эмулятор Windows Phone будет работать, а приложение будет закрыто, установите проект приложения консоли в качестве начального проекта по умолчанию, после чего нажмите клавишу F5, чтобы запустить приложение. 

	Вы получите всплывающее уведомление. Приложение загружается при касании этого всплывающего баннера.

В [каталоге всплывающих уведомлений] и [каталоге плиток] MSDN можно найти все возможные полезные данные.

##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows Phone 8. Чтобы ориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании центров уведомлений см. в [руководстве по использованию центров уведомлений].



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
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Портал управления Azure]: https://manage.windowsazure.com/
[руководстве по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Режим работы MPNS с аутентификацией]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-phone-send-breaking-news.md
[каталоге всплывающих уведомлений]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[каталоге плиток]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Концентратор уведомлений - учебник WP Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!--HONumber=49-->