<properties pageTitle="Call a custom API from an iOS app | Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="krisragh"  solutions="" writer="krisragh" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Вызов из клиента настраиваемого интерфейса API

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений iOS. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]. Для этого требуются следующие действия:

1.  [Определение настраиваемого интерфейса API][Определение настраиваемого интерфейса API]
2.  [Обновление приложения для вызова настраиваемого API][Обновление приложения для вызова настраиваемого API]
3.  [Тестирование приложения][Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами]. Для этого учебника требуется [Пакет Mobile Services iOS SDK][Пакет Mobile Services iOS SDK] и [XCode 4.5][XCode 4.5], а также iOS версии 5.0 или более поздней версии.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api][mobile-services-create-custom-api]]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api][mobile-services-ios-call-custom-api]]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для iOS, изучите следующие статьи о мобильных службах:

-   [Справочник серверных скриптов мобильных служб][Справочник серверных скриптов мобильных служб]<br/>
    Дополнительные сведения о создании настраиваемых API.

-   [Хранение серверных сценариев в системе управления версиями][Хранение серверных сценариев в системе управления версиями]<br/>
    Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors.URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /ru-ru/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-ios-call-custom-api "Серверная часть JavaScript"
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
  [Определение настраиваемого интерфейса API]: #define-custom-api
  [Обновление приложения для вызова настраиваемого API]: #update-app
  [Тестирование приложения]: #test-app
  [Пакет Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-ios-call-custom-api]: ../includes/mobile-services-ios-call-custom-api.md
  [Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Хранение серверных сценариев в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
