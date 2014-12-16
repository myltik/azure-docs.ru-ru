<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для Windows Phone Silverlight. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Обновление приложения: регистрация для получения уведомлений](#update-app)
2. [Обновление серверных скриптов для отправки push-уведомлений](#update-scripts)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создаст это подключение. 

>[WACOM.NOTE]Информацию об отправке push-уведомлений в приложение для Магазина Windows Phone 8.1 см. в версии для [приложения для Магазина Windows](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push) этого учебника.

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте в него следующую инструкцию `using`:

        using Microsoft.Phone.Notification;

3. Добавьте в App.xaml.cs следующий код:
	
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
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Этот код получает свойство ChannelURI для приложения из службы push-уведомлений Майкрософт (MPNS), которое используется Windows Phone Silverlight 8.x, и регистрирует полученное свойство ChannelURI для push-уведомлений.

	>[WACOM.NOTE]В этом учебнике мобильная служба отправляет на устройство всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале.

4. В верхней части обработчика событий **Application_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. Может потребоваться выполнять эту регистрацию в приложении только периодически, чтобы гарантировать, что регистрация актуальна. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Появится всплывающее диалоговое окно с ключом регистрации.
  
6.	 В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml file, откройте вкладку **Возможности** и убедитесь, что установлен флажок **ID___CAP___PUSH_NOTIFICATION**.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. 

##<a id="update-scripts"></a> Обновление серверных скриптов для отправки push-уведомлений

Наконец, необходимо обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem для отправки уведомлений.

1. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**. 

   	![][10]

2. Замените функцию вставки следующим кодом и щелкните **Сохранить**:

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		    '</wp:Text2></wp:Toast></wp:Notification>';
		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) во все регистрации приложения Windows Phone после успешной вставки.

3. Откройте вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

	>[WACOM.NOTE]При изучении этого учебника с использованием старой мобильной службы вы можете увидеть внизу вкладки **Push-уведомления** ссылку **Включение улучшенных push-уведомлений**. Щелкните по ней, чтобы обновить мобильную службу и интегрировать ее с концентраторами уведомлений. Это изменение нельзя будет отменить. Подробности включения улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>.

	![][11]

	Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

	>[WACOM.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув **Отправить** и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе [Настройка веб-службы с проверкой подлинности для отправки push-уведомлений в Windows Phone].

##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    >[WACOM.NOTE] При проверке в эмуляторе Windows Phone может возникнуть исключение 401 "Неавторизованное RegistrationAuthorizationException". Это может произойти во время вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

5. В приложении введите в текстовое поле слова "hello push", нажмите **Сохранить**, а затем сразу же нажмите кнопку запуска или кнопку "Назад", чтобы выйти из приложения.

   	![][4]

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Обратите внимание на то, что устройство получит всплывающее уведомление с текстом **hello push**.

	![][5]

	>[WACOM.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получить всплывающее уведомление, пока приложение активно, необходимо обработать событие [ShellToastNotificationReceived](http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).



## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении для Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

+ [Отправка широковещательных уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.

<!---+ [Send template-based notifications to subscribers]
	<br/>Использование шаблонов для отправки push-уведомлений из мобильной службы без создания зависящих от платформы полезных данных на сервере.
-->
Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о способах реализации бизнес-логики в мобильной службе.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users

[Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Отправка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Отправка подписчикам уведомлений на основе шаблона]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/
