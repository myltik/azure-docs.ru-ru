<properties pageTitle="How to create APIs, operations, and products in Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and products in API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs, operations, and products in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Как создавать API, операции и продукты в Azure API Management

В Azure API Management интерфейсы API и операции с ними добавляются в продукты, где они могут использоваться разработчиками при создании приложений с использованием этих API. Указания в этом разделе описывают создание API, добавление операций в них и последующее соединение API с продуктом и публикация его для использования разработчиками.

## <a name="create-apis"> </a>Как создать API

API в API Management представляет набор операций, которые могут быть вызваны клиентскими приложениями. Новые API создаются на портале API Management.

Это руководство описывает создание и настройку нового интерфейса API в API Management

-   [Как создать API][Как создать API]

## <a name="add-operations"> </a>Как добавить операцию в API

Прежде чем API может быть использован в API Management, должны быть добавлены операции. Это руководство описывает добавление и настройку операций различного типа в интерфейсе API в API Management.

-   [Как добавить операцию в API][Как добавить операцию в API]

API и операции можно импортировать за один шаг в формате WADL или Swagger.

-   [Как импортировать определение API с операциями][Как импортировать определение API с операциями]

## <a name="add-product"> </a>Как создать и опубликовать продукт

В API Management продукт содержит один и несколько API вместе с квотой использования и условиями использования. Как только продукт опубликован, разработчики могут подписаться на продукт и использовать интерфейсы API продукта. В данных разделах есть указания для создания продукта, добавления API и публикации его для разработчиков.

-   [Как создать и опубликовать продукт][Как создать и опубликовать продукт]
-   [Как создать и задать расширенные настройки продукта][Как создать и задать расширенные настройки продукта]

  [Как создать API]: ../api-management-howto-create-apis
  [Как добавить операцию в API]: ../api-management-howto-add-operations
  [Как импортировать определение API с операциями]: ../api-management-howto-import-api
  [Как создать и опубликовать продукт]: ../api-management-howto-add-products
  [Как создать и задать расширенные настройки продукта]: ../api-management-howto-product-with-rules
