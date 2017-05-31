---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51bed95b47f08cb9ba6c0785d9ac8bb1c9f1ad4c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="sorting-azure-cosmos-db-data-using-order-by"></a>Сортировка данных Azure Cosmos DB с помощью предложения ORDER BY
Служба Microsoft Azure Cosmos DB поддерживает запросы документов с помощью SQL через документы JSON. Результаты запроса можно упорядочить с помощью предложения ORDER BY в инструкции запроса SQL.

Ознакомившись с данной статьей, вы сможете ответить на следующие вопросы. 

* Как реализовать запрос с помощью предложения Order By?
* Как настроить политику индексации для предложения Order By?
* Что дальше?

Вы также найдете [образцы](#samples) и [часто задаваемые вопросы](#faq).

Более подробные сведения о запросах SQL см. на странице [Azure Cosmos DB](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Реализация запроса с помощью предложения Order By
Как и в ANSI-SQL, вы теперь можете включать необязательное предложение ORDER BY в инструкции SQL при запросе Cosmos DB. Предложение может включать необязательный аргумент ASC/DESC, указывающий порядок, в котором должны быть получены результаты. 

### <a name="ordering-using-sql"></a>Упорядочение с помощью SQL
Например, вот запрос для получения первых 10 книг с сортировкой их заголовков в порядке убывания. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Упорядочение с помощью SQL с фильтрацией
Можно упорядочить результаты с помощью любого вложенного свойства в рамках документов, например Books.ShippingDetails.Weight. Кроме того, можно указать дополнительный фильтр в предложении WHERE в сочетании с предложением Order By, как в этом примере:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Упорядочение с помощью поставщика LINQ для .NET
С помощью пакета SDK для .NET версии 1.2.0 и выше можно использовать предложение OrderBy() или OrderByDescending() в запросах LINQ, как в этом примере:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

Cosmos DB поддерживает сортировку по одиночному числу, строке или логическому свойству для каждого запроса. В ближайшем будущем ожидается поддержка дополнительных типов запросов. Дополнительные сведения см. в разделе [Дальнейшие действия](#Whats_coming_next).

## <a name="configure-an-indexing-policy-for-order-by"></a>Настройка политики индексации для предложения Order By
Помните, что Cosmos DB поддерживает два вида индексов (хэш и диапазон), которые можно задать для конкретных путей и свойств, типов данных (строковых и числовых) и с различной точностью значения (максимальная точность или значение с фиксированной точностью). Так как по умолчанию Cosmos DB использует хэш-индексацию, то для использования ORDER BY вам необходимо создать новую коллекцию с пользовательской политикой диапазонной индексации на числах, строках или числах и строках. 

> [!NOTE]
> Строковые диапазонные индексы были представлены 7 июля 2015 года вместе с REST API версии 2015-06-03. Чтобы создать политики для Order By к строкам, необходимо использовать пакет .NET SDK версии 1.2.0 либо пакеты Python, Node.js или Java SDK версии 1.1.0.
> 
> До REST API версии 2015-06-03 политикой индексации коллекций по умолчанию был хэш для строк и чисел. Она была изменена на хэш-индексацию для строк и диапазонную индексацию для чисел 
> 
> 

Дополнительные сведения см. в статье [Политики индексирования Azure Cosmos DB](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Индексирование в предложении Order By для всех свойств
Далее описывается как можно создать коллекцию с индексацией по "Всему диапазону" в предложении Order By для любых/всех числовых или строковых свойств, отображаемых в документах JSON. Здесь мы переопределяем тип индекса по умолчанию для строковых значений, меняя его на Range, и устанавливаем максимальную точность (-1).

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> Обратите внимание, что предложение Order By будет возвращать только типы данных (строковые и числовые), индексируемые с RangeIndex. Например, если у вас есть политика индексации по умолчанию, имеющая в числах только RangeIndex, предложение Order By по отношению к пути со строковыми значениями не вернет никаких документов.
> 
> 

### <a name="indexing-for-order-by-for-a-single-property"></a>Индексирование Order By для одного свойства
Вот как можно создать коллекцию с индексированием предложения Order By только для строкового свойства Title. Существует два пути: один для свойства Title ("/Title/?") с индексацией диапазона, а другой — для всех свойств со схемой индексации по умолчанию, где для строк используется хеш-индексация, а для чисел — диапазонная индексация.                    

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Примеры
Обратите внимание на этот [проект образцов Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries), который показывает, как использовать Order By, включая создание политик индексации и разбиение по страницам с помощью Order By. Образцы содержат открытый исходный код, и мы рекомендуем вам отправлять запросы с материалами, которые могут принести пользу другим разработчикам DocumentDB. Инструкции о том, как принять участие, см. [здесь](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md).  

## <a name="faq"></a>Часто задаваемые вопросы
**Какой расход единиц запроса (RU) ожидается для запросов Order By?**

Так как предложение ORDER BY использует индекс Cosmos DB для уточняющих запросов, количество единиц запроса, используемых запросами с ORDER BY, такое же, как и эквивалентных запросов без ORDER BY. Как и в случае любой другой операции Cosmos DB, число единиц запроса зависит от размера и формы документов, а также от сложности запроса. 

**Каковы ожидаемые затраты на индексирование для Order By?**

Затраты хранилища индексирования будут пропорциональны количеству свойств. В худшем случае затраты индексирования составят 100 % данных. Нет никакой разницы в затратах по пропускной способности (единицы запроса), между индексированием Range/Order By и индексированием Hash по умолчанию.

**Как запросить существующие данные в Cosmos DB с помощью предложения Order By?**

Для сортировки результатов запроса с помощью Order By необходимо изменить политику индексирования коллекции, чтобы использовать тип индекса диапазона для свойства, применяемого для сортировки. Ознакомьтесь с разделом [Изменение политики индексирования коллекции](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Каковы текущие ограничения Order By?**

Предложение Order By можно указать только для числовых или строковых свойств при индексированном диапазоне с максимальной точностью (-1).

Невозможно выполнить следующие задачи.

* Использовать Order By с внутренними строковыми свойствами, такими как id, _rid и _self (ожидается вскоре).
* Использовать Order By со свойствами, полученными в результате объединения внутри документа (ожидается вскоре).
* Использовать Order By с несколькими свойствами (ожидается вскоре).
* Использовать Order By с запросами к базам данных, коллекциям, пользователям, разрешениям или вложениям (ожидается в скором времени).
* Использовать Order By с вычисляемыми свойствами, например, с результатом выражения или с определяемой пользователем или встроенной функцией.

В настоящее время Order By не поддерживается для запросов между секциями, когда используется обозреватель запросов на портале Azure.

## <a name="troubleshooting"></a>Устранение неполадок
При отображении сообщения об ошибке о том, что Order By не поддерживается, проверьте, используется ли версия [пакета SDK](documentdb-sdk-dotnet.md), которая поддерживает Order By. 

## <a name="next-steps"></a>Дальнейшие действия
Скопируйте [проект образцов GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) и запустите упорядочение данных! 

## <a name="references"></a>Ссылки
* [SQL-запросы и синтаксис SQL в Azure Cosmos DB](documentdb-sql-query.md)
* [Как индексируются данные в Azure Cosmos DB](documentdb-indexing-policies.md)
* [DocumentDB SQL Syntax](https://msdn.microsoft.com/library/azure/dn782250.aspx) (Синтаксис SQL в DocumentDB)
* [Примеры кода с предложением ORDER BY в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)


