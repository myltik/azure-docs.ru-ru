---
title: "ProjectOnline | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Project Online — это гибкое интернет-решение для управления портфелем проектов (PPM) и повседневной работы с продуктами Майкрософт. Project Online предоставляется в рамках служб Office 365. Это решение позволяет организациям быстро приступить к работе с эффективными функциями управления проектами для планирования, определения приоритетов и управления проектами и инвестициями в портфель проектов в режиме повсеместной доступности."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-projectonline-connector"></a>Начало работы с соединителем ProjectOnline
Project Online — это гибкое интернет-решение для управления портфелем проектов (PPM) и повседневной работы с продуктами Майкрософт. Project Online предоставляется в рамках служб Office 365. Это решение позволяет организациям быстро приступить к работе с эффективными функциями управления проектами для планирования, определения приоритетов и управления проектами и инвестициями в портфель проектов в режиме повсеместной доступности.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview. 
> 
> 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель ProjectOnline можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML. 

 Соединитель ProjectOnline предоставляет следующие триггеры и действия:

### <a name="projectonline-actions"></a>Действия ProjectOnline
Вы можете выполнять перечисленные ниже действия:

| Действие | Описание |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |Список проектов на сайте Project Online |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |Создание проекта на сайте Project Online |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |Создание задачи в проекте |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |Создание корпоративных ресурсов на сайте Project Online |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |Список опубликованных задач в проекте |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |Извлечение проекта на сайте |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |Возврат и публикация существующего проекта на сайте |

### <a name="projectonline-triggers"></a>Триггеры ProjectOnline
Можно прослушивать указанные ниже события:

| Триггер | Description (Описание) |
| --- | --- |
| При создании проекта |Запускает поток при создании нового проекта |
| При создании ресурса |Запускает новый поток при создании нового ресурса |
| При создании новой задачи |Запускает поток при создании новой задачи |

## <a name="create-a-connection-to-projectonline"></a>Создание подключения к ProjectOnline
Для создания приложений логики с помощью ProjectOnline необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные ProjectOnline |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> Это подключение можно использовать в других приложениях логики.
> 
> 

## <a name="reference-for-projectonline"></a>Справочник по ProjectOnline
Относится к версии 1.0.

## <a name="onnewproject"></a>OnNewProject
При создании проекта: запуск потока при создании нового проекта 

```GET: /trigger/_api/ProjectData/Projects``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="onnewresource"></a>OnNewResource
При создании ресурса: запуск нового потока при создании нового ресурса 

```GET: /trigger/_api/ProjectData/Resources``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="onnewtask"></a>OnNewTask
При создании задачи: запуск потока при создании новой задачи 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="listprojects"></a>ListProjects
Список проектов: список проектов на сайте Project Online 

```GET: /_api/ProjectServer/Projects``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="createproject"></a>CreateProject
Создание проекта: создание проекта на сайте Project Online 

```POST: /_api/ProjectServer/Projects``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| proj | |Да |текст |Нет |Создаваемый проект |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="createtask"></a>CreateTask
Создание задачи: создание новой задачи в проекте 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| project_id |string |Да |path |Нет |Уникальный идентификатор проекта, в который добавляется задача |
| task | |Да |текст |Нет |Новая задача для добавления в проект |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="createresource"></a>CreateResource
Создание ресурса: создание корпоративных ресурсов на сайте Project Online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| resource | |Да |текст |Нет |Новые корпоративные ресурсы для добавления в проект |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="listtasks"></a>ListTasks
Список задач: список опубликованных задач в проекте 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| project_id |string |Да |path |Нет |Уникальный идентификатор проекта для получения задач |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="checkoutproject"></a>CheckoutProject
Извлечение проекта: извлечение проекта на сайте 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| project_id |string |Да |path |Нет |Уникальный идентификатор проекта, в который добавляется задача |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="publishproject"></a>PublishProject
Возврат и публикация проекта: возврат и публикация существующего проекта на сайте 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| siteUrl |string |Да |запрос |Нет |URL-адрес корневого сайта для проекта сайта (пример, https://sampletenant.sharepoint.com/teams/sampleteam). |
| project_id |string |Да |path |Нет |Уникальный идентификатор возвращаемого проекта |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| value |array |Нет |

### <a name="triggerproject"></a>TriggerProject
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| ProjectStartDate |string |Нет |
| ProjectFinishDate |string |Нет |
| ProjectCreatedDate |string |Нет |
| ProjectId |string |Нет |
| ProjectModifiedDate |string |Нет |
| ProjectType |целое число |Нет |
| ProjectName |string |Нет |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| value |array |Нет |

### <a name="triggerresource"></a>TriggerResource
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| ResourceId |string |Нет |
| ResourceBaseCalendar |string |Нет |
| ResourceBookingType |целое число |Нет |
| ResourceCanLevel |Логическое |Нет |
| ResourceCostPerUse |number |Нет |
| ResourceCreatedDate |string |Нет |
| ResourceEarliestAvailableFrom |string |Нет |
| ResourceEmail |string |Нет |
| ResourceInitials |string |Нет |
| ResourceIsActive |Логическое |Нет |
| ResourceIsGeneric |Логическое |Нет |
| ResourceLatestAvailableTo |string |Нет |
| ResourceModifiedDate |string |Нет |
| ResourceName |string |Нет |
| ResourceStatusName |string |Нет |
| ResourceType |целое число |Нет |
| TypeDescription |string |Нет |
| TypeName |string |Нет |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| value |array |Нет |

### <a name="triggertask"></a>TriggerTask
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| ProjectId |string |Нет |
| TaskId |строка |Нет |
| ProjectName |string |Нет |
| TaskName |string |Нет |
| TaskCreatedDate |string |Нет |
| TaskModifieddate |string |Нет |
| TaskStartDate |string |Нет |
| TaskFinishDate |string |Нет |
| TaskPriority |целое число |Нет |
| TaskIsActive |Логическое |Нет |

### <a name="newproject"></a>NewProject
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Имя |string |Да |
| Description (Описание) |string |Нет |
| Начало |string |Нет |

### <a name="newreource"></a>NewResource
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Имя |string |Да |
| IsBudget |Логическое |Нет |
| IsGeneric |Логическое |Нет |
| IsInactive |Логическое |Нет |

### <a name="project"></a>Project
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| ApprovedStart |string |Нет |
| ApprovedEnd |string |Нет |
| CheckedOutDate |string |Нет |
| CheckOutDescription |string |Нет |
| CheckOutId |string |Нет |
| CreatedDate |string |Нет |
| Идентификатор |string |Нет |
| IsCheckedOut |Логическое |Нет |
| LastPublishedDate |string |Нет |
| LastSavedDate |string |Нет |
| OptimizerDecision |целое число |Нет |
| PlannerDecision |целое число |Нет |
| ProjectType |целое число |Нет |
| Имя |string |Нет |
| WinprojVersion |string |Нет |

### <a name="projectswrapper"></a>ProjectsWrapper
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| value |array |Нет |

### <a name="newtask"></a>NewTask
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| parameters |не определено |Да |

### <a name="taskparameters"></a>TaskParameters
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Имя |string |Да |
| Примечания |string |Нет |
| Начало |string |Нет |
| Длительность |string |Нет |

### <a name="enterpriseresource"></a>EnterpriseResource
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| CanLevel |Логическое |Нет |
| Код |string |Нет |
| CostAccrual |целое число |Нет |
| CostCenter |string |Нет |
| Создано |string |Нет |
| DefaultBookingType |целое число |Нет |
| Email |string |Нет |
| ExternalId |string |Нет |
| Группа |string |Нет |
| HireDate |string |Нет |
| Идентификатор |string |Нет |
| Initials |string |Нет |
| IsActive |Логическое |Нет |
| IsBudget |Логическое |Нет |
| IsCheckedOut |Логическое |Нет |
| IsGeneric |Логическое |Нет |
| IsTeam |Логическое |Нет |
| MaterialLabel |string |Нет |
| Изменено |string |Нет |
| Имя |string |Нет |
| Phonetics |string |Нет |
| ResourceType |целое число |Нет |
| TerminationDate |string |Нет |

### <a name="taskswrapper"></a>TasksWrapper
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| value |array |Нет |

### <a name="task"></a>Задача
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Создано |string |Нет |
| Изменено |string |Нет |
| Начало |string |Нет |
| Готово |string |Нет |
| Имя |string |Нет |
| Идентификатор |string |Нет |
| Приоритет |целое число |Нет |
| PercentComplete |целое число |Нет |
| Примечания |string |Нет |
| Контакт |string |Нет |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)


