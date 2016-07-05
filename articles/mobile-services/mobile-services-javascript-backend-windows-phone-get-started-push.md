<properties
	pageTitle="Добавление push-уведомлений в приложение мобильных служб (Windows Phone) | Microsoft Azure"
	description="Узнайте, как использовать мобильные службы и центры уведомлений Azure для отправки push-уведомлений в приложение Windows Phone."
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
	ms.date="12/07/2015"
	ms.author="glenga"/>


# Добавление push-уведомлений к приложению мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Обзор

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для Windows Phone Silverlight. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами центр уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

Этот учебник основан на примере приложения TodoList. Прежде чем приступить к изучению данного учебника, необходимо выполнить задания раздела [Добавление мобильных служб в существующее приложение], чтобы подключить свой проект к мобильной службе Если мобильная служба не подключена, мастер добавления push-уведомлений может создать это подключение.

>[AZURE.NOTE] Информацию об отправке push-уведомлений в приложение Магазина Windows Phone 8.1 см. в версии для [приложения Магазина Windows](mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md) этого учебника.

##<a id="update-app"></a> Обновление приложения для регистрации в целях получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующую инструкцию `using`:

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

	>[AZURE.NOTE]В этом учебнике мобильная служба отправляет на устройство всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале.

4. В верхней части обработчика событий **Application\_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.

6.	В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, щелкните вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION**.

   	![Включение уведомлений в оболочке VS](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления.

##<a id="update-scripts"></a> Обновление серверных скриптов для отправки push-уведомлений

Наконец, необходимо обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem для отправки уведомлений.

1. Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

2. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

	Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

	>[AZURE.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув **Отправить** и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе [Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone].

##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    >[AZURE.NOTE] При проверке в эмуляторе Windows Phone может возникнуть исключение "401 Не санкционировано" RegistrationAuthorizationException. Это может произойти при выполнении вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

5. В приложении введите в текстовое поле слова «hello push», щелкните **Сохранить**, а затем сразу же щелкните кнопку запуска или кнопку «Назад», чтобы выйти из приложения.

   	![Ввод текста в приложение](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Заметьте, что устройство получает всплывающее уведомление с текстом **hello push**.

	![Получено всплывающее уведомление](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получить всплывающее уведомление, пока приложение активно, необходимо обработать событие [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и центров уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка широковещательных уведомлений подписчикам](../notification-hubs/notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) <br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

+ [Отправка подписчикам уведомлений, независимых от платформы ](../notification-hubs/notification-hubs-aspnet-cross-platform-notification.md) <br/>Узнайте, как использовать шаблоны для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.


Дополнительные сведения о мобильных службах и центрах уведомлений см. в следующих разделах.

* [Концентраторы уведомлений Azure — рекомендации по диагностике](../notification-hubs/notification-hubs-push-notification-fixer.md) <br/>Узнайте, как устранять неполадки, связанные с push-уведомлениями.

* [Приступая к работе с проверкой подлинности] <br/>Узнайте, как проверять подлинность пользователей приложения с разными типами учетных записей, используя мобильные службы.

* [Что такое концентраторы уведомлений?] <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Справочник принципов использования мобильных служб .NET] <br/>Узнайте, как использовать мобильные службы в .NET.

* [Справочник серверных скриптов мобильных служб] <br/>Узнайте, как реализовать бизнес-логику в мобильной службе.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Добавление мобильных служб в существующее приложение]: mobile-services-windows-phone-get-started-data.md
[Приступая к работе с проверкой подлинности]: mobile-services-windows-phone-get-started-users.md

[Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Что такое концентраторы уведомлений?]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0622_2016-->