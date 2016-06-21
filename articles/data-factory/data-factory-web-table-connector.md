<properties 
	pageTitle="Перемещение данных из веб-таблицы | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из локальной таблицы на веб-странице с помощью фабрики данных Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/02/2016" 
	ms.author="spelluru"/>

# Перемещение данных из источника веб-таблицы с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для копирования данных из таблицы на веб-странице в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает только перемещение данных из веб-таблицы в другие хранилища данных, но не наоборот.

## Пример копирования данных из веб-таблицы в BLOB-объект Azure

В примере ниже показано следующее.

1.	Связанная служба типа [Web](#web-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [WebTable](#WebTable-dataset-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [WebSource](#websource-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные из веб-таблицы каждый час копируются в BLOB-объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

В следующем примере показано, как скопировать данные из веб-таблицы в BLOB-объект Azure. Однако данные можно напрямую копировать в любые приемники, указанные в статье [Действия перемещения данных](data-factory-data-movement-activities.md). Для этого применяется действие копирования в фабрике данных Azure.

**Связанная веб-служба**. В этом примере используется связанная веб-служба с анонимной проверкой подлинности. Сведения о различных типах проверки подлинности, которые можно использовать, см. в разделе [Связанная веб-служба](#web-linked-service-properties).

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "https://en.wikipedia.org/wiki/"
	        }
	    }
	}


**Связанная служба хранения Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных WebTable**. Если для параметра **external** задать значение **true** и указать политику **externalData** (необязательно), фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

> [AZURE.NOTE] Действия для получения индекса таблицы на HTML-странице см. в разделе [Получение индекса таблицы на HTML-странице](#get-index-of-a-table-in-an-html-page).

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 1,
	            "path": "AFI's_100_Years...100_Movies"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/Movies"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **WebSource**, а для типа **sink** — значение **BlobSink**.

Список свойств, поддерживаемых WebSource, см. в разделе [Свойства типа WebSource](#websource-copy-activity-type-properties).
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}


## Свойства связанной веб-службы

В приведенной далее таблице содержится описание элементов JSON, которые относятся к связанной веб-службе.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **Web**. | Да | 
| URL-адрес | URL-адрес источника Web | Да |
| userName | Имя пользователя для обычной проверки подлинности. | Да (для обычной проверки подлинности)
| пароль | Пароль для обычной проверки подлинности. | Да (для обычной проверки подлинности)
| authenticationType | Анонимный, обычный или веб-API. | Да |
| apiKey | Ключ API для проверки подлинности веб-API. | Да (для проверки подлинности веб-API)|   

### Использовать анонимную проверку подлинности

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "https://en.wikipedia.org/wiki/"
	        }
	    }
	}


### Использовать обычную проверку подлинности
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## Свойства типа "Набор данных WebTable"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **WebTable** имеет указанные ниже свойства:

Свойство | Описание | Обязательно
:-------- | :----------- | :--------
type | Тип набора данных. Необходимо задать значение **WebTable** | Да
path | Относительный URL-адрес ресурса, который содержит таблицу. | Нет. Если путь не задан, используется только URL-адрес, указанный в определении связанной службы. 
index | Индекс таблицы в ресурсе. Действия для получения индекса таблицы на HTML-странице см. в разделе [Получение индекса таблицы на HTML-странице](#get-index-of-a-table-in-an-html-page). | Да


**Пример.**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 1,
	            "path": "AFI's_100_Years...100_Movies"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## WebSource — свойства типа "Действие копирования"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **WebSource**, установка дополнительных свойств не поддерживается.

## Получение индекса таблицы на HTML-странице

1. Запустите **Excel 2016** и перейдите на вкладку **Данные**.  
2. На панели инструментов щелкните **Создать запрос**, последовательно выберите **Из других источников** и щелкните **Из Интернета**.
	
	![Меню Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. В диалоговом окне **Из Интернета** введите **URL-адрес**, который будет использоваться в JSON связанной службы (например https://en.wikipedia.org/wiki/) вместе с указанным для набора данных путем (например AFI%27s\_100\_Years...100\_Movies), а затем нажмите кнопку **ОК**. 

	![Диалоговое окно "Из Интернета"](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	В этом примере используется следующий URL-адрес: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies. 
4.  В диалоговом окне **Доступ к веб-содержимому** выберите соответствующий **URL-адрес**, **тип проверки подлинности** и нажмите кнопку **Подключить**. 

	![Диалоговое окно "Доступ к веб-содержимому"](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  В представлении дерева щелкните элемент **таблицы**, чтобы просмотреть содержимое из таблицы, а затем в нижней части экрана нажмите кнопку **Изменить**.  

	![Диалоговое окно навигатора](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. В окне **Редактор запросов** нажмите кнопку **Расширенный редактор** на панели инструментов.

	![Кнопка "Расширенный редактор"](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. В диалоговом окне "Расширенный редактор" число, отображаемое рядом с полем "Источник", является индексом.

	![Расширенный редактор — индекс](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


Если вы работаете с Excel 2013, используйте [Microsoft Power Query для Excel](https://www.microsoft.com/download/details.aspx?id=39379), чтобы получить индекс. Дополнительные сведения см. в статье [Подключение к веб-странице](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8). Приведенные в ней действия аналогичны тем, которые выполняются в [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0608_2016-->