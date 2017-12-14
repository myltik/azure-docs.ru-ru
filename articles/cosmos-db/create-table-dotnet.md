---
title: "Краткое руководство. Использование API таблицы с .NET в Azure Cosmos DB | Документация Майкрософт"
description: "В этом руководстве показано, как использовать API таблицы Azure Cosmos DB для создания приложения с помощью портала Azure и .NET"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 44637049dd5d6cfe353afe98427d843a0d4e403a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Краткое руководство. Создание приложения API таблицы с помощью .NET и Azure Cosmos DB 

В этом руководстве показано, как использовать .NET и [API таблицы](table-introduction.md) Azure Cosmos DB для создания приложения путем клонирования примера с сайта GitHub. Кроме того, здесь показано, как создать учетную запись Azure Cosmos DB и использовать обозреватель данных для создания таблиц и сущностей на веб-портале Azure.

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

> [!IMPORTANT] 
> Для работы с общедоступными пакетами SDK для API таблиц нужно создать новую учетную запись API таблиц. Учетные записи API таблиц, созданные во время работы с предварительной версией, не поддерживаются в общедоступных пакетах SDK.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Добавление таблицы

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

Теперь вы можете добавить данные в созданную таблицу с помощью обозревателя данных.

1. В обозревателе данных разверните **пример таблицы**, затем щелкните **Сущности** и нажмите кнопку **Добавление сущности**.

   ![Создание сущностей в обозревателе данных на портале Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Добавьте данные в поля значений свойств PartitionKey и RowKey, а затем нажмите кнопку **Добавить сущность**.

   ![Указание ключа секции и ключа строки для новой сущности](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Теперь вы можете добавить больше сущностей в свою таблицу, изменить эти сущности или выполнить запрос данных в обозревателе данных. В обозревателе данных вы можете масштабировать пропускную способность, а также добавлять хранимые процедуры, определенные пользователем функции и триггеры в свою таблицу.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение "Таблица" из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в папку для установки примера приложения. 

    ```bash
    cd "C:\git-samples"
    ```

2. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Затем откройте файл решения TableStorage в Visual Studio. 

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Так вы обеспечите обмен данными между приложением и размещенной базой данных. 

1. На [портале Azure](http://portal.azure.com/) щелкните **Строка подключения**. 

    Используйте кнопки копирования в правой части экрана, чтобы скопировать основную строку подключения.

    ![Просмотр и копирование основной строки подключения на панели строки подключения](./media/create-table-dotnet/connection-string.png)

2. В Visual Studio откройте файл app.config. 

3. В этом руководстве не используется эмулятор хранения. Раскомментируйте элемент StorageConnectionString в строке 8 и закомментируйте элемент StorageConnectionString в строке 7. После этого строки 7 и 8 будут выглядеть так:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Вставьте значение основной строки подключения, полученное на портале, вместо значения параметра StorageConnectionString в строке 8. Значение нужно вставлять в кавычках. 

    > [!IMPORTANT]
    > Если для вашей конечной точки указан адрес documents.azure.com, это значит, что вы используете учетную запись для предварительной версии. В этом случае для работы с общедоступными пакетами SDK для API таблиц вам нужно создать [новую учетную запись API таблиц](#create-a-database-account). 
    > 

    Теперь строка 8 должна выглядеть примерно так:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Сохраните файл App.config.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="build-and-deploy-the-app"></a>Создание и развертывание приложения

1. Щелкните правой кнопкой мыши проект **TableStorage** в **обозревателе решений** Visual Studio и выберите **Управление пакетами NuGet**. 

2. В поле **обзора** NuGet введите *Microsoft.Azure.CosmosDB.Table*.

3. В результатах поиска найдите библиотеку **Microsoft.Azure.CosmosDB.Table** и установите ее. Установится пакет API таблицы Azure Cosmos DB, а также все зависимые компоненты.

4. Откройте файл BasicSamples.cs и добавьте точку останова в строки 30 и 52.

5. Нажмите клавиши CTRL+F5 для запуска приложения.

    В окне консоли отобразятся данные таблицы, добавленные к новой базе данных таблиц в Azure Cosmos DB. 
    
    Если возникнет ошибка о зависимостях, изучите раздел об [устранении неполадок](table-sdk-dotnet.md#troubleshooting).

    Когда будет достигнута первая точка останова, вернитесь в обозреватель данных на портале Azure, разверните созданный пример* таблицы и щелкните **Сущности**. На вкладке **Сущности** справа отобразится новая добавленная сущность. Обратите внимание, что для пользователя указан номер телефона 425-555-0101.
    
6. Закройте вкладку "Сущности" в обозревателе данных.
    
7. Приложение будет выполняться до следующей точки останова.

    Когда будет достигнута точка останова, снова щелкните "Сущности" на портале, чтобы открыть соответствующую вкладку. Обратите внимание, что номер телефона изменился на 425-555-0105.

8. Вернитесь в окно консоли, нажмите клавиши CTRL+C, чтобы завершить работу приложения. 

    Теперь можно вернуться в обозреватель данных, чтобы добавить или изменить сущности и запросить данные.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB и таблицу с помощью обозревателя данных, а также как запустить приложение.  Теперь вы можете выполнить запрос данных с помощью API таблиц.  

> [!div class="nextstepaction"]
> [Импорт данных таблиц в API таблицы](table-import.md)

