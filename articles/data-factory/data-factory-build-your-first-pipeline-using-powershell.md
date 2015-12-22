<properties
	pageTitle="Построение конвейера фабрики данных Azure с помощью Azure PowerShell"
	description="В этом учебнике вы создадите образец конвейера фабрики данных Azure с помощью Azure PowerShell."
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
	ms.topic="hero-article"
	ms.date="12/08/2015"
	ms.author="spelluru"/>

# Построение конвейера фабрики данных Azure с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


В этой статье описано, как создать конвейер с помощью Azure PowerShell. В учебнике рассматриваются следующие действия:

1.	Создание фабрики данных.
2.	Создание связанных служб (хранилищ данных и служб вычислений) и наборов данных.
3.	Создание конвейера.

> [AZURE.IMPORTANT]Здесь не приводятся общие сведения о службе фабрики данных Azure. Подробный обзор службы см. в статье [Введение в фабрику данных Azure](data-factory-introduction.md).
> 
> В этой статье рассматриваются не все командлеты фабрики данных. Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/dn820234.aspx).

## Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе "Обзор учебника", необходимо установить на компьютер Azure PowerShell.

- **Azure PowerShell**. Чтобы установить последнюю версию Azure PowerShell на локальном компьютере, следуйте указаниям в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md). 

Если вы используете Azure PowerShell **более ранней версии, чем 1.0**, используйте командлеты, описанные [здесь][cmdlet-reference]. Перед использованием командлетов фабрики данных также потребуется выполнить следующие команды:
 
1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	1. Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	2. Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	3. Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
4. Переключитесь в режим AzureResourceManager, так как командлеты фабрики данных Azure доступны только в этом режиме: **Switch-AzureMode AzureResourceManager**.


## Шаг 1. Создание фабрики данных

На этом этапе с помощью Azure PowerShell создается фабрика данных Azure с именем ADFTutorialDataFactoryPSH.

1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription <Name of the subscription>**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
3. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем ADFTutorialResourceGroup. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup указывайте на этом уроке выбранную вами группу.
4. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем DataFactoryMyFirstPipelinePSH.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

	> [AZURE.IMPORTANT]Имя фабрики данных Azure должно быть глобально уникальным. Если появится сообщение об ошибке **Имя фабрики данных DataFactoryMyFirstPipelinePSH недоступно**, измените имя (например, используйте ваше\_имя\_ADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
	> 
	> В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

Из следующих разделов учебника вы узнаете, как создать связанные службы, наборы данных и конвейер.

## Шаг 2. Создание связанных служб и наборов данных
На этом этапе учетная запись хранения Azure и кластер Azure HDInsight по требованию будут связаны с фабрикой данных. После чего будет создан набор данных, представляющий результаты выполнения сценария Hive.

### Создание связанной службы хранения Azure
1.	Создайте в папке C:\\ADFGetStartedPSH JSON-файл с именем StorageLinkedService.json со следующим содержимым. Создайте папку ADFGetStartedPSH, если она еще не существует.

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

	Замените **account name** именем своей учетной записи хранения Azure, а **account key** — ключом доступа к учетной записи хранения Azure. Сведения о получении ключа доступа к хранилищу см. в разделе [Просмотр, копирование и повторное создание ключей доступа к хранилищу](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	В Azure PowerShell перейдите в папку ADFGetStartedPSH.
3.	Создать связанную службу можно с помощью командлета **New-AzureRmDataFactoryLinkedService**. В этом командлете и в других командлетах фабрики данных, которые используются в этом учебнике, требуется передача значений для параметров *ResourceGroupName* и *DataFactoryName*. Кроме того, можно использовать командлет **Get-AzureRmDataFactory**, чтобы получить объект и передать объект **DataFactory** без необходимости ввода параметров *ResourceGroupName* и *DataFactoryName* при каждом запуске командлета. Выполните следующую команду, чтобы назначить выходные данные командлета **Get-AzureRmDataFactory** переменной **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Теперь выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Если вы не выполнили командлет **Get-AzureRmDataFactory** и не присвоили выходные данные переменной **$df**, вам нужно указать значения параметров *ResourceGroupName* и *DataFactoryName* следующим образом.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Если вы закроете Azure PowerShell во время выполнения описанных в руководстве инструкций, при следующем запуске Azure PowerShell вам нужно будет запустить командлет **Get-AzureRmDataFactory**, чтобы выполнить инструкции, описанные в руководстве.

### Создание связанной службы Azure HDInsight
Теперь будет создана связанная служба для кластера Azure HDInsight по требованию, которая будет использоваться для выполнения сценария Hive.

1.	Создайте в папке C:\\ADFGetStartedPSH JSON-файл HDInsightOnDemandLinkedService.json со следующим содержимым.


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

	Свойство | Описание
	-------- | -----------
	Version (версия) | Указывает, что версия создаваемого кластера HDInsight — 3.2.
	ClusterSize (размер кластера) | Создает кластер HDInsight с одним узлом.
	TimeToLive (срок жизни) | Указывает, сколько времени может простаивать кластер HDInsight, прежде чем он будет удален.
	linkedServiceName (имя связанной службы) | Указывает имя учетной записи хранения, в которой будут храниться журналы, создаваемые HDInsight.
2. Выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу с именем HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Создание выходного набора данных
Теперь вы создадите выходной набор данных, представляющий данные, которые хранятся в хранилище BLOB-объектов Azure.

1.	Создайте в папке C:\\ADFGetStartedPSH JSON-файл с именем EmpSQLTable.json со следующим содержимым.

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
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

	В предыдущем примере описано, как создать набор данных с именем **AzureBlobOutput** и определить структуру данных, получаемых с помощью сценария Hive. Кроме того, нужно указать, что результаты будут храниться в контейнере BLOB-объектов с именем **data** в папке с именем **partitioneddata**. В разделе **availability** указывается периодичность создания выходного набора данных — ежемесячно.

2. Выполните следующую команду в Azure PowerShell, чтобы создать набор данных фабрики данных.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Шаг 3. Создание конвейера
На этом этапе вы создадите свой первый конвейер.

1.	Создайте в папке C:\\ADFGetStartedPSH JSON-файл MyFirstPipelinePSH.json со следующим содержимым.

	> [AZURE.IMPORTANT]В JSON-файле замените свойство **storageaccountname** именем своей учетной записи хранения.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}

	В предыдущем примере описано, как создать конвейер, содержащий одно действие. Это действие использует Hive для обработки данных в кластере HDInsight.

	Файл partitionweblogs.hql сценария Hive хранится в учетной записи хранения Azure (указывается с помощью свойства scriptLinkedService с именем StorageLinkedService) в контейнере с именем **script**.

	Раздел **defines** используется для настройки параметров среды выполнения, которые будут переданы в сценарий Hive в качестве значений конфигурации (например, ${hiveconf:PartitionedData}).

	Активный период конвейера задается с помощью свойств **start** и **end**.

	В действии JSON вы указываете, что сценарий Hive будет выполняться на компьютере, определяемом связанной службой **HDInsightOnDemandLinkedService**.
2. Выполните следующую команду для создания таблицы фабрики данных.

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Поздравляем! Вы создали свой первый конвейер с помощью Azure PowerShell!

### <a name="MonitorDataSetsAndPipeline"></a> Мониторинг наборов данных и конвейера
На этом этапе Azure PowerShell будет использоваться для мониторинга процессов в фабрике данных Azure.

1.	Выполните командлет **Get-AzureRmDataFactory** и назначьте выходные данные переменной **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.	Выполните командлет **Get-AzureRmDataFactorySlice**, чтобы получить сведения обо всех срезах в таблице **EmpSQLTable** (выходной таблице конвейера).

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

	Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. Вы должны увидеть результат, аналогичный приведенному ниже.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0

3.	Выполните командлет **Get-AzureRmDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для конкретного среза.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

	Вы должны увидеть результат, аналогичный приведенному ниже.

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Вы можете выполнять этот командлет до тех пор, пока не увидите срез со статусом Ready (Готово) или Failed (Сбой). Когда срез будет в состоянии Ready (Готово), проверьте выходные данные в папке partitioneddata контейнера data в хранилище BLOB-объектов. Обратите внимание, что создание кластера HDInsight по требованию занимает некоторое время.

Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/azure/dn820234.aspx).



## Дальнейшие действия
В этой статье описывается создание конвейера с помощью действия преобразования (действие HDInsight), которое по требованию выполняет сценарий Hive в кластере Azure HDInsight. Инструкции по копированию данных из хранилища BLOB-объектов Azure в SQL Azure с помощью действия копирования см. в статье [Руководство по копированию данных из хранилища BLOB-объектов Azure в Azure SQL](./data-factory-get-started.md).


[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_1217_2015-->