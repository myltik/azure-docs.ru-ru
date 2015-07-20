<properties
	pageTitle="Приступая к работе с центрами уведомлений Azure"
	description="Узнайте, как использовать центры уведомлений Azure для push-уведомлений."
	services="notification-hubs"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="06/09/2015"
	ms.author="wesmc"/>

# Приступая к работе с центрами уведомлений

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Обзор

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение для Магазина Windows или Windows Phone 8.1 (без Silverlight). Если вы намерены использовать Windows Phone 8.1 Silverlight, см. версию статьи для [Windows Phone](notification-hubs-windows-phone-get-started.md). В этом учебнике вам предстоит создать пустое приложение Магазина Windows, получающее push-уведомления с помощью службы push-уведомлений Windows (WNS). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью концентратора уведомлений.

В этом учебнике описывается простой сценарий вещания с использованием концентраторов уведомлений. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании концентраторов уведомлений для охвата определенных пользователей и групп устройств.


##Предварительные требования

Для работы с данным учебником требуется следующее:

+ Microsoft Visual Studio Express 2013 для Windows с обновлением 2<br/>Эта версия Visual Studio необходима для создания проекта универсального приложения. Для создания приложения для Магазина Windows требуется Visual Studio 2012 Express для Windows 8.

+ Активная учетная запись Магазина Windows.

+ Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными концентраторам уведомлений для приложений Магазина Windows.

##Регистрация приложения для Магазина Windows

Чтобы отправлять push-уведомления в приложения Магазина Windows из мобильных служб, необходимо отправить приложение в Магазин Windows. После этого необходимо настроить концентратор уведомлений на интеграцию с WNS.

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Отправить страницу приложения</a> в центре разработчиков для приложений для Магазина Windows, выполните вход с использованием своей учетной записи Майкрософт и затем щелкните **Имя приложения**.

   	![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

   	![][1]

   	При этом создается новая регистрация в Магазине Windows для вашего приложения.

3. Создайте в Visual Studio новый проект с помощью шаблона **Пустое приложение** из списка Visual C# Store Apps.

   	![][2]

4. В обозревателе решений щелкните правой кнопкой мыши проект приложения для Магазина Windows, щелкните **Магазин** и затем щелкните **Связать приложение с Магазином...**.

   	![][3]

   	Откроется мастер **Свяжите свое приложение с Магазином Windows**.

5. В окне мастера щелкните **Вход** и затем войдите в систему, используя свою учетную запись Майкрософт.

6. Выберите приложение, зарегистрированное на шаге 2, щелкните **Далее** и затем щелкните **Связать**.

   	![][4]

   	Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

7. (Необязательно.) Для проекта приложения для Windows Phone повторите шаги с 4 по 6.

7. На странице Центра разработчиков Windows для нового приложения щелкните **Службы**.

   	![][5]

8. На странице **Службы** в разделе **Мобильные службы Microsoft Azure** щелкните **Сайт служб Live**.

   	![][17]

9. Откройте вкладку **Параметры приложений** и запишите значения полей **Секрет клиента** и **Идентификатор безопасности пакета**.

   	![][6]

 	> [AZURE.NOTE]**Примечание о безопасности.** Секрет клиента и идентификатор безопасности пакета — это важные учетные данные для безопасного доступа. Не сообщайте никому эти значения и не распространяйте их вместе со своим приложением.

##Настройка концентратора уведомлений

1. Войдите на [портал управления Azure] и щелкните **СОЗДАТЬ** в нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Сервисная шина**, **Концентратор уведомлений** и **Быстрое создание**.

   	![][7]

3. Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][8]

4. Выберите только что созданное пространство имен (обычно это ***имя центра уведомлений*-ns**) и щелкните расположенную сверху вкладку **Настройка**.

   	![][9]

5. Откройте расположенную сверху вкладку **Центры уведомлений** и выберите только что созданный центр уведомлений.

   	![][10]

6. Перейдите на расположенную сверху вкладку **Настройка**, введите значения **Секрет клиента** и **Идентификатор безопасности пакета**, полученные из WNS в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][11]

7. Откройте вкладку **Панель мониторинга** вверху и нажмите кнопку **Сведения о подключении** внизу страницы. Запишите две строки подключения.

   	![][12]

Концентратор уведомлений теперь настроен для работы с WNS, и у вас есть строки подключения, чтобы зарегистрировать приложение и отправлять уведомления.

##Подключение приложения к концентратору уведомлений

1. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**.

	При этом отобразится диалоговое окно "Управление пакетами NuGet".

2. Выполните поиск по запросу `WindowsAzure.Messaging.Managed` и щелкните **Установить**, выберите все проекты решения и примите условия использования.

	![][20]

	После этого будет выполнено скачивание и установка всех проектов, а также добавление во все проекты ссылок на библиотеку обмена сообщениями Azure для Windows с помощью <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">пакета WindowsAzure.Messaging.Managed NuGet</a>.

3. Откройте файл проекта App.xaml.cs и добавьте следующие инструкции `using`. В проекте универсального приложения этот файл находится в папке `<project_name>.Shared`.

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	

4. Кроме того, в файле App.xaml.cs добавьте определение метода **InitNotificationsAsync** в класс **App**:

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

    >[AZURE.NOTE]Обязательно замените заполнитель "имя концентратора" на имя концентратора уведомлений, отображаемое на вкладке **Концентраторы уведомлений** портала (например, в предыдущем примере это **mynotificationhub2**). Кроме того, замените заполнитель строки подключения на строку подключения **DefaultListenSharedAccessSignature**, полученную в предыдущем разделе.

5. В верхней части обработчика событий **OnLaunched** в файле App.xaml.cs добавьте в новый метод **InitNotificationsAsync** следующий вызов:

        InitNotificationsAsync();

    Это обеспечит регистрацию ChannelURI в центре уведомлений при каждом запуске приложения.

6. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows, и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**:

   	![][18]

   	В меню **Файл** выберите **Сохранить все**.

7. (Необязательно.) Для проекта приложения для Windows Phone повторите предыдущий шаг.

8. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.

   	![][19]

9. (Необязательно.) Для запуска другого проекта повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

##Отправка уведомления из серверной части

Уведомления можно отправлять с помощью концентраторов уведомлений из любого серверного компонента с помощью <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейса REST</a>. В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Примеры отправки уведомлений из серверных компонентов мобильных служб Azure, интегрированных с центрами уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Серверная часть .NET](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [Серверная часть JavaScript](../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)). Примеры отправки уведомлений с использованием API REST см. в статье **Использование центров уведомлений из Java или PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Щелкните правой кнопкой мыши решение, выберите **Добавить** и **Новый проект...**. После этого в разделе **Visual C#** последовательно выберите **Windows** и **Консольное приложение**, а затем нажмите кнопку **OK**.

   	![][13]

	При этом в решение добавляется новое консольное приложение Visual C#. Это можно сделать также и в отдельном решении.

4. В Visual Studio последовательно выберите **Сервис**, **Диспетчер пакетов Nuget** и **Консоль диспетчера пакетов**.

	В результате в Visual Studio откроется консоль диспетчера пакетов.

6. В окне консоли установите свойство **Проект по умолчанию** для вашего нового проекта консольного приложения, после чего в окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus

	В результате будет добавлена ссылка на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">пакета WindowsAzure.ServiceBus NuGet</a>.

5. Откройте файл Program.cs и добавьте следующий оператор `using`:

        using Microsoft.ServiceBus.Notifications;

6. Добавьте в класс **Program** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Обязательно замените заполнитель «hub name» на имя центра уведомлений, отображаемое на вкладке **Центры уведомлений** портала. Кроме того, замените заполнитель строки подключения на строку подключения с именем **DefaultFullSharedAccessSignature**, полученную в разделе «Настройка центра уведомлений».

	>[AZURE.NOTE]Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. У строки с доступом к прослушиванию отсутствуют разрешения для отправки уведомлений.

7. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

8. Щелкните проект консольного приложения в Visual Studio правой кнопкой мыши и выберите **Назначить запускаемым проектом**. Нажмите клавишу **F5**, чтобы запустить консольное приложение.

   	![][14]

	На всех зарегистрированных устройствах будет получено всплывающее уведомление. Если щелкнуть заголовок уведомления или коснуться его, приложение будет загружено.

В таких разделах MSDN, как [каталог всплывающих уведомлений], [каталог плиток] и [обзор эмблем], можно найти всю полезную информацию по данной теме.

##Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на все устройства Windows. Чтобы сориентироваться на определенных пользователей, см. учебник [Использование концентраторов уведомлений для отправки push-уведомлений пользователям]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительную информацию об использовании центров уведомлений см. в [руководстве по использованию центров уведомлений].



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
[портал управления Azure]: https://manage.windowsazure.com/
[руководстве по использованию центров уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx

[Использование концентраторов уведомлений для отправки push-уведомлений пользователям]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[каталог всплывающих уведомлений]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[каталог плиток]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[обзор эмблем]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 

<!---HONumber=July15_HO2-->