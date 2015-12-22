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
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Построение конвейера фабрики данных Azure с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


В этой статье описано, как создать конвейер с помощью шаблонов диспетчера ресурсов Azure. В учебнике рассматриваются следующие действия:

1.	Создание фабрики данных.
2.	Создание связанных служб (хранилищ данных и служб вычислений) и наборов данных.
3.	Создание конвейера.

 

## Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе "Обзор учебника", необходимо установить на компьютер Azure PowerShell.

- **Установка Azure PowerShell**. Чтобы установить последнюю версию Azure PowerShell на локальном компьютере, следуйте указаниям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- Здесь не приводятся общие сведения о службе фабрики данных Azure. Подробный обзор службы см. в статье [Введение в фабрику данных Azure](data-factory-introduction.md). 
- Сведения о создании шаблонов диспетчера ресурсов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md). 
 

## Шаг 1. Создание шаблона диспетчера ресурсов Azure

Создайте файл JSON с именем **ADFTutorialARM.json** в папке **C:\\ADFGetStarted** со следующим содержимым:

> [AZURE.IMPORTANT]Измените значения переменных **storageAccountName** и **storageAccountKey**. Измените значение переменной **dataFactoryName**, так как имя должно быть уникальным.

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
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
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
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
	                        "properties": {
	                            "description": "My first Azure Data Factory pipeline using ARM",
	                            "activities": [
	                              {
	                                "type": "HDInsightHive",
	                                "typeProperties": {
	                                  "scriptPath": "script/partitionweblogs.hql",
	                                  "scriptLinkedService": "StorageLinkedService",
	                                  "defines": {
                                        "partitionedtable": "[concat('wasb://data@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"	                                  }
	                                },
	                                "outputs": [
	                                  {
	                                    "name": "[variables('blobOutputDataset')]"
	                                  }
	                                ],
	                                "scheduler": {
	                                    "frequency": "Month",
	                                    "interval": 1
	                                },
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
	            ]
	        }
	    ]
	}


## Шаг 2: Развертывание сущностей фабрики данных с помощью шаблона диспетчера ресурсов Azure

1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription SubscriptionName**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
1. Выполните следующую команду, чтобы развернуть сущности фабрики данных с помощью шаблона, созданного на шаге 1. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Мониторинг конвейера
 
1.	Войдя на [портал Azure](http://portal.azure.com/), щелкните **Обзор** и выберите **Фабрики данных**. ![Просмотреть все -> Фабрики данных](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	В колонке **Фабрики данных** выберите созданную фабрику данных (**TutorialFactoryARM**).	
2.	В колонке **Фабрика данных** для своей фабрики данных щелкните элемент**Схема**. ![Плитка «Схема»](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	В **представлении схемы** вы увидите все конвейеры и наборы данных, используемые в этом руководстве.
	
	![Представление схемы](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. В представлении схемы дважды щелкните набор данных **AzureBlobOutput**. Вы увидите срез, который обрабатывается в данный момент.

	![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Как только обработка завершится, срез перейдет в состояние **Готово**. Обратите внимание, что создание кластера HDInsight по требованию, как правило, занимает некоторое время. 

	![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Когда срез перейдет в состояние **Готово**, проверьте выходные данные в папке **partitioneddata** контейнера **data** в хранилище BLOB-объектов.  
 

<!---HONumber=AcomDC_1217_2015-->