<properties 
	pageTitle="Вызов настраиваемого API из клиента Windows Phone - мобильные службы" 
	description="Узнайте, как определить настраиваемый API и вызывать его из приложения Windows Phone, которое использует мобильные службы Azure." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/06/2014" 
	ms.author="glenga"/>

# Вызов из клиента настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый API из приложений Windows Phone. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Добавьте эти функциональные возможности к приложению, созданному при прохождении учебника [Добавление мобильных служб к существующему приложению](mobile-services-windows-phone-get-started-data.md) . Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого интерфейса API]
3. [Тестирование приложения] 

Этот учебник построен на использовании GetStartedWithData, простого примера приложения TodoList. Прежде чем приступить к изучению данного учебника, необходимо выполнить задания учебника [Добавление мобильных служб к существующему приложению](mobile-services-windows-phone-get-started-data.md)

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Windows Phone, изучите следующие разделы о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых интерфейсов API.

* [Хранение серверных скриптов в системе управления версиями]
  <br/> Сведения о том, как использовать функцию управления версиями, чтобы легко разрабатывать и безопасно публиковать код сценариев настраиваемого интерфейса API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push/

[Хранение серверных скриптов в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
