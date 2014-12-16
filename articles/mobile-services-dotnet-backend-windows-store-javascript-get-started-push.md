<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />


# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в приложение Магазина Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом разделе показывается, как для приложения из Магазина Windows настроить push-уведомления с помощью службы уведомлений Windows (WNS). Вы можете использовать инструменты Visual Studio 2013, чтобы автоматически настроить те же push-уведомления в проекте приложения для Windows. Дополнительную информацию см. в разделе [Универсальная версия приложения для Windows](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push) этого учебника.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения с помощью WNS и настройка мобильных служб](#register)
2. [Обновление приложения: регистрация для получения уведомлений](#update-app)
3. [Обновление сервера для отправки push-уведомлений](#update-server)
4. [Включение push-уведомлений для локального тестирования](#local-testing)
5. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создаст это подключение. 

##<a id="register"></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

И мобильная служба, и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить приложение для Магазина Windows, чтобы зарегистрировать его с целью получения уведомлений.

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. Откройте файл default.js и вставьте приведенный ниже код после кода, который создает экземпляр **MobileServiceClient**. Этот код создает канал push-уведомлений и выполняет регистрацию для получения push-уведомлений.

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

2. В Visual Studio откройте файл Package.appxmanifest и убедитесь, что для параметра **Всплывающие уведомления** задано значение **Да** на вкладке **Пользовательский интерфейс приложения**.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном учебном проекте.

##<a id="update-server"></a> Обновление сервера для отправки push-уведомлений


[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении для Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

Просмотрите следующие разделы, посвященные мобильным службам и центрам уведомлений:

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений. 

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с приложениями JavaScript.

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/

[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
