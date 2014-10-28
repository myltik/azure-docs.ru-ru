<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Регистрация текущего пользователя для push-уведомлений мобильной службы

<div class="dev-center-tutorial-selector sublanding">

[Магазин Windows C#][Магазин Windows C#][iOS][iOS]

</div>

В этом разделе показано, как запросить регистрацию push-уведомления с помощью концентраторов уведомлений Azure, когда регистрация выполняется с помощью мобильных служб Azure. Этот раздел расширяет учебник [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений]. Чтобы создать прошедшую проверку подлинности мобильную службу, вы должны завершить требуемые действия в этом учебнике. Дополнительные сведения о сценарии уведомления пользователей см. в учебнике [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений].

1.  Откройте в Visual Studio 2012 Express для Windows 8 проект, созданный при завершении необходимого учебника [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией].

2.  В обозревателе решений щелкните правой кнопкой мыши проект, щелкните **Магазин** и затем щелкните **Связать приложение с магазином...**.

    ![][]

    Откроется мастер **Связь приложений с Магазином Windows**.

3.  В окне мастера щелкните **Вход** и затем войдите в систему с учетной записью Майкрософт.

4.  Выберите приложение, которое зарегистрировано на шаге [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений], щелкните **Далее** и затем **Связать**.

    ![][1]

    Это добавляет необходимые регистрационные данные Магазина Windows в манифест приложения.

    <div class="dev-callout">

    **Примечание.**
    При этом для этого приложения мобильных служб повторно используется регистрация Магазина Windows из приложения учебника "Концентраторы уведомлений". Это может помешать получению уведомлений приложения учебника "Концентраторы уведомлений".

    </div>

5.  В окне "Обозреватель решений" дважды щелкните файл проекта Package.appxmanifest, чтобы открыть его в редакторе Visual Studio.

6.  Выполните прокрутку вниз до элемента **Все активы изображений** и щелкните **Эмблема**. В поле **Уведомления** установите для параметра **Всплывающие уведомления** значение **Да**.

    ![][2]

    Это позволяет этому приложению учебника "Мобильные службы" получать всплывающие уведомления.

7.  В Visual Studio откройте файл MainPage.xaml.cs и добавьте следующий код, который определяет классы **NotificationRequest** и **RegistrationResult**:

        public class NotificationRequest
        {
            public string channelUri { get; set; }
            public string platform { get; set; }
        }

        public class RegistrationResult
        {
            public string RegistrationId { get; set; }
            public string ExpirationTime { get; set; }
        }

    Эти классы будут содержать тело запроса и идентификатор регистрации, возвращенный при вызове настраиваемого интерфейса API соответственно.

8.  Добавьте в класс **MainPage** следующий метод:

        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Этот метод создает канал для push-уведомлений и отправляет его, вместе с типом устройства, методу настраиваемого интерфейса API, который создает регистрацию в концентраторах уведомлений. Этот метод настраиваемого интерфейса API был определен в учебнике [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений].

9.  Добавьте следующую строку кода в метод **OnNavigatedTo** сразу после вызова метода **Authenticate**:

        await RegisterNotification();

    <div class="dev-callout">

    **Примечание.**
    Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

    </div>

Теперь, когда клиентское приложение было обновлено, вернитесь к учебнику [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений] и обновите мобильную службу для отправки уведомлений с помощью концентраторов уведомлений.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Магазин Windows C#"
  [iOS]: /ru-ru/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS"
  [Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users
  [Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
  []: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
  [1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
  [2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png
