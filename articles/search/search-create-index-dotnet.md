<properties
    pageTitle="Создание индекса службы поиска Azure с помощью пакета SDK для .NET | Microsoft Azure | Размещенная облачная служба поиска"
    description="Индекс можно создать в коде с помощью пакета SDK для .NET службы поиска Azure."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# Создание индекса службы поиска Azure с помощью пакета SDK для .NET
> [AZURE.SELECTOR]
- [Обзор](search-what-is-an-index.md)
- [Портал](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Эта статья поможет вам создать [индекс](https://msdn.microsoft.com/library/azure/dn798941.aspx) службы поиска Azure с помощью [пакета SDK для .NET службы поиска Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Перед выполнением инструкций, приведенных в этом руководстве, и созданием индекса следует [создать службу поиска Azure](search-create-service-portal.md).

Все приведенные здесь примеры кода написаны на языке C#. Полный исходный код можно найти на сайте [GitHub](http://aka.ms/search-dotnet-howto).

## 1\. Определение ключа API администратора службы поиска Azure
После подготовки службы поиска Azure все почти готово к тому, чтобы вы могли отправлять запросы для конечной точки вашей службы, используя пакет SDK для .NET. Для этого сначала нужно получить один из ключей API администратора, созданный для подготовленной службы поиска. Пакет SDK для .NET отправляет этот ключ при каждом запросе к службе. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

1. Чтобы найти ключи API своей службы, войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к колонке службы поиска Azure.
3. Щелкните значок "Ключи".

Ваша служба получит *ключи администратора* и *ключи запросов*.

  - Первичные и вторичные *ключи администратора* предоставляют полный доступ ко всем операциям, включая возможность управлять службой, создавать и удалять индексы, индексаторы и источники данных. Ключей два, поэтому вы можете и дальше использовать вторичный ключ, если решите повторно создать первичный ключ, и наоборот.
  - *Ключи запросов* предоставляют только разрешение на чтение индексов и документов; обычно они добавляются в клиентские приложения, которые создают запросы на поиск.

Для создания индекса можно использовать первичный или вторичный ключ администратора.

<a name="CreateSearchServiceClient"></a>
## 2\. Создание экземпляра класса SearchServiceClient
Чтобы начать использовать пакет SDK для .NET в службе поиска Azure, нужно создать экземпляр класса `SearchServiceClient`. Этот класс имеет несколько конструкторов. Нужный вам конструктор принимает в качестве параметров имя службы поиска и объект `SearchCredentials`. `SearchCredentials` содержит ключ API.

Приведенный ниже код создает новый `SearchServiceClient` с использованием значений для имени службы поиска и ключа API, которые хранятся в файле конфигурации приложения (`app.config` или `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` имеет свойство `Indexes`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления индексов службы поиска Azure.

> [AZURE.NOTE] Класс `SearchServiceClient` управляет подключениями к службе поиска. Во избежание открытия слишком большого числа подключений в приложении рекомендуется по возможности совместно использовать один экземпляр `SearchServiceClient`. Его методы поддерживают такое использование, так как являются потокобезопасными.

<a name="DefineIndex"></a>
## 3\. Определение индекса службы поиска Azure с помощью класса `Index`
Для создания индекса достаточно отправить один вызов в метод `Indexes.Create`. В качестве параметра этот метод принимает объект `Index`, определяющий индекс службы поиска Azure. Вам нужно создать и инициализировать объект `Index` следующим образом:

1. Задайте имя вашего индекса в качестве значения для свойства `Name` объекта `Index`.
2. Назначьте свойству `Fields` объекта `Index` массив объектов `Field`. Каждый из объектов `Field` определяет поведение поля в индексе. Имя поля вместе с типом данных можно указать в конструкторе (или анализаторе для строковых полей). Можно также задать другие свойства, например `IsSearchable`, `IsFilterable` и т. д.

При проектировании индекса важно помнить об удобстве работы с поиском и бизнес-потребностях, поэтому каждому `Field` необходимо назначить [подходящие свойства](https://msdn.microsoft.com/library/azure/dn798941.aspx). Эти свойства контролируют, какие функции поиска (фильтрация, фасетная навигация, сортировка полнотекстового поиска и т. д.) применяются к каждому полю. Для любого свойства, не заданного явным образом, класс `Field` по умолчанию отключает соответствующую функцию поиска, пока вы специально не включите ее.

В нашем примере мы присвоили индексу имя hotels и определили поля следующим образом:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Мы тщательно выбрали значения свойств для каждого `Field` в зависимости от того, как мы планируем использовать их в приложении. Например, вполне вероятно, что людей, которые ищут гостиницы, будут интересовать совпадения по ключевым словам в поле `description`, поэтому мы включаем для него полнотекстовый поиск, задав значение `true` для `IsSearchable`.

Обратите внимание, что только одно поле в индексе типа `DataType.String` должно быть назначено как поле _key_ путем задания значения `true` для `IsKey` (см. `hotelId` в примере выше).

Приведенное ранее определение индекса использует настраиваемый языковой анализатор для поля `description_fr`, так как оно предназначено для текста на французском языке. Дополнительные сведения об анализаторах языка см. в [статье о поддержке языков на сайте MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx), а также в соответствующей [записи блога](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [AZURE.NOTE]  Обратите внимание, что при передаче `AnalyzerName.FrLucene` в конструктор `Field` будет автоматически иметь тип `DataType.String`, а для его `IsSearchable` будет установлено значение `true`.

## 4\. Создание индекса
Теперь, когда у нас есть инициализированный объект `Index`, мы можем создать индекс, просто вызвав метод `Indexes.Create` для объекта `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

В случае успешного запроса метод возвращает обычные данные. При наличии проблем с запросом, например недопустимого параметра, метод вызывает исключение `CloudException`.

Если вы завершили работу с индексом и хотите удалить его, просто вызовите `Indexes.Delete` для `SearchServiceClient`. Например, индекс hotels удаляется так:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенных выше примерах можно использовать `CreateAsync` и `DeleteAsync` вместо `Create` и `Delete`.

## Далее
Создав индекс службы поиска Azure, вы сможете [передать в него содержимое](search-what-is-data-import.md) и искать нужные вам данные.

<!---HONumber=AcomDC_0831_2016-->