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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Учебник: Перемещение и обрабатывать файлы журнала, с помощью фабрики данных [PowerShell]
Эта статья содержит сквозное руководство по каноническому сценарию обработки журнала с помощью фабрики данных Azure для преобразования данных из файлов журнала в аналитические данные.

## Сценарий
Contoso представляет игры компании, которая создает игр для нескольких платформ: игровых консолей, карманный устройств и персональных компьютеров (ПК). Каждая из таких игр создает множество журналов. Цель компании Contoso — собрать и проанализировать журналы, созданные этими играми, чтобы получить информацию об использовании, определить возможности увеличения продаж и перекрестных продаж, разработать новые привлекательные функции и т.д. Все это позволит оптимизировать бизнес и предоставить клиентам больше удобства и возможностей.
 
В этом пошаговом руководстве мы будет собирать образцы журналов, обрабатывать и обогащать их ссылочными данными и преобразовывать данные для оценки эффективности маркетинговой кампании, которую Contoso недавно запустила.

## Подготовка к работе с учебником
1.	Чтение [Введение в Azure данные фабрики][adfintroduction] получить общие сведения о данных фабрика Azure и понятиями верхнего уровня.
2.	Для выполнения инструкций этого учебника необходимо иметь подписку Azure. Сведения о получении подписки см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
3.	Необходимо загрузить и установить [Azure PowerShell][download-azure-powershell] на компьютере. 
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
Сквозного рабочего процесса как показано ниже: ![Учебник сквозного потока][image-data-factory-tutorial-end-to-end-flow]

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

1.	После входа в [предварительной версии портала Azure][azure-preview-portal], щелкните **Создать** из нижнего левого угла и нажмите кнопку **фабрика данных** на **Создать** выноски. 

	![New -> DataFactory][image-data-factory-new-datafactory-menu]
	
	Если вы не видите **фабрика данных** на **Создать** выноску, прокрутите вниз.
	
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

> [AZURE.NOTE]Этой статьи используется Azure PowerShell для создания связанные службы, таблицы и конвейеры. В разделе [учебника, с помощью фабрики редактора данных][adftutorial-using-editor] требуется для выполнения этого учебника, с помощью портала Azure, в частности фабрики редактора данных.

На этом шаге вы создадите следующие связанные службы: StorageLinkedService, AzureSqlLinkedService, HDInsightStorageLinkedService и HDInsightLinkedService.


1.	В **LogProcessingFactory** стоечный модуль, щелкните **связанные службы** плитки.

	![Плитка связанные службы][image-data-factory-tutorial-linkedservice-tile]

2. В **связанные службы** стоечный модуль, щелкните **+ хранилища данных** на панели команд.

	![Добавить связанные службы - хранилище][image-data-factory-tutorial-linkedservices-add-datstore]

3. В **новое хранилище данных** выноску, введите **StorageLinkedService** для **имя**, щелкните **ТИПА (параметры, необходимые)**, и выберите **учетной записи хранилища Azure**.

	![Тип хранилища данных — хранилище Azure][image-data-factory-tutorial-datastoretype-azurestorage]

4. В **новое хранилище данных** выноску, вы увидите два новых поля: **имя учетной записи** и **ключ учетной записи**. Введите имя учетной записи и ключа учетной записи для вашей **учетной записи хранилища Azure**.

	![Параметры хранилища Azure][image-data-factory-tutorial-azurestorage-settings]

	Вы можете получить имя и ключ учетной записи для своей учетной записи хранения Azure на портале, как показано ниже:

	![Ключ хранилища][image-data-factory-tutorial-storage-key]
  
5. После нажатия кнопки **ОК** с новыми данными хранения выноску, вы увидите **StorageLinkedService** в списке **ХРАНИЛИЩ данных** на **связанные службы** выноски. Проверка **уведомления** концентратора (слева) сообщения.

	![Связанные службы выноску с хранилищем][image-data-factory-tutorial-linkedservices-blade-storage]
   
6. Повторите **шаги 2 – 5** для создания другой связанный с именем службы: **HDInsightStorageLinkedService**. Это хранилище, используемое вашим кластером HDInsight.
7. Убедитесь, что оба **StorageLinkedService** и **HDInsightStorageLinkedService** в списке в выноске связанные службы.
8. В **связанные службы** стоечный модуль, щелкните **Добавить (+) в хранилище данных** на панели команд.
9. Введите **AzureSqlLinkedService** в качестве имени.
10. Щелкните **ТИПА (параметры, необходимые)**, выберите **базы данных SQL Azure**.
11. Теперь необходимо выполнить следующие дополнительные поля на **новое хранилище данных** выноски. Введите имя базы данных SQL Azure **сервера**, **базы данных** имя **имя пользователя**, и **пароль**, и нажмите кнопку **ОК**.
	1. Введите **MarketingCampaigns** для **базы данных**. Это база данных Azure SQL, созданные сценарии, которые выполнялись в шаге 1. Следует убедиться, что эта база данных действительно создана скриптов (в случае, если возникли ошибки).
		
 		![Параметры Azure SQL][image-data-factory-tutorial-azuresql-settings]

		Чтобы получить эти значения с портала управления Azure: выберите представление базы данных SQL строки подключения для базы данных MarketingCampaigns

		![Строка подключения для базы данных Azure SQL][image-data-factory-tutorial-azuresql-database-connection-string]

12. Убедитесь, что все три данных хранилища, созданных: **StorageLinkedService**, **HDInsightStorageLinkedService**, и **AzureSqlLinkedService**.
13. Необходимо создать другой связанной службы, но это является службы вычислений, в частности **кластера Azure HDInsight**. Портал пока не поддерживает создание вычислительной связанной службы. Поэтому для создания этой связанной службы необходимо воспользоваться Azure PowerShell. 
14. Переключитесь в **Azure PowerShell** при наличии он уже открыт (или) запустите **Azure PowerShell**. Если закрыт и открыт повторно Azure PowerShell, необходимо выполнить следующие команды: 
	- Запустите **Add-AzureAccount** и введите имя пользователя и пароль, используемый для входа на портале Azure предварительной версии.  
	- Запустите **Get-AzureSubscription** для просмотра всех подписок для этой учетной записи.
	- Запустите **Select-AzureSubscription** выберите подписку, которую требуется работать с. Эта подписка должна совпадать с той, которая используется на портале предварительной версии Azure. 
15. Переключитесь в **AzureResourceManager** режиме как командлеты фабрика данных Azure доступны в этом режиме.

		Switch-AzureMode AzureResourceManager

16. Перейдите к **LinkedServices** в подпапку **C:\ADFWalkthrough** (или) из папки из расположения, где уже извлечены файлы.
17. Откройте **HDInsightLinkedService.json** в любом редакторе и обратите внимание, что тип имеет значение **HDInsightOnDemandLinkedService**.


	> [AZURE.NOTE]Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу. HDInsightLinkedService связывает фабрику данных кластера HDInsight по требованию. Для использования кластера HDInsight, обновите раздел свойства файла HDInsightLinkedService.json, как показано ниже (замените имя кластера, имя пользователя и пароль с соответствующими значениями):
	> 
			"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "HDInsightStorageLinkedService"
    		}
		

18. Воспользуйтесь следующей командой, чтобы установить переменную $df в имя фабрики данных.

		$df = “LogProcessingFactory”
19. С помощью командлета **Создать AzureDataFactoryLinkedService** Создание связанной службы. Начните с учетной записи хранения:

		New-AzureDataFactoryLinkedService -ResourceGroupName ADF -DataFactoryName $df -File .\HDInsightLinkedService.json
 
	Если используются другие имена для ResourceGroupName, DataFactoryName или LinkedService, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу связанной службы, если не удается найти этот файл.
20. Вы увидите все четыре связанные службы в **связанные службы** выноску, как показано ниже. Если выноски связанные службы не открыта, щелкните связанные службы в **ФАБРИКА данных** страницы для **LogProcessingFactory**. Обновление колонки «Linked services» (Связанные службы) может продлиться несколько секунд.

	![Связанные службы все][image-data-factory-tutorial-linkedservices-all]
 

## <a name="MainStep4"></a> Шаг 4: Создание таблицы 
В этом шаге вы создадите следующие таблицы:

- RawGameEventsTable
- PartitionedGameEventsTable
- RefGeoCodeDictionaryTable
- RefMarketingCampaignTable
- EnrichedGameEventsTable
- MarketingCampaignEffectivenessSQLTable
- MarketingCampaignEffectivenessBlobTable

	![Учебник начала до конца потока][image-data-factory-tutorial-end-to-end-flow]
 
На рисунке выше изображены конвейеры (в средней строке) и таблицы (в верхней и нижней строках).

Портал Azure пока не поддерживает создание наборов данных и таблиц, поэтому вам необходимо воспользоваться Azure PowerShell для создания таблиц в этом выпуске.

### Создание таблиц

1.	В Azure PowerShell, перейдите к **таблиц** папку (** C:\ADFWalkthrough\Tables**) из расположения, где уже извлечены образцов. 
2.	С помощью командлета **Создать AzureDataFactoryTable** следующим создание таблиц для **RawGameEventsTable**.json	


		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RawGameEventsTable.json

	Если вы используете другие имена для ResourceGroupName и DataFactoryName, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу таблицы, если не удается найти этот файл с помощью командлета.

3. Повторите предыдущий шаг, чтобы создать следующие таблицы:
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionedGameEventsTable.json
		
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefGeoCodeDictionaryTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\RefMarketingCampaignTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichedGameEventsTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessSQLTable.json
			
		New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName $df –File .\MarketingCampaignEffectivenessBlobTable.json



4. В **предварительной версии портала Azure**, щелкните **наборы данных** в **ФАБРИКА данных** выноски для **LogProcessingFactory** и убедитесь, что все наборы данных (таблицы, прямоугольная наборы данных).

	![Все наборы данных][image-data-factory-tutorial-datasets-all]

	Вы также можете воспользоваться следующей командой из Azure PowerShell:
			
		Get-AzureDataFactoryTable –ResourceGroupName ADF –DataFactoryName $df

	


## <a name="MainStep5"></a> Шаг 5: Создание и планирование конвейеры
На этом шаге будет создан следующий конвейеры: PartitionGameLogsPipeline, EnrichGameLogsPipeline и AnalyzeMarketingCampaignPipeline.

1. В **Проводник**, перейдите к **конвейеры** вложенную папку в **C:\ADFWalkthrough** папку (или из расположения, где уже извлечены образцы).
2.	Откройте **PartitionGameLogsPipeline.json** в своем любимом редакторе замените выделенную вашей учетной записи хранилища для данных учетной записи хранения данных и сохраните файл.
			
		"RAWINPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/rawgameevents/",
		"PARTITIONEDOUTPUT": "wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/logs/partitionedgameevents/",

3. Повторите этот шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**.json (3 вхождений)
	2. **AnalyzeMarketingCampaignPipeline**.json (3 вхождений)

	**Важно:** убедитесь, что Вы заменили все <storageaccountname> имя вашей учетной записи хранилища.
 
4.  В **Azure PowerShell**, перейдите к **конвейеры** вложенную папку в **C:\ADFWalkthrough** папку (или из расположения, где уже извлечены образцы).
5.  С помощью командлета **Создать AzureDataFactoryPipeline** Создание конвейеры следующим для **PartitionGameLogspeline**.json	 
			
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\PartitionGameLogsPipeline.json

	Если вы используете другие имена для ResourceGroupName, DataFactoryName или имени конвейера, сошлитесь на них в вышеупомянутом командлете. Также укажите полный путь к JSON-файлу конвейера.
6. Повторите предыдущий шаг, чтобы создать следующие конвейеры:
	1. **EnrichGameLogsPipeline**
			
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\EnrichGameLogsPipeline.json

	2. **AnalyzeMarketingCampaignPipeline**
				
			New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName $df –File .\AnalyzeMarketingCampaignPipeline.json

7. С помощью командлета **Get AzureDataFactoryPipeline** для получения списка конвейеры.
			
		Get-AzureDataFactoryPipeline –ResourceGroupName ADF –DataFactoryName $df

8. После создания конвейеров вы можете указать время, в течение которого будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.

Чтобы указать период активности конвейера, вы можете воспользоваться командлетом Set-AzureDataFactoryPipelineActivePeriod. В этом пошаговом руководстве образцов данных — от 05/01 до 05/05. Используйте 2014-05-01-как StartDateTime. EndDateTime — необязательный параметр.
			
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name PartitionGameLogsPipeline
  
9. Подтвердите, чтобы задать период активности конвейера.
			
			Confirm
			Are you sure you want to set pipeline 'PartitionGameLogsPipeline' active period from '05/01/2014 00:00:00' to '05/05/2014 00:00:00'?
			[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): n

10. Повторите предыдущие два шага, чтобы задать период активности для следующих конвейеров.
	1. **EnrichGameLogsPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z –EndDateTime 2014-05-05Z –Name EnrichGameLogsPipeline

	2. **AnalyzeMarketingCampaignPipeline**
			
			Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADF -DataFactoryName $df -StartDateTime 2014-05-01Z -EndDateTime 2014-05-05Z –Name AnalyzeMarketingCampaignPipeline

11. В **предварительной версии портала Azure**, щелкните **конвейеры** плитке (не на имена конвейеры) в **ФАБРИКА данных** выноски для **LogProcessingFactory**, вы увидите конвейеры, вы создали.

	![Все каналы][image-data-factory-tutorial-pipelines-all]

12. В **ФАБРИКА данных** выноски для **LogProcessingFactory**, щелкните **Схема**.

	![Ссылка на диаграмме][image-data-factory-tutorial-diagram-link]

13. Вы можете изменить эту схему, а здесь показана измененная схема, в которой данные прямого ввода отображается вверху, а выходные данные — внизу. Можно видеть, что выходные данные **PartitionGameLogsPipeline** передается в качестве входных данных для EnrichGameLogsPipeline и вывода **EnrichGameLogsPipeline** передается **AnalyzeMarketingCampaignPipeline**. Дважды щелкните мышью заголовок, чтобы просмотреть подробную информацию об артефакте, который представляет колонка.

	![Представление диаграммы][image-data-factory-tutorial-diagram-view]

	**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, конвейеры и таблицы, а также начали рабочий процесс.


## <a name="MainStep6"></a> Шаг 6: Срезы данных и конвейеры отслеживать 

1.	Если колонка «Фабрика данных» для LogProcessingFactory не открыта, вы можете выполнить одно из следующих действий:
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
 
7. Теперь на **КОНВЕЙЕРА** выноски для **PartiionGameLogsPipeline**, щелкните **создана**.
8. Вы должны увидеть список наборов данных, которые производит этот конвейер: 
9. Щелкните **PartitionedGameEvents** в таблицу **созданные наборы данных** выноски. 
10.	Убедитесь, что **состояние** всех фрагментов имеет значение **готов**. 
11.	Щелкните один из фрагментов, которые **готов** для просмотра **СРЕЗ данных** выноски для этого фрагмента.

	![Выноска СРЕЗ данных RawGameEventsTable][image-data-factory-monitoring-raw-game-events-table-dataslice-blade]

	Если произошла ошибка, вы увидите **Сбой **статус. Также возможны оба фрагменты с состоянием **готов**, или оба со статусом **PendingValidation**, в зависимости от того, насколько быстро обрабатываются срезы.
 
	Ссылаться на [Справочник разработчика Azure данных фабрики][developer-reference] понять все возможные срез состояния.

12.	В **СРЕЗ данных** выноску, выберите запускать с **действие выполняется** списка. Вы должны увидеть колонку «Activity Run» (Выполняемое действие) для этого фрагмента. Вы увидите следующее **сведения о выполнении действия** выноски.

	![Выноска сведения о выполнении действия][image-data-factory-monitoring-activity-run-details]

13.	Щелкните **загрузки** для загрузки файлов. Этот экран особенно полезен при устранении ошибок обработки HDInsight.
	 
	
После завершения выполнения всех конвейер можно найти в **MarketingCampaignEffectivenessTable** в **MarketingCampaigns** базы данных Azure SQL для просмотра результатов.

**Поздравляем!** Теперь вы можете отслеживать рабочие процессы и устранять возникающие в них неполадки. Вы узнали, как использовать фабрику данных Azure для обработки данных и получения аналитической информации.

## Расширить учебника, чтобы использовать локальных данных.
На последнем шаге обработки сценарий из пошагового руководства в данной статье журнала выходные данные эффективность маркетинговой кампании был скопирован в базе данных Azure SQL. Вы также могли переместить эти данные на локальный сервер SQL Server для аналитического анализа внутри вашей организации.
 
Для копирования данных эффективность маркетинговой кампании из больших двоичных объектов Azure для локального SQL Server, необходимо создать дополнительные локальные связанные службы, таблицы и конвейера появилась в этом руководстве в этой статье.

Рекомендуется [Пошаговое руководство: использование локального источника данных][tutorial-onpremises-using-powershell] чтобы узнать, как создать конвейер для копирования данных эффективность маркетинговой кампании локальной базы данных SQL Server.
 

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[adftutorial-using-editor]: data-factory-tutorial.md

[adfgetstarted]: data-factory-get-started.md
[adfintroduction]: data-factory-introduction.md
[useonpremisesdatasources]: data-factory-use-onpremises-datasources.md
[usepigandhive]: data-factory-pig-hive-activities.md
[tutorial-onpremises-using-powershell]: data-factory-tutorial-extend-onpremises-using-powershell.md
[download-azure-powershell]: ../powershell-install-configure.md

[azure-preview-portal]: http://portal.azure.com
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[sqlcmd-install]: http://www.microsoft.com/download/details.aspx?id=35580
[azure-sql-firewall]: http://msdn.microsoft.com/library/azure/jj553530.aspx


[adfwalkthrough-download]: http://go.microsoft.com/fwlink/?LinkId=517495
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908


[image-data-factory-tutorial-end-to-end-flow]: ./media/data-factory-tutorial-using-powershell/EndToEndWorkflow.png

[image-data-factory-tutorial-partition-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/PartitionGameLogsPipeline.png

[image-data-factory-tutorial-enrich-game-logs-pipeline]: ./media/data-factory-tutorial-using-powershell/EnrichGameLogsPipeline.png

[image-data-factory-tutorial-analyze-marketing-campaign-pipeline]: ./media/data-factory-tutorial-using-powershell/AnalyzeMarketingCampaignPipeline.png


[image-data-factory-tutorial-egress-to-onprem-pipeline]: ./media/data-factory-tutorial-using-powershell/EgreeDataToOnPremPipeline.png

[image-data-factory-tutorial-set-firewall-rules-azure-db]: ./media/data-factory-tutorial-using-powershell/SetFirewallRuleForAzureDatabase.png

[image-data-factory-tutorial-portal-new-everything]: ./media/data-factory-tutorial-using-powershell/PortalNewEverything.png

[image-data-factory-tutorial-datastorage-cache-backup]: ./media/data-factory-tutorial-using-powershell/DataStorageCacheBackup.png

[image-data-factory-tutorial-dataservices-blade]: ./media/data-factory-tutorial-using-powershell/DataServicesBlade.png

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

[image-data-factory-datamanagementgateway-configuration-manager]: ./media/data-factory-tutorial-using-powershell/DataManagementGatewayConfigurationManager.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-tutorial-using-powershell/NewDataFactoryMenu.png

[image-data-factory-new-datafactory-create-button]: ./media/data-factory-tutorial-using-powershell/DataFactoryCreateButton.png

<!---HONumber=GIT-SubDir--> 