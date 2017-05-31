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
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 9bbf188b0080b8b1cf71423023645f54f1f823e5
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB. Создание приложения .NET с помощью API таблицы

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

В этом кратком руководстве описано создание учетной записи Azure Cosmos DB, а также создание таблицы в рамках этой учетной записи с помощью портала Azure. Затем вы напишете код для вставки, обновления и удаления сущностей, а также выполнения некоторых запросов. Вы можете скачать [пакет SDK предварительной версии для службы хранилища Azure](https://aka.ms/premiumtablenuget) на сайте NuGet. Он имеет те же классы и подписи метода, что и [пакет SDK для службы хранилища Azure](https://www.nuget.org/packages/WindowsAzure.Storage), но также может подключаться к учетным записям Azure Cosmos DB через [API таблицы](table-introduction.md) (предварительную версию). 

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Добавление таблицы

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

Теперь вы можете добавить данные в созданную таблицу с помощью обозревателя данных.

1. В обозревателе данных разверните **пример базы данных**, **пример таблицы**, затем щелкните **Сущности**, а после этого нажмите кнопку **Добавление сущности**.
2. Теперь добавьте данные в поля значений свойств PartitionKey и RowKey, а затем нажмите кнопку **Добавление сущности**.

   ![Создание документов в обозревателе данных на портале Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    Теперь вы можете добавить больше сущностей в свою таблицу, изменить эти сущности или выполнить запрос данных в обозревателе данных. В обозревателе данных вы можете масштабировать пропускную способность, а также добавлять хранимые процедуры, определенные пользователем функции и триггеры в свою таблицу.

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API DocumentDB из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

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

* Создание контейнера таблицы. Этот код очень похож на обычный пакет SDK для Хранилища таблиц Azure. 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и первичный ключ в файл app.config.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-documentdb-dotnet-core/keys.png)

2. В Visual Studio откройте файл app.config. 

3. Скопируйте имя учетной записи Azure Cosmos DB на портале и используйте его в качестве значения AccountName в значении строки PremiumStorageConnection файла app.config. На снимке экрана выше используется имя учетной записи cosmos-db-quickstart. Имя вашей учетной записи отобразится в верхней области портала.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Затем скопируйте значение первичного ключа с портала и добавьте его в качестве значения параметра AccountKey в PremiumStorageConnectionString. 

    `AccountKey=AUTHKEY`

5. Наконец, скопируйте значение универсального кода ресурса (URI) на странице ключей портала (с помощью кнопки копирования) и используйте его в качестве значения параметра TableEndpoint PremiumStorageConnectionString.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    StandardStorageConnectionString можно оставить без изменений.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="run-the-web-app"></a>Запуск веб-приложения

1. В Visual Studio в **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Управление пакетами NuGet**. 

2. В NuGet в поле **обзора** введите *WindowsAzure.Storage* и установите флажок для поля **Include prerelease** (Включить предварительные выпуски). 

3. В результатах установите библиотеку **WindowsAzure.Storage**. Установится предварительная версия пакета API таблицы Azure Cosmos DB, а также все зависимые компоненты.

4. Нажмите клавиши CTRL+F5 для запуска приложения.

    В окне консоли появятся данные, добавляемые в таблицу. После завершения сценария закройте окно консоли. 

Вернитесь в обозреватель данных, где вы можете просматривать, запрашивать и изменять новые данные, а также работать с ними. 

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, сделав следующее: 

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB и таблицу с помощью обозревателя данных, а также как запустить приложение.  Теперь вы можете выполнить запрос данных с помощью API таблиц.  

> [!div class="nextstepaction"]
> [Azure Cosmos DB: How to query with the Table API (preview)?](tutorial-query-table.md) (Azure Cosmos DB. Выполнение запроса с помощью API таблицы (предварительная версия))


