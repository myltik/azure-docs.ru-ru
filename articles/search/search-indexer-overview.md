---
title: "Начало работы со службой поиска Azure | Документация Майкрософт"
description: "Обход содержимого базы данных Azure SQL, Azure Cosmos DB или службы хранилища Azure для извлечения доступных для поиска данных и заполнения индекса службы поиска Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2017
ms.author: heidist
ms.openlocfilehash: c1d393064313ea65d5226378172530115c338f86
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="indexers-in-azure-search"></a>Индексаторы в службе поиска Azure
> [!div class="op_single_selector"]
>
> * [Обзор](search-indexer-overview.md)
> * [Портал](search-import-data-portal.md)
> * [Azure SQL;](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md)
> * [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)
>

*Индексатор* в службе поиска Azure — это программа-обходчик, которая извлекает доступные для поиска данные и метаданные из внешнего источника данных, а затем заполняет индекс, сопоставляя поля в индексе и источнике данных. Этот подход иногда называют моделью извлечения, так как запрос данных службой не предполагает написание кода, который передает данные в индекс.

Индексаторы основаны на типах источников данных или платформах, при этом есть отдельные индексаторы для SQL Server в Azure, Cosmos DB, хранилище таблиц Azure и хранилище BLOB-объектов и так далее.

Индексатор можно использовать как отдельное средство для приема данных. Кроме того, можно использовать комбинацию методов, предусматривающих использование индексатора для загрузки только некоторых полей в индексе.

Вы можете запускать индексаторы по требованию или при регулярном обновлении данных, выполняемом каждые пятнадцать минут. Более частые обновления требуют использования модели отправки, выполняющей одновременное обновление данных в службе поиска Azure и источнике внешних данных.

## <a name="approaches-for-creating-and-managing-indexers"></a>Способы создания индексаторов и управления ими

Далее представлены способы создания индексаторов и управления ими.

* [Портал > мастер импорта данных;](search-get-started-portal.md)
* [API REST службы](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [ПАКЕТ SDK .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Изначально новый индексатор объявляется в качестве предварительной версии функции. Предварительные версии функций добавляют в API (REST и .NET), а затем после выхода общедоступной версии интегрируются на портале. При оценке нового индексатора следует запланировать написание кода.

## <a name="basic-configuration-steps"></a>Основные этапы настройки
В индексаторах реализованы уникальные функции для работы с источниками данных. Поэтому тип индексатора будет определять особенности настройки источника данных или индексатора. Тем не менее всем индексаторам присущи сходные структура и требования. Ниже описаны действия, общие для всех индексаторов.

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Индексатор извлекает данные из *источника данных*, который содержит такие сведения, как строка подключения и, возможно, учетные данные. Сейчас поддерживаются следующие источники данных:

* [база данных Azure SQL (или SQL Server на виртуальных машинах Azure);](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [База данных Azure Cosmos](search-howto-index-documentdb.md)
* [Хранилище больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) для выбранных типов содержимого
* [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)

Источники данных настраиваются и управляются независимо от индексаторов, которые их используют. Это означает, что источник данных может использоваться несколькими индексаторами для одновременной загрузки нескольких индексов.

### <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Хотя индексатор автоматизирует некоторые задачи, связанные с приемом данных, это не распространяется на создание индекса. Перед началом работы у вас должен быть стандартный индекс с полями, которые соответствуют полям во внешнем источнике данных. Дополнительные сведения о структурировании индексатора см. в статье, посвященной [созданию индекса (с использованием REST API службы поиска Azure)](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Справочные сведения по сопоставлению полей см. в статье [Сопоставление полей в индексаторах Поиска Azure](search-indexer-field-mappings.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Шаг 3. Создание и планирование индексатора
Определение индексатора — это конструкция, которая позволяет указать индекс, источник данных и расписание. Индексатор может ссылаться на источник данных из другой службы, если этот источник данных принадлежит к той же подписке. Дополнительные сведения о структурировании индексатора см. в статье, посвященной [созданию индекса (с использованием REST API службы поиска Azure)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основной идеей, можно переходить к изучению требований и задач, связанных с каждым типом источника данных.

* [база данных Azure SQL (или SQL Server на виртуальных машинах Azure);](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [База данных Azure Cosmos](search-howto-index-documentdb.md)
* [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md)
* [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)
* [Индексирование BLOB-объектов в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-csv-blobs.md)
* [Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-json-blobs.md)
