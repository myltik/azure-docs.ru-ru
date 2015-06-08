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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Учебник: Создание и отслеживание фабрика данных, с помощью фабрики редактора данных
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##Вот какие темы рассматривает этот учебник:
Этот учебник содержит следующие действия:

Шаг | Описание
-----| -----------
[Шаг 1: Создание фабрики данных Azure](#CreateDataFactory) | На этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactory**.  
[Шаг 2: Создание связанные службы](#CreateLinkedServices) | На этом шаге вы создадите две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связывает хранилища Azure и ADFTutorialDataFactory AzureSqlLinkedService связывает базу данных Azure SQL. Входные данные для конвейера находится в контейнере больших двоичных объектов в хранилище больших двоичных объектов и выходные данные будут храниться в таблице в базе данных Azure SQL. Таким образом можно добавить эти два хранилища данных как связанные службы фабрика данных.      
[Шаг 3: Создания входных и выходных таблиц](#CreateInputAndOutputDataSets) | На предыдущем шаге вы создали связанные службы, относящиеся к хранилищам данных, содержащих данные ввода вывода. На этом шаге вы создадите двух таблиц данных фабрики-- **EmpTableFromBlob** и **EmpSQLTable** --, представляющие данные ввода вывода, которые хранятся в хранилищах данных. Необходимо указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными, а также для EmpSQLTable EmpTableFromBlob, нужно будет указать таблице SQL, в которой будут храниться выходные данные. Необходимо также указать другие свойства, такие как структуры данных, доступность данных и т. д... 
[Шаг 4: Создание и запуск конвейера](#CreateAndRunAPipeline) | На этом шаге вы создадите конвейера с именем **ADFTutorialPipeline** в ADFTutorialDataFactory. Конвейер будет иметь **Действие копирования** копирует входные данные из Azure — BLOB-объектов в выходной таблице Azure SQL.
[Шаг 5: Мониторинг фрагменты и конвейера](#MonitorDataSetsAndPipeline) | На этом шаге будет следить за фрагменты входных и выходных таблиц с помощью предварительной версии портала Azure.
 

## <a name="CreateDataFactory"></a>Шаг 1: Создание фабрики данных Azure
На этом этапе предварительной версии портала Azure используется для создания фабрики данных Azure с именем **ADFTutorialDataFactory**.

1.	После входа в [предварительной версии портала Azure][azure-preview-portal], щелкните **Создать** в левом нижнем углу, выберите **анализа данных** в **Создать** стоечный модуль и нажмите кнопку **фабрика данных** в **анализа данных** выноски. 

	![New -> DataFactory][image-data-factory-new-datafactory-menu]

6. В **новую фабрику данных** выноску:
	1. Введите **ADFTutorialDataFactory** для **имя**. 
	
  		![Новая выноска фабрика данных][image-data-factory-getstarted-new-data-factory-blade]
	2. Щелкните **имя группы РЕСУРСОВ** и выполните следующие действия:
		1. Щелкните **Создать новую группу ресурсов**.
		2. В **Создать группу ресурсов** выноску, введите **ADFTutorialResourceGroup** для **имя** группы ресурсов и нажмите кнопку **ОК**. 

			![Создать группу ресурсов][image-data-factory-create-resource-group]

		Некоторые действия, описанные в этом учебнике предполагается, что используется имя: **ADFTutorialResourceGroup** для группы ресурсов. Дополнительные сведения о группах ресурсов см. в разделе [Использование групп ресурсов для управления ресурсами Azure](resource-group-overview.md).  
7. В **новую фабрику данных** выноску, обратите внимание, что **Добавить на начальную панель** выбран.
8. Щелкните **Создать** в **новую фабрику данных** выноски.

	> [AZURE.NOTE]Имя фабрики данных Azure должно быть глобально уникальным. При возникновении ошибки: **данных фабрики имя «ADFTutorialDataFactory» недоступно**, измените имя фабрики данных (например, yournameADFTutorialDataFactory) и попробуйте снова. Используйте это имя вместо ADFTutorialFactory при выполнении оставшихся шагов в этом учебнике. В разделе [фабрика данных — правила именования][data-factory-naming-rules] раздел по правилам именования для артефактов, фабрика данных.
	 
	![Имя фабрики данных не доступен][image-data-factory-name-not-available]

9. Щелкните **уведомления** концентратора слева, затем найдите уведомления от процесса создания. Щелкните **X** закрыть **уведомления** выноску, если он открыт.
10. По завершении создания вы увидите **ФАБРИКА данных** выноску, как показано ниже.

    ![Домашняя страница данных фабрики][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>Шаг 2: Создание связанные службы
Связанные службы связывают хранилища данных или вычислительные службы с фабрикой данных Azure. Данные могут храниться в хранилище Azure, в базе данных SQL Azure или в локальной базе данных SQL Server.

На этом шаге вы создадите две связанные службы: **StorageLinkedService** и **AzureSqlLinkedService**. StorageLinkedService связанные ссылки службы учетную запись хранилища Azure и AzureSqlLinkedService связывает базу данных Azure SQL для **ADFTutorialDataFactory**. Далее в этом учебнике, копирующего данные из контейнера BLOB-объектов в StorageLinkedService в таблицу SQL AzureSqlLinkedService будет создать конвейер.

### Создание связанной службы для учетной записи хранилища Azure
1.	В **ФАБРИКА данных** стоечный модуль, щелкните **автора и развертывание** плитки для запуска **редактор** для фабрики данных.

	![Плитка «Создание и развертывание»][image-author-deploy-tile]

	> [AZURE.NOTE]В разделе [данных фабрики редактора][data-factory-editor] разделе подробный обзор данных фабрики редактора.
	 
5. В **редактор**, щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **хранилища Azure** из раскрывающегося меню. Вы увидите JSON шаблон для создания службы хранилища Azure связаны в правой области.

	![Редактор новой кнопки хранилища данных][image-editor-newdatastore-button]
    
6. Замените **accountname** и **accountkey** с именем учетной записи и учетной записи значения ключа для вашей учетной записи хранилища Azure.

	![Хранилище BLOB-объектов редактора JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.

6. Щелкните **Развернуть** на панели инструментов, чтобы развернуть StorageLinkedService. Убедитесь, что сообщение **СВЯЗАННЫЕ службы СОЗДАН успешно** в строке заголовка.

	![Развертывание редактор хранилища BLOB-объектов][image-editor-blob-storage-deploy]

### Создание связанной службы для базы данных SQL Azure
1. В **данных фабрики редактора** , щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **базы данных Azure SQL** из раскрывающегося меню. Вы увидите JSON шаблон для создания службы SQL Azure связаны в правой области.

	![Параметры SQL Editr Azure][image-editor-azure-sql-settings]

2. Замените **имя_сервера**, **databasename**, **username@servername**, и **пароль** имена сервера, базы данных, учетная запись пользователя и пароль Azure SQL. 
3. Щелкните **Развернуть** на панели инструментов для создания и развертывания AzureSqlLinkedService. 
   

## <a name="CreateInputAndOutputDataSets"></a>Шаг 3: Создания входных и выходных таблиц
На предыдущем шаге вы создали связанные службы **StorageLinkedService** и **AzureSqlLinkedService** связать учетную запись хранилища Azure и базы данных Azure SQL в фабрику данных: **ADFTutorialDataFactory**. На этом шаге вы создадите двух таблиц данных фабрики-- **EmpTableFromBlob** и **EmpSQLTable** --, представляющие данные ввода вывода, которые хранятся в хранилищах данных, на которую ссылается StorageLinkedService и AzureSqlLinkedService соответственно. EmpTableFromBlob необходимо указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными, а также для EmpSQLTable, нужно будет указать таблице SQL, в которой будут храниться выходные данные.

### Создание входной таблицы 
Таблица представляет собой прямоугольный набор данных, который имеет схему. На этом шаге создается таблица с именем **EmpBlobTable** указывающего на контейнер больших двоичных объектов в хранилище Azure, представленного **StorageLinkedService** связанные службы.

1. В **редактор** фабрику данных щелкните **новый набор данных** кнопки на панели инструментов и нажмите кнопку **большой двоичный объект таблицы** из раскрывающегося меню. 
2. Замените следующий фрагмент JSON JSON в правой области. 

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
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     Обратите внимание на следующее:
	
	- расположение **тип** равен **AzureBlobLocation**.
	- **linkedServiceName** равен **StorageLinkedService**. Этот связанный службы был создан на шаге 2.
	- **folderPath** равен **adftutorial** контейнера. Вы также можете указать имя большого двоичного объекта внутри папки. Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.  
	- формат **тип** равен **TextFormat**
	- Существует два поля в текстовом файле — **FirstName** и **LastName** —, разделенных запятой (** columnDelimiter **)	
	-  **Доступности** равен **каждый час** (** частоты ** равен **час** и **интервал** имеет значение **1** ), поэтому служба фабрики данных будет искать входных данных каждый час в корневой папке в контейнер больших двоичных объектов (** adftutorial **) было задано. 
	

	Если не указать **fileName** для **ввода** **таблицы**, все файлы и большие двоичные объекты из входной папки (** folderPath **) рассматриваются в качестве входных данных. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Образцы файлов в разделе [учебника][adf-tutorial] примеры.
 
	Если вы не укажете **fileName** для **выходную таблицу**, созданных файлов в **folderPath** именами в следующем формате: данные. & lt; Идентификатор GUID & gt;. txt (пример: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2. Щелкните **Развернуть** на панели инструментов для создания и развертывания **EmpTableFromBlob** таблицы. Убедитесь, что **Таблица СОЗДАНА успешно** сообщения в заголовке окна редактора.

### Создание выходной таблицы
В этой части шага вы создадите выходную таблицу с именем **EmpSQLTable** точек в таблицу SQL в Azure SQL базы данных, представленного **AzureSqlLinkedService** связанные службы.

1. В **редактор** фабрику данных щелкните **новый набор данных** кнопки на панели инструментов и нажмите кнопку **Azure SQL table** из раскрывающегося меню. 
2. Замените следующий фрагмент JSON JSON в правой области.

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
            		"type": "AzureSqlTableLocation",
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
	* **linkedServiceName** равен **AzureSqlLinkedService** (на шаге 2 был создан этот связанный службы).
	* **tablename** равен **emp**.
	* Имеются три столбца — **идентификатор**, **FirstName**, и **LastName** — в таблице emp в базе данных, но идентификатор является столбцом идентификаторов, поэтому необходимо указать только **FirstName** и **LastName** здесь.
	*  **Доступности** равен **каждый час** (** частоты ** значение **час** и **интервал** значение **1**). Служба фабрики данных создает фрагмент выходных данных каждый час в **emp** таблицы в базе данных Azure SQL.


3. Щелкните **Развернуть** на панели инструментов для создания и развертывания **EmpSQLTable** таблицы.


## <a name="CreateAndRunAPipeline"></a>Шаг 4: Создание и запуск конвейера
На этом этапе можно создать конвейер с **Действие копирования** использующий **EmpTableFromBlob** в качестве входного и **EmpSQLTable** как output.

1. В **редактор** фабрику данных щелкните **новому** на панели инструментов. Нажмите **... (многоточие)** на панели инструментов, если кнопка не отображается. Кроме того, можно щелкнуть правой кнопкой мыши **Конвейеры** в древовидном представлении и выбрать **Создать конвейер**.

	![Редактор новой кнопки конвейера][image-editor-newpipeline-button]
 
2. Замените следующий фрагмент JSON JSON в правой области.

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	Обратите внимание на следующее:

	- В разделе «действия» имеется только одно действие, **тип** равен **CopyActivity**.
	- Ввод действия задается **EmpTableFromBlob** и вывода задается действие **EmpSQLTable**.
	- В **преобразования** разделе **BlobSource** указан в качестве типа источника и **SqlSink** указан как тип приемника.

	> [AZURE.NOTE]Замените значение **Запуск** свойство с текущего дня и **конец** значение со следующего дня. Можно указать только часть даты и пропустить временной части даты-времени. Например, «2015-02-03», который эквивалентен "2015-02-03T00:00:00Z" и запустите и даты окончания должны находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z.  **Конец** время необязательно, но мы будем использовать в этом учебнике. Если не указать значение для **конец** свойство, оно вычисляется как "** start + 48 часов **». Укажите на бесконечное выполнение конвейера **9999-09-09** как значение **end** свойство. В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
	
	> [AZURE.NOTE]В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.

4. Щелкните **Развернуть** на панели инструментов для создания и развертывания **ADFTutorialPipeline**. Убедитесь, что **КОНВЕЙЕРА успешно СОЗДАН** сообщение.
5. Теперь закройте **редактор** выноску, щелкнув **X**. Щелкните **X** чтобы закрыть выноске ADFTutorialDataFactory с представлением инструментов и дерева. Если вы видите **несохраненные изменения будут потеряны** сообщений, нажмите кнопку **ОК**.
6. Должен быть обратно в **ФАБРИКА данных** выноски для **ADFTutorialDataFactory**.

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.
 
### Просмотр фабрика данных в представлении диаграммы 
1. В **ФАБРИКА данных** стоечный модуль, щелкните **Схема**.

	![Выноска данных фабрики - диаграмма плитка][image-datafactoryblade-diagramtile]

2. Вы должны увидеть схему, аналогичную приведенной ниже:

	![Представление диаграммы][image-data-factory-get-started-diagram-blade]

	Можно увеличить, уменьшить масштаб, масштаб 100%, масштаб, размеру, автоматически размещения конвейеры и таблицы и отобразить сведения журнала обращений и преобразований (выделяет восходящие и нисходящие элементов, выбранных элементов). Вы можете double-blick объекта (таблицы ввода вывода или конвейера) для просмотра его свойств. 
3. Щелкните правой кнопкой мыши **ADFTutorialPipeline** в представлении диаграммы и нажмите кнопку **Откройте конвейера**. Вы увидите действий в конвейере и наборы данных ввода-вывода для действий. В этом учебнике имеется только одно действие в конвейере (действие копирования) с EmpTableBlob входного набора данных и EmpSQLTable как результирующий набор данных.   

	![Откройте конвейера](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. Щелкните **фабрика данных** в строке навигации в верхнем левом углу, чтобы вернуться в представление диаграммы. Представление диаграммы отображает все каналы. В этом примере вы создали только один конвейер.
 

## <a name="MonitorDataSetsAndPipeline"></a>Шаг 5: Мониторинг наборы данных и конвейера
В этом шаге вы будете использовать портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Дополнительные сведения об использовании командлетов для мониторинга в разделе [монитора и управлять фабрика данных с помощью командлетов PowerShell][monitor-manage-using-powershell].

1. Перейдите к [портала Azure (Предварительная версия)][azure-preview-portal] при отсутствии его открыть. 
2. Если выноски для **ADFTutorialDataFactory** не открыта, откройте ее, щелкнув **ADFTutorialDataFactory** на **начальной**. 
3. Вы увидите количество и имена таблиц, а также конвейер, который вы создали в этой колонке.

	![Домашняя страница с именами][image-data-factory-get-started-home-page-pipeline-tables]

4. Теперь щелкните **наборы данных** плитки.
5. В **наборы данных** стоечный модуль, щелкните **EmpTableFromBlob**. Это входная таблица для **ADFTutorialPipeline**.

	![Наборы данных с выбранной EmpTableFromBlob][image-data-factory-get-started-datasets-emptable-selected]   
5. Обратите внимание, что уже было произведено срезы данных до текущего времени и их **готов** из-за **emp.txt** файл существует постоянно в контейнер больших двоичных объектов: **adftutorial\input**. Убедитесь, что срезы не будут отображаться в **недавно произошел сбой фрагменты** в нижней.

	Оба **недавно обновлены фрагменты** и **недавно произошел сбой фрагменты** списки сортируются по **время ПОСЛЕДНЕГО обновления**. Время обновления фрагмента изменяется в следующих ситуациях.
    

	-  Обновить состояние срез вручную, например, с помощью **набора AzureDataFactorySliceStatus** (или), щелкнув **ЗАПУСКА** на **СРЕЗ** выноски для диаграммы.
	-  Срез изменяет состояние из-за выполнения (например выполнения работы, запуска завершается, а не удалось, запуска завершено и выполнена успешно, и т. д).
 
	Щелкните заголовок списки или **... (многоточие)** Чтобы просмотреть список больших фрагментов. Щелкните **фильтра** на панели инструментов для фильтрации фрагментов.
	
	Чтобы просмотреть срезы данных, отсортированных по времени начала и окончания фрагмента, вместо этого, щелкните **срезы данных (по времени срез)** плитки.

	![Срезы данных по времени среза][DataSlicesBySliceTime]

6. Теперь в **наборы данных** стоечный модуль, щелкните **EmpSQLTable**. Это выходной таблицы для **ADFTutorialPipeline**.

	![Выноска наборов данных][image-data-factory-get-started-datasets-blade]



	 
6. Вы увидите **EmpSQLTable** выноску, как показано ниже:

	![Выноска таблицы][image-data-factory-get-started-table-blade]
 
7. Обратите внимание, что уже было произведено срезы данных до текущего времени и их **готов**. Срезы не отображаются в **фрагменты проблемы** в нижней.
8. Щелкните **... (Многоточие)** для просмотра всех фрагментов.

	![Выноска срезы данных][image-data-factory-get-started-dataslices-blade]

9. Щелкните любой сегмент данных из списка, и вы увидите **СРЕЗ данных** выноски.

	![Выноска срез данных][image-data-factory-get-started-dataslice-blade]
  
	Если фрагмент не находится в **готов** состояние, можно увидеть вышестоящего срезы, которые еще не готовы и блокируют текущий фрагмент выполнение в **вышестоящего фрагменты, которые не готовы** списка.

11. В **СРЕЗ данных** выноску, вы увидите выполняет все действия в списке в нижней. Щелкните **Действие запуска** для просмотра **сведения о выполнении действия** выноски.

	![Сведения о выполнении действия][image-data-factory-get-started-activity-run-details]

	
12. Щелкните **X** закрыть все модули, пока не вернетесь на выноске home для **ADFTutorialDataFactory**.
14. (необязательно) Щелкните **конвейеры** на домашней странице для **ADFTutorialDataFactory**, щелкните **ADFTutorialPipeline** в **конвейеры** выноску и детализация входные таблицы (** израсходовано **) или выходных таблицах (** создана **).
15. Запустите **SQL Server Management Studio**, подключения к базе данных SQL Azure и убедитесь, что строки вставляются в **emp** таблицы в базе данных.

	![результаты запроса SQL][image-data-factory-get-started-sql-query-results]


## Сводка 
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Портал предварительной версии Azure используется для создания фабрики данные, связанные службы, таблиц и конвейера. Основные действия, которые вы выполнили в этом учебнике

1.	Создание Azure **фабрика данных**.
2.	Создание **связанные службы** связывающие хранилищ данных и вычисляет (называемый **связанные службы**) к производству данных.
3.	Создание **таблиц** описывающие входных данных и выходные данные для конвейера.
4.	Создание **конвейеры**. Конвейер состоит из одного или нескольких действий, обрабатывает входные данные и создает выходные данные. Задать срок для конвейера с помощью **Запуск** время и **End** время для конвейера. Период активности определяет период времени, в течение которого будут производиться срезы данных. 


> [AZURE.NOTE]Список поддерживаемых действий см. в разделе [конвейеры и действия][msdn-activities] раздел и список поддерживаемых связанные службы см. в разделе [связанные службы][msdn-linkedservices] раздела в библиотеке MSDN.
> 
> Для прохождения данного учебника, с помощью Azure PowerShell, в разделе [монитор фабрику данных с помощью Azure PowerShell и создать][monitor-manage-using-powershell].

## Дальнейшие действия

Статья | Описание
------ | ---------------
[Копирование данных с фабрикой данных Azure - действие копирования][copy-activity] | В этой статье содержит подробное описание **Действие копирования** используемые в этом учебнике. 
[Включить конвейеры для работы с локальными данными][use-onpremises-datasources] | Данная статья содержит пошаговое руководство, которое показано, как скопировать данные из **локальной базы данных SQL Server** в BLOB-объект Azure. 
[Учебник: Перемещение и обрабатывать файлы журнала, с помощью фабрики данных][adf-tutorial] | В этой статье приведены **в сквозном примере** показано, как реализовать **реалистичном** используется фабрика данных Azure для преобразования данных из файлов журнала в дополнительная информация.
[Устранение неполадок фабрика данных][troubleshoot] | В этой статье описывается как **Устранение** выдает фабрика данных Azure. Вы можете использовать руководство из этой статьи на учебной фабрике данных ADFTutorialDataFactory, намеренно создав ошибки (удалив таблицу в базе данных SQL Azure). 
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика имеется содержимое полный Справочник командлетов, скрипта JSON, функции и т. д... 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

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

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir-->