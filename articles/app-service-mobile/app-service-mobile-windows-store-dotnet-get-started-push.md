<properties 
	pageTitle="Добавление push-уведомлений в универсальное приложение среды выполнения Windows 8.1 | Мобильные приложения Azure" 
	description="Узнайте, как использовать мобильные приложения службы приложений Azure и центры уведомлений Azure для отправки push-уведомлений в приложение для Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="glenga"/>

# Добавление push-уведомлений в универсальное приложение среды выполнения Windows 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом разделе показано, как отправлять push-уведомления в универсальное приложение среды выполнения Windows 8.1 с помощью мобильных приложений службы приложений Azure и центров уведомлений Azure. В этом сценарии при добавлении нового элемента серверная часть мобильного приложения отправляет push-уведомление всем приложениям для Windows, которые зарегистрированы в службе уведомлений Windows (WNS).

Этот учебник создан на основе краткого руководства по мобильным приложениям службы приложений. Перед началом работы с этим учебником необходимо изучить краткое руководство [Создание приложения Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934);
* Изучение [краткого руководства](../app-service-mobile-windows-store-dotnet-get-started.md).  



##<a name="create-gateway"></a>Создание концентратора уведомлений

Выполните следующие действия, чтобы создать новый центр уведомлений для обработки push-уведомлений. Если у вас уже есть центр в этой группе ресурсов, вам не нужно выполнять действия, описанные в этом разделе.

1. Войдите на [портал Azure]. Щелкните **Просмотреть все** > **Мобильные приложения** и выберите только что созданную серверную часть приложения. Щелкните **Параметры** > **Мобильный** > **Push-уведомления**. 

2. Выполните процесс создания центра уведомлений. Необходимо будет создать новое пространство имен, если оно отсутствует в текущей группе ресурсов. Щелкните **Создать** после настройки всех параметров.

Далее этот центр уведомлений будет использован для включения push-уведомлений для вашего приложения.

##Регистрация приложения для работы с push-уведомлениями

Прежде чем вы сможете отправлять push-уведомления в приложения для Windows из Azure, необходимо отправить приложение в Магазин Windows. Затем можно будет настроить серверный проект для интеграции с WNS.

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект приложения Магазина Windows, щелкните **Магазин**, а затем — **Связать приложение с Магазином…**. 

    ![Связь приложения с Магазином Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. В окне мастера щелкните **Далее**, выполните вход с помощью учетной записи Майкрософт, введите имя приложения в поле **Зарезервировать новое имя приложения** и нажмите кнопку **Зарезервировать**.

3. После успешного создания регистрации приложения выберите новое имя приложения, нажмите кнопку **Далее**, а затем нажмите кнопку **Связать**. Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

7. Повторите шаги 1 и 3 для проекта приложения Магазина Windows Phone, используя регистрацию, созданную ранее для приложения Магазина Windows.

7. Перейдите в [Центр разработки для Windows](https://dev.windows.com/ru-RU/overview), войдите с помощью учетной записи Майкрософт, щелкните «Регистрация нового приложения» в области **Мои приложения**, а затем разверните **Службы** > **Push-уведомления**.

8. На странице **Push-уведомления** в разделе **Мобильные службы Microsoft Azure** щелкните **Сайт служб Live**.

9. Запишите значения полей **Секрет клиента** и **ИД безопасности пакета** на вкладке **Параметры приложений**.

    ![Параметр приложения в центре разработчиков](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]Секрет клиента и ИД безопасности пакета — это важные учетные данные для безопасного доступа. Не сообщайте никому эти значения и не распространяйте их вместе со своим приложением.

##Настройка мобильного приложения для отправки push-уведомлений

1. Войдите на [портал Azure], выберите **Обзор** > **Мобильное приложение** > свое приложение > **Службы push-уведомлений**.

2. На странице **Служба уведомлений Windows** введите **ключ безопасности** (секрет клиента) и **ИД безопасности пакета**, полученные на сайте служб Live, а затем нажмите кнопку **Сохранить**.

Теперь серверная часть мобильного приложения настроена для работы с WNS.

##<a id="update-service"></a>Обновление сервера для отправки push-уведомлений

После включения push-уведомлений в приложении необходимо обновить внутренний сервер приложения так, чтобы он отправлял push-уведомления.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект и щелкните **Управление пакетами NuGet**, найдите `Microsoft.Azure.NotificationHubs` и нажмите кнопку **Установить**. Будет установлена клиентская библиотека центров уведомлений.

3. В серверном проекте откройте **Контроллеры** > **TodoItemController.cs** и добавьте следующие операторы using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. В метод **PostTodoItem** добавьте следующий код после вызова **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Этот код заставляет центр уведомлений отправить push-уведомление после вставки элемента задачи.


## <a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Добавление push-уведомлений в приложение

1. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**. 

    При этом отобразится диалоговое окно "Управление пакетами NuGet".

2. Выполните поиск пакета SDK для клиента мобильного приложения службы приложений для управляемых приложений, нажмите кнопку **Установить**, выберите все клиентские проекты в решении и примите условия использования.

    Будет выполнено скачивание, установка и добавление во все клиентские проекты ссылки на библиотеку мобильных push-уведомлений Azure для Windows.

3. Откройте общий файл проекта **App.xaml.cs** и добавьте следующие операторы `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. В том же файле добавьте следующее определение метода **InitNotificationsAsync** в класс **App**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Этот код возвращает ChannelURI для приложения из WNS, а затем регистрирует ChannelURI в мобильном приложении службы приложений.
    
5. В верхней части обработчика событий **OnLaunched** в файле **App.xaml.cs** добавьте в определение метода модификатор **async**, а в новый метод **InitNotificationsAsync** добавьте следующий вызов (как в приведенном ниже примере):

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Это обеспечит регистрацию кратковременного ChannelURI при каждом запуске приложения.

6. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

    В меню **Файл** выберите **Сохранить все**.

7. Для проекта приложения Магазина Windows Phone повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

##<a id="test"></a>Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

<!-- Anchors. -->

<!-- URLs. -->
[портал Azure]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO1-->