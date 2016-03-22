<properties 
	pageTitle="Учебник. Создание конвейера с действием копирования с помощью Azure PowerShell" 
	description="В этом учебнике вы создадите конвейер фабрики данных Azure с действием копирования с помощью Azure PowerShell." 
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
	ms.topic="get-started-article" 
	ms.date="03/07/2016" 
	ms.author="spelluru"/>

# Учебник. Создание конвейера с действием копирования с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Обзор учебника](data-factory-get-started.md)
- [Редактор фабрики данных](data-factory-get-started-using-editor.md)
- [Visual Studio](data-factory-get-started-using-vs.md)
- [PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)

В учебнике [Начало работы с фабрикой данных Azure][adf-get-started] рассматривается создание и мониторинг фабрики данных Azure при помощи [портала Azure][azure-portal]. В этом учебнике создается и отслеживается фабрика данных Azure с помощью командлетов Azure PowerShell. Конвейер в фабрике данных, который вы создадите в ходе изучения этого руководства, копирует данные из двоичного объекта Azure в базу данных SQL Azure с помощью действия копирования.

Действие копирования означает перемещение данных в фабрику данных Azure. Это действие выполняется глобально доступной службой, обеспечивающей безопасное, надежное и масштабируемое копирование данных из одного хранилища в другое. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).

> [AZURE.IMPORTANT] 
Перед выполнением этого учебника, пожалуйста, перейдите к статье [Обзор учебника](data-factory-get-started.md) и выполните необходимые предварительные действия.
>   
> В этой статье рассматриваются не все командлеты фабрики данных. Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/dn820234.aspx).
  

##Предварительные требования
Помимо необходимых компонентов, перечисленных в разделе "Обзор учебника", необходимо установить на компьютер Azure PowerShell.

- **Azure PowerShell**. Далее, чтобы установить Azure PowerShell на локальном компьютере, следуйте указаниям в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Если вы используете версию Azure PowerShell **более раннюю, чем 1.0**, используйте командлеты, описанные [здесь][old-cmdlet-reference]. Перед использованием командлетов фабрики данных также потребуется выполнить следующие команды:

1. Откройте Azure PowerShell и выполните следующие команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	1. Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	2. Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	3. Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
4. Переключитесь в режим AzureResourceManager, так как командлеты фабрики данных Azure доступны только в этом режиме (**Switch-AzureMode AzureResourceManager**).
  

##В этом учебнике рассматриваются следующие темы:
В следующей таблице перечислены шаги, которые выполняются в рамках этого учебника, а также их описание.

Шаг | Описание
-----| -----------
[Шаг 1. Создание фабрики данных Azure](#CreateDataFactory) | На этом шаге создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**. 
[Шаг 2. Создание связанных служб](#CreateLinkedServices) | На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связывает хранилище Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с ADFTutorialDataFactoryPSH.
[Шаг 3. Создание входных и выходных наборов данных](#CreateInputAndOutputDataSets) | На этом шаге вы определите два набора данных (**EmpTableFromBlob** и **EmpSQLTable**), которые будут использоваться в качестве входных и выходных таблиц в **действии копирования** в конвейере ADFTutorialPipeline, созданном на следующем шаге.
[Шаг 4. Создание и запуск конвейера](#CreateAndRunAPipeline) | На этом шаге создается конвейер с именем **ADFTutorialPipeline** в фабрике данных **ADFTutorialDataFactoryPSH**. Данный конвейер будет иметь **действие копирования**, которое копирует данные из большого двоичного объекта Azure в выходную таблицу базы данных Azure.
[Шаг 5. Мониторинг наборов данных и конвейера](#MonitorDataSetsAndPipeline) | На этом шаге выполняется мониторинг наборов данных и конвейера при помощи Azure PowerShell.

## <a name="CreateDataFactory"></a>Шаг 1. Создание фабрики данных Azure
На этом шаге с помощью Azure PowerShell создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**.

1. Откройте Azure PowerShell и выполните следующую команду. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription <Name of the subscription>**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
3. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup указывайте на этом уроке выбранную вами группу. 
4. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем **ADFTutorialDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	Имя фабрики данных Azure должно быть глобально уникальным. При выводе сообщения об ошибке **Имя фабрики данных ADFTutorialDataFactoryPSH недоступно** измените это имя (например, на вашеимяADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.

	> [AZURE.NOTE] В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

## <a name="CreateLinkedServices"></a>Шаг 2. Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. Хранилище данных может располагаться в хранилище Azure, в базе данных SQL Azure или в локальной базе данных SQL Server, которая содержит вводные данные или в которой сохраняются выходные данные для конвейера фабрики данных. Служба вычислений — это служба, которая обрабатывает вводные данные и создает выходные данные.

На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. Связанная служба StorageLinkedService связывает учетную запись хранения Azure, а служба AzureSqlLinkedService связывает базу данных SQL Azure с фабрикой данных **ADFTutorialDataFactoryPSH**. Далее в этом учебнике будет создан конвейер, который копирует данные из контейнера больших двоичных объектов в StorageLinkedService в таблицу SQL в AzureSqlLinkedService.

### Создание связанной службы для учетной записи хранения Azure
1.	Создайте файл JSON с именем **StorageLinkedService.json** в **C:\\ADFGetStartedPSH** со следующим содержимым. Создайте папку ADFGetStartedPSH, если она еще не существует.

			{
		  		"name": "StorageLinkedService",
		  		"properties": {
	    			"type": "AzureStorage",
		    		"typeProperties": {
		      			"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    		}
		  		}
			}

	Замените **accountname** и **accountkey** на имя вашей учетной записи хранения Azure и ее ключ, соответственно.
2.	В **Azure PowerShell** перейдите в папку **ADFGetStartedPSH**. 
3.	Чтобы создать связанную службу, можно использовать командлет **New-AzureRmDataFactoryLinkedService**. В этом командлете и в других командлетах фабрики данных, которые используются в этом учебнике, требуется передача значений для параметров **ResourceGroupName** и **DataFactoryName**. Кроме того, можно использовать командлет **Get-AzureRmDataFactory**, чтобы получить объект DataFactory и передать этот объект без необходимости ввода параметров ResourceGroupName и DataFactoryName при каждом запуске командлета. Выполните следующую команду, чтобы назначить выходные данные командлета **Get-AzureRmDataFactory** переменной **$df**. 

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Теперь выполните командлет **New-AzureRmDataFactoryLinkedService**, чтобы создать связанную службу **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Если вы не выполнили командлет **Get-AzureRmDataFactory** и назначили выходные данные переменной **$df**, то потребуется указать значения параметров ResourceGroupName и DataFactoryName следующим образом.
		
		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Если вы закроете Azure PowerShell посреди этого учебника, то придется запустить командлет Get-AzureRmDataFactory при следующем запуске Azure PowerShell, чтобы завершить изучение учебника.

### Создание связанной службы для базы данных SQL Azure
1.	Создайте файл JSON с именем AzureSqlLinkedService.json со следующим содержимым.

			{
				"name": "AzureSqlLinkedService",
				"properties": {
					"type": "AzureSqlDatabase",
					"typeProperties": {
				      	"connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
					}
		  		}
			}

	Замените **servername**, **databasename**, ****username@servername** и **password** на имя вашего сервера Azure SQL Server, имя базы данных, имя учетной записи пользователя и пароль.

2.	Выполните следующую команду для создания связанной службы.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Убедитесь, что настройка **Разрешить доступ к службам Azure** включена для вашего сервера Azure SQL Server. Чтобы проверить и при необходимости включить этот параметр, выполните следующие действия.

	1. Щелкните раздел **ОБЗОР** слева и выберите пункт **Серверы SQL**.
	2. Выберите свой сервер и щелкните пункт **ПАРАМЕТРЫ** в колонке SQL SERVER.
	3. В колонке **ПАРАМЕТРЫ** щелкните пункт **Брандмауэр**.
	4. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
	5. Щелкните раздел **АКТИВНО** слева, чтобы переключиться в колонку **Фабрика данных**, которую вы использовали ранее.
	

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3. Создание входных и выходных таблиц

На предыдущем шаге были созданы связанные службы **StorageLinkedService** и **AzureSqlLinkedService** для связи учетной записи хранения Azure и базы данных SQL Azure с фабрикой данных **ADFTutorialDataFactoryPSH**. На этом шаге будут созданы наборы данных, представляющие входные и выходные данные для действия копирования в конвейере, который будет создан на последующем шаге.

Таблица представляет собой прямоугольный набор данных, и это единственный тип набора данных, поддерживаемый в настоящее время. Таблица входных данных в этом учебнике ссылается на контейнер больших двоичных объектов в хранилище Azure, на которое указывает StorageLinkedService, а таблица выходных данных ссылается на таблицу SQL в базе данных SQL Azure, на которую указывает AzureSqlLinkedService.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Пропустите этот шаг, если вы завершили изучение учебника из статьи [Приступая к работе с фабрикой данных Azure][adf-get-started].

Чтобы подготовить хранилище больших двоичных объектов Azure и базу данных SQL Azure для этого учебника, необходимо выполнить следующие шаги.
 
* Создайте контейнер больших двоичных объектов с именем **adftutorial** в хранилище больших двоичных объектов Azure, на которое указывает **StorageLinkedService**. 
* Создайте и отправьте текстовый файл **emp.txt** в качестве большого двоичного объекта в этот контейнер **adftutorial**. 
* Создайте таблицу с именем **emp** в базе данных SQL Azure, на которую указывает **AzureSqlLinkedService**.


1. Запустите «Блокнот», вставьте следующий текст и сохраните его с именем **emp.txt** в папке **C:\\ADFGetStartedPSH** на жестком диске. 

        John, Doe
		Jane, Doe
				
2. При помощи таких средств, как [обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и загрузите файл **emp.txt** в этот контейнер.

    ![Обозреватель хранилищ Azure][image-data-factory-get-started-storage-explorer]
3. Используйте следующий скрипт SQL, чтобы создать таблицу **emp** в базе данных SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Если на вашем компьютере установлен SQL Server 2014, следуйте инструкциям в статье [Шаг 2. Подключение к базе данных SQL управляющей базы данных SQL Azure при помощи SQL Server Management Studio][sql-management-studio] для подключения к серверу Azure SQL Server и запуска скрипта SQL.

	Если на компьютере установлено программное обеспечение Visual Studio 2013, на портале Azure ([http://portal.azure.com](http://portal.sazure.com)) щелкните узел **ПРОСМОТР** в левой части, **Серверы SQL**, выберите свою базу данных и на панели инструментов нажмите кнопку **Открыть в Visual Studio**, чтобы подключиться к серверу SQL Azure и запустить этот скрипт. Если клиенту не разрешен доступ к серверу Azure SQL Server, следует настроить брандмауэр вашего сервера Azure SQL Server, чтобы разрешить доступ с вашей машины (IP-адрес). См. статью выше, чтобы ознакомиться с шагами по настройке брандмауэра для сервера Azure SQL Server.
		
### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable**, указывающая на контейнер больших двоичных объектов в хранилище Azure, представленном связанной службой **StorageLinkedService**. Этот контейнер больших двоичных объектов (**adftutorial**) содержит входные данные в файле **emp.txt**.

1.	Создайте файл JSON с именем **EmpBlobTable.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым:

			{
			  "name": "EmpTableFromBlob",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureBlob",
			    "linkedServiceName": "StorageLinkedService",
			    "typeProperties": {
				  "fileName": "emp.txt",
			      "folderPath": "adftutorial/",
			      "format": {
			        "type": "TextFormat",
			        "columnDelimiter": ","
			      }
			    },
			    "external": true,
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}
	
	Обратите внимание на следующее.
	
	- для параметра **type** набора данных задано значение **AzureBlob**;
	- Для **LinkedServiceName** задано значение **StorageLinkedService**. 
	- В качестве значения **FolderPath** установлен контейнер **adftutorial**. 
	- Для параметра **fileName** задано значение **emp.txt**. Если имя большого двоичного объекта не указано, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- Для **типа** формата установлено значение **TextFormat**
	- В этом текстовом файле имеются два поля — **FirstName** и **LastName**, — которые разделяются запятой (**columnDelimiter**).	
	- Параметр **availability** имеет значение **hourly** (**frequency** имеет значение **hour**, а **interval** имеет значение **1**), что означает, что служба фабрики данных будет каждый час искать входные данные в корневой папке заданного вами контейнера больших двоичных объектов (**adftutorial**).

	Если параметр **fileName** для **входной** **таблицы** не задан, все файлы и большие двоичные объекты из входной папки (**folderPath**) считаются входными данными. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Примеры файлов см. в [учебнике][adf-tutorial].
 
	Если не указать **fileName** для **выходной таблицы**, то созданные в **folderPath** файлы получают имена в следующем формате: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Чтобы динамически установить параметры **folderPath** и **fileName** на основе времени **SliceStart**, используйте свойство **partitionedBy**. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а в fileName используется время (часы) из SliceStart. Например, если срез выполняется для временной отметки 2014-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2014/10/20, а fileName – 08.csv.

			"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	        "fileName": "{Hour}.csv",
	        "partitionedBy": 
	        [
	        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	        ],

	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2.	Выполните следующую команду для создания набора данных фабрики данных.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Создание выходной таблицы
В этой части шага создается выходная таблица с именем **EmpSQLTable**, которая указывает на таблицу SQL (**emp**) в базе данных SQL Azure, представленную связанной службой **AzureSqlLinkedService**. Конвейер копирует данные из входного большого двоичного объекта в таблицу **emp**.

1.	Создайте файл JSON с именем **EmpSQLTable.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым.
		
			{
			  "name": "EmpSQLTable",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureSqlTable",
			    "linkedServiceName": "AzureSqlLinkedService",
			    "typeProperties": {
			      "tableName": "emp"
			    },
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}

     Обратите внимание на следующее.
	
	* Для параметра **type** набора данных задано значение **AzureSQLTable**.
	* **LinkedServiceName** имеет значение **AzureSqlLinkedService**.
	* **Tablename** имеет значение **emp**.
	* В таблице emp базы данных имеются три столбца: **ID**, **FirstName** и **LastName**, но ID — это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
	* Параметр **availability** имеет значение **hourly**, при этом **frequency** получает значение **hour**, а **interval** получает значение **1**. Служба фабрики данных будет создавать срез выходных данных каждый час в таблице **emp** в базе данных SQL Azure.

2.	Выполните следующую команду для создания набора данных фабрики данных.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Шаг 4. Создание и запуск конвейера
На этом шаге создается конвейер с **действием копирования**, которое использует **EmpTableFromBlob** в качестве входных данных и **EmpSQLTable** в качестве выходных.

1.	Создайте файл JSON с именем **ADFTutorialPipeline.json** в папке **C:\\ADFGetStartedPSH** со следующим содержимым: 
	
			 {
			  "name": "ADFTutorialPipeline",
			  "properties": {
			    "description": "Copy data from a blob to Azure SQL table",
			    "activities": [
			      {
			        "name": "CopyFromBlobToSQL",
			        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
			        "type": "Copy",
			        "inputs": [
			          {
			            "name": "EmpTableFromBlob"
			          }
			        ],
			        "outputs": [
			          {
			            "name": "EmpSQLTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "BlobSource"
			          },
			          "sink": {
			            "type": "SqlSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2015-12-09T00:00:00Z",
			    "end": "2015-12-10T00:00:00Z",
			    "isPaused": false
			  }
			}

	Обратите внимание на следующее.

	- В разделе действий доступно только одно действие, параметр **type** которого имеет значение **Copy**.
	- Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных — **EmpSQLTable**.
	- В разделе **transformation** в качестве типа источника указан **BlobSource**, а в качестве типа приемника указан **SqlSink**.

	Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Время **окончания** указывать не обязательно, однако в этом примере мы будем его использовать.
	
	Если не указать значение свойства **end**, оно вычисляется по формуле "**время начала + 48 часов**". Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9/9/9999** для свойства **end**.
	
	В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Выполните следующую команду для создания таблицы фабрики данных. 
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5. Мониторинг наборов данных и конвейера
На этом шаге вы будете использовать Azure PowerShell для мониторинга процессов в фабрике данных Azure.

1.	Выполните командлет **Get-AzureRmDataFactory** и назначьте выходные данные переменной $df.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Выполните командлет **Get-AzureRmDataFactorySlice** для получения сведений обо всех срезах в таблице **EmpSQLTable**, которая является выходной таблицей конвейера.

		Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Замените год, месяц и дату в параметре **StartDateTime** на текущий год, месяц и дату. Это должно соответствовать значению параметра **Start** в конвейере JSON.

	Вы увидите 24 среза — по одному для каждого часа с 00:00 текущего дня до 00:00 следующего дня.
	
	**Первый срез:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     :
		LongRetryCount    : 0

	**Последний срез:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Выполните командлет **Get-AzureRmDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для **конкретного** среза. Измените значение параметра **StartDateTime** так, чтобы оно соответствовало времени в параметре **Start** для среза из выходных данных выше. Значение **StartDateTime** должно быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-03-03T22:00:00Z.

		Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

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

Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных][cmdlet-reference].

## См. также
Дополнительные сведения о **действии копирования** в фабрике данных Azure см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).


[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0316_2016-->