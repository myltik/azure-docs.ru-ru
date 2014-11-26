<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Приступая к работе с push-уведомлениями в мобильных службах с помощью Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Магазин Windows: C#">Магазин Windows: C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Магазин Windows: JavaScript" class="current">Магазин Windows: JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-ios" title="iOS" class="current">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a> 
</div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для Магазина Windows.
В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы push-уведомлений Windows (WNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

<div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике push-уведомления добавляются в приложение для Магазина Windows, созданное в Visual Studio 2012. Visual Studio 2013 предоставляет новые возможности, которые упрощают настройку push-уведомлений в приложении для Магазина Windows с помощью мобильных служб. Если вы используете Visual Studio 2013, см. раздел <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js">Приступая к работе с push-уведомлениями</a>.</p>
</div>

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения для push-уведомлений и конфигурация мобильных служб][Регистрация приложения для push-уведомлений и конфигурация мобильных служб]
2.  [Создание таблицы регистраций][Создание таблицы регистраций]
3.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
4.  [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
5.  [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

-   Microsoft Visual Studio 2012 Express для Windows 8.
-   Активная учетная запись Магазина Windows.

Это учебник основан на учебнике [Приступая к работе с данными][Приступая к работе с данными]. Перед началом работы с этим учебником необходимо изучить [этот учебник][Приступая к работе с данными].

## <a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для Магазина Windows

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить мобильную службу на интеграцию с WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Мобильная служба и приложение теперь настроены для работы с WNS. Далее нужно создать таблицу для хранения регистраций.

## <a name="create-table"></a>Создание новой таблицы

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение

1.  Откройте файл default.js и вставьте следующий фрагмент кода в перегрузку метода **app.OnActivated** сразу после метода **args.setPromise**:

        // Get the channel for the application.
        var channel;
        var channelOperation = Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (newChannel) {
                channel = newChannel;
            });

    Этот код получает и сохраняет канал push-уведомлений каждый раз, когда запускается приложение.

2.  Вставьте следующий код после кода, который создает экземпляр **MobileServiceClient**:

        // Insert the new channel URI into the Registrations table.
        var registrationsTable = client.getTable('Registrations');
        registrationsTable.insert({ handle: channel.uri });         

    Этот код вставляет текущий канал в таблицу «Registrations».

3.  Откройте файл Package.appxmanifest и убедитесь, что на вкладке **Пользовательский интерфейс приложения** для параметра **Всплывающие уведомления** установлено значение **Да**.

    ![][0]

    Это гарантирует, что приложение сможет создавать всплывающие уведомления.

## <a name="update-scripts"></a><span class="short-header">Обновление сценария вставки</span>Обновление зарегистрированного сценария вставки на портале управления

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Последовательно щелкните **TodoItem**, **Скрипт** и **Вставить**.

    ![][1]

2.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Этот скрипт вставки отправляет push-уведомление (с текстом вставленного элемента) по всем каналам, хранящимся в таблице **Регистрации**.

## <a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении

1.  В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2.  В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

    ![][2]

    Обратите внимание, что после завершения вставки приложение получает push-уведомление из WNS.

    ![][3]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

-   [Приступая к работе с центрами уведомлений][Приступая к работе с центрами уведомлений]
    Узнайте, как использовать центры уведомлений в приложении для Магазина Windows.

-   [Рассылка уведомлений подписчикам][Рассылка уведомлений подписчикам]
    Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

-   [Рассылка уведомлений пользователям][Рассылка уведомлений пользователям]
    Узнайте о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

-   [Отправка пользователям уведомлений между различными платформами][Отправка пользователям уведомлений между различными платформами]
    Узнайте об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    Узнайте больше о регистрации и использовании серверных сценариев.

-   [Справочник принципов использования мобильных служб HTML/JavaScript][Справочник принципов использования мобильных служб HTML/JavaScript]
    Узнайте больше о том, как использовать мобильные службы с HTML и JavaScript.

 
 


  [Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
  [Создание таблицы регистраций]: #create-table
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
  [Приступая к работе с центрами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
  [Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
  [Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/
