<properties 
	pageTitle="Перемещение и обработка файлов журнала с помощью фабрики данных Azure (с помощью Azure PowerShell)" 
	description="В этом подробном учебнике описывается реалистичный сценарий, который реализуется с помощью Azure PowerShell и службы фабрики данных Azure." 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных (PowerShell)
Эта статья содержит сквозное руководство по каноническому сценарию обработки журнала с помощью фабрики данных Azure для преобразования данных из файлов журнала в аналитические данные.

## Сценарий
Contoso — это компания, которая создает игры для нескольких платформ: игровых консолей, карманных устройств и персональных компьютеров (ПК). Каждая из таких игр создает множество журналов. Цель компании Contoso — собрать и проанализировать журналы, созданные этими играми, чтобы получить информацию об использовании, определить возможности увеличения продаж и перекрестных продаж, разработать новые привлекательные функции и т.д. Все это позволит оптимизировать бизнес и предоставить клиентам больше удобства и возможностей.
 
В этом пошаговом руководстве мы будет собирать образцы журналов, обрабатывать и обогащать их ссылочными данными и преобразовывать данные для оценки эффективности маркетинговой кампании, которую Contoso недавно запустила.

## Предварительные требования
1.	Чтобы получить общее представление о фабрике данных Azure и об основных понятиях, прочтите статью [Введение в фабрику данных Azure][adfintroduction].
2.	Для выполнения инструкций этого учебника необходимо иметь подписку Azure. Сведения о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
3.	Загрузите и установите на своем компьютере [Azure PowerShell][download-azure-powershell].

	В этой статье рассматриваются не все командлеты фабрики данных. Полную документацию по командлетам фабрики данных см. в [справочнике по командлетам фабрики данных](https://msdn.microsoft.com/library/dn820234.aspx).
    
	1. Выполните командлет **Add-AzureAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	2. Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	3. Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которая используется на портале Azure.
	
	Не закрывайте Azure PowerShell, пока выполняются описанные в учебнике инструкции. Если закрыть и снова открыть это окно, то придется вновь выполнять эти команды.


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
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которую вы используете на портале Azure.
	

## Обзор
Полный рабочий процесс показан ниже: ![Полный процесс учебника][image-data-factory-tutorial-end-to-end-flow]

1. Конвейер **PartitionGameLogsPipeline** считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает разделы, основываясь на годе, месяце и дне (PartitionedGameEventsTable).
2. Конвейер **EnrichGameLogsPipeline** объединяет секционированные события игры (таблица PartitionedGameEvents, которая является результатом работы конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и дополняет их, сопоставляя IP-адреса с соответствующими географическими расположениями (EnrichedGameEventsTable).
3. Конвейер **AnalyzeMarketingCampaignPipeline** использует обогащенные данные (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательный результат эффективности маркетинговой кампании, который копируется в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для последующего анализа.
    
Вот какие шаги вы выполните в этом учебнике.

1. [Передача демонстрационных данных и сценариев](#upload-sample-data-and-scripts). В этом шаге вы передадите все демонстрационные данные (включая все журналы и ссылочные данные) и сценарии Hive/Pig, которые будут выполняться рабочими процессами. Выполняемые вами сценарии также создают базу данных SQL Azure (с именем MarketingCampaigns), таблицы, пользовательские типы и хранимые процедуры.
2. [Создание фабрики данных Azure](#create-data-factory). В этом шаге вы создадите фабрику данных Azure с именем LogProcessingFactory.
3. [Создание связанных служб](#create-linked-services). В этом шаге вы создадите следующие связанные службы: 
	
	- 	**StorageLinkedService**. Связывает расположение службы хранилища Azure, которая содержит необработанные события игры, секционированные события игры, обогащенные события игры, информацию об эффективности маркетинговой кампании, ссылочные данные геокода и ссылочные данные маркетинговой кампании, с фабрикой LogProcessingFactory   
	- 	**AzureSqlLinkedService**. Связывает базу данных SQL Azure, которая содержит информацию об эффективности маркетинговой кампании. 
	- 	**HDInsightStorageLinkedService**. Связывает хранилище больших двоичных объектов Azure, связанное с кластером HDInsight, на который ссылается HDInsightLinkedService. 
	- 	**HDInsightLinkedService**. Связывает кластер HDInsight Azure с фабрикой LogProcessingFactory. Этот кластер используется для выполнения обработки данных pig/hive. 
 		
4. [Создание наборов данных](#create-datasets). В этом шаге вы создадите следующие таблицы:
	
	- **RawGameEventsTable**. Эта таблица указывает расположение необработанных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/rawgameevents/). 
	- **PartitionedGameEventsTable**. Эта таблица указывает расположение секционированных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/partitionedgameevents/). 
	- **RefGeoCodeDictionaryTable**. Эта таблица указывает расположение ссылочных данных геокода в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refgeocodedictionary/).
	- **RefMarketingCampaignTable**. Эта таблица указывает расположение ссылочных данных маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/refdata/refmarketingcampaign/).
	- **EnrichedGameEventsTable**. Эта таблица указывает расположение обогащенных данных событий игры в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/logs/enrichedgameevents/).
	- **MarketingCampaignEffectivenessSQLTable**. Эта таблица указывает таблицу SQL (MarketingCampaignEffectiveness) в базе данных SQL Azure, определенную службой AzureSqlLinkedService, которая содержит данные об эффективности маркетинговой кампании. 
	- **MarketingCampaignEffectivenessBlobTable**. Эта таблица указывает расположение данных об эффективности маркетинговой кампании в хранилище больших двоичных объектов Azure, определенном службой StorageLinkedService (adfwalkthrough/marketingcampaigneffectiveness/). 

	
5. [Создание и планирование конвейеров](#create-pipelines). В этом шаге вы создадите следующие конвейеры:
	- **PartitionGameLogsPipeline**. Конвейер считывает необработанные события игры из хранилища больших двоичных объектов (RawGameEventsTable) и создает секции, основываясь на годе, месяце и дне (PartitionedGameEventsTable). 


		![Конвейер PartitionGamesLogs][image-data-factory-tutorial-partition-game-logs-pipeline]


	- **EnrichGameLogsPipeline**. Конвейер объединяет секционированные события игры (таблица PartitionedGameEvents, которая является выходными данными конвейера PartitionGameLogsPipeline) с геокодом (RefGetoCodeDictionaryTable) и обогащает данные путем сопоставления IP-адреса с соответствующим географическим положением (EnrichedGameEventsTable)

		![EnrichedGameLogsPipeline][image-data-factory-tutorial-enrich-game-logs-pipeline]

	- **AnalyzeMarketingCampaignPipeline**. Конвейер использует обогащенные данные событий игры (таблицу EnrichedGameEventTable, созданную конвейером EnrichGameLogsPipeline) и обрабатывает их с рекламными данными (RefMarketingCampaignnTable), чтобы создать окончательные выходные данные эффективности маркетинговой кампании, которые копируются в базу данных SQL Azure (MarketingCampainEffectivensessSQLTable) и в хранилище больших двоичных объектов Azure (MarketingCampaignEffectivenessBlobTable) для аналитики


		![MarketingCampaignPipeline][image-data-factory-tutorial-analyze-marketing-campaign-pipeline]


6. [Мониторинг конвейеров и срезов данных](#monitor-pipelines). На этом шаге вы выполните мониторинг конвейеров, таблиц и срезов данных с помощью портала Azure.

## передача демонстрационных данных и сценариев
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
   
2. Убедитесь, что вашему локальному компьютеру разрешен доступ к базе данных SQL Azure. Чтобы разрешить доступ, используйте [портал Azure](http://manage.windowsazure.com) или процедуру **sp\_set\_firewall\_rule** в базе данных master, чтобы создать правило брандмауэра для IP-адреса вашего компьютера. Может потребоваться до пяти минут, чтобы это изменение вступило в силу. См. раздел [Настройка правил брандмауэра для SQL Azure][azure-sql-firewall].
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


## Создание фабрики данных
На этом шаге вы создадите фабрику данных Azure с именем **LogProcessingFactory**.

1. Переключитесь в **Azure PowerShell**, если он уже открыт, (или) запустите **Azure PowerShell**. Если вы закрыли и повторно открыли Azure PowerShell, потребуется выполнить следующие команды. 
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.  
	- Выполните командлет **Get-AzureSubscription**, чтобы просмотреть все подписки для этой учетной записи.
	- Выполните командлет **Select-AzureSubscription**, чтобы выбрать подписку, с которой вы собираетесь работать. Эта подписка должна совпадать с той, которую вы используете на портале Azure. 

2. Создайте группу ресурсов Azure с именем **ADFTutorialResourceGroup** (если это еще не сделано), выполнив следующую команду.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Некоторые действия, описанные в этом учебнике, предполагают, что вы используете группу ресурсов с именем ADFTutorialResourceGroup. Если вы используете другую группу ресурсов, вместо ADFTutorialResourceGroup указывайте на этом уроке выбранную вами группу.
4. Выполните командлет **New-AzureRmDataFactory**, чтобы создать фабрику данных с именем DataFactoryMyFirstPipelinePSH.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name LogProcessingFactory –Location "West US"

	> [AZURE.IMPORTANT] Имя фабрики данных Azure должно быть глобально уникальным. Если возникает ошибка **Имя фабрики данных LogProcessingFactory недоступно**, измените это имя (например, на ваше\_имяLogProcessingFactory). Используйте выбранное имя вместо LogProcessingFactory при выполнении действий из этого учебника. Ознакомьтесь с разделом [Фабрика данных — правила именования](data-factory-naming-rules.md), чтобы узнать о правилах именования артефактов фабрики данных.
	> 
	> В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.

 
## Создание связанных служб

> [AZURE.NOTE] В этой статье Azure PowerShell используется для создания связанных служб, таблиц и конвейеров. Если вы хотите выполнить процедуры, описанные в этом учебнике, с помощью портала Azure, прочитайте [учебник по использованию редактора фабрики данных][adftutorial-using-editor].

В этом шаге вы создадите следующие связанные службы: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService и HDInsightLinkedService.

16. В Azure PowerShell перейдите к вложенной папке **LinkedServices** в каталоге **C:\\ADFWalkthrough** или к папке из того расположения, где вы извлекли файлы.
17. Воспользуйтесь следующей командой, чтобы установить переменную $df в имя фабрики данных.

		$df = “LogProcessingFactory”
17. В редакторе откройте **StorageLinkedService.json**, укажите **имя учетной записи** и **ключ учетной записи** и сохраните файл.
17. Чтобы создать связанную службу, воспользуйтесь командлетом **New-AzureRmDataFactoryLinkedService**, как показано далее. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\StorageLinkedService.json
	
18. В редакторе откройте **StorageLinkedService.json**, укажите **имя учетной записи** и **ключ учетной записи** и сохраните файл.
19. Создайте **HDInsightStorageLinkedService**.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightStorageLinkedService.json
 
19. В редакторе откройте **AzureSqlLinkedService.json**, укажите имя **azure sql server**, **имя пользователя**, **пароль** и сохраните файл.
19. Чтобы создать связанную службу, воспользуйтесь командлетом **New-AzureRmDataFactoryLinkedService**, как показано далее. 

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\AzureSqlLinkedService.json
19. Откройте **HDInsightLinkedService.json** в любом редакторе и обратите внимание, что тип имеет значение **HDInsightOnDemandLinkedService**.

	Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу.
	
	HDInsightLinkedService связывает кластер HDInsight по запросу с фабрикой данных. Для использования собственного кластера HDInsight обновите раздел "Свойства" файла HDInsightLinkedService.json, как показано ниже (замените clustername, username и password соответствующими значениями):
	
		"Properties": 
		{
    		"Type": "HDInsightBYOCLinkedService",
        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
	    	"UserName": "<username>",
	    	"Password": "<password>",
	    	"LinkedServiceName": "HDInsightStorageLinkedService"
		}
		


19. Чтобы создать связанную службу, воспользуйтесь командлетом **New-AzureRmDataFactoryLinkedService**, как показано далее. Начните с учетной записи хранения:

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Если используются другие имена для ResourceGroupName, DataFactoryName или LinkedService, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу связанной службы, если не удается найти этот файл.

 

## Создание наборов данных
В этом шаге вы создадите следующие таблицы:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Полный процесс учебника][image-data-factory-tutorial-end-to-end-flow]
 
На рисунке выше изображены конвейеры (в средней строке) и таблицы (в верхней и нижней строках).

Портал Azure пока не поддерживает создание наборов данных и таблиц, поэтому вам необходимо воспользоваться Azure PowerShell для создания таблиц в этом выпуске.

### Создание таблиц

1.	В Azure PowerShell перейдите к папке **Tables** (**C:\\ADFWalkthrough\\Tables\\**) из расположения, в которое вы извлекли примеры файлов.
2.	Воспользуйтесь командлетом **New-AzureRmDataFactoryDataset**, чтобы создать наборы данных для файла **RawGameEventsTable**.json, как показано далее.	


		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Если вы используете другие имена для ResourceGroupName и DataFactoryName, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу таблицы, если не удается найти этот файл с помощью командлета.

3. Повторите предыдущий шаг, чтобы создать следующие таблицы:
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureRmDataFactoryDataset -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. На **портале Azure** щелкните **Наборы данных** в колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** и убедитесь, что отображаются все наборы данных (таблицы представляют собой прямоугольные наборы данных).

	![Наборы данных — все][image-data-factory-tutorial-datasets-all]

	Вы также можете воспользоваться следующей командой из Azure PowerShell:
			
		Get-AzureRmDataFactoryDataset –ResourceGroupName ADF –DataFactoryName $df

	


## Создание конвейеров
В этом шаге вы создадите следующие конвейеры: PartitionGameLogsPipeline, EnrichGameLogsPipeline и AnalyzeMarketingCampaignPipeline.

1. В **проводнике Windows** перейдите к вложенной папке **Pipelines** в каталоге **C:\\ADFWalkthrough** (или из расположения, в которое вы извлекли примеры файлов).
2.	Откройте **PartitionGameLogsPipeline.json** в любом редакторе, замените выделенные данные информацией своей учетной записи хранения для хранилища данных и сохраните файл.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Повторите этот шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**.json (3 вхождения)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 вхождения)

	**ВАЖНО!** Убедитесь, что вы заменили все вхождения <storageaccountname> именем вашей учетной записи хранения.
 
4.  В **Azure PowerShell** перейдите к вложенной папке **Pipelines** в каталоге **C:\\ADFWalkthrough** (или из расположения, в которое вы извлекли примеры файлов).
5.  Воспользуйтесь командлетом **New-AzureRmDataFactoryPipeline**, чтобы создать конвейеры для файла **PartitionGameLogspeline.json**, как показано далее.	 
			
		New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Если вы используете другие имена для ResourceGroupName, DataFactoryName или имени конвейера, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу конвейера.
6. Повторите предыдущий шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**
			
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. Выполните командлет **Get-AzureRmDataFactoryPipeline**, чтобы получить список конвейеров.
			
		Get-AzureRmDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. После создания конвейеров вы можете указать время, в течение которого будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.

Чтобы указать период активности конвейера, вы можете воспользоваться командлетом Set-AzureRmDataFactoryPipelineActivePeriod. В этом пошаговом руководстве используются демонстрационные данные от 05/01 до 05/05. Используйте 2014-05-01 как значение для StartDateTime. EndDateTime — необязательный параметр.
			
		Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Подтвердите, чтобы задать период активности конвейера.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Повторите предыдущие два шага, чтобы задать период активности для следующих конвейеров.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureRmDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. На **портале Azure** щелкните элемент **Конвейеры** (но не имена конвейеров) в колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory**. После этого должны появиться созданные вами конвейеры.

	![Все конвейеры][image-data-factory-tutorial-pipelines-all]

12. В колонке **ФАБРИКА ДАННЫХ** для **LogProcessingFactory** щелкните **Diagram** (Схема).

	![Ссылка на схему][image-data-factory-tutorial-diagram-link]

13. Вы можете изменить эту схему, а здесь показана измененная схема, в которой данные прямого ввода отображается вверху, а выходные данные — внизу. Как вы видите, выходные данные конвейера **PartitionGameLogsPipeline** передаются в качестве входных данных в конвейер EnrichGameLogsPipeline, а выходные данные **EnrichGameLogsPipeline** передаются в конвейер **AnalyzeMarketingCampaignPipeline**. Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка.

	![Представление схемы][image-data-factory-tutorial-diagram-view]

	**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, конвейеры и таблицы, а также начали рабочий процесс.


## Мониторинг конвейеров 

1.	Если колонка "Фабрика данных" для LogProcessingFactory не открыта, вы можете выполнить одно из следующих действий:
	1.	Щелкните **LogProcessingFactory** на **начальной панели**. При создании фабрики данных флажок **Add to Startboard** (Добавить на начальную панель) был автоматически установлен.

		![Начальная панель мониторинга][image-data-factory-monitoring-startboard]

	2. Щелкните раздел **ОБЗОР**, а затем выберите **Everything** (Все элементы).
	 	
		![Мониторинг. Раздел "Все элементы"][image-data-factory-monitoring-hub-everything]

		В колонке **Обзор** выберите пункт **Фабрики данных**, а затем в колонке **Фабрики данных** выберите элемент **LogProcessingFactory**.

		![Мониторинг. Обзор фабрик данных][image-data-factory-monitoring-browse-datafactories]
2. Вы можете отслеживать фабрики данных несколькими способами. Начните с конвейеров или наборов данных. Давайте начнем с конвейеров, а затем пойдем дальше. 
3.	Щелкните элемент **Конвейеры** в колонке **ФАБРИКА ДАННЫХ**. 
4.	Щелкните элемент **PartitionGameLogsPipeline** в колонке «Конвейеры». 
5.	В колонке **КОНВЕЙЕР** для **PartitionGameLogsPipeline** можно увидеть, что этот конвейер использует набор данных **RawGameEventsTable**. Щелкните **RawGameEventsTable**.

	![Полученные и созданные конвейеры][image-data-factory-monitoring-pipeline-consumed-produced]

6. В колонке «ТАБЛИЦА» для **RawGameEventsTable** отображаются все срезы. На следующем снимке экрана все срезы находятся в состоянии **Ready**, а проблемных срезов нет. Это означает, что данные готовы к обработке.

	![Колонка "ТАБЛИЦА" RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table]
 
7. Теперь в колонке **КОНВЕЙЕР** для **PartiionGameLogsPipeline** щелкните элемент **Созданные**.
8. Вы должны увидеть список наборов данных, которые производит этот конвейер: 
9. Щелкните таблицу **PartitionedGameEvents** в колонке **Produced datasets** (Созданные наборы данных). 
10.	Убедитесь, что **состояние** всех срезов имеет значение **Ready** (Готов). 
11.	Щелкните один из срезов с состоянием **Ready**, чтобы просмотреть колонку **СРЕЗ ДАННЫХ** для этого среза.

	![Колонка "СРЕЗ ДАННЫХ" RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Если произошла ошибка, вы увидите здесь состояние **Ошибка**. Вы также увидите оба среза с состоянием **Ready** или **Waiting**, в зависимости от того, насколько быстро обрабатываются срезы.
 
	Чтобы получить представление обо всех возможных состояниях срезов, см. [Справочник разработчика фабрики данных Azure][developer-reference].

12.	В колонке **СРЕЗ ДАННЫХ** щелкните один из элементов списка **Activity Runs** (Выполняемые действия). Вы должны увидеть колонку "Activity Run" (Выполняемое действие) для этого фрагмента. Вы должны увидеть следующую колонку **ACTIVITY RUN DETAILS** (СВЕДЕНИЯ О ВЫПОЛНЯЕМЫХ ДЕЙСТВИЯХ).

	![Колонка "Сведения о выполняемых действиях"][image-data-factory-monitoring-activity-run-details]

13.	Щелкните **Скачать**, чтобы скачать файлы. Этот экран особенно полезен при устранении ошибок обработки HDInsight.
	 
	
После завершения работы всех конвейеров вы сможете просмотреть таблицу **MarketingCampaignEffectivenessTable** в базе данных SQL Azure **MarketingCampaigns**, чтобы увидеть результаты.

**Поздравляем!** Теперь вы можете отслеживать рабочие процессы и устранять возникающие в них неполадки. Вы узнали, как использовать фабрику данных Azure для обработки данных и получения аналитической информации.

## Дополнение к учебнику: использование локальных данных
Когда выполнялся последний шаг сценария обработки журналов, содержащегося в этом руководстве, результаты оценки эффективности, относящиеся к маркетинговой кампании, были скопированы в базу данных SQL Azure. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Чтобы скопировать данные об эффективности маркетинговой кампании из большого двоичного объекта Azure на локальный сервер SQL Server, необходимо создать дополнительную локальную связанную службу, таблицу и конвейер, о которых говорилось в этой статье.

Чтобы узнать, как создать конвейер для копирования данных об эффективности маркетинговой кампании в локальную базу данных SQL Server, изучите статью [Пошаговое руководство. Использование локального источника данных][tutorial-onpremises-using-powershell].
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[usepigandhive]: data-factory-data-transformation-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-new-datafactory-blade]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryBlade.png

[image-data-factory-tutorial-resourcegroup-blade]: ./media/data-factory-tutorial-using-powershell/ResourceGroupBlade.png

[image-data-factory-tutorial-create-resourcegroup]: ./media/data-factory-tutorial-using-powershell/CreateResourceGroup.png

[image-data-factory-tutorial-datafactory-homepage]: ./media/data-factory-tutorial-using-powershell/DataFactoryHomePage.png

[image-data-factory-tutorial-create-datafactory]: ./media/data-factory-tutorial-using-powershell/CreateDataFactory.png

[image-data-factory-tutorial-linkedservice-tile]: ./media/data-factory-tutorial-using-powershell/LinkedServiceTile.png

[image-data-factory-tutorial-linkedservices-add-datstore]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAddDataStore.png

[image-data-factory-tutorial-datastoretype-azurestorage]: ./media/data-factory-tutorial-using-powershell/DataStoreTypeAzureStorageAccount.png

[image-data-factory-tutorial-azurestorage-settings]: ./media/data-factory-tutorial-using-powershell/AzureStorageSettings.png

[image-data-factory-tutorial-storage-key]: ./media/data-factory-tutorial-using-powershell/StorageKeyFromAzurePortal.png

[image-data-factory-tutorial-linkedservices-blade-storage]: ./media/data-factory-tutorial-using-powershell/LinkedServicesBladeWithAzureStorage.png

[image-data-factory-tutorial-azuresql-settings]: ./media/data-factory-tutorial-using-powershell/AzureSQLDatabaseSettings.png

[image-data-factory-tutorial-azuresql-database-connection-string]: ./media/data-factory-tutorial-using-powershell/DatabaseConnectionString.png

[image-data-factory-tutorial-linkedservices-all]: ./media/data-factory-tutorial-using-powershell/LinkedServicesAll.png

[image-data-factory-tutorial-datasets-all]: ./media/data-factory-tutorial-using-powershell/DataSetsAllTables.png

[image-data-factory-tutorial-pipelines-all]: ./media/data-factory-tutorial-using-powershell/AllPipelines.png

[image-data-factory-tutorial-diagram-link]: ./media/data-factory-tutorial-using-powershell/DataFactoryDiagramLink.png

[image-data-factory-tutorial-diagram-view]: ./media/data-factory-tutorial-using-powershell/DiagramView.png

[image-data-factory-monitoring-startboard]: ./media/data-factory-tutorial-using-powershell/MonitoringStartBoard.png

[image-data-factory-monitoring-hub-everything]: ./media/data-factory-tutorial-using-powershell/MonitoringHubEverything.png

[image-data-factory-monitoring-browse-datafactories]: ./media/data-factory-tutorial-using-powershell/MonitoringBrowseDataFactories.png

[image-data-factory-monitoring-pipeline-consumed-produced]: ./media/data-factory-tutorial-using-powershell/MonitoringPipelineConsumedProduced.png

[image-data-factory-monitoring-raw-game-events-table]: ./media/data-factory-tutorial-using-powershell/MonitoringRawGameEventsTable.png

[image-data-factory-monitoring-raw-game-events-table-dataslice-blade]: ./media/data-factory-tutorial-using-powershell/MonitoringPartitionGameEventsTableDataSliceBlade.png

[image-data-factory-monitoring-activity-run-details]: ./media/data-factory-tutorial-using-powershell/MonitoringActivityRunDetails.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

<!---HONumber=AcomDC_0420_2016-->