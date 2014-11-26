<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Как включить службу Google Cloud Messaging

В этом разделе показано, как включить ваше приложение Android для push-уведомлений с помощью службы Google Cloud Messaging (GCM). Полученный ключ API используется для регистрации приложения Android для push-уведомлений на [портале управления Azure][портале управления Azure]. Полный набор учебных курсов, который включает в себя обновления для вашего приложения, см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Теперь значение этого ключа API можно использовать, чтобы службы могли проверять подлинность с помощью службы GCM и отправлять push-уведомления от имени вашего приложения.

 
 


  [портале управления Azure]: https://manage.windowsazure.com/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
