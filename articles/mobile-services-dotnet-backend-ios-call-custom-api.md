<properties pageTitle="Вызов пользовательского API из приложения iOS | Мобильные службы" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="krisragh"  solutions="" writer="krisragh" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />


# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызвать пользовательский API из приложения iOS. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. Настраиваемый API позволяет получить больший контроль над передачей сообщений, включая чтение и задание заголовков сообщений HTTP и определение формата текста сообщения, отличного от JSON.

Пользовательский API, созданный в этом разделе, позволяет отправить единственный запрос POST, который устанавливает флаг completed равным `true` для всех подлежащих выполнению элементов в таблице. Без этого настраиваемого API клиенту пришлось бы отправлять отдельные запросы для обновления в таблице каждого элемента, подлежащего выполнению.

Добавьте эту функциональную возможность в приложение, созданное при прохождении учебника [Начало работы с мобильными службами] или [Начало работы с данными]. Для этого выполните следующие шаги:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. Дл этого учебник требуются [SDK мобильных служб iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), а также [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) и iOS 5.0 или более поздней версии.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для iOS, изучите следующие статьи о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых API.

* [Хранение серверных скриптов системе управления версиями]
  <br/> Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код скриптов настраиваемого API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Хранение серверных скриптов системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
