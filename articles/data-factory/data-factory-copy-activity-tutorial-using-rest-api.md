<properties 
	pageTitle="Руководство. Создание конвейера с действием копирования с помощью REST API" 
	description="С помощью этого руководства вы, используя REST API, создадите конвейер фабрики данных Azure с действием копирования." 
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
	ms.date="08/17/2016" 
	ms.author="spelluru"/>

# Руководство. Создание конвейера с действием копирования с помощью REST API
> [AZURE.SELECTOR]
- [Обзор учебника](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Редактор фабрики данных](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Использование интерфейса REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)

В этом руководстве рассматривается создание и мониторинг фабрики данных Azure с помощью REST API. Конвейер в фабрике данных копирует данные из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью действия копирования.

Действие копирования означает перемещение данных в фабрику данных Azure. Это действие выполняется глобально доступной службой, обеспечивающей безопасное, надежное и масштабируемое копирование данных из одного хранилища в другое. Дополнительные сведения о действии копирования см. в статье [Действия перемещения данных](data-factory-data-movement-activities.md).

> [AZURE.NOTE] 
В этой статье рассматриваются не все REST API фабрики данных. Полную документацию по командлетам фабрики данных см. в [справочнике по REST API фабрики данных](https://msdn.microsoft.com/library/azure/dn906738.aspx).
  

## Предварительные требования

- Дополнительные сведения см. в статье [Обзор руководства](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Установите на компьютер программу [curl](https://curl.haxx.se/dlwiz/). Она будет использоваться с командами REST для создания фабрики данных.
- Следуя инструкциям в [этой статье](../resource-group-create-service-principal-portal.md), выполните следующее:
	1. Создайте веб-приложение с именем **ADFCopyTutotiralApp** в Azure Active Directory.
	2. Получите **идентификатор клиента** и **секретный ключ**.
	3. Получите значение для **tenant\_id**.
	4. Назначьте приложение **ADFCopyTutotiralApp** роли **участника фабрики данных**.
- Установите [Azure PowerShell](../powershell-install-configure.md).
- Откройте **PowerShell** и выполните приведенные ниже команды. Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	1. Выполните следующую команду и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	
			Login-AzureRmAccount   
	2. Выполните следующую команду, чтобы просмотреть все подписки для этой учетной записи.

			Get-AzureRmSubscription 
	3. Выполните следующую команду, чтобы выбрать подписку, с которой вы собираетесь работать. Замените **&lt;NameOfAzureSubscription&gt;** именем подписки Azure.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
	1. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup**, выполнив следующую команду в PowerShell.

			New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

		Если группа ресурсов уже есть, укажите, требуется ли ее обновить (Y или N).

		Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем ADFTutorialResourceGroup. Если вы используете другую группу ресурсов, укажите ее имя вместо ADFTutorialResourceGroup.
  
## Создание определений JSON
В папке, где находится файл curl.exe, создайте следующие JSON-файлы.

### datafactory.json 
> [AZURE.IMPORTANT] Имя должно быть глобально уникальным, поэтому может потребоваться добавить к ADFCopyTutorialDF префикс или суффикс.

	{  
	    "name": "ADFCopyTutorialDF",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Замените значения **accountname** и **accountkey** на имя вашей учетной записи хранения Azure и ее ключ. Сведения о получении ключа доступа к хранилищу см. в разделах о [просмотре, копировании и повторном создании ключей доступа к хранилищу](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] Замените **servername**, **databasename**, **username** и **password** на имя сервера SQL Azure, имя базы данных SQL, имя учетной записи пользователя и ее пароль.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}


### inputdataset.json

	{
	  "name": "AzureBlobInput",
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
	    "linkedServiceName": "AzureStorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "fileName": "emp.txt",
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

Определение JSON определяет набор данных с именем **AzureBlobInput**, представляющий входные данные для действия в конвейере. Кроме того, он указывает, что входные данные размещаются в файле **emp.txt**, который находится в контейнере больших двоичных объектов **adftutorial**.

 Обратите внимание на следующее:

- для параметра **type** набора данных задано значение **AzureBlob**;
- Для **linkedServiceName** задано значение **AzureStorageLinkedService**.
- Для **folderPath** задано имя контейнера **adftutorial**, а для **fileName** — **emp.txt**.
- Для **типа** формата установлено значение **TextFormat**
- В этом текстовом файле имеются два поля — **FirstName** и **LastName**, — которые разделяются запятой (**columnDelimiter**).
- Для параметра **availability** задано значение **hourly** (параметру frequency присвоено значение hour, а параметру interval — значение 1), что означает, что служба фабрики данных будет каждый час искать входные данные в корневой папке заданного вами контейнера больших двоичных объектов (**adftutorial**).

Если параметр **fileName** для входного набора данных не задан, все файлы и большие двоичные объекты из входной папки (**folderPath**) считаются входными данными. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные.

Если не указать **fileName** для **выходной таблицы**, то созданные в **folderPath** файлы получают имена в следующем формате: Data.&lt;Guid&gt;.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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


### outputdataset.json
	
	{
	  "name": "AzureSqlOutput",
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


Определение JSON определяет набор данных с именем **AzureSqlOutput**, представляющий выходные данные для действия в конвейере. Кроме того, он указывает, что результаты сохраняются в таблице **emp**, которая содержится в базе данных, представленной AzureSqlLinkedService. В разделе **availability** указывается, что выходной набор данных будет создаваться ежечасно (frequency — hour, interval — 1).

Обратите внимание на следующее:

- Для параметра **type** набора данных задано значение **AzureSQLTable**.
- **LinkedServiceName** имеет значение **AzureSqlLinkedService**.
- **Tablename** имеет значение **emp**.
- В таблице emp базы данных имеются три столбца: **ID**, **FirstName** и **LastName**, но ID — это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
- Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**). Служба фабрики данных каждый час создает срез выходных данных в таблице **emp** в базе данных SQL Azure.

### pipeline.json

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
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	}


Обратите внимание на следующее:

- В разделе действий доступно только одно действие, **тип** которого имеет значение **CopyActivity**.
- Для входных данных действия задано значение **AzureBlobInput**, а для выходных данных — **AzureSqlOutput**.
- В разделе **transformation** в качестве типа источника указан **BlobSource**, а в качестве типа приемника указан **SqlSink**.

Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Можно указать только часть даты и пропустить временную часть указанной даты и времени. Например, "2015-02-03", что эквивалентно "2015-02-03T00:00:00Z"

Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Время **окончания** указывать не обязательно, однако в этом примере мы будем его использовать.

Если не указать значение свойства **end**, оно вычисляется по формуле «**время начала + 48 часов**». Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9999-09-09** в качестве значения свойства **окончание**.

В приведенном выше примере 24 среза данных, так как срезы данных производятся каждый час.
	
> [AZURE.NOTE] Дополнительные сведения о свойствах JSON, используемых в приведенном выше примере, см. в статье о [принципе работы конвейера](data-factory-create-pipelines.md#anatomy-of-a-pipeline).

## Настройка глобальных переменных

Выполните следующие команды в Azure PowerShell, подставив собственные значения.

> [AZURE.IMPORTANT] Инструкции по получению значений для параметров client\_id, client\_secret, tenant и subscription\_id см. в разделе [Предварительные требования](#prerequisites).

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "ADFCopyTutorialDF"

## Проверка подлинности с помощью AAD 
Выполните следующую команду для проверки подлинности с помощью Azure Active Directory (AAD).

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 

## Создание фабрики данных

На этом шаге создается фабрика данных Azure с именем **ADFCopyTutorialDF**. Фабрика данных может иметь один или несколько конвейеров. Конвейер может содержать одно или несколько действий. Это может быть, например, действие копирования, копирующее данные из исходного хранилища данных в конечное, и действие HDInsight Hive для выполнения скрипта Hive, преобразующего входные данные в выходные данные продукта. Чтобы создать фабрику данных, выполните следующие команды:

1. Назначьте команду переменной с именем **cmd**.

	Указываемое здесь имя фабрики данных (ADFCopyTutorialDF) должно соответствовать имени, указанному в **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания фабрики данных в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.

		$results

Обратите внимание на следующее:
 
- Имя фабрики данных Azure должно быть глобально уникальным. Если в результатах отобразится сообщение об ошибке **Имя ADFCopyTutorialDF фабрики данных недоступно**, сделайте следующее:
	1. Измените имя (например, на ваше\_имя\_ADFCopyTutorialDF) в файле **datafactory.json**.
	2. В первой команде, где переменной **$cmd** присваивается значение, замените ADFCopyTutorialDF на новое имя и выполните команду.
	3. Выполните следующие две команды, чтобы вызвать REST API для создания фабрики данных и вывода результатов операции.
	
	Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
- Чтобы создать экземпляры фабрики данных, вы должны быть администратором или участником подписки Azure.
- В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.
- Если появится сообщение об ошибке **Подписка не зарегистрирована для использования пространства имен Microsoft.DataFactory**, выполните одно из следующих действий и повторите попытку публикации.

	- В Azure PowerShell выполните следующую команду, чтобы зарегистрировать поставщик фабрики данных Azure:
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Чтобы убедиться, что поставщик фабрики данных зарегистрирован, выполните следующую команду:
	
			Get-AzureRmResourceProvider
	- Войдите на [портал Azure](https://portal.azure.com) с использованием подписки Azure и откройте колонку фабрики данных или создайте на портале фабрику данных. Поставщик будет зарегистрирован автоматически.

Прежде чем создавать конвейер, необходимо создать несколько сущностей фабрики данных. Сначала создайте связанные службы, чтобы связать исходные и целевые хранилища данных со своим хранилищем данных, и определите входные и выходные наборы данных, которые будут представлять данные в связанных хранилищах. Затем создайте конвейер с действием, в котором используются эти наборы данных.

## Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. Хранилище данных может располагаться в службе хранилища Azure, в базе данных SQL Azure или в локальной базе данных SQL Server, которая содержит вводные данные или в которой сохраняются выходные данные для конвейера фабрики данных. Служба вычислений — это служба, которая обрабатывает вводные данные и создает выходные данные.

На этом шаге создаются две связанные службы: **AzureStorageLinkedService** и **AzureSqlLinkedService**. Связанная служба AzureStorageLinkedService связывает учетную запись хранения Azure, а служба AzureSqlLinkedService связывает базу данных SQL Azure с фабрикой данных **ADFCopyTutorialDF**. Далее в этом руководстве создается конвейер, который копирует данные из контейнера больших двоичных объектов в AzuretStorageLinkedService в таблицу SQL в AzureSqlLinkedService.

### Создание связанной службы хранения Azure
На этом шаге вы свяжете учетную запись хранения Azure с фабрикой данных. В целях данного руководства используйте учетную запись хранения Azure для хранения входных данных.

1. Назначьте команду переменной с именем **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания связанной службы в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.
  
		$results

### Создание связанной службы SQL Azure
На этом шаге вы свяжете базу данных SQL Azure с фабрикой данных. В рамках этого руководства используйте ту же самую базу данных SQL Azure для хранения выходных данных.

1. Назначьте команду переменной с именем **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания связанной службы в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.
  
		$results

## Создание наборов данных

На предыдущем шаге были созданы связанные службы **AzureStorageLinkedService** и **AzureSqlLinkedService** для связи учетной записи хранения Azure и базы данных SQL Azure с фабрикой данных **ADFCopyTutorialDF**. На этом шаге создаются наборы данных, представляющие входные и выходные данные для действия копирования в конвейере, который будет создан на следующем шаге.

Входной набор данных в этом руководстве ссылается на контейнер больших двоичных объектов в службе хранилища Azure, на которую указывает AzureStorageLinkedService. Выходной набор данных ссылается на таблицу SQL в базе данных SQL Azure, на которую указывает AzureSqlLinkedService.

### Подготовка хранилища больших двоичных объектов Azure и базы данных SQL Azure для учебника
Чтобы подготовить хранилище BLOB-объектов Azure и базу данных SQL Azure для этого руководства, выполните следующие шаги.
 
* Создайте контейнер больших двоичных объектов с именем **adftutorial** в хранилище BLOB-объектов Azure, на которое указывает **AzureStorageLinkedService**.
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
На этом шаге создается набор данных с именем **AzureBlobInput**, указывающий на контейнер больших двоичных объектов в службе хранилища Azure, которая представлена связанной службой **AzureStorageLinkedService**. Этот контейнер больших двоичных объектов (**adftutorial**) содержит входные данные в файле **emp.txt**.

1. Назначьте команду переменной с именем **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания набора данных в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.
  
		$results

### Создание выходного набора данных
В этой части шага создается выходная таблица с именем **AzureSqlOutput**, которая указывает на таблицу SQL (**emp**) в базе данных SQL Azure, представленную связанной службой **AzureSqlLinkedService**. Конвейер копирует данные из входного большого двоичного объекта в таблицу **emp**.

1. Назначьте команду переменной с именем **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания набора данных в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.
  
		$results 

## Создание конвейера
На этом шаге создается конвейер с **действием копирования**, которое использует **AzureBlobInput** в качестве входных данных и **AzureSqlOutput** в качестве выходных.

1. Назначьте команду переменной с именем **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Выполните команду с использованием командлета **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Просмотрите результаты. После успешного создания набора данных в **результатах** появится JSON-файл. В противном случае отобразится сообщение об ошибке.

		$results

**Поздравляем!** Фабрика данных Azure с конвейером, который копирует данные из хранилища BLOB-объектов Azure в базу данных SQL Azure, успешно создана.

## Отслеживание конвейера
На этом шаге используется REST API фабрики данных, чтобы отслеживать срезы, созданные конвейером.

	$ds ="AzureSqlOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;


	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Выполняйте следующие команды, пока не отобразится срез в состоянии **Готово** или **Сбой**. Когда срез будет в состоянии "Готово", проверьте выходные данные в таблице **emp** в базе данных SQL Azure.

Для каждого среза в таблицу emp в базе данных SQL Azure копируются две строки данных из исходного файла. Таким образом, если все срезы успешно обработаны (в состоянии "Готово"), в таблице emp отобразится 24 записи.


## Сводка
В этом руководстве вы использовали REST API, чтобы создать фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вот обобщенные действия, которые вы выполнили в этом руководстве:

1.	Создание **фабрики данных Azure**.
2.	Создание **связанных служб**.
	1. Служба хранилища Azure — связанная служба для связи с учетной записью хранения Azure, которая содержит входные данные.
	2. SQL Azure — связанная служба для связи с базой данных SQL Azure, которая содержит выходные данные.
3.	Создание **наборов данных**, описывающих входные и выходные данные для конвейеров.
4.	Создание **конвейера** с BlobSource в качестве источника и SqlSink в качестве приемника с помощью действия копирования.

## См. также
| Раздел | Описание |
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