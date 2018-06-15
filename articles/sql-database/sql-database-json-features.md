---
title: Возможности использования JSON в базе данных SQL Azure | Документация Майкрософт
description: База данных SQL Azure позволяет анализировать, запрашивать и форматировать данные в формате JSON (нотация объектов JavaScript).
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.date: 04/01/2018
ms.author: jovanpop
ms.topic: conceptual
ms.openlocfilehash: 687986d108a15973c19ff06f5ca98a468439f30d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649005"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Приступая к работе с функциями JSON в Базе данных SQL Azure
База данных SQL Azure позволяет анализировать и запрашивать данные, представленные в формате [JSON](http://www.json.org/) (нотация объектов JavaScript), и экспортировать реляционные данные в виде текста JSON.

JSON – это распространенный формат данных, который используются для обмена данными в современных мобильных и веб-приложениях. Кроме того, формат JSON используется для хранения частично структурированных данных в файлах журнала или базах данных NoSQL, например [базе данных Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Многие веб-службы REST возвращают результаты в виде текста JSON или принимают данные в формате JSON. Большинство служб Azure, в том числе [Поиск Azure](https://azure.microsoft.com/services/search/), [служба хранилища Azure](https://azure.microsoft.com/services/storage/) и [база данных Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), имеют конечные точки REST, которые возвращают или используют данные JSON.

База данных SQL Azure позволяет легко работать с данными JSON и интегрировать свою базу данных с современными службами.

## <a name="overview"></a>Обзор
База данных SQL Azure предоставляет следующие функции для работы с данными JSON.

![Функции JSON](./media/sql-database-json-features/image_1.png)

Если имеется текст JSON, то можно извлечь данные из JSON или проверить правильность его форматирования с помощью встроенных функций [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) и [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Функция [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) позволяет обновить значение внутри текста JSON. Если требуются более сложные запросы и анализ, то с помощью функции [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) можно преобразовать массив объектов JSON в набор строк. С возвращенным результирующим набором можно выполнить любой SQL-запрос. Наконец, можно использовать предложение [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) , которое позволяет форматировать данные, хранящиеся в реляционных таблицах в виде текста JSON.

## <a name="formatting-relational-data-in-json-format"></a>Преобразование реляционных данных в формат JSON
При наличии веб-службы, которая извлекает данные из слоя базы данных и представляет ответ в формате JSON, либо клиентских платформ или библиотек JavaScript, которые принимают данные в формате JSON, можно преобразовывать содержимое базы данных в формат JSON непосредственно в SQL-запросе. Больше не придется писать код приложения, который преобразовывает результаты из Базы данных SQL Azure в JSON, или добавлять какую-либо библиотеку сериализации JSON, чтобы преобразовывать результаты табличного запроса, а затем сериализовать объекты в формат JSON. Вместо этого можно использовать предложение FOR JSON, чтобы преобразовывать результаты SQL-запроса в JSON в Базе данных SQL Azure и использовать их непосредственно в своем приложении.

В следующем примере строки из таблицы Sales.Customer преобразовываются в формат JSON с помощью предложения FOR JSON.

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Предложение FOR JSON PATH форматирует результаты запроса в виде текста JSON. Имена столбцов используются в качестве ключей, а значения ячеек создаются как значения JSON.

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Результирующий набор форматируется как массив JSON, в котором каждая строка форматируется как отдельный объект JSON.

PATH указывает, что можно настроить выходной формат результатов JSON, используя точечную нотацию в псевдонимах столбцов. Приведенный ниже запрос изменяет имя ключа CustomerName в выходном формате JSON и помещает номера телефона и факса в подобъект Contact.

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Выходные данные этого запроса имеют следующий вид.

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

В этом примере мы вернули отдельный объект JSON вместо массива, указав параметр [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx). Этот параметр можно использовать, если вы знаете, что результатом запроса является отдельный объект.

Основная ценность предложения FOR JSON заключается в том, что оно дает возможность возвращать сложные иерархические данные из базы данных в виде вложенных объектов JSON или массивов JSON. В приведенном ниже примере показано, как добавить заказы (Orders), принадлежащие клиенту (Customer), в виде вложенного массива объектов Orders.

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Вместо того, чтобы отправлять отдельные запросы для получения данных клиента (Customer), а затем получать список соответствующих заказов (Orders), можно получить все необходимые данные с помощью одного запроса, как показано в приведенном ниже примере выходных данных.

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Работа с данными JSON
Если у вас нет строго структурированных данных, имеются сложные вложенные объекты, массивы или иерархические данные либо структуры данных со временем развиваются, то формат JSON позволит представить любую сложную структуру данных.

JSON — это текстовый формат, который можно использовать как любой другой строковый тип в Базе данных SQL Azure. Данные JSON можно отправлять или хранить как стандартные данные типа NVARCHAR.

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Данные JSON, используемые в этом примере, представлены с помощью типа NVARCHAR(MAX). Данные JSON можно вставить в эту таблицу или указать в качестве аргумента хранимой процедуры, используя стандартный синтаксис Transact-SQL, как показано в следующем примере.

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Любой язык или библиотека на стороне клиента, которая работает со строковыми данными в Базе данных SQL Azure, также будет работать с данными JSON. Данные JSON могут храниться в таблице, которая поддерживает тип NVARCHAR. Например, это может быть оптимизированная для памяти таблица или таблица с системным управлением версиями. Формат JSON не накладывает каких-либо ограничений на код на стороне клиента или на уровне базы данных.

## <a name="querying-json-data"></a>Запрос данных JSON
Если данные в формате JSON хранятся в таблицах Azure SQL, то с помощью функций JSON эти данные можно использовать в SQL-запросе.

Функции JSON, доступные в Базе данных SQL Azure, позволяют обрабатывать данные в формате JSON как данные SQL любого другого типа. Можно легко извлечь значения из текста JSON и использовать данные JSON в любом запросе.

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Функция JSON_VALUE извлекает значение из текста JSON, хранящегося в столбце данных. Эта функция использует путь, как в JavaScript, для указания ссылки на извлекаемое значение в тексте JSON. Извлеченное значение может использоваться в любой части SQL-запроса.

Функция JSON_QUERY аналогична функции JSON_VALUE. В отличие от JSON_VALUE, эта функция извлекает сложный подобъект, например массивы или объекты, которые помещаются в текст JSON.

Функция JSON_MODIFY позволяет указать путь к значению в тексте JSON, которое следует обновить, а также новое значение, которым заменяется старое значение. Таким образом можно легко обновить текст JSON, не анализируя всю структуру.

Так как данные JSON хранятся в виде обычного текста, то нет никакой гарантии, что значения, хранящиеся в текстовых столбцах, отформатированы правильно. Вы можете проверить, правильно ли отформатирован текст, хранящийся в столбце JSON, с помощью стандартных проверочных ограничений Базы данных SQL Azure и функции ISJSON.

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Если входной текст имеет правильный формат JSON, то функция ISJSON возвращает значение 1. При каждой операции вставки или обновления столбца JSON это ограничение будет проверять, правилен ли формат JSON нового текстового значения.

## <a name="transforming-json-into-tabular-format"></a>Преобразование данных JSON в табличный формат
База данных SQL Azure позволяет также преобразовать коллекции JSON в табличный формат и загрузить или запросить данные JSON.

OPENJSON — это функция с табличным значением, которая анализирует текст JSON, находит массив объектов JSON, выполняет итерацию по элементам массива и возвращает одну строку для каждого элемента массива в выходных данных.

![Табличное представление JSON](./media/sql-database-json-features/image_2.png)

В приведенном выше примере можно указать, где искать массив JSON, который нужно открыть (в пути $.Orders), какие столбцы должны быть возвращены в качестве результата и где искать значения JSON, которые будут возвращены в виде ячеек.

В переменной @orders можно преобразовать массив JSON в набор строк, проанализировать этот результирующий набор или вставить строки в стандартную таблицу.

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Набор заказов, отформатированный как массив JSON и указанный в качестве параметра хранимой процедуры, можно проанализировать и вставить в таблицу Orders.

## <a name="next-steps"></a>Дополнительная информация
Чтобы узнать, как интегрировать JSON в приложение, ознакомьтесь с приведенным ниже материалами.

* [Блог TechNet](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Документация MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Видео Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Чтобы узнать о различных сценариях интеграции JSON в приложение, ознакомьтесь с роликами [Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) или найдите подходящий вам сценарий в [записях блога о JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

