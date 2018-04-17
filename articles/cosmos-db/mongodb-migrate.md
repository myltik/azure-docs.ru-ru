---
title: Использование mongoimport и mongorestore с API Azure Cosmos DB для MongoDB | Документация Майкрософт
description: Узнайте, как использовать mongoimport и mongorestore для импорта данных в учетную запись API для MongoDB.
keywords: mongoimport, mongorestore
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 5c87483e384a09591aca496292638d7b68476beb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: импорт данных MongoDB 

Для переноса данных из MongoDB в учетную запись Azure Cosmos DB для использования с API для MongoDB необходимо выполнить следующие действия:

* Скачайте файл *mongoimport.exe* или *mongorestore.exe* из [центра скачивания MongoDB](https://www.mongodb.com/download-center).
* Получите [строку подключения API для MongoDB](connect-mongodb-account.md).

Если вы импортируете данные из MongoDB и планируете использовать их с Azure Cosmos DB, то используйте [средство переноса данных](import-data.md) для их импорта.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Получение строки подключения
> * Импорт данных MongoDB с помощью mongoimport
> * Импорт данных MongoDB с помощью mongorestore

## <a name="prerequisites"></a>предварительным требованиям

* Увеличьте пропускную способность. Продолжительность переноса данных зависит от пропускной способности, настроенной для коллекций. Увеличьте пропускную способность для крупных миграций. После переноса уменьшите пропускную способность для экономии расходов. Дополнительные сведения об увеличении пропускной способности на [портале Azure](https://portal.azure.com) см. в статье [Прекращение использования уровней производительности S1, S2 и S3 в DocumentDB](performance-levels.md).

* Включите SSL. В Azure Cosmos DB строгие требования к безопасности и стандарты. Обязательно включите SSL при взаимодействии с учетной записью. Процедуры, описанные в оставшейся части статьи, включают инструкции по включению SSL для mongoimport и mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Поиск сведений о строке подключения (узел, порт, имя пользователя и пароль)

1. В левой панели на [портале Azure](https://portal.azure.com) щелкните запись **Azure Cosmos DB**.
2. На панели **Подписки** выберите имя своей учетной записи.
3. В колонке **Строка подключения** щелкните **Строка подключения**.  
На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.

    ![Колонка "Строка подключения"](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Импорт данных в API для MongoDB с помощью mongoimport

Чтобы импортировать данные в учетную запись Azure Cosmos DB, используйте следующий шаблон. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.  

Шаблон:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Пример:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Импорт данных в API для MongoDB с помощью mongorestore

Чтобы восстановить данные в API для учетной записи MongoDB, используйте следующий шаблон для выполнения импорта. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.

Шаблон:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Пример:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Инструкции для успешного переноса

1. Заранее создайте и масштабируйте свои коллекции:
        
    * По умолчанию Azure Cosmos DB подготавливает новую коллекцию MongoDB, обеспечивающую пропускную способность 1000 единиц запроса в секунду (ЕЗ/с). Прежде чем начать перенос с помощью mongoimport, mongorestore или mongomirror, создайте все свои коллекции с [портала Azure](https://portal.azure.com) или из драйверов и инструментов MongoDB. Если размер коллекции превышает 10 ГБ, то создайте [сегментированную или секционированную коллекцию](partition-data.md) с соответствующим ключом сегмента.

    * На [портале Azure](https://portal.azure.com) увеличьте показатели пропускной способности своих коллекций (с 1000 ЕЗ/с для односекционной коллекции и 2500 ЕЗ/с для сегментированной коллекции) только на время выполнения переноса. Более высокая пропускная способность позволяет избежать регулирования и выполнить перенос быстрее. Почасовая модель выставления счетов, применяемая в Azure Cosmos DB, позволяет снизить пропускную способность сразу после переноса для сокращения затрат.

2. Вычислите приблизительную стоимость ЕЗ при записи одного документа:

    a. Подключитесь к базе данных MongoDB в Azure Cosmos DB из оболочки MongoDB. Инструкции доступны в статье [Подключение приложения MongoDB к Azure Cosmos DB](connect-mongodb-account.md).
    
    Б. Выполните пример команды insert, используя один из примеров документов из оболочки MongoDB:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Выполните команду ```db.runCommand({getLastRequestStatistics: 1})```, в результате чего будет получен ответ, аналогичный этому:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Запишите значение RequestCharge (стоимость запроса).
    
3. Определите задержку между вашим компьютером и облачной службой Azure Cosmos DB:
    
    a. Включите ведение подробного журнала из оболочки MongoDB с помощью команды ```setVerboseShell(true)```.
    
    Б. Выполните простой запрос к базе данных: ```db.coll.find().limit(1)```. В результате будет получен ответ, аналогичный этому:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Перед выполнением переноса удалите вставленный документ, чтобы точно не было повторяющихся документов. Для удаления документов можно использовать команду ```db.coll.remove({})```.

5. Вычислите приблизительные значения *batchSize* и *numInsertionWorkers*:

    * Для расчета *batchSize* поделите общее число подготовленных ЕЗ на число ЕЗ, использованных для записи одного документа (на шаге 3).
    
    * Если полученное значение *batchSize* <= 24, то используйте его в качестве значения *batchSize*.
    
    * Если полученное значение *batchSize* > 24, то задайте значение *batchSize* равное 24.
    
    * Для расчета *numInsertionWorkers* используйте следующую формулу: *numInsertionWorkers = (подготовленная пропускная способность * задержка в секундах) / (размер пакета * число ЕЗ, использованных для одной операции записи)*.
        
    |Свойство|Значение|
    |--------|-----|
    |batchSize| 24 |
    |Подготовленные ЕЗ | 10 000 |
    |Latency | 0,100 с |
    |ЕЗ, использованные для записи 1 документа | 10 ЕЗ |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 ЕЗ x 0,1 с) / (24 x 10 ЕЗ) = 4,1666*

6. Выполните окончательную команду переноса:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Дополнительная информация

Вы можете перейти к следующему руководству, из которого вы узнаете, как запрашивать данные MongoDB с помощью Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API для MongoDB](../cosmos-db/tutorial-query-mongodb.md)
