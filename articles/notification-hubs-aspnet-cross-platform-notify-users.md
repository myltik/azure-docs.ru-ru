<properties urlDisplayName="Notify Users xplat aspnet" pageTitle="Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />
# Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений


В предыдущем учебнике под названием [Уведомление пользователей посредством центров уведомлений] вы научились отправлять push-уведомления на все устройства, зарегистрированные конкретным пользователем, прошедшим проверку. В этом учебнике для отправки уведомления на каждую поддерживаемую платформу клиента требовалось несколько запросов. Концентраторы уведомлений поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Это упрощает отправку кроссплатформенных уведомлений. В этом разделе показано, как использовать шаблоны для отправки в одном запросе независимых от платформы уведомлений, рассчитанных на все платформы. Дополнительную информацию о шаблонах см. в разделе [Общие сведения о концентраторах уведомлений Azure][Templates].

<div class="dev-callout"><b>Примечание.</b>
	<p>Концентраторы уведомлений позволяют устройству зарегистрировать несколько шаблонов с одним и тем же тегом. В этом случае входящее сообщение, предназначенное для этого тега, приводит к отправке на устройство нескольких уведомлений, по одному для каждого шаблона. Это дает возможность отображения одного сообщения в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в Магазине Windows.</p>
</div>

Выполните следующие действия для отправки кроссплатформенных уведомлений с использованием шаблонов:

1. В обозревателе решений в Visual Studio разверните папку **Контроллеры**, затем откройте файл RegisterController.cs. 

2. Найдите в методе **Post** блок кода, создающий новую замену регистрации для содержания "switch" со следующим кодом:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":

                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"msg\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	Этот код вызывает метод, используемый на конкретной платформе для регистрации шаблонов вместо собственной регистрации. Уже имеющиеся регистрации изменять не нужно, поскольку регистрация шаблонов является производной от собственной регистрации.

3. В контроллере **Уведомления** замените метод **sendNotification** следующим кодом:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	Этот код отправляет уведомление одновременно для всех платформ без необходимости указания собственных полезных данных. Концентраторы уведомлений создают и доставляют правильные полезные данные для каждого устройства с указанным значением тега в соответствии с зарегистрированными шаблонами.

4. Повторно опубликуйте серверный проект WebApi.

5. Снова запустите клиентское приложение и убедитесь, что регистрация прошла успешно.

6. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение. 

	Обратите внимание, что на каждом из устройств отображается уведомление.

## Дальнейшие действия

Теперь, когда вы завершили работу с данным учебником, вы можете узнать больше о концентраторах уведомлений и шаблонах в следующих разделах:

+ **[Использование центров уведомлений для передачи экстренных новостей]** <br/>В этом разделе описан другой сценарий использования шаблонов. 

+  **[Общие сведения о концентраторах уведомлений Azure][Templates]**<br/>Здесь приведена более подробная информация о шаблонах.

+  **[Указания по использованию центров уведомлений для магазина Windows]**<br/>В этом разделе содержится справочная информация по языку выражений для шаблонов.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push-уведомления для пользователей ASP.NET]: /ru-ru/manage/services/notification-hubs/notify-users-aspnet
[Push-уведомления для пользователей мобильных служб]: /ru-ru/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express для Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Портал управления]: https://manage.windowsazure.com/
[Использование центров уведомлений для передачи экстренных новостей]: /ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Центры уведомлений Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Уведомление пользователей с помощью центров уведомлений]: /ru-ru/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[Шаблоны]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Указания по использованию центров уведомлений для магазина Windows]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927172.aspx

<!--HONumber=35.1-->
