<properties title="Get started using Azure Data Factory" pageTitle="Приступая к работе с фабрикой данных Azure" description="This tutorial shows you how to create a sample data pipeline that copies data from a blob to an Azure SQL Database instance." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Приступая к работе с фабрикой данных Azure
Эта статья поможет вам начать работу с фабрикой данных Azure. Представленный в этой статье учебник показывает, как создать фабрику данных Azure и создать конвейер в фабрике данных, чтобы скопировать образец данных из хранилища больших двоичных объектов Azure в базу данных SQL Azure.

В следующем списке приведены типичные шаги, которые необходимо выполнить разработчикам: 

1.	Создайте **фабрику данных Azure**.
2.	Создайте **связанные службы** для связи хранилищ данных и вычислительных служб с фабрикой данных.  Например, связанная служба может связать базу данных SQL Azure или кластер HDInsight с фабрикой данных.
3.	Создайте **таблицы**, описывающие входные и выходные данные для конвейеров. Таблицы также указывают фактическое расположение данных внутри хранилищ данных, связанных с фабрикой данных. Например, таблица может указывать имя таблицы SQL в базе данных SQL Azure (или) контейнер больших двоичных объектов в большом двоичном объекте Azure. 
4.	Создайте **конвейеры**. Конвейер состоит из одного или нескольких действий, которые принимают входные данные и создают выходные данные. Действие копирования копирует данные из исходного хранилища данных в целевое хранилище данных, а действие HDInsight обрабатывает входные данные с помощью сценариев Hive/Pig.  
5.	Укажите **период активности** конвейеров (для обработки данных). Период активности определяет период времени, в котором будут производиться срезы данных. Вы можете указать для конвейера дату-время начала и дату-время окончания (или) вы можете указать, чтобы он выполнялся все время.

Изучив данный учебник, вы научитесь: 

1.	Чтобы создать фабрику данных Azure и связанные службы для хранилищ данных, используйте **портал предварительной версии Azure**.
2.	Используйте **Azure PowerShell** для создания таблиц и конвейера. Портал не поддерживает создание таблиц и конвейеров в этом выпуске


##Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- Подписка Azure. Дополнительную информацию о получении подписки см. в разделах [Варианты приобретения] [azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
- Скачайте и установите на своем компьютере [Azure PowerShell][download-azure-powershell].
- Прочитайте раздел [Введение в фабрику данных Azure][data-factory-introduction].
- Учетная запись хранения Azure. В этом учебнике в качестве исходного хранилища данных будет использоваться хранилище больших двоичных объектов.
- База данных SQL Azure. В этом учебнике вы узнаете, как создать пример базы данных и использовать его в качестве целевого хранилища данных.

##Вот какие темы рассматривает этот учебник:

Шаг | Описание
-----| -----------
[Шаг 1: создание фабрики данных Azure](#CreateDataFactory) | В этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactory**. 
[Шаг 2: создание связанных служб](#CreateLinkedServices) | В этом шаге вы создадите две связанные службы: **MyBlobStore** и **MyAzureSQLStore**. MyBlobStore связывает службу хранилища Azure, а MyAzureSQLStore связывает базу данных SQL Azure с ADFTutorialDataFactory.
[Шаг 3: создание входных и выходных наборов данных](#CreateInputAndOutputDataSets) | В этом шаге вы определите два набора данных (**EmpTableFromBlob** и **EmpSQLTable**), используемые в качестве входных и выходных данных для **действия копирования** в конвейере ADFTutorialPipeline, который вы создадите в следующем шаге.
[Шаг 4: создание и запуск конвейера](#CreateAndRunAPipeline) | В этом шаге вы создадите конвейер с именем **ADFTutorialPipeline**. Конвейер будет содержать **действие копирования**, которое копирует данные из большого двоичного объекта Azure в выходную таблицу базы данных Azure.
[Шаг 5: мониторинг наборов данных и конвейера](#MonitorDataSetsAndPipeline) | В этом шаге вы будете отслеживать наборы данных и конвейер, используя Azure Management Studio.
 


## <a name="CreateDataFactory"></a>Шаг 1: создание фабрики данных Azure
В этом шаге воспользуйтесь порталом предварительной версии Azure для создания фабрики данных Azure с именем **ADFTutorialDataFactory**.

1.	После входа на [портал предварительной версии Azure][azure-preview-portal] в нижнем левом углу щелкните **СОЗДАТЬ**, затем в колонке **Создать** выберите **Фабрика данных**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Если в колонке **Создать** вы не видите **Фабрика данных**, прокрутите колонку вниз.  


6. В колонке **Новая фабрика данных**:
	1. В поле **имя** введите **ADFTutorialDataFactory**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ** и выполните следующие действия:
		1. Щелкните **Создать новую группу ресурсов**.
		2. В колонке **Create resource group** (Создать группу ресурсов) введите **ADFTutorialResourceGroup** в качестве **имени** группы ресурсов, затем щелкните **ОК**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, необходимо использовать именно ту группу ресурсов, которую вы выбрали здесь, а не ADFTutorialResourceGroup.  
7. Обратите внимание, что в колонке **New data factory** (Создать фабрику данных), выбрано **Add to Startboard** (Добавить на начальную панель).
8. В колонке **New data factory** (Создать фабрику данных) щелкните **Create** (Создать).
9. Слева щелкните раздел **УВЕДОМЛЕНИЯ** и просмотрите уведомления от процесса создания.
10. После завершения создания вы увидите колонку "Фабрика данных", как показано ниже
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. Вы также можете открыть ее из **начальной панели**, как показано ниже, щелкнув **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Шаг 2: создание связанных служб
Связанные службы связывают хранилища данных или вычислительные службы с фабрикой данных Azure. Хранилищем данных может быть служба хранилища Azure, база данных SQL Azure или локальная база данных SQL Server.

В этом шаге вы создадите две связанные службы - **MyBlobStore** и **MyAzureSQLStore**. Связанная служба MyBlobStore связывает учетную запись хранения Azure, а MyAzureSQLStore связывает базу данных SQL Azure с **ADFTutorialDataFactory**. Далее в этом учебнике вы узнаете, как создать контейнер, который копирует данные из контейнера больших двоичных объектов в MyBlobStore в таблицу SQL в MyAzureSQLStore.

### Создание связанной службы для учетной записи хранения Azure
1.	В колонке **ФАБРИКА ДАННЫХ** щелкните плитку **Связанные службы**, чтобы запустилась колонка **Связанные службы**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. В колонке **Связанные службы** щелкните **Добавить хранилище данных**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. В колонке **Новое хранилище данных** выполните следующиее:  
	1. Введите **имя** для хранилища данных. Для целей этого учебника введите **имя** **MyBlobStore**.
	2. Введите **описание** (необязательно) хранилища данных.
	3. Щелкните **Тип**.
	4. Выберите **Учетная запись хранения Azure** и нажмите кнопку **ОК**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Теперь вы вернулись к колонке **Новое хранилище данных**, которая выглядит, как показано ниже:

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Введите данные в поля **Имя учетной записи** и **Ключ учетной записи** для учетной записи хранения, а затем нажмите кнопку **ОК**.   

6. После того, как вы щелкнули **ОК** в колонке **Новое хранилище данных**, вы должны увидеть **myblobstore** в списке **ХРАНИЛИЩА ДАННЫХ** в колонке **Связанные службы**. Щелкните раздел **УВЕДОМЛЕНИЯ** (слева), чтобы проверить наличие сообщений.

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Создание связанной службы для базы данных SQL Azure
1. В колонке **Связанные службы** на панели инструментов щелкните **Добавить хранилище данных**, чтобы добавить другой источник данных (базу данных SQL Azure).
2. В колонке "Новое хранилище данных":
	1. Введите **имя** для хранилища данных. Для примера в этом учебнике введите **MyAzureSQLStore** в качестве **имени**. 
	2. Введите **ОПИСАНИЕ (необязательно)** для хранилища.
	3. Щелкните **Тип** и выберите **База данных SQL Azure**.
3. Введите значения **Сервер**, "База данных", "Имя пользователя" и **Пароль** для базы данных SQL Azure, затем щелкните **ОК**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. После того, как вы щелкнули **ОК** в колонке **Новое хранилище данных**, вы должны увидеть оба хранилища в колонке **Связанные службы**

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3: создание входных и выходных таблиц

В предыдущем шаге вы создали связанные службы **MyBlobStore** и **MyAzureSQLStore**, чтобы связать учетную запись хранения Azure и базу данных SQL Azure с фабрикой данных: **ADFTutorialDataFactory**. В этом шаге вы создадите таблицы, представляющие входные и выходные данные для действия копирования в конвейере, который вы создадите в следующем шаге. 

Таблица представляет собой прямоугольный набор данных, и это единственный тип набора данных, поддерживаемый в настоящее время. Входная таблица ссылается на контейнер больших двоичных объектов в службу хранилища Azure, на которое указывает MyBlobStore, а выходная таблица ссылается на таблицу SQL в базе данных SQL Azure, на которую указывает MyAzureSQLStore.  
 
В настоящее время создание наборов данных и конвейеров не поддерживается порталом предварительной версии Azure, поэтому для создания таблиц и конвейеров используйте командлеты Azure PowerShell. Прежде чем создавать таблицы, необходимо выполнить следующие действия (подробное описание шагов приводится далее).

* Создайте контейнер больших двоичных объектов с именем **adftutorial** в хранилище больших двоичных объектов Azure, на которое указывает MyBlobStore. 
* Создайте и передайте текстовый файл **emp.txt**, как большой двоичный объект, в папку **входных данных** в контейнере **adftutorial** (**input/emp.txt**). 
* Создайте таблицу с именем **emp** в базе данных SQL Azure, на которую указывает MyAzureSQLStore.
* Создайте папку с именем **ADFGetStarted** на жестком диске.  

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
1. Запустите Блокнот, вставьте следующий текст и сохраните его в виде файла **emp.txt** в папке **C:\ADFGetStarted** на жестком диске. 

        John, Doe
		Jane, Doe
				
2. Используйте инструменты, такие как [обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), чтобы создать контейнер **adftutorial** и передать файл **emp.txt** в папку **входных данных** внутри контейнера (**input/emp.txt**).

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Используйте следующий сценарий SQL для создания таблицы **emp** в базе данных SQL Azure. Вы можете использовать SQL Server Management Studio для подключения к базе данных SQL Azure и запуска сценария SQL.

        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				

### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. В этом шаге вы создадите таблицу с именем **EmpBlobTable**, которая указывает на контейнер больших двоичных объектов в службе хранилища Azure, представленной связанной службой **MyBlobStore**.


1. Создайте JSON-файл для таблицы фабрики данных, которая представляет данные в emp.txt в большом двоичном объекте. Запустите Блокнот, скопируйте следующий сценарий JSON и сохраните его как EmpBlobTable.json в папке C:\ADFGetStarted.


        {
     	    "name": "EmpTableFromBlob",
		    "properties":
    		{
        		"structure":  
       			 [ 
            		{ "name": "FirstName", "type": "String"},
            		{ "name": "LastName", "type": "String"}
        		],
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/input",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1,
            		"waitonexternal": {}
       		 	}
    		}
		}

		
     Обратите внимание на следующее: 
	
	- для **type** расположения задается значение **AzureBlobLocation**.
	- **linkedServiceName** задается значение **MyBlobStore**. Вы создали эту связанную службу в шаге 2.
	- **folderPath** задается значение папки **input** в контейнере **adftutorial**. Вы также можете указать имя большого двоичного объекта внутри папки. Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- для **type** формата задается значение **TextFormat**
	- - В текстовом файле есть два поля - **FirstName** и **ColumnName** - разделенные запятой (columDelimiter)	
	- Для **availability** задается значение **hourly** (для **frequency** задается значение **hour**, а для **interval** задается значение **1**). Каждый час служба фабрики данных будет выполнять поиск входных данных в папке **input** указанного вами контейнера больших двоичных объектов (**adftutorial**).

	Если не указать **fileName** для **входной таблицы**, все файлы или большие двоичные объекты из входной папки (**folderPath**) рассматриваются как входные данные. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Примеры файлов см. в [учебнике][adf-tutorial].
 
	Если вы не зададите параметр **fileName** для **выходной таблицы**, формат файлов, созданных в папке **folderPath**, будет следующим: Data.<Guid>.txt (например, : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Для динамической установки папки **folderPath** и имени **fileName** на основе времени **SliceStart** используйте свойство partitionedBy. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а fileName использует время (часы) из SliceStart. Например, если срез выполняется для 2014-10-20T08:00:00, для folderName будет установлено значение wikidatagateway/wikisampledataout/2014/10/20, а для fileName - 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Подробную информацию о свойствах JSON см. в [справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Запустите модуль **Azure PowerShell** и выполните следующую команду для перехода в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Воспользуйтесь командлетом **New-AzureDataFactoryTable** для создания входной таблицы с помощью файла **EmpBlobTable.json**.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Подробную информацию об этом и других командлетах фабрики данных см. в [справочнике по командлетам фабрики данных][cmdlet-reference].
 
### Создание выходной таблицы
В этой части шага вы создадите выходную таблицу с именем **EmpSQLTable**, которая указывает на таблицу SQL в базе данных SQL Azure, представленной связанной службой **MyAzureSQLStore**. 

1. Создайте JSON-файл для таблицы фабрики данных, которая представляет данные в базе данных SQL Azure. Запустите Блокнот, скопируйте следующий сценарий JSON и сохраните его как **EmpSQLTable.json** в папке **C:\ADFGetStarted**.



        {
    		"name": "EmpSQLTable",
    		"properties":
    		{
        		"structure":
        		[
                	{ "name": "FirstName", "type": "String"},
                	{ "name": "LastName", "type": "String"}
        		],
        		"location":
        		{
            		"type": "AzureSQLTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     Обратите внимание на следующее: 
	
	* для **type** расположения задается значение **AzureSQLTableLocation**.
	* **linkedServiceName** задается значение **MyAzureSQLStore** (вы создали эту связанную службу в шаге 2).
	* **tablename** задается значение **emp**.
	* Таблица emp в базе данных имеет три столбца - **ID**, **FirstName** и **LastName**, но ID - это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
	* Для **availability** задается значение **hourly** (для частоты задается значение часа, а для интервала - 1).  Служба фабрики данных будет создавать срез выходных данных каждый час в таблице **emp** в базе данных SQL Azure.


2. В **Azure PowerShell** выполните следующую команду, чтобы создать другую таблицу фабрики данных для представления таблицы **emp** в базе данных SQL Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Шаг 4: создание и запуск конвейера
В этом шаге создайте конвейер с **действием копирования**, которое использует **EmpTableFromBlob** входные данные, а **EmpSQLTable** - как выходные даннные.

1. Создайте JSON-файл для конвейера. Запустите Блокнот, скопируйте следующий сценарий JSON и сохраните его как **ADFTutorialPipeline.json** в папке **C:\ADFGetStarted**.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpTableFromBlob"} ],
						"outputs": [ {"name": "EmpSQLTable"} ],		
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		
					}
        		]
      		}
		} 

	Обратите внимание на следующее:

	- В разделе действий есть только одно действие, для **type** которого задано значение **CopyActivity**.
	- Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных - значение **EmpSQLTable**.
	- В разделе **transformation** в качестве типа источника указан **BlobSource**, а в качестве типа приемника - **SqlSink**.

2. Воспользуйтесь командлетом **New-AzureDataFactoryPipeline**, чтобы создать конвейер с помощью уже созданного файла **ADFTutorialPipeline.json**.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. После создания конвейеров вы можете указать время, в которое будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.  Выполните следующую команду PowerShell, чтобы установить период активности конвейера, и введите "Y", чтобы подтвердить. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Замените значение **StartDateTime** текущим днем, а **EndDateTime** - следующим днем. Значения StartDateTime и EndDateTime представлены в формате времени UTC, и должны быть указаны в формате [ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. Значение **EndDateTime** является необязательным, но мы используем его в этом учебнике. 
	> Если вы не укажете значение **EndDateTime**, оно будет рассчитываться по формуле **StartDateTime + 48 часов**. Чтобы конвейер выполнялся в течение неопределенного срока, укажите значение **9/9/9999** для **EndDateTime**.  
	
	В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.

4. На **портале Azure** в колонке **ФАБРИКА ДАННЫХ** для **ADFTutorialDataFactory** выберите **Схема**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Вы должны увидеть схему, аналогичную приведенной ниже: (Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка).

	![Diagram view][image-data-factory-get-started-diagram-blade]

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5: мониторинг наборов данных и конвейера
В этом шаге вы будете использовать портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Дополнительную информацию об использовании командлетов для мониторинга см. в разделе [Мониторинг и управление фабрикой данных с помощью командлетов PowerShell][monitor-manage-using-powershell].

1. Перейдите на [портал Azure (предварительная версия)][azure-preview-portal], если он у вас не открыт. 
2. Если колонка для **ADFTutorialDataFactory** не открыта, откройте ее, щелкнув **ADFTutorialDataFactory** на **начальной панели**. 
3. Вы увидите количество и имена таблиц, а также конвейер, который вы создали в этой колонке.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Теперь щелкните плитку **Наборы данных**.
5. В колонке **Наборы данных** щелкните **EmpTableFromBlob**. Это входная таблица для **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Обратите внимание, что срезы данных до настоящего момента уже произведены и находятся в состоянии **Ready**, потому что файл **emp.txt** существует в контейнере больших двоичных объектов все время: **adftutorial\input**. Убедитесь, что в разделе **Проблемные срезы** в нижней части окна не показаны срезы.
6. Затем в колонке **Наборы данных** щелкните **EmpSQLTable**. Это выходная таблица для **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Вы должны увидеть колонку **EmpSQLTable**, как показано ниже:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Обратите внимание, что до текущего времени выполнены срезы данных и состояние каждого из них - **Ready**. В разделе **Проблемные срезы** в нижней части окна не отображается ни один срез.
8. Щелкните **... (многоточие)**, чтобы просмотреть все срезы.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Щелкните любой срез данных в списке и увидите колонку **СРЕЗ ДАННЫХ**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. В колонке **СРЕЗ ДАННЫХ** вы должны увидеть все выполняемые действия в виде списка в нижней части. Щелкните **выполняемое действие**, чтобы просмотреть колонку **ACTIVITY RUN DETAILS** (Подробная информация о выполняемом действии). 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Щелкните **X**, чтобы закрыть все колонки, пока не вернетесь к начальной колонке **ADFTutorialDataFactory**.
14. (необязательно) На домашней странице щелкните **Конвейеры**, чтобы открыть **ADFTutorialDataFactory**, щелкните **ADFTutorialPipeline** в колонке **Конвейеры** и детализируйте входные таблицы (**Consumed** (Потреблено)) или выходные таблицы (**Produced** (Произведено)).
15. Запустите **SQL Server Management Studio**, подключитесь к базе данных SQL Azure и убедитесь, что строки вставляются в таблицу **emp** в базе данных.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Сводка 
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали портал предварительной версии Azure для создания фабрики данных и связанных служб. Вы использовали командлеты Azure PowerShell для создания таблиц и конвейера, а затем выполнили планирование конвейера. Вот действия верхнего уровня, которые вы выполнили в этом учебнике:  

1.	Создание **фабрики данных **Azure.
2.	Создание **связанных служб** для связи хранилищ данных и вычислительных служб (называемых **связанными службами**) с фабрикой данных.
3.	Создание **таблиц**, описывающих входные и выходные данные для конвейеров.
4.	Создание **конвейеров**. Конвейер состоит из одного или нескольких действий, обрабатывает входные данные и создает выходные данные. 
5.	Настройка **периода активности** конвейеров (для обработки данных). Период активности определяет период времени, в котором будут производиться срезы данных.

## Дальнейшие действия

Статья | Описание
------ | ---------------
[Копирование данных с помощью фабрики данных Azure - действие копирования][copy-activity] | Эта статья содержит подробное описание **действия копирования**, которое вы использовали в этом учебнике. 
[Работа конвейеров с локальными данными][use-onpremises-datasources] | В этой статье представлены пошаговые указания по копированию данных из **локальной базы данных SQL Server** в большой двоичный объект Azure.
[Использование сценариев pig и hive с фабрикой данных][use-pig-and-hive-with-data-factory] | Эта статья содержит пошаговое руководство, которое показывает, как использовать **действие HDInsight**, чтобы выполнить сценарий **hive и pig** для обработки входных данных и создания выходных данных. 
[Учебник. Перемещение и обработка файлов журналов с помощью фабрики данных][adf-tutorial] | В этой статье **рассматриваются все стороны** реализации **близкого к реалистичному** сценария с использованием фабрики данных Azure для преобразования данных из файлов журналов в аналитику.
[Использование пользовательских действий в фабрике данных][use-custom-activities] | Эта статья содержит пошаговое руководство с указаниями по созданию **настраиваемых действий** и их использованию в конвейере. 
[Мониторинг фабрики данных Azure и управление ею с помощью среды PowerShell][monitor-manage-using-powershell] | В этой статье описывается, как выполнять **мониторинг и управление** фабрикой данных Azure с помощью **командлетов Azure PowerShell**. Вы можете использовать примеры в статье на ADFTutorialDataFactory.
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается **устранение неполадок** в работе фабрики данных Azure. Вы можете использовать руководство в этой статье на ADFTutorialDataFactory путем введения ошибки (удалив таблицу в базе данных Azure SQL). 
[Справочник по командлетам фабрик данных Azure][cmdlet-reference] | Этот справочник содержит информацию о всех **командлетах фабрики данных**.
[Справочник разработчика фабрик данных Azure][developer-reference] | Справочник разработчика содержит разнообразные материалы по использованию командлетов, сценариев JSON, функций и т. д. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png
