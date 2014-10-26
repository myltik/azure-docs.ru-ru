<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в приложение Магазина Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

> [WACOM.NOTE]В этом разделе показывается, как для приложения для Магазина Windows настроить push-уведомления с помощью службы уведомлений Windows (WNS). Вы можете использовать инструменты Visual Studio 2013, чтобы автоматически настроить те же push-уведомления в проекте приложения для Windows. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows][Универсальная версия приложения для Windows] этого учебника.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения с помощью WNS и настройка мобильных служб][Регистрация приложения с помощью WNS и настройка мобильных служб]
2.  [Обновление приложения: регистрация для получения уведомлений][Обновление приложения: регистрация для получения уведомлений]
3.  [Обновление сервера для отправки push-уведомлений][Обновление сервера для отправки push-уведомлений]
4.  [Включение push-уведомлений для локального тестирования][Включение push-уведомлений для локального тестирования]
5.  [Вставка данных для получения push-уведомлений][Вставка данных для получения push-уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение.

## <span id="register"></span></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][mobile-services-notification-hubs-register-windows-store-app]]

Мобильная служба и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить приложение Магазина Windows для регистрации с целью получения уведомлений.

## <span id="update-app"></span></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1.  Откройте файл default.js и вставьте приведенный ниже код после кода, который создает экземпляр **MobileServiceClient**. Этот код создает канал push-уведомлений и выполняет регистрацию для получения push-уведомлений.

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    Этот код получает URI канала (ChannelURI) для приложения из WNS, а затем регистрирует этот URI для push-уведомлений. Если выполнить регистрацию не удалось, в диалоговом окне сообщений отображается сообщение об ошибке.

2.  В Visual Studio откройте файл Package.appxmanifest и убедитесь, что на вкладке **Интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**.

    ![][]

    Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном проекте из краткого руководства.

## <span id="update-server"></span></a> Обновление сервера для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push][mobile-services-dotnet-backend-update-server-push]]

## <span id="local-testing"></span></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-windows-store-test-push][mobile-services-windows-store-test-push]]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Магазина Windows возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

-   [Отправка push-уведомлений аутентифицированным пользователям][Отправка push-уведомлений аутентифицированным пользователям]   
    Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только аутентифицированным пользователям.

Просмотрите следующие разделы, посвященные мобильным службам и центрам уведомлений:

-   [Приступая к работе с данными][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]

    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]

    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]

    Узнайте больше о том, как использовать мобильные службы с приложениями на JavaScript.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Универсальная версия приложения для Windows]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [Регистрация приложения с помощью WNS и настройка мобильных служб]: #register
  [Обновление приложения: регистрация для получения уведомлений]: #update-app
  [Обновление сервера для отправки push-уведомлений]: #update-server
  [Включение push-уведомлений для локального тестирования]: #local-testing
  [Вставка данных для получения push-уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Отправка push-уведомлений аутентифицированным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
