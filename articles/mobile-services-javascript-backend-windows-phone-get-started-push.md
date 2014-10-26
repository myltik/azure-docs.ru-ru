<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для Windows Phone Silverlight. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

> [WACOM.NOTE]В этом учебнике показана интеграция мобильных служб с центрами уведомлений, а именно — отправка push-уведомлений из мобильной службы. Пользователям со старой версией мобильной службы, в которой используется устаревшая система принудительной отправки и не обновлены центры уведомлений, *рекомендуется выполнить обновление* во время прохождения этого учебника. Для тех, кто предпочитает отказаться от обновления, предназначена следующая версия учебника: [Приступая к работе с push-уведомлениями (в устаревшей версии)][Приступая к работе с push-уведомлениями (в устаревшей версии)]

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Обновление приложения: регистрация для получения уведомлений][Обновление приложения: регистрация для получения уведомлений]
2.  [Обновление серверных скриптов для отправки push-уведомлений][Обновление серверных скриптов для отправки push-уведомлений]
3.  [Вставка данных для получения push-уведомлений][Вставка данных для получения push-уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение.

> [WACOM.NOTE]Информацию об отправке push-уведомлений в приложение для Магазина Windows Phone 8.1 см. в версии для [приложения для Магазина Windows][приложения для Магазина Windows] этого учебника.

## <span id="update-app"></span></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1.  В Visual Studio откройте файл App.xaml.cs и добавьте следующую инструкцию `using`:

        using Microsoft.Phone.Notification;

2.  Добавьте в App.xaml.cs следующий код:

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

    > [WACOM.NOTE]В этом учебнике мобильная служба отправляет на устройство всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале.

3.  В верхней части обработчика событий **Application\_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

    Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

4.  Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.

5.  В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, откройте вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID_CAP_PUSH_NOTIFICATION**.

    ![][]

    Это гарантирует, что приложение сможет создавать всплывающие уведомления.

## <span id="update-scripts"></span></a> Обновление серверных скриптов для отправки push-уведомлений

Наконец, необходимо обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem для отправки уведомлений.

1.  Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

    ![][1]

2.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

3.  Откройте вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

    > [WACOM.NOTE]Если вы проходите обучение с использованием мобильной службы предыдущей версии, в нижней части вкладки **Push-уведомления** может отображаться надпись **Включить улучшенные push-уведомления**. Щелкните ее, чтобы обновить мобильную службу для интеграции с центрами уведомлений. Это изменение нельзя отменить. Подробные указания по включению улучшенных push-уведомлений в рабочей мобильной службе см. в [этом руководстве][этом руководстве].

    ![][2]

    Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

    > [WACOM.NOTE]В этом учебнике MPNS используется в режиме без аутентификации. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув **Отправить** и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе [Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone][Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone].

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    > [WACOM.NOTE] При проверке в эмуляторе Windows Phone может возникнуть исключение 401 «Не авторизовано, RegistrationAuthorizationException». Это может произойти во время вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

2.  В приложении введите в текстовое поле слова «hello push», щелкните **Сохранить**, а затем сразу же щелкните кнопку запуска или кнопку «Назад», чтобы выйти из приложения.

    ![][3]

    При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Заметьте, что устройство получает всплывающее уведомление с текстом **hello push**.

    ![][4]

    > [WACOM.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получить всплывающее уведомление, пока приложение активно, необходимо обработать событие [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Магазина Windows возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

-   [Отправка push-уведомлений аутентифицированным пользователям][Отправка push-уведомлений аутентифицированным пользователям]
    <br/>. Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только аутентифицированным пользователям.

-   [Рассылка широковещательных уведомлений подписчикам][Рассылка широковещательных уведомлений подписчикам]
    <br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными (][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    <br/>Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
    <br/>Дополнительные сведения об использовании мобильных служб в .NET.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте о том, как реализовать бизнес-логику в мобильной службе.

<!-- Anchors.  Images.- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Приступая к работе с push-уведомлениями (в устаревшей версии)]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/
  [Обновление приложения: регистрация для получения уведомлений]: #update-app
  [Обновление серверных скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения push-уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data
  [приложения для Магазина Windows]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  []: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [этом руководстве]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Настройка веб-службы с аутентификацией для отправки push-уведомлений в Windows Phone]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [Отправка push-уведомлений аутентифицированным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users
  [Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
