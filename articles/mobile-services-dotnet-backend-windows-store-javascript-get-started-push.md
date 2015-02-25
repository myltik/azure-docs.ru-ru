<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр мобильных разработок" description="Узнайте, как использовать мобильные службы и центры уведомлений среды выполнения .NET Azure для отправки push-уведомлений в приложение JavaScript Магазина Windows." services="mobile-services, notification-hubs" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>


# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в приложение Магазина Windows. В этом учебнике объясняется включение push-уведомлений с помощью концентраторов уведомлений Azure в проекте быстрого запуска. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[AZURE.NOTE]В этом разделе показывается, как настроить push-уведомления для приложения Магазина Windows с помощью службы уведомлений Windows (WNS). Вы можете использовать инструменты Visual Studio 2013, чтобы автоматически настроить те же push-уведомления в проекте приложения для Windows. Дополнительные сведения см. в [версии этого учебника для универсального приложения Windows](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения с помощью WNS и настройка мобильных служб](#register)
2. [Обновление приложения для регистрации в целях получения уведомлений](#update-app)
3. [Обновление сервера для отправки push-уведомлений](#update-server)
4. [Включение push-уведомлений для локального тестирования](#local-testing)
5. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение. 

##<a id="register"></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Мобильная служба и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить приложение Магазина Windows для регистрации с целью получения уведомлений.

##<a id="update-app"></a> Обновление приложения для регистрации в целях получения уведомлений

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

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном проекте из краткого руководства.

##<a id="update-server"></a>Обновление сервера для отправки push-уведомлений


[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рекомендуется ознакомиться с одним из следующих учебников:

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

Просмотрите следующие разделы, посвященные мобильным службам и центрам уведомлений:

* [Приступая к работе с данными]
  <br/>Дополнительная информация о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительная информация об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе центров уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Руководство по устранению неполадок и решения по отладке центров уведомлений. 

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с приложениями JavaScript.

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/

[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
