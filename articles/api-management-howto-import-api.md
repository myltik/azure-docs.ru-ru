<properties pageTitle="API Management key concepts" metaKeywords="" description="Learn about APIs, products, roles, groups, and other API Management key concepts." metaCanonical="" services="" documentationCenter="API Management" title="API Management key concepts" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Как импортировать определение API с операциями в Azure API Management

В API Management (предварительная версия) новые интерфейсы API можно создавать, а также добавлять операции, вручную, либо API можно импортировать вместе с операциями за один шаг.

Интерфейсы API и их операции можно импортировать с помощью следующих форматов.

-   WADL
-   Swagger

В этом руководстве показано, как создать новый API и импортировать его операции за один шаг.

> Дополнительные сведения о создании API и добавлении операций вручную см. в разделе [Как создать интерфейсы API][Как создать интерфейсы API] и [Как добавить операции к API][Как добавить операции к API].

## Содержание раздела

-   [Импорт API][Импорт API]
-   [Экспорт API][Экспорт API]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="import-api"> </a>Импорт API

Для создания и настройки интерфейсов API щелкните **Консоль управления** на портале Azure службы API Management. Открывается административный портал API Management.

> Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

![Консоль управления][Консоль управления]

Щелкните **API** в расположенном слева меню **API Management**, а затем **Импортировать API**.

![Импорт API][1]

Окно **Импорт API** содержит три вкладки, которые соответствуют трем способам ввода спецификации API.

-   **Из буфера обмена** позволяет вставлять спецификацию API в указанное текстовое поле.
-   **Из файла** позволяет искать и выбирать файл, который содержит спецификацию API.
-   **Из URL-адреса** позволяет ввести URL-адрес спецификации для API.

![Формат импорта API][Формат импорта API]

После предоставления спецификации API используйте переключатели, расположенные справа, для выбора формата спецификации. Поддерживаются следующие форматы.

-   WADL
-   Swagger

Затем, введите **Суффикс URL-адреса веб-API**. Он добавляется к основному URL-адресу для вашей службы API Management. Основной URL-адрес является общим для всех интерфейсов API, размещенных в каждом экземпляре службы API Management. API Management различает интерфейсы API по их суффиксу. Следовательно, суффикс должен быть уникальным для каждого API в конкретном экземпляре службы API Management.

После ввода всех значений щелкните **Сохранить** для создания API и связанных операций.

## <a name="export-api"> </a> Экспорт API

В дополнение к импорту новых интерфейсов API с консоли управления можно экспортировать определения своих интерфейсов API. Для этого щелкните **Экспорт API** на вкладке **Сводка** своего интерфейса **API**.

![Экспорт API][2]

Интерфейсы API можно экспортировать в формате WADL или Swagger. Выберите требуемый формат, щелкните **Сохранить** и выберите папку, в которую следует сохранить файл.

![Формат экспорта API][Формат экспорта API]

## <a name="next-steps"> </a>Дальнейшие действия

Как только API создан, и операции импортированы, можно просмотреть и настроить все дополнительные параметры, добавить API к продукту, и опубликовать его, чтобы он стал доступен для разработчиков. Дополнительные сведения см. в следующих руководствах.

-   [Как настраивать параметры API][Как настраивать параметры API]
-   [Как создать и опубликовать продукт][Как создать и опубликовать продукт]

  [Как создать интерфейсы API]: ../api-management-howto-create-apis
  [Как добавить операции к API]: ../api-management-howto-add-operations
  [Импорт API]: #import-api
  [Экспорт API]: #export-api
  [Дальнейшие действия]: #next-steps
  [Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
  [Начинаем работу с API Management]: ../api-management-get-started
  [Консоль управления]: ./media/api-management-howto-import-api/api-management-management-console.png
  [1]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [Формат импорта API]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [2]: ./media/api-management-howto-import-api/api-management-export-api.png
  [Формат экспорта API]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [Как настраивать параметры API]: ../api-management-howto-create-apis/#configure-api-settings
  [Как создать и опубликовать продукт]: ../api-management-howto-add-products
