<properties
	pageTitle="Вызов настраиваемого API из клиента Windows Phone — мобильные службы"
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
	ms.date="09/28/2015"
	ms.author="glenga"/>

# Вызов из клиента настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый API из приложений Windows Phone. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый интерфейс API, созданный в этом разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Добавьте эти функциональные возможности к приложению, созданному в учебнике [Добавление мобильных служб к существующему приложению](mobile-services-windows-phone-get-started-data.md). Этот учебник построен на использовании GetStartedWithData, простого примера приложения TodoList. Прежде чем приступить к изучению данного учебника, необходимо выполнить задания учебника [Добавление мобильных служб к существующему приложению](mobile-services-windows-phone-get-started-data.md).

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]

## Дальнейшие действия

В этой статье мы рассказали вам, как с помощью метода **InvokeApiAsync** вызывать относительно простой настраиваемый интерфейс API из приложения Windows Phone. Дополнительные сведения об использовании метода **InvokeApiAsync** см. в записи блога [Настраиваемый интерфейс API в мобильных службах Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Также рекомендуем просмотреть дополнительные статьи, посвященные мобильным службам:

* [Справочник серверных скриптов мобильных служб] <br/>Узнайте больше о создании настраиваемых интерфейсов API.

* [Хранение серверных сценариев в системе управления версиями] <br/>Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[Get started with data]: mobile-services-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-windows-phone-get-started-users.md
[Get started with push notifications]: ../mobile-services-windows-phone-get-started-push.md

[Хранение серверных сценариев в системе управления версиями]: mobile-services-store-scripts-source-control.md

<!---HONumber=Oct15_HO1-->