<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />


# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в универсальное приложение Windows. 
В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом разделе показано, как вручную настроить push-уведомления с помощью мобильных служб в проекте для Магазина Windows. Для добавления push-уведомлений в проект приложения для Магазина Windows можно использовать Visual Studio 2013. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data) этого учебника. 

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения с помощью WNS и настройка мобильных служб](#register)
2. [Обновление приложения: регистрация для получения уведомлений](#update-app)
3. [Обновление серверных скриптов для отправки push-уведомлений](#update-scripts)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создаст это подключение. 

>[WACOM.NOTE]В этом разделе используются приложения для Магазина Windows Phone 8.1. Сведения о том, как добавить push-уведомления в приложение Windows Phone 8 или Windows Phone Silverlight 8.1, см. в этой версии раздела [Приступая к работе с push-уведомлениями в мобильных службах](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push).

##<a id="register"></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

И мобильная служба, и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить универсальное приложение для Магазина Windows, чтобы выполнить регистрацию для получения уведомлений.

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующую инструкцию `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Add the following method to **App** class: 
	
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
    
4. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте следующий вызов нового метода **InitNotificationsAsync**:

        InitNotificationsAsync();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. Может потребоваться выполнять эту регистрацию в приложении только периодически, чтобы гарантировать, что регистрация актуальна. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Появится всплывающее диалоговое окно с ключом регистрации.
  
6. (Необязательно) Если вы не используете созданный порталом управления проект быстрого запуска, откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**.

   	![][2]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном учебном проекте.

##<a id="update-scripts"></a> Обновление серверных скриптов для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении для Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

+ [Отправка широковещательных уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.

+ [Отправка подписчикам уведомлений на основе шаблона]
	<br/>Использование шаблонов для отправки push-уведомлений из мобильной службы без создания зависящих от платформы полезных данных на сервере.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

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

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о способах реализации бизнес-логики в мобильной службе.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Отправка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Отправка подписчикам уведомлений на основе шаблона]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
