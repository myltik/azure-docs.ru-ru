<properties
	pageTitle="Создание фабрики данных (шаблон диспетчера ресурсов Azure) | Microsoft Azure"
	description="Работая с этим руководством, вы создадите образец конвейера фабрики данных Azure с помощью шаблона диспетчера ресурсов Azure."
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
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# Руководство. Создание фабрики данных Azure с помощью шаблона диспетчера ресурсов Azure
> [AZURE.SELECTOR]
- [Обзор учебника](data-factory-build-your-first-pipeline.md)
- [Редактор фабрики данных](data-factory-build-your-first-pipeline-using-editor.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Шаблон диспетчера ресурсов](data-factory-build-your-first-pipeline-using-arm.md)


В этой статье вы найдете информацию о том, как создать свою первую фабрику данных Azure с помощью шаблона диспетчера ресурсов Azure (ARM).


## Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе "Обзор учебника", необходимо установить на компьютер Azure PowerShell.

- Прежде чем продолжать, **обязательно** прочтите [обзорную статью](data-factory-build-your-first-pipeline.md) по этой теме и выполните предварительные условия. 
- **Установите Azure PowerShell**. Чтобы установить последнюю версию Azure PowerShell на локальном компьютере, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- Здесь не приводятся общие сведения о службе фабрики данных Azure. Подробный обзор службы см. в статье [Введение в службу фабрики данных Azure](data-factory-introduction.md). 
- Сведения о создании шаблонов диспетчера ресурсов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md). 


## Шаг 1. Создание шаблона диспетчера ресурсов Azure

Создайте в папке **C:\\ADFGetStarted** файл JSON с именем **ADFTutorialARM.json** со следующим содержимым:

> [AZURE.IMPORTANT] Измените значения переменных **storageAccountName** и **storageAccountKey**. Измените значение переменной **dataFactoryName**, так как имя должно быть уникальным.

Этот шаблон позволяет создавать следующие сущности фабрики данных:

1. **Фабрика данных** с именем **TutorialDataFactoryARM**. Фабрика данных может иметь один или несколько конвейеров. Конвейер может содержать одно или несколько действий. Это может быть, например, действие копирования, копирующее данные из исходного хранилища данных в конечное, и действие HDInsight Hive для выполнения скрипта Hive, преобразующего входные данные в выходные данные продукта. 
2. Две **связанные службы**: **StorageLinkedService** и **HDInsightOnDemandLinkedService**. Эти связанные службы связывают учетную запись хранения Azure и кластер Azure HDInsight по запросу с фабрикой данных. В этом примере учетная запись хранения Azure содержит входные и выходные данные для конвейера. Для выполнения скрипта Hive, указанного в действии конвейера, в этом примере используется связанная служба HDInsight. Необходимо определить, какие данные хранилища и службы вычислений используются в сценарии, и связать эти службы с фабрикой данных путем создания связанных служб. 
3. Два **набора данных** (входной и выходной): **AzureBlobInput** и **AzureBlobOutput**. Эти наборы данных представляют входные и выходные данные для обработки Hive. Эти наборы данных ссылаются на службу **StorageLinkedService**, созданную ранее в ходе работы с этим руководством. Точки связанной службы указывают на учетную запись хранения Azure, а наборы данных указывают контейнер, папку и имя файла в хранилище, в котором содержатся входные и выходные данные.   

Щелкните вкладку **С помощью редактора фабрики данных**, чтобы перейти к статье с подробными сведениями о свойствах JSON, используемых в этом шаблоне.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
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
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
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
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}


Обратите внимание на следующее.

- С помощью вышеупомянутого файла JSON фабрика данных создает кластер HDInsight **под управлением Windows**. Также можно создать кластер HDInsight **под управлением Linux**. Дополнительные сведения см. в статье [Связанная служба HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
- Вместо доступного по запросу кластера HDInsight можно использовать **собственный кластер HDInsight**. Дополнительные сведения см. в статье [Связанная служба Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. Это сделано специально. В случае связанной службы HDInsight по запросу кластер HDInsight создается для обработки каждого среза данных (если не используется динамический кластер **timeToLive**) и удаляется по ее завершении.

	По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Подобные контейнеры имеют имена вида "adf**имя\_вашей\_фабрики\_данных**-**имя\_связанной\_службы**-метка\_даты\_и\_времени". Для удаления контейнеров из хранилища BLOB-объектов Azure пользуйтесь такими средствами, как [обозреватель хранилищ Майкрософт](http://storageexplorer.com/).

Дополнительные сведения см. в статье [Связанная служба HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [AZURE.NOTE] Еще один пример шаблона ARM для создания фабрики данных Azure можно найти на [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Шаг 2: Развертывание сущностей фабрики данных с помощью шаблона диспетчера ресурсов Azure

1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription SubscriptionName**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
1. Выполните следующую команду, чтобы развернуть сущности фабрики данных с помощью шаблона, созданного на шаге 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Мониторинг конвейера
 
1.	Войдя на [портал Azure](https://portal.azure.com/), щелкните **Обзор** и выберите **Фабрики данных**. ![Просмотреть все -> Фабрики данных](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	В колонке **Фабрики данных** выберите созданную фабрику данных (**TutorialFactoryARM**).	
2.	В колонке **Фабрика данных** для своей фабрики данных щелкните элемент **Схема**. ![Плитка «Схема»](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	В **представлении схемы** вы увидите все конвейеры и наборы данных, используемые в этом руководстве.
	
	![Представление схемы](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. В представлении схемы дважды щелкните набор данных **AzureBlobOutput**. Вы увидите срез, который обрабатывается в данный момент.

	![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Как только обработка завершится, срез перейдет в состояние **Готово**. Обратите внимание, что создание кластера HDInsight по требованию обычно занимает некоторое время (около 20 минут). 

	![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Когда срез перейдет в состояние **Готово**, проверьте выходные данные в папке **partitioneddata** контейнера **adfgetstarted** в хранилище BLOB-объектов.  
 

<!---HONumber=AcomDC_0309_2016-->