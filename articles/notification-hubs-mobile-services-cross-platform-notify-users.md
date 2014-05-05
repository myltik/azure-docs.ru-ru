<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="уведомление пользователей мобильных служб xplat" pageTitle="Отправка кросcплатформенных уведомлений для пользователей с концентраторами уведомлений (мобильными службами)" metaKeywords="" description="Как использовать концентраторы уведомлений для отправки в одном запросе уведомлений, предназначенных для всех платформ." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Отправка кроссплатформенных уведомлений для пользователей с концентраторами уведомлений" authors="glenga" solutions="" manager="" editor="" />

# Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="Мобильные службы" class="current">Мобильные службы</a>
    <a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET">ASP.NET</a>
</div> 

В предыдущем учебнике под названием [Уведомление пользователей с помощью концентраторов уведомлений] вы научились отправлять push-уведомления на все устройства, зарегистрированные конкретным пользователем, прошедшим проверку. В этом учебнике для отправки уведомления на каждую поддерживаемую платформу клиента требовалось несколько запросов. Концентраторы уведомлений поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Это упрощает отправку кроссплатформенных уведомлений. В этом разделе показано, как использовать шаблоны для отправки в одном запросе независимых от платформы уведомлений, рассчитанных на все платформы. Дополнительные сведения о шаблонах см. в разделе [Обзор концентраторов уведомлений Azure][Templates].

<div class="dev-callout"><b>Примечание.</b>
	<p>Концентраторы уведомлений позволяют устройству зарегистрировать несколько шаблонов с одним и тем же тегом. В этом случае входящее сообщение, предназначенное для этого тега, приводит к отправке на устройство нескольких уведомлений, по одному для каждого шаблона. Это дает возможность отображения одного сообщения в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в Магазине Windows.</p>
</div>

Выполните следующие действия для отправки кроссплатформенных уведомлений с использованием шаблонов:
	
1. Выполните вход на [портал управления Azure][Management Portal], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

   	![][0]

2. Щелкните вкладку **API**, а затем в таблице API щелкните запись **регистрация_уведомлений**.

	![][1]

5. Щелкните вкладку **Сценарий**, найдите блок **else**, создающий новую регистрацию, когда `existingRegs` принимает значение **false**, и замените его следующим кодом:

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

3. Нажмите вкладку **Данные** и затем щелкните таблицу **TodoItem**. 

   	![][2]

2. В **todoitem** нажмите вкладку **Сценарий** и выберите **Вставить**, а затем замените существующую функцию **insert** следующим кодом:
   
  	![][3]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию insert следующим кодом:

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

	Этот код отправляет уведомление одновременно для всех платформ без необходимости указания собственных полезных данных. Концентраторы уведомлений создают и доставляют правильные полезные данные для каждого устройства с указанным значением _тега_ в соответствии с зарегистрированными шаблонами.

4. Обновите сценарий, чтобы заменить _`<NOTIFICATION_HUB_NAME>`_ и _`<FULL_SAS_CONNECTION_STRING>`_ значениями для своего концентратора уведомлений, а затем нажмите кнопку **Сохранить**.

5. Остановите эмулятор устройства или удалите с устройства существующее клиентское приложение.

	Это гарантирует, что клиентом служб мобильных устройств будет создан новый идентификатор установки. Если этого не сделать, служба пытается использовать существующую регистрацию не на базе шаблона. 

5. Снова разверните и запустите клиентское приложение и убедитесь, что регистрация прошла успешно и что отображается новый идентификатор регистрации.

6. Опять введите текст и добавьте новый элемент.	

	Обратите внимание, что после завершения вставки приложение получает push-уведомление от концентраторов уведомлений.

7. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение и вставьте текст. 

	Обратите внимание, что на каждом из устройств отображается уведомление.

## Дальнейшие действия

Теперь, когда вы завершили работу с данным учебником, вы можете узнать больше о концентраторах уведомлений и шаблонах в следующих разделах:

+ **Использование концентраторов уведомлений для передачи экстренных новостей ([Магазин Windows C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Демонстрация другого сценария для использования шаблонов 

+  **[Обзор концентраторов уведомлений Azure][Templates]**<br/>содержится более подробная информация о шаблонах.

+  **[Инструкции по использованию концентраторов уведомлений для магазина Windows]**<br/>Включает ссылку на язык выражения шаблона.



<!-- Anchors. -->
[Серверная версия ASP.NET]: #aspnet
[Серверная версия мобильных служб]: #mobileservices

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
<!-- URLs. -->
[Push-уведомления для пользователей ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet/
[Push-уведомления для пользователей мобильных служб]: /ru-ru/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express для Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Портал управления]: https://manage.windowsazure.com/
[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Экстренные новости в .NET]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Экстренные новости в iOS]: /ru-ru/manage/services/notification-hubs/breaking-news-ios
[Концентраторы уведомлений Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users 
[Шаблоны]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927172.aspx

