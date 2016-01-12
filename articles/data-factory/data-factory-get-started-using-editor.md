<properties 
	pageTitle="Учебник. Создание конвейера с действием копирования с помощью редактора фабрики данных" 
	description="Это руководство предусматривает создание конвейера фабрики данных Azure с действием копирования с помощью редактора фабрики данных на классическом портале Azure." 
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
	ms.date="11/02/2015" 
	ms.author="spelluru"/>

# Учебник. Создание конвейера с действием копирования с помощью редактора фабрики данных
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)



##Вот какие темы рассматривает этот учебник:
Этот учебник содержит следующие действия.

Шаг | Описание
-----| -----------
[Шаг 1. Создание фабрики данных Azure](#CreateDataFactory) | На этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactory**.  
[Шаг 2. Создание связанных служб](#CreateLinkedServices) | На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связывает хранилище Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с ADFTutorialDataFactory. Входные данные для конвейера размещены в хранилище больших двоичных объектов Azure, а выходные данные будут сохраняться в таблице в базе данных Azure SQL. Таким образом, можно добавить эти два хранилища данных как связанные службы в фабрику данных.      
[Шаг 3. Создание входных и выходных таблиц](#CreateInputAndOutputDataSets) | На предыдущем шаге были созданы связанные службы, ссылающиеся на хранилища данных, содержащие входные и выходные данные. На этом шаге будет определено две таблицы фабрики данных, **EmpTableFromBlob** и **EmpSQLTable**, которые являются представлением входных и выходных данных, сохраняемых в этих хранилищах данных. Для EmpTableFromBlob потребуется указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными для таблицы EmpSQLTable, а также таблицу SQL, в которой будут сохраняться выходные данные. Вы также укажите другие свойства, такие как структура данных, доступность данных и т. п. 
[Шаг 4. Создание и запуск конвейера](#CreateAndRunAPipeline) | На этом шаге создается конвейер с именем **ADFTutorialPipeline** в ADFTutorialDataFactory. Для конвейера будет назначено **действие копирования**, которое будет копировать входные данные большого двоичного объекта Azure в выходную таблицу SQL Azure.
[Шаг 5. Мониторинг секторов и конвейера](#MonitorDataSetsAndPipeline) | На этом шаге будут отслеживаться срезы входных и выходных таблиц при помощи портала Azure.
 

## <a name="CreateDataFactory"></a>Шаг 1. Создание фабрики данных Azure
На этом шаге с помощью портала Azure создается фабрика данных с именем **ADFTutorialDataFactory**.

1.	После входа на [портал Azure][azure-portal] щелкните **СОЗДАТЬ** в нижнем левом углу, выберите **Анализ данных** в колонке **Создать**, затем щелкните **Фабрика данных** в колонке **Анализ данных**. 

	![Создать -> Фабрика данных][image-data-factory-new-datafactory-menu]

6. В колонке **Создать фабрику данных** выполните следующие действия.
	1. Введите **ADFTutorialDataFactory** в поле **Имя**. 
	
  		![Создать колонку "Фабрика данных"][image-data-factory-getstarted-new-data-factory-blade]
	2. Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ** и выполните одно из следующих действий.
		1. Щелкните **Создать новую группу ресурсов**.
		2. В колонке **Создать новую группу ресурсов** введите **ADFTutorialResourceGroup** в поле **имени** группы ресурсов и щелкните **ОК**. 

			![Создать группу ресурсов][image-data-factory-create-resource-group]

		Некоторые действия, описанные в этом учебнике, предполагают, что для группы ресурсов используется имя **ADFTutorialResourceGroup**. Сведения о группах ресурсов см. в разделе [Использование групп ресурсов для управления ресурсами Azure](../resource-group-overview.md).  
7. В колонке **Создать фабрику данных** убедитесь, что выбран пункт **Добавить в начальную панель**.
8. Нажмите **Создать** в колонке **Создать фабрику данных**.

	Имя фабрики данных Azure должно быть глобально уникальным. При возникновении ошибки **Имя фабрики данных ADFTutorialDataFactory недоступно** измените имя этой фабрики данных (например, на вашеимяADFTutorialDataFactory) и попробуйте создать ее снова. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
	 
	![Имя фабрики данных недоступно][image-data-factory-name-not-available]
	
	> [AZURE.NOTE]В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

9. Щелкните концентратор **УВЕДОМЛЕНИЯ** слева и просмотрите уведомления процесса создания. Щелкните **X**, чтобы закрыть колонку **УВЕДОМЛЕНИЯ**, если она открыта.
10. Затем вы увидите колонку **ФАБРИКА ДАННЫХ**, как показано ниже.

    ![Домашняя страница фабрики данных][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Шаг 2. Создание связанных служб
Связанные службы связывают хранилища данных или вычислительные службы с фабрикой данных Azure. Данные могут храниться в хранилище Azure, в базе данных SQL Azure или в локальной базе данных SQL Server.

На этом шаге создаются две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. Связанная служба StorageLinkedService связывает учетную запись хранения Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с **ADFTutorialDataFactory**. Далее в этом учебнике будет создан конвейер, который копирует данные из контейнера больших двоичных объектов в StorageLinkedService в таблицу SQL в AzureSqlLinkedService.

### Создание связанной службы для учетной записи хранилища Azure
1.	В колонке **ФАБРИКА ДАННЫХ** щелкните плитку **Разработка и развертывание**, чтобы запустить **редактор** для фабрики данных.

	![Плитка "Создание и развертывание"][image-author-deploy-tile]

	Подробный обзор редактора фабрики данных см. в разделе [Редактор фабрики данных][data-factory-editor].
	 
5. В **Редакторе** нажмите кнопку **Создать хранилище данных** на панели инструментов и выберите в раскрывающемся меню пункт **Хранилище Azure**. На панели справа должен появиться шаблон JSON для создания связанной службы хранилища Azure.

	![Кнопка "Создать хранилище данных" в редакторе][image-editor-newdatastore-button]
    
6. Замените **accountname** и **accountkey** значениями имени и ключа учетной записи для вашей учетной записи Azure.

	![Хранилище больших двоичных объектов JSON в редакторе][image-editor-blob-storage-json]
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Нажмите кнопку **Развернуть** на панели инструментов, чтобы развернуть StorageLinkedService. Убедитесь, что в строке заголовка отображается сообщение **СВЯЗАННАЯ СЛУЖБА УСПЕШНО СОЗДАНА**.

	![Развертывание хранилища больших двоичных объектов в редакторе][image-editor-blob-storage-deploy]

### Создание связанной службы для базы данных SQL Azure.
1. В **редакторе фабрики данных** нажмите кнопку **Создать хранилище данных** на панели инструментов и выберите **База данных SQL Azure** в раскрывающемся меню. В правой панели должен появиться шаблон JSON для создания связанной службы SQL Azure.

	![Параметры редактора SQL Azure][image-editor-azure-sql-settings]

2. Замените **servername**, **databasename**, ****username@servername** и **password** на имя вашего сервера Azure SQL Server, имя базы данных, имя учетной записи пользователя и пароль.
3. Щелкните **Развернуть** на панели инструментов, чтобы создать и развернуть AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3. Создание входных и выходных таблиц
На предыдущем шаге были созданы связанные службы **StorageLinkedService** и **AzureSqlLinkedService** для связи учетной записи хранения Azure и базы данных SQL Azure с фабрикой данных **ADFTutorialDataFactory**. На этом шаге будет определено две таблицы фабрики данных, **EmpTableFromBlob** и **EmpSQLTable**, представляющие входные и выходные данные, которые сохраняются в хранилищах данных, на которые ссылаются службы StorageLinkedService и AzureSqlLinkedService соответственно. Для EmpTableFromBlob потребуется указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными для таблицы EmpSQLTable, а также таблицу SQL, в которой будут сохраняться выходные данные.

### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable**, указывающая на контейнер больших двоичных объектов в хранилище Azure, представленном связанной службой **StorageLinkedService**.

1. В **редакторе** для фабрики данных нажмите кнопку **Создать набор данных** на панели инструментов и щелкните **Таблица больших двоичных объектов** в раскрывающемся меню. 
2. Замените JSON на правой панели следующим фрагментом JSON: 

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

		
     Обратите внимание на следующее.
	
	- для параметра **type** набора данных задано значение **AzureBlob**;
	- для **LinkedServiceName** задано значение **StorageLinkedService**. Эта связанная служба была создана на шаге 2.
	- В качестве значения **FolderPath** установлен контейнер **adftutorial**. Вы также можете указать имя большого двоичного объекта внутри папки. Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- Для **типа** формата установлено значение **TextFormat**
	- В этом текстовом файле имеются два поля, **FirstName** и **LastName**, которые разделяются запятой (**columnDelimiter**).	
	- Параметр **availability** имеет значение **hourly** (**frequency** имеет значение **hour**, а **interval** имеет значение **1**), что означает, что служба фабрики данных будет каждый час искать входные данные в корневой папке заданного вами контейнера больших двоичных объектов (**adftutorial**). 
	

	Если параметр **fileName** для **входной** **таблицы** не задан, все файлы и большие двоичные объекты из входной папки (**folderPath**) считаются входными данными. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Примеры файлов см. в [учебнике][adf-tutorial].
 
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

	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2. Щелкните **Развернуть** на панели инструментов, чтобы создать и развернуть таблицу **EmpTableFromBlob**. Убедитесь, что на панели заголовка редактора отображается сообщение **ТАБЛИЦА УСПЕШНО СОЗДАНА**.

### Создание выходной таблицы
В этой части шага создается выходная таблица с именем **EmpSQLTable**, которая указывает на SQL базы данных SQL Azure, представленную связанной службой **AzureSqlLinkedService**.

1. В **редакторе** для фабрики данных нажмите кнопку **Создать набор данных** на панели инструментов и щелкните **Таблица SQL Azure** в раскрывающемся меню. 
2. Замените JSON на правой панели следующим фрагментом JSON:

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
	* **LinkedServiceName** имеет значение **AzureSqlLinkedService** (эта связанная служба была создана на шаге 2);
	* **Tablename** имеет значение **emp**.
	* В таблице emp базы данных имеются три столбца: **ID**, **FirstName** и **LastName**, но ID — это столбец идентификаторов, поэтому здесь необходимо задать только **FirstName** и **LastName**.
	* Параметр **availability** имеет значение **hourly**, при этом **frequency** получает значение **hour**, а **interval** получает значение **1**. Служба фабрики данных будет создавать срез выходных данных каждый час в таблице **emp** в базе данных SQL Azure.


3. Щелкните **Развернуть** на панели инструментов для создания и развертывания таблицы **EmpSQLTable**.


## <a name="CreateAndRunAPipeline"></a>Шаг 4. Создание и запуск конвейера
На этом шаге создается конвейер с **действием копирования**, которое использует **EmpTableFromBlob** в качестве входных данных и **EmpSQLTable** в качестве выходных.

1. В **редакторе** фабрики данных нажмите кнопку **Создать конвейер** на панели инструментов. Нажмите **... (многоточие)** на панели инструментов, если кнопка не отображается. Кроме того, можно щелкнуть правой кнопкой мыши **Конвейеры** в древовидном представлении и выбрать **Создать конвейер**.

	![Кнопка редактора "Создать конвейер"][image-editor-newpipeline-button]
 
2. Замените JSON на правой панели следующим фрагментом JSON:
		
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
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	Обратите внимание на следующее:

	- В разделе действий доступно только одно действие, **тип** которого имеет значение **CopyActivity**.
	- Для входных данных действия задано значение **EmpTableFromBlob**, а для выходных данных — **EmpSQLTable**.
	- В разделе **transformation** в качестве типа источника указан **BlobSource**, а в качестве типа приемника указан **SqlSink**.

	Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Можно указать только часть даты и пропустить временную часть указанной даты и времени. Например, "2015-02-03", что эквивалентно "2015-02-03T00:00:00Z"
	
	Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Время **окончания** указывать не обязательно, однако в этом примере мы будем его использовать.
	
	Если не указать значение свойства **end**, оно вычисляется по формуле «**время начала + 48 часов**». Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9999-09-09** в качестве значения свойства **окончание**.
	
	В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Щелкните **Развернуть** на панели инструментов для создания и развертывания конвейера **ADFTutorialPipeline**. Убедитесь, что отображается сообщение **КОНВЕЙЕР УСПЕШНО СОЗДАН**.
5. Теперь закройте колонку **Редактор**, щелкнув **X**. Щелкните **X** снова, чтобы закрыть колонку ADFTutorialDataFactory с представлением панели инструментов и дерева. При отображении сообщения **Несохраненные редакторы будут отклонены** щелкните **OK**.
6. После этого следует вернуться в колонку **ФАБРИКА ДАННЫХ** для фабрики **ADFTutorialDataFactory**.

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.
 
### Просмотр фабрики данных в представлении схемы 
1. В колонке **ФАБРИКА ДАННЫХ** щелкните **Схема**.

	![Колонка "Фабрика данных" — плитка схемы][image-datafactoryblade-diagramtile]

2. Вы должны увидеть схему, аналогичную приведенной ниже:

	![Представление схемы][image-data-factory-get-started-diagram-blade]

	Можно увеличивать и уменьшать масштаб, выбирать 100%-й масштаб или масштаб по размеру, автоматически размещать конвейеры и таблицы, а также отображать сведения из журнала обращений и преобразований (выделение восходящих и нисходящих элементов для выбранных элементов). Дважды щелкните объект (входную или выходную таблицу или конвейер), чтобы просмотреть его свойства. 
3. Щелкните правой кнопкой мыши конвейер **ADFTutorialPipeline** в представлении схемы и щелкните элемент **Открыть конвейер**. Будут отображены действия в конвейере вместе с входными и выходными наборами данных для этих действий. В этом учебнике представлено только одной действие в конвейере (действие копирования) с использованием объекта EmpTableBlob в качестве входного набора данных и EmpSQLTable в качестве выходного набора данных.   

	![Открыть конвейер](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Щелкните **Фабрика данных** в строке навигации в верхнем левом углу, чтобы перейти к представлению схемы. В представлении схемы отображаются все конвейеры. В этом примере создан только один конвейер.
 

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5. Мониторинг наборов данных и конвейера
На этом шаге вы будете использовать классический портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Подробные сведения об использовании командлетов для мониторинга см. в разделе [Мониторинг фабрики данных и управление ею с помощью командлетов PowerShell][monitor-manage-using-powershell].

1. Перейдите на [классический портал Azure (предварительной версии)][azure-portal], если он еще не открыт. 
2. Если колонка **ADFTutorialDataFactory** закрыта, откройте ее, щелкнув **ADFTutorialDataFactory** на **начальной панели**. 
3. Вы увидите количество и имена таблиц, а также конвейер, который вы создали в этой колонке.

	![домашняя страница с именами][image-data-factory-get-started-home-page-pipeline-tables]

4. Теперь щелкните плитку **Наборы данных**.
5. В колонке **Наборы данных** щелкните **EmpTableFromBlob**. Это входная таблица для **ADFTutorialPipeline**.

	![Наборы данных с выбранным объектом EmpTableFromBlob][image-data-factory-get-started-datasets-emptable-selected]   
5. Обратите внимание, что срезы данных до настоящего момента уже произведены и находятся в состоянии **Готов**, поскольку файл **emp.txt** все это время существует в контейнере больших двоичных объектов **adftutorial\input**. Убедитесь, что срезы не отображаются в разделе **Срезы, в которых недавно произошел сбой** в нижней части.

	Оба списка, **Недавно обновленные срезы** и **Срезы, в которых недавно произошел сбой**, сортируются по **ПОСЛЕДНЕМУ ВРЕМЕНИ ОБНОВЛЕНИЯ**. Время обновления среза изменяется в таких ситуациях:
    

	-  Вы обновляете состояние среза вручную, например используя командлет **Set-AzureRmDataFactorySliceStatus** или щелкнув **ВЫПОЛНИТЬ** в колонке **СРЕЗ** для этого среза.
	-  В ходе выполнения состояние среза меняется (например, выполнение началось, выполнение завершилось со сбоем, выполнение завершилось успешно и т. п.).
 
	Щелкните заголовок списков или **... (многоточие)**, чтобы просмотреть расширенный список срезов. Чтобы отфильтровать срезы, выберите пункт **Фильтр** на панели инструментов.
	
	Чтобы вместо этого просмотреть срезы данных, отсортированные по времени начала и окончания среза, щелкните плитку **Срезы данных (по времени среза)**.

	![Срезы данных по времени среза][DataSlicesBySliceTime]

6. Затем в колонке **Наборы данных** щелкните **EmpSQLTable**. Это выходная таблица для **ADFTutorialPipeline**.

	![Колонка "Наборы данных"][image-data-factory-get-started-datasets-blade]



	 
6. Колонка **EmpSQLTable** должна иметь следующий вид:

	![Колонка "Таблица"][image-data-factory-get-started-table-blade]
 
7. Обратите внимание, что срезы данных до текущего момента времени уже выполнены и все они находятся в состоянии **Готов**. В разделе **Проблемные срезы** в нижней части окна срезов нет.
8. Нажмите кнопку **… (многоточие)**, чтобы отобразить все срезы.

	![Колонка срезов данных][image-data-factory-get-started-dataslices-blade]

9. Щелкните любой срез данных в списке для отображения колонки **СРЕЗ ДАННЫХ**.

	![Колонка среза данных][image-data-factory-get-started-dataslice-blade]
  
	Если срез не находится в состоянии **Готов**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**.

11. В колонке **СРЕЗ ДАННЫХ** в списке в нижней части окна отображаются все выполненные действия. Щелкните **выполняемое действие**, чтобы просмотреть колонку **ПОДРОБНАЯ ИНФОРМАЦИЯ О ВЫПОЛНЯЕМОМ ДЕЙСТВИИ**.

	![Сведения о выполнении действия][image-data-factory-get-started-activity-run-details]

	
12. С помощью кнопки **X** закройте все колонки, чтобы вернуться к начальной колонке **ADFTutorialDataFactory**.
14. (Необязательно.) Щелкните **Конвейеры** на домашней странице фабрики данных **ADFTutorialDataFactory**, щелкните **ADFTutorialPipeline** в колонке **Конвейеры** и изучите входные таблицы (**Использованные**) или выходные таблицы (**Созданные**).
15. Запустите **SQL Server Management Studio**, подключитесь к базе данных SQL Azure и убедитесь, что строки вставляются в таблицу **emp** в базе данных.

	![результаты SQL-запроса][image-data-factory-get-started-sql-query-results]


## Сводка 
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали портал Azure для создания фабрики данных, связанных служб, таблиц и конвейера. Основные действия, которые вы выполнили в этом учебнике

1.	Создание **фабрики данных** Azure.
2.	Создание **связанных служб** для связи хранилищ данных и вычислительных служб (**связанных служб**) с фабрикой данных.
3.	Создание **таблиц**, описывающих входные и выходные данные для конвейеров.
4.	Создание **конвейеров**. Конвейер состоит из одного или нескольких действий, обрабатывает входные данные и создает выходные данные. Установите активный период для конвейера, указав время **запуска** и **завершения** работы конвейера. Период активности определяет период времени, в течение которого будут производиться срезы данных. 


Чтобы отобразить список поддерживаемых действий, см. тему [Конвейеры и действия][msdn-activities], а чтобы отобразить список поддерживаемых связанных служб, см. тему [Связанные службы][msdn-linkedservices] в библиотеке MSDN.
 
Чтобы выполнить задания этого учебника при помощи Azure PowerShell, см. раздел [Создание и отслеживание фабрики данных при помощи Azure PowerShell][monitor-manage-using-powershell].


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 

<!---HONumber=AcomDC_0107_2016-->