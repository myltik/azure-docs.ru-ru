<properties 
	pageTitle="Перемещение и обработка файлов журнала с помощью фабрики данных Azure" 
	description="В этом расширенном учебнике описывается сценарий, близкий к реальному, и реализуется сценарий использования службы фабрики данных Azure." 
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
	ms.date="1/28/2015" 
	ms.author="spelluru"/>

# Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных
Эта статья содержит сквозное руководство по каноническому сценарию обработки журнала с помощью фабрики данных Azure для преобразования данных из файлов журнала в аналитические данные. 

## Сценарий
Contoso - это компания-разработчик игр, которая создает игры для различных платформ: игровые приставки, карманные устройства и персональные компьютеры (ПК). Каждая из таких игр создает множество журналов. Цель компании Contoso - собрать и проанализировать журналы, созданные этими играми, чтобы получить информацию об использовании, определить возможности увеличения продаж и перекрестных продаж, разработать новые привлекательные функции и т.д. Все это позволит оптимизировать бизнес и предоставить клиентам больше удобства и возможностей.
 
В этом пошаговом руководстве мы будет собирать образцы журналов, обрабатывать и обогащать их ссылочными данными и преобразовывать данные для оценки эффективности маркетинговой кампании, которую Contoso недавно запустила.

## Подготовка к работе с учебником
1.	Чтобы получить общее представление о фабрике данных Azure и понимание основных понятий, прочитайте статью [Введение в фабрику данных Azure][adfintroduction].
2.	Для выполнения инструкций этого учебника необходимо иметь подписку Azure. Информацию о получении подписки см. в разделах [Варианты приобретения] [azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
3.	Скачайте и установите на своем компьютере [Azure PowerShell][download-azure-powershell]. 
2.	**(рекомендуется)** Чтобы ознакомиться с порталом и командлетами, прочитайте и выполните простые инструкции учебника, изложенные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
3.	**(рекомендуется)** Чтобы выполнить пошаговое руководство по созданию конвейера для перемещения данных из локального источника данных в хранилище больших двоичных объектов Azure, прочитайте и выполните инструкции, изложенные в статье [Использование Pig и Hive с фабрикой данных Azure][usepigandhive].
4.	Скачайте файлы [ADFWalkthrough][adfwalkthrough-download] в папку **C:\ADFWalkthrough**, **сохраняя структуру папок**:
	- **Pipelines:** включает в себя  JSON-файлы, содержащие определение конвейеров.
	- **Tables:** включает в себя  JSON-файлы, содержащие определение таблиц.
	- **LinkedServices:** включает в себя JSON-файлы, содержащие определение вашего кластера хранилища и вычислений (HDInsight) 
	- **Scripts:** включает в себя сценарии Hive и Pig, которые используются для обработки данных и вызываются из конвейеров
	- **SampleData:** включает в себя демонстрационные данные для этого пошагового руководства
	- **OnPremises:** включает в себя JSON-файлы и сценарий, которые используются для демонстрации доступа к вашим локальным данным
	- **AzureEnvironmentSetup.ps1:** это сценарий PowerShell для настройки среды Azure
	- **uploadSampleDataAndScripts.ps1:** этот сценарий передает демонстрационные данные и сценарии в Azure.
5. Убедитесь, что вы создали следующие ресурсы Azure:			
	- Учетная запись хранения Azure.
	- База данных SQL Azure
	- Кластер HDInsight Azure версии 3.1 или выше	
7. После создания ресурсов Azure убедитесь, что у вас есть информация, необходимая для подключения к каждому из этих ресурсов.
 	- **Учетная запись хранения Azure** - имя учетной записи и ключ учетной записи.  
	- **База данных SQL Azure** - сервер, база данных, имя пользователя и пароль.
	- **Кластер HDInsight Azure** - имя кластера HDInsight, имя пользователя, пароль, имя учетной записи и ключ учетной записи для службы хранилища Azure, связанного с этим кластером.  
 8. Запустите **Azure PowerShell**, перейдите к **C:\ADFWalkthrough** и запустите сценарий подготовки .**\AzureEnvironmentSetup.ps1**.
 
		Прежде чем запускать сценарий, вам необходимо иметь имя подписки и учетную запись Майкрософт, связанную с этой подпиской.

		Необходимо настроить среду Azure, используя приведенный выше сценарий при каждом запуске   Azure PowerShell. Файл конфигурации не сохраняется между сеансами и должен выполняться отдельно даже при одновременном использовании нескольких сеансов.

		В качестве альтернативы для входа в Azure вы можете использовать командлет **Add-AzureAccount**, а для выбора подписки (если у вас несколько подписок) - командлет **Select-AzureSubscription**.
	

## Обзор
Ниже изображен сквозной рабочий процесс:
	![Tutorial End to End Flow][image-data-factory-tutorial-end-to-end-flow]

1. Конвейер **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секции, основываясь на годе, месяце и дне (PartitionedGameEventsTable).
2. Конвейер **EnrichGameLogsPipeline** объединяет секционированные события игры (таблица PartitionedGameEvents, которая является выходными данными конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и обогащает данные путем сопоставления IP-адреса с соответствующим географическим положением (EnrichedGameEventsTable).
3. Конвейер **AnalyzeMarketingCampaignPipeline** использует обогащенные данные (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательные выходные данные эффективности маркетинговой кампании, которые копируются в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для аналитики.
    
## Пошаговое руководство: создание, развертывание и мониторинг рабочих процессов
1. [Шаг 1: передача демонстрационных данных и сценариев](#MainStep1). В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые будут выполняться рабочими процессами. Выполняемые вами сценарии также создают базу данных SQL Azure (с именем MarketingCampaigns), таблицы, пользовательские типы и хранимые процедуры.
2. [Шаг 2: создание фабрики данных Azure](#MainStep2). В этом шаге вы создадите фабрику данных Azure с именем LogProcessingFactory.
3. [Шаг 3: создание связанных служб](#MainStep3). В этом шаге вы создадите следующие связанные службы: 
	
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
	- **MarketingCampaignEffectivenessSQLTable**. Эта таблица определяет таблицу SQL (MarketingCampaignEffectiveness) в базе данных SQL Azure, определенной службой AzureSqlLinkedService, которая содержит данные об эффективности маркетинговой кампании. 
	- **MarketingCampaignEffectivenessBlobTable**. Эта таблица указывает расположение данных об эффективности маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Шаг 5: создание и планирование конвейеров](#MainStep5). В этом шаге вы создадите следующие конвейеры:
	- **PartitionGameLogsPipeline**. Конвейер считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секции, основываясь на годе, месяце и дне (PartitionedGameEventsTable). 


		![PartitionGamesLogs pipeline][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Конвейер объединяет секционированные события игры (таблица PartitionedGameEvents, которая является выходными данными конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и обогащает данные путем сопоставления IP-адреса с соответствующим географическим положением (EnrichedGameEventsTable) 

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Конвейер использует обогащенные данные событий игры (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательные выходные данные эффективности маркетинговой кампании, которые копируются в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для аналитики


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Шаг 6: мониторинг конвейеров и срезов данных](#MainStep6). В этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.

### <a name="MainStep1"></a>Шаг 1: передача демонстрационных данных и сценариев
В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые вызываются рабочими процессами. Выполняемые вами сценарии также создают базу данных SQL Azure с именем **MarketingCampaigns**, таблицы, пользовательские типы и хранимые процедуры. 

Таблицы, пользовательские типы и хранимые процедуры используются при перемещении результатов эффективности маркетинговой кампании из хранилища больших двоичных объектов Azure в базу данных SQL Azure.

1. Откройте **uploadSampleDataAndScripts.ps1** из папки **C:\ADFWalkthrough** (или папки, которая содержит извлеченные файлы) в любом редакторе, замените выделенные данные информацией своего кластера и сохраните файл.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	> [WACOM.NOTE] Для этого сценария требуется установить на компьютере служебную программу sqlcmd. Если у вас установлен SQL Server, то эта программа у вас уже есть. В противном случае [скачайте][sqlcmd-install] и установите эту служебную программу. 
	> В качестве альтернативы вы можете использовать файлы в папке C:\ADFWalkthrough\Scripts для передачи сценариев pig/hive и примеров файлов в контейнер adfwalkthrough в хранилище больших двоичных объектов и для создания таблицы MarketingCampaignEffectiveness в базе данных SQL Azure MarketingCamapaigns.   
2. Убедитесь, что вашему локальному компьютеру разрешен доступ к базе данных SQL Azure. Чтобы разрешить доступ, воспользуйтесь **порталом управления Azure**, или воспользуйтесь **sp_set_firewall_rule** в базе данных master, чтобы создать правило брандмауэра для IP-адреса вашего компьютера. Может потребоваться до пяти минут, чтобы это изменение вступило в силу. См. раздел [Настройка правил брандмауэра для SQL Azure][azure-sql-firewall].
3. Запустите **Azure PowerShell**. 
4. Перейдите к расположению, в которое вы извлекли примеры файлов (например, **C:\ADFWalkthrough**)
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
		6/6/2014 11:54:36 AM 3. Created 'MarketingCampaigns' Azure SQL database and tables.
		6/6/2014 11:54:36 AM You are ready to deploy Linked Services, Tables and Pipelines. 


### <a name="MainStep2"></a>Шаг 2: создание фабрики данных Azure
В этом шаге вы создадите фабрику данных Azure с именем **LogProcessingFactory**.

1.	После входа на [портал предварительной версии Azure][azure-preview-portal] в нижнем левом углу щелкните **СОЗДАТЬ**, затем в колонке **Создать** выберите **Фабрика данных**. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Если в колонке **Создать** вы не видите **Фабрика данных**, прокрутите колонку вниз. 
	
5. В колонке **New data factory** (Создать фабрику данных) введите **LogProcessingFactory** в поле **Name** (Имя).

	![Data Factory Blade][image-data-factory-tutorial-new-datafactory-blade]

6. Если вы еще не создали группу ресурсов Azure с именем **ADF**, выполните следующее:
	1. Щелкните **RESOURCE GROUP NAME** (Имя группы ресурсов), затем щелкните **Create a new resource group** (Создать новую группу ресурсов).
	
		![Resource Group Blade][image-data-factory-tutorial-resourcegroup-blade]
	2. В колонке **Create resource group** (Создать группу ресурсов) введите **ADF** в качестве имени группы ресурсов, затем щелкните **ОК**.
	
		![Create Resource Group][image-data-factory-tutorial-create-resourcegroup]
7. Выберите **ADF** для **RESOURCE GROUP NAME** (Имя группы ресурсов).  
8.	Обратите внимание, что в колонке **New data factory** (Создать фабрику данных) значение **Add to Startboard** (Добавить на начальную панель) выбрано по умолчанию. Это добавляет связь с фабрикой данных на начальной панели (то, что вы видите при входе на портал предварительной версии Azure).

	![Create Data Factory Blade][image-data-factory-tutorial-create-datafactory]

9.	Чтобы создать фабрику данных, в колонке **New data factory** (Создать фабрику данных) щелкните **Create** (Создать).
10.	После создания фабрики данных вы должны увидеть колонку **ФАБРИКА ДАННЫХ** с заголовком **LogProcessingFactory**.

	![Data Factory Homepage][image-data-factory-tutorial-datafactory-homepage]

	
	Если вы ее не видите, выполните одно из следующих действий:

	- Щелкните **LogProcessingFactory** на **начальной панели** (домашней странице)
	- Щелкните слева **BROWSE** (Обзор), затем **Everything** (Все элементы), затем **Data factories** (Фабрики данных) и, наконец, щелкните фабрику данных.
 

 
### <a name="MainStep3"></a>Шаг 3: создание связанных служб

В этом шаге вы создадите следующие связанные службы: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService, and HDInsightLinkedService.

1.	В колонке **LogProcessingFactory** щелкните область **Linked Services** (Связанные службы).

	![Linked Services Tile][image-data-factory-tutorial-linkedservice-tile]

2. В колонке **Linked Services** (Связанные службы) из панели команд щелкните **+ Data Store** (+ Хранилище данных).	

	![Linked Services - Add Store][image-data-factory-tutorial-linkedservices-add-datstore]

3. В колонке **New data store** (Создать хранилище данных) введите **StorageLinkedService** в поле **Name** (Имя), щелкните **TYPE (settings required)** (Тип (обязательные настройки)) и выберите**Azure storage account** (Учетная запись хранения Azure).

	![Data Store Type - Azure Storage][image-data-factory-tutorial-datastoretype-azurestorage]

4. В колонке **New data store** (Создать хранилище данных) вы увидите два новых поля: **Account Name** (Имя учетной записи) и **Account Key** (Ключ учетной записи). Введите имя и ключ учетной записи для своей **учетной записи хранения Azure**.

	![Azure Storage Settings][image-data-factory-tutorial-azurestorage-settings]

	Вы можете получить имя и ключ учетной записи для своей учетной записи хранения Azure на портале, как показано ниже:

	![Storage Key][image-data-factory-tutorial-storage-key]
  
5. После того, как вы щелкнули **OK** в колонке "New data store" (Создать хранилище данных), вы должны увидеть **StorageLinkedService** в списке **DATA STORES** (Хранилища данных) в колонке **Linked Services** (Связанные службы). Щелкните центр **NOTIFICATIONS** (Уведомления) (слева), чтобы просмотреть сообщения.

	![Linked Services Blade with Storage][image-data-factory-tutorial-linkedservices-blade-storage]
   
 6. Повторите **шаги 2-5**, чтобы создать другую связанную службу с именем: **HDInsightStorageLinkedService**. Это хранилище, используемое вашим кластером HDInsight.
7. Убедитесь, что обе службы, **StorageLinkedService** и **HDInsightStorageLinkedService**, отображаются в списке в колонке "Linked Services" (Связанные службы).
8. В колонке **Linked Services** (Связанные службы) из панели команд щелкните **Add (+) Data Store** (Добавить (+) хранилище данных).
9. В поле имени введите **AzureSqlLinkedService**.
10. Щелкните **TYPE (settings required)** (Тип (обязательные настройки)) и выберите **Azure SQL Database** (База данных SQL Azure).
11. Теперь в колонке **New data store** (Создать хранилище данных) должны отобразить следующие дополнительные поля. Введите имя **server** (сервера) базы данных SQL Azure, имя **database** (базы данных), **user name** (имя пользователя) и **password** (пароль), а затем щелкните **ОК**.
	1. Введите **MarketingCampaigns** в поле **database** (база данных). Это база данных SQL Azure, созданная сценариями, которые вы выполнили в шаге 1. Следует убедиться, действительно ли эта база данных была создана сценариями (в случае, если возникли ошибки).
		
 		![Azure SQL Settings][image-data-factory-tutorial-azuresql-settings]

		Чтобы получить эти значения с портала управления Azure: щелкните строки подключения "View SQL Database" (Просмотр базы данных SQL) для базы данных MarketingCampaigns

		![Azure SQL Database Connection String][image-data-factory-tutorial-azuresql-database-connection-string]

12. Убедитесь, что все три созданные вами хранилища данных отображаются: **StorageLinkedService**, **HDInsightStorageLinkedService** и **AzureSqlLinkedService**.
13. Необходимо создать другую связанную службу, но в этот раз - вычислительную службу, а именно **Azure HDInsight cluster** (Кластер HDInsight Azure). Портал пока не поддерживает создание вычислительной связанной службы. Поэтому для создания этой связанной службы необходимо воспользоваться Azure PowerShell. 
14. Переключитесь в **Azure PowerShell**, если он уже открыт, (или) запустите **Azure PowerShell**.
15. Переключитесь в режим **AzureResourceManager**, поскольку командлеты фабрики данных Azure доступны в этом режиме.

		Switch-AzureMode AzureResourceManager

16. Перейдите к вложенной папке **LinkedServices** в **C:\ADFWalkthrough** (или) к папке из того расположения, где вы извлекли файлы.
17. Откройте **HDInsightLinkedService.json** в любом редакторе, замените выделенные данные информацией своего кластера и сохраните файл.

        "clusterUri": "https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "<hdiusername>",
        "location": "<hdiregion>",
        "password": "<hdipassword>",

18. Воспользуйтесь следующей командой, чтобы установить переменную $df в имя фабрики данных.

		$df = "LogProcessingFactory"
19. Чтобы создать связанную службу, воспользуйтесь командлетом **New-AzureDataFactoryLinkedService**, как показано далее. Начните с учетной записи хранения:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Если используются другие имена для ResourceGroupName, DataFactoryName или LinkedService, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу связанной службы, если не удается найти этот файл.
20. Вы должны увидеть все четыре связанные службы в колонке **Linked Services** (Связанные службы), как показано ниже. Если колонка "Связанные службы" не открыта, щелкните "Связанные службы" на странице **ФАБРИКА ДАННЫХ**, чтобы открыть **LogProcessingFactory**. Обновление колонки "Linked services" (Связанные службы) может продлиться несколько секунд.

	![Linked Services All][image-data-factory-tutorial-linkedservices-all]
 

### <a name="MainStep4"></a>Шаг 4: создание таблиц .
В этом шаге вы создадите следующие таблицы: 

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Tutorial End-to-End Flow][image-data-factory-tutorial-end-to-end-flow]
 
На рисунке выше изображены конвейеры (в средней строке) и таблицы (в верхней и нижней строках). 

Портал Azure пока не поддерживает создание наборов данных и таблиц, поэтому вам необходимо воспользоваться Azure PowerShell для создания таблиц в этом выпуске.

#### Создание таблиц

1.	В Azure PowerShell перейдите к папке **Tables** (Таблицы) (**C:\ADFWalkthrough\Tables**) из расположения, в которое вы извлекли примеры файлов. 
2.	Воспользуйтесь командлетом **New-AzureDataFactoryTable** для создания таблиц для файла **RawGameEventsTable**.json, как показано далее	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RawGameEventsTable.json

	Если вы используете другие имена для ResourceGroupName и DataFactoryName, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу таблицы, если не удается найти этот файл с помощью командлета.

3. Повторите предыдущий шаг, чтобы создать следующие таблицы:	
		
	- **PartitionedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionedGameEventsTable.json

	- **RefGeoCodeDictionaryTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefGeoCodeDictionaryTable.json

	- **RefMarketingCampaignTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\RefMarketingCampaignTable.json

	- **EnrichedGameEventsTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichedGameEventsTable.json

	- **MarketingCampaignEffectivenessSQLTable**

			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessSQLTable.json

	- **MarketingCampaignEffectivenessBlobTable**
			
			New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessBlobTable.json



4. На **портале предварительной версии Azure** щелкните **Наборы данных** в колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** и убедитесь, что отображаются все наборы данных (таблицы представляют собой прямоугольные наборы данных). 

	![Data Sets All][image-data-factory-tutorial-datasets-all]

	Вы также можете воспользоваться следующей командой из Azure PowerShell:
			
		Get-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df

	


### <a name="MainStep5"></a>Шаг 5: создание и планирование конвейеров
В этом шаге вы создадите следующие конвейеры: PartitionGameLogsPipeline, EnrichGameLogsPipeline и AnalyzeMarketingCampaignPipeline.

1. В **проводнике Windows** перейдите к вложенной папке **Pipelines** (Конвейеры) в папке **C:\ADFWalkthrough** (или из расположения, в которое вы извлекли примеры файлов).
2.	Откройте **PartitionGameLogsPipeline.json** в любом редакторе, замените выделенные данные информацией своей учетной записи хранения для хранилища данных и сохраните файл.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Повторите этот шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**.json (3 вхождения)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 вхождения)

	**ВАЖНО!** Убедитесь, что вы заменили все случаи <storageaccountname> именем вашей учетной записи хранения. 
 
4.  В **Azure PowerShell** перейдите к вложенной папке **Pipelines** (Конвейеры) в папке **C:\ADFWalkthrough** (или из расположения, в которое вы извлекли примеры файлов).
5.  Воспользуйтесь командлетом **New-AzureDataFactoryPipeline** для создания конвейеров для файла **PartitionGameLogspeline**.json, как показано далее	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\PartitionGameLogsPipeline.json

	Если вы используете другие имена для ResourceGroupName, DataFactoryName или имени конвейера, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу конвейера.
6. Повторите предыдущий шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\AnalyzeMarketingCampaignPipeline.json

7. Воспользуйтесь командлетом **Get-AzureDataFactoryPipeline**, чтобы получить список конвейеров.
			
		Get-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df

8. После создания конвейеров вы можете указать время, в которое будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.

Чтобы указать период активности конвейера, вы можете воспользоваться командлетом Set-AzureDataFactoryPipelineActivePeriod. В этом пошаговом руководстве используются демонстрационные данные от 05/01 до 05/05. Используйте 2014-05-01 как значение для StartDateTime. EndDateTime - необязательный параметр.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name PartitionGameLogsPipeline
  
9. Подтвердите, чтобы задать период активности конвейера.
			
			Подтверждение
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Повторите предыдущие два шага, чтобы задать период активности для следующих конвейеров.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline** 
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name AnalyzeMarketingCampaignPipeline

11. На **портале предварительной версии Azure** щелкните область **Конвейеры** (но не имена конвейеров) в колонке **Фабрика данных** для **LogProcessingFactory**. Вы должны увидеть созданные вами конвейеры.

	![All Pipelines][image-data-factory-tutorial-pipelines-all]

12. В колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** щелкните **Diagram** (Схема).

	![Diagram Link][image-data-factory-tutorial-diagram-link]

13. Вы можете изменить эту схему, а здесь показана измененная схема, в которой данные прямого ввода отображается вверху, а выходные данные - внизу. Вы можете видеть, что выходные данные конвейера **PartitionGameLogsPipeline** передаются в качестве входных данных в EnrichGameLogsPipeline, а выходные данные конвейера **EnrichGameLogsPipeline** передаются в **AnalyzeMarketingCampaignPipeline**. Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка.

	![Diagram View][image-data-factory-tutorial-diagram-view]

	**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, конвейеры и таблицы, а также начали рабочий процесс. 


### <a name="MainStep6"></a>Шаг 6: мониторинг конвейеров и срезов данных 

1.	Если колонка "Фабрика данных" для LogProcessingFactory не открыта, вы можете выполнить одно из следующих действий:
	1.	Щелкните **LogProcessingFactory** на **начальной панели**. При создании фабрики данных параметр **Add to Startboard** (Добавить на начальную панель) был автоматически включен.

		![Monitoring Startboard][image-data-factory-monitoring-startboard]

	2. Щелкните центр **BROWSE** (Обзор), затем щелкните **Everything** (Все элементы).
	 	
		![Monitoring Hub Everything][image-data-factory-monitoring-hub-everything]

	3. В колонке **Browse** (Обзор) выберите **Data factories** (Фабрики данных), а затем выберите **LogProcessingFactory** в колонке **Data factories** (Фабрики данных).

	![Monitoring Browse Datafactories][image-data-factory-monitoring-browse-datafactories]
2. Вы можете отслеживать фабрики данных несколькими способами. Начните с конвейеров или наборов данных. Давайте начнем с конвейеров, а затем пойдем дальше. 
3.	Щелкните **Конвейеры** в колонке **ФАБРИКА ДАННЫХ**. 
4.	Щелкните **PartitionGameLogsPipeline** в колонке "Конвейеры". 
5.	В колонке **КОНВЕЙЕР** для **PartitionGameLogsPipeline** вы можете видеть, что конвейер использует набор данных **RawGameEventsTable**.  Щелкните **RawGameEventsTable**.

	![Pipeline Consumed and Produced][image-data-factory-monitoring-pipeline-consumed-produced]

6. В колонке "Таблица" для **RawGameEventsTable** вы можете видеть все срезы. На следующем снимке экрана все срезы находятся в состоянии **Ready**, и нет проблемных срезов. Это означает, что данные готовы к обработке.	

	![RawGameEventsTable TABLE blade][image-data-factory-monitoring-raw-game-events-table]
 
7. Теперь в колонке **КОНВЕЙЕР** для **PartiionGameLogsPipeline** щелкните **Produced** (Произведено). 
8. Вы должны увидеть список наборов данных, которые производит этот конвейер: 
9. Щелкните таблицу **PartitionedGameEvents** в колонке **Produced datasets** (Произведенные наборы данных). 
10.	Убедитесь, что **состояние** всех срезов имеет значение **Ready**. 
11.	Щелкните один из срезов с состоянием **Ready**, чтобы просмотреть колонку **СРЕЗ ДАННЫХ** для этого среза.

	![RawGameEventsTable DATA SLICE blade][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Если произошла ошибка, вы увидите здесь состояние **Failed** (Ошибка).  Вы также можете увидеть оба среза с состоянием **Ready** или оба с состоянием **PendingValidation** в зависимости от того, насколько быстро обрабатываются срезы.
 
	Чтобы получить представление обо всех возможных состояниях срезов, см. [справочник разработчика фабрики данных Azure][developer-reference].

12.	В колонке **СРЕЗ ДАННЫХ** щелкните один из элементов списка **Activity Runs** (Выполняемые действия). Вы должны увидеть колонку "Activity Run" (Выполняемое действие) для этого фрагмента. Вы должны увидеть следующую колонку **ACTIVITY RUN DETAILS** (Подробности выполняемого действия).

	![Activity Run Details blade][image-data-factory-monitoring-activity-run-details]

13.	Щелкните **Скачать**, чтобы скачать файлы. Этот экран особенно полезен при устранении ошибок обработки HDInsight. 
	 
	
После завершения работы всех конвейеров вы сможете увидеть **MarketingCampaignEffectivenessTable** в базе данных SQL Azure **MarketingCampaigns** и просмотреть результаты. 

**Поздравляем!** Теперь вы можете отслеживать рабочие процессы и устранять возникающие в них неполадки. Вы узнали, как использовать фабрику данных Azure для обработки данных и получения аналитической информации.

	 

## Пошаговое руководство: использование локальных данных

**(рекомендуется)** Чтобы выполнить пошаговое руководство по созданию конвейера для перемещения данных из локального сервера SQL Server в хранилище больших двоичных объектов Azure, прочитайте и выполните инструкции, изложенные в статье [Включение конвейеров для работы с локальными данными][useonpremisesdatasources].


В этом пошаговом руководстве вы узнаете, как настроить среду для включения конвейера, работающего с вашими локальными данными.
 
В последнем шаге сценария обработки журнала из первого пошагового руководства был описан рабочий процесс "секционирование -> обогащение -> анализ", где выходные данные эффективности маркетинговой кампании были скопированы в базу данных SQL Azure. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Чтобы скопировать данные эффективности маркетинговой кампании из большого двоичного объекта Azure на локальный сервер SQL, необходимо создать дополнительную локальную связанную службу, таблицу и конвейер, используя тот же набор командлетов, представленный в первом пошаговом руководстве.

Вот какие шаги вы выполните в этом учебнике: 

1. [Шаг 1: создание шлюза управления данными](#OnPremStep1). Шлюз управления данными   - это агент клиента, который предоставляет доступ к локальным источникам данных в вашей организации из облака. Шлюз обеспечивает передачу данных между локальным сервером SQL Server и хранилищами данных Azure.	

	Вам необходимо установить по крайней мере один шлюз в корпоративной среде, а также зарегистрировать его в фабрике данных Azure, прежде чем добавлять локальную базу данных SQL Server как связанную службу в фабрику данных Azure.

2. [Шаг 2: создание связанной службы для локального сервера SQL Server](#OnPremStep2). В этом шаге вы сначала создадите на локальном компьютере SQL Server базу данных и таблицу, а затем создадите связанную службу: **OnPremSqlLinkedService**.  
3. [Шаг 3: создание таблицы и конвейера](#OnPremStep3). В этом шаге вы создадите таблицу **MarketingCampaignEffectivenessOnPremSQLTable** и конвейер **EgressDataToOnPremPipeline**. 

4. [Шаг 4: мониторинг конвейера и просмотр результата](#OnPremStep4). В этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.


### <a name="OnPremStep1"></a>Шаг 1: создание шлюза управления данными

Шлюз управления данными - это агент клиента, который предоставляет доступ к локальным источникам данных в вашей организации из облака. Шлюз обеспечивает передачу данных между локальным сервером SQL Server и хранилищами данных Azure.
  
Вам необходимо установить по крайней мере один шлюз в корпоративной среде, а также зарегистрировать его в фабрике данных Azure, прежде чем добавлять локальную базу данных SQL Server как связанную службу в фабрику данных Azure.

Если у вас есть шлюз данных, который можно использовать, пропустите этот шаг.

1.	Создайте логический шлюз данных. На **портале предварительной версии Azure** щелкните **Связанные службы** в колонке **ФАБРИКА ДАННЫХ**.
2.	На панели команд щелкните **Add (+) Data Gateway** (Добавить (+) шлюз данных).  
3.	В колонке **New data gateway** (Создать шлюз данных) щелкните **CREATE** (Создать).
4.	В колонке **Create** (Создать) введите **MyGateway** в поле **имени** шлюза данных.
5.	Щелкните **PICK A REGION** (Выбрать регион) и, при необходимости, измените регион. 
6.	Щелкните **ОК** в колонке **Create** (Создать). 
7.	Вы должны увидеть колонку **Configure** (Настройка). 
8.	В колонке **Настроить** щелкните **Установить непосредственно на этот компьютер**. Это приведет к скачиванию, установке и настройке шлюза на компьютере и к регистрации шлюза в службе. Если у вас на компьютере уже установлен существующий шлюз, который вы хотите связать с этим логическим шлюзом на портале, используйте ключ в этой колонке для повторной регистрации шлюза с помощью диспетчера конфигураций шлюза управления данными (предварительная версия).

	![Data Management Gateway Configuration Manager][image-data-factory-datamanagementgateway-configuration-manager]

9. Щелкните **ОК**, чтобы закрыть колонку **Configure** (Настройка), затем снова **ОК**, чтобы закрыть колонку **Create** (Создать). Подождите, пока состояние **MyGateway** в колонке **Linked Services** (Связанные службы) не изменится на **GOOD** (Хорошо). Вы также можете запустить инструмент **Диспетчер конфигураций шлюза управления данными (предварительная версия)**, чтобы подтвердить, что имя шлюза совпадает с именем на портале, а **состояние** - **Registered** (Зарегистрировано). Может потребоваться закрыть и снова открыть колонку "Linked Services" (Связанные службы), чтобы увидеть последнее состояние. Может пройти несколько минут, пока последнее состояние не обновится на экране.	

### <a name="OnPremStep2"></a>Шаг 2: создание связанной службы для локального сервера SQL Server

В этом шаге вы сначала создадите на локальном компьютере SQL Server необходимые вам базу данных и таблицу, а затем создадите связанную службу.

#### Подготовка локальной базы данных и таблицы

Прежде всего необходимо создать базу данных SQL Server, таблицу, пользовательские типы и хранимые процедуры. Они будут использоваться для перемещения результатов **MarketingCampaignEffectiveness** из большого двоичного объекта Azure в базу данных SQL Server.

1.	В **проводнике Windows** перейдите к вложенной папке **OnPremises** в папке **C:\ADFWalkthrough** (или в расположении, в которое вы извлекли примеры файлов).
2.	Откройте **prepareOnPremDatabase&Table.ps1** в любом редакторе, замените выделенные данные информацией своего сервера SQL Server и сохраните файл (укажите данные **SQL authentication** (Аутентификация SQL)). Для примера в этом учебнике включите аутентификацию SQL для базы данных. 
			
		$dbServerName = "<servername>"
		$dbUserName = "<username>"
		$dbPassword = "<password>"

3. В **Azure PowerShell** перейдите к папке **C:\ADFWalkthrough\OnPremises**.
4.	Запустите **prepareOnPremDatabase&Table.ps1** **(либо & в двойных кавычках, либо как показано ниже)**.
			
		& '.\prepareOnPremDatabase&Table.ps1'

5. Once the script executes successfully, you will see the following:	
			
		PS E:\ADF\ADFWalkthrough\OnPremises> & '.\prepareOnPremDatabase&Table.ps1'
		6/10/2014 10:12:33 PM Script to create sample on-premises SQL Server Database and Table
		6/10/2014 10:12:33 PM Creating the database [MarketingCampaigns], table and stored procedure on [.]...
		6/10/2014 10:12:33 PM Connecting as user [sa]
		6/10/2014 10:12:33 PM Summary:
		6/10/2014 10:12:33 PM 1. Database 'MarketingCampaigns' created.
		6/10/2014 10:12:33 PM 2. 'MarketingCampaignEffectiveness' table and stored procedure 


#### Создание связанной службы

1.	На **портале предварительной версии Azure** щелкните поле **Связанные службы** в колонке **Фабрика данных** для **LogProcessingFactory**.
2.	В колонке **Связанные службы** щелкните **Add (+) Data Store** (Добавить (+) хранилище данных).
3.	В колонке **New data store** (Создать хранилище данных) введите **OnPremSqlLinkedService** в поле **Name** (Имя). 
4.	Щелкните **Type (Settings required)** (Тип (обязательные настройки) и выберите **SQL Server**. Теперь вы должны увидеть настройки **DATA GATEWAY** (Шлюз данных), **Server** (Сервер), **Database** (База данных) и **CREDENTIALS** (Учетные данные) в колонке **New data store** (Создать хранилище данных). 
5.	Щелкните **DATA GATEWAY (configure required settings)** (Шлюз данных (введите обязательные настройки)) и выберите шлюз **MyGateway**, который вы создали ранее. 
6.	Введите **имя** сервера базы данных, на котором размещена база данных **MarketingCampaigns**. 
7.	Введите **MarketingCampaigns** в поле "Database" (База данных). 
8.	Щелкните **CREDENTIALS** (Учетные данные). 
9.	В колонке **Credentials** (Учетные данные) выберите **Click here to set credentials securely** (Щелкните здесь, чтобы задать учетные данные безопасно).
10.	Это действие устанавливает приложение, запускаемое одним щелчком при первом входе, и открывает диалоговое окно **Setting Credentials** (Установка учетных данных). 
11.	В диалоговом окне **Setting Credentials** (Установка учетных данных) введите **User Name** (Имя пользователя) и **Password** (Пароль), затем щелкните **ОК**. Подождите, пока закроется диалоговое окно. 
12.	Щелкните **ОК** в колонке **New data store** (Создать хранилище данных). 
13.	Убедитесь, что в колонке **Linked Services** (Связанные службы) служба **OnPremSqlLinkedService** отображается в списке, и **состояние** связанной службы - **Good** (Хорошо).

### <a name="OnPremStep3"></a>Шаг 3: создание таблицы и конвейера

#### Создание локальной логической таблицы

1.	В **Azure PowerShell** перейдите к папке **C:\ADFWalkthrough\OnPremises**. 
2.	Воспользуйтесь командлетом **New-AzureDataFactoryTable**, чтобы создать таблицы, как показано для **MarketingCampaignEffectivenessOnPremSQLTable.json**.

			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df -File .\MarketingCampaignEffectivenessOnPremSQLTable.json
	 
#### Создание конвейера для копирования данных из большого двоичного объекта Azure на сервер SQL Server

1.	Воспользуйтесь командлетом **New-AzureDataFactoryPipeline**, чтобы создать коннвейер, как показано для **EgressDataToOnPremPipeline.json**.

			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df -File .\EgressDataToOnPremPipeline.json
	 
2. Воспользуйтесь командлетом **Set-AzureDataFactoryPipelineActivePeriod**, чтобы указать период активности для **EgressDataToOnPremPipeline**.

			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01 -EndDateTime 2014-05-05 -Name EgressDataToOnPremPipeline

	Чтобы продолжить, нажмите **'Y'** (Да).
	
### <a name="OnPremStep4"></a>Шаг 4: мониторинг конвейера и просмотр результата

Теперь вы можете выполнить те же действия, которые представлены в разделе [Шаг 6: мониторинг таблиц и конвейеров](#MainStep6)  , чтобы отслеживать новый конвейер и срезы данных для новой локальной таблицы ADF.
 
Когда вы увидите, что состояние среза таблицы **MarketingCampaignEffectivenessOnPremSQLTable** изменилось на "Ready", это означает, что конвейер закончил выполнение среза. Чтобы просмотреть результаты, запросите таблицу **MarketingCampaignEffectiveness** в базе данных **MarketingCampaigns** на вашем сервере SQL Server.
 
Поздравляем! Вы успешно прошли пошаговое руководство по использованию локальных данных. 
 
## См. также

Статья | Описание
------ | ---------------
[Включение конвейеров для работы с локальными данными][useonpremisesdatasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Использование сценариев pig и hive с фабрикой данных][usepigandhive] | В этой статье показано, как использовать действия HDInsight для выполнения сценариев hive и pig, чтобы обработать входные данные и получить выходные данные. 
[Использование настраиваемых действий в фабрике данных][use-custom-activities] | Эта статья представляет собой пошаговое руководство с указаниями по созданию настраиваемых действий и их использованию в конвейере. 
[Мониторинг и управление фабрикой данных Azure с помощью PowerShell][monitor-manage-using-powershell] | В этой статье описывается, как выполнять мониторинг фабрики данных Azure с помощью командлетов Azure PowerShell. Вы можете использовать примеры в статье на ADFTutorialDataFactory.
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure. Вы можете использовать руководство в этой статье на ADFTutorialDataFactory путем введения ошибки (удалив таблицу в базе данных Azure SQL). 
[Справочник разработчика фабрик данных Azure][developer-reference] | Справочник разработчика содержит разнообразные материалы по использованию командлетов, сценариев JSON, функций и т. д.
[Справочник по командлетам фабрики данных Azure][cmdlet-reference] | Этот справочник содержит подробную информацию о всех командлетах фабрики данных. 

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfintroduction]: ../data-factory-introduction
[useonpremisesdatasources]: ../data-factory-use-onpremises-datasources
[usepigandhive]: ../data-factory-pig-hive-activities

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/ru-ru/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx

[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


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

<!--HONumber=46--> 
