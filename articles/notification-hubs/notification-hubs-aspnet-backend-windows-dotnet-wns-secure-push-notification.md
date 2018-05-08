---
title: Безопасные push-уведомления для концентраторов уведомлений Azure
description: Узнайте, как отправлять безопасные push-уведомления в Azure. Примеры кода написаны на C# с использованием API .NET.
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 8d051107a5e114ed8aa5f4b5a629a439519157b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-notification-hubs-secure-push"></a>Безопасные push-уведомления посредством центров уведомлений Azure
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Обзор
Поддержка push-уведомлений в Microsoft Azure позволяет получить доступ к простой в использовании, многоплатформенной и масштабируемой инфраструктуре для отправки push-уведомлений, которая значительно упрощает реализацию push-уведомлений как для индивидуальных пользователей, так и для корпоративных приложений для мобильных платформ.

Из-за ограничений, связанных с правовыми нормами или обеспечением безопасности, иногда в уведомлении могут присутствовать данные, которые нельзя передать через стандартную инфраструктуру push-уведомлений. В этом учебнике рассказывается о том, как реализовать этот принцип при отправке важной информации через защищенное соединение с проверкой подлинности, установленное между устройством клиента и серверной частью приложения.

На высоком уровне поток можно представить следующим образом.

1. Серверная часть приложения:
   * Сохраняет полезную нагрузку в базе данных серверной части.
   * Отправляет идентификатор этого уведомления устройству (защищаемые сведения не передаются).
2. Приложение на устройстве при получении уведомления:
   * Устройство связывается с серверной частью и запрашивает полезную нагрузку.
   * Приложение может показывать полезную нагрузку в виде уведомления на устройстве.

Стоит отметить: в предыдущем потоке (и в этом учебнике) мы предположили, что устройство сохраняет маркер проверки подлинности в локальном хранилище после входа пользователя. Таким образом обеспечивается удобство работы, так как с помощью этого маркера устройство способно получать безопасные полезные данные уведомлений. Если приложение не сохраняет маркеры проверки подлинности на устройстве, или если истек срок действия маркеров, приложение устройства, после получения уведомления, должно отобразить общее уведомление, предлагая пользователю запустить приложение. Затем приложение выполняет проверку подлинности пользователя и отображает полезную нагрузку уведомления.

В этом учебнике по безопасности push-уведомлений показано, как безопасно отправлять push-уведомление. Данное руководство является продолжением другого учебника под названием [Уведомление пользователей](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , поэтому необходимо сначала выполнить шаги в указанном учебнике.

> [!NOTE]
> В этом учебнике подразумевается, что вы создали и настроили центр уведомлений в соответствии с описанием в руководстве [Приступая к работе с центрами уведомлений (Магазин Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Кроме того, обратите внимание, что для Windows Phone 8.1 требуются учетные данные Windows (не Windows Phone) и что фоновые задачи не работают на Windows Phone 8.0 и в Silverlight 8.1. При работе в приложениями из Магазина Windows уведомления можно получать через фоновую задачу только в том случае, если включен экран блокировки приложения (установите флажок в Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Изменение проекта для Windows Phone
1. В проекте **NotifyUserWindowsPhone** добавьте следующий код в App.xaml.cs, чтобы зарегистрировать фоновую задачу push-уведомления. В конце метода `OnLaunched()` добавьте следующую строку кода:
   
        RegisterBackgroundTask();
2. Находясь в App.xaml.cs, добавьте следующий код сразу после метода `OnLaunched()` :
   
        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();
   
                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }
3. Добавьте в начало файла App.xaml.cs следующие операторы `using` :
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. В меню **Файл** Visual Studio выберите **Сохранить все**.

## <a name="create-the-push-background-component"></a>Создайте фоновый компонент push-уведомления
Следующий шаг заключается в создании фонового компонента push-уведомления.

1. В обозревателе решений щелкните правой кнопкой мыши узел верхнего уровня решения (в данном случае — **Solution SecurePush**), а затем нажмите кнопку **Добавить**, после чего щелкните **Создать проект**.
2. Разверните пункт **Приложения Магазина**, затем щелкните **Приложения Windows Phone** и выберите **Компонент среды выполнения Windows (Windows Phone)**. Присвойте проекту имя **PushBackgroundComponent**, а затем нажмите кнопку **ОК**, чтобы создать проект.
   
    ![][12]
3. В обозревателе решений щелкните правой кнопкой мыши проект **PushBackgroundComponent (Windows Phone 8.1)**, а затем щелкните **Добавить** и выберите **Класс**. Присвойте новому классу имя **PushBackgroundTask.cs**. Щелкните кнопку **Добавить** , чтобы создать класс.
4. Замените все содержимое определения пространства имен **PushBackgroundComponent** следующим кодом, заменив заполнитель `{back-end endpoint}` конечной точкой серверной части, полученной при развертывании серверной части:
   
        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }
   
            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";
   
                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;
   
                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);
   
                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);
   
                    ShowToast(notification);
   
                    deferral.Complete();
                }
   
                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }
5. В обозревателе решений щелкните правой кнопкой мыши проект **PushBackgroundComponent (Windows Phone 8.1)**, а затем щелкните **Управление пакетами NuGet**.
6. В левой части окна выберите **В сети**.
7. В текстовом поле **Поиск** введите **Клиент HTTP**.
8. В списке результатов выберите **Клиентские библиотеки Microsoft HTTP** и нажмите **Установить**. Выполните установку.
9. Вернитесь к полю NuGet **Поиск** и введите **Json.net**. Установите пакет **Json.NET** , затем закройте окно диспетчера пакетов NuGet.
10. Добавьте следующие операторы `using` в начало файла **PushBackgroundTask.cs** :
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. В обозревателе решений в проекте **NotifyUserWindowsPhone (Windows Phone 8.1)** щелкните правой кнопкой мыши **Ссылки**, а затем щелкните **Добавить ссылку...**. В диалоговом окне "Диспетчер ссылок" установите флажок **PushBackgroundComponent**, а затем нажмите кнопку **ОК**.
12. В обозревателе решений дважды щелкните **Package.appxmanifest** в проекте **NotifyUserWindowsPhone (Windows Phone 8.1)**. В поле **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.
    
    ![][3]
13. Находясь в **Package.appxmanifest**, откройте меню **Объявления** вверху. В раскрывающемся списке **Доступные объявления** щелкните **Фоновые задачи** и затем нажмите кнопку **Добавить**.
14. В **Package.appxmanifest** в разделе **Свойства** включите параметр **Push-уведомление**.
15. В **Package.appxmanifest** в области **Параметры приложения** введите **PushBackgroundComponent.PushBackgroundTask** в поле **Точка входа**.
    
    ![][13]
16. В меню **Файл** выберите **Сохранить все**.

## <a name="run-the-application"></a>Запуск приложения
Для запуска приложения выполните следующие действия:

1. В Visual Studio запустите приложение веб-API **AppBackend** . Отобразится веб-страница ASP.NET.
2. В Visual Studio запустите приложение **NotifyUserWindowsPhone (Windows Phone 8.1)** для Windows Phone. Эмулятор Windows Phone запустится и автоматически загрузит приложение.
3. В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** введите имя пользователя и пароль. Это могут быть любые совпадающие строки.
4. В пользовательском интерфейсе приложения **NotifyUserWindowsPhone** щелкните **Log in and register** (Вход и регистрация). Затем нажмите **Отправить push-уведомление**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
