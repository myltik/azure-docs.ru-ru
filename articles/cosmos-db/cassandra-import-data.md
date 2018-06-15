---
title: Импорт данных Cassandra в Azure Cosmos DB | Документация Майкрософт
description: Сведения об использовании команды CQL Copy для копирования данных Cassandra в Azure Cosmos DB.
services: cosmos-db
author: govindk
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 26731d80f5917f9d21aacafb5f8a79cfb02855af
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795080"
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: импорт данных Cassandra

Это руководство содержит инструкции по импорту данных Cassandra в Azure Cosmos DB с помощью команды COPY языка запросов Cassandra (CQL). 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Получение строки подключения
> * Импорт данных командой cqlsh COPY
> * Импорт с помощью соединителя Spark 

# <a name="prerequisites"></a>предварительным требованиям

* Установите [Apache Cassandra](http://cassandra.apache.org/download/) и убедитесь, что присутствует *cqlsh*.
* Увеличьте пропускную способность. Продолжительность переноса данных зависит от пропускной способности, которую вы предоставите для таблиц. Увеличьте пропускную способность для крупных миграций. После переноса уменьшите пропускную способность для экономии расходов. Дополнительные сведения об увеличении пропускной способности с помощью [портала Azure](https://portal.azure.com) см. в статье [Настройка пропускной способности для коллекций Azure Cosmos DB](set-throughput.md).
* Включите SSL. В Azure Cosmos DB строгие требования к безопасности и стандарты. Обязательно включите SSL при взаимодействии с учетной записью. При использовании CQL с SSH у вас есть возможность предоставить сведения SSL. 

## <a name="find-your-connection-string"></a>Получение строки подключения

1. На [портале Azure](https://portal.azure.com) слева щелкните **Azure Cosmos DB**.

2. На панели **Подписки** выберите имя своей учетной записи.

3. Щелкните **Строка подключения**. На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.

    ![Страница строки подключения](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Использование cqlsh COPY

Чтобы импортировать в Azure Cosmos DB данные Cassandra, которые можно использовать с API-интерфейсом Cassandra, следуйте инструкциям ниже.

1. Войдите в cqhsh, используя полученные на портале сведения о подключении.
2. Используйте [команду CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh), чтобы скопировать локальные данные в конечную точку API-интерфейса Apache Cassandra. Убедитесь, что исходный и целевой объекты находятся в одном центре обработки данных, чтобы свести к минимуму проблемы задержки.

### <a name="guide-for-moving-data-with-cqlsh"></a>Руководство по переносу данных с помощью cqlsh

1. Заранее создайте и масштабируйте таблицу:
    * Azure Cosmos DB по умолчанию подготавливает новую таблицу API-интерфейса Cassandra с пропускной способностью 1000 единиц запроса в секунду, а при подготовке на основе CQL — 400 единиц запроса в секунду. Прежде чем запустить миграцию командой cqlsh, создайте все таблицы с помощью [портала Azure](https://portal.azure.com) или cqlsh. 

    * Кроме того, с помощью [портала Azure](https://portal.azure.com) увеличьте пропускную способность таблиц на период миграции с установленного по умолчанию значения (400 или 1 000 единиц запроса в секунду) до 10 000 единиц запроса в секунду. Более высокая пропускная способность позволяет избежать регулирования и выполнить перенос быстрее. Почасовая модель выставления счетов, применяемая в Azure Cosmos DB, позволяет снизить пропускную способность сразу после переноса для сокращения затрат.

2. Определите стоимости операции в единицах запроса. Это можно сделать с помощью любого пакета SDK Azure Cosmos DB для API-интерфейса Cassandra. В этом примере мы используем для оценки затрат версию .NET. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Определите задержку между своим компьютером и службой Azure Cosmos DB. Если компьютер расположен в центе обработки данных Azure, задержка не должна превышать пяти миллисекунд. Если вы работаете за пределами центра обработки данных Azure, примените средство psping или сайт azurespeed.com для приблизительной оценки задержки для вашего местоположения.   

4. Рассчитайте правильные значения для параметров (NUMPROCESS, INGESTRATE, MAXBATCHSIZE или MINBATCHSIZE), которые обеспечат хорошую производительность. 

5. Выполните команду для начала переноса. Чтобы эта команда выполнилась успешно, сеанс cqlsh нужно запускать с информацией о строке подключения.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Использование Spark для импорта данных

Для данных, хранящихся в существующем кластере виртуальных машин Azure, можно применить импорт данных с помощью Spark. Для этого следует настроить Spark в качестве промежуточного звена для однократного или регулярного приема данных. 

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнять следующие задачи.

> [!div class="checklist"]
> * Получить строку подключения
> * Импорт данных с помощью команды cql copy
> * Импорт с помощью соединителя Spark 

Теперь можно перейти к разделу основных понятий, чтобы получить дополнительные сведения о службе Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Настраиваемые уровни согласованности данных в Azure Cosmos DB](../cosmos-db/consistency-levels.md)
