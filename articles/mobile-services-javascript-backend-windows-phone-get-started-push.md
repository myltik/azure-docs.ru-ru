<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как применять мобильные службы Azure и концентраторы уведомлений для отправки push-уведомлений в приложение Магазина Windows." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors="glenga" solutions="" manager="" editor="" />


# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Магазин Windows — C#">Магазин Windows — C#</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Серверная версия .NET">Серверная версия .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/"  title="Серверная версия JavaScript" class="current">Серверная версия JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение магазина Windows. 
В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом учебнике показана интеграция мобильных служб с концентраторами уведомлений, которая в настоящее время находится на этапе предварительной версии. По умолчанию отправка push-уведомлений с помощью концентраторов уведомлений из серверной части JavaScript не включена.  После создания нового концентратора уведомлений процесс интеграции необратим. Push-уведомления для iOS и Android в настоящее время доступны только при использовании поддержки push-уведомлений по умолчанию, которая описана в [этой версии раздела](/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Обновление приложения: регистрация для получения уведомлений](#update-app)
2. [Обновление серверных скриптов для отправки push-уведомлений](#update-scripts)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение. 

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

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

    Этот код получает URI канала (ChannelURI) для приложения из WNS, а затем регистрирует этот URI для push-уведомлений.

	>[WACOM.NOTE]В этом учебнике мобильная служба отправляет на устройство всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале.

4. В верхней части обработчика событий **Application_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
  
6.	В обозревателе решений разверните элемент **Свойства**, откройте файл WMAppManifest.xml, перейдите на вкладку **Возможности** и убедитесь, что установлен флажок для функции **ID___CAP___PUSH_NOTIFICATION**.

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

3. Перейдите на вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

	![][11]

	Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

	>[WACOM.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув **Отправить** и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе [Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone].

##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

5. В приложении введите в текстовом поле текст "hello push", затем щелкните **Сохранить**.

   	![][4]

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
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push

[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx

