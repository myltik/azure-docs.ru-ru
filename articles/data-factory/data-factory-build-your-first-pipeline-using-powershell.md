<properties
	pageTitle="Построение первого конвейера с помощью фабрики данных Azure"
	description="В этом учебнике показано, как создать конвейер, преобразующий данные с помощью Azure HDInsight, используя Azure PowerShell."
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
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Построение конвейера фабрики данных Azure с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Из этой статьи вы узнаете, как создать конвейер с помощью Azure PowerShell. В учебнике рассматриваются следующие действия:

1.	Создание фабрики данных.
2.	Создание связанных служб (хранилищ данных и служб вычислений) и наборов данных.
3.	Создание конвейера.

Здесь не приводятся общие сведения о службе фабрики данных Azure. Подробный обзор службы см. в статье [Введение в фабрику данных Azure](data-factory-introduction.md).

## Шаг 1. Создание фабрики данных

На этом шаге с помощью Azure PowerShell создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**.

1. Запустите **Azure PowerShell** и выполните следующие команды. Не закрывайте Azure PowerShell до завершения этого учебника. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые применяются для входа на предварительную версию портала Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure. 
2. Переключитесь в режим **AzureResourceManager**, поскольку командлеты фабрики данных Azure доступны только в этом режиме.

		Switch-AzureMode AzureResourceManager 
3. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup указывайте на этом уроке выбранную вами группу. 
4. Выполните командлет **New-AzureDataFactory**, чтобы создать фабрику данных с именем **DataFactoryMyFirstPipelinePSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"


	Имя фабрики данных Azure должно быть глобально уникальным. При выводе сообщения об ошибке **Имя фабрики данных DataFactoryMyFirstPipelinePSH недоступно** измените имя (например, на [вашеимя]ADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике.

Из следующих разделов учебника вы узнаете, как создать связанные службы, наборы данных и конвейер.

## Шаг 2. Создание связанных служб и наборов данных
На этом этапе вы свяжете учетную запись хранения Azure и кластер Azure HDInsight по требованию с фабрикой данных, а затем создадите набор данных, представляющий результаты выполнения сценария Hive.

### Создание связанной службы хранения Azure
1.	Создайте файл JSON с именем **StorageLinkedService.json** в **C:\\ADFGetStartedPSH** со следующим содержимым. Создайте папку ADFGetStartedPSH, если она еще не существует.

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

	Замените **accountname** на имя вашей учетной записи хранения Azure, а **account key** — на ключ доступа к хранилищу. Сведения о том, как получить ключ доступа к хранилищу, см. в разделе [Просмотр, копирование и повторное создание ключей доступа к хранилищу](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	В **Azure PowerShell** перейдите в папку **ADFGetStartedPSH**.
3.	Чтобы создать связанную службу, можно использовать командлет **New-AzureDataFactoryLinkedService**. В этом командлете и в других командлетах фабрики данных, которые используются в этом учебнике, требуется передача значений для параметров **ResourceGroupName** и **DataFactoryName**. Кроме того, можно использовать командлет **Get-AzureDataFactory**, чтобы получить объект DataFactory и передать этот объект без необходимости ввода параметров ResourceGroupName и DataFactoryName при каждом запуске командлета. Выполните следующую команду, чтобы назначить выходные данные командлета **Get-AzureDataFactory** переменной **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Теперь выполните командлет **New-AzureDataFactoryLinkedService**, чтобы создать связанную службу **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Если вы не выполнили командлет **Get-AzureDataFactory** и назначили выходные данные переменной **$df**, то потребуется указать значения параметров ResourceGroupName и DataFactoryName следующим образом.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Если вы закроете Azure PowerShell посреди этого учебника, то придется запустить командлет Get-AzureDataFactory при следующем запуске Azure PowerShell, чтобы завершить изучение учебника.

### Создание связанной службы Azure HDInsight
Теперь вы создадите связанную службу для кластера HDInsight по требованию, который будет использоваться для выполнения сценария Hive.

1.	Создайте JSON-файл **HDInsightOnDemandLinkedService.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым. 


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:05:00",
		      "jobsContainer": "adfjobs",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	В следующей таблице приведены описания свойств JSON, используемых в этом фрагменте кода.
		
	Свойство | Описание
	-------- | -----------
	Version (версия) | Указывает, что версия создаваемого кластера HDInsight — 3.1. 
	ClusterSize (размер кластера) | Создает кластер HDInsight с одним узлом. 
	TimeToLive (срок жизни) | Указывает, сколько времени может простаивать кластер HDInsight, прежде чем он будет удален.
	JobsContainer (контейнер заданий) | Указывает имя контейнера заданий, в котором будут храниться журналы, создаваемые HDInsight.
	linkedServiceName (имя связанной службы) | Указывает имя учетной записи хранения, в которой будут храниться журналы, создаваемые HDInsight.
2. Выполните командлет **New-AzureDataFactoryLinkedService**, чтобы создать связанную службу **HDInsightOnDemandLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Создание выходного набора данных
Теперь вы создадите выходной набор данных, представляющий данные, которые хранятся в хранилище BLOB-объектов Azure.

1.	Создайте JSON-файл **EmpSQLTable.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым.

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
	Он создает набор данных с именем **AzureBlobOutput** и определяет структуру данных, получаемых с помощью сценария Hive. В нем мы также указываем, что результаты должны храниться в BLOB-контейнере **data** в папке **partitioneddata**. В разделе **availability** мы задаем частоту создания выходного набора данных (ежемесячно).

2. Выполните следующую команду в Azure PowerShell, чтобы создать таблицу фабрики данных.

		New-AzureDataFactoryTable $df -File .\OutputTable.json

## Шаг 3. Создание конвейера
На этом этапе вы создадите свой первый конвейер.

1.	Создайте JSON-файл **MyFirstPipelinePSH.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым: 

	> [AZURE.IMPORTANT]Замените в нем **storageaccountname** на имя вашей учетной записи хранения.

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
		
	Этот фрагмент создает конвейер из одного действия, использующего Hive для обработки данных в кластере HDInsight.
	
	Файл сценария Hive **partitionweblogs.hql** хранится в учетной записи хранения Azure (задается с помощью связанной службы **StorageLinkedService**) и в контейнере с именем **script**.

	Раздел **extendedProperties** используется для настройки параметров запуска, которые будут передаваться в сценарий Hive в качестве значений конфигурации (например, ${hiveconf:PartitionedData}).

	Период активности конвейера задается с помощью свойств **start** и **end**.

	В JSON действия указывается, что сценарий Hive будет выполняться с использованием ресурса, указанного связанной службой **HDInsightOnDemandLinkedService**.
2. Выполните следующую команду для создания таблицы фабрики данных. 
	
		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Поздравляем! Вы создали свой первый конвейер с помощью Azure PowerShell!

### <a name="MonitorDataSetsAndPipeline"></a>Мониторинг наборов данных и конвейера
На этом шаге вы будете использовать Azure PowerShell для мониторинга процессов в фабрике данных Azure.

1.	Выполните командлет **Get-AzureDataFactory** и назначьте выходные данные переменной $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH
 
2.	Выполните командлет **Get-AzureDataFactorySlice** для получения сведений обо всех срезах в таблице **EmpSQLTable**, которая является выходной таблицей конвейера.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. Вы должны увидеть результат, аналогичный приведенному ниже:

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0
	
3.	Выполните командлет **Get-AzureDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для **конкретного** среза.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	Вы должны увидеть результат, аналогичный приведенному ниже:

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

	Вы можете выполнять этот командлет до тех пор, пока не увидите срез со статусом Ready (Готово) или Failed (Сбой). Когда срез будет в состоянии Ready (Готово), проверьте выходные данные в папке partitioneddata контейнера data в хранилище BLOB-объектов. Обратите внимание, что создание кластера HDInsight по требованию, как правило, занимает некоторое время.

Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/azure/dn820234.aspx).

 

## Дальнейшие действия
В этой статье вы создали конвейер с действием преобразования (действие HDInsight), которое выполняет сценарий Hive в кластере HDInsight по требованию. Информацию о том, как копировать данные из хранилища BLOB-объектов Azure в Azure SQL, см. в учебнике [Копирование данных из хранилища BLOB-объектов Azure в Azure SQL](./data-factory-get-started.md).
  

<!---HONumber=July15_HO5-->