---
title: "Основные концепции API Management"
description: "Сведения об API, продуктах, ролях, группах и других основных концепциях службы управления API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 126fd84259c604785cdd5db0543f78539c1db9c7


---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Как импортировать определение API с операциями в Azure API Management
В управлении API можно создавать новые интерфейсы API и добавлять операции вручную, либо API можно импортировать вместе с операциями за один шаг.

Интерфейсы API и их операции можно импортировать с помощью следующих форматов.

* WADL
* Swagger

В этом руководстве показано, как создать новый API и импортировать его операции за один шаг. Дополнительные сведения о создании API и добавлении операций вручную см. в статьях [Как создавать API][Как создавать API] и [Как добавлять операции в API][Как добавлять операции в API].

## <a name="import-api"> </a>Импорт API
API создаются и настраиваются на портале издателя. Чтобы перейти на портал издателя, на портале Azure щелкните **Publisher portal** (Портал издателя) для службы управления API. Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Создание экземпляра управления API] в статье [Начало работы со службой управления Azure API][Приступая к работе со службой управления API].

![Портал издателя][api-management-management-console]

Щелкните **APIs** в расположенном слева меню **Управление API**, а затем — **Импортировать API**.

![Импортировать API][api-management-import-apis]

Окно **Импорт API** содержит три вкладки, которые соответствуют трем способам ввода спецификации API.

* **Из буфера обмена** позволяет вставлять спецификацию API в указанное текстовое поле.
* **Из файла** позволяет искать и выбирать файл, который содержит спецификацию API.
* **Из URL-адреса** позволяет ввести URL-адрес спецификации для API.

![Формат импорта API][api-management-import-api-clipboard]

После предоставления спецификации API используйте переключатели, расположенные справа, для выбора формата спецификации. Поддерживаются следующие форматы.

* WADL
* Swagger

Затем, введите **Суффикс URL-адреса веб-API**. Он добавляется к основному URL-адресу для вашей службы API Management. Основной URL-адрес является общим для всех интерфейсов API, размещенных в каждом экземпляре службы API Management. API Management различает интерфейсы API по их суффиксу. Следовательно, суффикс должен быть уникальным для каждого API в конкретном экземпляре службы API Management.

После ввода всех значений щелкните **Сохранить** для создания API и связанных операций. 

> [!NOTE]
> Инструкции по импорту API базового калькулятора в формате Swagger см. в статье [Начало работы со службой управления Azure API](api-management-get-started.md).
> 
> 

## <a name="export-api"> </a> Экспорт API
Помимо импорта новых API можно экспортировать определения своих API с портала издателя. Для этого щелкните **Экспорт API** на вкладке **Сводка** своего интерфейса **API**.

![Экспорт API][api-management-export-api]

Интерфейсы API можно экспортировать в формате WADL или Swagger. Выберите требуемый формат, щелкните **Сохранить**и выберите папку, в которую следует сохранить файл.

![Формат экспорта API][api-management-export-api-format]

## <a name="next-steps"> </a>Дальнейшие действия
Как только API создан, и операции импортированы, можно просмотреть и настроить все дополнительные параметры, добавить API к продукту, и опубликовать его, чтобы он стал доступен для разработчиков. Дополнительные сведения см. в следующих руководствах.

* [Как настраивать параметры API][Как настраивать параметры API]
* [Как создать и опубликовать продукт][Как создать и опубликовать продукт]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Импорт API]: #import-api
[Экспорт API]: #export-api
[Настройка параметров API]: #configure-api-settings
[Дальнейшие действия]: #next-steps

[Приступая к работе со службой управления API]: api-management-get-started.md
[Создание экземпляра управления API]: api-management-get-started.md#create-service-instance

[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Как создавать API]: api-management-howto-create-apis.md
[Как настраивать параметры API]: api-management-howto-create-apis.md#configure-api-settings



<!--HONumber=Nov16_HO3-->


