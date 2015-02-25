<properties pageTitle="Вызов настраиваемого API из приложения для iOS | Мобильные службы" description="Узнайте, как определить настраиваемый API и вызывать его из приложения iOS, которое использует мобильные службы Azure." services="mobile-services" documentationCenter="ios" authors="krisragh" writer="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Вызов из клиента настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений iOS. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого интерфейса API]
3. [Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. Для этого учебника требуется [Пакет Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) и [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532), а также iOS версии 5.0 или более поздней версии.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для iOS, изучите следующие статьи о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых интерфейсов API.

* [Хранение серверных скриптов в системе управления версиями]
  <br/> Сведения о том, как использовать функцию управления версиями, чтобы легко разрабатывать и безопасно публиковать код сценариев настраиваемого интерфейса API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-ios-get-started-push/
[Хранение серверных скриптов в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
