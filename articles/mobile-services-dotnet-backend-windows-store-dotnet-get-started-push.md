<properties pageTitle="Приступая к работе с концентраторами push-уведомлений при использовании мобильных служб среды выполнения .NET" description="Узнайте, как использовать мобильные службы и центры уведомлений Azure для отправки push-уведомлений в приложение Магазина Windows." services="mobile-services, notification-hubs" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal"/>


# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией .NET для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью центров уведомлений Azure в универсальном проекте быстрого запуска для Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части .NET с помощью концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[AZURE.NOTE]В этом разделе показывается, как настроить push-уведомления для приложения Магазина Windows с помощью службы уведомлений Windows (WNS). Вы можете использовать инструменты Visual Studio 2013, чтобы автоматически настроить те же push-уведомления в проекте приложения для Windows. О том, как это сделать, читайте в [версии этого учебника для универсального приложения Windows](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push) .

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения с помощью WNS и настройка мобильных служб](#register)
2. [Обновление приложения для регистрации в целях получения уведомлений](#update-app)
3. [Обновление сервера для отправки push-уведомлений](#update-server)
4. [Включение push-уведомлений для локального тестирования](#local-testing)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. 

>[AZURE.NOTE]В этом разделе используются приложения для Магазина Windows Phone 8.1. Сведения о том, как добавить push-уведомления в приложение Windows Phone 8 или Windows Phone Silverlight 8.1, см. в этой версии раздела [Приступая к работе с push-уведомлениями в мобильных службах](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

##<a id="register"></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Мобильная служба и приложения теперь настроены для работы с WNS и концентраторами уведомлений. Далее вам будет нужно обновить универсальное приложение для Windows, чтобы зарегистрироваться для получения уведомлений.

##<a id="update-app"></a> Обновление приложения для регистрации в целях получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующие операторы `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Добавьте следующий метод `InitNotificationAsync` в класс **App**, чтобы создать канал push-уведомлений и выполнить регистрацию для получения push-уведомлений: 
	
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
    
3. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации. 

4. В Visual Studio откройте файл Package.appxmanifest и убедитесь, что для параметра **Всплывающие уведомления** задано значение **Да** на вкладке **Пользовательский интерфейс приложения**. Сохраните файл.

   	![][1]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. 

##<a id="update-server"></a>Обновление сервера для отправки push-уведомлений

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Включение push-уведомлений для локального тестирования

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложении Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Рассылка push-уведомлений проверенным пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только тем пользователям, которые прошли проверку подлинности.

<!--+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

+ [Рассылка широковещательных уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений по шаблону подписчикам]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.
-->
Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Дополнительная информация о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительная информация об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе центров уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Руководство по устранению неполадок и решения по отладке центров уведомлений. 

* [Справочник по принципам использования мобильных служб .NET]
  <br/>Дополнительные сведения об использовании мобильных служб с .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Рассылка уведомлений по шаблону подписчикам]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Справочник по принципам использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
