<properties pageTitle="Приступая к работе с концентраторами push-уведомлений при использовании мобильных служб среды выполнения .NET" metaKeywords="" description="Узнайте, как использовать мобильные службы и центры уведомлений среды выполнения .NET Microsoft Azure для отправки push-уведомлений в приложение Windows Phone." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной службой .NET для отправки push-уведомлений в приложение для Windows Phone Silverlight 8. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Windows Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Обновление приложения: регистрация для получения уведомлений](#update-app)
3. [Обновление сервера для отправки push-уведомлений](#update-server)
4. [Включение push-уведомлений для локального тестирования](#local-testing)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе.

>[WACOM.NOTE]Этот учебник направлен на приложения для Windows Phone 8.1 Silverlight. Если вместо этого вы создаете магазина Windows Phone 8.1, см. [приложение Магазина Windows] (mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) версии этого учебника. Информацию о приложениях для Windows Phone Silverlight и их сравнении с приложениями для Магазина Windows Phone см. в разделе [Приложения для Windows Phone Silverlight 8.1]. 

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте в него следующую инструкцию `using`:

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

    Этот код извлекает URI канала для приложения, если он существует. В противном случае он будет создан. Затем URI канала открывается и привязывается для всплывающих уведомлений. После полного открытия URI канала вызывается обработчик для метода `ChannelUriUpdated` и канал регистрируется для получения push-уведомлений. В случае сбоя регистрации канал закрывается, так что при последующих выполнениях приложения можно повторить попытку регистрации. Обработчик `ShellToastNotificationReceived` настроен таким образом, что приложение во время выполнения может получать и обрабатывать push-уведомления.
    
4. В обработчике событий `Application_Launching` в файле App.xaml.cs добавьте в новый метод `AcquirePushChannel` следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке приложения. Может потребоваться выполнять эту регистрацию в приложении только периодически, чтобы гарантировать, что регистрация актуальна. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Появится всплывающее диалоговое окно с ключом регистрации.
  
6. В Visual Studio откройте файл Package.appxmanifest и убедитесь, что для параметра **Всплывающие уведомления** задано значение **Да** на вкладке **Пользовательский интерфейс приложения**.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. 

##<a id="update-server"></a> Обновление сервера для отправки push-уведомлений

1. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте TodoItemController.cs и обновите определение метода `PostTodoItem` с помощью следующего кода:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
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

    Этот код отправляет push-уведомления (с текстом вставленного элемента) после вставки элемента, подлежащего выполнению. В случае ошибки код добавляет запись в журнал ошибок, который можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.

2. Войдите в [Портал управления Azure], щелкните **Мобильные службы**, затем щелкните свое приложение.

3. Откройте вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

   	![][4]

	>[WACOM.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув <strong>Отправить</strong> и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx">Настройка веб-службы с проверкой подлинности для отправки push-уведомлений в Windows Phone</a>.

Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    >[WACOM.NOTE] При проверке в эмуляторе Windows Phone может возникнуть исключение 401 "Неавторизованное RegistrationAuthorizationException". Это может произойти во время вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

5. В приложении введите в текстовое поле слова "hello push", нажмите **Сохранить**, а затем сразу же нажмите кнопку запуска или кнопку "Назад", чтобы выйти из приложения.

   	![][2]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Обратите внимание на то, что устройство получит всплывающее уведомление с текстом **hello push**.

	![][5]

	>[WACOM.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получить всплывающее уведомление, пока приложение активно, необходимо обработать событие [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps">Дальнейшие действия</a>

В этом учебнике показаны основы включения в приложении для Windows Phone возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Затем рассмотрите возможность пройти следующий учебник, [Отправка push-уведомлений пользователям, прошедшим проверку подлинности], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку подлинности.

<!--+ [Send push notifications to authenticated users]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

+ [Отправка широковещательных уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.
-->
Просмотрите следующие разделы, посвященные мобильным службам и центрам уведомлений:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений. 

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Отправка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Отправка подписчикам уведомлений на основе шаблона]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
[Приложения Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Портал управления Azure]: https://manage.windowsazure.com/
