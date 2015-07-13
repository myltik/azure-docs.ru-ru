<properties 
	pageTitle="Как создавать API, операции и продукты в Azure API Management" 
	description="Сведения о создании интерфейсов API, операций и продуктов в службе управления API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="sdanie"/>

# Как создавать API, операции и продукты в Azure API Management

В Azure API Management интерфейсы API и операции с ними добавляются в продукты, где они могут использоваться разработчиками при создании приложений с использованием этих API. Указания в этом разделе описывают создание API, добавление операций в них и последующее соединение API с продуктом и публикация его для использования разработчиками.

## <a name="create-apis"> </a>Как создавать API

API в API Management представляет набор операций, которые могут быть вызваны клиентскими приложениями. Новые API создаются на портале API Management.

Это руководство описывает создание и настройку нового интерфейса API в API Management

-   [Как создавать API][]

## <a name="add-operations"> </a>Как добавлять операции в API

Прежде чем API может быть использован в API Management, должны быть добавлены операции. Это руководство описывает добавление и настройку операций различного типа в интерфейсе API в API Management.

-   [Как добавлять операции в API][]

API и операции можно импортировать за один шаг в формате WADL или Swagger.

-	[Как импортировать определение API с операциями][]

## <a name="add-product"> </a>Как создать и опубликовать продукт

В API Management продукт содержит один и несколько API вместе с квотой использования и условиями использования. Как только продукт опубликован, разработчики могут подписаться на продукт и использовать интерфейсы API продукта. В данных разделах есть указания для создания продукта, добавления API и публикации его для разработчиков.

-   [Как создать и опубликовать продукт][]
-	[Как создать и настроить расширенные настройки продукта][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Как создавать API]: api-management-howto-create-apis.md
[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Как импортировать определение API с операциями]: api-management-howto-import-api.md
[Как создать и настроить расширенные настройки продукта]: api-management-howto-product-with-rules.md

<!---HONumber=62-->