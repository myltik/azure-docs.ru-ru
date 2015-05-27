<properties 
	pageTitle="Добавление push-уведомлений в универсальное приложение Windows с помощью службы приложений Azure" 
	description="Использование службы приложений Azure для отправки push-уведомлений в универсальное приложение Windows." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="yuaxu"/>

# Добавление push-уведомлений в приложение Магазина Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

В этом разделе показано, как отправлять push-уведомления в универсальное приложение Windows с внутреннего сервера .NET, используя службу приложений Azure. По завершении вы отправите push-уведомления с внутреннего сервера .NET во все зарегистрированные универсальные приложения Windows при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений](#register)
2. [Настройка](#configure)
3. [Обновление службы для отправки push-уведомлений](#update-service)
4. [Добавление push-уведомлений в приложение](#add-push)
5. [Тестирование push-уведомлений в приложении](#test)

Этот учебник создан на основе краткого руководства по мобильным приложениям службы приложений. Прежде чем приступить к этому учебнику, необходимо сначала пройти учебник [Начало работы с мобильными приложениями службы приложений].

Для работы с этим учебником требуется:

* активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045);
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Регистрация приложения для получения push-уведомлений

Для отправки push-уведомлений в универсальные приложения в службе приложений Azure необходимо отправить приложение в Магазин Windows. Затем следует интегрировать учетные данные службы push-уведомлений мобильного приложения с WNS.

1. Если вы еще не зарегистрировали свое приложение, перейдите к разделу <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Отправить страницу приложения</a> в центре разработчиков для приложений для Магазина Windows, выполните вход с использованием своей учетной записи Майкрософт и затем щелкните **Имя приложения**.

    ![][0]

2. Введите имя приложения в поле **Имя приложения**, щелкните **Зарезервировать имя приложения** и затем щелкните **Сохранить**.

    ![][1]

    При этом создается новая регистрация в Магазине Windows для вашего приложения.

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

##<a id="configure"></a>Настройка мобильного приложения для отправки push-уведомлений

1. Войдите на [портал предварительной версии Azure], последовательно выберите **Обзор**, **Мобильное приложение**, а затем выберите свое приложение и откройте службы push-уведомлений.

2. В службе уведомлений Windows введите **секрет клиента** и **идентификатор безопасности пакета (SID)**, а затем сохраните эту настройку.

Мобильное приложение службы приложений теперь настроено для работы с WNS.

<!-- URLs. -->
[портал предварительной версии Azure]: https://portal.azure.com/

##<a id="update-service"></a>Обновление службы для отправки push-уведомлений

После включения push-уведомлений в приложении необходимо обновить внутренний сервер приложения так, чтобы он отправлял push-уведомления.

1. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**.

2. Выполните поиск **Microsoft.Azure.NotificationHubs** и нажмите кнопку **Установить** для всех проектов в решении.

3. В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильного внутреннего сервера. Откройте файл TodoItemController.cs. Добавьте следующие операторы `using` в начало файла:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

4. Добавьте следующий фрагмент в метод `PostTodoItem` после вызова **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

    Этот код запрашивает в концентраторе уведомлений, связанном с этим мобильным приложением, отправку push-уведомления после вставки элемента задачи.


## <a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Добавление push-уведомлений в приложение

1. В Visual Studio щелкните правой кнопкой мыши решение, а затем щелкните **Управление пакетами NuGet**. 

    При этом отобразится диалоговое окно "Управление пакетами NuGet".

2. Выполните поиск пакета SDK для клиента мобильного приложения службы приложений для управляемых приложений, нажмите кнопку **Установить**, выберите все проекты в решении и примите условия использования.

    При этом выполняется загрузка, установка и добавление во всех проектах ссылки на библиотеку мобильных push-уведомлений Azure для Windows.

3. Откройте файл проекта **App.xaml.cs** и добавьте следующие инструкции `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    В проекте универсального приложения этот файл находится в папке `<project_name>.Shared`.

4. В том же файле добавьте следующее определение метода **InitNotificationsAsync** в класс **App**:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Этот код возвращает ChannelURI для приложения из WNS, а затем регистрирует ChannelURI в мобильном приложении службы приложений.
    
5. В верхней части обработчика событий **OnLaunched** в файле **App.xaml.cs** добавьте следующий вызов нового метода **InitNotificationsAsync**:

        InitNotificationsAsync();

    Это обеспечит регистрацию кратковременного ChannelURI при каждом запуске приложения.

6. В обозревателе решений дважды щелкните файл **Package.appxmanifest** приложения для Магазина Windows и в разделе **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

    В меню **Файл** выберите **Сохранить все**.

7. (Необязательно.) Для проекта приложения для Windows Phone повторите предыдущий шаг.

8. Нажмите клавишу **F5**, чтобы запустить приложения.

Теперь приложение готово к получению всплывающих уведомлений.

##<a id="test"></a>Тестирование push-уведомлений в приложении

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
<!--HONumber=54-->