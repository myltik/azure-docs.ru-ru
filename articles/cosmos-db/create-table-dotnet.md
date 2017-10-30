---
title: "Создание приложения .NET Azure Cosmos DB с помощью API таблицы | Документация Майкрософт"
description: "Начните работать с API таблицами Azure Cosmos DB, используя приложение .NET"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.openlocfilehash: 0ce99a4754d7ec6f35bda63af6fc0166cf7e0eb4
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB. Создание приложения .NET с помощью API таблицы

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве описано создание учетной записи Azure Cosmos DB, а также создание таблицы в рамках этой учетной записи с помощью портала Azure. Затем нужно написать код для вставки, обновления и удаления сущностей, а также выполнить некоторые запросы с использованием нового пакета [Таблица службы хранилища Windows Azure класса Premium](https://aka.ms/premiumtablenuget) (предварительная версия) из NuGet. Эта библиотека включает те же классы и подписи методов, что и [пакет SDK для службы хранилища Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage). Кроме того, она может подключаться к учетным записям Azure Cosmos DB через [API таблиц](table-introduction.md) (предварительная версия). 

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Добавление таблицы

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

Теперь вы можете добавить данные в созданную таблицу с помощью обозревателя данных (предварительная версия).

1. В обозревателе данных разверните **пример таблицы**, затем щелкните **Сущности** и нажмите кнопку **Добавление сущности**.

   ![Создание сущностей в обозревателе данных на портале Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Теперь добавьте данные в поля значений свойств PartitionKey и RowKey, а затем нажмите кнопку **Добавление сущности**.

   ![Указание ключа секции и ключа строки для новой сущности](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Теперь вы можете добавить больше сущностей в свою таблицу, изменить эти сущности или выполнить запрос данных в обозревателе данных. В обозревателе данных вы можете масштабировать пропускную способность, а также добавлять хранимые процедуры, определенные пользователем функции и триггеры в свою таблицу.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение "Таблица" из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала Git, например Git Bash, и выполните команду `cd`, чтобы перейти в рабочий каталог.  

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Затем откройте файл решения в Visual Studio. 

## <a name="review-the-code"></a>Просмотр кода

Сделаем краткий обзор того, что происходит в приложении. Откройте файл Program.cs, и вы увидите, что эти строки кода создают ресурсы Azure Cosmos DB. 

* Инициализация CloudTableClient.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Создание таблицы, если она не существует.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Ряд действий выполняется в таблице с помощью класса `TableOperation`.

   ```csharp
   TableOperation insertOperation = TableOperation.Insert(item);
   table.Execute(insertOperation);
   ```
   
   ```csharp
   TableOperation retrieveOperation = TableOperation.Retrieve<T>(items[i].PartitionKey, items[i].RowKey);
   table.Execute(retrieveOperation);
   ```
   
   ```csharp
   TableOperation deleteOperation = TableOperation.Delete(items[i]);
   table.Execute(deleteOperation);
   ```


## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь мы обновим данные строки подключения, чтобы приложение могло взаимодействовать с Azure Cosmos DB. 

1. В Visual Studio откройте файл app.config. 

2. На [портале Azure](http://portal.azure.com/) в меню навигации Azure Cosmos DB слева щелкните **Строка подключения**. В новой области нажмите кнопку копирования для строки подключения. 

    ![Просмотр и копирование конечной точки и ключа учетной записи в области данных строки подключения](./media/create-table-dotnet/keys.png)

3. Вставьте значение в файл app.config для параметра PremiumStorageConnectionString. 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    StandardStorageConnectionString можно оставить без изменений.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="run-the-console-app"></a>Запуск консольного приложения

1. В **обозревателе решений** Visual Studio щелкните проект **PremiumTableGetStarted** правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**. 

2. В поле **Обзор** введите *WindowsAzure.Storage-PremiumTable*.

3. Установите флажок **Включить предварительные выпуски**. 

4. Получив результаты, установите библиотеку **WindowsAzure.Storage-PremiumTable**. Установится предварительная версия пакета API таблицы Azure Cosmos DB, а также все зависимые компоненты. Обратите внимание, что этот пакет NuGet отличается от пакета службы хранилища Windows Azure, используемого хранилищем таблиц Azure. 

5. Нажмите клавиши CTRL+F5 для запуска приложения.

    В окне консоли отобразятся добавленные в таблицу, полученные, запрашиваемые, замененные и удаленные из таблицы данные. После выполнения скрипта закройте окно консоли, нажав любую клавишу. 
    
    ![Выходные данные этого краткого руководства, отображаемые в консоли](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. Чтобы увидеть новые сущности в обозревателе данных, закомментируйте строки 188–208 в файле program.cs (так они не будут удалены) и запустите пример снова. 

    Теперь вернитесь в обозреватель данных, нажмите кнопку **Обновить**, разверните таблицу **Пользователи**, щелкните **Сущности** и начинайте работу с новыми данными. 

    ![Новые сущности в обозревателе данных](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее: 

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB и таблицу с помощью обозревателя данных, а также как запустить приложение.  Теперь вы можете выполнить запрос данных с помощью API таблиц.  

> [!div class="nextstepaction"]
> [Azure Cosmos DB. Выполнение запроса с помощью API таблицы (предварительная версия)](tutorial-query-table.md)

