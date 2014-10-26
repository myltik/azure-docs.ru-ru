<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Приступая к работе с центрами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение для Магазина Windows или Windows Phone 8.1 (без Silverlight). Если целевой ОС является Windows Phone 8.1 Silverlight, см. версию для [Windows Phone][1].
В этом учебнике вам предстоит создать пустое приложение для Магазина Windows, получающее push-уведомления с помощью службы push-уведомлений Windows (WNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения для получения push-уведомлений][Регистрация приложения для получения push-уведомлений]
2.  [Настройка концентратора уведомлений][Настройка концентратора уведомлений]
3.  [Подключение приложения к концентратору уведомлений][Подключение приложения к концентратору уведомлений]
4.  [Отправка уведомлений из серверной части][Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств. Для работы с данным учебником требуется следующее:

-   Microsoft Visual Studio Express 2013 для Windows с обновлением 2
    Эта версия Visual Studio необходима для создания проекта универсального приложения. Для создания приложения для Магазина Windows требуется Visual Studio 2012 Express для Windows 8.

-   Активная учетная запись Магазина Windows.

-   Активная учетная запись Azure.
    Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Магазина Windows.

## <a name="register"></a>Регистрация приложения для Магазина Windows

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить концентратор уведомлений на интеграцию с WNS.

1.  Если вы еще не зарегистрировали свое приложение, перейдите к разделу [Отправить страницу приложения][Отправить страницу приложения] в центре разработчиков для приложений для Магазина Windows, выполните вход с использованием своей учетной записи Майкрософт и затем щелкните **Имя приложения**.

    ![][]

2.  Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

    ![][2]

    При этом создается новая регистрация в Магазине Windows для вашего приложения.

3.  Создайте в Visual Studio новый проект с помощью шаблона **Пустое приложение** из списка Visual C# Store Apps.

    ![][3]

4.  В обозревателе решений щелкните правой кнопкой мыши проект приложения для Магазина Windows, щелкните **Магазин** и затем щелкните **Связать приложение с Магазином...**.

    ![][4]

    Откроется мастер **Свяжите свое приложение с Магазином Windows**.

5.  В окне мастера щелкните **Вход** и затем войдите в систему, используя свою учетную запись Майкрософт.

6.  Выберите приложение, зарегистрированное на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

    ![][5]

    Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

7.  (Необязательно.) Для проекта приложения для Windows Phone повторите шаги с 4 по 6.

8.  На странице Центра разработчиков Windows для нового приложения щелкните **Службы**.

    ![][6]

9.  На странице **Службы** в разделе **Мобильные службы Microsoft Azure** щелкните **Сайт служб Live**.

    ![][7]

10. Откройте вкладку **Параметры приложений** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**.

    ![][8]

    <div class="dev-callout"><b>Примечание о безопасности</b>
<p>Секрет клиента и ИД безопасности пакета &mdash; это важные учетные данные для безопасного доступа. Не сообщайте никому эти значения и не распространяйте их вместе со своим приложением.</p>
</div>

## <a name="configure-hub"></a>Настройка концентратора уведомлений

1.  Войдите на [портал управления Azure][портал управления Azure] и щелкните **СОЗДАТЬ** в нижней части экрана.

2.  Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Центр уведомлений** и **Быстро создать**.

    ![][9]

3.  Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

    ![][10]

4.  Выберите только что созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

    ![][11]

5.  Откройте расположенную сверху вкладку **Центры уведомлений** и выберите только что созданный центр уведомлений.

    ![][12]

6.  Перейдите на расположенную сверху вкладку **Настройка**, введите значения **Секрет клиента** и **Идентификатор безопасности пакета**, полученные из WNS в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

    ![][13]

7.  Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Сведения о подключении**. Запишите значения двух строк подключения.

    ![][14]

Концентратор уведомлений теперь настроен для работы с WNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

## <a name="connecting-app"></a>Подключение приложения к концентратору уведомлений

1.  В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**.

    Откроется диалоговое окно «Управление пакетами NuGet».

2.  Выполните поиск `WindowsAzure.Messaging.Managed` и щелкните **Установить**, выберите все проекты решения и примите условия использования.

    ![][15]

    После этого будет выполнено скачивание и установка всех проектов, а также добавление во все проекты ссылок на библиотеку обмена сообщениями Azure для Windows с помощью[пакета NuGet WindowsAzure.Messaging.Managed][пакета NuGet WindowsAzure.Messaging.Managed].

3.  Откройте файл проекта App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    В проекте универсального приложения этот файл находится в папке `<project_name>.Shared`.

4.  Кроме того, в файле App.xaml.cs добавьте определение метода **InitNotificationsAsync** в класс **App**:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
            var result = await hub.RegisterNativeAsync(channel.Uri);
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Этот код получает универсальный код ресурса (URI) канала (ChannelURI) для приложения из WNS, а затем регистрирует этот универсальный код ресурса (URI) канала в центре уведомлений.

    > [WACOM.NOTE]Обязательно замените заполнитель «hub name» на имя центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала (например, в предыдущем примере это **mynotificationhub2**). Кроме того, замените заполнитель строки подключения на строку подключения **DefaultListenSharedAccessSignature**, полученную в предыдущем разделе.

5.  В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это обеспечит регистрацию ChannelURI в центре уведомлений при каждом запуске приложения.

6.  В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows, и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**:

    ![][16]

    В меню **Файл** выберите **Сохранить все**.

7.  (Необязательно.) Для проекта приложения для Windows Phone повторите предыдущий шаг.

8.  Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.

    ![][17]

9.  (Необязательно.) Для запуска другого проекта повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

## <a name="send"></a>Отправка уведомления из серверной части

Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью [интерфейса REST][интерфейса REST]. В этом учебнике уведомления отправляются с помощью консольного приложения .NET. Примеры отправки уведомлений из серверных компонентов мобильных служб Azure, интегрированных с центрами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Серверная часть .NET][Серверная часть .NET] | [Серверная часть JavaScript][Серверная часть .NET]). Примеры отправки уведомлений с использованием API REST см. в статье **Использование центров уведомлений из Java или PHP** ([Java][Java] | [PHP][PHP]).

1.  Щелкните правой кнопкой мыши решение, выберите **Добавить** и **Новый проект...**. После этого в разделе **Visual C#** последовательно выберите **Windows** и **Консольное приложение**, а затем нажмите кнопку **OK**.

    ![][18]

    В результате к решению будет добавлено новое консольное приложение Visual C#. Это можно сделать также и в отдельном решении.

2.  Щелкните правой кнопкой мыши, последовательно выберите **Средства**, **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**.

    Откроется консоль диспетчера пакетов.

3.  В окне консоли определите свой новый проект как **Проект по умолчанию**, а затем в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

    В результате с помощью [пакета NuGet WindowsAzure.ServiceBus][пакета NuGet WindowsAzure.ServiceBus] будет добавлена ссылка на пакет SDK для Azure Service Bus.

4.  Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Добавьте в класс **Program** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Обязательно замените заполнитель «hub name» на имя центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала. Кроме того, замените заполнитель строки подключения на строку подключения с именем **DefaultFullSharedAccessSignature**, полученную в разделе «Настройка центра уведомлений».

    > [WACOM.NOTE]Обратите внимание, что используемая строка подключения имеет **полный** доступ, а не доступ к **прослушиванию**. У строки с доступом к прослушиванию отсутствуют разрешения для отправки уведомлений.

6.  Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Определите консольное приложение как запускаемый проект и запустите его, нажав клавишу **F5**.

    ![][19]

    На всех зарегистрированных устройствах будет получено всплывающее уведомление. Если щелкнуть заголовок уведомления или коснуться его, приложение будет загружено.

В таких разделах MSDN, как [каталог всплывающих уведомлений][каталог всплывающих уведомлений], [каталог плиток][каталог плиток] и [обзор эмблем][обзор эмблем], можно найти всю полезную информацию по данной теме.

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям][Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей][Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании центров уведомлений см. в [руководстве по использованию центров уведомлений][руководстве по использованию центров уведомлений].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Universal]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ru-ru/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ru-ru/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/
  [Регистрация приложения для получения push-уведомлений]: #register
  [Настройка концентратора уведомлений]: #configure-hub
  [Подключение приложения к концентратору уведомлений]: #connecting-app
  [Отправка уведомлений из серверной части]: #send
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [портал управления Azure]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [пакета NuGet WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [интерфейса REST]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn223264.aspx
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /ru-ru/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ru-ru/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [пакета NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [каталог всплывающих уведомлений]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh761494.aspx
  [каталог плиток]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh761491.aspx
  [обзор эмблем]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh779719.aspx
  [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
  [Использование концентраторов уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [руководстве по использованию центров уведомлений]: http://msdn.microsoft.com/ru-ru/library/jj927170.aspx
