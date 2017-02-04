---
title: "Как использовать соединитель Salesforce в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Соединитель Salesforce предоставляет API для работы с объектами Salesforce."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: b6758aa36120c9c187e91ee5d9e7ceb5041eae6a


---
# <a name="get-started-with-the-salesforce-connector"></a>Начало работы с соединителем Salesforce
Соединитель Salesforce предоставляет API для работы с объектами Salesforce.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Подключение к соединителю Salesforce
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

### <a name="create-a-connection-to-salesforce-connector"></a>Создание подключения к соединителю Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Использование триггера соединителя Salesforce
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Добавить условие
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Использование действия соединителя Salesforce
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>Технические сведения
Ниже приведены сведения о триггерах, действиях и ответах, которые поддерживает это подключение.

## <a name="salesforce-connector-triggers"></a>Триггеры соединителя Salesforce
Соединитель Salesforce предоставляет следующие триггеры.  

| Триггер | Описание |
| --- | --- |
| [При создании объекта](connectors-create-api-salesforce.md#when-an-object-is-created) |Активирует поток при создании объекта. |
| [При изменении объекта](connectors-create-api-salesforce.md#when-an-object-is-modified) |Активирует поток при изменении объекта. |

## <a name="salesforce-connector-actions"></a>Действия соединителя Salesforce
Соединитель Salesforce предоставляет следующие действия.

| Действие | Description (Описание) |
| --- | --- |
| [Получение объектов](connectors-create-api-salesforce.md#get-objects) |Эта операция получает объекты определенного типа, например "Lead". |
| [Создание объекта](connectors-create-api-salesforce.md#create-object) |Создает объект. |
| [Получение объекта](connectors-create-api-salesforce.md#get-object) |Получает объект. |
| [Удаление объекта](connectors-create-api-salesforce.md#delete-object) |Удаляет объект. |
| [Обновление объекта](connectors-create-api-salesforce.md#update-object) |Обновляет объект. |
| [Получение типов объекта](connectors-create-api-salesforce.md#get-object-types) |Выводит список доступных типов объектов. |

### <a name="action-details"></a>Сведения о действиях
Ниже приведены подробные сведения о действиях и триггерах этого соединителя, а также ответы на них.

### <a name="get-objects"></a>Получение объектов
Эта операция получает объекты определенного типа, например "Lead". 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта SalesForce, например "Lead" |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения количества записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="create-object"></a>Создание объекта
Создает объект. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта, например "Lead" |
| item* |Объект |Объект для создания |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |строка |

### <a name="get-object"></a>Получение объекта
Получает объект. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта SalesForce, например "Lead" |
| id* |Идентификатор объекта |Идентификатор объекта для получения |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |строка |

### <a name="delete-object"></a>Удаление объекта
Удаляет объект. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта, например "Lead" |
| id* |Идентификатор объекта |Идентификатор объекта для удаления |

Звездочка (*) означает, что свойство является обязательным.

### <a name="update-object"></a>Обновление объекта
Обновляет объект. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта, например "Lead" |
| id* |Идентификатор объекта |Идентификатор объекта для обновления |
| item* |Объект |Объект с измененными свойствами |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |string |

### <a name="when-an-object-is-created"></a>При создании объекта
Активирует поток при создании объекта. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта, например "Lead" |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения количества записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="when-an-object-is-modified"></a>При изменении объекта
Активирует поток при изменении объекта. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| table* |Тип объекта |Тип объекта, например "Lead" |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения количества записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="get-object-types"></a>Получение типов объекта
Выводит список доступных типов объектов. 

Для этого вызова параметры отсутствуют

#### <a name="output-details"></a>Сведения о выходных данных
TablesList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Ответы HTTP
Описанные выше действия и триггеры могут возвращать один или несколько кодов состояния HTTP, которые приведены ниже. 

| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 202 |Принято |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка. |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


