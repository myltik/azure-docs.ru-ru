<properties
	pageTitle="Создание первого конвейера фабрики данных Azure с помощью редактора фабрики данных"
	description="В этом учебнике вы создадите образец конвейера фабрики данных Azure с помощью редактора фабрики данных на портале Azure."
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
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Построение первого конвейера фабрики данных Azure с помощью редактора фабрики данных (на портале Azure)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Из этой статьи вы узнаете, как создать конвейер с помощью [портала предварительной версии Azure](https://portal.azure.com/). В учебнике рассматриваются следующие действия:

1.	Создание фабрики данных.
2.	Создание связанных служб (хранилищ данных и служб вычислений) и наборов данных.
3.	Создание конвейера.

Здесь не приводятся общие сведения о службе фабрики данных Azure. Подробный обзор службы см. в статье [Введение в фабрику данных Azure](data-factory-introduction.md).

> [AZURE.IMPORTANT]Перед выполнением этого учебника, пожалуйста, перейдите к статье [Обзор учебника](data-factory-build-your-first-pipeline.md) и выполните необходимые предварительные действия.

## Шаг 1. Создание фабрики данных

1.	Войдите на [портал предварительной версии Azure](http://portal.azure.com/) и выполните следующие действия.
	1.	Нажмите кнопку **СОЗДАТЬ** в меню слева. 
	2.	Щелкните **Аналитика данных** в колонке **Создание**.
	3.	Щелкните **Фабрика данных** в колонке **Аналитика данных**.

		![Колонка "Создание"](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	В колонке **Создание фабрики данных** введите **DataFactoryMyFirstPipeline** в поле «Имя».

	![Создать колонку "Фабрика данных"](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Имя фабрики данных Azure должно быть глобально уникальным. При возникновении ошибки **Имя фабрики данных DataFactoryMyFirstPipeline недоступно** измените имя этой фабрики данных (например, на ваше\_имяDataFactoryMyFirstPipeline) и попробуйте создать ее снова. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
	>  
	> В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

3.	Если вы еще не создали группу ресурсов, сделайте это сейчас. Для этого:
	1.	Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ**.
	2.	Выберите команду **Создать группу ресурсов** в колонке **Группа ресурсов**.
	3.	В колонке **Создание группы ресурсов** введите значение **ADF** в поле **Имя**.
	4.	Нажмите кнопку **ОК**.
	
		![Создать группу ресурсов](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	После выбора группы ресурсов убедитесь, что используется именно та подписка, в которой вы хотите создать фабрику данных.
5.	Нажмите кнопку **Создать** в колонке **Создание фабрики данных**.
6.	Созданная фабрика данных появится на **начальной панели** портала предварительной версии Azure.   

	![Статус создания фабрики данных](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Поздравляем! Вы успешно создали свою первую фабрику данных! Ее содержимое отображается на специальной странице. 	

	![Колонка "Фабрика данных"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Из следующих разделов учебника вы узнаете, как создать связанные службы, наборы данных и конвейер.

## Шаг 2. Создание связанных служб и наборов данных
На этом этапе учетная запись хранения Azure и кластер Azure HDInsight по требованию будут связаны с фабрикой данных. После чего будет создан набор данных, представляющий результаты выполнения сценария Hive.

### Создание связанной службы хранения Azure
1.	Щелкните **Создание и развертывание** в колонке **ФАБРИКА ДАННЫХ** для **DataFactoryFirstPipeline**. Откроется редактор фабрики данных. 
	 
	![Плитка "Создание и развертывание"](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	Щелкните **Создать хранилище данных** и выберите **Хранилище Azure**.
	
	![Связанная служба хранения Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	В редакторе отобразится сценарий JSON для создания связанной службы хранилища Azure. 
4. Замените **account name** именем вашей учетной записи хранения Azure, а **account key** — ключом доступа к учетной записи хранения Azure. Сведения о получении ключа доступа к хранилищу см. в разделе [Просмотр, копирование и повторное создание ключей доступа к хранилищу](../storage/storage-create-storage-account.md/#view-copy-and-regenerate-storage-access-keys).
5. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

	![Кнопка "Развернуть"](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Создание связанной службы Azure HDInsight
Теперь вы создадите связанную службу для кластера HDInsight по требованию, который будет использоваться для выполнения сценария Hive.

1. В **редакторе фабрики данных** на панели команд щелкните **Создать службу вычислений** и выберите **Кластер HDInsight по запросу**.

	![Новая служба вычислений](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Вставьте приведенный ниже фрагмент JSON в окно Draft-1. Он описывает свойства, которые будут использоваться для создания кластера HDInsight по требованию. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
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
3. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд. 
4. Убедитесь, что обе службы (StorageLinkedService и HDInsightOnDemandLinkedService) отображаются в иерархическом представлении слева.

	![Иерархическое представление со связанными службами](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### Создание выходного набора данных
Теперь вы создадите выходной набор данных, представляющий данные, которые хранятся в хранилище BLOB-объектов Azure.

1. На панели команд **редактора фабрики данных** щелкните **Создать набор данных** и выберите **Хранилище больших двоичных объектов Azure**.  

	![Новый набор данных](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Вставьте приведенный ниже фрагмент JSON в окно Draft-1. Он создает набор данных с именем **AzureBlobOutput** и определяет структуру данных, получаемых с помощью сценария Hive. Кроме того, нужно указать, что результаты будут храниться в контейнере больших двоичных объектов с именем **data** и в папке с именем **partitioneddata**. В разделе **availability** указывается частота, с которой будет создаваться выходной набор данных (ежемесячно).
	
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

3. Нажмите кнопку **Развернуть** на панели команд, чтобы развернуть только что созданный набор данных.
4. Убедитесь, что набор данных успешно создан.

	![Иерархическое представление со связанными службами](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Шаг 3. Создание конвейера
На этом этапе вы создадите свой первый конвейер.

1. В **редакторе фабрики данных** щелкните **значок многоточия (...)**, а затем выберите команду **Создать конвейер**.
	
	![Кнопка "Создать конвейер"](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Вставьте приведенный ниже фрагмент JSON в окно Draft-1.

	> [AZURE.IMPORTANT]В JSON-файле замените свойство **storageaccountname** именем вашей учетной записи хранения.

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
 
	Этот фрагмент создает конвейер из одного действия, использующего Hive для обработки данных в кластере HDInsight.
	
	Файл сценария Hive **partitionweblogs.hql** хранится в учетной записи хранения Azure (задается с помощью свойства scriptLinkedService с именем **StorageLinkedService**) и в контейнере с именем **script**.

	Раздел **extendedProperties** используется для настройки параметров среды выполнения, которые будут передаваться в сценарий Hive в качестве значений конфигурации (например, ${hiveconf:PartitionedData}).

	Активный период конвейера задается с помощью свойств **start** и **end**.

	В JSON действия указывается, что сценарий Hive будет выполняться с использованием ресурса, указанного связанной службой **HDInsightOnDemandLinkedService**.
3. Чтобы развернуть конвейер, нажмите кнопку **Развернуть** на панели команд.
4. Убедитесь, что конвейер отображается в иерархической структуре.

	![Иерархическое представление с конвейером](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Поздравляем! Вы создали свой первый конвейер!
6. Щелкните **X**, чтобы закрыть колонки редактора фабрики данных и вернуться в колонку фабрики данных, а затем щелкните плитку **Схема**.
  
	![Плитка "Схема"](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. В представлении диаграммы вы увидите все конвейеры и наборы данных, используемые в этом учебнике.
	
	![Представление схемы](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. В представлении схемы дважды щелкните набор данных **AzureBlobOutput**. Вы увидите срез, который обрабатывается в данный момент.

	![Выборка](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. По завершении обработки срез перейдет в состояние **Готово**. Обратите внимание, что создание кластера HDInsight по требованию, как правило, занимает некоторое время. 

	![Выборка](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. Когда срез будет в состоянии **Готово**, проверьте выходные данные в папке **partitioneddata** контейнера **data** в хранилище больших двоичных объектов.  
 

 

## Дальнейшие действия
В этой статье вы создали конвейер с действием преобразования (действие HDInsight), которое выполняет сценарий Hive в кластере HDInsight по требованию. Сведения о том, как копировать данные из хранилища больших двоичных объектов Azure в SQL Azure с помощью действия копирования, см. в разделе [Учебник. Копирование данных из хранилища больших двоичных объектов Azure в Azure SQL](./data-factory-get-started.md).
  

## Отправить отзыв
Мы будем очень благодарны за ваш отзыв об этой статье. Отправьте его [по электронной почте](mailto:adfdocfeedback@microsoft.com?subject=data-factory-build-your-first-pipeline-using-editor.md).

<!---HONumber=Nov15_HO2-->