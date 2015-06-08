<properties 
	pageTitle="Учебник: Создание и отслеживание фабрику данных Azure с помощью Azure PowerShell" 
	description="Узнайте, как использовать Azure PowerShell для создания и мониторинга фабрики данных Azure." 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Учебник: Создание и отслеживание фабрику данных с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

 [Приступая к работе с фабрикой данных Azure][adf-get-started] учебнике показано, как создавать и отслеживать фабрики данных Azure с помощью [предварительной версии портала Azure][azure-preview-portal]. В этом учебнике будет создавать и отслеживать фабрику данных Azure с помощью командлетов Azure PowerShell. Конвейер в фабрике данных создан в этом учебнике копирует данные из Azure blob в базу данных Azure SQL.

> [AZURE.NOTE]В данной статье не рассматриваются все командлеты фабрика данных. В разделе [Справочник по командлетам фабрики данных][cmdlet-reference] для Полная документация по командлетам фабрика данных.

##Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе Обзор учебника необходимо иметь Azure PowerShell, установленной на компьютере. Если у вас его уже, загрузите и установите [Azure PowerShell][download-azure-powershell] на компьютере.

##Вот какие темы рассматривает этот учебник:
В следующей таблице перечислены действия, которые необходимо выполнить в рамках этого учебника и их описания.

Шаг | Описание
-----| -----------
[Шаг 1: Создание фабрики данных Azure](#CreateDataFactory) | На этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactoryPSH**. 
[Шаг 2: Создание связанные службы](#CreateLinkedServices) | На этом шаге вы создадите две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связывает хранилища Azure и AzureSqlLinkedService ADFTutorialDataFactoryPSH связывает базу данных Azure SQL.
[Шаг 3: Создания входных и выходных наборов данных](#CreateInputAndOutputDataSets) | На этом шаге будут определены два набора данных (** EmpTableFromBlob ** и **EmpSQLTable**), которые используются как входные и выходные таблицы для **Действие копирования** в ADFTutorialPipeline, который будет создан на следующем шаге.
[Шаг 4: Создание и запуск конвейера](#CreateAndRunAPipeline) | На этом шаге вы создадите конвейера с именем **ADFTutorialPipeline** в фабрике данных: **ADFTutorialDataFactoryPSH**... Конвейер будет иметь **Действие копирования** копирует данные из Azure BLOB-объект для выходной таблицы базы данных SQL Azure.
[Шаг 5: Мониторинг наборов данных и конвейера](#MonitorDataSetsAndPipeline) | На этом шаге будет отслеживать наборы данных и с помощью Azure PowerShell на этом этапе конвейера.

## <a name="CreateDataFactory"></a>Шаг 1: Создание фабрики данных Azure
На этом шаге использовать Azure PowerShell для создания фабрики данных Azure с именем **ADFTutorialDataFactoryPSH**.

1. Запустите **Azure PowerShell** и выполните следующие команды. Не закрывайте Azure PowerShell до завершения этого учебника. Если закрыть и снова открыть, необходимо еще раз выполните следующие команды.
	- Запустите **Add-AzureAccount** и введите имя пользователя и пароль, используемый для входа на портале Azure предварительной версии.  
	- Запустите **Get-AzureSubscription** для просмотра всех подписок для этой учетной записи.
	- Запустите **Select-AzureSubscription** выберите подписку, которую требуется работать с. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure. 
2. Переключитесь в **AzureResourceManager** режиме как командлеты фабрика данных Azure доступны в этом режиме.

		Switch-AzureMode AzureResourceManager 
3. Создание группы ресурсов Azure с именем: **ADFTutorialResourceGroup** выполнив следующую команду.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике предполагается использовать группу ресурсов с именем **ADFTutorialResourceGroup**. При использовании различных группах ресурсов, необходимо будет использовать его вместо ADFTutorialResourceGroup в этом учебнике. 
4. Запустите **Создать AzureDataFactory** командлет, чтобы создать фабрику данных с именем: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Имя фабрики данных Azure должно быть глобально уникальным. Если сообщение об ошибке: **данных фабрики имя «ADFTutorialDataFactoryPSH» недоступно**, измените имя (например, yournameADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике.

## <a name="CreateLinkedServices"></a>Шаг 2: Создание связанные службы
Связанные службы связывают хранилища данных или вычислительные службы с фабрикой данных Azure. Хранилище данных может быть хранилище Azure, база данных SQL Azure или локальной базы данных SQL Server содержит входные данные или сохраняет выходные данные для конвейера данных фабрики. Службы вычислений — это служба, которая обрабатывает входные данные и формирует выходные данные.

На этом шаге вы создадите две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связанные ссылки службы учетную запись хранилища Azure и AzureSqlLinkedService связывает фабрику данных базы данных Azure SQL: **ADFTutorialDataFactoryPSH**. Далее в этом учебнике, копирующего данные из контейнера BLOB-объектов в StorageLinkedService в таблицу SQL AzureSqlLinkedService будет создать конвейер.

### Создание связанной службы для учетной записи хранения Azure
1.	Создайте файл JSON **StorageLinkedService.json** в **C:\ADFGetStartedPSH** со следующим содержимым. Создайте папку ADFGetStartedPSH, если он еще не существует.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	В **Azure PowerShell**, переключитесь в **ADFGetStartedPSH** папки. 
3.	Можно использовать **Создать AzureDataFactoryLinkedService** командлет для создания связанной службы. Этот командлет и другие командлеты фабрика данных использовать в этом учебнике требуется передавать значения **ResourceGroupName** и **DataFactoryName** параметров. Кроме того, можно использовать **Get AzureDataFactory** для получения объекта DataFactory и передать ему объект без ввода ResourceGroupName и DataFactoryName при каждом выполнении командлета. Выполните следующую команду для назначения выходные данные **Get AzureDataFactory** командлета переменной: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	Теперь запустите **Создать AzureDataFactoryLinkedService** командлет, чтобы создать связанные службы: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	При запуске не **Get AzureDataFactory** командлета и назначен выходные данные **$df** переменной необходимо указать значения для параметров ResourceGroupName и DataFactoryName следующим образом.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Если закрыть Azure PowerShell в центре учебника будет выполнения командлета Get-AzureDataFactory следующий раз запустить Azure PowerShell для работы с учебником.

### Создание связанной службы для базы данных SQL Azure
1.	Создайте файл JSON AzureSqlLinkedService.json со следующим содержимым.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Выполните следующую команду для создания связанной службы. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Убедитесь, что **Разрешить доступ к службам Azure** включен для сервера Azure SQL. Для проверки и включить его, выполните следующие действия.
	>
	> <ol>
<li>Щелкните <b>Обзор</b> концентратора слева, затем щелкните <b>серверов SQL Server</b>.</li>
<li>Выберите сервер и нажмите кнопку <b>Параметры</b> на <b>SQL SERVER</b> выноски.</li>
<li>В <b>Параметры</b> стоечный модуль, щелкните <b>брандмауэра</b>.</li>
<li>В <b>Параметры Firewalll</b> стоечный модуль, щелкните <b>ON</b> для <b>Разрешить доступ к службам Azure</b>.</li>
<li>Щелкните <b>ACTIVE</b> концентратора слева, чтобы переключиться на <b>фабрика данных</b> были на выноске.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3: Создания входных и выходных таблиц

На предыдущем шаге вы создали связанные службы **StorageLinkedService** и **AzureSqlLinkedService** связать учетную запись хранилища Azure и базы данных Azure SQL в фабрику данных: **ADFTutorialDataFactoryPSH**. На этом шаге вы создадите таблицы, представления входных данных и выходные данные для операции копирования в конвейере, которые будут созданы на следующем шаге.

Таблица представляет собой прямоугольный набор данных, и это единственный тип набора данных, поддерживаемый в настоящее время. Входной таблицы в этом учебнике означает контейнер больших двоичных объектов в хранилище Azure, StorageLinkedService точек, а выходной таблицы в таблицу SQL в базе данных Azure SQL, указывающего AzureSqlLinkedService.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Пропустите этот шаг, если Вы завершили учебника [Приступая к работе с фабрикой данных Azure][adf-get-started] статьи.

Необходимо выполнить следующие действия, чтобы подготовить хранилище больших двоичных объектов и базой данных Azure SQL для этого учебника.
 
* Создание контейнера BLOB-объекта с именем **adftutorial** в Azure хранилище BLOB-объектов, **StorageLinkedService** указывает. 
* Создание и отправка текстового файла, **emp.txt**, как BLOB-объект для **adftutorial** контейнера. 
* Создать таблицу с именем **emp** в базе данных SQL Azure в Azure SQL database **AzureSqlLinkedService** указывает.


1. Запустите Блокнот и вставьте следующий текст сохраняется как **emp.txt** для **C:\ADFGetStartedPSH** папку на жестком диске. 

        John, Doe
		Jane, Doe
				
2. Использовать средства, такие как [обозревателя хранилищ Azure](https://azurestorageexplorer.codeplex.com/) для создания **adftutorial** контейнера и передачи **emp.txt** файла в контейнер.

    ![Обозреватель хранилищ Azure][image-data-factory-get-started-storage-explorer]
3. Используйте следующий сценарий SQL для создания **emp** таблицы в базе данных SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	При наличии на компьютере SQL Server 2014: следуйте инструкциям из [Шаг 2: подключение к базе данных SQL, управление базы данных SQL Azure с помощью SQL Server Management Studio][sql-management-studio] статьи для подключения к серверу Azure SQL и запустите сценарий SQL.

	При наличии на компьютере установлена Visual Studio 2013: в предварительной версии портала Azure ([http://portal.azure.com](http://portal.sazure.com)), нажмите кнопку **Обзор** концентратора на левый, нажмите кнопку **серверов SQL Server**, выберите базу данных и нажмите кнопку **в среде Visual Studio** кнопки на панели инструментов для подключения к серверу Azure SQL и запустите сценарий. Если ваш клиент не может получить доступ к серверу Azure SQL, необходимо настроить брандмауэр разрешить доступ с компьютера (IP-адрес) сервера Azure SQL. См. в статье выше шаги по настройке брандмауэра для сервера Azure SQL.
		
### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable** указывающего на контейнер больших двоичных объектов в хранилище Azure, представленного **StorageLinkedService** связанные службы. Этот контейнер больших двоичных объектов (** adftutorial **) содержит входные данные в файле: **emp.txt**.

1.	Создайте файл JSON **EmpBlobTable.json** в **C:\ADFGetStartedPSH** папку со следующим содержимым:

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
	                "linkedServiceName": "StorageLinkedService"
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
	
	- расположение **тип** равен **AzureBlobLocation**.
	- **linkedServiceName** равен **StorageLinkedService**. 
	- **folderPath** равен **adftutorial** контейнера. Вы также можете указать имя большого двоичного объекта внутри папки. Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- формат **тип** равен **TextFormat**
	- Существует два поля в текстовом файле — **FirstName** и **LastName** —, разделенных запятой (** columnDelimiter **)	
	-  **Доступности** равен **каждый час** (** частоты ** равен **час** и **интервал** имеет значение **1** ), поэтому служба фабрики данных будет искать входных данных каждый час в корневой папке в контейнер больших двоичных объектов (** adftutorial **) было задано.

	Если не указать **fileName** для **ввода** **таблицы**, все файлы и большие двоичные объекты из входной папки (** folderPath **) рассматриваются в качестве входных данных. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Образцы файлов в разделе [учебника][adf-tutorial] примеры.
 
	Если вы не укажете **fileName** для **выходную таблицу**, созданных файлов в **folderPath** именами в следующем формате: данных. < Guid > .txt (пример: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Чтобы динамически установить параметры **folderPath** и **fileName** на основе времени **SliceStart**, используйте свойство **partitionedBy**. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а в fileName используется время (часы) из SliceStart. Например, если срез выполняется для временной отметки 2014-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2014/10/20, а fileName – 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.

2.	Выполните следующую команду для создания таблицы данных фабрики.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Создание выходной таблицы
В этой части шага вы создадите выходную таблицу с именем **EmpSQLTable** указывающего на таблицу SQL (** emp **) в базе данных Azure SQL, представленного **AzureSqlLinkedService** связанные службы. Конвейер копирует данные из входного большого двоичного объекта для **emp** таблицы.

1.	Создайте файл JSON **EmpSQLTable.json** в **C:\ADFGetStartedPSH** папку со следующим содержимым.
		
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
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     Обратите внимание на следующее:
	
	* расположение **тип** равен **AzureSQLTableLocation**.
	* **linkedServiceName** равен **AzureSqlLinkedService**.
	* **tablename** равен **emp**.
	* Имеются три столбца — **идентификатор**, **FirstName**, и **LastName** — в таблице emp в базе данных, но идентификатор является столбцом идентификаторов, поэтому необходимо указать только **FirstName** и **LastName** здесь.
	*  **Доступности** равен **каждый час** (** частоты ** значение **час** и **интервал** значение **1**). Служба фабрики данных создает фрагмент выходных данных каждый час в **emp** таблицы в базе данных Azure SQL.

2.	Выполните следующую команду для создания таблицы данных фабрики.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Шаг 4: Создание и запуск конвейера
На этом этапе можно создать конвейер с **Действие копирования** использующий **EmpTableFromBlob** в качестве входного и **EmpSQLTable** как output.

1.	Создайте файл JSON **ADFTutorialPipeline.json** в **C:\ADFGetStartedPSH** папку со следующим содержимым: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
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
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Обратите внимание на следующее:

	- В разделе «действия» имеется только одно действие, **тип** равен **CopyActivity**.
	- Ввод действия задается **EmpTableFromBlob** и вывода задается действие **EmpSQLTable**.
	- В **преобразования** разделе **BlobSource** указан в качестве типа источника и **SqlSink** указан как тип приемника.

	> [AZURE.NOTE]Замените значение **Запуск** свойство с текущего дня и **конец** значение со следующего дня. Как запустить и даты окончания должны находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z.  **Конец** время необязательно, но мы будем использовать в этом учебнике. Если не указать значение для **конец** свойство, оно вычисляется как "** start + 48 часов **». Укажите на бесконечное выполнение конвейера **9, 9-9999** как значение **end** свойство. В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
	
	> [Ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Выполните следующую команду для создания таблицы данных фабрики. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5: Мониторинг наборы данных и конвейера
На этом шаге будет использовать Azure PowerShell для наблюдения за то, что происходит в фабрику данных Azure.

1.	Запустите **Get AzureDataFactory** и присвойте результат переменной $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Запустите **Get AzureDataFactorySlice** для получения сведений о всех фрагментов из **EmpSQLTable**, являющийся конвейера выходной таблицы.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Замените год, месяц и дату **StartDateTime** параметра с текущего года, месяца и даты. Оно должно соответствовать **Запуск** значение в конвейере JSON.

	Вы увидите 24 срезов — по одному для каждого часа с 00: 00 текущего дня до 00: 00 следующего дня.
	
	**Первый фрагмент:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Последний фрагмент:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Запустите **Get AzureDataFactoryRun** чтобы получить сведения о действие выполняется **конкретного** срез. Измените значение **StartDateTime** параметр в соответствии с **Запуск** время фрагмента в приведенном выше примере. Значение **StartDateTime** должен находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Вы должны увидеть результат, аналогичный приведенному ниже:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]В разделе [Справочник по командлетам фабрики данных][cmdlet-reference] для Полная документация по командлетам фабрика данных.

## Дальнейшие действия

Статья | Описание
------ | ---------------
[Копирование данных с фабрикой данных Azure - действие копирования][copy-activity] | В этой статье содержит подробное описание **Действие копирования** используемые в этом учебнике. 
[Включить конвейеры для работы с локальными данными][use-onpremises-datasources] | Данная статья содержит пошаговое руководство, которое показано, как скопировать данные из **локальной базы данных SQL Server** в BLOB-объект Azure. 
[Использование Pig и Hive с фабрикой данных][use-pig-and-hive-with-data-factory] | Данная статья содержит пошаговое руководство, которое показывает, как использовать **HDInsight действия** для запуска **hive и pig** сценарий для обработки входных данных для получения выходных данных.
[Учебник: Перемещение и обрабатывать файлы журнала, с помощью фабрики данных][adf-tutorial] | В этой статье приведены **в сквозном примере** показано, как реализовать **реалистичном** используется фабрика данных Azure для преобразования данных из файлов журнала в дополнительная информация.
[Использовать настраиваемые действия в фабрике данных][use-custom-activities] | Эта статья содержит пошаговое руководство с пошаговые инструкции по созданию **настраиваемое действие** и их использования в конвейере. 
[Устранение неполадок фабрика данных][troubleshoot] | В этой статье описывается как **Устранение** выдает фабрика данных Azure. Вы можете использовать руководство из этой статьи на учебной фабрике данных ADFTutorialDataFactory, намеренно создав ошибки (удалив таблицу в базе данных SQL Azure). 
[Справочник по командлетам фабрики данных Azure][cmdlet-reference] | Это справочные материалы содержит подробные сведения обо всех **командлеты фабрика данных**.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика имеется содержимое полный Справочник командлетов, скрипта JSON, функции и т. д... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: powershell-install-configure.md
[data-factory-create-sql-database]: sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir-->