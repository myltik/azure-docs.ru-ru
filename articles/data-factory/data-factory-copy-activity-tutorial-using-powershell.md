.<properties 
	pageTitle="Учебник. Создание конвейера с действием копирования с помощью Azure PowerShell" 
	description="В этом руководстве вы создадите конвейер фабрики данных Azure с действием копирования с помощью Azure PowerShell." 
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
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Учебник. Создание конвейера с действием копирования с помощью Azure PowerShell
> [AZURE.SELECTOR]
- [Обзор учебника](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Редактор фабрики данных](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Использование интерфейса REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)

В руководстве [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) рассматривается создание и мониторинг фабрики данных Azure с помощью [портала Azure][azure-portal]. В этом руководстве описывается создание и отслеживание фабрики данных Azure с помощью командлетов Azure PowerShell. Конвейер в фабрике данных, который вы создадите в ходе изучения этого руководства, копирует данные из двоичного объекта Azure в базу данных SQL Azure с помощью действия копирования.

Действие копирования означает перемещение данных в фабрику данных Azure. Это действие выполняется глобально доступной службой, обеспечивающей безопасное, надежное и масштабируемое копирование данных из одного хранилища в другое. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).

> [AZURE.IMPORTANT] 
Перед изучением этого руководства перейдите к статье [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) и выполните необходимые предварительные действия.
>   
> В этой статье рассматриваются не все командлеты фабрики данных. Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/dn820234.aspx).
  

##Предварительные требования
Помимо предварительных требований, перечисленных в разделе "Обзор руководства", необходимо:

- **Azure PowerShell**. Далее, чтобы установить Azure PowerShell на локальном компьютере, следуйте указаниям в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

##В этом учебнике рассматриваются следующие темы:
В следующей таблице перечислены шаги, которые выполняются в рамках этого руководства, а также их описание.

Шаг | Описание
-----| -----------
[Создание фабрики данных Azure](#create-data-factory) | На этом шаге создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**. 
[Создание связанных служб](#create-linked-services) | На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связывает хранилище Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с ADFTutorialDataFactoryPSH.
[Создать входные и выходные наборы данных](#create-datasets) | На этом шаге определяются два набора данных (**EmpTableFromBlob** и **EmpSQLTable**), которые будут использоваться в качестве входных и выходных таблиц в **действии копирования** в конвейере ADFTutorialPipeline, созданном на следующем шаге.
[Создание и запуск конвейера](#create-pipeline) | На этом шаге создается конвейер с именем **ADFTutorialPipeline** в фабрике данных **ADFTutorialDataFactoryPSH**. Данный конвейер содержит **действие копирования**, которое копирует данные из большого двоичного объекта Azure в выходную таблицу базы данных Azure.
[Контроль наборов данных и конвейера](#monitor-pipeline) | На этом шаге выполняется мониторинг наборов данных и конвейера с помощью Azure PowerShell.

## Создание фабрики данных
На этом шаге с помощью Azure PowerShell создается фабрика данных Azure с именем **ADFTutorialDataFactoryPSH**.

1. Откройте **PowerShell** и выполните приведенные ниже команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	1. Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	
			Login-AzureRmAccount   
	2. Выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи.

			Get-AzureRmSubscription 
	3. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените **&lt;NameOfAzureSubscription&gt;** именем подписки Azure.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

3. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем **ADFTutorialResourceGroup**. Если вы используете другую группу ресурсов, укажите ее вместо ADFTutorialResourceGroup.
4. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем **ADFTutorialDataFactoryPSH**.

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	
Обратите внимание на следующее:
 
- Имя фабрики данных Azure должно быть глобально уникальным. При выводе сообщения об ошибке **Имя ADFTutorialDataFactoryPSH фабрики данных недоступно** измените это имя (например, на ваше\_имя\_ADFTutorialDataFactoryPSH). Используйте это имя вместо ADFTutorialFactoryPSH при выполнении шагов в этом учебнике. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
- Чтобы создать экземпляры фабрики данных, вы должны быть администратором или участником подписки Azure.
- В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.
- Если появится сообщение об ошибке **Подписка не зарегистрирована для использования пространства имен Microsoft.DataFactory**, выполните одно из следующих действий и повторите попытку публикации.

	- В Azure PowerShell выполните следующую команду, чтобы зарегистрировать поставщик фабрики данных Azure:
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Чтобы убедиться, что поставщик фабрики данных зарегистрирован, выполните следующую команду:
	
			Get-AzureRmResourceProvider
	- Войдите на [портал Azure](https://portal.azure.com) с использованием подписки Azure и откройте колонку фабрики данных или создайте на портале фабрику данных. Поставщик будет зарегистрирован автоматически.

## Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. Хранилище данных может располагаться в хранилище Azure, в базе данных SQL Azure или в локальной базе данных SQL Server, которая содержит вводные данные или в которой сохраняются выходные данные для конвейера фабрики данных. Служба вычислений — это служба, которая обрабатывает вводные данные и создает выходные данные.

На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. Связанная служба StorageLinkedService связывает учетную запись хранения Azure, а служба AzureSqlLinkedService связывает базу данных SQL Azure с фабрикой данных **ADFTutorialDataFactoryPSH**. Далее в этом руководстве вы создадите конвейер, который копирует данные из контейнера больших двоичных объектов в StorageLinkedService в таблицу SQL в AzureSqlLinkedService.

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

	Если вы закроете Azure PowerShell посреди этого руководства, то придется запустить командлет Get-AzureRmDataFactory при следующем запуске Azure PowerShell, чтобы завершить изучение руководства.

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

	Вместо **servername**, **databasename**, **username@servername** и **password** укажите имя своего сервера Azure SQL Server, имя базы данных, имя учетной записи пользователя и пароль.

2.	Выполните следующую команду для создания связанной службы.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Убедитесь, что настройка **Разрешить доступ к службам Azure** включена для вашего сервера Azure SQL Server. Чтобы проверить и при необходимости включить этот параметр, выполните следующие действия.

	1. Щелкните раздел **ОБЗОР** слева и выберите пункт **Серверы SQL**.
	2. Выберите свой сервер и щелкните пункт **ПАРАМЕТРЫ** в колонке SQL SERVER.
	3. В колонке **ПАРАМЕТРЫ** щелкните пункт **Брандмауэр**.
	4. В колонке **Параметры брандмауэра** щелкните **ВКЛ** для параметра **Разрешить доступ к службам Azure**.
	5. Щелкните раздел **АКТИВНО** слева, чтобы переключиться в колонку **Фабрика данных**, которую вы использовали ранее.
	

## Создание наборов данных

На предыдущем шаге были созданы связанные службы **StorageLinkedService** и **AzureSqlLinkedService** для связи учетной записи хранения Azure и базы данных SQL Azure с фабрикой данных **ADFTutorialDataFactoryPSH**. На этом шаге создаются наборы данных, представляющие входные и выходные данные для действия копирования в конвейере, который будет создан на следующем шаге.

Таблица представляет собой прямоугольный набор данных, и это единственный тип набора данных, поддерживаемый в настоящее время. Таблица входных данных в этом учебнике ссылается на контейнер больших двоичных объектов в хранилище Azure, на которое указывает StorageLinkedService, а таблица выходных данных ссылается на таблицу SQL в базе данных SQL Azure, на которую указывает AzureSqlLinkedService.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Пропустите этот шаг, если вы завершили изучение руководства [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Чтобы подготовить хранилище больших двоичных объектов Azure и базу данных SQL Azure для этого учебника, необходимо выполнить следующие шаги.
 
* Создайте контейнер больших двоичных объектов с именем **adftutorial** в хранилище больших двоичных объектов Azure, на которое указывает **StorageLinkedService**.
* Создайте и отправьте текстовый файл **emp.txt** в качестве большого двоичного объекта в этот контейнер **adftutorial**.
* Создайте таблицу с именем **emp** в базе данных SQL Azure, на которую указывает **AzureSqlLinkedService**.


1. Запустите «Блокнот», вставьте следующий текст и сохраните его с именем **emp.txt** в папке **C:\\ADFGetStartedPSH** на жестком диске.

        John, Doe
		Jane, Doe
				
2. При помощи таких средств, как [обозреватель хранилища Azure](https://azurestorageexplorer.codeplex.com/), создайте контейнер **adftutorial** и загрузите файл **emp.txt** в этот контейнер.

    ![Обозреватель хранилищ Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
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

	Если на компьютере установлено программное обеспечение Visual Studio 2013, на портале Azure ([http://portal.azure.com](http://portal.sazure.com)) щелкните узел **Просмотр** в левой части, **Серверы SQL**, выберите свою базу данных и на панели инструментов нажмите кнопку **Открыть в Visual Studio**, чтобы подключиться к серверу SQL Azure и запустить этот скрипт. Если клиенту не разрешен доступ к серверу Azure SQL Server, следует настроить брандмауэр вашего сервера Azure SQL Server, чтобы разрешить доступ с вашей машины (IP-адрес). См. статью выше, чтобы ознакомиться с шагами по настройке брандмауэра для сервера Azure SQL Server.
		
### Создание входного набора данных 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable**, указывающая на контейнер больших двоичных объектов в службе хранилища Azure, представленной связанной службой **StorageLinkedService**. Этот контейнер больших двоичных объектов (**adftutorial**) содержит входные данные в файле **emp.txt**.

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
	
	Обратите внимание на следующее:
	
	- для параметра **type** набора данных задано значение **AzureBlob**;
	- Для **LinkedServiceName** задано значение **StorageLinkedService**.
	- В качестве значения **folderPath** установлен контейнер **adftutorial**.
	- Для параметра **fileName** задано значение **emp.txt**. Если имя большого двоичного объекта не указано, данные из всех больших двоичных объектов в контейнере считаются входными данными.
	- Для **типа** формата установлено значение **TextFormat**
	- В этом текстовом файле имеются два поля — **FirstName** и **LastName**, — которые разделяются запятой (**columnDelimiter**).
	- Для параметра **availability** задано значение **hourly** (**frequency** присвоено значение **hour**, а параметру **interval** — **1**), что означает, что служба фабрики данных будет каждый час искать входные данные в корневой папке заданного вами контейнера больших двоичных объектов (**adftutorial**).

	Если параметр **fileName** для **входной** **таблицы** не задан, все файлы и большие двоичные объекты из входной папки (**folderPath**) считаются входными данными. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные.
 
	Если не указать **fileName** для **выходной таблицы**, то созданные в **folderPath** файлы получают имена в следующем формате: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2.	Выполните следующую команду для создания набора данных фабрики данных.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Создание выходного набора данных
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

     Обратите внимание на следующее:
	
	* Для параметра **type** набора данных задано значение **AzureSQLTable**.
	* **LinkedServiceName** имеет значение **AzureSqlLinkedService**.
	* **Tablename** имеет значение **emp**.
	* В таблице emp базы данных имеются три столбца: **ID**, **FirstName** и **LastName**, но ID — это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
	* Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**). Служба фабрики данных каждый час создает срез выходных данных в таблице **emp** в базе данных SQL Azure.

2.	Выполните следующую команду для создания набора данных фабрики данных.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## Создание конвейера
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

	Обратите внимание на следующее:

	- В разделе действий доступно только одно действие, параметр **type** которого имеет значение **Copy**.
	- Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных — **EmpSQLTable**.
	- В разделе **transformation** в качестве типа источника указан **BlobSource**, а в качестве типа приемника указан **SqlSink**.

	Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Время **окончания** указывать не обязательно, однако в этом примере мы будем его использовать.
	
	Если не указать значение свойства **end**, оно вычисляется по формуле «**время начала + 48 часов**». Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9/9/9999** для свойства **end**.
	
	В приведенном выше примере 24 среза данных, так как срезы данных производятся каждый час.
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Выполните следующую команду для создания таблицы фабрики данных.
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

## Отслеживание конвейера
На этом шаге используется Azure PowerShell для мониторинга процессов в фабрике данных Azure.

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

## Сводка
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали PowerShell для создания фабрики данных, связанных служб, наборов данных и конвейера. Вот обобщенные действия, которые вы выполнили в этом руководстве:

1.	Создание **фабрики данных Azure**.
2.	Создание **связанных служб**.
	1. **Служба хранилища Azure** — связанная служба для связи с учетной записью хранения Azure, которая содержит входные данные.
	2. **SQL Azure** — связанная служба для связи с базой данных SQL Azure, которая содержит выходные данные.
3.	Создание **наборов данных**, описывающих входные и выходные данные для конвейеров.
4.	Создание **конвейера** с **BlobSource** в качестве источника и **SqlSink** в качестве приемника с помощью **действия копирования**.

## См. также
| Раздел | Description (Описание) |
| :---- | :---- |
| [Действия перемещения данных](data-factory-data-movement-activities.md) | В этой статье содержится информация о действии копирования, которое использовалось в этом руководстве. |
| [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) | Здесь объясняются аспекты планирования и исполнения в модели приложений фабрики данных. |
| [Конвейеры](data-factory-create-pipelines.md) | Эта статья поможет вам понять сущность конвейеров и действий в фабрике данных Azure, а также научиться с их помощью создавать комплексные рабочие процессы, управляемые данными, для конкретных бизнес-сценариев. |
| [Наборы данных](data-factory-create-datasets.md) | Эта статья поможет вам понять, что такое наборы данных в фабрике данных Azure.
| [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md) | В этой статье описывается мониторинг и отладка конвейеров, а также управление ими с помощью приложения мониторинга и управления. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0817_2016-->