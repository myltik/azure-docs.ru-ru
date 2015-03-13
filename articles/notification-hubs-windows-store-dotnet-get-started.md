<properties 
	pageTitle="Приступая к работе с центрами уведомлений Azure" 
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>

# Приступая к работе с центрами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение для Магазина Windows или Windows Phone 8.1 (без Silverlight). Если вы намерены использовать Windows Phone 8.1 Silverlight, см. версию статьи для [Windows Phone](/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/). 
В этом учебнике вам предстоит создать пустое приложение Магазина Windows, получающее push-уведомления с помощью службы push-уведомлений Windows (WNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений]
2. [Настройка центра уведомлений]
3. [Подключение приложения к центру уведомлений]
4. [Отправка уведомлений из серверной части]

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании центров уведомлений для охвата определенных пользователей и групп устройств. Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio Express 2013 для Windows с обновлением 2<br/>Эта версия Visual Studio требуется для создания проекта универсального приложения. Для создания приложения для Магазина Windows требуется Visual Studio 2012 Express для Windows 8.

+ Активная учетная запись Магазина Windows.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными центрам уведомлений для приложений Магазина Windows. 

##<a name="register"></a>Регистрация приложения для Магазина Windows

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить центр уведомлений на интеграцию с WNS.

1. Если вы еще не зарегистрировали свое приложение, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">страницу "Отправить приложение"</a> в Центре разработки для приложений Магазина Windows, войдите с использованием вашей учетной записи Майкрософт, а затем щелкните **Имя приложения**.

   	![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![][1]

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. Создайте в Visual Studio новый проект с помощью шаблона **Пустое приложение** из списка Visual C# Store Apps.

   	![][2]

4. В обозревателе решений щелкните правой кнопкой мыши проект приложения для Магазина Windows, щелкните **Магазин** и затем щелкните **Связать приложение с Магазином...**. 

   	![][3]

   	Откроется мастер **Связь приложений с Магазином Windows**.

5. В окне мастера щелкните **Вход** и войдите в систему с использованием учетной записи Майкрософт.

6. Выберите приложение, зарегистрированное на шаге 2, нажмите кнопку **Далее**, а затем щелкните **Связать**.

   	![][4]

   	При этом в манифест приложения будут добавлены необходимые регистрационные данные Магазина Windows. 

7. (Необязательно) Для проекта приложения для Windows Phone повторите шаги с 4 по 6.  

7. На странице Центра разработчиков Windows для нового приложения щелкните **Службы**. 

   	![][5] 

8. На странице **Службы** щелкните **Сайт служб Live** в поле **Мобильные службы Microsoft Azure**.

   	![][17]

9. На вкладке **Параметры приложения** запишите значения элементов **Секрет клиента** и **Идентификатор безопасности пакета (SID)**. 

   	![][6]

 	> [AZURE.NOTE] **Примечание о безопасности**
	Секрет клиента и ИД безопасности пакета - это важные учетные данные для безопасного доступа. Не сообщайте никому эти значения и не распространяйте их вместе со своим приложением.

##<a name="configure-hub"></a>Настройка центра уведомлений

1. Войдите на [портал управления Azure] и щелкните пункт **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Центр уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый центр уведомлений**.

   	![][8]

4. Выберите недавно созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][9]

5. Выберите расположенную сверху вкладку **Центры уведомлений** и выберите недавно созданный центр уведомлений.

   	![][10]

6. Перейдите на расположенную сверху вкладку **Настройка**, введите значения **Секрет клиента** и **Идентификатор безопасности пакета**, полученные из WNS в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][11]

7. Выберите расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Информация о подключении**. Запишите значения двух строк подключения.

   	![][12]

Центр уведомлений теперь настроен для работы с WNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

##<a name="connecting-app"></a>Подключение приложения к центру уведомлений

1. В Visual Studio щелкните правой кнопкой по решению, после чего щелкните **Управление пакетами NuGet**. 

	При этом отобразится диалоговое окно "Управление пакетами NuGet".

2. Выполните поиск `WindowsAzure.Messaging.Managed` и щелкните **Установить**, выберите все проекты решения и примите условия использования. 

	![][20]

	После этого будет выполнено скачивание и установка всех проектов, а также добавление во все проекты ссылок на библиотеку обмена сообщениями Azure для Windows с помощью <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета WindowsAzure.Messaging.Managed NuGet</a>. 

3. Откройте файл проекта App.xaml.cs и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	В проекте универсального приложения этот файл находится в папке <имя_проекта>.Shared.

4. Кроме того, в файле App.xaml.cs добавьте следующее определение метода **InitNotificationsAsync** в класс **App**.
	
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

    >[AZURE.NOTE]Обязательно замените заполнитель "имя центра" именем центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала (например, в предыдущем примере это **mynotificationhub2**): Кроме того, замените заполнитель строки подключения на строку подключения **DefaultListenSharedAccessSignature**, полученную в предыдущем разделе.
    
5. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это обеспечит регистрацию ChannelURI в центре уведомлений при каждом запуске приложения.

6. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows, в поле **Уведомления** установите для парарметра **Всплывающие уведомления** значение **Да**.

   	![][18]

   	В меню **Файл** выберите **Сохранить все**.

7. (Необязательно.) Для проекта приложения для Windows Phone повторите предыдущий шаг.

8. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
   
   	![][19]

9. (Необязательно.) Для запуска другого проекта повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

##<a name="send"></a>Отправка уведомления из серверной части

Уведомления можно отправлять с помощью центров уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Пример отправки уведомлений из серверной части мобильных служб Azure, интегрированной с помощью центра уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Серверная часть .NET](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)) | [Серверная часть JavaScript](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)).  Чтобы ознакомиться с примером отправки уведомлений при помощи REST API, см. раздел **Использование центров уведомлений из Java/PHP** ([Java](/ru-ru/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ru-ru/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Щелкните правой кнопкой решение, выберите **Добавить**, затем **Новый проект...**, затем в разделе **Visual C#** щелкните **Windows** и **Консольное приложение**, после чего щелкните **ОК**. 

   	![][13]

	При этом в решение добавляется новое консольное приложение Visual C#. Это можно также сделать отдельным решением. 

4. Щелкните правой кнопкой мыши, затем **Сервис**, затем щелкните **Диспетчер пакетов библиотеки**, после чего щелкните **Консоль диспетчера пакетов**. 

	Отобразится консоль диспетчера пакетов.

6. В окне консоли установите свойство **Проект по умолчанию** для вашего нового проекта консольного приложения, после чего в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
	После этого будет добавлена ссылка на пакет SDK для Azure Service Bus с помощью пакета NuGet <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

5. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

6. В классе **Program** добавьте следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Обязательно замените заполнитель "имя центра" именем центра уведомлений, отображаемым на вкладке **Центры уведомлений** портала: Замените заполнитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка центра уведомлений". 

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не с доступом для **прослушивания**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

7. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

8. Определите консольное приложение как запускаемый проект и запустите его, нажав клавишу **F5**. 

   	![][14]

	На всех зарегистрированных устройствах будет получено всплывающее уведомление. Если щелкнуть заголовок уведомления или коснуться его, приложение будет загружено.

Вы можете найти любую полезную информацию о поддержке в разделах MSDN в [каталоге всплывающих уведомлений], [каталоге плиток] и [обзоре эмблем].

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows. Чтобы ориентироваться на определенных пользователей, см. учебник [Использование центров уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование центров уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании центров уведомлений см. в [руководстве по использованию центров уведомлений].

<!-- Anchors. -->
[Регистрация приложения для получения push-уведомлений]: #register
[Настройка центра уведомлений]: #configure-hub
[Подключение приложения к центру уведомлений]: #connecting-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[Push-уведомления для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Руководство по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx

[Использование центров уведомлений для отправки push-уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Использование центров уведомлений для передачи экстренных новостей]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet

[каталог всплывающих уведомлений]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[каталог уведомлений на плитке]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[обзор эмблем]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!--HONumber=45--> 
