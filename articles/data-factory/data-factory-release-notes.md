<properties 
	pageTitle="Фабрика данных – заметки о выпуске | Microsoft Azure" 
	description="Заметки о выпуске фабрики данных" 
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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Заметки о выпуске фабрики данных Azure
См. статью [Фабрика данных: журнал изменений API .NET](data-factory-api-change-log.md), чтобы узнать об изменениях в пакете SDK .NET для фабрики данных в конкретной версии.

## Заметки о выпуске фабрики данных от 17.07.2015 г.
Следующие изменения JSON представлены в выпуске Azure PowerShell от июля 2015 г.

## Обновление типов связанных служб, таблиц и действий
Тип ресурса | Текущее имя в формате JSON | Новое имя в формате JSON
------------- | -------------------- | ----------------
Связанная служба (источник данных) | AzureSqlLinkedService; | AzureSqlDatabase
Связанная служба (источник данных) | AzureStorageLinkedService | AzureStorage
Связанная служба (источник данных) | DocumentDbLinkedService | DocumentDb
Связанная служба (источник данных) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
Связанная служба (источник данных) | OnPremisesOracleLinkedService | OnPremisesOracle
Связанная служба (источник данных) | OnPremisesSqlLinkedService | OnPremisesSqlServer
Связанная служба (источник данных) | OnPremisesMySqlLinkedService | OnPremisesMySql
Связанная служба (источник данных) | OnPremisesDb2LinkedService | OnPremisesDb2
Связанная служба (источник данных) | OnPremisesTeradataLinkedService | OnPremisesTeradata
Связанная служба (источник данных) | OnPremisesSybaseLinkedService | OnPremisesSybase
Связанная служба (источник данных) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
Связанная служба (вычисления) | AzureMlLinkedService | AzureML
Связанная служба (вычисления) | HDInsightBYOCLinkedService | HDInsight
Связанная служба (вычисления) | HDInsightOnDemandLinkedService | HDInsightOnDemand
Связанная служба (вычисления) | AzureBatchLinkedService | AzureBatch
Выборка | AzureBlobLocation | AzureBlob
Выборка | AzureTableLocation | AzureTable
Выборка | AzureSqlTableLocation | AzureSqlTable
Выборка | DocumentDbCollectionLocation | DocumentDbCollection 
Выборка | OnPremisesFileSystemLocation | FileShare
Выборка | OnPremisesOracleTableLocation | OracleTable
Выборка | OnPremisesSqlServerTableLocation | SqlServerTable
Выборка | RelationTableLocation | RelationalTable
Действие | CopyActivity | Копировать
Действие | HDInsightActivity (преобразование Hive) | HDInsightHive
Действие | HDInsightActivity (преобразование Pig) | HDInsightPig
Действие | HDInsightActivity (преобразование MapReduce) | HDInsightMapReduce
Действие | HDInsightActivity (потоковая передача) | HDInsightHadoopStreaming
Действие | AzureMLBatchScoringActivity | AzureMLBatchScoring
Действие | StoredProcedureActivity | SqlServerStoredProcedure

## Новый элемент typeProperties
Новый элемент **typeProperties** содержит свойства конкретного типа для связанной службы, таблицы или действия.

### Старая связанная служба JSON
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### Новая связанная служба JSON
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

Обратите внимание на следующее.

- Свойство **type** перемещено на один уровень вверх и имеет значение **AzureStorage** (изменено с **AzureStorageLinkedService** на **AzureStorage**). 
- Новый элемент **typeProperties**, содержащий свойства, поддерживаемые связанной службой хранилища Azure (**connectionString** в этом примере).  

### Старый набор данных JSON
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### Новый набор данных JSON

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Обратите внимание на следующее.

- Свойство **type** перемещено на один уровень вверх, а имя свойства **AzureTableLocation** изменено на **AzureTable**.
- **LinkedServiceName** перемещено на один уровень вверх. 
- Элемент **location** теперь удален, и свойства конкретного типа, например **tableName**, указанные в разделе **location**, указаны в новом разделе **typeProperties**.  

### Старое действие JSON

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### Новое действие JSON
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

Обратите внимание на следующее.

- Обратите внимание, что элемент **transformation** заменен новым элементом **typeProperties**.

## Элемент waitOnExternal удален
Элемент **WaitOnExternal** заменен новыми свойствами **external** и **externalData**.

### Старый JSON
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
	        "location": 
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "adftutorial/",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	            "linkedServiceName": "StorageLinkedService"
	        },
	        "availability": 
	        {
	            "frequency": "hour",
	            "interval": 1,
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### Новый JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

Обратите внимание на следующее.

- Свойство **waitOnExternal** удалено из раздела **availability**. 
- Новое свойство **external** добавлено уровнем выше и имеет значение **true** для внешней таблицы. 
- Свойства элемента **waitOnExternal**, такие как **retryInterval**, добавлены в новый раздел **externalData** элемента **Policy**.
- **ExternalData** — необязательный элемент. 
- При использовании элемента **externalData** для свойства **external** должно быть установлено значение **true**. 
 

## Новое свойство copyBehavior для BlobSink
**BlobSink** поддерживает новое свойство с именем **copyBehavior**. Это свойство определяет поведение копирования, если источником является **BlobSource** или **FileSystem**. Существует три возможных значения для свойства **copyBehavior**.

**PreserveHierarchy**: сохраняет иерархию файлов в папке назначения, т. е. относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.


**FlattenHierarchy**: все файлы из исходной папки будут размещены на первом уровне в целевой папке. Целевые файлы будут иметь автоматически сформированное имя.


**MergeFiles**: объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано.
 
## Новое свойство getDebugInfo для всех действий HDInsight
Действия HDInsight (Hive, Pig, MapReduce, потоковая передача Hadoop) поддерживают новое свойство **getDebugInfo**. Свойство **GetDebugInfo** является необязательным элементом. Если для него установлено значение**Failure**, журналы загружаются только при сбое выполнения. Если для него установлено значение **All**, журналы скачиваются всегда, независимо от состояния выполнения. Если для него установлено значение **None**, журналы не загружаются.

  
     

## Заметки о выпуске фабрики данных от 10 апреля 2015 г.
Добавлена возможность просмотра списков **Недавно обновленные срезы** и **Последние срезы с ошибками** в колонке **ТАБЛИЦА**. Эти списки сортируются по времени обновления среза. Время обновления среза изменяется в таких ситуациях:

-  Вы обновляете состояние среза вручную, например используя командлет **Set-AzureRmDataFactorySliceStatus** или щелкнув **ВЫПОЛНИТЬ** в колонке **СРЕЗ** для этого среза.
-  В ходе выполнения состояние среза меняется (например, выполнение началось, выполнение завершилось со сбоем, выполнение завершилось успешно и т. п.).

Щелкните заголовок списков или **... (многоточие)**, чтобы просмотреть расширенный список срезов. Чтобы отфильтровать срезы, выберите пункт **Фильтр** на панели инструментов.
 
Вы по-прежнему можете просматривать срезы, отсортированные по времени среза, щелкнув плитку **Срезы данных (по времени среза)**. Срезы в этих коллекциях упорядочиваются по времени среза. Например, если установлено ежечасное расписание, могут быть такие срезы:
- 4/4/2015 17:00 выполняется,
- 4/4/2015 16:00 успешно и
- 4/4/2015 15:00 неудачно.

Но если старый срез запускается повторно, он не будет отображаться вверху этого списка, несмотря на то что пользователю он может быть наиболее интересен.

## Заметки о выпуске фабрики данных от 31 марта 2015 г.
- Обновленный пакет установки **шлюза управления данными** добавлен в [центр загрузки Майкрософт][adf-gateway-download].
- Добавлена поддержка копирования из **локальной файловой системы в большой двоичный объект Azure**. Дополнительные сведения см. в таких статьях:
	-  [Связанная служба локальной файловой системы.](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Свойства OnPremisesFileSystemLocation в JSON-файле таблицы.](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Поддерживаемые источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx). См. обновленную таблицу копирования и свойства **FileSystemSource**. 
-  Добавлена поддержка копирования из **локальной базы данных Oracle в большой двоичный объект Azure**. Дополнительные сведения см. в таких статьях: 
	-  [Связанная служба локальной базы данных Oracle.](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Свойства OnPremisesOracleTableLocation в таблице JSON.](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Поддерживаемые источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx). См. обновленную таблицу копирования и свойства **OracleSource**.
-  Добавлена возможность выбора кодировки для текстовых файлов в большом двоичном объекте Azure. См. сведения о новом [свойстве encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- При копировании в приемник SQL можно вызвать хранимую процедуру с дополнительными параметрами.    

Дополнительные сведения с примерами см. в сообщении блога [Обновление фабрики данных Azure: новые хранилища данных](https://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/).

## Заметки о выпуске фабрики данных от 27 февраля 2015 г.

### Улучшения
- **Редактор фабрики данных Azure**. Редактор фабрики данных является частью портала Azure и позволяет создавать, изменять и развертывать JSON-файлы, которые определяют связанные службы, наборы данных и конвейеры. Его основная цель — предоставить простой и удобный пользовательский интерфейс для быстрого создания артефактов фабрики данных Azure. При этом не нужно устанавливать Azure PowerShell и использовать командлеты PowerShell. Краткий обзор редактора фабрики данных и видео о работе в нем см. в сообщении блога [Редактор фабрики данных Azure: простой веб-редактор][adf-editor-blog].  

### изменения

## Заметки о выпуске фабрики данных от 26 января 2015 г. ##

### изменения
- Обновленный пакет установки **шлюза управления данными** добавлен в [центр загрузки Майкрософт][adf-gateway-download]. Начиная с этого выпуска, новые версии шлюза управления данными для использования с фабрикой данных Azure можно будет скачивать с данного веб-сайта. Пакет установки подходит как для фабрики данных Azure, так и для Power BI для служб Office 365. Если вы используете обе службы, обратите внимание, что шлюзы для фабрики данных и Power BI должны быть установлены на разных компьютерах и настроены по-разному (см. руководство по работе с фабрикой данных и документацию по Power BI).
- **Действие копирования** теперь поддерживает копирование данных между локальной базой данных SQL Server и базой данных SQL Azure. 
- **SqlSink** поддерживает новое свойство **WriteBatchTimeout**. Это свойство позволяет указывать время ожидания для операции пакетной вставки. Чтобы использовать это свойство для операции гибридного копирования (которая задействует локальный и облачный источники данных), необходима версия шлюза не ниже 1.4. 
- Для **связанной службы SQL Server** добавлена поддержка **проверки подлинности Windows**. 
	- Теперь при создании связанной службы SQL Server на портале вы можете выбрать проверку подлинности Windows и использовать соответствующие учетные данные. Для этого необходима версия шлюза не ниже 1.4. 
	- Если для создания связанной службы SQL Server используется Azure PowerShell, сведения о подключении можно указать в виде простого текста или зашифровать их с помощью обновленного [командлета New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx), а затем в полезных данных JSON связанной службы использовать зашифрованную строку в качестве значения свойства строки подключения. Возможность шифрования пока еще не поддерживается командлетом New-AzureRmDataFactoryEncryptValue. 

## Заметки о выпуске фабрики данных от 11 декабря 2014 г. ##

### Улучшения

- Интеграция машинного обучения Azure
	- Этот выпуск службы фабрики данных Azure позволяет интегрировать фабрику данных Azure с машинным обучением Azure с помощью **AzureMLLinkedService** и **AzureMLBatchScoringActivity**. Дополнительные сведения см. в статье [Создание прогнозирующих конвейеров с помощью фабрики данных и машинного обучения Azure][adf-azure-ml]. 
- Предоставляется состояние версии шлюза
	- При появлении более новой версии шлюза на портале Azure и в выходных данных командлета Get-AzureRmDataFactoryGateway будет отображаться состояние "NewVersionAvailable". Вы можете продолжить изучение портала, загрузить новый установочный файл (.msi) и запустить его для установки последнего шлюза. Дополнительные настройки для этого не требуются.

### изменения

- Удален контейнер JobsContainer из службы HdInsightOnDemandLinkedService.
	- В определении JSON для службы HDInsightOnDemandLinkedService больше не нужно задавать свойство **jobsContainer**. Если для связанной службы по запросу уже задано это свойство, оно будет игнорироваться. Вы можете удалить свойство из определения JSON для связанной службы и обновить определение связанной службы с помощью командлета New-AzureRmDataFactoryLinkedService.
- Дополнительные параметры конфигурации для HDInsightOnDemandLinkedService
	- В этом выпуске мы добавлена поддержка нескольких дополнительных параметров конфигурации для HDInsightOnDemandLinked (кластера HDInsight по запросу). Подробные сведения см. в статье [Свойства ClusterCreateParameters][on-demand-hdi-parameters].
- Удалено расположение шлюза
	- При создании шлюза фабрики данных Azure через портал или с помощью PowerShell (New-AzureRmDataFactoryGateway) теперь не требуется указывать расположение шлюза. Будет наследоваться регион фабрики данных. Таким же образом, больше не требуется настраивать связанную службу сервера SQL Server с помощью свойства JSON "gatewayLocation". Пакет .NET SDK фабрики данных также обновлен для отражения этих изменениями.
	- При использовании более ранней версии SDK и Azure PowerShell вам по-прежнему придется выполнять настройку расположения.
 
     

#### Критические изменения
	
- Действие CustomActivity переименовано в DotNetActivity
	- Интерфейс **ICustomActivity** переименован в **IDotNetActivity**. Вам потребуется обновить пакеты NuGet для Data Factory и заменить строку ICustomActivity на IDotNetActivity в исходном коде пользовательского действия.  
	- В определении JSON настраиваемого действия необходимо изменить тип действия с **CustomActivity** на **DotNetActivity**. 
	- Классы **CustomActivity** и **CustomActivityProperties** переименованы в **DotNetActivity** и **DotNetActivityProperties** соответственно. Наборы их свойств не изменились.

		В более ранней версии SDK и Azure PowerShell можно по-прежнему использовать CustomActivity вместо DotNetActivity.
    
  		Пошаговое руководство по созданию настраиваемого действия и использованию его в конвейере фабрики данных Azure см. в разделе [Использование настраиваемых действий в конвейере фабрики данных Azure][adf-custom-activities].

[adf-azure-ml]: data-factory-azure-ml-batch-execution-activity.md
[adf-custom-activities]: data-factory-use-custom-activities.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=AcomDC_0128_2016-->