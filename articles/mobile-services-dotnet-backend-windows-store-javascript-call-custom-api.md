<properties pageTitle="Вызов пользовательского API-интерфейса из клиента Магазина Windows - мобильные службы" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="glenga" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызвать пользовательский API-интерфейс из приложения для Магазина Windows. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. Настраиваемый API позволяет получить больший контроль над передачей сообщений, включая чтение и задание заголовков сообщений HTTP и определение формата текста сообщения, отличного от JSON.

Пользовательский API, созданный в этом разделе, позволяет отправить единственный запрос POST, который устанавливает флаг completed равным `true` для всех подлежащих выполнению элементов в таблице. Без этого настраиваемого API клиенту пришлось бы отправлять отдельные запросы для обновления в таблице каждого элемента, подлежащего выполнению.

Добавьте эту функциональную возможность в приложение, созданное при прохождении учебника [Начало работы с мобильными службами] или [Начало работы с данными]. Для этого выполните следующие шаги:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения] 

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. В этом учебнике используется Visual Studio 2012 Express для Windows 8.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Обновление приложения для вызова настраиваемого API

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]


## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Магазина Windows, изучите следующие разделы о мобильных службах:

* [Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]
	<br/>Узнайте, как использовать настраиваемый API для поддержки периодических уведомлений в приложениях Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке пользовательского API-интерфейса и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск".

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
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications
[Хранение серверных скриптов системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
