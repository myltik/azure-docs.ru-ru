<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Уведомление пользователей xplat aspnet" pageTitle="Отправка кросcплатформенных уведомлений для пользователей с концентраторами уведомлений (ASP.NET)" metaKeywords="" description="Как использовать концентраторы уведомлений для отправки в одном запросе уведомлений, предназначенных для всех платформ." metaCanonical="" services="notification-hubs" documentationCenter="" title="Отправка кроссплатформенных уведомлений для пользователей с концентраторами уведомлений" authors="glenga" solutions="" manager="" editor="" />
# Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="Мобильные службы">Мобильные службы</a><a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET" class="current">ASP.NET</a>
</div> 

В предыдущем учебнике под названием [Уведомление пользователей с помощью концентраторов уведомлений] вы научились отправлять push-уведомления на все устройства, зарегистрированные конкретным пользователем, прошедшим проверку. В этом учебнике для отправки уведомления на каждую поддерживаемую платформу клиента требовалось несколько запросов. Концентраторы уведомлений поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Это упрощает отправку кроссплатформенных уведомлений. В этом разделе показано, как использовать шаблоны для отправки в одном запросе независимых от платформы уведомлений, рассчитанных на все платформы. Дополнительные сведения о шаблонах см. в разделе [Обзор концентраторов уведомлений Azure][Templates].

<div class="dev-callout"><b>Примечание.</b>
	<p>Концентраторы уведомлений позволяют устройству зарегистрировать несколько шаблонов с одним и тем же тегом. В этом случае входящее сообщение, предназначенное для этого тега, приводит к отправке на устройство нескольких уведомлений, по одному для каждого шаблона. Это дает возможность отображения одного сообщения в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в Магазине Windows.</p>
</div>

Выполните следующие действия для отправки кроссплатформенных уведомлений с использованием шаблонов:

1. В обозревателе решений в Visual Studio разверните папку **Контроллеры**, затем откройте файл RegisterController.cs. 

2. Найдите в методе **Post** блок кода, создающий новую регистрацию, когда `updated` принимает значение **false**, и замените его следующим кодом:

		if (!updated)
        {
            switch (platform)
            {
                case "windows":
                    var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                    await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                    break;
                case "ios":
                    template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                    await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                    break;
            } 
        }
	
	Этот код вызывает метод, используемый на конкретной платформе для регистрации шаблонов вместо собственной регистрации. Уже имеющиеся регистрации изменять не нужно, поскольку регистрация шаблонов является производной от собственной регистрации.

3. Замените метод **sendNotification** следующим кодом:

        // Send a cross-plaform notification by using templates. 
        private async Task sendNotification(string notificationText, string tag)
        {           
                var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                await hubClient.SendTemplateNotificationAsync(notification, tag);        
        }

	Этот код отправляет уведомление одновременно для всех платформ без необходимости указания собственных полезных данных. Концентраторы уведомлений создают и доставляют правильные полезные данные для каждого устройства с указанным значением тега в соответствии с зарегистрированными шаблонами.

4. Снова запустите клиентское приложение и убедитесь, что регистрация прошла успешно.

5. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение. 

	Обратите внимание, что на каждом из устройств отображается уведомление.

## Дальнейшие действия

Теперь, когда вы завершили работу с данным учебником, вы можете узнать больше о концентраторах уведомлений и шаблонах в следующих разделах:

+ **Использование концентраторов уведомлений для передачи экстренных новостей ([Магазин Windows C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Демонстрация другого сценария для использования шаблонов 

+  **[Обзор концентраторов уведомлений Azure][Templates]**<br/>В обзорном разделе содержится более подробная информация о шаблонах.

+  **[Инструкции по использованию концентраторов уведомлений для магазина Windows]**<br/>Включает справочник по языку выражений шаблона.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push-уведомления для пользователей ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Push-уведомления для пользователей мобильных служб]: /ru-ru/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express для Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Портал управления]: https://manage.windowsazure.com/
[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Экстренные новости в .NET]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Экстренные новости в iOS]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Концентраторы уведомлений Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Уведомление пользователей с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Шаблоны]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Инструкции по использованию концентраторов уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927172.aspx

