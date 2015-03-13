<properties 
	pageTitle="Приступая к работе с фабрикой данных Azure" 
	description="В этом учебнике показано, как создать пример конвейера данных, который копирует данные из большого двоичного объекта в экземпляр базы данных SQL Azure." 
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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Приступая к работе с фабрикой данных Azure
Эта статья поможет вам начать работу с фабрикой данных Azure. Представленный в этой статье учебник демонстрирует создание фабрики данных Azure, создание конвейера в фабрике данных и копирование образца данных из хранилища BLOB-объектов Azure в базу данных SQL Azure.

В следующем списке приведены типичные шаги, которые необходимо выполнить разработчикам: 

1.	Создание **фабрики данных Azure**.
2.	Создание **связанных служб** для связи хранилищ данных и вычислительных служб с фабрикой данных.  Например, такая служба может связать с фабрикой данных базу данных SQL Azure или кластер HDInsight.
3.	Создание **таблиц**, описывающих входные и выходные данные для конвейеров. Таблицы также указывают фактическое расположение данных внутри хранилищ данных, связанных с фабрикой данных. Например, такая таблица может указывать имя таблицы SQL в базе данных SQL Azure, или контейнер больших двоичных объектов в Azure Blob. 
4.	Создание **конвейеров**. Конвейер состоит из одного или нескольких действий, которые принимают входные данные и создают выходные данные. Действие копирования выполняет копирование данных из исходного хранилища в целевое хранилище данных, а действие HDInsight обрабатывает входные данные при помощи сценариев Hive/Pig.  
5.	Настройка **периода активности** конвейеров (для обработки данных). Период активности определяет промежуток времени, в течение которого будут производиться срезы данных. Можно указать для конвейера дату и время начала и дату и время окончания или непрерывное выполнение.

Изучив данный учебник, вы научитесь: 

1.	Создавать фабрику данных Azure и связанные службы для хранилищ данных с помощью **портала предварительной версии Azure**.
2.	Создавать таблицы и конвейер при помощи **Azure PowerShell**. Данная версия портала не поддерживает создание таблиц и конвейеров.


##Предварительные требования
Перед началом работы с этим учебником необходимо иметь следующее:

- Подписка Azure. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения] [azure-purchase-options], [Предложения для участников][azure-member-offers] и [Бесплатная пробная версия][azure-free-trial].
- Загрузите и установите [Azure PowerShell][download-azure-powershell] на компьютер.
- Ознакомьтесь с разделом [введение в фабрику данных][data-factory-introduction].
- Учетная запись хранения Azure. В этом учебнике для хранения исходных данных будет использоваться хранилище BLOB-объектов. Этапы создания хранилища Azure см. в статье [Что такое учетная запись хранения?][data-factory-create-storage]
- База данных SQL Azure. В этом учебнике вы узнаете, как создать базу данных и использовать ее в качестве целевого хранилища данных. Этапы создания баз данных SQL Azure см. в разделе [Создание и настройка базы данных SQL Azure][data-factory-create-sql-database].

##Вот какие темы рассматривает этот учебник:

Шаг | Описание
-----| -----------
[Шаг 1. создание фабрики данных Azure](#CreateDataFactory) | На этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactory**. 
[Шаг 2. создание связанных служб](#CreateLinkedServices) | На этом шаге вы создадите две связанные службы: **MyBlobStore** и **MyAzureSQLStore**. Служба MyBlobStore связывает хранилище Azure, а MyAzureSQLStore связывает базу данных SQL Azure с фабрикой данных ADFTutorialDataFactory.
[Шаг 3. Создание наборов входных и выходных данных](#CreateInputAndOutputDataSets) | На этом шаге вы определите два набора данных (**EmpTableFromBlob** и **EmpSQLTable**), которые используются для ввода и вывода данных при **копировании** в конвейере ADFTutorialPipeline, созданном на последующем этапе.
[Шаг 4. Создание и запуск конвейера](#CreateAndRunAPipeline) | На этом шаге вы создадите конвейер с именем **ADFTutorialPipeline**. Данный конвейер будет иметь **действие копирования**, которое копирует данные из большого двоичного объекта Azure в выходную таблицу базы данных Azure.
[Шаг 5. Контроль наборов данных и конвейера](#MonitorDataSetsAndPipeline) | На этом шаге вы проверите наборы данных и конвейер при помощи Azure Management Studio.
 


## <a name="CreateDataFactory"></a>Шаг 1. Создание фабрики данных Azure
На этом шаге вы создадите фабрику данных с именем **ADFTutorialDataFactory** через портал предварительной версии Azure.

1.	Войдите на [портал предварительной версии Azure][azure-preview-portal], нажмите кнопку **СОЗДАТЬ** в левом нижнем углу и щелкните **Фабрика данных** в колонке **Создать**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Если вы не видите **фабрики данных** на колонке **Создать**, прокрутите вниз.  


6. На колонке **Новая фабрика данных**
	1. Введите **ADFTutorialDataFactory** в поле **имя**. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ** и выполните следующие действия.
		1. Щелкните **Создать новую группу ресурсов**.
		2. В колонке **Создать новую группу ресурсов** введите **ADFTutorialResourceGroup** в поле **имени** группы ресурсов и щелкните **OK**. 

			![Create Resource Group][image-data-factory-create-resource-group]

		Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup используйте выбранную вами группу.  
7. Обратите внимание, что в колонке **Создать фабрику данных** выбрано **Добавить в начальную панель**.
8. Нажмите **Создать** на колонке **Создать фабрику данных**.

	> [WACOM.NOTE] Имя фабрики данных Azure должно быть глобально уникальным. При появлении ошибки **Имя фабрики данных ADFTutorialDataFactory недоступно**, измените имя (например, на yournameADFTutorialDataFactory). Используйте это имя вместо ADFTutorialFactory при выполнении действий из этого учебника.  
	 
	![Data Factory name not available][image-data-factory-name-not-available]

9. Нажмите слева **УВЕДОМЛЕНИЯ** и просмотрите уведомления процесса создания.
10. После завершения создания вы увидите колонку "Фабрика данных", как показано ниже
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. Можно также открыть ее из **начальной панели**, как показано ниже, щелкнув **ADFTutorialFactory** 

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>Шаг 2. Создание связанных служб
Связанные службы связывают хранилища данных или вычислительные службы с фабрикой данных Azure. Данные могут храниться в хранилище Azure, в базе данных SQL Azure или в локальной базе данных SQL Server.

В этом шаге вы создадите две связанные службы: **MyBlobStore** и **MyAzureSQLStore**. Связанная служба MyBlobStore устанавливает связь учетной записи хранения Azure, а MyAzureSQLStore устанавливает связь базы данных SQL Azure с **ADFTutorialDataFactory**. Далее в этом учебнике вы научитесь создавать конвейер для копирования данных из хранилища BLOB-объектов MyBlobStore в таблицу SQL в MyAzureSQLStore.

### Создание связанной службы для учетной записи хранения Azure
1.	В колонке **ФАБРИКА ДАННЫХ** 	щелкните плитку **Связанные службы**, чтобы открыть колонку **Связанные службы**.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. В колонке **Связанные службы** щелкните **Добавить хранилище данных**.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. В колонке **Новое хранилище данных**:  
	1. Введите **имя** хранилища данных. Для целей этого учебника введите **MyBlobStore** в поле **имя**.
	2. Введите **описание** (не обязательно) хранилища данных.
	3. Щелкните **Тип**.
	4. Выберите **Учетная запись хранения Azure** и щелкните **OK**.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  Теперь вы вернулись в колонку **Новое хранилище данных**, которая выглядит следующим образом.

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5. Введите **Имя учетной записи** и **Ключ учетной записи** вашей учетной записи хранения Azure и щелкните**OK**.   

6. После нажатия кнопки **OK** в колонке **Новое хранилище данных** вы увидите **myblobstore** в списке **ХРАНИЛИЩА ДАННЫХ** в колонке **Связанные службы**. Проверьте, есть ли какие-либо сообщения в разделе **УВЕДОМЛЕНИЯ** (слева).

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Создание связанной службы для базы данных SQL Azure
1. В колонке **Связанные службы** на панели инструментов щелкните **Добавить хранилище данных**, чтобы добавить еще один источник данных (базу данных SQL Azure).
2. В колонке "Новое хранилище данных":
	1. Введите **имя** хранилища данных. Для целей данного учебника введите **MyAzureSQLStore** в поле **ИМЯ**. 
	2. Введите **ОПИСАНИЕ (не обязательно)** для этого хранилища.
	3. Щелкните **Тип** и выберите **База данных SQL Azure**.
3. Заполните поля **Сервер**, **База данных**, **Имя пользователя** и **Пароль** для этой базы данных SQL Azure и щелкните **OK**.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. После нажатия кнопки **OK** в колонке **Новое хранилище данных** вы увидите оба хранилища в колонке **Связанные службы**.

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3. Создание входных и выходных таблиц

В предыдущем шаге вы создали связанные службы **MyBlobStore** и **MyAzureSQLStore**, чтобы связать учетную запись хранения Azure и базу данных SQL Azure с фабрикой данных: **ADFTutorialDataFactory**. В этом шаге вы создадите таблицы, представляющие входные и выходные данные для действия копирования в конвейере, который будет создан в последующем шаге. 

Таблица представляет собой прямоугольный набор данных, и это единственный тип набора данных, поддерживаемый в настоящее время. Входная таблица ссылается на контейнер BLOB-объектов в хранилище Azure, на которое указывает MyBlobStore, а выходная таблица ссылается на таблицу SQL в базе данных SQL Azure, на которую указывает MyAzureSQLStore.  
 
В настоящее время создание наборов данных и конвейеров не поддерживается порталом предварительной версии Azure, поэтому для создания таблиц и конвейеров используйте командлеты Azure PowerShell. Прежде чем создавать таблицы, необходимо выполнить следующие действия (подробное описание шагов приводится ниже).

* Создайте контейнер BLOB-объекта с именем **adftutorial** в Azure хранилище BLOB-объектов, на которое указывает MyBlobStore. 
* Создайте и загрузите текстовый файл **emp.txt** в качестве BLOB-объекта в контейнер **adftutorial**. 
* Создайте таблицу с именем **emp** в базе данных SQL Azure, на которую указывает MyAzureSQLStore.
* Создайте на жестком диске папку с именем **ADFGetStarted**.  

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
1. Запустите Блокнот, вставьте следующий текст и сохраните файл на жестком диске под именем **emp.txt** в папке **C:\ADFGetStarted**. 

        John, Doe
		Jane, Doe
				
2. При помощи таких средств, как [Обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и загрузите файл **emp.txt** в этот контейнер.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. Используйте следующий сценарий SQL, чтобы создать таблицу **emp** в базе данных SQL Azure. При помощи Azure SQL Management Console можно подключиться к базе данных SQL Azure и запустить сценарий SQL. То же самое можно сделать при помощи SQL Server Management Studio. 


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				
	Чтобы запустить Azure SQL Management Console, щелкните **УПРАВЛЕНИЕ**, как показано на рисунке ниже.
 
	![Launch Azure SQL Management Console][image-data-factory-sql-management-console]

	![Azure SQL Management Console][image-data-factory-sql-management-console-2]
### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable**, которая указывает на контейнер BLOB-объектов в хранилище Azure, представленном связанной службой **MyBlobStore**.


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
            		"folderPath": "adftutorial/",
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
	
	- в качестве расположения свойства **type** задано значение **AzureBlobLocation**.
	- **linkedServiceName** имеет значение **MyBlobStore**. Вы создали эту связанную службу в шаге 2).
	- **folderPath** установлен в значение контейнера **adftutorial**. Вы также можете указать имя большого двоичного объекта внутри папки. Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- **тип** формата имеет значение **TextFormat**
	- в текстовом файле есть два поля - **FirstName** и **ColumnName**, - разделенные запятой (columDelimiter)	
	- **availability** имеет значение **hourly** (**frequency** имеет значение **hour**, а **interval** имеет значение **1**). Служба фабрики данных будет искать входные данные каждый час в корневом каталоге указанного вами контейнера BLOB-объектов (**adftutorial**).

	Если указать **fileName** для **входной таблицы**, то все файлы и большие двоичные объекты из входной папки (**folderPath**) рассматриваются как входные данные. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Примеры файлов см. в [учебнике][adf-tutorial].
 
	Если **fileName** для **выходной таблицы** не указано, то созданные файлы в **folderPath** именуются в следующем формате: Data.<Guid>.txt (например, : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Чтобы устанавливать **folderPath** и **fileName** динамически, основываясь на времени **SliceStart**, используйте свойство partitionedBy. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а fileName использует время (часы) из SliceStart. Например, если срез выполняется для временной отметки 2014-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2014/10/20, а fileName - 08.csv. 

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

2. Запустите **Azure PowerShell** и выполните следующую команду, чтобы перейти в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		
	If you haven't already done so, do the following:


	- Запустите **Add-AzureAccount** и введите имя пользователя и пароль, используемые для входа на портал предварительной версии Azure.  
	- Запустите **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Запустите **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure.

3. Воспользуйтесь командлетом **New-AzureDataFactoryTable**, чтобы создать входную таблицу из файла **EmpBlobTable.json**.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	Подробные сведения об этом и других командлетах фабрики данных см. в [справочнике по командлетам фабрики данных][cmdlet-reference].
 
### Создание выходной таблицы
В этой части шага вы создадите выходную таблицу с именем **EmpSQLTable**, которая указывает на таблицу SQL в базе данных SQL Azure, представленной связанной службой **MyAzureSQLStore**. 

1. Создайте файл JSON для таблицы фабрики данных, которая представляет данные в базе SQL Azure. Запустите Блокнот, скопируйте следующий сценарий JSON и сохраните его под именем **EmpSQLTable.json** в папке **C:\ADFGetStarted**.



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
	
	* **type** расположения имеет значение **AzureSQLTableLocation**.
	* **linkedServiceName** имеет значение **MyAzureSQLStore** (вы создали эту связанную службу в шаге 2).
	* **tablename** имеет значение **emp**.
	* Таблица emp в базе данных имеет три столбца - **ID**, **FirstName** и **LastName**, но ID - это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
	* **availability** имеет значение **hourly** (frequency установлена в значение hour, а interval имеет значение 1).  Служба фабрики данных будет создавать срез выходных данных каждый час в таблице **emp** в базе данных SQL Azure.


2. В **Azure PowerShell** выполните следующую команду, чтобы создать еще одну таблицу фабрики данных, которая будет представлять таблицу **emp** базы данных SQL Azure.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>Шаг 4. Создание и запуск конвейера
В этом шаге вы создадите конвейер с **действием копирования**, которое использует **EmpTableFromBlob** в качестве входных данных и **EmpSQLTable** в качестве выходных.

1. Создайте файл JSON для конвейера. Запустите Блокнот, скопируйте следующий сценарий JSON и сохраните файл под именем **ADFTutorialPipeline.json** в папке **C:\ADFGetStarted**.


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

	- В разделе "activities" доступно только одно действие, **тип** которого имеет значение **CopyActivity**.
	- Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных - **EmpSQLTable**.
	- В разделе **transformation** переменная **BlobSource** указывается в качестве типа источника, а **SqlSink** указывается в качестве типа приемника.

2. Воспользуйтесь командлетом **New-AzureDataFactoryPipeline**, чтобы создать конвейер с использованием ранее созданного файла **ADFTutorialPipeline.json**.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. После создания конвейеров вы можете указать время, в течение которого будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.  Выполните следующую команду PowerShell, чтобы установить период активности конвейера, и введите "Y", чтобы подтвердить команду. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] Замените величину **StartDateTime** на текущий день, а величину **EndDateTime** - на следующий день StartDateTime и EndDateTime являются значениями времени в формате UTC, и потому должны быть заданы в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. Величина **EndDateTime** не является обязательной, но мы используем ее в данном учебнике. 
	> Если вы не указали **EndDateTime**, эта величина вычисляется по формуле **StartDateTime + 48 часов**. Чтобы конвейер выполнялся в течение неопределенного срока, укажите значение **9/9/9999** для параметра **EndDateTime**.  
	
	В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.

4. На **портале Azure** в колонке **ФАБРИКА ДАННЫХ** в разделе **ADFTutorialDataFactory** щелкните **Схема**.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. Вы должны увидеть схему, аналогичную приведенной ниже: (Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка).

	![Diagram view][image-data-factory-get-started-diagram-blade]

****Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5. Мониторинг наборов данных и конвейера
В этом шаге вы будете использовать портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Подробные сведения об использовании командлетов для мониторинга см. в разделе [Мониторинг и управление фабрикой данных с помощью командлетов PowerShell][monitor-manage-using-powershell].

1. Перейдите на портал [Azure (предварительной версии)][azure-preview-portal], если он у вас еще не открыт. 
2. Если колонка **ADFTutorialDataFactory** не открыта, откройте ее, щелкнув **ADFTutorialDataFactory** на **начальной панели**. 
3. Вы увидите количество и имена таблиц, а также конвейер, который вы создали в этой колонке.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. Теперь щелкните плитку **Наборы данных**.
5. В колонке **Наборы данных** щелкните **EmpTableFromBlob**. Это входная таблица для **ADFTutorialPipeline**.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. Обратите внимание, что срезы данных до настоящего момента уже произведены и находятся в состоянии **Готов**, поскольку файл **emp.txt** все это время существует в контейнере BLOB-объектов: **adftutorial\input**. Убедитесь, что в разделе **Проблемные срезы** в нижней части окна не показаны срезы.
6. Затем в колонке **Наборы данных** щелкните **EmpSQLTable**. Это выходная таблица для **ADFTutorialPipeline**.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. Колонка **EmpSQLTable** должна иметь следующий вид:

	![table blade][image-data-factory-get-started-table-blade]
 
7. Обратите внимание, что срезы данных до текущего момента времени уже выполнены и все они находятся в состоянии **Готов**. В разделе **Проблемные срезы** в нижней части окна срезов нет.
8. Нажмите кнопку **... (многоточие)**, чтобы увидеть все срезы.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. Щелкните любой срез данных в списке, и вы увидите колонку **СРЕЗ ДАННЫХ**.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. В колонке **СРЕЗ ДАННЫХ** в списке в нижней части окна отображаются все выполненные действия. Щелкните **выполняемое действие**, чтобы просмотреть колонку **ПОДРОБНАЯ ИНФОРМАЦИЯ О ВЫПОЛНЯЕМОМ ДЕЙСТВИИ**. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. Нажимая на кнопку **X**, закройте все колонки, чтобы вернуться к начальной колонке **ADFTutorialDataFactory**.
14. (не обязательно) Щелкните **Конвейеры** на домашней странице фабрики данных **ADFTutorialDataFactory**, щелкните **ADFTutorialPipeline** в колонке **Конвейеры** и изучите входные таблицы (**Использованные**) или выходные таблицы(**Произведенные**).
15. Запустите **SQL Server Management Studio**, подключитесь к базе данных SQL Azure и убедитесь, что строки вставляются в таблицу **emp** в базе данных.

	![sql query results][image-data-factory-get-started-sql-query-results]


## Сводка 
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали портал предварительной версии Azure для создания фабрики данных и связанных служб. Вы использовали командлеты Azure PowerShell для создания таблиц и конвейера, а затем выполнили планирование конвейера. Основные действия, которые вы выполнили в этом учебнике  

1.	Создание **фабрики данных ** Azure.
2.	Создание **связанных служб** для связи хранилищ данных и вычислительных служб (**связанных служб**) с фабрикой данных.
3.	Создание **таблиц**, описывающих входные и выходные данные для конвейеров.
4.	Создание **конвейеров**. Конвейер состоит из одного или нескольких действий, обрабатывает входные данные и создает выходные данные. 
5.	Настройка **периода активности** конвейеров (для обработки данных). Период активности определяет период времени, в течение которого будут производиться срезы данных.

## Дальнейшие действия

Статья | Описание
------ | ---------------
[Учебник. Перемещение и обработка файлов журналов с помощью фабрики данных][adf-tutorial] | В этой статье **рассматриваются все стороны** реализации **близкого к реалистичному сценария** с использованием фабрики данных Azure для преобразования данных из файлов журналов в аналитику.
[Копирование данных с помощью фабрики данных Azure - действие копирования][copy-activity] | Эта статья содержит подробное описание **действия копирования**, которое вы использовали в этом учебнике. 
[Обеспечение для конвейеров возможности работы с локальными данными][use-onpremises-datasources] | В этой статье представлены пошаговые указания по копированию данных из **локальной базы данных SQL Server** в BLOB-объект Azure.
[Использование сценариев Pig и Hive с фабрикой данных][use-pig-and-hive-with-data-factory] | В этой статье показано, как использовать **действие HDInsight** для выполнения сценария **hive/pig** и обработки входных данных для получения выходных данных. 
[Применение пользовательских действий в конвейере фабрики данных][use-custom-activities] | Эта статья содержит пошаговое руководство с указаниями по созданию **настраиваемых действий** и их использованию в конвейере. 
[Мониторинг и администрирование фабрики данных с помощью PowerShell][monitor-manage-using-powershell] | В этой статье описывается, как выполнять **мониторинг и администрирование** фабрики данных Azure при помощи командлетов **Azure PowerShell**. Вы можете использовать примеры из этой статьи на учебной фабрике данных ADFTutorialDataFactory.
[Устранение неполадок фабрики данных][troubleshoot] | Данная статья описывает способы **устранения неполадок** фабрик данных Azure. Вы можете использовать руководство из этой статьи на учебной фабрике данных ADFTutorialDataFactory, намеренно создав ошибки (удалив таблицу в базе данных SQL Azure). 
[Справочник по командлетам фабрики данных Azure][cmdlet-reference] | Этот справочник содержит подробную информацию обо всех **командлетах фабрики данных**.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полную справку по командлетам, сценарию JSON, функциям и т. д. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction
[data-factory-create-storage]: ../storage-whatis-account
[data-factory-create-sql-database]: ../sql-database-create-configure/


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

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
