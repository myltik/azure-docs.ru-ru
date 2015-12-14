<properties 
	pageTitle="Устранение неполадок фабрики данных Azure" 
	description="Узнайте, как устранять неполадки при использовании фабрики данных Azure." 
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

# Устранение неполадок фабрики данных
Вы можете устранять неполадки фабрики данных Azure с помощью классического портала Azure (или) командлетов Azure PowerShell. Эта статья содержит пошаговые инструкции, показывающие, как с помощью классического портала Azure быстро устранять ошибки, возникающие при работе с фабрикой данных.

## Проблема: не удается выполнить командлеты фабрики данных
Чтобы устранить эту проблему, переключите Azure в режим **AzureResourceManager**.

Чтобы перейти в режим **AzureResourceManager**, запустите **Azure PowerShell** и выполните приведенную ниже команду. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager

## Проблема: ошибка авторизации при выполнении командлета фабрики данных
Скорее всего, для Azure PowerShell используется неправильная учетная запись или подписка Azure. Чтобы выбрать правильную учетную запись и подписку Azure для использования с Azure PowerShell, используйте такие командлеты:

1. Add-AzureAccount — ввод имени пользователя и пароля.
2. Get-AzureSubscription — просмотр всех подписок для этой учетной записи. 
3. Select-AzureSubscription <subscription name> — выбор нужной подписки. Используйте ту же подписку, что применялась для создания фабрики данных на классическом портале Azure.

## Проблема: не удается запустить экспресс-установку шлюза данных на классическом портале Azure
Для экспресс-установки шлюза данных требуется Internet Explorer или другой веб-браузер, совместимый с Microsoft ClickOnce. Если вам не удается запустить экспресс- установку:

1. В случае проблем с другими браузерами используйте Internet Explorer. Или
2. Чтобы выполнить установку, используйте ссылки «Установка вручную», которые отображаются в той же колонке на портале, а затем скопируйте показываемый на экране ключ и вставьте его при настройке шлюза управления данными. Если шлюз не запускается, выберите в меню «Пуск» пункт «Шлюз управления данными Майкрософт» и вставьте ключ при его запуске. 


## Проблема: не удается запустить диспетчер учетных данных на классическом портале Azure
Чтобы обеспечить безопасность при настройке или обновлении связанной службы SQL Server через классический портал Azure, запускается диспетчер учетных данных. Для запуска требуется Internet Explorer или другой веб-браузер, совместимый с Microsoft ClickOnce. Если с другими браузерами возникают проблемы, используйте Internet Explorer.

## Проблема: не удается подключиться к локальному серверу SQL Server 
Убедитесь, что сервер SQL Server доступен для компьютера, на котором установлен шлюз. Для этого на компьютере, на котором установлен шлюз, сделайте вот что:

1. Проверьте связь с компьютером, на котором установлен сервер SQL Server. Или
2. Попытайтесь подключиться к экземпляру SQL Server с учетными данными, которые вы указали в SQL Server Management Studio (SSMS) на классическом портале Azure.


## Проблема: срезы входных данных постоянно находятся в состоянии PendingExecution или PendingValidation

Срезы могут находиться в состоянии **PendingExecution** или **PendingValidation** по ряду причин, и одна из самых распространенных заключается в том, что для свойства **external** не указано значение **true**. Все наборы данных, созданные вне фабрики данных Azure, должны быть помечены свойством **external**. Это означает, что данные являются внешними и не поддерживаются какими-либо конвейерами в фабрике данных. После того как данные станут доступны в соответствующем хранилище, срезы данных помечаются флагом **Ready** (готово).

См. следующий пример использования свойства **external**. Если нужно, можно указать **externalData*** при задании значения true для external.

Дополнительные сведения об этом свойстве см. в разделе «Таблицы» [справочника по сценариям JSON][json-scripting-reference].
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "dataDelay": "00:10:00",
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

 Чтобы устранить эту ошибку, добавьте свойство **external** и дополнительный раздел **externalData** в определение JSON входной таблицы и повторно создайте эту таблицу.

## Проблема: сбой гибридной операции копирования
Чтобы получить дополнительные сведения, выполните следующие действия.

1. Запустите диспетчер конфигурации шлюза управления данными на компьютере, на котором установлен шлюз. Убедитесь, что для параметра **Имя шлюза** задано имя логического шлюза на **классическом портале Azure**, для параметра **Состояние ключа шлюза** — значение **Зарегистрирован**, а для параметра **Состояние службы** — **Запущена**. 
2. Запустите **средство просмотра событий**. Разверните узел **Журналы приложений и служб** и щелкните пункт **Шлюз управления данными**. Проверьте наличие ошибок, относящихся к шлюзу управления данными. 

## Проблема: сбой подготовки HDInsight по запросу с ошибкой

При использовании связанной службы типа HDInsightOnDemandLinkedService нужно задать имя linkedServiceName, указывающее на хранилище больших двоичных объектов Azure. Эта учетная запись хранения будет использоваться для копирования всех журналов и вспомогательных файлов для кластера HDInsight по запросу. Иногда операция, которая выполняет подготовку HDInsight по запросу, может завершаться с приведенной ниже ошибкой.

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Обычно эта ошибка указывает на то, что расположение учетной записи хранения, указанной в linkedServiceName, не совпадает с расположением центра обработки данных, в котором происходит подготовка HDInsight. Например, если ваша фабрика данных Azure находится на Западе США (West US) и подготовка HDInsight по запросу происходит на Западе США, но для учетной записи хранения больших двоичных объектов Azure задано расположение на Востоке США (East US), подготовка по запросу выполнена не будет.

Есть еще одно свойство JSON, additionalLinkedServiceNames, где можно указать дополнительные учетные записи хранения в HDInsight по запросу. Эти дополнительные связанные учетные записи хранения должны находиться в том же расположении, что и кластер HDInsight, иначе произойдет сбой с той же ошибкой.



## Проблема: сбой настраиваемого действия
При использовании настраиваемого действия в фабрике данных Azure (тип действия конвейера CustomActivity) пользовательское приложение выполняется в указанной связанной службе с HDInsight как потоковое задание MapReduce в режиме «только сопоставление».

При выполнении настраиваемого действия фабрика данных Azure может записать выходные данные из кластера HDInsight и сохранить их в контейнере хранилища *adfjobs* в вашей учетной записи хранения больших двоичных объектов Azure. При возникновении ошибки ознакомьтесь с текстом в выходном текстовом файле **stderr**. Такие файлы можно прочитать непосредственно на классическом портале Azure, используя веб-браузер. Кроме того, вы можете получить непосредственный доступ к файлам в контейнере хранилища больших двоичных объектов Azure, используя инструменты обозревателя хранилища.

Чтобы перечислить и прочитать журналы для определенного настраиваемого действия, выполните инструкции пошагового руководства на этой странице. Ниже приведены эти инструкции.

1.  На классическом портале Azure щелкните группу **Обзор** и выберите фабрику данных.
2.  Нажмите кнопку **Схема**, чтобы просмотреть схему фабрики данных, а затем щелкните таблицу **набора данных** рядом с конкретным **конвейером** с настраиваемым действием. 
3.  В колонке **Таблица** щелкните интересующий вас срез в разделе **проблемных срезов** за период времени, который требуется исследовать.
4.  Откроется колонка **Срез данных** с подробными сведениями. В ней могут быть указаны несколько **выполненных действий** для этого среза. Выберите **действие** из списка. 
5.  Откроется колонка **Сведения о выполненном действии**. В центре этой колонки будет показано **сообщение об ошибке**, а в нижней части колонки, связанной с выполненным действием, будут перечислены **файлы журналов**.
	- Logs/system-0.log;
	- Состояние
	- Status/exit;
	- Status/stderr;
	- Status/stdout.

6. Щелкните первый **файл журнала** в списке. Полный текст файла отобразится в новой колонке, и вы сможете его прочитать. Щелкните каждый журнал, который нужно просмотреть, и изучите его в колонке с текстом. При желании можно скачать текстовый файл для просмотра в автономном режиме. Для этого нажмите кнопку **Скачать**.

Одна из **распространенных ошибок** настраиваемого действия — ошибка при выполнении пакета с кодом выхода «1». Дополнительные сведения см. по ссылке wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr.

Чтобы получить дополнительные сведения об ошибках этого типа, откройте **STDERR**-файл. Здесь можно увидеть одну из распространенных ошибок, связанных с истечением времени ожидания, например INFO mapreduce.Job: Task Id : attempt\_1424212573646\_0168\_m\_000000\_0, Status : FAILED AttemptID:attempt\_1424212573646\_0168\_m\_000000\_0 Timed out after 600 secs.

Такая же ошибка может появляться несколько раз, если, например, выполнение задания прекращалось три раза за 30 и более минут.

Эта ошибка указывает, что истекло время ожидания 600 секунд (10 минут). Обычно это означает, что пользовательское приложение .NET не обновляло состояние 10 минут подряд. Если приложение зависает или остается в состоянии ожидания слишком долго, то 10-минутное время ожидания позволяет предотвратить бесконечное ожидание и возникновение задержек в работе конвейера фабрики данных Azure.

Время ожидания задается в конфигурации кластера HDInsight, связанного с этим настраиваемым действием. Для этого используется параметр **mapred.task.timeout**, который по умолчанию имеет значение 600 000 миллисекунд, как указано в документации по стандартным параметрам Apache (http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml).

Вы можете изменить используемое по умолчанию значение во время подготовки кластера HDInsight. При использовании фабрики данных Azure и связанной службы **HDInsight по запросу** можно задать это свойство JSON вместе со свойствами HDInsightOnDemandLinkedService. Например, с помощью данного свойства JSON можно увеличить время ожидания до 20 минут.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Дополнительный контекст и полный пример JSON изменения этих свойств конфигурации MapReduce см. в примере 3 в этой статье на сайте MSDN (https://msdn.microsoft.com/library/azure/dn893526.aspx).

## Проблема: запрос PowerShell завершается неудачно с ошибкой 400 (недопустимый запрос) «Не обнаружен зарегистрированный поставщик ресурса...»

Начиная с 10 марта 2015 г. поддержка закрытых предварительных версий PowerShell фабрики данных Azure (2014-05-01-preview, 2014-07-01-preview и 2014-08-01-preview) будет прекращена. Мы рекомендуем использовать последнюю версию командлетов ADF, которые теперь входят в пакет загрузки Azure PowerShell (http://go.microsoft.com/?linkid=9811175&clcid=0x409).

При использовании версий пакета SDK Azure PowerShell, поддержка которых прекращена, могут возникать приведенные ниже ошибки.

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Пошаговое руководство. Устранение ошибки копирования данных
В этом пошаговом руководстве на примере ошибки, которая будет умышленно сделана при выполнении инструкций статьи «Приступая к работе с фабрикой данных», вы узнайте, как можно использовать классический портал Azure для устранения ошибок.

### Предварительные требования
1. Выполните инструкции, приведенные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
2. Убедитесь, что объект **ADFTutorialDataFactory** создает данные в таблице **emp** в базе данных SQL Azure.  
3. Теперь удалите таблицу **emp** (**drop table emp**) из Базы данных SQL Azure. Это приведет к ошибке.
4. Выполните следующую команду в **Azure PowerShell**, чтобы обновить период активности конвейера. Она попытается записать данные в несуществующую таблицу **emp**.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	В качестве значения **StartDateTime** укажите сегодняшний день, а в качестве значения **EndDateTime** — следующий день.


### Устранение ошибки с помощью классического портала Azure

1.	Войдите на [портал Azure][azure-portal]. 
2.	Щелкните **ADFTutorialDataFactory** на **начальной панели**. Если вы не видите ссылку на фабрику данных на **начальной панели**, щелкните концентратор **ОБЗОР**, а затем выберите элемент **Все**. Щелкните пункт **Фабрики данных** в колонке **Обзор** и выберите **ADFTutorialDataFactory**.
3.	Обратите внимание, что на плитке **Наборы данных** отображается ссылка **С ошибками**. Щелкните ссылку **С ошибками**. Должна открыться колонка **Наборы данных с ошибками**.

	![Ссылка «Фабрика данных с ошибками»][image-data-factory-troubleshoot-with-error-link]

4. В колонке **Наборы данных с ошибками** щелкните **EmpSQLTable**, чтобы открыть колонку **ТАБЛИЦА**.

	![Колонка «Наборы данных с ошибками»][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. В нижней части колонки **ТАБЛИЦА** в списке **Проблемные срезы** должны отобразиться срезы с ошибками. Последние срезы с ошибками можно увидеть также в списке **Последние срезы**. Выберите срез в списке **Проблемные срезы**.

	![Колонка «Таблица» с проблемными срезами][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Если вы щелкнете заголовок **Проблемные срезы**, а не конкретную проблему, то откроется колонка **СРЕЗЫ ДАННЫХ**. Щелкните в ней **конкретный проблемный срез**, чтобы увидеть колонку **СРЕЗ ДАННЫХ** для него.

6. В нижней части колонки **СРЕЗ ДАННЫХ** для **EmpSQLTable** отображается список всех **выполненных действий** для среза. Выберите **выполненное действие** с ошибкой.

	![Колонка «Срез данных» с выполняемыми действиями][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. В колонке **Сведения о выполненном действии** должны отображаться подробные сведения об ошибке. В нашем примере должна отображаться ошибка **Недопустимое имя объекта: emp**.

	![Сведения о выполненном действии с ошибкой][image-data-factory-troubleshoot-activity-run-with-error]

Чтобы устранить эту проблему, создайте таблицу **emp**, используя сценарий SQL согласно инструкциям, приведенным в статье [Приступая к работе с фабрикой данных][adfgetstarted].


### Устранение ошибки с помощью командлетов Azure PowerShell
1.	Запустите **Azure PowerShell**. 
2.	Перейдите в режим **AzureResourceManager**, так как командлеты фабрики данных доступны только в нем.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием Failed (сбой).

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Замените **StartDateTime** на значение StartDateTime, которое вы указали для **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Запишите значение **Start** для проблемного среза (среза, у которого параметр **Status** имеет значение **Failed**), полученное в результате выполнения команды. 
4. Теперь запустите командлет **Get-AzureDataFactoryRun**, чтобы получить подробные сведения о выполненном для среза действии.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	В качестве значения **StartDateTime** укажите время начала для проблемного (содержащего ошибку) среза, которое вы отметили на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
5. Вы должны увидеть выходные данные с подробными сведениями об ошибке (аналогично следующему):

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Пошаговое руководство. Устранение ошибки обработки Hive и Pig
Это пошаговое руководство содержит инструкции по устранению ошибки обработки Hive и Pig с использованием классического портала Azure и Azure PowerShell.


### Пошаговое руководство. Устранение ошибки обработки Pig и Hive с помощью классического портала Azure
В этом сценарии набор данных находится в состоянии ошибки из-за сбоя обработки Hive в кластере HDInsight.

1. Щелкните ссылку **С ошибками** на плитке **Наборы данных** на домашней странице **ФАБРИКА ДАННЫХ**.

	![Ссылка «С ошибками» на плитке «Наборы данных»][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. В колонке **Наборы данных с ошибками** щелкните интересующую вас **таблицу**.

	![Колонка «Наборы данных с ошибками»][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. В колонке **ТАБЛИЦА** щелкните **проблемный срез**, имеющий **состояние** **Failed** (сбой).

	![Таблица с проблемными срезами][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. В колонке **СРЕЗ ДАННЫХ** щелкните **выполненное действие**, которое завершилось неудачно.

	![Срез данных с неудачными действиями][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. В колонке **СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ** вы можете скачать файлы, связанные с обработкой HDInsight. Чтобы скачать файл журнала ошибок c подробными сведениями об ошибке, щелкните ссылку **Скачать** в строке журнала **Status/stderr**.

	![Сведения о выполненном действии со ссылкой для загрузки][image-data-factory-troubleshoot-activity-run-details]

    
### Пошаговое руководство. Использование Azure PowerShell для устранения ошибки обработки Pig или Hive
1.	Запустите **Azure PowerShell**. 
2.	Перейдите в режим **AzureResourceManager**, так как командлеты фабрики данных доступны только в нем.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием Failed (сбой).

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Замените **StartDateTime** на значение StartDateTime, которое вы указали для **Set-AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Запишите значение **Start** для проблемного среза (среза, у которого параметр **Status** имеет значение **Failed**), полученное в результате выполнения команды. 
4. Теперь запустите командлет **Get-AzureDataFactoryRun**, чтобы получить подробные сведения о выполненном для среза действии.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	В качестве значения **StartDateTime** укажите время начала для проблемного (содержащего ошибку) среза, которое вы отметили на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
5. Вы должны увидеть выходные данные с подробными сведениями об ошибке (аналогично следующему):

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Вы можете выполнить командлет **Save-AzureDataFactoryLog** с идентификатором, который отображается в результате описанных выше команд, и загрузить файлы журналов, используя параметр **-DownloadLogs** командлета.



[adfgetstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 

<!---HONumber=AcomDC_1203_2015-->