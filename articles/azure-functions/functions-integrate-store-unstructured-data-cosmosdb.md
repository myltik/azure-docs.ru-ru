---
title: Хранение неструктурированных данных с помощью Azure Cosmos DB и службы "Функции" | Документация Майкрософт
description: Хранение неструктурированных данных с помощью служб Функции Azure и Cosmos DB
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, Cosmos DB, динамические вычисления, бессерверная архитектура
ms.assetid: ''
ms.service: functions
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: b9bb71adf85490fe68bf6b73133017c5e9c377e1
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2018
ms.locfileid: "27767559"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Хранение неструктурированных данных с помощью Azure Cosmos DB и службы "Функции Azure"

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) — это отличный способ хранения неструктурированных данных и данных в формате JSON. В сочетании с функциями Azure Cosmos DB позволяет быстро и просто сохранять данные, используя код гораздо меньшего объема, чем требуется для хранения данных в реляционной базе данных.

> [!NOTE]
> Сейчас триггер, входные и выходные привязки Azure Cosmos DB работают только с учетными записями API SQL и API Graph.

В Функциях Azure входные и выходные привязки предоставляют декларативный способ подключения данных внешних служб к функции. В этой статье вы узнаете, как обновить имеющуюся функцию C#, чтобы добавить выходную привязку, которая сохраняет неструктурированные данные в документе Cosmos DB. 

![База данных Cosmos](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-cosmosdb.png)

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="add-an-output-binding"></a>Добавление выходной привязки

1. Разверните ваше приложение-функцию и функцию.

1. Выберите элементы **Интегрировать** и **Новые выходные данные**, которые находятся в верхней правой части страницы. Выберите **Azure Cosmos DB** и щелкните **Выбрать**.

    ![Добавление выходной привязки Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

3. Используйте параметры **Azure Cosmos DB output** (Выходные данные Azure Cosmos DB) как указано в таблице: 

    ![Настройка выходной привязки Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Имя параметра документа** | taskDocument | Имя, которое ссылается на объект Cosmos DB в коде. |
    | **Database name** (Имя базы данных) | taskDatabase | Имя базы данных для сохранения документов. |
    | **Имя коллекции** | TaskCollection | Имя коллекции базы данных. |
    | **If true, creates the Cosmos DB database and collection** (Если значение равно true, создается база данных Cosmos DB и коллекция) | Флажок установлен | Коллекция не существует, создайте ее. |

4. Выберите **Новый** рядом с надписью **Azure Cosmos DB document connection** (Подключение к учетной записи Cosmos DB) и щелкните **Создать**. 

5. Используйте параметры **Новая учетная запись**, как указано в таблице: 

    ![Настройка подключения к Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Идентификатор** | Имя базы данных | Уникальный идентификатор для базы данных Azure Cosmos DB  |
    | **API** | SQL | Выберите API SQL. Сейчас триггер, входные и выходные привязки Azure Cosmos DB работают только с учетными записями API SQL и API Graph. |
    | **Подписка** | Подписка Azure | Подписка Azure  |
    | **Группа ресурсов** | myResourceGroup |  Используйте имеющуюся группу ресурсов, которая содержит функцию-приложение. |
    | **Местоположение.**  | WestEurope | Выберите расположение рядом с приложением-функцией или другими приложениями, которые используют хранимые документы.  |

6. Нажмите кнопку **ОК**, чтобы создать базу данных. Создание базы данных может занять несколько минут. После создания базы данных строка подключения к базе данных сохраняется в качестве параметра функции-приложения. Имя параметра приложения вставляется в строку **подключения к учетной записи Azure Cosmos DB**. 
 
8. После задания строки подключения выберите **Сохранить**, чтобы создать привязку.

## <a name="update-the-function-code"></a>Обновление кода функции

Замените имеющийся код функции C# следующим кодом:

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```
Этот пример кода считывает строки HTTP-запроса и назначает их в качестве полей объекта `taskDocument`. Привязка `taskDocument` отправляет данные объекта из этого параметра привязки для их сохранения в связанной базе данных документов. База данных создается при первом запуске функции.

## <a name="test-the-function-and-database"></a>Тестирование функции и базы данных

1. Разверните окно справа и выберите **Тест**. В разделе **Запрос** щелкните **Добавить параметр**, чтобы добавить следующие параметры в строку запроса:

    + `name`
    + `task`
    + `duedate`

2. Щелкните **Выполнить**. Должно быть возвращено состояние 200.

    ![Настройка выходной привязки Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

1. В левой части портала Azure разверните панель значков, введите `cosmos` в поле поиска и выберите **Azure Cosmos DB**.

    ![Поиск службы Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png)

2. Выберите учетную запись Azure Cosmos DB, а затем выберите **обозреватель данных**. 

3. Разверните узлы **Коллекции**, выберите новый документ и убедитесь, что документ содержит ваши значения строки запроса, а также некоторые дополнительные метаданные. 

    ![Проверка записи Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Вы успешно добавили привязку к триггеру HTTP, которая сохраняет неструктурированные данные в Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о привязке к базе данных Cosmos DB см. в статье [Привязки Cosmos DB в Функциях Azure](functions-bindings-cosmosdb.md).
