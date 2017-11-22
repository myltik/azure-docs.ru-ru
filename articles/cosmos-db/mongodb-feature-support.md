---
title: "Поддержка возможностей MongoDB в Azure Cosmos DB | Документация Майкрософт"
description: "Сведения о возможностях MongoDB 3.4, которые предоставляет API-интерфейс MongoDB для Azure Cosmos DB."
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Поддержка возможностей и синтаксиса MongoDB в API-интерфейсе MongoDB

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете обмениваться данными с API-интерфейсом базы данных MongoDB, используя любой из [драйверов](https://docs.mongodb.org/ecosystem/drivers) с открытым кодом для клиента MongoDB. API-интерфейс MongoDB поддерживает существующие драйверы клиента благодаря [сетевому протоколу](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

С помощью API-интерфейса базы данных MongoDB для Azure Cosmos DB вы можете не только продолжать пользоваться существующими функциями MongoDB, но и применять новые возможности Azure Cosmos DB, включая [глобальное распространение](distribute-data-globally.md), [автоматическое сегментирование](partition-data.md), гарантии по уровням доступности и задержкам, автоматическое индексирование каждого поля, шифрование хранимых данных, резервное копирование и многое другое.

## <a name="mongodb-query-language-support"></a>Поддержка языка запросов MongoDB

API-интерфейс MongoDB для Azure Cosmos DB предоставляет полную поддержку всех конструкций языка запросов MongoDB. Ниже приводится подробный список поддерживаемых операций, операторов, этапов, команд и параметров.


## <a name="database-commands"></a>Команды базы данных

Azure Cosmos DB поддерживает следующие команды базы данных для всех учетных записей API-интерфейса MongoDB. 

### <a name="query-and-write-operation-commands"></a>Команды для запросов и записи
- удалить
- поиск
- findAndModify
- getLastError
- getMore
- insert
- обновить

### <a name="authentication-commands"></a>Команды для аутентификации
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Команды для администрирования
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Команды для диагностики
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Конвейер статистической обработки</a>

Azure Cosmos DB поддерживает конвейер статистической обработки в режиме общедоступной предварительной версии. В [блоге Azure](https://aka.ms/mongodb-aggregation) вы найдете инструкции по освоению этой общедоступной предварительной версии.

### <a name="aggregation-commands"></a>Команды статистической обработки
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Этапы статистической обработки
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Статистические выражения

#### <a name="boolean-expressions"></a>Логические выражения
- $and
- $or
- $not

#### <a name="set-expressions"></a>Выражения для наборов
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Выражения сравнения
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Арифметические выражения
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Строковые выражения
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Выражения для массивов
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Выражения для дат
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Условные выражения
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Операторы накопления статистической обработки
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Операторы

Ниже представлен список поддерживаемых операторов с примерами их использования. Изучите пример документа, используемый в приведенных ниже запросах.

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

operator | Пример |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Примечания

В запросах $regex выражения, привязанные слева, поддерживают поиск по индексу. Но если добавить модификатор i (отключение учета регистра) и m (многостроковое выражение), коллекция будет проверяться во всех выражениях.
Если есть необходимость включить $ или |, желательно создать два (или более) запроса regex. Например, исходный запрос вида ```find({x:{$regex: /^abc$/})``` следует изменить следующим образом: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Тогда первая часть будет использовать индекс, чтобы ограничить поиск только теми документами, имя которых начинается со стороки ^abc, а вторая часть будет проверять соответствие строк. Оператор вертикальной черты | действует как функция "или". Например, запрос ```find({x:{$regex: /^abc|^def/})``` отбирает документы whin, у которых значение поля x начинается со строк abc или def. Чтобы использовать индекс, мы рекомендуем разбивать запрос на два разных запроса, соединенных оператором $or, вот так: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.


### <a name="geospatial-operators"></a>Геопространственные операторы

operator | Пример 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Да
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Да
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Да
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Да
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Да
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Да
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Да
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Да
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Да
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Да
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Да

## <a name="additional-operators"></a>Дополнительные операторы

operator | Пример | Примечания 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Не поддерживается. Вместо этого используйте $regex 

### <a name="methods"></a>Методы

Поддерживаются следующие методы:

#### <a name="cursor-methods"></a>Методы курсора

Метод | Пример | Примечания 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Документы без ключа сортировки не возвращаются

## <a name="unique-indexes"></a>Уникальные индексы

По умолчанию Azure Cosmos DB индексирует каждое поле в документах, сохраняемых в базе данных. Уникальные индексы гарантируют, что среди всех документов в коллекции не будет повторяющихся значений определенного поля, то есть для него сохраняется уникальность, как для стандартного ключа _id. Теперь вы можете создавать в базе данных Cosmos Azure пользовательские индексы, используя команду createIndex, которая поддерживает ограничение уникальности (unique).

Уникальные индексы доступны для всех учетных записей API-интерфейса MongoDB.

## <a name="time-to-live-ttl"></a>Срок жизни (TTL)

Azure Cosmos DB поддерживает относительный срок жизни (TTL), определяемый по метке времени для документа. Срок жизни можно включить для коллекций API-интерфейса MongoDB через [портал Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Управление пользователями и ролями

Azure Cosmos DB пока не поддерживает концепцию пользователей и ролей. Azure Cosmos DB поддерживает управление доступом на основе ролей (RBAC), а также пароли (ключи) для чтения и записи или только для чтения, которые можно получить с помощью [портала Azure](https://portal.azure.com) (на странице "Строка подключения").

## <a name="replication"></a>Репликация

Azure Cosmos DB поддерживает собственный механизм автоматической репликации на самых нижних уровнях. Этот механизм применяется и для организации глобальной репликации с низкой задержкой. Azure Cosmos DB не поддерживает запуск или настройку репликации вручную.

## <a name="sharding"></a>Сегментирование

Azure Cosmos DB поддерживает автоматическое сегментирование на стороне сервера. Azure Cosmos DB не поддерживает запуск или настройку сегментирования вручную.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API-интерфейсом для базы данных MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API для базы данных MongoDB.
- Изучите [примеры](mongodb-samples.md) использования Azure Cosmos DB с поддержкой протокола MongoDB.
