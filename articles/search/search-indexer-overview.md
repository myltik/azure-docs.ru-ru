---
title: "Начало работы со службой поиска Azure | Документация Майкрософт"
description: "Обход содержимого базы данных Azure SQL, DocumentDB или службы хранилища Azure для извлечения доступных для поиска данных и заполнения индекса службы поиска Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4bcd31a200024a182ee3d5a21bcbcb621fed595f
ms.openlocfilehash: fd46641709d260f8b468556972aae14205fdb515
ms.lasthandoff: 01/19/2017

---

# <a name="indexers-in-azure-search"></a>Индексаторы в службе поиска Azure
> [!div class="op_single_selector"]
>
> * [Обзор](search-indexer-overview.md)
> * [Портал](search-import-data-portal.md)
> * [Azure SQL;](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [DocumentDB](search-howto-index-documentdb.md)
> * [Хранилище BLOB-объектов (предварительная версия)](search-howto-indexing-azure-blob-storage.md)
> * [Хранилище таблиц (предварительная версия)](search-howto-indexing-azure-tables.md)
>
>

**Индексатор** в службе поиска Azure — это программа-обходчик, которая извлекает доступные для поиска данные и метаданные из внешнего источника данных, а затем заполняет индекс, сопоставляя поля в индексе и источнике данных. Этот подход иногда называют моделью извлечения, так как запрос данных службой не предполагает написание кода, который передает данные в индекс.

Индексатор можно использовать как отдельное средство для приема данных. Кроме того, можно использовать комбинацию методов, предусматривающих использование индексатора для загрузки только некоторых полей в индексе.

Вы можете запускать индексаторы по требованию или при регулярном обновлении данных, выполняемом каждые пятнадцать минут. Более частые обновления требуют использования модели отправки, выполняющей одновременное обновление данных в службе поиска Azure и источнике внешних данных.

## <a name="approaches-for-creating-and-managing-indexers"></a>Способы создания индексаторов и управления ими
Вы можете создавать общедоступные индексаторы (например, Azure SQL или DocumentDB), а также управлять ими, используя следующие средства.

* [Портал > мастер импорта данных;](search-get-started-portal.md)
* [API REST службы](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [Пакет SDK для .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Основные этапы настройки
В индексаторах реализованы уникальные функции для работы с источниками данных. Поэтому тип индексатора будет определять особенности настройки источника данных или индексатора. Тем не менее всем индексаторам присущи сходные структура и требования. Ниже описаны действия, общие для всех индексаторов.

### <a name="step-1-create-an-index"></a>Шаг 1. Создание индекса
Хотя индексатор автоматизирует некоторые задачи, связанные с приемом данных, это не распространяется на создание индекса. Перед началом работы у вас должен быть стандартный индекс с полями, которые соответствуют полям во внешнем источнике данных. Дополнительные сведения о структурировании индексатора см. в статье, посвященной [созданию индекса (с использованием REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Шаг 2. Создание источника данных
Индексатор извлекает данные из **источника данных** , который содержит такие сведения, как строка подключения. Сейчас поддерживаются следующие источники данных:

* [база данных Azure SQL (или SQL Server на виртуальных машинах Azure);](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](search-howto-index-documentdb.md)
* [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md), которое используется для извлечения текста из документов в формате PDF, Office, HTML и XML
* [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)

Источники данных настраиваются и управляются независимо от индексаторов, которые их используют. Это означает, что источник данных может использоваться несколькими индексаторами для одновременной загрузки нескольких индексов.

### <a name="step-3create-and-schedule-the-indexer"></a>Шаг 3. Создание и планирование индексатора
Определение индексатора — это конструкция, которая позволяет указать индекс, источник данных и расписание. Индексатор может ссылаться на источник данных из другой службы, если этот источник данных принадлежит к той же подписке. Дополнительные сведения о структурировании индексатора см. в статье, посвященной [созданию индекса (с использованием REST API службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основной идеей, можно переходить к изучению требований и задач, связанных с каждым типом источника данных.

* [база данных Azure SQL (или SQL Server на виртуальных машинах Azure);](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [DocumentDB](search-howto-index-documentdb.md)
* [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md), которое используется для извлечения текста из документов в формате PDF, Office, HTML и XML
* [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)
* [Индексирование BLOB-объектов в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-csv-blobs.md)
* [Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-json-blobs.md)

