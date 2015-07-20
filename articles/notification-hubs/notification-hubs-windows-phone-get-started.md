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
	ms.topic="get-started-article" 
	ms.date="06/16/2015"
	ms.author="wesmc"/>
# Приступая к работе с концентраторами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом разделе показано, как использовать концентраторы уведомлений Windows Azure для отправки push-уведомлений в приложение Sliverlight для Windows Phone 8 или Windows Phone 8.1. Если вы ориентируетесь на Windows Phone 8.1 (не Silverlight), см. версию [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md). В этом учебнике вам предстоит создать пустое приложение Windows Phone 8, получающее push-уведомления с помощью службы push-уведомлений Майкрософт (MPNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

> [AZURE.NOTE]Концентраторы уведомлений Windows Phone SDK не поддерживают использование WNS с приложениями Windows Phone 8.1 Silverlight. Использование WNS (вместо MPNS) с приложениями Windows Phone 8.1 Silverlight описано в примере [Концентратор уведомлений: учебник для WP Silverlight], в котором используются API REST.

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений.

##Предварительные требования

Для работы с данным учебником требуется следующее:

+ [Microsoft Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Windows Phone 8.

> [AZURE.NOTE]Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Создание концентратора уведомлений

1. Войдите на портал [управления Windows Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите только что созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Концентраторы** уведомлений и выберите недавно созданный концентратор уведомлений.

   	![][10]

6. Щелкните расположенный внизу элемент **Сведения о подключении**. Запишите две строки подключения.

   	![][12]

7. Откройте вкладку **Настройка**, а затем установите флажок **Включить push-уведомления без проверки подлинности** в разделе **Параметры уведомлений Windows Phone**.

   	![][15]

Теперь у вас есть строки подключения, чтобы зарегистрировать приложение Windows Phone 8 и отправлять уведомления.

> [AZURE.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. Режим MPNS без проверки подлинности налагает ограничения на использование уведомлений, отправляемых для каждого канала. Концентраторы уведомлений поддерживают [режим работы MPNS с проверкой подлинности]http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Подключение приложения к концентратору уведомлений

1. В Visual Studio создайте новое консольное приложение Windows Phone 8.

   	![][13]

	В Visual Studio 2013 с обновлением 2 вместо этого вы создаете приложение Windows Phone Silverlight.

	![][11]

2. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**.

	При этом отобразится диалоговое окно "Управление пакетами NuGet".

3. Выполните поиск по запросу `WindowsAzure.Messaging.Managed` и щелкните **Установить**, после чего примите условия использования.

	![][20]

	После этого будет выполнена загрузка, установка и добавление ссылки на библиотеку Azure Messaging для Windows при помощи <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета WindowsAzure.Messaging.Managed NuGet</a>.

4. Откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

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

    Обязательно вставьте имя концентратора и строку подключения с именем **DefaultListenSharedAccessSignature**, полученные в предыдущем разделе. Этот код получает универсальный код ресурса (URI) канала (ChannelURI) для приложения из MPNS, а затем регистрирует ChannelURI в вашем концентраторе уведомлений. Он также гарантирует регистрацию ChannelURI в концентраторе уведомлений при каждом запуске приложения.

	>[AZURE.NOTE]В этом учебнике на устройство отправляется всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале. Чтобы поддерживать одновременно и всплывающие и мозаичные уведомления, следует вызвать оба метода — **BindToShellTile** и **BindToShellToast**.

6. В обозревателе решений разверните меню **Свойства**, откройте файл WMAppManifest.xml, щелкните вкладку **Возможности** и убедитесь, что установлен флажок **ID___CAP___PUSH_NOTIFICATION**.

   	![][14]

   	Это гарантирует, что приложение сможет получать push-уведомления.

7. Нажмите клавишу F5, чтобы запустить приложение.

	Отобразится сообщение о регистрации сообщения.

##Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений из серверных компонентов мобильных служб Azure, интегрированных с центрами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Серверная часть .NET](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [Серверная часть JavaScript](../mobile-services-javascript-backend-windows-phone-get-started-push.md)). Примеры отправки уведомлений с использованием API REST см. в статье **Использование центров уведомлений из Java или PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Щелкните правой кнопкой мыши решение, выберите **Добавить** и **Новый проект...**. После этого в разделе **Visual C#** последовательно выберите **Windows** и **Консольное приложение**, а затем нажмите кнопку **OK**.

   	![][6]

	При этом в решение добавляется новое консольное приложение Visual C#. Это можно сделать также и в отдельном решении.

4. Щелкните правой кнопкой мыши, последовательно выберите **Средства**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

	Откроется консоль диспетчера пакетов.

6. В окне консоли определите свой новый проект как **Проект по умолчанию**, а затем в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

	В результате будет добавлена ссылка на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета WindowsAzure.ServiceBus NuGet</a>.

5. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

6. Добавьте в класс **Program** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Обязательно замените заполнитель «hub name» на имя центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала. Кроме того, замените заполнитель строки подключения на строку подключения с именем **DefaultFullSharedAccessSignature**, полученную в разделе «Настройка центра уведомлений».

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. У строки с доступом к прослушиванию отсутствуют разрешения для отправки уведомлений.

4. Затем добавьте следующую строку в метод Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Когда эмулятор Windows Phone будет работать, а приложение будет закрыто, установите проект приложения консоли в качестве начального проекта по умолчанию, после чего нажмите клавишу F5, чтобы запустить приложение.

	Вы получите всплывающее уведомление. Приложение загружается при касании этого всплывающего баннера.

В [каталоге всплывающих уведомлений] и [каталоге уведомлений на плитке] MSDN можно найти самые разнообразные полезные данные.

##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows Phone 8. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений].



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
[управления Windows Azure]: https://manage.windowsazure.com/
[руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[режим работы MPNS с проверкой подлинности]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-phone-send-breaking-news.md
[каталоге всплывающих уведомлений]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[каталоге уведомлений на плитке]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Концентратор уведомлений: учебник для WP Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp
 

<!---HONumber=July15_HO2-->