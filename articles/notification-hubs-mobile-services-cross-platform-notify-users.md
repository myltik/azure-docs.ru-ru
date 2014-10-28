<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="Мобильные службы" class="current">Мобильные службы</a>
    <a href="/ru-ru/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>
</div>

В предыдущем учебнике под названием [Уведомление пользователей с помощью концентраторов уведомлений][Уведомление пользователей с помощью концентраторов уведомлений] вы научились отправлять push-уведомления на все устройства, зарегистрированные конкретным пользователем, прошедшим проверку. В этом учебнике для отправки уведомления на каждую поддерживаемую платформу клиента требовалось несколько запросов. Концентраторы уведомлений поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Это упрощает отправку кроссплатформенных уведомлений. В этом разделе показано, как использовать шаблоны для отправки в одном запросе независимых от платформы уведомлений, рассчитанных на все платформы. Дополнительные сведения о шаблонах см. в разделе [Обзор концентраторов уведомлений Windows Azure][Обзор концентраторов уведомлений Windows Azure].

<div class="dev-callout"><b>Примечание.</b>
    <p>Концентраторы уведомлений позволяют устройству зарегистрировать несколько шаблонов с одним и тем же тегом. В этом случае входящее сообщение, предназначенное для этого тега, приводит к отправке на устройство нескольких уведомлений, по одному для каждого шаблона. Это дает возможность отображения одного сообщения в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в Магазине Windows.</p>
</div>

Выполните следующие действия для отправки кроссплатформенных уведомлений с использованием шаблонов:

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

    ![][]

2.  Щелкните по вкладке **API**, после чего щелкните по записи **register\_notifications** в таблице API.

    ![][1]

3.  Щелкните по вкладке **Сценарий**, найдите блок **else**, создающий новую регистрацию, когда `existingRegs` принимает значение **false**, и замените его следующим кодом:

        else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }

    Этот код вызывает метод, используемый на конкретной платформе для регистрации шаблонов вместо собственной регистрации. Уже имеющиеся регистрации изменять не нужно, поскольку регистрация шаблонов является производной от собственной регистрации.

4.  Нажмите вкладку **Данные** и нажмите таблицу **TodoItem**.

    ![][2]

5.  В **todoitem** нажмите вкладку **Сценарий** и выберите **Вставить**, а затем замените существующую функцию **insert** следующим кодом:

    ![][3]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

6.  Замените функцию вставки следующим кодом:

        function insert(item, user, request) {
            var azure = require('azure');
            var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
            '<FULL_SAS_CONNECTION_STRING>');

            // Execute the request and send notifications.
            request.execute({
                success: function() {
                    // Write the default response and send a notification 
                    // to the user on all devices by using the userId tag.
                    request.respond();
                    // Create a template-based payload.
                    var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                    // Send a notification to the current user on all platforms. 
                    hub.send(user.userId, payload,  
                    function(error, outcome){
                        // Do something here with the outcome or error.
                    });     
                }
            });
        }

    Этот код отправляет уведомление одновременно для всех платформ без необходимости указания собственных полезных данных. Концентраторы уведомлений создают и доставляют правильные полезные данные для каждого устройства с указанным значением *тега* в соответствии с зарегистрированными шаблонами.

7.  Обновите сценарий, чтобы заменить *`<NOTIFICATION_HUB_NAME>`* и *`<FULL_SAS_CONNECTION_STRING>`* значениями для своего концентратора уведомлений, а затем нажмите кнопку **Сохранить**.

8.  Остановите эмулятор устройства или удалите с устройства существующее клиентское приложение.

    Это гарантирует, что клиентом служб мобильных устройств будет создан новый идентификатор установки. Если этого не сделать, служба пытается использовать существующую регистрацию не на базе шаблона.

9.  Снова разверните и запустите клиентское приложение и убедитесь, что регистрация прошла успешно и что отображается новый идентификатор регистрации.

10. Опять введите текст и добавьте новый элемент.

    Обратите внимание, что после завершения вставки приложение получает push-уведомление от концентраторов уведомлений.

11. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение и вставьте текст.

    Обратите внимание, что на каждом из устройств отображается уведомление.

## Дальнейшие действия

Теперь, когда вы завершили работу с данным учебником, вы можете узнать больше о концентраторах уведомлений и шаблонах в следующих разделах:

-   **Использование концентраторов уведомлений для передачи экстренных новостей ([Магазин Windows C#][Магазин Windows C#]/[iOS][Магазин Windows C#])**
    Демонстрация другого сценария для использования шаблонов

-   В разделе **[Обзор концентраторов уведомлений Azure][Обзор концентраторов уведомлений Windows Azure]**
    содержится более подробная информация о шаблонах.

-   **[Инструкции по использованию концентраторов уведомлений для магазина Windows][Инструкции по использованию концентраторов уведомлений для магазина Windows]**
    Включает ссылку на язык выражения шаблона.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Мобильные службы]: /ru-ru/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/ "Мобильные службы"
  [ASP.NET]: /ru-ru/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/ "ASP.NET"
  [Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users
  [Обзор концентраторов уведомлений Windows Azure]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Магазин Windows C#]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
  [Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927172.aspx
