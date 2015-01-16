<properties pageTitle="Вызов пользовательского API-интерфейса из приложения Windows Phone - мобильные службы" metaKeywords="" description="Узнайте, как определить настраиваемый API и вызывать его из приложения Windows Phone, которое использует мобильные службы Microsoft Azure." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызвать пользовательский API из приложения Windows Phone. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. Настраиваемый API позволяет получить больший контроль над передачей сообщений, включая чтение и задание заголовков сообщений HTTP и определение формата текста сообщения, отличного от JSON.

Пользовательский API, созданный в этом разделе, позволяет отправить единственный запрос POST, который устанавливает флаг completed равным `true` для всех подлежащих выполнению элементов в таблице. Без этого настраиваемого API клиенту пришлось бы отправлять отдельные запросы для обновления в таблице каждого элемента, подлежащего выполнению.

Добавьте эти функциональные возможности к приложению, созданному при прохождении учебника [Добавление мобильных служб к существующему приложению](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Для этого выполните следующие шаги:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения] 

Этот учебник основан на примере GetStartedWithData, простом приложении TodoList. Прежде чем приступать к работе с этим учебником, вы должны пройти [Добавление мобильных служб к существующему приложению](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/).

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]


## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Windows Phone, изучите следующие разделы о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых API.

* [Хранение серверных скриптов системе управления версиями]
  <br/> Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Хранение серверных скриптов системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
