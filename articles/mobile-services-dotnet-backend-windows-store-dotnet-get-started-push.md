<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной частью .NET для отправки push-уведомлений в универсальное приложение для Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью центров уведомлений Azure в универсальном проекте быстрого запуска для Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

> [WACOM.NOTE]В этом разделе показывается, как для приложения для Магазина Windows настроить push-уведомления с помощью службы уведомлений Windows (WNS). Вы можете использовать инструменты Visual Studio 2013, чтобы автоматически настроить те же push-уведомления в проекте приложения для Windows. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows][Универсальная версия приложения для Windows] этого учебника.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения с помощью WNS и настройка мобильных служб][Регистрация приложения с помощью WNS и настройка мобильных служб]
2.  [Обновление приложения: регистрация для получения уведомлений][Обновление приложения: регистрация для получения уведомлений]
3.  [Обновление сервера для отправки push-уведомлений][Обновление сервера для отправки push-уведомлений]
4.  [Включение push-уведомлений для локального тестирования][Включение push-уведомлений для локального тестирования]
5.  [Вставка данных для получения push-уведомлений][Вставка данных для получения push-уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе.

> [WACOM.NOTE]В этом разделе используются приложения для Магазина Windows Phone 8.1. Чтобы добавить push-уведомления в приложение для Windows Phone 8 или Windows Phone Silverlight 8.1, см. эту версию раздела [Приступая к работе с push-уведомлениями в мобильных службах][Приступая к работе с push-уведомлениями в мобильных службах].

## <span id="register"></span></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Мобильная служба и приложения теперь настроены для работы с WNS и центрами уведомлений. Далее вам будет нужно обновить универсальное приложение для Windows, чтобы зарегистрироваться для получения уведомлений.

## <span id="update-app"></span></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1.  В Visual Studio откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Добавьте следующий метод `InitNotificationAsync` в класс **App**, чтобы создать канал push-уведомлений и выполнить регистрацию для получения push-уведомлений:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Этот код получает URI канала (ChannelURI) для приложения из WNS, а затем регистрирует этот URI для push-уведомлений.

3.  В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

4.  В Visual Studio откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**. Сохраните файл.

    ![][0]

    Это гарантирует, что приложение сможет создавать всплывающие уведомления.

## <span id="update-server"></span></a> Обновление сервера для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике показаны основы включения в приложении для Магазина Windows возможностей отправки push-уведомлений с помощью мобильных служб и центров уведомлений. Далее полезно пройти следующий учебник [Отправка push-уведомлений аутентифицированным пользователям][Отправка push-уведомлений аутентифицированным пользователям]. Узнайте, как использовать теги для отправки push-уведомлений из мобильной службы только аутентифицированным пользователям.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными ][Приступая к работе с данными]

    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]

    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]

    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб в .NET.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Универсальная версия приложения для Windows]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Регистрация приложения с помощью WNS и настройка мобильных служб]: #register
  [Обновление приложения: регистрация для получения уведомлений]: #update-app
  [Обновление сервера для отправки push-уведомлений]: #update-server
  [Включение push-уведомлений для локального тестирования]: #local-testing
  [Вставка данных для получения push-уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Приступая к работе с push-уведомлениями в мобильных службах]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Отправка push-уведомлений аутентифицированным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
