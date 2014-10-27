<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga"></tags>

# Приступая к работе с push-уведомлениями в мобильных службах

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией JavaScript для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части JavaScript во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

> [WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio 2013 с обновлением 3 для отправки push-уведомлений из мобильных служб в универсальное приложение Windows. Эти же инструкции можно использовать для добавления push-уведомлений из мобильных служб в приложение Магазина Windows или Магазина Windows Phone 8.1. Если вы не можете выполнить обновление до Visual Studio 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение Магазина Windows, см. [эту версию][эту версию] раздела.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Регистрация приложения для получения push-уведомлений][Регистрация приложения для получения push-уведомлений]
2.  [Обновление службы для отправки push-уведомлений][Обновление службы для отправки push-уведомлений]
3.  [Тестирование push-уведомлений в приложении][Тестирование push-уведомлений в приложении]

Для работы с этим учебником требуется:

-   активная [учетная запись Microsoft Store][учетная запись Microsoft Store];
-   [Visual Studio Express 2013 для Windows][Visual Studio Express 2013 для Windows] с обновлением 3 или более поздней версии.

## <span id="register"></span></a> Регистрация приложения для получения push-уведомлений

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Перейдите в папку проекта `\services\mobileServices\scripts`, скопируйте созданный файл скрипта \<*имя\_службы*\>.push.register.js в общую папку `\js`, а затем удалите этот файл из проектов приложений Windows и Windows Phone.

2.  Откройте этот файл скрипта в общей папке проекта `\js`, найдите в прослушивателе событий *activated* код, который регистрирует URL-адрес канала устройства в концентраторе уведомлений, и удалите функцию обещания **done**.

    В примере в этом учебнике уведомления отправляются при вставке нового элемента, а не при вызове настраиваемого API.

3.  В проекте приложения Windows откройте файл default.html и измените путь ссылки на файл скрипта в общей папке проекта `\js` так, чтобы он выглядел следующим образом:

        <script src="/js/your_service_name.push.register.js"></script>

4.  Повторите этот шаг для проекта приложения WindowsPhone.

    Теперь в обоих проектах используется общая версия скрипта регистрации push-уведомлений.

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления.

## <span id="update-service"></span></a>Обновление службы для отправки push-уведомлений

Ниже показано, как обновить скрипт вставки, зарегистрированный в таблице TodoItem. Подобный код можно реализовать в любом серверном скрипте или в другом месте в серверных службах.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложениях Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Отправка push-уведомлений прошедшим проверку пользователям][Отправка push-уведомлений прошедшим проверку пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными (][Приступая к работе с данными (]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Использование клиента HTML/JavaScript для мобильных служб Azure][Использование клиента HTML/JavaScript для мобильных служб Azure]
    Дополнительные сведения об использовании мобильных служб из приложений HTML и JavaScript.



  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [эту версию]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Регистрация приложения для получения push-уведомлений]: #register
  [Обновление службы для отправки push-уведомлений]: #update-service
  [Тестирование push-уведомлений в приложении]: #test
  [учетная запись Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Express 2013 для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Отправка push-уведомлений прошедшим проверку пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Приступая к работе с данными (]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users
  [Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
  [Использование клиента HTML/JavaScript для мобильных служб Azure]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
