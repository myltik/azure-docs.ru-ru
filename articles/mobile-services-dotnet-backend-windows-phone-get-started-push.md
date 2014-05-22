<properties pageTitle="Приступая к работе с концентраторами push-уведомлений при использовании мобильных служб среды выполнения .NET" metaKeywords="" description="Узнайте, как использовать мобильные службы Azure в среде выполнения .NET и концентраторы уведомлений для отправки push-уведомлений в приложение Windows Phone." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors="wesmc"  solutions="" writer="wesmc" manager="" editor=""  />


# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Магазин Windows — C#">Магазин Windows — C#</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a>
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Серверная версия .NET" class="current">Серверная версия .NET</a> | 
	<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="Серверная версия JavaScript">Серверная версия JavaScript</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure в среде выполнения .Net для отправки push-уведомлений в приложение Windows Phone 8. 
В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. В результате ваша мобильная служба среды выполнения .Net будет отправлять push-уведомление с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Обновление приложения: регистрация для получения уведомлений](#update-app)
3. [Обновление сервера для отправки push-уведомлений](#update-server)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. 

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Microsoft.Phone.Notification;

2. Добавьте следующий метод `AcquirePushChannel` в класс `App`: 
	
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

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке приложения. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
  
6. В Visual Studio откройте файл Package.appxmanifest и убедитесь, что для параметра **Всплывающие уведомления** задано значение **Да** на вкладке **Пользовательский интерфейс приложения**.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. 

##<a id="update-server"></a> Обновление сервера для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

<ol start="2">
<li><p>Выполните вход на <a href=" https://manage.windowsazure.com/" target="_blank">портал управления Azure</a>, щелкните <strong>Мобильные службы</strong>, а затем щелкните ваше приложение.</p></li>

<li><p>Откройте вкладку <strong>Push-уведомления</strong>, установите флажок <strong>Включить push-уведомления без проверки подлинности</strong>, затем щелкните <strong>Сохранить</strong>.</p>

</li>
</ol>

   ![][4]

>[WACOM.NOTE]В этом учебнике MPNS используется в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув <strong>Отправить</strong> и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx">Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone</a>

Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

5. В приложении введите в текстовом поле текст "hello push", затем щелкните **Сохранить**.

   	![][2]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Заметьте, что приложение получает всплывающее уведомление с текстом **hello push**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для магазина Windows возможностей работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении магазина Windows.

+ [Рассылка уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений пользователям]
	<br/>Дополнительные сведения о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

+ [Отправка пользователям уведомлений между различными платформами]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним с использованием мобильных служб среды выполнения .Net.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб среды выполнения .Net.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

