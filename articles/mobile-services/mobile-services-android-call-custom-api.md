<properties 
	pageTitle="Вызов настраиваемого интерфейса API из клиента Android | Центр мобильных разработок" 
	description="Узнайте, как определить настраиваемый API и вызывать его из приложения Android, которое использует мобильные службы Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="ricksal"/>

# Вызов из клиента настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений Android. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Пользовательский API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага *completed* значение `true` для всех элементов списка дел в таблице мобильной службы. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными].


>[AZURE.NOTE]Просмотреть исходный код завершенного приложения можно <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/CallCustomApi/Android" target="_blank">здесь</a>.

##Предварительные требования

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


## Дальнейшие действия

В этой статье мы рассказали вам, как с помощью метода **InvokeApi** вызывать относительно простой настраиваемый интерфейс API из приложения Android. Дополнительные сведения об использовании метода **InvokeApi** см. в записи блога [Настраиваемый интерфейс API в мобильных службах Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Также рекомендуем просмотреть дополнительные статьи, посвященные мобильным службам:

* [Справочник серверных скриптов мобильных служб] <br/>Узнайте больше о создании настраиваемых интерфейсов API.

* [Хранение серверных сценариев в системе управления версиями] <br/>Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код сценариев настраиваемого API.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: mobile-services-android-get-started.md
[Приступая к работе с данными]: mobile-services-android-get-started-data.md
[Get started with authentication]: mobile-services-android-get-started-users.md
[Get started with push notifications]: ../mobile-services-android-get-started-push.md

[Хранение серверных сценариев в системе управления версиями]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=August15_HO6-->