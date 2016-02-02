<properties
	pageTitle="Индексаторы в службе поиска Azure | Microsoft Azure | Размещенная облачная служба поиска"
	description="Обход содержимого базы данных для извлечения доступных для поиска данных и заполнения индекса службы поиска Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="01/24/2016"
	ms.author="heidist"/>

# Индексаторы в службе поиска Azure
> [AZURE.SELECTOR]
- [Overview](search-indexer-overview.md)
- [Portal](search-import-data-portal.md)
- [Blob Storage (preview)](search-howto-indexing-azure-blob-storage.md)
- [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)

**Индексатор** в службе поиска Azure — это программа-обходчик, которая извлекает доступные для поиска данные и метаданные из внешнего источника данных, а затем заполняет индекс, сопоставляя поля в индексе и источнике данных. Этот подход иногда называют моделью извлечения, так как запрос данных службой не предполагает написание кода, который передает данные в индекс.

Индексатор можно использовать как отдельное средство для приема данных. Кроме того, можно использовать комбинацию методов, предусматривающих использование индексатора для загрузки только некоторых полей в индексе.

Индексаторы могут запускаться по требованию или при регулярном обновлении данных, выполняемом каждые пятнадцать минут. Более частые обновления требуют использования модели отправки, выполняющей одновременное обновление данных в службе поиска Azure и источнике внешних данных.

Индексатор извлекает данные из **источника данных**, который содержит такие сведения, как строки подключения. Сейчас поддерживаются следующие источники данных:

- [база данных Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) (или SQL Server в виртуальных машинах Azure);
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md) (сейчас доступно в режиме предварительной версии; извлекает текст из файлов PDF, документов Office, HTML, XML).

Источники данных настраиваются и управляются независимо от индексаторов, которые их используют. Это означает, что источник данных может использоваться несколькими индексаторами для одновременной загрузки нескольких индексов.

Оба средства ([пакет SDK для .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) и [REST API службы](https://msdn.microsoft.com/library/azure/dn946891.aspx)) поддерживают управление индексаторами и источниками данных. Кроме того, вы также можете настроить индексатор на портале, используя мастер **импорта данных**.

<!---HONumber=AcomDC_0128_2016-->