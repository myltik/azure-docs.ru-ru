---
title: "Создание индекса службы поиска Azure с помощью пакета SDK для .NET | Документация Майкрософт"
description: "Индекс можно создать в коде с помощью пакета SDK для .NET службы поиска Azure."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: 3a5131323f438109d94137cb4f577054ec13227f


---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Создание индекса службы поиска Azure с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [Обзор](search-what-is-an-index.md)
> * [Портал](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Эта статья поможет вам создать [индекс](https://docs.microsoft.com/rest/api/searchservice/Create-Index) службы поиска Azure с помощью [пакета SDK для .NET службы поиска Azure](https://aka.ms/search-sdk).

Перед выполнением инструкций, приведенных в этом руководстве, и созданием индекса следует [создать службу поиска Azure](search-create-service-portal.md).

Все приведенные здесь примеры кода написаны на языке C#. Полный исходный код можно найти на сайте [GitHub](http://aka.ms/search-dotnet-howto).

## <a name="identify-your-azure-search-services-admin-api-key"></a>Определение ключа API администратора службы поиска Azure
После подготовки службы поиска Azure все почти готово к тому, чтобы вы могли отправлять запросы для конечной точки вашей службы, используя пакет SDK для .NET. Для этого сначала нужно получить один из ключей API администратора, созданный для подготовленной службы поиска. Пакет SDK для .NET отправляет этот ключ при каждом запросе к службе. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

1. Чтобы найти ключи API своей службы, войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к колонке службы поиска Azure.
3. Щелкните значок "Ключи".

Ваша служба получит *ключи администратора* и *ключи запросов*.

* Первичные и вторичные *ключи администратора* предоставляют полный доступ ко всем операциям, включая возможность управлять службой, создавать и удалять индексы, индексаторы и источники данных. Ключей два, поэтому вы можете и дальше использовать вторичный ключ, если решите повторно создать первичный ключ, и наоборот.
* *Ключи запросов* предоставляют только разрешение на чтение индексов и документов; обычно они добавляются в клиентские приложения, которые создают запросы на поиск.

Для создания индекса можно использовать первичный или вторичный ключ администратора.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Создание экземпляра класса SearchServiceClient
Чтобы начать использовать пакет SDK для .NET в службе поиска Azure, нужно создать экземпляр класса `SearchServiceClient`. Этот класс имеет несколько конструкторов. Нужный вам конструктор принимает в качестве параметров имя службы поиска и объект `SearchCredentials` . `SearchCredentials` содержит ключ API.

Приведенный ниже код создает новый `SearchServiceClient` с использованием значений для имени службы поиска и ключа API, которые хранятся в файле конфигурации приложения (`app.config` или `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` имеет свойство `Indexes`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления индексов службы поиска Azure.

> [!NOTE]
> Класс `SearchServiceClient` управляет подключениями к службе поиска. Во избежание открытия слишком большого числа подключений в приложении рекомендуется по возможности совместно использовать один экземпляр `SearchServiceClient` . Его методы поддерживают такое использование, так как являются потокобезопасными.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Определение индекса службы поиска Azure
Для создания индекса достаточно отправить один вызов в метод `Indexes.Create` . В качестве параметра этот метод принимает объект `Index` , определяющий индекс службы поиска Azure. Вам нужно создать и инициализировать объект `Index` следующим образом:

1. Задайте имя вашего индекса в качестве значения для свойства `Name` объекта `Index`.
2. Назначьте свойству `Fields` объекта `Index` массив объектов `Field`. Самый простой способ создания объектов `Field` — вызвать метод `FieldBuilder.BuildForType`, передав класс модели для параметра типа. Свойства класса модели сопоставляются с полями индекса. Это позволяет привязать документы из индекса поиска к экземплярам класса модели.

> [!NOTE]
> Если вы не планируете использовать класс модели, индекс можно определить путем непосредственного создания объектов `Field`. Имя поля вместе с типом данных можно указать в конструкторе (или анализаторе для строковых полей). Можно также задать другие свойства, например `IsSearchable`, `IsFilterable` и т. д.
>
>

При проектировании индекса важно помнить об удобстве работы с поиском и бизнес-потребностях, поэтому каждому полю необходимо назначить [подходящие свойства](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Эти свойства контролируют, какие функции поиска (фильтрация, фасетная навигация, сортировка полнотекстового поиска и т. д.) применяются к каждому полю. Для любого свойства, не заданного явным образом, класс `Field` по умолчанию отключает соответствующую функцию поиска, пока вы специально не включите ее.

В нашем примере мы присвоили индексу имя hotels и определили поля с помощью класса модели. Каждое свойство класса модели имеет атрибуты, которые определяют связанные с поиском характеристики соответствующего поля индекса. Класс модели определяется следующим образом.

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Мы тщательно выбрали атрибуты для каждого свойства в зависимости от того, как мы планируем использовать их в приложении. Например, вполне вероятно, что людей, которые ищут гостиницы, будут интересовать совпадения по ключевым словам в поле `description`, поэтому мы включаем для него полнотекстовый поиск, добавив атрибут `IsSearchable` для свойства `Description`.

Обратите внимание, что только одно поле в индексе типа `string` должно быть назначено как поле *key*, для чего нужно добавить атрибут `Key` (см. `HotelId` в примере выше).

В приведенном выше определении индекса используется языковой анализатор для поля `description_fr`, так как оно предназначено для текста на французском языке. Дополнительные сведения об анализаторах языка см. в статье [Language support (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Language-support) (Поддержка языков (REST API службы поиска Azure)), а также в соответствующей [записи блога](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [!NOTE]
> По умолчанию имя каждого свойства в классе модели используется в качестве имени соответствующего поля в индексе. Если необходимо сопоставить имена всех свойств с именами полей в нижнем регистре, пометьте класс с помощью атрибута `SerializePropertyNamesAsCamelCase`. Если требуется выполнить сопоставление с другим именем, можно использовать атрибут `JsonProperty`, как в свойстве `DescriptionFr` выше. Атрибут `JsonProperty` имеет приоритет над атрибутом `SerializePropertyNamesAsCamelCase`.
> 
> 

Теперь, когда мы определили класс модели, можно легко создать определение индекса.

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>Создание индекса
Теперь, когда у нас есть инициализированный объект `Index`, мы можем создать индекс, просто вызвав метод `Indexes.Create` для объекта `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

В случае успешного запроса метод возвращает обычные данные. При наличии проблем с запросом, например недопустимого параметра, метод вызывает исключение `CloudException`.

Если вы завершили работу с индексом и хотите удалить его, просто вызовите `Indexes.Delete` для `SearchServiceClient`. Например, индекс hotels удаляется так:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Для простоты пример кода в этой статье использует синхронные методы пакета SDK для Поиска Azure в .NET. Рекомендуем использовать в ваших приложениях асинхронные методы, чтобы сохранить масштабируемость и скорость отклика. Например, в приведенных выше примерах можно использовать `CreateAsync` и `DeleteAsync` вместо `Create` и `Delete`.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Создав индекс службы поиска Azure, вы сможете [передать в него содержимое](search-what-is-data-import.md) и искать нужные вам данные.




<!--HONumber=Jan17_HO2-->


