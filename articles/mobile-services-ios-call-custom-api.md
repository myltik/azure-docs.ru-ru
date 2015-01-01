<properties pageTitle="Вызов настраиваемого API из приложения для iOS | Мобильные службы" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="krisragh"  solutions="" writer="krisragh" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений iOS. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение true для всех элементов списка дел в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого интерфейса API]
3. [Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с этим учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. Для работы с этим учебником необходимы [пакет SDK для мобильных служб для iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533) , [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) и IOS 5.0 или более поздних версий.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для iOS, изучите следующие статьи о мобильных службах:

* [Справочник серверных сценариев мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых интерфейсов API.

* [Хранение серверных сценариев в системе управления версиями]
  <br/> Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-ios-get-started-push/
[Хранение серверных сценариев в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=35_1-->
