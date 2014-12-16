<properties writer="ricksal" pageTitle="Вызов пользовательского API из клиента Android | Центр мобильных разработок" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Windows Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызвать пользовательский API из приложения Android. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. Настраиваемый API позволяет получить больший контроль над передачей сообщений, включая чтение и задание заголовков сообщений HTTP и определение формата текста сообщения, отличного от JSON.

Настраиваемый API, создаваемый в этом разделе, позволяет отправить единственный запрос POST, который устанавливает флажок *completed* равным `true` для всех элементов, подлежащих выполнению, в таблице мобильной службы. Без этого настраиваемого API клиенту пришлось бы отправлять отдельные запросы для обновления в таблице каждого элемента, подлежащего выполнению.

Добавьте эту функциональную возможность в приложение, созданное при прохождении учебника [Начало работы с мобильными службами] или [Начало работы с данными]. Для этого выполните следующие шаги:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения] 

Этот учебник создан на основе краткого руководства по мобильным службам. Прежде чем приступать к этому учебнику, необходимо пройти до конца учебник [Начало работы с мобильными службами]. 

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Android, изучите следующие статьи о мобильных службах:

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
[Android SDK для мобильных служб]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Хранение серверных скриптов системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
