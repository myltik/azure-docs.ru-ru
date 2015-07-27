<properties 
	pageTitle="Основные концепции API Management" 
	description="Сведения об API, продуктах, ролях, группах и других основных концепциях службы управления API." 
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
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Как импортировать определение API с операциями в Azure API Management

В управлении API можно создавать новые интерфейсы API и добавлять операции вручную, либо API можно импортировать вместе с операциями за один шаг.

Интерфейсы API и их операции можно импортировать с помощью следующих форматов.

-	WADL
-	Swagger

В этом руководстве показано, как создать новый API и импортировать его операции за один шаг. Дополнительные сведения о создании API и добавлении операций вручную см. в разделе [Как создать интерфейсы API][] и [Как добавить операции к API][].

## <a name="import-api"> </a>Импорт API

API создаются и настраиваются на портале издателя. Чтобы перейти на портал издателя, щелкните **Управление** на портале Azure для службы управления API. Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра службы управления API][] в руководстве [Начинаем работу со службой управления API][].

![Портал издателя][api-management-management-console]

Щелкните **API** в расположенном слева меню **Управление API**, а затем **Импортировать API**.

![Импорт API][api-management-import-apis]

Окно **Импорт API** содержит три вкладки, которые соответствуют трем способам ввода спецификации API.

-	**Из буфера обмена** позволяет вставлять спецификацию API в указанное текстовое поле.
-	**Из файла** позволяет искать и выбирать файл, который содержит спецификацию API.
-	**Из URL-адреса** позволяет ввести URL-адрес спецификации для API.

![Формат импорта API][api-management-import-api-clipboard]

После предоставления спецификации API используйте переключатели, расположенные справа, для выбора формата спецификации. Поддерживаются следующие форматы.

-	WADL
-	Swagger

Затем, введите **Суффикс URL-адреса веб-API**. Он добавляется к основному URL-адресу для вашей службы API Management. Основной URL-адрес является общим для всех интерфейсов API, размещенных в каждом экземпляре службы API Management. API Management различает интерфейсы API по их суффиксу. Следовательно, суффикс должен быть уникальным для каждого API в конкретном экземпляре службы API Management.

После ввода всех значений щелкните **Сохранить** для создания API и связанных операций.

>[AZURE.NOTE]Инструкции по импорту базового API калькулятора в формате Swagger см. в статье [Управление API в службе управления API Azure](api-management-get-started.md).

## <a name="export-api"> </a>Экспорт API

Помимо импорта новых API можно экспортировать определения своих API с портала издателя. Для этого щелкните **Экспорт API** на вкладке **Сводка** своего интерфейса **API**.

![Экспорт API][api-management-export-api]

Интерфейсы API можно экспортировать в формате WADL или Swagger. Выберите требуемый формат, щелкните **Сохранить** и выберите папку, в которую следует сохранить файл.

![Формат экспорта API][api-management-export-api-format]

## <a name="next-steps"> </a>Дальнейшие действия

Как только API создан, и операции импортированы, можно просмотреть и настроить все дополнительные параметры, добавить API к продукту, и опубликовать его, чтобы он стал доступен для разработчиков. Дополнительные сведения см. в следующих руководствах.

-	[Как настраивать параметры API][]
-	[Как создать и опубликовать продукт][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Начинаем работу со службой управления API]: api-management-get-started.md
[Создание экземпляра службы управления API]: api-management-get-started.md#create-service-instance

[Как добавить операции к API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Как создать интерфейсы API]: api-management-howto-create-apis.md
[Как настраивать параметры API]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=July15_HO3-->