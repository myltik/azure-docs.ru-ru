<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Магазин Windows C#" >Магазин Windows C#</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
    <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a>
</div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение для Windows Phone 8.
В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы push-уведомлений Майкрософт (MPNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]Указания в этом разделе приведены с учетом использования *имеющихся* мобильных служб, которые *пока еще не были обновлены* до версии, поддерживающей интеграцию с центрами уведомлений. При создании *новой* мобильной службы интегрированные функции включаются автоматически. Информацию о работе с мобильными службами последних версий см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].
>
> Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и лучшее масштабирование. *Если это возможно, обновите имеющиеся мобильные службы, чтобы использовать центры уведомлений*. После обновления ознакомьтесь с этой версией раздела [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Создание таблицы регистраций][Создание таблицы регистраций]
2.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
3.  [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
4.  [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Для работы с учебником требуется [Visual Studio 2012 Express для Windows Phone][Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

> [WACOM.NOTE]При отправке более 500 сообщений на пользователя в день следует использовать центры уведомлений. Дополнительные сведения см. в разделе [Приступая к работе с концентраторами уведомлений][Приступая к работе с концентраторами уведомлений].

## <a name="create-table"></a>Создание новой таблицы

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение

1.  В Visual Studio откройте файл проекта MainPage.xaml.cs и добавьте следующий код, который создает новый класс **Регистрации**:

        public class Registrations
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    Для хранения URI канала используется свойство Handle.

2.  Откройте файл App.xaml.cs и добавьте следующий оператор "using":

        using Microsoft.Phone.Notification;

3.  Добавьте в App.xaml.cs следующий код:

        public static HttpNotificationChannel CurrentChannel { get; private set; }
        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    Этот код получает и сохраняет канал для подписки на push-уведомления, а также привязывает их к плитке приложения по умолчанию.

    <div class="dev-callout"><b>Примечание.</b>
    <p>В этом учебнике мобильная служба отправляет на устройство уведомление перевернутой плитки. При отправке всплывающего уведомления вместо этого необходимо вызвать для канала метод <strong>BindToShellToast</strong>. Чтобы поддерживались одновременно и всплывающие уведомления, и уведомления плиток, следует вызвать оба метода &mdash; <strong>BindToShellTile</strong> и <strong>BindToShellToast</strong>. </p>
</div>

4.  В верхней части обработчика событий **Application\_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

    Это гарантирует, что свойство **CurrentChannel** инициализируется при каждом запуске приложения.

5.  В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, откройте вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID___CAP___PUSH_NOTIFICATION**.

    ![][0]

    Это гарантирует, что приложение сможет получать push-уведомления.

## <a name="update-scripts"></a><span class="short-header">Обновление сценария вставки</span>Обновление зарегистрированных сценариев вставки на портале управления

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
                            push.mpns.sendFlipTile(registration.handle, {
                                title: item.text
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

1.  В Visual Studio выберите **Развернуть решение** в меню **Сборка**.

2.  В эмуляторе проведите пальцем влево, чтобы отобразить список установленных приложений и найти новое приложение **TodoList**.

3.  Нажмите и удерживайте значок приложения, а затем выберите в контекстном меню команду **Закрепить в меню пуска**.

    ![][2]

    В результате в меню "Пуск" будет закреплена плитка с именем **TodoList**.

4.  Коснитесь плитки с именем **TodoList**, чтобы запустить приложение.

    ![][3]

5.  В приложении введите в текстовом поле текст "Первое push-уведомление" и нажмите кнопку **Сохранить**.

    ![][4]

    При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента.

6.  Нажмите клавишу **Пуск**, чтобы вернуться в меню "Пуск".

    ![][5]

    Обратите внимание, что приложение получило push-уведомление и теперь на плитке отображается текст **Первое push-уведомление**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

-   [Приступая к работе с центрами уведомлений][Приступая к работе с центрами уведомлений]
	<br/>Узнайте, как использовать центры уведомлений в приложении для Магазина Windows.

-   [Что такое центры уведомлений?][Что такое центры уведомлений?]
	<br/>Узнайте, как создавать уведомления и доставлять их пользователям на нескольких платформах.

-   [Рассылка уведомлений подписчикам][Рассылка уведомлений подписчикам]
	<br/>Узнайте, как пользователи могут регистрироваться и получать push-уведомления с учетом категорий, которые им интересны.

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с данными][Приступая к работе с данными]
	<br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
	<br/>Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
	<br/>Узнайте больше о регистрации и использовании серверных сценариев.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]
	<br/>Дополнительные сведения об использовании мобильных служб в .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Создание таблицы регистраций]: #create-table
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-wp8
  [Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  [0]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [Приступая к работе с центрами уведомлений]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-wp8/
  [Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library/
