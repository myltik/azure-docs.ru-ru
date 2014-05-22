<properties pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как применять мобильные службы Azure и концентраторы уведомлений для отправки push-уведомлений в приложение Магазина Windows." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors="glenga" solutions="" manager="" editor="" />


# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Магазин Windows — C#" class="current">Магазин Windows — C#</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Магазин Windows — JavaScript">Магазин Windows — JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Серверная версия .NET">Серверная версия .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/"  title="Серверная версия JavaScript" class="current">Серверная версия JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение магазина Windows. 
В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление с использованием концентраторов уведомлений каждый раз при вставке записи. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом учебнике показана интеграция мобильных служб с концентраторами уведомлений, которая в настоящее время находится на этапе предварительной версии. По умолчанию отправка push-уведомлений с помощью концентраторов уведомлений из серверной части JavaScript не включена.  После создания нового концентратора уведомлений процесс интеграции необратим. Push-уведомления для iOS и Android в настоящее время доступны только при использовании поддержки push-уведомлений по умолчанию, которая описана в [этой версии раздела](/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения с помощью WNS и настройка мобильных служб](#register)
2. [Обновление приложения: регистрация для получения уведомлений](#update-app)
3. [Обновление серверных скриптов для отправки push-уведомлений](#update-scripts)
3. [Вставка данных для получения push-уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение. 

##<a id="register"></a> Регистрация приложения с помощью WNS и настройка мобильных служб

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

Мобильная служба и приложение теперь настроены для работы с WNS и концентраторами уведомлений. Далее потребуется обновить приложение Магазина Windows для регистрации с целью получения уведомлений.

##<a id="update-app"></a> Обновление приложения: регистрация для получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Добавьте в класс **App** следующий метод: 
	
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
    
4. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации. 

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
  
6. (Дополнительно) Если вы не используете созданный порталом управления проект быстрого запуска, откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** задано значение **Да**.

   	![][2]

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления. Эти уведомления уже включены в загруженном проекте из краткого руководства.

##<a id="update-scripts"></a> Обновление серверных скриптов для отправки push-уведомлений

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	![][13]

   	Обратите внимание, что после завершения вставки приложение получает push-уведомление из WNS.

   	![][14]

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


[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Пакет Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Справочник принципов использования мобильных служб .NET]: /ru-ru/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library

