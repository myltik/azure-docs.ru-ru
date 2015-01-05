<properties writer="ricksal" pageTitle="Вызов настраиваемого интерфейса API из клиента Android | Центр мобильных разработок" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="ricksal,glenga" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga" />

# Вызов из клиента настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений Android. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый интерфейс API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение true для всех элементов списка дел в таблице мобильной службы. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого интерфейса API]
3. [Тестирование приложения] 


>[AZURE.NOTE] Если вы хотите посмотреть исходный код готового приложения, перейдите <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">здесь</a>.

##Предварительные требования

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Android, изучите следующие статьи о мобильных службах:



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
[Пакет SDK для мобильных служб для Android]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-android-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-android-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-android-get-started-push/

[Хранение серверных сценариев в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=35.1-->
