.<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Создание приложений логики с помощью службы приложений Azure. Project Online — это гибкое интернет-решение для управления портфелем проектов (PPM) и повседневной работы с продуктами Майкрософт. Project Online предоставляется в рамках служб Office 365. Это решение позволяет организациям быстро приступить к работе с эффективными функциями управления проектами для планирования, определения приоритетов и управления проектами и инвестициями в портфель проектов в режиме повсеместной доступности."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Начало работы с соединителем ProjectOnline

Project Online — это гибкое интернет-решение для управления портфелем проектов (PPM) и повседневной работы с продуктами Майкрософт. Project Online предоставляется в рамках служб Office 365. Это решение позволяет организациям быстро приступить к работе с эффективными функциями управления проектами для планирования, определения приоритетов и управления проектами и инвестициями в портфель проектов в режиме повсеместной доступности.

>[AZURE.NOTE] Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.

Для начала можно создать приложение логики, как указано в соответствующей [статье](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Триггеры и действия

Соединитель ProjectOnline можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML.

 Соединитель ProjectOnline предоставляет следующие триггеры и действия:

### Действия ProjectOnline
Вы можете выполнять перечисленные ниже действия:

|Действие|Description (Описание)|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Список проектов на сайте Project Online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Создание проекта на сайте Project Online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Создание задачи в проекте|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Создание корпоративных ресурсов на сайте Project Online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Список опубликованных задач в проекте|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Извлечение проекта на сайте|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Возврат и публикация существующего проекта на сайте|
### Триггеры ProjectOnline
Можно прослушивать указанные ниже события:

|Триггер | Description (Описание)|
|--- | ---|
|При создании проекта|Запускает поток при создании нового проекта|
|При создании ресурса|Запускает новый поток при создании нового ресурса|
|При создании новой задачи|Запускает поток при создании новой задачи|


## Создание подключения к ProjectOnline
Для создания приложений логики с помощью ProjectOnline необходимо создать **подключение**, а затем указать данные для следующих свойств:

|Свойство| Обязательно|Description (Описание)|
| ---|---|---|
|Маркер|Да|Укажите учетные данные ProjectOnline|

>[AZURE.INCLUDE [Шаги по созданию подключения к ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Это подключение можно использовать в других приложениях логики.

## Справочник по ProjectOnline
Относится к версии 1.0.

## OnNewProject
При создании проекта: запуск потока при создании нового проекта

```GET: /trigger/_api/ProjectData/Projects```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## OnNewResource
При создании ресурса: запуск нового потока при создании нового ресурса

```GET: /trigger/_api/ProjectData/Resources```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## OnNewTask
При создании задачи: запуск потока при создании новой задачи

```GET: /trigger/_api/ProjectData/Tasks```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## ListProjects
Список проектов: список проектов на сайте Project Online

```GET: /_api/ProjectServer/Projects```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## CreateProject
Создание проекта: создание проекта на сайте Project Online

```POST: /_api/ProjectServer/Projects```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|proj| |Да|текст|Нет|Создаваемый проект|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## CreateTask
Создание задачи: создание новой задачи в проекте

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|project\_id|string|Да|path|Нет|Уникальный идентификатор проекта, в который добавляется задача|
|task| |Да|текст|Нет|Новая задача для добавления в проект|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## CreateResource
Создание ресурса: создание корпоративных ресурсов на сайте Project Online

```POST: /_api/ProjectServer/EnterpriseResources```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|resource| |Да|текст|Нет|Новые корпоративные ресурсы для добавления в проект|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## ListTasks
Список задач: список опубликованных задач в проекте

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks```

| Имя| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Description (Описание)|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|project\_id|string|Да|path|Нет|Уникальный идентификатор проекта для получения задач|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## CheckoutProject
Извлечение проекта: извлечение проекта на сайте

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Description (Описание)|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|project\_id|string|Да|path|Нет|Уникальный идентификатор проекта, в который добавляется задача|

#### Ответ

|Name (Имя)|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## PublishProject
Возврат и публикация проекта: возврат и публикация существующего проекта на сайте

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)```

| Name (Имя)| Тип данных|Обязательно|Местонахождение|Значение по умолчанию|Описание|
| ---|---|---|---|---|---|
|siteUrl|string|Да|запрос|Нет|URL-адрес корневого сайта проекта (например, https://sampletenant.sharepoint.com/teams/sampleteam).|
|project\_id|string|Да|path|Нет|Уникальный идентификатор возвращаемого проекта|

#### Ответ

|Имя|Описание|
|---|---|
|200|ОК|
|400|Ошибка запроса|
|401|Не авторизовано|
|403|Запрещено|
|404|Не найдено|
|500|Внутренняя ошибка сервера. Произошла неизвестная ошибка|
|по умолчанию|Операция завершилась ошибкой.|


## Определения объектов 

### TriggerProjectsWrapper


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|value|array|Нет |



### TriggerProject


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|ProjectStartDate|string|Нет |
|ProjectFinishDate|string|Нет |
|ProjectCreatedDate|string|Нет |
|ProjectId|string|Нет |
|ProjectModifiedDate|string|Нет |
|ProjectType|целое число|Нет |
|ProjectName|string|Нет |



### TriggerResourcesWrapper


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|value|array|Нет |



### TriggerResource


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|ResourceId|string|Нет |
|ResourceBaseCalendar|string|Нет |
|ResourceBookingType|целое число|Нет |
|ResourceCanLevel|Логическое|Нет |
|ResourceCostPerUse|number|Нет |
|ResourceCreatedDate|string|Нет |
|ResourceEarliestAvailableFrom|string|Нет |
|ResourceEmail|string|Нет |
|ResourceInitials|string|Нет |
|ResourceIsActive|Логическое|Нет |
|ResourceIsGeneric|Логическое|Нет |
|ResourceLatestAvailableTo|string|Нет |
|ResourceModifiedDate|string|Нет |
|ResourceName|string|Нет |
|ResourceStatusName|string|Нет |
|ResourceType|целое число|Нет |
|TypeDescription|string|Нет |
|TypeName|string|Нет |



### TriggerTasksWrapper


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|value|array|Нет |



### TriggerTask


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|ProjectId|string|Нет |
|TaskId|строка|Нет |
|ProjectName|string|Нет |
|TaskName|string|Нет |
|TaskCreatedDate|string|Нет |
|TaskModifieddate|string|Нет |
|TaskStartDate|string|Нет |
|TaskFinishDate|string|Нет |
|TaskPriority|целое число|Нет |
|TaskIsActive|Логическое|Нет |



### NewProject


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|Имя|string|Да |
|Description (Описание)|string|Нет |
|Начало|string|Нет |



### NewResource


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|Имя|string|Да |
|IsBudget|Логическое|Нет |
|IsGeneric|Логическое|Нет |
|IsInactive|Логическое|Нет |



### Project


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|ApprovedStart|string|Нет |
|ApprovedEnd|string|Нет |
|CheckedOutDate|string|Нет |
|CheckOutDescription|string|Нет |
|CheckOutId|string|Нет |
|CreatedDate|string|Нет |
|Идентификатор|string|Нет |
|IsCheckedOut|Логическое|Нет |
|LastPublishedDate|string|Нет |
|LastSavedDate|string|Нет |
|OptimizerDecision|целое число|Нет |
|PlannerDecision|целое число|Нет |
|ProjectType|целое число|Нет |
|Name (Имя)|string|Нет |
|WinprojVersion|string|Нет |



### ProjectsWrapper


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|value|array|Нет |



### NewTask


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|parameters|не определено|Да |



### TaskParameters


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|Name (Имя)|string|Да |
|Примечания|string|Нет |
|Начало|string|Нет |
|Длительность|string|Нет |



### EnterpriseResource


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|CanLevel|Логическое|Нет |
|Код|string|Нет |
|CostAccrual|целое число|Нет |
|CostCenter|string|Нет |
|Создано|string|Нет |
|DefaultBookingType|целое число|Нет |
|Email|string|Нет |
|ExternalId|string|Нет |
|Группа|string|Нет |
|HireDate|string|Нет |
|Идентификатор|string|Нет |
|Initials|string|Нет |
|IsActive|Логическое|Нет |
|IsBudget|Логическое|Нет |
|IsCheckedOut|Логическое|Нет |
|IsGeneric|Логическое|Нет |
|IsTeam|Логическое|Нет |
|MaterialLabel|string|Нет |
|Изменено|string|Нет |
|Имя|string|Нет |
|Phonetics|string|Нет |
|ResourceType|целое число|Нет |
|TerminationDate|string|Нет |



### TasksWrapper


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|value|array|Нет |



### Задача


| Имя свойства | Тип данных | Обязательно |
|---|---|---|
|Создано|string|Нет |
|Изменено|string|Нет |
|Начало|string|Нет |
|Готово|string|Нет |
|Имя|string|Нет |
|Идентификатор|string|Нет |
|Приоритет|целое число|Нет |
|PercentComplete|целое число|Нет |
|Примечания|string|Нет |
|Контакт|string|Нет |


## Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->