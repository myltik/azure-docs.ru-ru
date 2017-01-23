---
title: "Отправка запросов в индекс службы поиска Azure | Документация Майкрософт"
description: "В службе поиска Azure можно создавать поисковые запросы и с помощью параметров поиска фильтровать, сортировать и уточнять результаты."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 455c4847893175c1091ae21fa22215fd1dd10c53
ms.openlocfilehash: 1e18f20e202c199036ff2012dcc6d415898cac7f


---
# <a name="query-your-azure-search-index"></a>Отправка запросов в индекс службы поиска Azure
> [!div class="op_single_selector"]
> * [Обзор](search-query-overview.md)
> * [Портал](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

При отправке поисковых запросов в службу поиска Azure нужно не только ввести в поле поиска приложения определенные слова, но и задать ряд параметров. Эти параметры запросов обеспечивают более точное управление при работе с полнотекстовым поиском.

Ниже приведен список, в котором кратко описано стандартное использование параметров запроса в службе поиска Azure. Подробную информацию о параметрах запроса и их поведении см. в материалах, посвященных [REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) и [пакету SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Типы запросов
Служба поиска Azure предлагает множество параметров для создания расширенных запросов. Два основных типа запросов, которые вы будете использовать, — это `search` и `filter`. Запрос `search` ищет как минимум одно вхождение во всех *поддерживающих поиск* полях индекса. Он работает примерно так же, как поисковая система Google или Bing. Запрос `filter` оценивает логическое выражение во всех *фильтруемых* полях индекса. В отличие от запросов `search` запросы `filter` ищут точные вхождения в содержимом полей. Это значит, что такие запросы нужно вводить с учетом регистра.

Запросы этих двух типов можно использовать вместе или по отдельности. Если они используются вместе, сначала ко всему индексу применяется фильтр, а затем в результатах фильтрации выполняется поиск. Поэтому фильтры могут повысить производительность запросов, так как они уменьшают количество документов, которые поисковый запрос должен обработать.

Синтаксис выражений фильтра — это подмножество [языка фильтра OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Для поисковых запросов можно использовать [упрощенный синтаксис](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) или [синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) (они описаны ниже).

### <a name="simple-query-syntax"></a>Простой синтаксис запросов
[Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) — это язык запросов, используемый в службе поиска Azure по умолчанию. Простой синтаксис запросов поддерживает ряд общих операторов поиска, включая AND, OR, NOT, а также фразы, суффиксы и операторы приоритета.

### <a name="lucene-query-syntax"></a>синтаксис запросов Lucene
[Синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) позволяет использовать широко распространенный и выразительный язык запросов, разработанный как часть библиотеки [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Этот синтаксис запросов позволяет с легкостью использовать следующие функции и возможности: [запросы, относящиеся к полям](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [нечеткий поиск](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [поиск с расстоянием](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [повышение значения слов](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [поиск по регулярным выражениям](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [поиск с использованием подстановочных знаков](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [основы синтаксиса](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) и [запросы, содержащие логические операторы](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Упорядочение результатов
Вы можете запросить, чтобы служба поиска Azure упорядочила результаты поискового запроса по значениям в определенном поле. По умолчанию служба поиска Azure упорядочивает результаты поиска, руководствуясь важностью суммы поиска документа. Это значение вычисляется на основании меры [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Если нужно, чтобы служба поиска Azure упорядочивала результаты не по сумме поиска, а по другому значению, вы можете воспользоваться поисковым параметром `orderby` . Вы можете указать значение параметра `orderby`, чтобы включить имена полей и вызовы в [`geo.distance()`функцию ](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) для геопространственных значений. После каждого выражения можно поставить символ `asc`, чтобы указать, что результаты требуется представить по возрастанию, или символ `desc`, если результаты нужно упорядочить по убыванию. По умолчанию результаты сортируются по возрастанию.

## <a name="paging"></a>Разбиение по страницам
Служба поиска Azure позволяет легко разбить результаты поиска по страницам. С помощью параметров `top` и `skip` вы можете отправлять такие поисковые запросы, результаты которых возвращаются в управляемых упорядоченных подмножествах. В них поиск можно выполнять проверенными способами с помощью пользовательского интерфейса. При получении этих маленьких подмножеств вы можете получать также сведения о количестве документов во всех результатах поиска.

Дополнительные сведения о разбивке результатов поиска на страницы см. в статье [Разбивка результатов поиска на страницы в службе поиска Azure](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Выделение совпадений
В службе поиска Azure вы можете легко выделить конкретную часть результатов поиска, которые соответствуют поисковому запросу. Для этого воспользуйтесь параметрами `highlight`, `highlightPreTag` и `highlightPostTag`. Вы можете указать, в каких *поддерживающих поиск* полях следует выделять вхождения. Кроме того, можно указать конкретные теги строк, которые нужно добавлять к началу и концу вхождений, которые возвращает служба поиска Azure.




<!--HONumber=Dec16_HO2-->


