<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Как включить push-уведомления Apple

В этом разделе показано, как включить ваше приложение iOS для push-уведомлений с помощью службы push-уведомлений Apple (APNS). Полученный сертификат используется для регистрации приложения IOS для push-уведомлений на [портале управления Azure][портале управления Azure]. Полный набор учебных курсов, который включает в себя обновления для вашего приложения, см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

[WACOM.INCLUDE [Включение push-уведомлений Apple](../includes/enable-apple-push-notifications.md)]

Теперь этот сертификат .p12 можно использовать, чтобы службы могли проверять подлинность с помощью APNS и отправлять push-уведомления от имени вашего приложения.

 
 


  [портале управления Azure]: https://manage.windowsazure.com/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
