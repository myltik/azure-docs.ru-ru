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
	ms.date="11/10/2015" 
	ms.author="glenga"/>

# Добавление push-уведомлений в универсальное приложение среды выполнения Windows 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом разделе показано, как отправлять push-уведомления в универсальное приложение среды выполнения Windows 8.1 с помощью мобильных приложений службы приложений Azure и центров уведомлений Azure. В этом сценарии при добавлении нового элемента серверная часть мобильного приложения отправляет push-уведомление всем приложениям для Windows, которые зарегистрированы в службе уведомлений Windows (WNS).

Этот учебник создан на основе краткого руководства по мобильным приложениям службы приложений. Перед началом работы с этим учебником необходимо изучить краткое руководство [Создание приложения Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934);
* Изучение [краткого руководства](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Создание концентратора уведомлений

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Регистрация приложения для работы с push-уведомлениями

Прежде чем вы сможете отправлять push-уведомления в приложения для Windows из Azure, необходимо отправить приложение в Магазин Windows. Затем можно будет настроить серверный проект для интеграции с WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Настройка мобильного приложения для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Обновление сервера для отправки push-уведомлений

После включения push-уведомлений в приложении необходимо обновить внутренний сервер приложения так, чтобы он отправлял push-уведомления.

1. В Visual Studio щелкните правой кнопкой мыши серверный проект и щелкните **Управление пакетами NuGet**, найдите `Microsoft.Azure.NotificationHubs` и нажмите кнопку **Установить**. Будет установлена клиентская библиотека центров уведомлений.

2. В серверном проекте откройте **Контроллеры** > **TodoItemController.cs** и добавьте следующие операторы using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. В метод **PostTodoItem** добавьте следующий код после вызова **InsertAsync**:

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

1. Откройте общий файл проекта **App.xaml.cs** и добавьте следующие операторы `using`:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. В том же файле добавьте следующее определение метода **InitNotificationsAsync** в класс **App**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Этот код возвращает ChannelURI для приложения из WNS, а затем регистрирует ChannelURI в мобильном приложении службы приложений.
    
3. В верхней части обработчика событий **OnLaunched** в файле **App.xaml.cs** добавьте в определение метода модификатор **async**, а в новый метод **InitNotificationsAsync** добавьте следующий вызов (как в приведенном ниже примере):

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Это обеспечит регистрацию кратковременного ChannelURI при каждом запуске приложения.

4. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

    В меню **Файл** выберите **Сохранить все**.

5. Для проекта приложения Магазина Windows Phone повторите предыдущий шаг.

Теперь приложение готово к получению всплывающих уведомлений.

##<a id="test"></a>Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##<a id="more"></a>Дополнительные сведения

* Шаблоны обеспечивают гибкость при отправке push-уведомлений локально и между различными платформами. В статье [Как использовать управляемый клиент для мобильных приложений Azure](app-service-mobile-dotnet-how-to-use-client-library.md) описано, как зарегистрировать шаблоны.
* Теги позволяют отправлять push-уведомления клиентам в зависимости от их сегмента. В статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) показано, как добавлять теги для установки устройства.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1125_2015--->