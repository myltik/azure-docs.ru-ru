<properties 
	pageTitle="Использование мобильных служб и центры уведомлений Azure для отправки уведомлений в приложение Windows Phone." 
	description="Дополнительные сведения об использовании концентраторов уведомлений с мобильными службами Azure для отправки push-уведомлений в приложение Windows Phone." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="wesmc"/>

# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

##Обзор

В этом разделе описано использование мобильных служб Azure с серверной службой .NET для отправки push-уведомлений в приложение для Windows Phone Silverlight 8 с помощью службы push-уведомлений Майкрософт (MPNS). В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами центр уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

Этот учебник создан на основе проекта TodoList, посвященному мобильным службам. Прежде чем приступить к изучению данного учебника, необходимо выполнить задания учебника [Добавление мобильных служб к существующему приложению], чтобы подключить свой проект к мобильной службе.

>[AZURE.NOTE]Этот учебник ориентирован на приложения Windows Phone 8.1 Silverlight. Если же вы создаете приложение для Магазина Windows Phone 8.1, см. версию этого учебника для [приложения для Магазина Windows](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push). Информацию о приложениях для Windows Phone Silverlight и их сравнении с приложениями для Магазина Windows Phone см. в разделе [Приложения для Windows Phone Silverlight 8.1].

##Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующую инструкцию `using`:

        using Microsoft.Phone.Notification;

2. Добавьте следующий метод `AcquirePushChannel` в класс `App`:

        public static HttpNotificationChannel CurrentChannel { get; private set; }	
        
        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }
            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {
                    // Register for notifications using the new channel
                    System.Exception exception = null;
                    try
                    {
                        await MobileService.GetPush()
                            .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                    }
                    catch (System.Exception ex)
                    {
                        CurrentChannel.Close();
                        exception = ex;
                    }
                    if (exception != null)
                    {
                        Deployment.Current.Dispatcher.BeginInvoke(() =>
                        {
                            MessageBox.Show(exception.Message, 
                                            "Registering for Push Notifications",
                                            MessageBoxButton.OK);
                        });
                    }
            });
            CurrentChannel.ShellToastNotificationReceived += 
                new EventHandler<NotificationEventArgs>((o, args) =>
                {
                    string message = "";
                    foreach (string key in args.Collection.Keys)
                    {
                        message += key + " : " + args.Collection[key] + ", ";
                    }
                    Deployment.Current.Dispatcher.BeginInvoke(() =>
                    {
                        MessageBox.Show(message);
                    });
            });
        }

    Этот код извлекает URI канала для приложения, если он существует. В противном случае он будет создан. Затем URI канала открывается и привязывается для всплывающих уведомлений. После полного открытия универсального кода ресурса (URI) канала вызывается обработчик для метода `ChannelUriUpdated`, и канал регистрируется для получения push-уведомлений. В случае сбоя регистрации канал закрывается, так что при последующих выполнениях приложения можно повторить попытку регистрации. Обработчик `ShellToastNotificationReceived` настроен таким образом, что приложение во время выполнения может получать и обрабатывать push-уведомления.
    
4. В обработчике событий `Application_Launching` в файле App.xaml.cs добавьте в новый метод `AcquirePushChannel` следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке приложения. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
  
6. В Visual Studio откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления.

##Обновление сервера для отправки push-уведомлений

1. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs и обновите определение метода `PostTodoItem` с помощью следующего кода:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Этот код отправит push-уведомление (с текстом вставленного элемента) после вставки элемента списка дел. В случае возникновения ошибки код добавит запись в журнал ошибок, которую можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.

2. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

3. Откройте вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

   	![Вкладка push-уведомлений мобильных служб](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

	>[AZURE.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув <strong>Отправить</strong> и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в статье <a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">Настройка веб-службы с проверкой подлинности для отправки push-уведомлений в Windows Phone</a>.

Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

##Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    >[AZURE.NOTE]При проверке в эмуляторе Windows Phone может возникнуть исключение "401 Не санкционировано" RegistrationAuthorizationException. Это может произойти при выполнении вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

5. В приложении введите в текстовое поле слова «hello push», щелкните **Сохранить**, а затем сразу же щелкните кнопку запуска или кнопку «Назад», чтобы выйти из приложения.

   	![Вставка текста][2]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Заметьте, что устройство получает всплывающее уведомление с текстом **hello push**.

	![Получено push-уведомление](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получить всплывающее уведомление, пока приложение активно, необходимо обработать событие [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

##Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Windows Phone возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Далее полезно пройти следующий учебник [Отправка push-уведомлений аутентифицированным пользователям]. Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только аутентифицированным пользователям.

Просмотрите следующие разделы, посвященные мобильным службам и центрам уведомлений:

* [Добавление проверки подлинности в приложение] <br/>Узнайте, как проверять подлинность пользователей приложения с разными типами учетных записей, используя мобильные службы.

* [Что такое Центры уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Получите руководство по устранению неполадок и отладке решений концентраторов уведомлений.

* [Справочник принципов использования мобильных служб .NET] <br/>Дополнительные сведения об использовании мобильных служб в .NET.



<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png

<!-- URLs. -->
[Добавление мобильных служб к существующему приложению]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Добавление проверки подлинности в приложение]: mobile-services-dotnet-backend-windows-phone-get-started-users.md

[Отправка push-уведомлений аутентифицированным пользователям]: mobile-services-dotnet-backend-windows-phone-push-notifications-app-users.md

[Что такое Центры уведомлений?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-phone-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-phone-send-localized-breaking-news.md


[Справочник принципов использования мобильных служб .NET]: mobile-services-html-how-to-use-client-library.md
[Приложения для Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[портал управления Azure]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO3-->