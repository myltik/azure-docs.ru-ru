<properties 
	pageTitle="Использование конвейера для работы с локальными данными | Фабрика данных Azure" 
	description="Узнайте, как зарегистрировать локальный источник данных в фабрике данных Azure и копировать данные из источника данных и в него." 
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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Использование конвейера для работы с локальными данными

Чтобы использовать конвейеры в фабрике данных Azure для работы с локальным источником данных, его необходимо добавить в качестве связанной службы в фабрику данных с помощью портала управления Azure или службы Azure PowerShell.
 
Чтобы добавить локальный источник данных в качестве связанной службы в фабрику данных, сначала вам необходимо скачать и установить шлюз управления данными Microsoft на локальный компьютер и настроить связанную службу для локального источника данных, чтобы использовать шлюз.


## <a href="DMG"></a> Шлюз управления данными

**Шлюз управления данными** программное обеспечение, которое подключается локальных источников данных к облачным службам безопасности и управления способом. Шлюз управления данными предоставляет перечисленные ниже возможности.

- **Соединение с локальными данными для доступа к данным гибридного** — локальных данных можно подключиться к облачным службам преимущества облачных служб, сохраняя бизнеса с локальными данными.
- **Определить учетную запись-посредник для защиты данных** — можно определить источники данных, которые локально предоставляется через шлюз управления данными, проверяет подлинность запроса данных из облачных служб, шлюз и защищает локальных источников данных.
- **Управление шлюза для полного управления** — вам будет предоставлен полный мониторинг и запись всех действий, выполняемых внутри шлюза управления данными для управления и контроля.
- **Эффективного перемещения данных** — данные передаются в параллельном режиме, устойчивость к периодические сетевых проблем с автоматически логику повторных попыток.


В шлюзе управления данными предусмотрен полный набор функциональных возможностей для подключения локальных данных.

- **Не требует вмешательства для корпоративного брандмауэра** — после установки просто работает шлюз управления данными, без необходимости открывать брандмауэра подключения или требует существенных изменений в инфраструктуру корпоративной сети. 
- **Шифровать учетные данные с помощью сертификата** – учетные данные, используемые для подключения к источникам данных, шифруются с помощью сертификата, полностью принадлежащих пользователю. Без сертификата никто, даже сотрудник корпорации Майкрософт, не сможет расшифровать учетные данные и преобразовать их в обычный текст.

### Особенности использования шлюза управления данными
1.	Один экземпляр шлюза управления данными можно использовать для нескольких источников данных в локальной, но следует помнить, **шлюза привязывается к производству данных Azure** и не может использоваться совместно с другой фабрика данных.
2.	Может иметь **только один экземпляр шлюза управления данными** на компьютере. Предположим, у вас есть две фабрики данных, которым необходимо получить доступ к локальным источникам данных. В этом случае вам необходимо установить шлюзы на двух локальных компьютерах, чтобы каждый шлюз был связан с отдельной фабрикой данных.
3.	 **Шлюза не на том же компьютере в качестве источника данных**, но остаются ближе к источнику данных уменьшает время для шлюза для подключения к источнику данных. Рекомендуется установить шлюз на компьютере, который отличается от той, которая размещена на локальный источник данных, чтобы шлюз не конкурируют за ресурсы с источником данных.
4.	Может иметь **несколько шлюзов на разных компьютерах, подключение к тому же источнику данных локальной**. Например имеется два шлюза, обслуживающий двух данных фабрики, но тот же источник данных в локальной регистрируются в данных фабрик. 
5.	Если уже установлен на ваш компьютер обслуживанием шлюза **Power BI** сценарий, установите **отдельный шлюз для фабрики Azure данных** на другом компьютере.

### Порты и вопросы безопасности 
- Программа установки шлюза управления данными открывает **8050** и **8051** порты на компьютере шлюза. Эти порты используются **диспетчера учетных данных** (приложение ClickOnce), что позволяет задать учетные данные для локальной связанные службы и проверить соединение с источником данных. Эти порты не доступен из Интернета и требуется нет их открытия в корпоративного брандмауэра.
- При копировании данных из/в локальную базу данных SQL Server из базы данных Azure SQL, убедитесь в следующем.
 
	- Брандмауэр на компьютере шлюза разрешает исходящие соединения TCP на **TCP** порт **1433**
	- Настройка [параметры брандмауэра Azure SQL](https://msdn.microsoft.com/library/azure/jj553530.aspx) добавление **IP-адрес шлюза машины** для **Разрешенные IP-адреса**.

- При копировании данных из локального SQL Server в любое место назначения и шлюза и машины SQL Server, отличаются, выполните следующие действия: [настройки брандмауэра Windows](https://msdn.microsoft.com/library/ms175043.aspx) на SQL Server компьютера, чтобы шлюз может доступ к базе данных через порты, экземпляр SQL Server прослушивает. Для экземпляра по умолчанию это порт 1433.

- Необходимо запустить **диспетчера учетных данных** приложение на компьютере, который может подключиться к шлюзу управления данными, чтобы иметь возможность задать учетные данные для источника данных и проверить подключение к источнику данных.

### Установка шлюза - предварительные требования 

1.	Поддерживаемые **операционной системы** версий, Windows 7, Windows 8 и 8.1, Windows Server 2008 R2, Windows Server 2012.
2.	Рекомендуемые **Конфигурация** для шлюза компьютер имеет по крайней мере 2 ГГц, 4 ядра, 8 ГБ ОЗУ и 80 ГБ дискового.
3.	Если хост-компьютер перейдет в спящий режим, шлюз не сможет отвечать на запросы данных. Таким образом, настройте соответствующее **управления питанием** на компьютере перед установкой шлюза. Установка шлюза запрашивает сообщение, если компьютер настроен на режим гибернации.  


 

## Пошаговое руководство

Это руководство поможет вам создать фабрику данных с конвейером, который позволяет перемещать данные из локальной базы данных SQL Server в большой двоичный объект Azure.

## Шаг 1: Создание фабрики данных Azure
На этом шаге использовать портал управления Azure для создания экземпляра фабрики Azure данных с именем **ADFTutorialOnPremDF**. Фабрику данных также можно создать с помощью командлетов фабрики данных Azure.

1.	После входа в [предварительной версии портала Azure][azure-preview-portal], щелкните **Создать** в левом нижнем углу, выберите **анализа данных** в **Создать** стоечный модуль и нажмите кнопку **фабрика данных** на **анализа данных** выноски.

	![New -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. В **новую фабрику данных** выноску:
	1. Введите **ADFTutorialOnPremDF** для **имя**.
	2. Щелкните **имя группы РЕСУРСОВ** и выберите **ADFTutorialResourceGroup** (если этого учебника [Приступая к работе с фабрикой данных Azure][adfgetstarted]. Вы можете выбрать существующую группу ресурсов или создать новую группу. Чтобы создать новую группу ресурсов:
		1. Щелкните **Создать новую группу ресурсов**.
		2. В **выноска группы ресурсов создать**, введите **имя** группы ресурсов и нажмите кнопку **ОК**.

7. Обратите внимание, что **Добавить на начальную панель** установлен на **новую фабрику данных** выноски.

	![Добавить на начальную панель][image-data-factory-add-to-startboard]

8. В **новую фабрику данных** стоечный модуль, щелкните **Создать**.

	> [AZURE.NOTE]**Имя фабрики данных «ADFTutorialOnPremDF» недоступен**  
9. Найдите уведомления от процесса создания в **уведомления** концентратора слева. Щелкните **X** закрыть **NOTIFCATIONS** выноску, если он открыт.

	![Концентратор УВЕДОМЛЕНИЙ][image-data-factory-notifications-hub]

11. После завершения создания вы увидите **фабрика данных** выноску, как показано ниже:

	![Данные фабрики домашней страницы][image-data-factory-datafactory-homepage]

## Шаг 2: Создание шлюза управления данными
5.	На **фабрика данных** выноски для **ADFTutorialOnPremDF**, щелкните **связанные службы**. 

	![Данные фабрики домашней страницы][image-data-factory-home-age]

2.	На **связанные службы** стоечный модуль, щелкните **+ шлюз данных**.

	![Связанные службы - кнопка шлюза][image-data-factory-linkedservices-add-gateway-button]

2. В **Создать** выноску, введите **adftutorialgateway** для **имя**, и нажмите кнопку **ОК**.

	![Создание шлюза выноска][image-data-factory-create-gateway-blade]

3. В колонке **Настройка** щелкните **Установить непосредственно на этот компьютер**. Это позволит скачать пакет установки для шлюза, а также установить, настроить и зарегистрировать шлюз на компьютере.

	> [AzURE.NOTE]Рекомендуется используйте Internet Explorer или Microsoft ClickOnce совместимых веб-браузера.

	![Шлюз - Настройка выноски][image-data-factory-gateway-configure-blade]

	Это самый простой способ (одним щелчком) скачать, установить, настроить и зарегистрировать шлюз в один шаг. Вы увидите **диспетчера конфигурации шлюза управления данными Майкрософт** на компьютере установлено приложение. Можно также найти исполняемый файл **ConfigManager.exe** в папке: **данных управления C:\Program Files\Microsoft Gateway\1.0\Shared**.

	Можно также загрузить и установить шлюз вручную с помощью ссылок в этой выноски и зарегистрируйте его с помощью ключа, показанные в **с ключа регистрации** текстового поля.
	
	В разделе [Шлюз управления данными](#DMG) подробные сведения о шлюзе, включая рекомендации и важные вопросы см.

	>[AZURE.NOTE]Необходимо быть администратором на локальном компьютере для установки и настройки шлюза управления данными успешно. Можно добавить дополнительных пользователей в локальную группу Windows пользователи шлюза управления данными. Члены этой группы смогут использовать средство диспетчера конфигурации шлюза управления данными для настройки шлюза.

4. Щелкните **уведомления** концентратора слева. Дождитесь появления **Быстрая установка для adftutorialgateway успешно** сообщений в **уведомления** выноски.

	![Быстрая установка выполнена успешно][express-setup-succeeded]
5. Щелкните **ОК** на **Создать** выноску и затем на **Новый шлюз данных** выноски.
6. Закрыть **связанные службы** выноска (нажав **X** кнопки в правом верхнем углу) и снова откройте **связанные службы** выноску, чтобы увидеть последнее состояние шлюза. 
7. Убедитесь, что **состояние** шлюза — **Online**. 

	![Состояние шлюза][gateway-status]
5. Запустите **диспетчера конфигурации шлюза управления данными Майкрософт** приложения на компьютере.

	![Диспетчер конфигурации шлюза][image-data-factory-gateway-configuration-manager]

6. Подождите, пока установятся следующие значения:
	1. Если служба **состояние** не задано значение **работает**, щелкните **Запуск службы** для запуска службы и подождите в течение минуты, обновить поля.
	2. **Имя шлюза** равен **adftutorialgateway**.
	3. **Имя экземпляра** равен **adftutorialgateway**.
	4. **Состояние ключа шлюза** равен **зарегистрированного**.
	5. Строки нижней отображает состояния **подключен к облачной службе шлюза управления данными** вместе с **зеленый**.
	
7. На **связанные службы** выноску, убедитесь, что **состояние** шлюза — **хорошим**.
8. Закройте все модули, пока не дойдете до **фабрика данных** домашней страницы. 

## Шаг 2: Создание связанные службы 
На этом шаге вы создадите две связанные службы: **StorageLinkedService** и **SqlServerLinkedService**.  **SqlServerLinkedService** связывает локальную базу данных SQL Server и **StorageLinkedService** связывает связанные службы Azure BLOB-хранилища для **ADFTutorialDataFactory**. Далее это руководство поможет вам создать конвейер, который позволит копировать данные из локальной базы данных SQL Server в службу хранилища больших двоичных объектов Azure.

### Добавление связанной службы в локальную базу данных SQL Server
1.	В **ФАБРИКА данных** выноску, clcik **Автор и развернуть** плитки для запуска **редактор** для фабрики данных.

	![Плитка «Создание и развертывание»][image-author-deploy-tile]

	> [AZURE.NOTE][Фабрика редактора данных][data-factory-editor]
2.	В **редактор**, щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **базы данных локального SQL server** из раскрывающегося меню. 

	![Редактор новой кнопки хранилища данных][image-editor-newdatastore-onpremsql-button]
    
3.	Вы увидите JSON шаблон для создания локальной службы SQL Server связаны в правой области. ![Связанные службы SQL локальную - параметры][image-editor-newdatastore-onpremsql-settings]

4.	выполните следующие действия в области JSON.
	1.	Для **gatewayName** свойство, введите **adftutorialgateway** заменить весь текст в двойные кавычки.  
	2.	Если вы используете **проверки подлинности SQL**: 
		1.	Для **connectionString** свойства, замените **< имя_сервера >**, **< имя_базы_данных >**, **< имя_пользователя >**, и **< пароль >** с именами на локальном сервере SQL server, базы данных, учетная запись пользователя и пароль.	
		2.	Удаление последних двух свойств (** username ** и **пароль**) с JSON и удалить **запятая (,)** символ в конце последней строки из оставшихся скрипта JSON.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Если вы используете **Проверка подлинности Windows**:
		1. Для **connectionString** свойства, замените **< имя_сервера >** и **< имя_базы_данных >** с именами на локальном сервере SQL server и базы данных. Задайте **встроенной безопасности** для **True**. Удалить **идентификатор** и **пароль** из строки подключения.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Щелкните **Развернуть** на панели инструментов, чтобы развернуть SqlServerLinkedService. Убедитесь, что сообщение **СВЯЗАННЫЕ службы СОЗДАН успешно** в строке заголовка. Вы также увидите **SqlServerLinkedService** в представлении дерева в левой части окна.
		   
	![Успешное развертывание SqlServerLinkedService][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]Можно также создать службу SQL Server связан, щелкнув **новое хранилище данных** на панели инструментов **связанные службы** выноски. При переходе этого маршрута задаются учетные данные для источника данных с помощью приложения ClickOnce диспетчер учетных данных, работающей на компьютере, доступ к порталу. Если для доступа к порталу с того компьютера, отличается от шлюза компьютера, необходимо убедиться в том, что диспетчер учетных данных приложение может подключиться к машине шлюза. Если приложение не может связаться с компьютером шлюза, он не позволяет задать учетные данные для источника данных и проверить подключение к источнику данных.

#### Добавление связанной службы для учетной записи хранения Azure
 
1. В **редактор**, щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **хранилища Azure** из раскрывающегося меню. Вы увидите JSON шаблон для создания службы хранилища Azure связаны в правой области. 

	![Редактор новой кнопки хранилища данных][image-editor-newdatastore-button]
    
6. Замените **< имя_учетной_записи >** и **< accountkey >** с именем учетной записи и учетной записи значения ключа для вашей учетной записи хранилища Azure.

	![Хранилище BLOB-объектов редактора JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.

6. Щелкните **Развернуть** на панели инструментов, чтобы развернуть StorageLinkedService. Убедитесь, что сообщение **СВЯЗАННЫЕ службы СОЗДАН успешно** в строке заголовка.

	![Развертывание редактор хранилища BLOB-объектов][image-editor-blob-storage-deploy]

 
## Шаг 3: Создания входных и выходных наборов данных
В этом шаге вы создадите наборы входных и выходных данных, которые представляют собой входные и выходные данные для операции копирования (из локальной базы данных SQL в хранилище больших двоичных объектов Azure). Перед созданием наборов данных или таблиц (прямоугольных наборов данных) необходимо сделать следующее (после списка есть подробные шаги):

- Создать таблицу с именем **emp** в базе данных SQL Server был добавлен в качестве связанного службы к данным фабрики и вставки несколько записей в таблицу.
- - Если еще не проходили учебника [Приступая к работе с фабрикой данных Azure][adfgetstarted] статьи, создайте контейнер больших двоичных объектов с именем **adftutorial** в Azure BLOB-объект был добавлен в качестве связанного службы фабрики данных учетной записи хранилища.

### Подготовка локальной связанной службы SQL Server для учебника

1. В базе данных, указанной для локального SQL Server связанные службы (** SqlServerLinkedService **), используйте следующий сценарий SQL для создания **emp** таблицы в базе данных.


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

1.	В **данных фабрики редактора**, щелкните **новый набор данных** на панели команд, а затем выберите **локального SQL**. 
2.	Замените следующий текст JSON в правой области.    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
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
	
	- расположение **тип** равен **OnPremisesSqlServerTableLocation**.
	- **tableName** равен **emp**.
	- **linkedServiceName** равен **SqlServerLinkedService** (на шаге 2 был создан этот связанный службы).
	- Входную таблицу, которая не создается другой конвейер в фабрике Azure данные, необходимо указать **waitOnExternal** раздел в JSON. Это означает, что входные данные создаются вне службы фабрики данных Azure.   

	В разделе [ссылки сценария JSON][json-script-reference] подробные сведения о свойствах JSON.

2. Щелкните **Развернуть** на панели команд, чтобы развернуть набор данных (таблица представляет собой прямоугольный набор данных). Убедитесь, что сообщения в заголовке окна с сообщением **таблицы успешно РАЗВЕРНУТ**.


### Создание выходной таблицы

1.	В **данных фабрики редактора**, щелкните **новый набор данных** на панели команд, а затем выберите **хранилища BLOB-объектов Azure**.
2.	Замените следующий текст JSON в правой области. 

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
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Обратите внимание на следующее:
	
	- расположение **тип** равен **AzureBlobLocation**.
	- **linkedServiceName** равен **StorageLinkedService** (на шаге 2 был создан этот связанный службы).
	- **folderPath** равен **adftutorial/outfromonpremdf** где outfromonpremdf-это папка в контейнере adftutorial. Необходимо создать **adftutorial** контейнера.
	-  **Доступности** равен **каждый час** (** частоты ** значение **час** и **интервал** значение **1**). Служба фабрики данных создает фрагмент выходных данных каждый час в **emp** таблицы в базе данных SQL Azure. 

	Если не указать **fileName** для **входной таблицы**, все файлы и большие двоичные объекты из входной папки (** folderPath **) рассматриваются в качестве входных данных. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Образцы файлов в разделе [учебника][adf-tutorial] примеры.
 
	Если не указать **fileName** для **выходной таблицы**, то созданные  в**folderPath** файлы получают имена в следующем формате: Data.<Guid>.txt (например: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Чтобы задать **folderPath** и **fileName** динамически на основе **SliceStart** время, используйте свойство partitionedBy. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а в fileName используется время (часы) из SliceStart. Например, если срез выполняется для временной отметки 2014-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2014/10/20, а fileName – 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	В разделе [ссылки сценария JSON][json-script-reference] подробные сведения о свойствах JSON.

2.	Щелкните **Развернуть** на панели команд, чтобы развернуть набор данных (таблица представляет собой прямоугольный набор данных). Убедитесь, что сообщения в заголовке окна с сообщением **таблицы успешно РАЗВЕРНУТ**.
  

## Шаг 4: Создание и запуск конвейера
На этом шаге создается **конвейера** с одной **Действие копирования** использующий **EmpOnPremSQLTable** в качестве входного и **OutputBlobTable** как output.

1.	Щелкните **новому** на панели команд. Если кнопка не отображается, щелкните **... (многоточие)** Чтобы развернуть панель команд.
2.	Замените следующий текст JSON в правой области.   


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
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	Обратите внимание на следующее:
 
	- В разделе «действия» имеется только действия которого **тип** равен **CopyActivity**.
	- **Ввода** задается действие **EmpOnPremSQLTable** и **выходной** задается действие **OutputBlobTable**.
	- В **преобразования** разделе **SqlSource** указан как **Тип источника** и **BlobSink **указан как **приемника типа**. - SQL-запрос **выберите * из emp** указан для **sqlReaderQuery** свойство **SqlSource**.

	> [AZURE.NOTE]Замените значение **Запуск** свойство с текущего дня и **конец** значение со следующего дня. Как запустить и даты окончания должны находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z.  **Конец** время необязательно, но мы будем использовать в этом учебнике. Если не указать значение для **конец** свойство, оно вычисляется как "** start + 48 часов **». Укажите на бесконечное выполнение конвейера **9, 9-9999** как значение **end** свойство. При определении продолжительность времени, в котором будут обрабатываться срезы данных на основе **доступности** свойства, которые были определены для каждой таблицы данных фабрики Azure. В приведенном выше примере будет 24 среза данных, так как срезы данных производятся каждый час.
	
2. Щелкните **Развернуть** на панели команд, чтобы развернуть набор данных (таблица представляет собой прямоугольный набор данных). Убедитесь, что сообщения в заголовке окна с сообщением **КОНВЕЙЕРА успешно РАЗВЕРНУТ**.
5. Теперь закройте **редактор** выноску, щелкнув **X**. Щелкните **X** чтобы закрыть выноске ADFTutorialDataFactory с представлением инструментов и дерева. Если вы видите **несохраненные изменения будут потеряны** сообщений, нажмите кнопку **ОК**.
6. Должен быть обратно в **ФАБРИКА данных** выноски для **ADFTutorialOnPremDF**.

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.

### Просмотр фабрика данных в представлении диаграммы 
1. В **предварительной версии портала Azure**, щелкните **Схема** плитки на домашней странице для **ADFTutorialOnPremDF** фабрика данных.:

	![Ссылка на диаграмме][image-data-factory-diagram-link]

2. Вы должны увидеть схему, аналогичную приведенной ниже:

	![Представление диаграммы][image-data-factory-diagram-view]

	Можно увеличить, уменьшить масштаб, масштаб 100%, масштаб, размеру, автоматически размещения конвейеры и таблицы и отобразить сведения журнала обращений и преобразований (выделяет восходящие и нисходящие элементов, выбранных элементов). Вы можете double-blick объекта (таблицы ввода вывода или конвейера) для просмотра его свойств.

## Шаг 5: Мониторинг наборы данных и конвейеры
В этом шаге вы будете использовать портал Azure для мониторинга фабрики данных Azure. Вы также можете использовать командлеты PowerShell для мониторинга наборов данных и конвейеров. Дополнительные сведения об использовании командлетов для мониторинга в разделе [монитора и управлять фабрика данных Azure, с помощью PowerShell][monitor-manage-powershell].

1. Перейдите к **предварительной версии портала Azure** (если было закрыто)
2. Если выноски для **ADFTutorialOnPremDF** не открыта, откройте ее, щелкнув **ADFTutorialOnPremDF** на **начальной**.
3. Вы увидите **число** и **имена** таблиц и конвейера, созданной на этой выноски.

	![Данные фабрики домашней страницы][image-data-factory-homepage-2]
4. Теперь щелкните **наборы данных** плитки.
5. На **наборы данных** стоечный модуль, щелкните **EmpOnPremSQLTable**.

	![Фрагменты EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Обратите внимание, что уже было произведено срезы данных до текущего времени и их **готов**. Это результат того, что вы вставили данные в базу данных SQL Server, и они находились там все время. Убедитесь, что срезы не будут отображаться в **фрагменты проблемы** в нижней.


	Оба **недавно обновлены фрагменты** и **недавно произошел сбой фрагменты** списки сортируются по **время ПОСЛЕДНЕГО обновления**. Время обновления фрагмента изменяется в следующих ситуациях.
    

	-  Обновить состояние срез вручную, например, с помощью **набора AzureDataFactorySliceStatus** (или), щелкнув **ЗАПУСКА** на **СРЕЗ** выноски для диаграммы.
	-  Срез изменяет состояние из-за выполнения (например выполнения работы, запуска завершается, а не удалось, запуска завершено и выполнена успешно, и т. д).
 
	Щелкните заголовок списки или **... (многоточие)** Чтобы просмотреть список больших фрагментов. Щелкните **фильтра** на панели инструментов для фильтрации фрагментов.
	
	Чтобы просмотреть срезы данных, отсортированных по времени начала и окончания фрагмента, вместо этого, щелкните **срезы данных (по времени срез)** плитки.

7. Теперь в **наборы данных** стоечный модуль, щелкните **OutputBlobTable**.

	![Фрагменты OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Подтвердите создаваемом срезов до текущего времени и **готов**. Подождите, пока значение состояния срезов до текущего времени **готов**.
9. Щелкните любой сегмент данных из списка, и вы увидите **СРЕЗ данных** выноски.

	![Выноска срез данных][image-data-factory-dataslice-blade]

	Если фрагмент не находится в **готов** состояние, можно увидеть вышестоящего срезы, которые еще не готовы и блокируют текущий фрагмент выполнение в **вышестоящего фрагменты, которые не готовы** списка.

10. Щелкните **Действие запуска** из списка внизу, чтобы увидеть **сведения о выполнении действия**.

	![Выноска сведения о выполнении действия][image-data-factory-activity-run-details]

11. Щелкните **X** закрыть все модули, пока не вернетесь на выноске home для **ADFTutorialOnPremDF**.
14. (необязательно) Щелкните **конвейеры**, щелкните **ADFTutorialOnPremDF**, и детализацию входные таблицы (** израсходовано **) или выходных таблицах (** создана **).
15. Использовать средства, такие как **обозревателя хранилищ Azure** проверка выходные данные.

	![Обозреватель хранилищ Azure][image-data-factory-stroage-explorer]


## Создание и регистрация шлюза с помощью Azure PowerShell 
В этом разделе описывается, как создать и зарегистрировать шлюз с использованием командлетов Azure PowerShell.

1. Запустите **Azure PowerShell** в режиме администратора. 
2. Командлеты фабрика данных Azure доступны в **AzureResourceManager** режим. Выполните следующую команду, чтобы перейти к **AzureResourceManager** режим.     

        switch-azuremode AzureResourceManager


2. Используйте **Создать AzureDataFactoryGateway** командлет для создания логических шлюза следующим образом:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Пример команды и результаты выполнения**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Используйте **Создать AzureDataFactoryGatewayKey** командлет, чтобы создать ключ регистрации для вновь созданного шлюза, а ключ хранится в локальной переменной **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Пример выходных данных команды:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. Azure PowerShell, перейдите в папку: **C:\Program Files\Microsoft данных управления Gateway\1.0\PowerShellScript** и запускать **RegisterGateway.ps1** сценарий, связанный с локальной переменной **$Key** как показано в следующую команду, чтобы зарегистрировать клиентский агент на компьютере с помощью логических шлюза можно создать более ранней версии.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. Можно использовать **Get AzureDataFactoryGateway** командлет, чтобы получить список шлюзов в фабрике данных. При **состояние** показывает **online**, это означает, что шлюз готов к использованию.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Можно удалить шлюз с помощью **Удаление AzureDataFactoryGateway** описание командлета и обновление шлюза с помощью **набора AzureDataFactoryGateway** командлетов. Дополнительную информацию о синтаксисе и другую информацию об этих командлетах см. в «Справочных материалах по командлетам фабрики данных».



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
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

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=GIT-SubDir-->