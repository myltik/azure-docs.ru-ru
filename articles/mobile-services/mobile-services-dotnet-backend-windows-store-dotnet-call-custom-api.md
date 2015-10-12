<properties
	pageTitle="Вызов пользовательского API-интерфейса из клиента Магазина Windows — мобильные службы"
	description="Узнайте, как определить настраиваемый API и вызывать его из приложения магазина Windows, которое использует мобильные службы Azure."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="glenga"/>

# Вызов из клиента настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый API из приложений Магазина Windows. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый интерфейс API, созданный в этом разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с данным учебником вам необходимо выполнить все задания в учебнике [Приступая к работе с мобильными службами] или [Добавление мобильных служб к существующему приложению].

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

## <a name="update-app"></a>Обновление приложения для вызова настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## Дальнейшие действия

В этой статье мы рассказали вам, как с помощью метода **InvokeApiAsync** вызывать относительно простой настраиваемый интерфейс API из приложения Магазина Windows. Дополнительные сведения об использовании метода **InvokeApiAsync** см. в записи блога [Настраиваемый интерфейс API в мобильных службах Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Также рекомендуем просмотреть дополнительные статьи, посвященные мобильным службам:

* [Определение настраиваемого интерфейса API с поддержкой периодических уведомлений] <br/>Узнайте, как использовать настраиваемый интерфейс API для поддержки периодических уведомлений в приложении Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого интерфейса API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск".

* [Справочник по серверным сценариям мобильных служб] <br/>Дополнительные сведения о создании настраиваемых API.

* [Хранение серверных сценариев в системе управления версиями] <br/>Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Справочник по серверным сценариям мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Добавление мобильных служб к существующему приложению]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md

[Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]: mobile-services-windows-store-dotnet-create-pull-notifications.md
[Хранение серверных сценариев в системе управления версиями]: mobile-services-store-scripts-source-control.md

<!---HONumber=Oct15_HO1-->