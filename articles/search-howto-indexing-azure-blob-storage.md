<properties
pageTitle="Индексирование хранилища BLOB-объектов Azure с помощью службы поиска Azure"
description="Узнайте, как индексировать хранилище BLOB-объектов Azure и извлекать текст из документов с помощью службы поиска Azure."
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="12/09/2015"
ms.author="eugenesh" />

# Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure

Пользователи службы поиска Azure уже достаточно давно могут «автомагически» индексировать некоторые популярные источники данных с помощью индексаторов [базы данных SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) и [Azure DocumentDB](documentdb-search-indexer.md).

Теперь мы добавили поддержку индексирования документов, находящихся в хранилище BLOB-объектов Azure. Многие клиенты обращались к нам с просьбами упростить индексирование документов, хранящихся в больших двоичных объектах, включая файлы в формате PDF, документы Office или HTML-страницы. Раньше этот процесс предполагал написание пользовательского кода для извлечения текста и добавления документов в индекс службы поиска Azure.

> [AZURE.IMPORTANT]Сейчас эта функция доступна в режиме предварительной версии. Она доступна при использовании REST API версии **2015-02-28-Preview**. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.

## Настройка индексирования больших двоичных объектов

Установить и настроить индексатор хранилища BLOB-объектов Azure можно с помощью REST API службы поиска Azure. Вы сможете создать **индексаторы** и **источники данных**, а также управлять ими, как описано в [этой статье](https://msdn.microsoft.com/library/azure/dn946891.aspx). В будущем поддержка индексирования больших двоичных объектов будет добавлена в пакет Azure SDK для .NET и на портал Azure.

Источник данных определяет следующее: какие данные нужно индексировать, какие учетные данные требуются для доступа к этим данным, а также какие политики нужны, чтобы служба поиска Azure могла эффективно определять изменения в данных (новые, измененные или удаленные строки). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

Индексатор — это ресурс, связывающий источники данных с целевыми индексами поиска.

Чтобы настроить индексатор больших двоичных объектов, сделайте следующее.

1. Создайте источник данных типа `azureblob` со ссылкой на контейнер (и, если нужно, — на папку в этом контейнере) в учетной записи хранилища Azure.
	- Передайте строку подключения к учетной записи хранилища как параметр `credentials.connectionString`.
	- Укажите имя контейнера. Можно дополнительно включить папку с помощью параметра `query`
2. Создайте индексатор, связав источник данных с существующим целевым индексом (если у вас нет индекса, создайте его).

Иллюстрация приведена в примере ниже.

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "my-folder" }
	}   

Создайте индексатор со ссылкой на источник данных и целевой индекс. Например:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}


## Поддерживаемые форматы документов

Индексатор больших двоичных объектов может извлечь текст из документов следующих форматов:

- PDF;
- форматы Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (сообщения электронной почты Outlook);  
- HTML
- XML
- ZIP;
- обычные текстовые файлы (включая JSON).  

## Процесс извлечения документа

Служба поиска Azure индексирует каждый документ (большой двоичный объект) указанным ниже образом.

- Все текстовое содержимое документа извлекается в поле строки с именем `content`. Обратите внимание, что сейчас извлечение нескольких документов из одного большого двоичного объекта не поддерживается.
	- Например, CSV-файл индексируется как один документ.
	- Составной или внедренный документ (например, ZIP-архив или документ Word с внедренным сообщением электронной почты Outlook и вложением в формате PDF) также индексируется как один документ.

- В большом двоичном объекте определяемые пользователем свойства метаданных извлекаются без изменений. Свойства метаданных также можно использовать для управления некоторыми аспектами процесса извлечения документа. Дополнительные сведения см. в разделе [Использование пользовательских метаданных для управления извлечением документа](#CustomMetadataControl).

- Стандартные свойства метаданных большого двоичного объекта извлекаются в указанные ниже поля.

	- **metadata\_storage\_name** (Edm.String) — имя файла большого двоичного объекта. Например, для большого двоичного объекта /my-container/my-folder/subfolder/resume.pdf значение этого поля — `resume.pdf`.

	- **metadata\_storage\_path** (Edm.String) — полный универсальный код ресурса большого двоичного объекта, включая учетную запись хранения. Например, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String) — тип содержимого, указанный в коде для отправки большого двоичного объекта. Например, `application/octet-stream`.

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) — последняя измененная метка времени для большого двоичного объекта. Служба поиска Azure использует эту метку времени для определения измененных больших двоичных объектов, чтобы не выполнять повторное полное индексирование.

	- **metadata\_storage\_size** (Edm.Int64) — размер большого двоичного объекта в байтах.

	- **metadata\_storage\_content\_md5** (Edm.String) — хэш MD5 содержимого большого двоичного объекта, если он доступен.

- Определенные для каждого формата документа свойства метаданных извлекаются в поля, список которых приводится [здесь](#ContentSpecificMetadata).

Вам не нужно определять поля для всех перечисленных свойств в индексе поиска — достаточно записать свойства, необходимые для приложения.

> [AZURE.NOTE]Как правило, имена полей в существующем индексе отличаются от имен полей, созданных при извлечении документа. Можно использовать **сопоставления полей** для сопоставления имен свойств, предоставленных службой поиска Azure, с именами полей в индексе поиска.

## Выбор поля ключа документа и работа с разными именами полей

В службе поиска Azure ключ документа однозначно определяет документ. Каждый индекс поиска должен содержать только одно поле ключа типа Edm.String. Поле ключа является обязательным для каждого документа, который добавляется к индексу (собственно, это единственное обязательное для заполнения поле).
   
Следует определить, какие извлеченные поля должны сопоставляться с полем ключа индекса. Основные варианты представлены ниже.

- **metadata\_storage\_name** — удобный вариант, но следует учесть следующее. 1) Имена могут не быть уникальными, так как возможно наличие больших двоичных объектов с одинаковыми именами в разных папках. 2) Имя может содержать символы, недопустимые для использования в ключах документов, например дефисы. Можно работать с недопустимыми символами, активировав параметр `base64EncodeKeys` в свойствах индексатора. После этого обязательно закодируйте ключи документов для передачи во время вызовов API, таких как подстановка. (Например, в .NET для этого можно использовать [метод UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)).

- **metadata\_storage\_path** — использование полного пути гарантирует уникальность, но такой путь определенно содержит символы `/`, [недопустимые для использования в ключе документа](https://msdn.microsoft.com/library/azure/dn857353.aspx). Как упоминалось выше, вы можете закодировать ключи с помощью параметра `base64EncodeKeys`.

- Если ни один вариант вам не подходит, вы можете просто добавить свойства пользовательских метаданных в большие двоичные объекты. Однако для этого варианта требуется, чтобы при передаче эти свойства метаданных добавлялись ко всем большим двоичным объектам. Поскольку ключ является обязательным свойством, все большие двоичные объекты без этого свойства индексироваться не будут.

> [AZURE.IMPORTANT]Если в индексе отсутствует явное сопоставление поля ключа, служба поиска Azure будет автоматически использовать `metadata_storage_path` (второй параметр из указанных выше) в качестве ключа, активировав кодировку ключей base-64.

В этом примере мы выберем поле `metadata_storage_name` в качестве ключа документа. Предположим, что индекс содержит поле ключа с именем `key` и поле `fileSize` для хранения данных о размере документа. Чтобы правильно связать все компоненты при создании или обновлении индексатора, укажите следующие сопоставления полей:

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

Чтобы объединить все компоненты, можно добавить сопоставления полей и активировать кодировку ключей base-64 для существующего индексатора:

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

## Добавочное индексирование и обнаружение удаления

Когда вы настраиваете для индексатора больших двоичных объектов запуск по расписанию, повторно индексируются только измененные большие двоичные объекты. Они определяются меткой времени большого двоичного объекта `LastModified`.

> [AZURE.NOTE]Нет необходимости указывать политику обнаружения изменений — добавочное индексирование будет активировано автоматически.

Выбрать определенные документы, которые будут удалены из индекса, можно с помощью стратегии обратимого удаления. Вместо удаления соответствующих больших двоичных объектов добавьте свойство пользовательских метаданных, указывающее, что они удалены. Затем настройте политику обнаружения обратимого удаления в источнике данных.

> [AZURE.NOTE]Если вы просто удалите большие двоичные объекты (не используя политику обнаружения удаления), соответствующие документы не будут удалены из индекса поиска.

Например, в соответствии с приведенной ниже политикой большой двоичный объект удаляется, если у него есть свойство метаданных `IsDeleted` со значением `true`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<your storage connection string>" },
		"container" : { "name" : "my-container", "query" : "my-folder" },
		"dataDeletionDetectionPolicy" : {
			"@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", 	
			"softDeleteColumnName" : "IsDeleted",
			"softDeleteMarkerValue" : "true"
		}
	}   

<a name="ContentSpecificMetadata"></a>
## Свойства метаданных определенного типа содержимого

В таблице ниже содержатся сводные данные обработки для каждого формата документа, а также описание свойств метаданных, извлеченных службой поиска Azure.

<table style="font-size:12">

<tr>
<th>Формат документа или тип содержимого</th>
<th>Свойства метаданных определенного типа содержимого</th>
<th>Сведения об обработке </th>
</tr>

<tr>
<td>HTML (`text/html`)</td>
<td>
`metadata_content_encoding`<br/>
`metadata_content_type`<br/>
`metadata_language`<br/>
`metadata_description`<br/>
`metadata_keywords`<br/>
`metadata_title`
</td>
<td>Удаление разметки HTML и извлечение текста</td>
</tr>

<tr>
<td>PDF (`application/pdf`)</td>
<td>
`metadata_content_type`<br/>
`metadata_language`<br/>
`metadata_author`<br/>
`metadata_title`
</td>
<td>Извлечение текста, включая внедренные документы (кроме изображений)</td>
</tr>

<tr>
<td>DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_character_count`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`<br/>
`metadata_page_count`<br/>
`metadata_word_count`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>DOC (application/msword)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_character_count`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`<br/>
`metadata_page_count`<br/>
`metadata_word_count`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>XLS (application/vnd.ms-excel)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`<br/>
`metadata_slide_count`<br/>
`metadata_title`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>PPT (application/vnd.ms-powerpoint)</td>
<td>
`metadata_content_type`<br/>
`metadata_author`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`<br/>
`metadata_slide_count`<br/>
`metadata_title`
</td>
<td>Извлечение текста, включая внедренные документы</td>
</tr>

<tr>
<td>MSG (application/vnd.ms-outlook)</td>
<td>
`metadata_content_type`<br/>
`metadata_message_from`<br/>
`metadata_message_to`<br/>
`metadata_message_cc`<br/>
`metadata_message_bcc`<br/>
`metadata_creation_date`<br/>
`metadata_last_modified`<br/>
`metadata_subject`
</td>
<td>Извлечение текста, включая вложения</td>
</tr>

<tr>
<td>ZIP (application/zip)</td>
<td>
`metadata_content_type`
</td>
<td>Извлечение текста из всех документов в архиве</td>
</tr>

<tr>
<td>XML (application/xml)</td>
<td>
`metadata_content_type`</br>
`metadata_content_encoding`</br>
</td>
<td>Удаление разметки XML и извлечение текста </td>
</tr>

<tr>
<td>JSON (application/json)</td>
<td>
`metadata_content_type`</br>
`metadata_content_encoding`
</td>
<td></td>
</tr>

<tr>
<td>Обычный текст (text/plain)</td>
<td>
`metadata_content_type`</br>
`metadata_content_encoding`</br>
</td>
<td></td>
</tr>
</table>

<a name="CustomMetadataControl"></a>
## Использование пользовательских метаданных для управления извлечением документов

Можно добавить свойства метаданных в большой двоичный объект, чтобы управлять некоторыми аспектами индексирования больших двоичных объектов и извлечения документов. В настоящее время поддерживаются следующие свойства.

<table style="font-size:12">

<tr>
<th>Имя свойства</th>
<th>Значение свойства</th>
<th>Пояснение</th>
</tr>

<tr>
<td>AzureSearch_Skip</td>
<td>True</td>
<td>Указывает, что индексатор должен полностью пропустить большой двоичный объект. Не будут извлекаться ни метаданные, ни содержимое. Это полезно, если требуется пропустить определенные типы содержимого или когда обработка определенного большого двоичного объекта постоянно завершается сбоем и индексирование прерывается.
</td>
</tr>

</table>

## Помогите нам усовершенствовать службу поиска Azure

Добавить отзыв или запрос на функцию можно на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).

<!---HONumber=AcomDC_1210_2015-->