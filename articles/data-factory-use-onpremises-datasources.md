<properties title="Enable your pipelines to work with on-premises data" pageTitle="Использование конвейера для работы с локальными данными | Фабрика данных Azure" description="Learn how to register an on-premises data source with an Azure data factory and copy data to/from the data source." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Использование конвейера для работы с локальными данными

Чтобы использовать конвейеры в фабрике данных Azure для работы с локальным источником данных, его необходимо добавить в качестве связанной службы в фабрику данных с помощью портала управления Azure или службы Azure PowerShell.
 
Чтобы добавить локальный источник данных в качестве связанной службы в фабрику данных, сначала вам необходимо скачать и установить шлюз управления данными Microsoft на локальный компьютер и настроить связанную службу для локального источника данных, чтобы использовать шлюз.
 
> [WACOM.NOTE] Сейчас в качестве локального источника данных поддерживается только SQL Server.

## <a href="DMG"></a> Шлюз управления данными

**Шлюз управления данными** - это программное обеспечение, которое обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Шлюз управления данными предоставляет перечисленные ниже возможности.

- **Подключение к данным в локальной среде для получения гибридного доступа к данным** - вы можете подключать локальные данные к облачным службам, чтобы пользоваться их преимуществами, и при этом продолжать использовать локальные данные в работе организации.
- **Определение безопасного прокси-сервера для данных** - вы можете определять, к каким локальным источникам данных открывается доступ при использовании шлюза управления данными, чтобы шлюз управления данными проводил проверку подлинности запросов данных из облачных служб и обеспечивал безопасность локальных источников данных.
- **Управление шлюзом для обеспечения полного контроля** - шлюз управления данными предлагает функции всеобъемлющего мониторинга и ведения журналов всех действий, обеспечивая возможности управления и контроля.
- **Эффективное перемещение данных** - передача данных осуществляется параллельно, а логика автоматического повторения обеспечивает устойчивость при сбоях подключения к сети.


В шлюзе управления данными предусмотрен полный набор функциональных возможностей для подключения локальных данных.

- **Отсутствие вмешательства в работу корпоративного брандмауэра** - шлюз управления данными начинает работать сразу после установки. Вам не потребуется открывать соединение брандмауэра или вносить изменения в инфраструктуру корпоративной сети.
- **Шифрование учетные данные с помощью вашего сертификата** - учетные данные, используемые для подключения к источникам данных, будут зашифрованы с помощью сертификата, который полностью принадлежит пользователю. Без сертификата никто, даже сотрудник корпорации Майкрософт, не сможет расшифровать учетные данные и преобразовать их в обычный текст.

### Рекомендации по установке шлюза

1.	Когда хост-компьютер переходит в спящий режим, шлюз не может отвечать на запросы данных. Поэтому перед установкой шлюза на компьютере следует настроить соответствующую **схему управления питанием**. 
2.	Шлюз управления данными должен подключаться к локальным источникам данных, которые будут зарегистрированы в службе фабрики данных Azure. Шлюз не должен находиться в той же машине, что и источник данных, но, **если он расположен вблизи источника данных**, это сокращает количество времени, требуемого для подключения шлюза к этому источнику.

### Особенности использования шлюза управления данными
1.	Один экземпляр шлюза управления данными можно использовать для нескольких локальных источников данных, но учтите, что **шлюз связан с фабрикой данных Azure**, и его нельзя использовать совместно с другой фабрикой данных.
2.	На компьютере может быть установлен** только один экземпляр шлюза управления данными**. Предположим, у вас есть две фабрики данных, которым необходимо получить доступ к локальным источникам данных. В этом случае вам необходимо установить шлюзы на двух локальных компьютерах, чтобы каждый шлюз был связан с отдельной фабрикой данных.
3.	Если вы уже установили шлюз на компьютер, который обслуживает сценарий **Power BI**, установите **отдельный шлюз для фабрики данных Azure** в другой машине. 
 

## Пошаговое руководство

Это руководство поможет вам создать фабрику данных с конвейером, который позволяет перемещать данные из локальной базы данных SQL Server в большой двоичный объект Azure. 

### Шаг 1. Создание фабрики данных Azure
В этом шаге вы создадите экземпляр фабрики данных Azure с именем **ADFTutorialOnPremDF**, используя портал управления Azure. Фабрику данных также можно создать с помощью командлетов фабрики данных Azure. 

1.	После входа на [портал предварительной версии Azure][
2.	azure-preview-portal] щелкните **СОЗДАТЬ** в нижнем левом углу, а затем в колонке **Создать** щелкните **Фабрика данных**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Если в колонке **Создать** вы не видите **Фабрика данных**, прокрутите колонку вниз.  
6. В колонке **Новая фабрика данных**:
	1. Введите имя **ADFTutorialOnPremDF******.
	2. Щелкните **ИМЯ ГРУППЫ РЕСУРСОВ** и выберите **ADFTutorialResourceGroup** (если вы ознакомились с учебником из статьи [Начало работы с фабрикой данных Azure][adfgetstarted]. Вы можете выбрать существующую группу ресурсов или создать новую группу. Чтобы создать новую группу ресурсов:
		1. Щелкните **Создать новую группу ресурсов**.
		2. В колонке **Создать группу ресурсов** введите **имя** для группы ресурсов и нажмите кнопку **ОК**.

7. Обратите внимание, что в колонке **Новая фабрика данных** установлен флажок **Добавить на начальную панель**.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. В колонке **Новая фабрика данных** щелкните **Создать**.
9. Обращайте внимание на уведомления, возникающие в процессе создания, в разделе **УВЕДОМЛЕНИЯ** слева.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. По завершении создания вы увидите колонку "Фабрика данных", как показано ниже.

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. Вы также должны видеть ее на **начальной панели**, как показано ниже. Щелкните ее, чтобы открыть колонку **Фабрика данных**, если она еще не открыта.

	![Startboard][image-data-factory-startboard]

### Шаг 2. Создание связанных служб 
В этом шаге вы создадите две связанные службы: **MyBlobStore** и **OnPremSqlLinkedService**. Служба **OnPremSqlLinkedService** связывается с локальной базой данных SQL Server, а связанная служба **MyBlobStore** связывает службы хранилища больших двоичных объектов Azure с **ADFTutorialDataFactory**. Далее это руководство поможет вам создать конвейер, который позволит копировать данные из локальной базы данных SQL Server в службу хранилища больших двоичных объектов Azure. 

### Добавление связанной службы в локальную базу данных SQL Server
1.	В колонке **Фабрика данных** для экземпляра **ADFTutorialOnPremDF** щелкните **Связанные службы**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	В колонке **Связанные службы** щелкните **+ Шлюз данных**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. В колонке **Создать** в поле **Имя** введите **adftutorialgateway** и нажмите кнопку **ОК**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. В колонке **Настроить** щелкните **Установить непосредственно на этот компьютер**. Это позволит скачать пакет установки для шлюза, а также установить, настроить и зарегистрировать шлюз на компьютере.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Это самый простой способ (одним щелчком) скачать, установить, настроить и зарегистрировать шлюз в один шаг. Вы увидите, что на компьютере установлено приложение **Microsoft Data Management Gateway Configuration Manager**. Вы также можете найти исполняемый файл **ConfigManager.exe** в папке по следующему пути: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	Шлюз также можно загрузить и установить вручную, используя ссылки в этой колонке. Затем вы можете зарегистрировать его с помощью ключа, показанного в текстовом поле **ЗАРЕГИСТРИРОВАТЬ С ПОМОЩЬЮ КЛЮЧА**.
	
	Дополнительную информацию о шлюзе, в том числе рекомендации и важные особенности, см. в разделе [Шлюз управления данными](#DMG). 

4. В колонке **Новый шлюз данных** нажмите кнопку **ОК**.
5. Запустите на компьютере приложение **Microsoft Data Management Gateway Configuration Manager**   .

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Подождите, пока установятся следующие значения:
	1. Если для **состояния** службы не установлено значение **Запущена**, щелкните **Запустить службу**, чтобы запустить ее, и подождите минуту, пока другие поля не обновятся.
	2. **В поле Имя шлюза** установлено значение **adftutorialgateway**.
	3. **Для имени экземпляра** установлено значение **adftutorialgateway**.
	4. **Для состояния шлюза** установлено значение **Зарегистрирован**.
	5. В строке состояния внизу отображается надпись **Установлено подключение к облачной службе шлюза управления данными** и **зеленый флажок**.  

7. Убедитесь, чтобы в колонке **Связанные службы** для **состояния** шлюза установлено значение **Хорошее**, и щелкните **+ Хранилище данных**. Возможно, чтобы обновить колонку, вам потребуется закрыть ее, а затем открыть снова. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. В колонке **Новое хранилище данных** в поле **Имя** введите **OnPremSqlLinkedService**.
9. Щелкните **ТИП (необходимо настроить)** и выберите **SQL Server**. 
10. В колонке **Новое хранилище данных** щелкните **ШЛЮЗ ДАННЫХ (настройте требуемые параметры)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Выберите ранее созданный шлюз **adftutorialgateway**. 
12.	В колонке **Новое хранилище данных** щелкните **УЧЕТНЫЕ ДАННЫЕ**, чтобы увидеть колонку **Учетные данные**.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	В колонке **Учетные данные** выберите ссылку **Щелкните здесь, чтобы безопасно задать учетные данные**. Это позволит открыть всплывающее диалоговое окно.

	![One Click application install][image-data-factory-oneclick-install]

14. Нажмите кнопку **Выполнить**, чтобы установить приложение **Credentials Manager**, и вы увидите диалоговое окно "Настройка учетных данных". 
15.	В диалоговом окне **Настройка учетных данных** введите **имя пользователя** и **пароль**, которые будут использоваться для получения доступа к локальной базе данных SQL Server в службе, а затем нажмите кнопку **ОК**. Это диалоговое окно поддерживает только **проверку подлинности SQL**. Подождите, пока закроется диалоговое окно.
16.	Нажмите кнопку **ОК** в колонке **Учетные данные**, а затем нажмите **ОК** в колонке **Новое хранилище данных**. Вы увидите, что в колонку "Связанные службы" добавлена связанная служба **OnPremSqlLinkedService**.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Добавление связанной службы для учетной записи хранения Azure

1.	В колонке **Фабрика данных** щелкните плитку **Связанные службы**, чтобы открыть колонку **Связанные службы**.
2. В колонке **Связанные службы** щелкните **Добавить хранилище данных**.	
3. В колонке **Новое хранилище данных** выполните следующиее:
	1. Введите **имя** для хранилища данных. Для целей этого учебника введите **имя** **MyBlobStore**.
	2. Введите **описание (необязательно)** для хранилища данных.
	3. Щелкните **Тип**.
	4. Выберите **Учетная запись хранения Azure** и нажмите кнопку **ОК**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. Теперь вы вернулись к плитке **Новое хранилище данных**, которая выглядит, как показано ниже:

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Введите данные в поля **Имя учетной записи** и **Ключ учетной записи** для учетной записи хранения, а затем нажмите кнопку **ОК**.

  
6. После нажатия кнопки **ОК** в колонке **Новое хранилище данных** вы увидите, что связанная служба **MyBlobStore** добавлена с список **ХРАНИЛИЩА ДАННЫХ** в колонке **Связанные службы**. Проверьте, есть ли какие-либо сообщения в разделе **УВЕДОМЛЕНИЯ**.

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Шаг 3. Создание наборов входных и выходных данных
В этом шаге вы создадите наборы входных и выходных данных, которые представляют собой входные и выходные данные для операции копирования (из локальной базы данных SQL в хранилище больших двоичных объектов Azure). Портал Azure еще не поддерживает создание наборов данных и конвейеров, поэтому для создания таблиц и конвейеров вы будете использовать командлеты Azure PowerShell. Перед созданием наборов данных или таблиц (прямоугольных наборов данных) необходимо сделать следующее (после списка есть подробные шаги):

- в базе данных SQL Server, которую вы добавили в фабрику данных как связанную службу, создайте таблицу с именем **emp** и вставьте в таблицу пару записей в качестве примера;
- - если вы не ознакомились с учебником из статьи [Начало работы с фабрикой данных Azure][adfgetstarted], создайте контейнер больших двоичных объектов с именем **adftutorial** в учетной записи службы хранилища больших двоичных объектов Azure, которую вы добавили в фабрику данных как связанную службу.

### Подготовка локальной связанной службы SQL Server для учебника

1. В базе данных SQL Server, которую вы указали для локальных связанных служб (**OnPremSqlLinkedService**), используйте следующий сценарий SQL, чтобы создать в базе данных таблицу **emp**.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. Вставьте несколько образцов в таблицу: 


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### Создание входной таблицы

1.	Создайте JSON-файл для таблицы фабрики данных, в котором содержатся данные таблицы **emp** в базе данных SQL Server. Запустите программу **Блокнот**, скопируйте в нее следующий JSON-сценарий и сохраните его как файл **EmpOnPremSQLTable.json** в папку с путем C:\ADFGetStarted\**OnPrem**. Создайте вложенную папку **OnPrem** в папке с путем **C:\ADFGetStarted**, если она еще не создана. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Обратите внимание на следующее: 
	
	- для параметра **type** для location установлено значение **OnPremisesSqlServerTableLocation**;
для параметра 	- **tableName** установлено значение **emp**;
для параметра 	- **linkedServiceName** установлено значение **OnPremSqlLinkedService** (вы создали эту связанную службу в шаге 2).
	- Другие конвейеры фабрики данных Azure не создают эти параметры для входной таблицы, поэтому вам необходимо указать раздел **waitOnExternal** в JSON-файле. Это означает, что входные данные создаются вне службы фабрики данных Azure.   

	Дополнительную информацию о свойствах JSON-сценариев см. в разделе [JSON Scripting Reference][json-script-reference] (Справочные материалы по использованию JSON-сценариев).

2. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**. Запустите модуль **Azure PowerShell** и выполните следующую команду, чтобы перейти в режим **AzureResourceManager**.     

        switch-azuremode AzureResourceManager

	Скачайте модуль [Azure PowerShell][azure-powershell-install], если он не установлен на компьютере.
3. Используйте командлет **New-AzureDataFactoryTable**, чтобы создать входную таблицу, используя файл **EmpOnPremSQLTable.json**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Обновите команду, если вы используете другую группу ресурсов.

### Создание выходной таблицы

1.	Создайте JSON-файл для таблицы фабрики данных. Он будет использоваться для получения выходных данных для конвейера, который вы создадите в следующем шаге. Запустите программу Блокнот, скопируйте в нее следующий JSON-сценарий и сохраните его как файл **OutputBlobTable.json** в папку с путем C:\ADFGetStarted\ **C:\ADFGetStarted\OnPrem**.

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Обратите внимание на следующее: 
	
	- для типа **расположения** установлено значение **AzureBlobLocation**;
	- **для параметра linkedServiceName** установлено значение **MyBlobStore** (вы создали эту связанную службу в шаге 2).
для параметра 	- **folderPath** установлено значение **adftutorial/outfromonpremdf**, где outfromonpremdf - это папка в контейнере adftutorial. Вам просто нужно создать контейнер **adftutorial**;
	- для **availability** установлено выполнение **каждый час** (для параметра **frequency** установлено значение **hour**, а для **interval** - **1**).  Служба фабрики данных будет создавать срезы выходных данных в таблице **emp** в базе данных базы данных SQL Azure каждый час. 

	Если вы не зададите параметр **fileName** для **входной таблицы**, все файлы или большие двоичные объекты из входной папки (**folderPath**) будут считаться входными файлами или объектами. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Примеры файлов см. в [учебнике][].
 
	Если не указать **fileName** для **выходной таблицы**, создаваемые в **folderPath** файлы именуются в следующем формате: Data.<Guid>.txt (например, : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Для динамической установки папки **folderPath** и имени **fileName** на основе времени **SliceStart**, используйте свойство partitionedBy. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а fileName использует время (часы) из SliceStart. Например, если срез выполняется для 2014-10-20T08:00:00, для folderName будет установлено значение wikidatagateway/wikisampledataout/2014/10/20, а для fileName - 08.csv. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Дополнительную информацию о свойствах JSON-сценариев см. в разделе [JSON Scripting Reference][json-script-reference] (Справочные материалы по использованию JSON-сценариев).

2.	В модуле **Azure PowerShell** выполните следующую команду, чтобы создать еще одну таблицу фабрики данных для представления таблицы в базе данных SQL Azure.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Шаг 4. Создание и запуск конвейера
В этом шаге вы создадите **конвейер** одним **действием копирования**, для выполнения которого **EmpOnPremSQLTable** будет использоваться как входные данные, а **OutputBlobTable** - как выходные данные.

1.	Создайте JSON-файл для конвейера. Запустите программу Блокнот, скопируйте в нее следующий JSON-сценарий и сохраните его как файл **ADFTutorialPipelineOnPrem.json** в папку с путем **C:\ADFGetStarted\OnPrem**.
	 

        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
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
 
	- в разделе действий есть лишь одно действие, для параметра **type** которого установлено значение **CopyActivity**;
	- **для параметра действия input** установлено значение **EmpOnPremSQLTable**, а для **output** - **OutputBlobTable**;
	- **в разделе transformation** в качестве **типа источника** установлено **SqlSource**, а в качестве **типа приемника** - **BlobSink**;
	- для свойства **sqlReaderQuery** типа **SqlSource** задан вид SQL-запроса **select * from emp**.

2. Воспользуйтесь командлетом **New-AzureDataFactoryPipeline**, чтобы создать конвейер с помощью уже созданного файла **ADFTutorialPipeline.json**.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. После создания конвейера можно задать длительность обработки данных. Указывая активный период для конвейера, вы определяете период времени, в течение которого будут обрабатываться срезы данных на основе свойств **доступности**, определенных в каждой таблице фабрики данных Azure.  Выполните следующую команду PowerShell, чтобы задать активный период для конвейера и введите Y для подтверждения. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Замените значение **StartDateTime** текущим днем, а **EndDateTime** - следующим днем. StartDateTime и EndDateTime являются значениями времени в формате UTC, и потому должны быть заданы в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. EndDateTime - не обязательный параметр, но он используется в этом учебнике.
	> Если вы не укажете значение **EndDateTime**, оно будет рассчитываться по формуле **StartDateTime + 48 часов**. Чтобы конвейер выполнялся в течение неопределенного срока, укажите значение **9/9/9999** для **EndDateTime**.

	В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
4. На **портале предварительной версии Azure** щелкните плитку **Схемы** на домашней странице фабрики данных **ADFTutorialOnPremDF**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Вы должны увидеть схему, аналогичную приведенной ниже:

	![Diagram View][image-data-factory-diagram-view]

	**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и  	конвейер, а также запустили рабочий процесс.

## Шаг 5. Мониторинг наборов данных и конвейеров
В этом шаге вы будете использовать портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Дополнительную информацию об использовании командлетов для мониторинга см. в разделе [Monitor and Manage Azure Data Factory using PowerShell][monitor-manage-powershell] (Мониторинг фабрики данных Azure и управление ею с использованием PowerShell).

1. Перейдите на **портал предварительной версии Azure** (если вы закрыли его страницу)
2. Если колонка для **ADFTutorialOnPremDF** закрыта, откройте ее, щелкнув **ADFTutorialOnPremDF** на **начальной панели**.
3. Вы увидите **количество** и **имена** таблиц, а также конвейер, который вы создали в этой колонке.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. Теперь щелкните плитку **Наборы данных**.
5. В колонке **Наборы данных** щелкните **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Обратите внимание, что до текущего времени выполнены срезы данных и состояние каждого из них - **Готов**. Это результат того, что вы вставили данные в базу данных SQL Server, и они находились там все время. Убедитесь, что в разделе **Проблемные срезы** в нижней части окна не показаны срезы.
7. Затем в колонке **Наборы данных** щелкните **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Убедитесь, что выполнены срезы вплоть до текущего времени и состояние каждого из них - **Готов**.
9. Щелкните любой срез данных в списке и увидите колонку **СРЕЗ ДАННЫХ**.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Щелкните **выполненное действие** в нижней части списка, чтобы увидеть **дополнительную информацию о выполненном действии**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Нажмите кнопку **X**, чтобы закрыть все колонки, пока вы не вернетесь к главной колонке для **ADFTutorialOnPremDF**.
14. (Необязательное действие) щелкните плитку **Конвейеры**, а затем - конвейер **ADFTutorialOnPremDF** и детализируйте входные таблицы (**Использовано**) или выходные таблицы (**Выполнено**).
15. Используйте инструменты, такие как **Azure Storage Explorer** для проверки выходных данных.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Создание и регистрация шлюза с использованием командлетов Azure PowerShell
В этом разделе описывается, как создать и зарегистрировать шлюз с использованием командлетов Azure PowerShell. 

1. Запустите модуль **Azure PowerShell** в режиме администратора. 
2. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**. Выполните следующую команду, чтобы перейти в режим **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Используйте командлет **New-AzureDataFactoryGateway** для создания логического шлюза следующим образом:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Пример команды и выходных данных**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Используйте командлет **New-AzureDataFactoryGatewayKey**, чтобы создать регистрационный ключ для вновь созданного шлюза и сохраните этот ключ в локальной переменной **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Пример вывода команды:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. В Azure PowerShell перейдите к папке с путем **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript\** и выполните сценарий **RegisterGateway.ps1**, связанный с локальной переменной **$Key**, как показано в следующей команде, чтобы зарегистрировать агент клиента, установленный на вашем компьютере с логическим шлюзом, созданным ранее.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Вы можете использовать командлет **Get-AzureDataFactoryGateway**, чтобы получить список шлюзов своей фабрики данных. Если в поле **Состояние** показано значение **В сети**, шлюз готов к эксплуатации.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

Вы можете удалить шлюз, используя командлет **Remove-AzureDataFactoryGateway**, или обновить описание шлюза, используя командлет **Set-AzureDataFactoryGateway**. Дополнительную информацию о синтаксисе и другую информацию об этих командлетах см. в "Справочных материалах по командлетам фабрики данных".  




## См. также

Статья | Описание
------ | ---------------
[Get started with Azure Data Factory][adf-getstarted] |  (Начало работы с фабрикой данных Azure). В этой статье содержится полный учебник, в котором показано, как создать образец фабрики данных Azure, которая копирует данные из службы хранилища больших двоичных объектов Azure в базу данных SQL Azure.
[Use Pig and Hive with Data Factory][use-pig-and-hive-with-data-factory] (Использование сценариев Pig и Hive с фабрикой данных)| В этой статье содержится руководство, в котором показано, как использовать действие HDInsight для выполнения сценариев Hive и Pig, чтобы обрабатывать входные данные для получения выходных данных. 
[Учебник. Move and process log files using Data Factory][adf-tutorial(Перемещение и обработка файлов журнала с помощью фабрики данных)] | В этой статье содержится полное руководство, в котором показана реализация сценария, приближенного к действующему, используя фабрику данных Azure для преобразования данных из файлов журнала в файлы HDInsight.
[Use custom activities in a Data Factory][use-custom-activities] | (Использование настраиваемых действий в фабрике данных)В этой статье содержится руководство с пошаговыми указаниями для создания настраиваемого действия и его использования в конвейере. 
[Troubleshoot Data Factory issues][troubleshoot] | (Устранение неполадок фабрики данных)В этой статье описываются способы устранения неполадок в фабрике данных Azure.  
[Azure Data Factory Developer Reference][developer-reference] | (Справочник по фабрике данных Azure для разработчика)В справочнике разработчика содержатся исчерпывающие справочные данные о командлетах, JSON-сценариях, функциях и т. д. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/ru-ru/documentation/articles/install-configure-powershell/


[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png
