---
title: Фильтры аспекта в службе "Поиск Azure" | Документация Майкрософт
description: Условия фильтрации по удостоверениям безопасности, языку, географическому расположению или числовым значениям для уменьшения результатов поиска в запросах в службе "Поиск Azure", размещенной облачной службе поиска в Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 3f2cfea52d3c3f4bfc75364d0662a4218219152d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Как создать фильтр аспекта в службе "Поиск Azure" 

Фасетная навигация используется для автоматической фильтрации результатов запроса в поисковом приложении, когда ваше приложение предлагает элементы пользовательского интерфейса для поиска с ограничением до групп документов (например, категорий или брендов), а служба "Поиск Azure" предоставляет структуру данных. В этой статье кратко рассматриваются основные шаги для создания структуры фасетной навигации с возможностями поиска, которые вы хотите предоставить. 

> [!div class="checklist"]
> * Выбор полей для фильтрации и фасетизации
> * Указание атрибутов поля
> * Построение индекса и загрузка данных
> * Добавление фильтров аспектов в запрос
> * Обработка результатов

Аспекты являются динамическими и возвращаются в запросе. В ответах поиска приводятся фасетные категории, используемые для навигации по результатам. Если вы не знакомы с аспектами, ниже приведен пример структуры фасетной навигации.

  ![](./media/search-filters-facets/facet-nav.png)

Хотите подробнее узнать о фасетной навигации? Ознакомьтесь со статьей [Как реализовать фасетную навигацию в службе поиска Azure](search-faceted-navigation.md).

## <a name="choose-fields"></a>Выбор полей

Аспекты могут быть вычислены для отдельных полей значений, а также для коллекций. Поля, которые лучше всего работают в фасетной навигации, имеют низкую кратность: небольшое количество различных значений, которые повторяются во всех документах в данных поиска (например, список цветов, стран или торговых марок). 

Фасетизация включается для каждого поля при создании индекса, задавая для атрибутов `filterable` и `facetable` значение TRUE. Аспектировать можно только фильтруемые поля.

Для всех [типов полей](https://docs.microsoft.com/rest/api/searchservice/supported-data-types), которые можно использовать в фасетной навигации, по умолчанию устанавливается значение facetable:

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Типы числовых полей: Edm.Int32, Edm.Int64, Edm.Double

В фасетной навигации нельзя использовать Edm.GeographyPoint. Аспекты формируются на основе понятного для человека текста и чисел. Таким образом аспекты не поддерживаются для географических координат. Вам понадобится поле города или региона для поиска по местоположению с использованием фасетизации.

## <a name="set-attributes"></a>Определение атрибутов

Атрибуты индекса, которые управляют использованием поля, добавляются к отдельным определениям полей в индексе. В следующем примере поля с низкой кратностью, полезные для фасетизации, состоят из: категории (гостиницы, мотели, общежития), услуг и рейтингов. 

В .NET API атрибуты фильтрации должны быть явно заданы. В API REST фасетизация и фильтрация включены по умолчанию, а это означает, что вам нужно только явно указать атрибуты, когда вы хотите их отключить. Хотя это не является техническим требованием, в следующем примере REST атрибуты указаны в целях обучения. 

> [!Tip]
> Для оптимизации производительности и хранения отключите фасетизацию для полей, которые никогда не должны использоваться в качестве аспектов. В частности, для полей строк одноэлементных значений, таких как идентификатор или название продукта, следует задать значение false для параметра Facetable во избежание их случайного (и неэффективного) использования при фасетной навигации.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Это определение индекса можно скопировать в статье [Создание индекса службы поиска Azure с помощью REST API](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Оно идентично, за исключением поверхностных различий в определениях полей. Фильтруемые и аспектируемые атрибуты явно добавляются в поля категория, теги, parkingIncluded, smokingAllowed, а также рейтинг. На практике вы получаете возможность фильтровать и аспектировать по типам полей Edm.String, Edm.Boolean и Edm.Int32. 

## <a name="build-and-load-an-index"></a>Построение и загрузка индекса

Промежуточным (и, возможно, очевидным) шагом является [сборка и заполнение индекса](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) перед формулировкой запроса. Здесь мы упомянем этот шаг для полноты картины. Один из способов определить доступность индекса — это проверить список индексов на [портале](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Добавление фильтров аспектов в запрос

В коде приложения создайте запрос, который определяет все части допустимого запроса, включая выражения поиска, фасеты, фильтры, профили оценки — все, что используется для формулировки запроса. В следующем примере создается запрос, применяемый для фасетной навигации с использованием фасетов размещения, рейтинга и других удобств.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Возврат отфильтрованных результатов при событии щелчка

которое обрабатывает событие щелчка в значении фасета. Щелчок категории "мотель" реализуется с помощью выражения `$filter`, которое выбирает размещения этого типа с учетом фасета категории. Если пользователь щелкнет "мотели", чтобы отобразить только мотели, отправляемый приложением запрос будет включать фрагмент $filter=category eq "motels".

Следующий фрагмент кода добавляет категорию в фильтр, если пользователь выбирает значение из фасета категории.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
С помощью REST API запрос формулируется как `$filter=category eq 'c1'`. Чтобы сделать категорию многозначным полем, используйте следующий синтаксис: `$filter=category/any(c: c eq 'c1')`.

## <a name="tips-and-workarounds"></a>Советы и обходные решения

### <a name="initialize-a-page-with-facets-in-place"></a>Инициализация страницы с помощью фасетов

Если требуется инициализировать страницу с помощью фасетов, вы можете отправить запрос как часть инициализации страницы, чтобы заполнить страницу исходной структурой фасета.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Асинхронное сохранение структуры фасетной навигации отфильтрованных результатов

Одна из сложностей фасетной навигации в службе "Поиск Azure" заключается в том, что фасеты существуют только для текущих результатов. На практике обычно сохраняется статический набор фасетов, чтобы пользователь мог перемещаться в обратном направлении, проходя по поисковому содержимому для поиска альтернативных путей. 

Хотя это распространенный вариант использования, структура фасетной навигации в настоящее время не предоставляет этого изначально. Разработчики, которым требуются статические аспекты, обычно обходят ограничение, выполняя два отфильтрованных запроса: один в пределах результатов, а другой для создания статического списка аспектов для навигации.

## <a name="see-also"></a>См. также

+ [Фильтры в службе "Поиск Azure"](search-filters.md)
+ [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индексов (REST API службы "Поиск Azure")).
+ [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Поиск по документам (REST API службы поиска Azure))

