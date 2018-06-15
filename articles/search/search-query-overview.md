---
title: Запросы в службе поиска Azure | Документация Майкрософт
description: В службе поиска Azure можно создавать поисковые запросы и с помощью параметров поиска фильтровать, сортировать и уточнять результаты.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/13/2017
ms.openlocfilehash: 50e27cbc485259ea5ce744ba71feee5e90293bcb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792915"
---
# <a name="queries-in-azure-search"></a>Запросы в службе поиска Azure
> [!div class="op_single_selector"]
> * [Обзор](search-query-overview.md)
> * [Портал](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

При отправке поисковых запросов в службу поиска Azure нужно не только ввести в поле поиска приложения определенные слова, но и задать ряд параметров. Эти параметры запросов обеспечивают более точное управление при [работе с полнотекстовым поиском](search-lucene-query-architecture.md).

Ниже приведен список, в котором кратко описано стандартное использование параметров запроса в службе поиска Azure. Подробную информацию о параметрах запроса и их поведении см. в материалах, посвященных [REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) и [пакету SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Типы запросов
Служба поиска Azure предлагает множество параметров для создания расширенных запросов. Два основных типа запросов, которые вы будете использовать, — это `search` и `filter`. Запрос `search` ищет как минимум одно вхождение во всех *поддерживающих поиск* полях индекса. Он работает примерно так же, как поисковая система Google или Bing. Запрос `filter` оценивает логическое выражение во всех *фильтруемых* полях индекса. В отличие от запросов `search` запросы `filter` ищут точные вхождения в содержимом полей. Это значит, что такие запросы нужно вводить с учетом регистра.

Запросы этих двух типов можно использовать вместе или по отдельности. Если они используются вместе, сначала ко всему индексу применяется фильтр, а затем в результатах фильтрации выполняется поиск. Поэтому фильтры могут повысить производительность запросов, так как они уменьшают количество документов, которые поисковый запрос должен обработать.

Синтаксис выражений фильтра — это подмножество [языка фильтра OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Для поисковых запросов можно использовать [упрощенный синтаксис](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) или [синтаксис запросов Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) (они описаны ниже).

### <a name="simple-query-syntax"></a>Простой синтаксис запросов
[Простой синтаксис запросов](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) — это язык запросов, используемый в службе поиска Azure по умолчанию. Простой синтаксис запросов поддерживает ряд общих операторов поиска, включая AND, OR, NOT, а также фразы, суффиксы и операторы приоритета.

### <a name="lucene-query-syntax"></a>Синтаксис запросов Lucene
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

## <a name="try-out-query-syntax"></a>Знакомство с синтаксисом запросов

Лучший способ разобраться в синтаксисе запросов — практиковаться в их отправке с последующим просмотром результатов.

+ Используйте [обозреватель поиска](search-explorer.md) на портале Azure. Развернув [индекс образцов](search-get-started-portal.md) и используя средства на портале, можно быстро создать запрос индекса.

+ Используйте Telerik Fiddler или Chrome Postman для отправки запросов индекса, переданного в службу поиска. Оба средства поддерживают вызовы REST к конечной точке HTTP. 