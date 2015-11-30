<properties 
	pageTitle="Перемещение и обработка файлов журнала с помощью фабрики данных Azure (на портале Azure)" 
	description="В этом подробном учебнике описывается реалистичный сценарий, который реализуется с помощью редактора фабрики данных и службы фабрики данных Azure на портале Azure." 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Учебник. Оценка эффективности маркетинговой кампании  
Contoso — это компания, которая создает игры для нескольких платформ: игровых консолей, карманных устройств и персональных компьютеров (ПК). Эти игры создают большое количество журналов. Компания Contoso собирает и анализирует эти журналы, выявляя таким образом предпочтения клиентов, их демографические характеристики, типичное поведение и т. д. Это нужно, чтобы выявлять перспективы дополнительных и перекрестных продаж, разрабатывать новые востребованные компоненты, развивать бизнес и предоставлять больше возможностей клиентам.

В этом учебнике мы создадим конвейеры фабрики данных для оценки эффективности маркетинговой кампании, которую недавно запустила компания Contoso. Конвейеры собирают журналы, обрабатывают данные, дополняют их эталонными данными и преобразуют данные. Нам потребуются три конвейера.

1.	Конвейер **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища больших двоичных объектов и создает разделы на основе года, месяца и дня.
2.	Конвейер **EnrichGameLogsPipeline** объединяет секционированные события игры с данными геокода и дополняет их путем сопоставления IP-адресов с соответствующими географическими расположениями.
3.	Конвейер **AnalyzeMarketingCampaignPipeline** использует обогащенные данные и обрабатывает их вместе с рекламными данными для создания конечного результата, содержащего оценку эффективности маркетинговой кампании.

## Подготовка к работе с учебником
1.	Чтобы получить общее представление о фабрике данных Azure и об основных понятиях, прочтите статью [Введение в фабрику данных Azure][adfintroduction].
2.	Для выполнения инструкций этого учебника необходимо иметь подписку Azure. Сведения о получении подписки см. в разделе [Варианты приобретения](http://azure.microsoft.com/pricing/purchase-options/), [Предложения для участников](http://azure.microsoft.com/pricing/member-offers/) или [Бесплатное пробное использование](http://azure.microsoft.com/pricing/free-trial/).
3.	Скачайте и установите на своем компьютере [Azure PowerShell][download-azure-powershell]. Командлеты фабрики данных будут использоваться для отправки демонстрационных данных и сценариев Pig и Hive в хранилище больших двоичных объектов. 
2.	**(рекомендуется)** Чтобы ознакомиться с порталом и командлетами, изучите и выполните простые инструкции, изложенные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
3.	**(рекомендуется)** Изучите пошаговое руководство в статье [Использование Pig и Hive с фабрикой данных Azure][usepigandhive], чтобы узнать, как создать конвейер для перемещения данных из локального источника данных в хранилище BLOB-объектов Azure.
4.	Загрузите файлы [ADFWalkthrough][adfwalkthrough-download] в папку **C:\\ADFWalkthrough**, **сохраняя такую структуру папок**.
	- Папка **Pipelines** включает JSON-файлы, содержащие определение конвейеров.
	- **Tables** включает JSON-файлы, содержащие определение таблиц.
	- **LinkedServices** включает JSON-файлы, содержащие определение вашего кластера хранилища и вычислений (HDInsight). 
	- **Scripts** содержит сценарии Hive и Pig, которые вызываются из конвейеров и используются для обработки данных.
	- **SampleData** содержит демонстрационные данные для этого пошагового руководства.
	- **OnPremises** содержит сценарий и JSON-файлы, используемые для демонстрации доступа к вашим локальным данным.
	- Сценарий **uploadSampleDataAndScripts.ps1** отправляет демонстрационные данные и сценарии в Azure.
5. Убедитесь, что вы создали следующие ресурсы Azure:			
	- Учетная запись хранения Azure.
	- База данных SQL Azure
	- Кластер Azure HDInsight 3.1 или более поздней версии (или кластер HDInsight по запросу, который служба данных фабрики создает автоматически).	
7. После создания ресурсов Azure убедитесь, что у вас есть информация, необходимая для подключения к каждому из этих ресурсов.
 	- **Учетная запись хранилища Azure** — имя и ключ учетной записи.  
	- **База данных SQL Azure** — сервер, база данных, имя пользователя и пароль.
	- **Кластер HDInsight Azure** — имя кластера HDInsight, имя пользователя, пароль, имя и ключ учетной записи хранилища Azure, связанной с этим кластером. Этот шаг можно пропустить, если вы хотите использовать кластер HDInsight по запросу вместо собственного кластера HDInsight.  
8. Запустите **Azure PowerShell** и выполните приведенные ниже команды. Не закрывайте Azure PowerShell. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.
	- Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые применяются для входа на предварительную версию портала Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure.	

## Обзор
Ниже изображен сквозной рабочий процесс:

![Полный процесс учебника][image-data-factory-tutorial-end-to-end-flow]

1. Конвейер **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает разделы на основе года, месяца и дня (PartitionedGameEventsTable).
2. Конвейер **EnrichGameLogsPipeline** объединяет секционированные события игры (таблица PartitionedGameEvents, которая является результатом работы конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и дополняет их, сопоставляя IP-адреса с соответствующими географическими расположениями (EnrichedGameEventsTable).
3. Конвейер **AnalyzeMarketingCampaignPipeline** использует обогащенные данные (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательный результат эффективности маркетинговой кампании, который копируется в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для последующего анализа.
    
## Пошаговое руководство. Создание, развертывание и мониторинг рабочих процессов
1. [Шаг 1. Отправка демонстрационных данных и скриптов](#MainStep1). В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые будут выполняться рабочими процессами. Выполняемые вами сценарии также создают базу данных SQL Azure (с именем MarketingCampaigns), таблицы, пользовательские типы и хранимые процедуры.
2. [Шаг 2. Создание фабрики данных Azure](#MainStep2). В этом шаге вы создадите фабрику данных Azure с именем LogProcessingFactory.
3. [Шаг 3. Создание связанных служб](#MainStep3). В этом шаге вы создадите следующие связанные службы: 
	
	- 	**StorageLinkedService**. Связывает расположение службы хранилища Azure, которая содержит необработанные события игры, секционированные события игры, обогащенные события игры, информацию об эффективности маркетинговой кампании, ссылочные данные геокода и ссылочные данные маркетинговой кампании, с фабрикой LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Связывает базу данных SQL Azure, которая содержит информацию об эффективности маркетинговой кампании. 
	- 	**HDInsightStorageLinkedService**. Связывает хранилище больших двоичных объектов Azure, связанное с кластером HDInsight, на который ссылается HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Связывает кластер HDInsight Azure с фабрикой LogProcessingFactory. Этот кластер используется для выполнения обработки данных pig/hive. 
 		
4. [Шаг 4. Создание таблиц](#MainStep4). В этом шаге вы создадите следующие таблицы:
	
	- **RawGameEventsTable**. Эта таблица указывает расположение необработанных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Эта таблица указывает расположение секционированных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Эта таблица указывает расположение ссылочных данных геокода в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Эта таблица указывает расположение ссылочных данных маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Эта таблица указывает расположение обогащенных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Эта таблица указывает таблицу SQL (MarketingCampaignEffectiveness) в базе данных SQL Azure, определенную службой AzureSqlLinkedService, которая содержит данные об эффективности маркетинговой кампании. 
	- **MarketingCampaignEffectivenessBlobTable**. Эта таблица указывает расположение данных об эффективности маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Шаг 5. Создание и планирование конвейеров](#MainStep5). В этом шаге вы создадите следующие конвейеры:
	- **PartitionGameLogsPipeline**. Конвейер считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секции, основываясь на годе, месяце и дне (PartitionedGameEventsTable). 


		![Конвейер PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Конвейер объединяет секционированные события игры (таблица PartitionedGameEvents, которая является выходными данными конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и обогащает данные путем сопоставления IP-адреса с соответствующим географическим положением (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Конвейер использует обогащенные данные событий игры (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательные выходные данные эффективности маркетинговой кампании, которые копируются в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для аналитики


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Шаг 6. Мониторинг конвейеров и срезов данных](#MainStep6). На этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.

## <a name="MainStep1"></a>Шаг 1. Отправка демонстрационных данных и скриптов.
В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые вызываются рабочими процессами. Выполняемые вами сценарии создают также базу данных SQL Azure с именем **MarketingCampaigns**, таблицы, пользовательские типы и хранимые процедуры.

Таблицы, пользовательские типы и хранимые процедуры используются при перемещении результатов эффективности маркетинговой кампании из хранилища больших двоичных объектов Azure в базу данных SQL Azure.

1. Откройте файл **uploadSampleDataAndScripts.ps1** в папке **C:\\ADFWalkthrough** (или в папке, которая содержит извлеченные файлы) в любом редакторе, замените выделенные данные значениями своего кластера и сохраните файл.


		$storageAccount = <storage account name>
		$storageKey = <storage account key>
		$azuresqlServer = <sql azure server>.database.windows.net
		$azuresqlUser = <sql azure user>@<sql azure server>
		$azuresqlPassword = <sql azure password>

 
	Для этого сценария требуется установить на компьютере служебную программу sqlcmd. Если у вас установлен SQL Server, то эта программа у вас уже есть. В противном случае вам нужно ее [скачать][sqlcmd-install] и установить.
	
	Или вы можете использовать файлы в папке C:\\ADFWalkthrough\\Scripts для отправки сценариев или примеров файлов Pig либо Hive в контейнер adfwalkthrough в хранилище больших двоичных объектов. Кроме того, вы можете создать таблицу MarketingCampaignEffectiveness в базе данных MarketingCamapaigns Azure SQL.
   
2. Убедитесь, что вашему локальному компьютеру разрешен доступ к базе данных SQL Azure. Чтобы разрешить доступ, используйте **портал управления Azure** или **sp\_set\_firewall\_rule** в базе данных master, чтобы создать правило брандмауэра для IP-адреса вашего компьютера. Может потребоваться до пяти минут, чтобы это изменение вступило в силу. См. раздел [Настройка правил брандмауэра для SQL Azure][azure-sql-firewall].
4. В Azure PowerShell перейдите в расположение, в которое вы извлекли примеры файлов (например, **C:\\ADFWalkthrough**).
5. Запустите **uploadSampleDataAndScripts.ps1**. 
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

## <a name="MainStep2"></a>Шаг 2. Создание фабрики данных Azure.
На этом шаге вы создадите фабрику данных Azure с именем **LogProcessingFactory**.

1.	Войдите на [портал предварительной версии Azure][azure-preview-portal], в нижнем левом углу щелкните **СОЗДАТЬ**, выберите в колонке **Создание** пункт **Анализ данных**, а затем в колонке **Анализ данных** щелкните **Фабрика данных**. 

	![Создать -> Фабрика данных][image-data-factory-new-datafactory-menu]

5. В колонке **Создание фабрики данных** введите **LogProcessingFactory** в поле **Имя**.

	![Колонка "Фабрика данных"][image-data-factory-tutorial-new-datafactory-blade]

6. Если вы еще не создали группу ресурсов Azure с именем **ADF**, сделайте вот что.
	1. Щелкните **RESOURCE GROUP NAME** (Имя группы ресурсов), затем щелкните **Создать новую группу ресурсов**.
	
		![Колонка "Группа ресурсов"][image-data-factory-tutorial-resourcegroup-blade]
	2. В колонке **Create resource group** (Создать группу ресурсов) введите **ADF** в качестве имени группы ресурсов, а затем нажмите **ОК**.
	
		![Создать группу ресурсов][image-data-factory-tutorial-create-resourcegroup]
7. Выберите **ADF** в качестве **ИМЕНИ ГРУППЫ РЕСУРСОВ**.  
8.	Обратите внимание, что в колонке **New data factory** (Создать фабрику данных) флажок **Add to Startboard** (Добавить на начальную панель) установлен по умолчанию. Это добавляет связь с фабрикой данных на начальной панели (то, что вы видите при входе на портал предварительной версии Azure).

	![Создать колонку "Фабрика данных"][image-data-factory-tutorial-create-datafactory]

9.	В колонке **New data factory** (Создать фабрику данных) нажмите **Создать**, чтобы создать фабрику данных.
10.	После этого должна появиться колонка **ФАБРИКА ДАННЫХ** с заголовком **LogProcessingFactory**.

	![Домашняя страница фабрики данных][image-data-factory-tutorial-datafactory-homepage]

	
	Если вы ее не видите, выполните одно из следующих действий:

	- Щелкните **LogProcessingFactory** на **начальной панели** (домашней странице).
	- Нажмите **ОБЗОР** слева, затем последовательно выберите пункты **Все**, **Фабрики данных** и выберите нужную фабрику данных.
 
	Имя фабрики данных Azure должно быть глобально уникальным. Если возникает ошибка **Имя фабрики данных LogProcessingFactory недоступно**, измените это имя (например, на ваше\_имяLogProcessingFactory). Используйте выбранное имя вместо LogProcessingFactory при выполнении действий из этого учебника.
 
## <a name="MainStep3"></a>Шаг 3. Создание связанных служб.

> [AZURE.NOTE]В этой статье для создания связанных служб, таблиц и конвейеров используется портал Azure, в частности редактор фабрики данных. Если вы хотите выполнить описанные в учебнике действия с помощью Azure PowerShell, используйте аналогичный [учебник для Azure PowerShell][adftutorial-using-powershell].

В этом шаге вы создадите следующие связанные службы:

- StorageLinkedService;
- AzureSqlLinkedService;
- HDInsightStorageLinkedService;
- HDInsightLinkedService. 

### Создание StorageLinkedService и HDInsightStorageLinkedService

1.	В колонке **ФАБРИКА ДАННЫХ** щелкните плитку **Разработка и развертывание**, чтобы запустить **Редактор** фабрики данных.

	![Плитка "Создание и развертывание"][image-author-deploy-tile]

	Подробный обзор редактора фабрики данных см. в разделе [Редактор фабрики данных][data-factory-editor].

2.  В **Редакторе** нажмите кнопку **Создать хранилище данных** на панели инструментов и выберите в раскрывающемся меню пункт **Хранилище Azure**. На панели справа должен появиться шаблон JSON для создания связанной службы хранилища Azure.
	
	![Кнопка "Создать хранилище данных" в редакторе][image-editor-newdatastore-button]

3. Замените **accountname** и **accountkey** значениями имени и ключа учетной записи для вашей учетной записи Azure.

	![Хранилище больших двоичных объектов JSON в редакторе][image-editor-blob-storage-json]
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

4. Нажмите кнопку **Развернуть** на панели инструментов, чтобы развернуть StorageLinkedService. Убедитесь, что в строке заголовка отображается сообщение **СВЯЗАННАЯ СЛУЖБА УСПЕШНО СОЗДАНА**.

	![Развертывание хранилища больших двоичных объектов в редакторе][image-editor-blob-storage-deploy]

5. Повторите эти шаги для создания другой связанной службы хранилища Azure с именем **HDInsightStorageLinkedService** для хранилища, связанного с вашим кластером HDInsight. В сценарии JSON для этой связанной службы измените значение свойства **name** на **HDInsightStorageLinkedService**.

### Создание AzureSqlLinkedService
1. В **редакторе фабрики данных** нажмите кнопку **Создать хранилище данных** на панели инструментов и выберите в раскрывающемся меню пункт **База данных SQL Azure**. В правой панели должен появиться шаблон JSON для создания связанной службы SQL Azure.
2. Замените **servername**, ****username@servername** и **password** на имя вашего сервера SQL Azure, имя учетной записи пользователя и пароль.
3. Замените **databasename** на **MarketingCampaigns**. Это база данных SQL Azure, созданная в результате выполнения сценариев на шаге 1. При возникновении ошибок убедитесь, что эта база данных действительно была создана сценариями. 
3. Щелкните **Развернуть** на панели инструментов, чтобы создать и развернуть AzureSqlLinkedService.

### Создание HDInsightLinkedService
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу.

#### Использование кластера HDInsight по запросу
1. Щелкните **Создать вычисление** на панели команд и выберите в меню пункт **Кластер HDInsight по запросу**.
2. В сценарии JSON выполните такие действия: 
	1. В свойстве **clusterSize** укажите размер кластера HDInsight.
	2. В свойстве **jobsContainer** укажите имя контейнера, в котором по умолчанию будут сохраняться журналы кластера. Для целей данного учебника введите **adfjobscontainer**.
	3. В свойстве **timeToLive** укажите, как долго может простаивать клиент, прежде чем он будет удален. 
	4. В свойстве **version** укажите версию HDInsight, которую хотите использовать. Если исключить это свойство, будет использоваться последняя версия.  
	5. Для **linkedServiceName** укажите службу **HDInsightStorageLinkedService**, которая была создана в учебнике по началу работы. 

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

		Обратите внимание, что в качестве **типа** связанной службы задано значение **HDInsightOnDemandLinkedService**.

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.
   
   
#### Использование собственного кластера HDInsight 

1. Щелкните **Создать вычисление** на панели команд и выберите в меню пункт **Кластер HDInsight**.
2. В сценарии JSON выполните такие действия: 
	1. В свойстве **clusterUri** укажите URL-адрес вашего кластера HDInsight, Например, https://<clustername>.azurehdinsight.net/     
	2. В свойстве **UserName** введите имя пользователя, у которого есть доступ к кластеру HDInsight.
	3. В свойстве **Password** укажите пароль этого пользователя. 
	4. В свойстве **LinkedServiceName** введите **StorageLinkedService**. Это связанная служба, которая была создана в учебнике по началу работы. 

	Обратите внимание, что в качестве **типа** связанной службы задано значение **HDInsightBYOCLinkedService** (BYOC означает Bring Your Own Cluster — принеси свой кластер).

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.


## <a name="MainStep4"></a>Шаг 4. Создание таблиц.
 
На этом шаге вы создадите такие таблицы:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Полный процесс учебника][image-data-factory-tutorial-end-to-end-flow]
 
На рисунке выше изображены конвейеры (в средней строке) и таблицы (в верхней и нижней строках).

### Создание таблиц
	
1. В **Редакторе** фабрики данных нажмите на панели инструментов кнопку **Создать набор данных** и выберите в раскрывающемся меню пункт **Хранилище больших двоичных объектов Azure**. 
2. Замените JSON в области справа на сценарий JSON из файла **RawGameEventsTable.json** в папке **C:\\ADFWalkthrough\\Tables**.
3. Чтобы создать и развернуть таблицу, нажмите кнопку **Развернуть** на панели инструментов. Убедитесь, что на панели заголовка в редакторе отображается сообщение **ТАБЛИЦА УСПЕШНО СОЗДАНА**.
4. Повторите шаги 1–3, используя содержимое таких файлов: 
	1. PartitionedGameEventsTable.json;
	2. RefGeoCodeDictionaryTable.json;
	3. RefMarketingCampaignTable.json;
	4. EnrichedGameEventsTable.json;
	5. MarketingCampaignEffectivenessBlobTable.json. 
5. Повторите шаги 1–3, используя содержимое приведенного ниже файла. Но после нажатия кнопки **Создать набор данных** выберите **Azure Sql**.
	1. MarketingCampaignEffectivenessSQLTable.json
	

## <a name="MainStep5"></a>Шаг 5. Создание и планирование конвейеров
В этом шаге вы создадите следующие конвейеры:

- PartitionGameLogsPipeline
- EnrichGameLogsPipeline
- AnalyzeMarketingCampaignPipeline

### Создание конвейеров

1. В **редакторе фабрики данных**, щелкните кнопку **Создать конвейер** на панели инструментов. Нажмите **... (многоточие)** на панели инструментов, если кнопка не отображается. Кроме того, можно щелкнуть правой кнопкой мыши **Конвейеры** в древовидном представлении и выбрать **Создать конвейер**.
2. Замените JSON в области справа на сценарий JSON из файла **PartitionGameLogsPipeline.json** в папке **C:\\ADFWalkthrough\\Pipelines**.
3. Добавьте запятую **(,)** в конце закрывающей квадратной скобки **(])** в JSON и после этой закрывающей квадратной скобки добавьте следующие три строки. 

        "start": "2014-05-01T00:00:00Z",
        "end": "2014-05-05T00:00:00Z",
        "isPaused": false

	Обратите внимание, что время начала и окончания задано как 1.05.2014 и 5.05.2014, поскольку образец данных в этом пошаговом руководстве получен с 1.05.2014 по 5.05.2014.
 
3. Щелкните **Развернуть** на панели инструментов для создания и развертывания конвейера. Убедитесь, что в заголовке окна редактора отображается сообщение **КОНВЕЙЕР УСПЕШНО СОЗДАН**.
4. Повторите шаги 1–3, используя содержимое таких файлов: 
	1. EnrichGameLogsPipeline.json;
	2. AnalyzeMarketingCampaignPipeline.json.
4. Закройте колонки фабрики данных, нажав значок **X** в правом верхнем углу, чтобы перейти на домашнюю страницу вашей фабрики данных (в колонку **ФАБРИКА ДАННЫХ**).

### Представление схемы

1. В колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** щелкните элемент **Схема**. 

	![Ссылка на схему][image-data-factory-tutorial-diagram-link]

2. Вы можете изменить эту схему, а здесь показана измененная схема, в которой данные прямого ввода отображается вверху, а выходные данные — внизу. Как вы видите, выходные данные конвейера ** PartitionGameLogsPipeline** передаются в качестве входных данных в конвейер EnrichGameLogsPipeline, а выходные данные **EnrichGameLogsPipeline** передаются в конвейер **AnalyzeMarketingCampaignPipeline**. Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка.

	![Представление схемы][image-data-factory-tutorial-diagram-view]

3. Щелкните правой кнопкой мыши **AnalyzeMarketingCampaignPipeline** и выберите команду **Открыть конвейер**. Должны отобразиться все действия в конвейере вместе с их входными и выходными наборами данных.
 
	![Открыть конвейер](./media/data-factory-tutorial/AnalyzeMarketingCampaignPipeline-OpenPipeline.png)

	Щелкните элемент **Фабрика данных** в строке навигации в левом верхнем углу, чтобы перейти в представление схемы.


**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, конвейеры и таблицы, а также начали рабочий процесс.


## <a name="MainStep6"></a> Шаг 6. Мониторинг конвейеров и срезов данных 

1.	Если колонка **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** не открыта, выполните одно из следующих действий.
	1.	Щелкните **LogProcessingFactory** на **начальной панели**. При создании фабрики данных флажок **Add to Startboard** (Добавить на начальную панель) был автоматически установлен.

		![Мониторинг — начальная панель][image-data-factory-monitoring-startboard]

	2. Щелкните раздел **ОБЗОР**, а затем выберите **Everything** (Все элементы).
	 	
		![Мониторинг. Раздел "Все элементы"][image-data-factory-monitoring-hub-everything]

		В колонке **Обзор** выберите пункт **Фабрики данных**, а затем в колонке **Фабрики данных** выберите элемент **LogProcessingFactory**.

		![Мониторинг. Обзор фабрик данных][image-data-factory-monitoring-browse-datafactories]
2. Вы можете отслеживать фабрики данных несколькими способами. Начните с конвейеров или наборов данных. Давайте начнем с конвейеров, а затем пойдем дальше. 
3.	Щелкните элемент **Конвейеры** в колонке **ФАБРИКА ДАННЫХ**. 
4.	Щелкните элемент **PartitionGameLogsPipeline** в колонке "Конвейеры". 
5.	В колонке **КОНВЕЙЕР** для **PartitionGameLogsPipeline** можно увидеть, что этот конвейер использует набор данных **RawGameEventsTable**. Щелкните **RawGameEventsTable**.

	![Полученные и созданные конвейеры][image-data-factory-monitoring-pipeline-consumed-produced]

6. В колонке "ТАБЛИЦА" для **RawGameEventsTable** отображаются все срезы. На следующем снимке экрана все срезы находятся в состоянии **Ready**, а проблемных срезов нет. Это означает, что данные готовы к обработке.

	![Колонка "ТАБЛИЦА" RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]

	Оба списка, **Недавно обновленные срезы** и **Последние срезы с ошибками**, сортируются по **последнему времени обновления**. Время обновления среза изменяется в таких ситуациях:

	-  Вы обновляете состояние среза вручную, например используя командлет **Set-AzureDataFactorySliceStatus** или щелкнув **ВЫПОЛНИТЬ** в колонке **СРЕЗ** для этого среза.
	-  В ходе выполнения состояние среза меняется (например, выполнение началось, выполнение завершилось со сбоем, выполнение завершилось успешно и т. п.).
 
	Щелкните заголовок списков или **... (многоточие)**, чтобы просмотреть расширенный список срезов. Чтобы отфильтровать срезы, выберите пункт **Фильтр** на панели инструментов.
	
	Чтобы вместо этого просмотреть срезы данных, отсортированные по времени начала и окончания среза, щелкните плитку **Срезы данных (по времени среза)**.

	![Срезы данных по времени среза][DataSlicesBySliceTime]
 
7. Теперь в колонке **КОНВЕЙЕР** для **PartiionGameLogsPipeline** щелкните элемент **Созданные**.
8. Вы должны увидеть список наборов данных, которые производит этот конвейер: 
9. Щелкните таблицу **PartitionedGameEvents** в колонке **Produced datasets** (Созданные наборы данных). 
10.	Убедитесь, что **состояние** всех срезов имеет значение **Ready** (Готов). 
11.	Щелкните один из срезов с состоянием **Ready**, чтобы просмотреть колонку **СРЕЗ ДАННЫХ** для этого среза.

	![Колонка "СРЕЗ ДАННЫХ" RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Если произошла ошибка, вы увидите здесь состояние **Ошибка**. Вы также увидите оба среза с состоянием **Готово** или **Ожидание проверки**, в зависимости от того, насколько быстро обрабатываются срезы.

	Если срез не находится в состоянии **Готов**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**.
 
	Чтобы получить представление обо всех возможных состояниях срезов, см. [Справочник разработчика фабрики данных Azure][developer-reference].

12.	В колонке **СРЕЗ ДАННЫХ** щелкните один из элементов списка **Activity Runs** (Выполняемые действия). Вы должны увидеть колонку "Activity Run" (Выполняемое действие) для этого фрагмента. Вы должны увидеть следующую колонку **ACTIVITY RUN DETAILS** (СВЕДЕНИЯ О ВЫПОЛНЯЕМЫХ ДЕЙСТВИЯХ).

	![Колонка "Сведения о выполняемых действиях"][image-data-factory-monitoring-activity-run-details]

13.	Щелкните **Скачать**, чтобы скачать файлы. Этот экран особенно полезен при устранении ошибок обработки HDInsight.
	 
	
После завершения работы всех конвейеров вы сможете просмотреть таблицу **MarketingCampaignEffectivenessTable** в базе данных SQL Azure **MarketingCampaigns**, чтобы увидеть результаты.

**Поздравляем!** Теперь вы можете отслеживать рабочие процессы и устранять возникающие в них неполадки. Вы узнали, как использовать фабрику данных Azure для обработки данных и получения аналитической информации.

## Дополнение к учебнику: использование локальных данных
Когда выполнялся последний шаг сценария обработки журналов, содержащегося в этом руководстве, результаты оценки эффективности, относящиеся к маркетинговой кампании, были скопированы в базу данных SQL Azure. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Чтобы скопировать данные об эффективности маркетинговой кампании из большого двоичного объекта Azure на локальный сервер SQL Server, необходимо создать дополнительную локальную связанную службу, таблицу и конвейер, о которых говорилось в этой статье.

Чтобы узнать, как создать конвейер для копирования данных об эффективности маркетинговой кампании в локальную базу данных SQL Server, изучите статью [Пошаговое руководство. Использование локального источника данных][tutorial-onpremises].


[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[data-factory-editor]: data-factory-editor.md

[adfsamples]: data-factory-samples.md
[adfgetstarted]: data-factory-get-started.md
[adftutorial-using-powershell]: data-factory-tutorial-using-powershell.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises]: data-factory-tutorial-extend-onpremises.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: https://azure.microsoft.com/documentation/articles/sql-database-configure-firewall-settings/


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

<!---HONumber=Nov15_HO4-->