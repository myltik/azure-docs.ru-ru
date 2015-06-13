<properties 
	pageTitle="Перемещение и обработка файлов журнала с помощью фабрики данных Azure" 
	description="Это расширенный учебник описывает почти реальном сценарии и реализует сценарий с помощью редактора фабрики данных и служба фабрики данных Azure." 
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

# Учебник: Измерение эффективности маркетинговой кампании  
Contoso представляет игры компании, которая создает игр для нескольких платформ: игровых консолей, карманный устройств и персональных компьютеров (ПК). Эти игры производят большое количество журналов и Contoso целью является сбор и анализ этих журналов для получения понимание параметров клиента, демографические данные, использование поведения и т.д., для определения возможности увеличения продаж и перекрестные продажи, разрабатывать новые привлекательные функции для развития бизнеса и удобства клиентам.

В этом учебнике вы создадите конвейеры данных фабрики для оценки эффективности маркетинговой кампании, Contoso недавно запущенно образец журналы для сбора, обработки и усовершенствованию их ссылочными данными и преобразования данных. Он состоит из следующих трех конвейеры.

1.	 **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища BLOB-объектов и создает секциями, основываясь на год, месяц и день.
2.	 **EnrichGameLogsPipeline** объединение секционированных игры события с географически кода ссылочных данных и дополняет данные путем сопоставления IP-адреса для соответствующих географических расположений.
3.	 **AnalyzeMarketingCampaignPipeline** конвейера использует богатый набор данных и обрабатывает его с данными рекламу для создания конечного результата, содержащий эффективности маркетинговой кампании.

## Подготовка к работе с учебником
1.	Чтение [Введение в Azure данные фабрики][adfintroduction] получить общие сведения о данных фабрика Azure и понятиями верхнего уровня.
2.	Для выполнения инструкций этого учебника необходимо иметь подписку Azure. Сведения о получении подписки см. в разделе [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/), [Предложения для участников](http://azure.microsoft.com/pricing/member-offers/) или [Бесплатное пробное использование](http://azure.microsoft.com/pricing/free-trial/).
3.	Необходимо загрузить и установить [Azure PowerShell][download-azure-powershell] на компьютере. Будет выполнять командлеты фабрика данных для передачи образцов данных и pig и hive скрипты для хранилища blob. 
2.	**(рекомендуется)** проверки и практики в учебнике [Приступая к работе с фабрикой данных Azure][adfgetstarted] статьи для простого учебника, чтобы ознакомиться с порталом и командлеты.
3.	**(рекомендуется)** проверки и практики пошагового руководства в [Использование Pig и Hive с фабрикой данных Azure][usepigandhive] статьи Пошаговое руководство по созданию конвейера для перемещения данных из локального источника данных в хранилище Azure BLOB-объектов.
4.	Загрузить [ADFWalkthrough][adfwalkthrough-download] файлы **C:\ADFWalkthrough** папки **Сохранение структуры папок**:
	- **Конвейеры:** он включает файлы JSON, содержащий определение конвейеры.
	- **Таблиц:** он включает файлы JSON, содержащий определение таблицы.
	- **LinkedServices:** он включает файлы JSON, содержащие определения хранилища и вычислений (HDInsight) кластера 
	- **Сценариев:** он включает Hive и Pig сценарии, которые используются для обработки данных и вызывать из конвейеры
	- **SampleData:** он включает образцы данных для этого пошагового руководства
	- **Котором локально:** он включает файлы JSON и сценариев, используемых для демонстрации получить доступ к локальным данным
	- **uploadSampleDataAndScripts.ps1:** этот скрипт отправляет образец данных и скриптов в Azure.
5. Убедитесь, что вы создали следующие ресурсы Azure:			
	- Учетная запись хранения Azure.
	- База данных SQL Azure
	- Кластера Azure HDInsight версии 3.1 или выше (или по требованию использовать кластер HDInsight, который будет автоматически создавать службы данных фабрики)	
7. После создания ресурсов Azure убедитесь, что у вас есть информация, необходимая для подключения к каждому из этих ресурсов.
 	- **Учетной записи хранилища azure** — имя учетной записи и ключ учетной записи.  
	- **Базы данных SQL azure** -сервера, базы данных, имя пользователя и пароль.
	- **Кластера azure HDInsight**.-имя кластера HDInsight, имя пользователя, пароль и имя учетной записи и ключ учетной записи хранилища Azure, связанного с этим кластером. Этот шаг можно пропустить, если вы хотите использовать кластер HDInsight по требованию вместо кластера HDInsight.  
8. Запустите **Azure PowerShell** и выполните следующие команды. Не закрывайте Azure PowerShell. Если закрыть и снова открыть, необходимо еще раз выполните следующие команды.
	- Запустите **Add-AzureAccount** и введите имя пользователя и пароль, используемый для входа на портале Azure предварительной версии.  
	- Запустите **Get-AzureSubscription** для просмотра всех подписок для этой учетной записи.
	- Запустите **Select-AzureSubscription** выберите подписку, которую требуется работать с. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure.	

## Обзор
Ниже изображен сквозной рабочий процесс:

![Учебник сквозного потока][image-data-factory-tutorial-end-to-end-flow]

1.  **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секциями, основываясь на год, месяц и день (PartitionedGameEventsTable).
2.  **EnrichGameLogsPipeline** объединяет события секционированных игры (PartitionedGameEvents таблица является выходным PartitionGameLogsPipeline) географически кодом (RefGetoCodeDictionaryTable) и дополнение данных путем сопоставления IP-адреса для соответствующего географическое местоположение (EnrichedGameEventsTable).
3.  **AnalyzeMarketingCampaignPipeline** конвейера использует богатый набор данных (созданный EnrichGameLogsPipeline EnrichedGameEventTable) и обрабатывает его с данными рекламу (RefMarketingCampaignnTable) для создания выходных маркетинговых кампаний эффективность, копируются в базу данных Azure SQL (MarketingCampainEffectivensessSQLTable) и хранилище больших двоичных объектов (MarketingCampaignEffectivenessBlobTable) для аналитики.
    
## Пошаговое руководство: Создание, развертывание и наблюдение за рабочими процессами
1. [Шаг 1: загрузить образцы данных и сценарии](#MainStep1). В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые будут выполняться рабочими процессами. Выполняемые вами сценарии также создают базу данных SQL Azure (с именем MarketingCampaigns), таблицы, пользовательские типы и хранимые процедуры.
2. [Шаг 2: создание фабрики данных Azure](#MainStep2). В этом шаге вы создадите фабрику данных Azure с именем LogProcessingFactory.
3. [Шаг 3: создать связанные службы](#MainStep3). В этом шаге вы создадите следующие связанные службы: 
	
	- 	**StorageLinkedService**. Связывает расположение службы хранилища Azure, которая содержит необработанные события игры, секционированные события игры, обогащенные события игры, информацию об эффективности маркетинговой кампании, ссылочные данные геокода и ссылочные данные маркетинговой кампании, с фабрикой LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Связывает базу данных SQL Azure, которая содержит информацию об эффективности маркетинговой кампании. 
	- 	**HDInsightStorageLinkedService**. Связывает хранилище больших двоичных объектов Azure, связанное с кластером HDInsight, на который ссылается HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Связывает кластер HDInsight Azure с фабрикой LogProcessingFactory. Этот кластер используется для выполнения обработки данных pig/hive. 
 		
4. [Шаг 4: создание таблиц](#MainStep4). В этом шаге вы создадите следующие таблицы:
	
	- **RawGameEventsTable**. Эта таблица указывает расположение необработанных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Эта таблица указывает расположение секционированных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Эта таблица указывает расположение ссылочных данных геокода в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Эта таблица указывает расположение ссылочных данных маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Эта таблица указывает расположение обогащенных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Эта таблица определяет таблицу SQL (MarketingCampaignEffectiveness) в базе данных SQL Azure, определяется AzureSqlLinkedService данными эффективность маркетинговой кампании. 
	- **MarketingCampaignEffectivenessBlobTable**. Эта таблица указывает расположение данных об эффективности маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Шаг 5: Создание и планирование конвейеры](#MainStep5). В этом шаге вы создадите следующие конвейеры:
	- **PartitionGameLogsPipeline**. Конвейер считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секции, основываясь на годе, месяце и дне (PartitionedGameEventsTable). 


		![PartitionGamesLogs конвейера][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Конвейер объединяет секционированные события игры (таблица PartitionedGameEvents, которая является выходными данными конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и обогащает данные путем сопоставления IP-адреса с соответствующим географическим положением (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Конвейер использует обогащенные данные событий игры (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательные выходные данные эффективности маркетинговой кампании, которые копируются в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для аналитики


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Шаг 6: отслеживать фрагменты данных и конвейеры](#MainStep6). На этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.

## <a name="MainStep1"></a> Шаг 1: Загрузить образцы данных и сценарии
В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые вызываются рабочими процессами. Можно также выполнять скрипты создания базы данных Azure SQL называется **MarketingCampaigns**, определяемые пользователем типы таблиц и хранимых процедур.

Таблицы, пользовательские типы и хранимые процедуры используются при перемещении результатов эффективности маркетинговой кампании из хранилища больших двоичных объектов Azure в базу данных SQL Azure.

1. Откройте **uploadSampleDataAndScripts.ps1** из **C:\ADFWalkthrough** папку (или папку, содержащую извлеченные файлы) в своем любимом редакторе замените выделенную данные кластера и сохраните файл.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [AZURE.NOTE][скачать][sqlcmd-install]   
2. Убедитесь, что вашему локальному компьютеру разрешен доступ к базе данных SQL Azure. Чтобы разрешить доступ, используйте **портала управления Azure** или **sp_set_firewall_rule** в базе данных master, чтобы создать правило брандмауэра для IP-адрес вашего компьютера. Может потребоваться до пяти минут, чтобы это изменение вступило в силу. В разделе [Настройка правил брандмауэра для Azure SQL][azure-sql-firewall].
4. В Azure PowerShell перейдите в расположение, где уже извлечены образцов (например: **C:\ADFWalkthrough**)
5. Запустите **uploadSampleDataAndScripts.ps1** 
6. Если сценарий выполнится успешно, вы увидите следующее:

		$storageAccount = <storage account name>
		PS C:\ ADFWalkthrough> & '.\uploadSampleDataAndScripts.ps1'

		Name			PublicAccess		LastModified
		-----			--------		------
		ADFWalkthrough		off			6/6/2014 6:53:34 PM +00:00
	
		Uploading sample data and script files to the storage container [adfwalkthrough]

		Container Uri: https://<yourblobstorage>.blob.core.windows.net/adfwalkthrough

		Name                        BlobType   Length   ContentType               LastModified                        
		----                        --------   ------   -----------               ------------                        
		logs/rawgameevents/raw1.csv  BlockBlob  12308   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw2.csv  BlockBlob  16119   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw3.csv  BlockBlob  16062   application/octet-stream  6/6/2014 6:54:35 PM 
		logs/rawgameevents/raw4.csv  BlockBlob  16245   application/octet-stream  6/6/2014 6:54:35 PM 
		refdata/refgeocodedictiona.. BlockBlob  18647   application/octet-stream  6/6/2014 6:54:36 PM 
		refdata/refmarketingcampai.. BlockBlob  8808    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/partitionlogs.hql    BlockBlob  2449    application/octet-stream  6/6/2014 6:54:36 PM 
		scripts/enrichlogs.pig       BlockBlob  1631    application/octet-stream  6/6/2014 6:54:36 PM
		scripts/transformdata.hql    BlockBlob  1753    application/octet-stream  6/6/2014 6:54:36 PM

		6/6/2014 11:54:36 AM Summary
		6/6/2014 11:54:36 AM 1. Uploaded Sample Data Files to blob container.
		6/6/2014 11:54:36 AM 2. Uploaded Sample Script Files to blob container.
		6/6/2014 11:54:36 AM 3. Created ‘MarketingCampaigns’ Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 

## <a name="MainStep2"></a> Шаг 2: Создание фабрики данных Azure
На этом шаге, создайте фабрику данных Azure с именем **LogProcessingFactory**.

1.	После входа в [предварительной версии портала Azure][azure-preview-portal], щелкните **Создать** в левом нижнем углу, выберите **анализа данных** в **Создать** стоечный модуль и нажмите кнопку **фабрика данных** на **анализа данных** выноски. 

	![New -> DataFactory][image-data-factory-new-datafactory-menu]

5. В **новую фабрику данных** выноску, введите **LogProcessingFactory** для **имя**.

	![Выноска фабрика данных][image-data-factory-tutorial-new-datafactory-blade]

6. Если вы еще не создали группу ресурсов Azure с именем **ADF** уже, выполните следующие действия:
	1. Щелкните **имя группы РЕСУРСОВ**, и нажмите кнопку **Создать новую группу ресурсов**.
	
		![Выноска группы ресурсов][image-data-factory-tutorial-resourcegroup-blade]
	2. В **Создать группу ресурсов** выноску, введите **ADF** для имени группы ресурсов и нажмите кнопку **ОК**.
	
		![Создать группу ресурсов][image-data-factory-tutorial-create-resourcegroup]
7. Выберите **ADF** для **имя группы РЕСУРСОВ**.  
8.	В **новую фабрику данных** выноску, обратите внимание, что **Добавить на начальную панель** выбран по умолчанию. Это добавляет связь с фабрикой данных на начальной панели (то, что вы видите при входе на портал предварительной версии Azure).

	![Создание фабрики выноска данных][image-data-factory-tutorial-create-datafactory]

9.	В **новую фабрику данных** стоечный модуль, щелкните **Создать** для создания фабрики данных.
10.	После создания фабрики данных вы увидите **ФАБРИКА данных** выноске под названием **LogProcessingFactory**.

	![Домашняя страница фабрика данных][image-data-factory-tutorial-datafactory-homepage]

	
	Если вы ее не видите, выполните одно из следующих действий:

	- Щелкните **LogProcessingFactory** на **начальной** (Домашняя страница)
	- Щелкните **Обзор** слева, щелкните **все**, нажмите кнопку **фабрики данных**, и нажмите кнопку фабрика данных.
 
	> [AZURE.NOTE]Имя фабрики данных Azure должно быть глобально уникальным. Если сообщение об ошибке: **данных фабрики имя «LogProcessingFactory» недоступно**, измените имя (например, yournameLogProcessingFactory). Используйте это имя вместо LogProcessingFactory при выполнении шагов в этом учебнике.
 
## <a name="MainStep3"></a> Шаг 3: Создание связанные службы

> [AZURE.NOTE]В этой статье использует портала Azure, в частности фабрики редактора, для создания связанные службы, таблицы и конвейеры. В разделе [учебника с помощью Azure PowerShell][adftutorial-using-powershell] Если вы хотите выполнить этот учебник, с помощью Azure PowerShell.

В этом шаге вы создадите следующие связанные службы:

- StorageLinkedService
- AzureSqlLinkedService
- HDInsightStorageLinkedService
- HDInsightLinkedService. 

### Создание StorageLinkedService и HDInsightStorageLinkedService

1.	В **ФАБРИКА данных** стоечный модуль, щелкните **автора и развертывание** плитки для запуска **редактор** для фабрики данных.

	![Плитка «Создание и развертывание»][image-author-deploy-tile]

	> [AZURE.NOTE]В разделе [данных фабрики редактора][data-factory-editor] разделе подробный обзор данных фабрики редактора.

2.  В **редактор**, щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **хранилища Azure** из раскрывающегося меню. Вы увидите JSON шаблон для создания службы хранилища Azure связаны в правой области.
	
	![Редактор новой кнопки хранилища данных][image-editor-newdatastore-button]

3. Замените **accountname** и **accountkey** с именем учетной записи и учетной записи значения ключа для вашей учетной записи хранилища Azure.

	![Хранилище BLOB-объектов редактора JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.

4. Щелкните **Развернуть** на панели инструментов, чтобы развернуть StorageLinkedService. Убедитесь, что сообщение **СВЯЗАННЫЕ службы СОЗДАН успешно** в строке заголовка.

	![Развертывание редактор хранилища BLOB-объектов][image-editor-blob-storage-deploy]

5. Повторите шаги для создания другого хранилища Azure связан с именем службы: **HDInsightStorageLinkedService** для хранения данных, связанных с кластером HDInsight. В сценарии JSON для связанной службы, измените значение **имя** свойства **HDInsightStorageLinkedService**.

### Создание AzureSqlLinkedService
1. В **данных фабрики редактора** , щелкните **новое хранилище данных** кнопки на панели инструментов и выберите **базы данных Azure SQL** из раскрывающегося меню. Вы увидите JSON шаблон для создания службы SQL Azure связаны в правой области.
2. Замените **имя_сервера**, **username@servername**, и **пароль** с именами сервера Azure SQL, учетная запись пользователя и пароль. 3. Замените **databasename** с **MarketingCampaigns**. Это база данных Azure SQL, созданные сценарии, которые выполнялись в шаге 1. Следует убедиться, что эта база данных действительно создана скриптов (в случае, если возникли ошибки). 
3. Щелкните **Развернуть** на панели инструментов для создания и развертывания AzureSqlLinkedService.

### Создание HDInsightLinkedService
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу.

#### Использование кластера HDInsight по запросу
1. Щелкните **новых вычислений** из командной строки и выберите **кластера HDInsight по требованию** меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterSize** свойство, укажите размер кластера HDInsight.
	2. Для **jobsContainer** свойство, укажите имя контейнера по умолчанию, где будет храниться журнал кластера. В целях этого учебника укажите **adfjobscontainer**.
	3. Для **timeToLive** свойство, укажите, как долго клиент может простаивать перед его удалением. 
	4. Для **версии** свойство, укажите версию HDInsight, вы хотите использовать. Если исключить это свойство используется последняя версия.  
	5. Для **linkedServiceName**, укажите **HDInsightStorageLinkedService** был создан в Get учебник работы. 

			{
		    	"name": "HDInsightLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "HDInsightStorageLinkedService"
		    	}
			}

		Обратите внимание, что **тип** связанные службы имеет значение **HDInsightOnDemandLinkedService**.

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.
   
   
#### Использование собственного кластера HDInsight 

1. Щелкните **новых вычислений** из командной строки и выберите **кластера HDInsight** в меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterUri** свойство, введите URL-адрес для вашей HDInsight. Например: https://<clustername>.azurehdinsight.net/     
	2. Для **пользователя** свойство, введите имя пользователя, который имеет доступ к кластеру HDInsight.
	3. Для **пароль** свойство, введите пароль для пользователя. 
	4. Для **LinkedServiceName** свойство, введите **StorageLinkedService**. Это связанные службы, с которой был создан в учебнике работы Get. 

	Nore, **тип** связанные службы имеет значение **HDInsightBYOCLinkedService** (BYOC означает перевести собственные кластера).

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.


## <a name="MainStep4"></a> Шаг 4: Создание таблицы
 
На этом шаге вы создадите в следующих таблицах фабрика данных:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Учебник начала до конца потока][image-data-factory-tutorial-end-to-end-flow]
 
На рисунке выше изображены конвейеры (в средней строке) и таблицы (в верхней и нижней строках).

### Создание таблиц
	
1. В **редактор** фабрику данных щелкните **новый набор данных** кнопки на панели инструментов и нажмите кнопку **хранилища BLOB-объектов Azure** из раскрывающегося меню. 
2. Замените скрипт JSON из JSON в правой области **RawGameEventsTable.json** файл **C:\ADFWalkthrough\Tables** папки.
3. Щелкните **Развернуть** на панели инструментов, чтобы создать и развернуть таблицу. Убедитесь, что **Таблица СОЗДАНА успешно** сообщения в заголовке окна редактора.
4. Повторите шаги 1-3 с содержимым из следующих файлов: 
	1. PartitionedGameEventsTable.json
	2. RefGeoCodeDictionaryTable.json
	3. RefMarketingCampaignTable.json
	4. EnrichedGameEventsTable.json
	5. MarketingCampaignEffectivenessBlobTable.json 
5. Повторите шаги 1-3 с содержимым из следующего файла. НО выберите **Azure Sql** после нажатия кнопки **новый набор данных**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a> Шаг 5: Создание и планирование конвейеры
В этом шаге вы создадите следующие конвейеры:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Чтобы создать конвейеры

1. В **редакторе фабрики данных**, щелкните кнопку **Создать конвейер** на панели инструментов. Нажмите **... (многоточие)** на панели инструментов, если кнопка не отображается. Кроме того, можно щелкнуть правой кнопкой мыши **Конвейеры** в древовидном представлении и выбрать **Создать конвейер**.
2. Замените скрипт JSON из JSON в правой области **PartitionGameLogsPipeline.json** файл **C:\ADFWalkthrough\Pipelines** папки.
3. Добавьте запятую **(,)** в конце закрывающей квадратной скобки **(])** в JSON и после этой закрывающей квадратной скобки добавьте следующие три строки. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	[AZURE.NOTE]Обратите внимание, что время начала и окончания задано как 1.05.2014 и 5.05.2014, поскольку образец данных в этом пошаговом руководстве получен с 1.05.2014 по 5.05.2014.
 
3. Щелкните **Развернуть** на панели инструментов для создания и развертывания конвейера. Убедитесь, что в заголовке окна редактора отображается сообщение **КОНВЕЙЕР УСПЕШНО СОЗДАН**.
4. Повторите шаги 1-3 с содержимым из следующих файлов: 
	1. EnrichGameLogsPipeline.json
	2. AnalyzeMarketingCampaignPipeline.json
4. Закройте фабрика данных системам, нажав клавишу **X** (верхний правый угол) см. на домашней странице (** ФАБРИКА данных **выноску) для фабрики данных. 
### Представление диаграммы

1. В **ФАБРИКА данных** выноски для **LogProcessingFactory**, щелкните **Схема**. 

	![Ссылка на диаграмме][image-data-factory-tutorial-diagram-link]

2. Вы можете изменить эту схему, а здесь показана измененная схема, в которой данные прямого ввода отображается вверху, а выходные данные — внизу. Можно видеть, что выходные данные **PartitionGameLogsPipeline** передается в качестве входных данных для EnrichGameLogsPipeline и вывода **EnrichGameLogsPipeline** передается **AnalyzeMarketingCampaignPipeline**. Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка.

	![Представление диаграммы][image-data-factory-tutorial-diagram-view]

3. Щелкните правой кнопкой мыши **AnalyzeMarketingCampaignPipeline**, и нажмите кнопку **Откройте конвейера**. Вы увидите все действия в конвейере и наборы данных ввода-вывода для действий.
 
	![Откройте конвейера](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Щелкните **фабрика данных** в строке навигации в левом верхнем углу вернуться в представление диаграммы с конвейерами.


**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, конвейеры и таблицы, а также начали рабочий процесс.


## <a name="MainStep6"></a> Шаг 6: Срезы данных и конвейеры отслеживать 

1.	Если у вас **ФАБРИКА данных** выноски для **LogProcessingFactory** открытым, можно выполнить одно из следующих действий:
	1.	Щелкните **LogProcessingFactory** на **начальной**. При создании фабрики данных **Добавить на начальную панель** автоматически выбран параметр.

		![Начальной панели мониторинга][image-data-factory-monitoring-startboard]

	2. Щелкните **Обзор** концентратор и нажмите кнопку **все**.
	 	
		![Все, что мониторинг концентратора][image-data-factory-monitoring-hub-everything]

		В **Обзор** выноску, выберите **фабрики данных** и выберите **LogProcessingFactory** в **данных фабрики** выноски.

		![Наблюдение за Datafactories обзора][image-data-factory-monitoring-browse-datafactories]
2. Вы можете отслеживать фабрики данных несколькими способами. Начните с конвейеров или наборов данных. Давайте начнем с конвейеров, а затем пойдем дальше. 
3.	Щелкните **конвейеры** на **ФАБРИКА данных** выноски. 
4.	Щелкните **PartitionGameLogsPipeline** в выноске конвейеры. 
5.	В **КОНВЕЙЕРА** выноски для **PartitionGameLogsPipeline**, видно, что конвейер потребляет **RawGameEventsTable** набора данных. Щелкните **RawGameEventsTable**.

	![Конвейер потребления и построения][image-data-factory-monitoring-pipeline-consumed-produced]

6. В выноске таблицы для **RawGameEventsTable**, можно увидеть все фрагменты. На следующем снимке экрана все фрагменты, в **готов** состояние и срезы без проблем. Это означает, что данные готовы к обработке.

	![Выноска RawGameEventsTable таблицы][image-data-factory-monitoring-raw-game-events-table]

	Оба **недавно обновлены фрагменты** и **недавно произошел сбой фрагменты** списки сортируются по **время ПОСЛЕДНЕГО обновления**. Время обновления фрагмента изменяется в следующих ситуациях.

	-  Обновить состояние срез вручную, например, с помощью **набора AzureDataFactorySliceStatus** (или), щелкнув **ЗАПУСКА** на **СРЕЗ** выноски для диаграммы.
	-  Срез изменяет состояние из-за выполнения (например выполнения работы, запуска завершается, а не удалось, запуска завершено и выполнена успешно, и т. д).
 
	Щелкните заголовок списки или **... (многоточие)** Чтобы просмотреть список больших фрагментов. Щелкните **фильтра** на панели инструментов для фильтрации фрагментов.
	
	Чтобы просмотреть срезы данных, отсортированных по времени начала и окончания фрагмента, вместо этого, щелкните **срезы данных (по времени срез)** плитки.

	![Срезы данных по времени среза][DataSlicesBySliceTime]
 
7. Теперь на **КОНВЕЙЕРА** выноски для **PartiionGameLogsPipeline**, щелкните **создана**.
8. Вы должны увидеть список наборов данных, которые производит этот конвейер: 
9. Щелкните **PartitionedGameEvents** в таблицу **созданные наборы данных** выноски. 
10.	Убедитесь, что **состояние** всех фрагментов имеет значение **готов**. 
11.	Щелкните один из фрагментов, которые **готов** для просмотра **СРЕЗ данных** выноски для этого фрагмента.

	![Выноска СРЕЗ данных RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Если произошла ошибка, вы увидите **Сбой **статус. Также возможны оба фрагменты с состоянием **готов**, или оба со статусом **PendingValidation**, в зависимости от того, насколько быстро обрабатываются срезы.

	Если фрагмент не находится в **готов** состояние, можно увидеть вышестоящего срезы, которые еще не готовы и блокируют текущий фрагмент выполнение в **вышестоящего фрагменты, которые не готовы** списка.
 
	Ссылаться на [Справочник разработчика Azure данных фабрики][developer-reference] понять все возможные срез состояния.

12.	В **СРЕЗ данных** выноску, выберите запускать с **действие выполняется** списка. Вы должны увидеть колонку «Activity Run» (Выполняемое действие) для этого фрагмента. Вы увидите следующее **сведения о выполнении действия** выноски.

	![Выноска сведения о выполнении действия][image-data-factory-monitoring-activity-run-details]

13.	Щелкните **загрузки** для загрузки файлов. Этот экран особенно полезен при устранении ошибок обработки HDInsight.
	 
	
После завершения выполнения всех конвейер можно найти в **MarketingCampaignEffectivenessTable** в **MarketingCampaigns** базы данных Azure SQL для просмотра результатов.

**Поздравляем!** Теперь вы можете отслеживать рабочие процессы и устранять возникающие в них неполадки. Вы узнали, как использовать фабрику данных Azure для обработки данных и получения аналитической информации.

## Расширить учебника, чтобы использовать локальных данных.
На последнем шаге обработки сценарий из пошагового руководства в данной статье журнала выходные данные эффективность маркетинговой кампании был скопирован в базе данных Azure SQL. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Для копирования данных эффективность маркетинговой кампании из больших двоичных объектов Azure для локального SQL Server, необходимо создать дополнительные локальные связанные службы, таблицы и конвейера появилась в этом руководстве в этой статье.

Рекомендуется [Пошаговое руководство: использование локального источника данных][tutorial-onpremises] чтобы узнать, как создать конвейер для копирования данных эффективность маркетинговой кампании локальной базы данных SQL Server.


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[DataSlicesBySliceTime]: ./media/data-factory-tutorial/DataSlicesBySliceTime.png
[image-author-deploy-tile]: ./media/data-factory-tutorial/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-tutorial/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-tutorial/editor-blob-storage-json.png
[image-editor-blob-storage-deploy]: ./media/data-factory-tutorial/editor-blob-storage-deploy.png

[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial/DataServicesBlade.png

[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial/MonitoringActivityRunDetails.png

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 