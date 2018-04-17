---
title: Создание функции, активируемой с помощью Azure Cosmos DB | Документация Майкрософт
description: Используйте службу "Функции Azure" для создания бессерверной функции, которая вызывается при добавлении данных в базу данных в Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 60836bd88335dc5c58423bad94b6a95e9782c474
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Создание функции, активируемой с помощью Azure Cosmos DB

Узнайте, как создать функцию, которая активируется при добавлении или изменении данных в Azure Cosmos DB. Дополнительные сведения об Azure Cosmos DB см. в статье [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](..\cosmos-db\serverless-computing-database.md).

![Просмотр сообщения в журналах](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Создание триггера Azure Cosmos DB

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, выберите **Пользовательская функция**. Откроется полный набор шаблонов функций.

    ![Страница быстрого начала работы с функциями на портале Azure](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. В поле поиска введите `cosmos` и выберите нужный язык для шаблона триггера Azure Cosmos DB.

    ![Выбор триггера Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Настройте новый триггер с параметрами, как указано в таблице под изображением.

    ![Создание функции, активируемой с помощью Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Параметр      | Рекомендуемое значение  | Описание                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Имя** | значение по умолчанию | Используйте имя функции по умолчанию, предложенное шаблоном. |
    | **Имя коллекции** | Items | Имя коллекции для отслеживания. |
    | **Create lease collection if it doesn't exist** (Создать коллекцию аренд, если она не существует) | Флажок установлен | Коллекция не существует, создайте ее. |
    | **Database name** (Имя базы данных) | Задачи | Имя базы данных с коллекцией для отслеживания. |

4. Выберите **Создать** рядом с надписью **Azure Cosmos DB account connection** (Подключение к учетной записи Azure Cosmos DB). Затем выберите существующую учетную запись или **+ Создать**. 
 
    ![Настройка подключения к Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. При создании новой учетной записи Cosmos DB используйте параметры **новой учетной записи**, указанные в таблице.

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Идентификатор** | Имя базы данных | Уникальный идентификатор для базы данных Azure Cosmos DB  |
    | **API** | SQL | В этом разделе используются API SQL.  |
    | **Подписка** | Подписка Azure | Подписка, в которой создана учетная запись Cosmos DB.  |
    | **Группа ресурсов** | myResourceGroup |  Используйте имеющуюся группу ресурсов, которая содержит функцию-приложение. |
    | **Местоположение.**  | WestEurope | Выберите расположение рядом с приложением-функцией или другими приложениями, которые используют хранимые документы.  |

6. Нажмите кнопку **ОК**, чтобы создать базу данных. Создание базы данных может занять несколько минут. После создания базы данных строка подключения к базе данных сохраняется в качестве параметра функции-приложения. Имя параметра приложения вставляется в строку **подключения к учетной записи Azure Cosmos DB**. 

7. Нажмите кнопку **Создать**, чтобы создать функцию, активируемую с помощью Azure Cosmos DB. После создания функции отображается код функции на основе шаблона.  

    ![Шаблон функции Cosmos DB на C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Этот шаблон функции записывает в журналы количество документов и идентификатор первого документа. 

Затем подключитесь к учетной записи Azure Cosmos DB и создайте в базе данных коллекцию **Задачи**. 

## <a name="create-the-items-collection"></a>Создание коллекции элементов

1. Откройте второй экземпляр [портала Azure](https://portal.azure.com) на новой вкладке в браузере. 

2. В левой части портала разверните панель значков, введите `cosmos` в поле поиска и выберите **Azure Cosmos DB**.

    ![Поиск службы Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Выберите учетную запись Azure Cosmos DB, а затем выберите **обозреватель данных**. 
 
3. В разделе **Коллекции** выберите **taskDatabase** и **Новая коллекция**.

    ![Создание коллекции](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. На странице **Добавить коллекцию** используйте параметры, перечисленные в таблице под изображением. 
 
    ![Определение taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Параметр|Рекомендуемое значение|ОПИСАНИЕ |
    | ---|---|--- |
    | **Идентификатор базы данных** | Задачи |Имя новой базы данных. Должен соответствовать имени, заданному в привязке функции. |
    | **Идентификатор коллекции** | Items | Имя новой коллекции. Должен соответствовать имени, заданному в привязке функции.  |
    | **Емкость хранилища** | Фиксированный (10 ГБ)|Используйте значение по умолчанию. Это значение представляет емкость хранилища базы данных. |
    | **Пропускная способность** |400 ЕЗ| Используйте значение по умолчанию. Чтобы сократить задержку, позже вы можете увеличить масштаб пропускной способности. |
    | **[Ключ секции](../cosmos-db/partition-data.md#design-for-partitioning)** | /category|Ключ секции, который равномерно распределяет данные в каждой секции. Для создания высокопроизводительной коллекции важно выбрать правильный ключ раздела. | 

1. Нажмите кнопку **ОК** для создания коллекции **Задачи**. Процесс создания коллекции может занять некоторое время.

После создания коллекции, указанной в привязке функции, можно протестировать функцию, добавив в новую коллекцию документы.

## <a name="test-the-function"></a>Проверка функции

1. Разверните новую коллекцию **taskCollection** в обозревателе данных, выберите **Documents** (Документы), а затем — **Новый документ**.

    ![Создание документа в taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Замените содержимое нового документа следующим содержимым, а затем выберите **Сохранить**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. На портале переключитесь на первую вкладку браузера, содержащую функцию. Разверните журналы функций и убедитесь, что новый документ активировал функцию. На экране будет видно, что значение идентификатора документа `task1` записано в журналы. 

    ![Просмотр сообщения в журналах](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Необязательно.) Перейдите обратно к документу, внесите изменения и нажмите кнопку **Изменить**. Затем вернитесь к журналам функций и убедитесь, что изменение также активировало функцию.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали функцию, которая запускается при добавлении или изменении документов в Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах Azure Cosmos DB см. в статье [Привязки Azure Cosmos DB для службы "Функции Azure"](functions-bindings-cosmosdb.md).
