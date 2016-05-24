<properties
	pageTitle="Создание первой фабрики данных (PowerShell) | Microsoft Azure"
	description="В этом учебнике вы создадите образец конвейера фабрики данных Azure с помощью Azure PowerShell."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"
/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/16/2016"
	ms.author="spelluru"/>

# Создание первой фабрики данных Azure с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Обзор учебника](data-factory-build-your-first-pipeline.md)
- [Редактор фабрики данных](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Шаблон диспетчера ресурсов](data-factory-build-your-first-pipeline-using-arm.md)


Из этой статьи вы узнаете, как создать свою первую фабрику данных с помощью Azure PowerShell.


## Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе "Обзор учебника", необходимо установить на компьютер Azure PowerShell.

- Прежде чем продолжать, **обязательно** прочтите [обзорную статью](data-factory-build-your-first-pipeline.md) по этой теме и выполните предварительные условия.
- **Azure PowerShell**. Чтобы установить последнюю версию Azure PowerShell на локальном компьютере, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- В этой статье рассматриваются не все командлеты фабрики данных (необязательный раздел). Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/dn820234.aspx). 

Если вы используете Azure PowerShell **более ранней версии, чем 1.0**, используйте командлеты, описанные [здесь][cmdlet-reference]. Перед использованием командлетов фабрики данных также потребуется выполнить следующие команды:
 
1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	1. Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	2. Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	3. Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
4. Переключитесь в режим AzureResourceManager, так как командлеты фабрики данных Azure доступны только в этом режиме (**Switch-AzureMode AzureResourceManager**).

Дополнительные сведения см. в статье [Устаревший командлет Switch-AzureMode в Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


## Создание фабрики данных

На этом этапе с помощью Azure PowerShell создается фабрика данных Azure с именем **FirstDataFactoryPSH**. Фабрика данных может иметь один или несколько конвейеров. Конвейер может содержать одно или несколько действий. Это может быть, например, действие копирования, копирующее данные из исходного хранилища данных в конечное, и действие HDInsight Hive для выполнения скрипта Hive, преобразующего входные данные в выходные данные продукта. Начнем с создания фабрики данных.

1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureRmSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureRmSubscription <Name of the subscription>**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
3. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем ADFTutorialResourceGroup. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup указывайте на этом уроке выбранную вами группу.
4. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем **FirstDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT] Имя фабрики данных Azure должно быть глобально уникальным. Если появится сообщение об ошибке **Имя фабрики данных FirstDataFactoryPSH недоступно** измените это имя (например, на yournameFirstDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
	> 
	> В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

Прежде чем создавать конвейер, необходимо создать несколько сущностей фабрики данных. Сначала создайте связанные службы, чтобы связать хранилища данных и вычисления со своим хранилищем данных, и определите входные и выходные наборы данных, которые будут представлять данные в связанных хранилищах. Затем создайте конвейер с действием, в котором используются эти наборы данных.

## Создание связанных служб 
На этом этапе вы свяжете учетную запись службы хранилища Azure и используемый по запросу кластер Azure HDInsight с фабрикой данных. В этом примере учетная запись хранения Azure содержит входные и выходные данные для конвейера. Для выполнения скрипта Hive, указанного в действии конвейера, в этом примере используется связанная служба HDInsight. Необходимо определить, какие данные хранилища и службы вычислений используются в сценарии, и связать эти службы с фабрикой данных путем создания связанных служб.

### Создание связанной службы хранения Azure
На этом этапе вы свяжете учетную запись хранения Azure с фабрикой данных. В целях данного руководства используйте одну и ту же учетную запись хранения Azure для хранения входных и выходных данных и файла скрипта HQL.

1. Создайте в папке C:\\ADFGetStarted JSON-файл с именем StorageLinkedService.json со следующим содержимым. Создайте папку ADFGetStarted, если она еще не существует.

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	Замените **account name** именем своей учетной записи хранения Azure, а **account key** — ключом доступа к учетной записи хранения Azure. Сведения о получении ключа доступа к хранилищу см. в разделах о [просмотре, копировании и повторном создании ключей доступа к хранилищу](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. В Azure PowerShell перейдите в папку ADFGetStarted.
3. Создать связанную службу можно с помощью командлета **New-AzureRmDataFactoryLinkedService**. В этом командлете и в других командлетах фабрики данных, которые используются в этом учебнике, требуется передача значений для параметров *ResourceGroupName* и *DataFactoryName*. Кроме того, можно использовать командлет **Get-AzureRmDataFactory**, чтобы получить и передать объект **DataFactory** без необходимости ввода параметров *ResourceGroupName* и *DataFactoryName* при каждом запуске командлета. Выполните следующую команду, чтобы назначить выходные данные командлета **Get-AzureRmDataFactory** переменной **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Теперь выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Если вы не выполнили командлет **Get-AzureRmDataFactory** и не присвоили выходные данные переменной **$df**, вам нужно указать значения параметров *ResourceGroupName* и *DataFactoryName* следующим образом.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	Если вы закроете Azure PowerShell во время выполнения описанных в руководстве инструкций, при следующем запуске Azure PowerShell вам нужно будет запустить командлет **Get-AzureRmDataFactory**, чтобы выполнить инструкции, описанные в руководстве.

### Создание связанной службы Azure HDInsight
На этом этапе вы свяжете используемый по запросу кластер HDInsight с фабрикой данных. Кластер HDInsight автоматически создается в среде выполнения и удаляется после завершения обработки и простоя в течение указанного времени. Вместо используемого по запросу кластера HDInsight можно использовать собственный кластер HDInsight. Дополнительные сведения см. в статье [Связанные службы вычислений](data-factory-compute-linked-services.md).

1. Создайте в папке **C:\\ADFGetStarted** JSON-файл **HDInsightOnDemandLinkedService**.json со следующим содержимым.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	В следующей таблице приведены описания свойств JSON, используемых в этом фрагменте кода.

	| Свойство | Описание |
	| :------- | :---------- |
	| Version (версия) | Указывает, что версия создаваемого кластера HDInsight — 3.2. | 
	| ClusterSize (размер кластера) | Создает кластер HDInsight с одним узлом. | 
	| TimeToLive (срок жизни) | Указывает, сколько времени может простаивать кластер HDInsight, прежде чем он будет удален. |
	| linkedServiceName (имя связанной службы) | Указывает имя учетной записи хранения, в которой будут храниться журналы, создаваемые HDInsight. |

	Обратите внимание на следующее.
	
	- С помощью вышеупомянутого файла JSON фабрика данных создает кластер HDInsight **под управлением Windows**. Также можно создать кластер HDInsight **под управлением Linux**. Дополнительные сведения см. в статье [Связанная служба HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
	- Вместо доступного по запросу кластера HDInsight можно использовать **собственный кластер HDInsight**. Дополнительные сведения см. в статье [Связанная служба Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
	- Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. Это сделано специально. В случае связанной службы HDInsight по запросу кластер HDInsight создается для обработки каждого среза данных (если не используется динамический кластер **timeToLive**) и удаляется по ее завершении.
	
		По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Подобные контейнеры имеют имена вида "adf**имя\_вашей\_фабрики\_данных**-**имя\_связанной\_службы**-метка\_даты\_и\_времени". Для удаления контейнеров из хранилища BLOB-объектов Azure пользуйтесь такими средствами, как [обозреватель хранилищ Майкрософт](http://storageexplorer.com/).

	Дополнительные сведения см. в статье [Связанная служба HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
2. Выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу с именем HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Создание наборов данных
На этом этапе вы создадите наборы данных, которые представляют входные и выходные данные для обработки Hive. Эти наборы данных ссылаются на службу **StorageLinkedService**, созданную ранее в ходе работы с этим руководством. Точки связанной службы указывают на учетную запись хранения Azure, а наборы данных указывают контейнер, папку и имя файла в хранилище, в котором содержатся входные и выходные данные.

### Создание входного набора данных
1. Создайте в папке **C:\\ADFGetStarted** JSON-файл с именем **InputTable.json** со следующим содержимым.

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	Приведенный выше JSON-файл определяет набор данных с именем **AzureBlobInput**, представляющий входные данные для действия в конвейере. Кроме того, файл указывает, что входные данные размещаются в контейнере BLOB-объектов **adfgetstarted** и в папке **inputdata**.

	В следующей таблице приведены описания свойств JSON, используемых в этом фрагменте кода.

	| Свойство | Описание |
	| :------- | :---------- |
	| type | Для свойства типа задано значение AzureBlob, так как данные хранятся в хранилище BLOB-объектов Azure. |  
	| linkedServiceName (имя связанной службы) | Ссылается на созданную ранее службу StorageLinkedService. |
	| fileName | Это необязательное свойство. Если это свойство не указано, выбираются все файлы из папки folderPath. В этом случае обрабатывается только файл input.log. |
	| type | Файлы журнала представлены в текстовом формате, поэтому мы используем значение TextFormat. | 
	| columnDelimiter | Столбцы в файлах журнала разделяются запятыми (,). |
	| frequency и interval | Для свойства frequency задано значение Month, а для свойства interval — значение 1. Это означает, что срезы входных данных доступны ежемесячно. | 
	| external | Это свойство имеет значение true, если входные данные не создаются службой фабрики данных. | 

2. Выполните следующую команду в Azure PowerShell, чтобы создать набор данных фабрики данных.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Создание выходного набора данных
Теперь создайте выходной набор данных, представляющий выходные данные, которые хранятся в хранилище BLOB-объектов Azure.

1. Создайте в папке **C:\\ADFGetStarted** JSON-файл с именем **OutputTable.json** со следующим содержимым.

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

	Указанный выше JSON-файл определяет набор данных с именем **AzureBlobOutput**, представляющий выходные данные для действия в конвейере. Кроме того, файл указывает, что результаты хранятся в контейнере BLOB-объектов **adfgetstarted** и в папке **partitioneddata**. В разделе **availability** указывается частота, с которой будет создаваться выходной набор данных (ежемесячно).

2. Выполните следующую команду в Azure PowerShell, чтобы создать набор данных фабрики данных.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Создание конвейера
На этом этапе вы создадите свой первый конвейер с действием **HDInsightHive**. Обратите внимание, что срез входных данных создается ежемесячно (frequency: Month, interval: 1), срез выходных данных создается ежемесячно, а свойство scheduler для действия тоже имеет значение Month (см. ниже). Параметры выходного набора данных (outputs) и планировщика действия (scheduler) должны совпадать. В настоящее время расписание активируется с помощью выходного набора данных, поэтому его необходимо создать, даже если действие не создает никаких выходных данных. Если действие не принимает никаких входных данных, входной набор данных можно не создавать. Свойства, используемые в следующем фрагменте JSON, описаны в конце этого раздела.


1. Создайте в папке C:\\ADFGetStarted JSON-файл MyFirstPipelinePSH.json со следующим содержимым.

	> [AZURE.IMPORTANT] В JSON-файле замените свойство **storageaccountname** именем своей учетной записи хранения.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2016-04-01T00:00:00Z",
		        "end": "2016-04-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	Этот фрагмент создает конвейер из одного действия, использующего Hive для обработки данных в кластере HDInsight.
	
	Файл **partitionweblogs.hql** скрипта Hive хранится в учетной записи хранения Azure (указывается с помощью свойства scriptLinkedService с именем **StorageLinkedService**) в папке **script** в контейнере **adfgetstarted**.

	Раздел **defines** используется для настройки параметров среды выполнения, которые будут переданы в скрипт Hive в качестве значений конфигурации Hive (например, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

	Активный период конвейера задается с помощью свойств **start** и **end**.

	В JSON действия укажите, что скрипт Hive будет выполняться в среде вычислений, указанной в свойстве **linkedServiceName**, — **HDInsightOnDemandLinkedService**.

	> [ACOM.NOTE] Подробные сведения о свойствах JSON, используемых в приведенном выше примере, см. в статье [Анатомия конвейера](data-factory-create-pipelines.md#anatomy-of-a-pipeline). 
2.  Убедитесь, что файл **input.log** отображается в папке **adfgetstarted/inputdata** в хранилище BLOB-объектов Azure, и выполните следующую команду, чтобы развернуть конвейер. Так как время в свойствах **start** и **end** задано в прошлом, а для свойства **isPaused** задано значение false, конвейер (действие в конвейере) запускается сразу после развертывания. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Поздравляем! Вы создали свой первый конвейер с помощью Azure PowerShell!

## Отслеживание конвейера
На этом этапе Azure PowerShell будет использоваться для мониторинга процессов в фабрике данных Azure.

1. Выполните командлет **Get-AzureRmDataFactory** и назначьте выходные данные переменной **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Выполните командлет **Get-AzureRmDataFactorySlice**, чтобы получить сведения обо всех срезах в таблице **EmpSQLTable** (выходной таблице конвейера).

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. Вы должны увидеть результат, аналогичный приведенному ниже.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. Выполните командлет **Get-AzureRmDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для конкретного среза.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	Вы должны увидеть результат, аналогичный приведенному ниже.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Вы можете выполнять этот командлет до тех пор, пока не увидите срез со статусом **Ready** (Готово) или **Failed** (Сбой). Когда срез перейдет в состояние «Готово», проверьте выходные данные в папке **partitioneddata** контейнера **adfgetstarted** в хранилище BLOB-объектов. Обратите внимание, что создание кластера HDInsight по требованию занимает некоторое время.

	![выходные данные](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## Дальнейшие действия
В этой статье описывается создание конвейера с помощью действия преобразования (действие HDInsight), которое по требованию выполняет сценарий Hive в кластере Azure HDInsight. Сведения о том, как копировать данные из хранилища BLOB-объектов Azure в SQL Azure с помощью действия копирования, см. в статье [Учебник. Копирование данных из хранилища BLOB-объектов Azure в Azure SQL](./data-factory-get-started.md).

### Ссылки
| Раздел | Описание |
| :---- | :---- |
| [Справочник по командлетам фабрики данных](https://msdn.microsoft.com/library/azure/dn820234.aspx) | См. полную документацию по командлетам фабрики данных. |
| [Конвейеры](data-factory-create-pipelines.md) | Эта статья поможет вам понять сущность конвейеров и действий в фабрике данных Azure, а также научиться с их помощью создавать комплексные рабочие процессы, управляемые данными, для конкретных бизнес-сценариев. |
| [Наборы данных](data-factory-create-datasets.md) | Эта статья поможет вам понять, что такое наборы данных в фабрике данных Azure.
| [Планирование и выполнение](data-factory-scheduling-and-execution.md) | Здесь объясняются аспекты планирования и исполнения в модели приложений фабрики данных. |
| [Мониторинг конвейеров фабрики данных Azure и управление ими](data-factory-monitor-manage-pipelines.md) | В этой статье описываются мониторинг, управление и отладка конвейеров. В ней также приводятся инструкции по настройке оповещений в случае сбоев. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_0518_2016-->