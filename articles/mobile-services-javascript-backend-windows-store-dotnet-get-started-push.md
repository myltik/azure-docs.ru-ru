<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в универсальное приложение для Windows.
В этом учебнике вам предстоит использовать push-уведомления с помощью центров уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

> [WACOM.NOTE]В этом разделе показано, как вручную настроить push-уведомления с помощью мобильных служб в проекте для Магазина Windows. Для добавления push-уведомлений в проект приложения для Магазина Windows можно использовать Visual Studio 2013. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows][Универсальная версия приложения для Windows] этого учебника.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения с помощью WNS и настройка мобильных служб][Регистрация приложения с помощью WNS и настройка мобильных служб]
2.  [Обновление приложения: регистрация для получения уведомлений][Обновление приложения: регистрация для получения уведомлений]
3.  [Обновление серверных скриптов для отправки push-уведомлений][Обновление серверных скриптов для отправки push-уведомлений]
4.  [Вставка данных для получения push-уведомлений][Вставка данных для получения push-уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение.

> [WACOM.NOTE]В этом разделе используются приложения для Магазина Windows Phone 8.1. Чтобы добавить push-уведомления в приложение для Windows Phone 8 или Windows Phone Silverlight 8.1, см. эту версию раздела [Приступая к работе с push-уведомлениями в мобильных службах][Приступая к работе с push-уведомлениями в мобильных службах].

## <span id="register"></span></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Мобильная служба и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить универсальное приложение для Магазина Windows, чтобы выполнить регистрацию для получения уведомлений.

## <span id="update-app"></span></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1.  В Visual Studio откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Добавьте в класс **App** следующий метод:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Этот код получает URI канала (ChannelURI) для приложения из WNS, а затем регистрирует этот URI для push-уведомлений.

3.  В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

4.  Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.

5.  (Дополнительно.) Если вы не используете созданный порталом управления проект быстрого запуска, откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**.

    ![][0]

    Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном проекте из краткого руководства.

## <span id="update-scripts"></span></a> Обновление серверных скриптов для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Магазина Windows возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

-   [Отправка push-уведомлений аутентифицированным пользователям][Отправка push-уведомлений аутентифицированным пользователям]
    <br/>. Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только аутентифицированным пользователям.

-   [Рассылка широковещательных уведомлений подписчикам][Рассылка широковещательных уведомлений подписчикам]
    <br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

-   [Отправка подписчикам уведомлений на основе шаблонов][Отправка подписчикам уведомлений на основе шаблонов]
    <br/>Узнайте о том, как использовать шаблоны для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

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

<!-- Anchors.   Images. URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Универсальная версия приложения для Windows]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Регистрация приложения с помощью WNS и настройка мобильных служб]: #register
  [Обновление приложения: регистрация для получения уведомлений]: #update-app
  [Обновление серверных скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения push-уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Приступая к работе с push-уведомлениями в мобильных службах]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Отправка push-уведомлений аутентифицированным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Отправка подписчикам уведомлений на основе шаблонов]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
