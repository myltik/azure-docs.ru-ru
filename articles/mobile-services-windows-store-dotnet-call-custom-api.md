<properties urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Вызов из клиента настраиваемого интерфейса API

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/ru-ru/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как вызывать настраиваемый API из приложений Магазина Windows. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]. Для этого требуются следующие действия:

1.  [Определение настраиваемого интерфейса API][Определение настраиваемого интерфейса API]
2.  [Обновление приложения для вызова настраиваемого API][Обновление приложения для вызова настраиваемого API]
3.  [Тестирование приложения][Тестирование приложения]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными]. В этом учебнике используется Visual Studio 2012 Express для Windows 8.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Магазина Windows, изучите следующие разделы о мобильных службах:

-   [Определение настраиваемого интерфейса API с поддержкой периодических уведомлений][Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]
    <br/>Узнайте, как использовать настраиваемый API для поддержки периодических уведомлений в приложении для Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого интерфейса API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск".

-   [Справочник серверных скриптов мобильных служб][Справочник серверных скриптов мобильных служб]
    <br/>Дополнительные сведения о создании настраиваемых API.

-   [Хранение серверных сценариев в системе управления версиями][Хранение серверных сценариев в системе управления версиями]
    <br/>Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
<!-- URLs. -->

  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [Определение настраиваемого интерфейса API]: #define-custom-api
  [Обновление приложения для вызова настраиваемого API]: #update-app
  [Тестирование приложения]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-windows-store-dotnet-call-custom-api]: ../includes/mobile-services-windows-store-dotnet-call-custom-api.md
  [Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications
  [Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Хранение серверных сценариев в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control
