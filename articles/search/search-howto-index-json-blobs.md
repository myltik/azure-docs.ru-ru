<properties
pageTitle="Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure"
description="Узнайте, как индексировать BLOB-объекты JSON с помощью службы поиска Azure"
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
ms.date="04/20/2016"
ms.author="eugenesh" />

# Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure 

По умолчанию [индексатор BLOB-объектов службы поиска Azure](search-howto-indexing-azure-blob-storage.md) анализирует BLOB-объекты JSON как один блок текста. Часто требуется сохранить структуру документов JSON. Например, предположим, что у вас есть следующий документ JSON

	{ 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

и вы хотите проанализировать его так, чтобы получить поля "text", "datePublished" и "tags" в индексе поиска.

В этой статье показано, как настроить индексатор BLOB-объектов службы поиска Azure для анализа JSON. Успехов в индексировании!

> [AZURE.IMPORTANT] Сейчас эта функциональность доступна в режиме предварительной версии. Она доступна при использовании REST API версии **2015-02-28-Preview**. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.

## Настройка индексирования JSON

Для индексирования BLOB-объектов JSON используйте индексатор BLOB-объектов в режиме "Анализ JSON". Включите параметр конфигурации `useJsonParser` в свойстве `parameters` определения индексатора:

	{
	  "name" : "my-json-indexer",
	  ... other indexer properties
	  "parameters" : { "configuration" : { "useJsonParser" : true } }
	}

При необходимости используйте **сопоставление полей** для выбора свойств исходного документа JSON, используемых для заполнения целевого индекса поиска. Это подробнее описано ниже.

> [AZURE.IMPORTANT] При использовании режима анализа JSON служба поиска Azure предполагает, что все BLOB-объекты в источнике данных будут объектами JSON. Если необходима поддержка как объектов JSON, так и других объектов в одном источнике данных, сообщите нам об этом на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## Построение документов поиска с помощью сопоставления полей 

Сейчас служба поиска Azure не может индексировать произвольные документы JSON напрямую, так как она поддерживает только простые типы данных, строковые массивы и точки GeoJSON. Однако с помощью **сопоставлений полей** можно выбирать части документа JSON и "поднимать" их в поля верхнего уровня документа поиска. Дополнительные сведения об основах сопоставления полей см. в статье [Сопоставления полей индексатора в поиске Azure устраняют расхождения между источниками данных и индексами поиска](search-indexer-field-mappings.md).

Вернемся к нашему примеру документа JSON:

    { 
		"article" : {
	 		"text" : "A hopefully useful article explaining how to parse JSON blobs",
	        "datePublished" : "2016-04-13" 
			"tags" : [ "search", "storage", "howto" ]    
	    }
	}

Предположим, у вас есть индекс поиска со следующими полями: `text` типа Edm.String, `date` типа Edm.DateTimeOffset и `tags` типа Collection(Edm.String). Чтобы сопоставить JSON с необходимой формой, используйте следующие сопоставления полей:

	"fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	]

Имена полей источника в сопоставлениях задаются с помощью нотации [указателя JSON](http://tools.ietf.org/html/rfc6901). Укажите косую черту (корень документа JSON) и путь до нужного свойства (на произвольном уровне вложенности), разделяя элементы пути косой чертой.

Также можно ссылаться на отдельные элементы массива, используя отсчитываемый от нуля индекс. Например, чтобы выбрать первый элемент массива "tags" из приведенного выше примера, используйте следующее сопоставление полей:

	{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Если имя поля источника в пути сопоставления полей ссылается на свойство, которое не существует в JSON, это сопоставление пропускается без ошибки. Это необходимо для поддержки документов с разными схемами (что часто встречается на практике). Поскольку проверка на ошибки не выполняется, будьте внимательны и не допускайте опечаток в спецификации сопоставления полей.

Если документы JSON содержат только простые свойства верхнего уровня, сопоставление полей может вообще не потребоваться. Например, для следующего документа JSON свойства верхнего уровня "text", "datePublished" и "tags" будут напрямую сопоставляться с соответствующими полями в индексе поиска:
 
	{ 
	   "text" : "A hopefully useful article explaining how to parse JSON blobs",
	   "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
 	}

> [AZURE.NOTE] Сейчас служба поиска Azure поддерживает только анализ одного BLOB-объекта JSON для одного документа поиска. Если BLOB-объекты содержат массивы JSON, которые вы хотите преобразовать в несколько документов поиска, проголосуйте за [данное предложение UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/13431384-parse-blob-containing-a-json-array-into-multiple-d), чтобы помочь нам определить приоритет для этой работы.

## Примеры запросов

Чтобы свести все описанное выше вместе, воспользуйтесь приведенными далее примерами данных.

Источник данных:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "my-blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "optional, my-folder" }
	}   

Индексатор:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "my-json-indexer",
	  "dataSourceName" : "my-blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
  	  ]
	}

## Помогите нам усовершенствовать службу поиска Azure

Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0518_2016-->