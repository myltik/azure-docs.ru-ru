<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Приступая к работе с концентраторами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать концентраторы уведомлений Windows Azure для отправки push-уведомлений в приложение Sliverlight для Windows Phone 8 или Windows Phone 8.1. Если вы используете Windows Phone 8.1 (без Silverlight), см. версию [Windows Universal][1].
В этом материале для изучения вы создадите пустое приложение для Windows Phone 8, которое получает push-уведомления при помощи службы push-уведомлений Майкрософт (MPNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

> [AZURE.NOTE] Концентраторы уведомлений Windows Phone SDK не поддерживают использование WNS с приложениями Windows Phone 8.1 Silverlight. Чтобы использовать WNS (вместо MPNS) с приложениями Windows Phone 8.1 Silverlight, вам необходимо настроить учетные данные WNS, как это показано в разделе [Начало работы с Windows Universal][1]. После этого их можно зарегистрировать со стороны внутренней службы, как это показано в учебных материалах разделов [Уведомление пользователей][Уведомление пользователей], или использовать [API концентраторов уведомлений для REST][API концентраторов уведомлений для REST].

В этом учебнике рассматриваются следующие шаги для включения push-уведомлений:

1.  [Создание концентратора уведомлений][Создание концентратора уведомлений]
2.  [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
3.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. Для работы с данным учебником требуется следующее:

-   [Microsoft Visual Studio 2012 Express для Windows Phone][Microsoft Visual Studio 2012 Express для Windows Phone] или более поздняя версия.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Windows Phone 8.

<div class="dev-callout"><strong>Примечание.</strong> <p>Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.</p></div>

## <a name="configure-hub"></a>Создание концентратора уведомлений

1.  Войдите на портал [управления Windows Azure][управления Windows Azure] и щелкните элемент **+СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Концентратор уведомлений** и **Быстрое создание**.

    ![][0]

3.  Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

    ![][2]

4.  Выберите недавно созданное пространство имен (обычно это ***имя концентратора уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

    ![][3]

5.  Откройте расположенную сверху вкладку **Концентраторы** уведомлений и выберите недавно созданный концентратор уведомлений.

    ![][4]

6.  Щелкните расположенный внизу элемент **Сведения о подключении**. Запишите значения двух строк подключения.

    ![][5]

7.  Откройте вкладку **Настройка**, а затем установите флажок **Включить push-уведомления без проверки подлинности** в разделе **Параметры уведомлений Windows Phone**.

    ![][6]

Теперь у вас есть строки подключения, чтобы зарегистрировать приложение Windows Phone 8 и отправлять уведомления.

<div class="dev-callout"><b>Примечание.</b>
        <p>В этом учебнике используется MPNS в режиме без проверки подлинности. Режим MPNS без проверки подлинности налагает ограничения на использование уведомлений, отправляемых для каждого канала. Концентраторы уведомлений поддерживают <a href="http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/ff941099(v=vs.105).aspx">режим работы MPNS с проверкой подлинности</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>Подключение приложения к концентратору уведомлений

1.  В Visual Studio создайте новое консольное приложение Windows Phone 8.

    ![][7]

    В Visual Studio 2013 с обновлением 2 вместо этого вы создаете приложение Windows Phone Silverlight.

    ![][8]

2.  В Visual Studio щелкните правой кнопкой по решению, после чего щелкните **Управление пакетами NuGet**.

    При этом отобразится диалоговое окно "Управление пакетами NuGet".

3.  Выполните поиск `WindowsAzure.Messaging.Managed` и щелкните **Установить**, после чего примите условия использования.

    ![][9]

    После этого будет выполнена загрузка, установка и добавление ссылки на библиотеку Azure Messaging для Windows при помощи [пакета WindowsAzure.Messaging.Managed NuGet][пакета WindowsAzure.Messaging.Managed NuGet].

4.  Откройте файл App.xaml.cs и добавьте следующие инструкции `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  При возникновении следующего кода в верхней части метода **Application\_Launching** в App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Обязательно вставьте имя концентратора и строку подключения с именем **DefaultListenSharedAccessSignature**, которое вы получили в предыдущем разделе.
    Этот код извлечет ChannelURI для приложения из MPNS, после чего зарегистрирует этот ChannelURI в концентраторе уведомлений. Он также гарантирует регистрацию ChannelURI в концентраторе уведомлений при каждом запуске приложения.

    > [WACOM.NOTE]В этом учебнике на устройство отправляется всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале. Чтобы поддерживать одновременно и всплывающие и мозаичные уведомления, следует вызвать оба метода — **BindToShellTile** и **BindToShellToast**.

6.  В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, откройте вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID\_CAP\_PUSH\_NOTIFICATION**.

    ![][10]

    Это гарантирует, что приложение сможет получать push-уведомления.

7.  Нажмите клавишу F5, чтобы запустить приложение.

    Отобразится сообщение о регистрации сообщения.

## <a name="send"></a>Отправка уведомлений из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В этом учебном материале описана отправка уведомлений при помощи консольного приложения .NET. Чтобы ознакомиться с примером отправки уведомлений из серверной части мобильных служб Azure, интегрированной с концентраторами уведомлений, см. раздел **Начало работы с push-уведомлениями в мобильных службах** ([Серверная часть .NET][Серверная часть .NET] | [Серверная часть JavaScript][Серверная часть .NET]). Чтобы ознакомиться с примером отправки уведомлений при помощи REST API, см. раздел **Использование концентраторов уведомлений из Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Щелкните правой кнопкой решение, выберите **Добавить**, затем **Новый проект...**, затем в разделе **Visual C#** щелкните **Windows** и **Консольное приложение**, после чего щелкните **OK**.

    ![][11]

    При этом в решение добавляется новое консольное приложение Visual C#. Это можно также сделать отдельным решением.

2.  Щелкните правой кнопкой мыши, затем **Сервис**, затем щелкните **Диспетчер пакетов библиотеки**, после чего щелкните **Консоль диспетчера пакетов**.

    При этом отобразится консоль диспетчера пакетов.

3.  В окне консоли установите свойство **Проект по умолчанию** для вашего нового проекта консольного приложения, после чего в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

    Выполнение этого действия добавит ссылку на SDK служебной шины Azure с [пакетом WindowsAzure.ServiceBus NuGet][пакетом WindowsAzure.ServiceBus NuGet].

4.  Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5.  В классе **Программа** добавьте следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала. Также замените заполнитель строки подключения строкой подключения с именем **DefaultFullSharedAccessSignature**, которую вы получили в разделе "Настройка концентратора уведомлений".

    > [WACOM.NOTE]Убедитесь, что вы используете строку подключения с **полным** доступом, а не доступом к **прослушиванию**. Строка доступа на прослушивание не обладает разрешениями на отправку уведомлений.

6.  Затем добавьте следующую строку в метод Main:

         SendNotificationAsync();
         Console.ReadLine();

7.  Когда эмулятор Windows Phone будет работать, а приложение будет закрыто, установите проект приложения консоли в качестве начального проекта по умолчанию, после чего нажмите клавишу F5, чтобы запустить приложение.

    Вы получите всплывающее уведомление. Приложение загружается при касании этого всплывающего баннера.

В [каталоге всплывающих уведомлений][каталоге всплывающих уведомлений] и [каталоге уведомлений на плитке][каталоге уведомлений на плитке] MSDN можно найти самые разнообразные полезные данные.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows Phone 8. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании концентраторов уведомлений см. в [руководстве по использованию концентраторов уведомлений][руководстве по использованию концентраторов уведомлений].



  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [1]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Уведомление пользователей]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [API концентраторов уведомлений для REST]: http://msdn.microsoft.com/ru-ru/library/dn223264.aspx
  [Создание концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Отправка уведомлений из серверной части]: #send
  [Microsoft Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [управления Windows Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [пакета WindowsAzure.Messaging.Managed NuGet]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ru-ru/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [пакетом WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [каталоге всплывающих уведомлений]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [каталоге уведомлений на плитке]: http://msdn.microsoft.com/ru-ru/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [руководстве по использованию концентраторов уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
