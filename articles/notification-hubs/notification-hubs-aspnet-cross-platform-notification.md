---
title: Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений Azure (ASP.NET)
description: Узнайте, как использовать шаблоны центров уведомлений для отправки в одном запросе независимых от платформы уведомлений, предназначенных для всех платформ.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 95793aac3c25563e3af39f3c47cebdd06e25e35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777959"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Отправка кроссплатформенных уведомлений пользователям с помощью центров уведомлений
В предыдущем руководстве под названием [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET] вы научились отправлять push-уведомления на все устройства, которые зарегистрированы для конкретного пользователя, прошедшего проверку. В этом учебнике для отправки уведомления на каждую поддерживаемую платформу клиента требовалось несколько запросов. Концентраторы уведомлений Azure поддерживают шаблоны, которые позволяют указать, каким образом конкретное устройство должно получать уведомления. Этот метод упрощает отправку кроссплатформенных уведомлений. 

В этой статье рассказывается, как воспользоваться преимуществами шаблонов для отправки в одном запросе независимых от платформы уведомлений, предназначенных для всех платформ. Дополнительные сведения о шаблонах см. в статье [Общие сведения о концентраторах уведомлений][Templates].

> [!IMPORTANT]
> Проекты Windows Phone 8.1 и более ранней версии не поддерживаются в Visual Studio 2017. Дополнительные сведения см. в статье [Целевая платформа и совместимость для Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Центры уведомлений позволяют устройству зарегистрировать несколько шаблонов с одним и тем же тегом. В этом случае входящее сообщение, предназначенное для этого тега, приводит к отправке на устройство нескольких уведомлений, по одному для каждого шаблона. Этот процесс дает возможность отобразить одно сообщение в нескольких визуальных уведомлениях, например в виде значка и в виде всплывающего уведомления в приложении Магазина Windows.
> 
> 

Для отправки кроссплатформенных уведомлений с использованием шаблонов выполните инструкции ниже.

1. В обозревателе решений в Visual Studio разверните папку **Контроллеры**, а затем откройте файл RegisterController.cs.

2. Найдите в методе **Put** блок кода, который создает регистрацию, и замените содержимое `switch` кодом ниже.
   
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
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Этот код вызывает метод, используемый на конкретной платформе для регистрации шаблонов вместо собственной регистрации. Регистрация шаблонов является производной от собственной регистрации. Поэтому существующие регистрации изменять не нужно.

3. В контроллере **Уведомления** замените метод **sendNotification** следующим кодом:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Этот код отправляет уведомление одновременно для всех платформ. При этом вам не нужно указывать собственные полезные данные. Центры уведомлений создают и доставляют правильные полезные данные для каждого устройства с указанным значением *тега* в соответствии с зарегистрированными шаблонами.

4. Повторно опубликуйте проект серверной части веб-API.

5. Снова запустите клиентское приложение и убедитесь, что регистрация прошла успешно.

6. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение.
    Уведомление отображается на каждом из устройств.

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы завершили работу с данным учебником, вы можете узнать больше о центрах уведомлений и шаблонах в следующих разделах:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Общие сведения о концентраторах уведомлений][Templates] — содержит подробные сведения о шаблонах.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
