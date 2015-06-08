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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Устранение неполадок фабрики данных
Вы можете устранять неполадки фабрики данных Azure с помощью портала Azure (или) командлетов Azure PowerShell. Эта статья содержит пошаговые инструкции, показывающие, как с помощью портала Azure быстро устранять ошибки, возникающие при работе с фабрикой данных.

## Проблема: Не удается запускать командлеты фабрика данных
Чтобы устранить эту проблему, переключитесь в режиме Azure для **AzureResourceManager**:

Запустите **Azure PowerShell** и выполните следующую команду, чтобы перейти к **AzureResourceManager** режим. Командлеты фабрика данных Azure доступны в **AzureResourceManager** режим.

         switch-azuremode AzureResourceManager

## Проблема: Несанкционированного ошибки при выполнении командлета фабрика данных
Скорее всего, не используется учетная запись Azure вправо или подписки с использованием Azure PowerShell. Используйте следующие командлеты для выбора учетной записи право Azure и подписку для использования с Azure PowerShell.

1. Add-AzureAccount - использование правом пользователя и пароль
2. Get-AzureSubscription - просмотреть все подписки для учетной записи. 
3. SELECT-AzureSubscription <subscription name> -выберите подходящую подписку. Используйте тот же идентификатор используется для создания фабрики данных на портале Azure предварительной версии.

## Проблема: Не удается запустить установки Express данных шлюза с портала Azure
Программа установки Express для шлюза данных требуется Internet Explorer или Microsoft ClickOnce совместимых веб-браузера. Если не удается запустить программу установки Express, вы можете

1. Переключитесь в Internet Explorer, при сбое с другими браузерами. Или
2. Используйте ссылки «Установка вручную» показано на выноске же на портале для выполнения установки, а затем скопируйте ключ, который предоставляется на экране и вставьте при готовности конфигурации шлюза управления данными. Если он не запускается, проверьте «Шлюз управления данными Майкрософт» меню "Пуск" и вставьте ключ при его запуске. 


## Проблема: Не удается запустить диспетчер учетных данных из портала Azure
Если настройка или обновление связанной службы SQL Server через портал Azure, диспетчер учетных данных, для обеспечения безопасности будет запущено приложение. Необходим Internet Explorer или Microsoft ClickOnce совместимых веб-браузера. При сбое в других браузерах можно переключиться на Internet Explorer.

## Проблема: Не удается подключиться к локальному серверу SQL 
Убедитесь, что сервер SQL Server доступен для компьютера, на котором установлен шлюз. На компьютере, на котором установлен шлюз вы можете

1. Проверка компьютера установки SQL Server. Или
2. Попробуйте подключиться к экземпляру SQL Server с использованием учетных данных, указанное на портале с помощью SQL Server Management Studio (SSMS).


## Проблема: Фрагменты ввода не в состоянии PendingExecution или PendingValidation для постоянно

Срезы могут находиться в **PendingExecution** или **PendingValidation** сохраняется состояние по ряду причин, а также один из распространенных причин **waitOnExternal** свойство не указано в **доступности** раздел первой таблицы и набора данных в конвейере. Любой набор данных, созданных за пределами области данных фабрики Azure должны быть помечены **waitOnExternal** свойства в разделе **доступности** раздела. Это означает, что данные являются внешними и не поддерживает все конвейеры в фабрике данных. Срезы данных, помечаются как **готов** после того как данные доступны в соответствующие хранилища.

В следующем примере для использования в разделе **waitOnExternal** свойство. Можно указать **{waitOnExternal}** без указания значения для свойства в разделе, используются значения по умолчанию.

В разделе таблиц в [ссылки сценария JSON][json-scripting-reference] Дополнительные сведения об этом свойстве.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Чтобы устранить эту ошибку, добавьте **waitOnExternal** раздела в определении JSON входную таблицу и повторно создать таблицу.

## Проблема: Сбой операции копирования гибридного
Чтобы получить дополнительные сведения:

1. Запустите диспетчер конфигурации шлюза управления данными на компьютере, на котором установлен шлюз. Убедитесь, что **имя шлюза** присвоено имя логического шлюза на **портала Azure**, **состояние ключа шлюза** — **Зарегистрированные** и **состояние службы** — **работает**. 
2. Запустите **средство просмотра событий**. Разверните **Журналы приложений и служб** и нажмите кнопку **Шлюз управления данными**. Проверьте наличие ошибок, относящихся к шлюзу управления данными. 

## Проблема: По запросу HDInsight подготовки завершится неудачно, ошибка

При использовании службы связанного типа HDInsightOnDemandLinkedService, следует указать linkedServiceName, указывающий на BLOB-хранилища Azure. Эта учетная запись хранения будет использоваться для копирования журналов и вспомогательные файлы для кластера HDInsight по требованию. Иногда действия, который выполняет подготовку по требованию на HDInsight может завершиться со следующей ошибкой:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Эта ошибка обычно указывает, что расположение хранилища учетной записи, указанной в linkedServiceName не же местоположение центра обработки данных, в котором происходит подготовки HDInsight. Например если фабрика данных Azure находится Запад США и Подготовка по требованию HDInsight происходит в Запад США, но расположение учетной записи хранилища Azure BLOB-объекта равен Восток США, Подготовка по требованию не будет выполнена.

Кроме того имеется второй additionalLinkedServiceNames свойство JSON где дополнительные учетные записи хранения может быть указан в HDInsight по требованию. Эти дополнительные связанные учетные записи хранения должны находиться в том же расположении, что и кластер HDInsight, или она завершится с той же ошибки.



## Проблема: Сбой настраиваемого действия
При использовании настраиваемого действия в фабрике Azure данных (тип действия конвейера CustomActivity), пользовательское приложение выполняется в службе указанного связанного в HDInsight как карты только потоковой передачи задания MapReduce.

При выполнении настраиваемого действия, фабрика Azure данных смогут выходных из кластера HDInsight и сохраните его в *adfjobs* контейнер хранилища в вашей учетной записи хранилища Azure BLOB-объектов. В случае возникновения ошибки можно прочитать текст из **stderr** выходного текстового файла после возникновения сбоя. Файлы, доступно и для чтения из портала Azure сам в веб-браузере или с помощью средства обозревателя хранилища для доступа к файлам, которые хранятся в контейнере хранилища Azure Blob Storage непосредственно.

Для перечисления и читать журналы для определенного пользовательского действия, можно воспользоваться одним руководства показано далее на этой странице. В заключение:

1.  На портале Azure **Обзор** для поиска данных фабрики.
2.  Используйте **схемы** и нажмите на кнопку Просмотр схемы фабрики данных **набора данных** таблицу, которая следует за определенный **конвейера** которого имеет настраиваемое действие. 
3.  В **таблицы** щелкните фрагмент заинтересованность в выноске **фрагменты проблемы** период времени, необходимо изучить.
4.  Подробные **срез данных** выноску будут отображаться и могут быть перечислены несколько **действие выполняется** для диаграммы. Щелкните **действия** из списка. 
5.   **Сведения о выполнении действия** появится выноски. Он отображает **сообщение об ошибке** в середине выноски, а также несколько **файлы журналов** перечислены в нижней части выноски, связанной с запуска действия.
	- Журналы, система 0.log
	- Состояние
	- Состояние или выход
	- Состояние и stderr
	- Состояние и stdout

6. Щелкните первый **файл журнала** элемент в списке, а журнал будет открыт в новая выноска с полный текст, отображаемый для прочтения. Просмотрите текст каждого из журналов, щелкнув каждый из них. Открыть выноску просмотра текста. Можно щелкнуть **загрузки** чтобы загрузить текстовый файл для просмотра в автономном режиме необязательно.

Один **распространенных ошибок** от настраиваемого действия является выполнение пакета завершилось код выхода «1». В разделе "wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/состояние/stderr" Дополнительные сведения.

Для получения дополнительных сведений для этого типа ошибки, откройте **stderr** файл. Об истечении времени ожидания, например, это является одной из распространенных ошибок, видны: сведений mapreduce. Задания: Идентификатор задачи: attempt_1424212573646_0168_m_000000_0, состояние: сбой AttemptID:attempt_1424212573646_0168_m_000000_0 после истечения времени ожидания в секундах 600

Такая же ошибка может появиться несколько раз, если задание повторять 3 раза, например по длительности 30 и более минут.

Эта ошибка времени ожидания указывает 600 секунды (10 минут) произошло времени ожидания. Обычно это означает, что пользовательское приложение .net не выполнялась обновления состояния для 10 минут. Если приложение является висячей или остановлены, на что-то ожидание слишком длинный 10 минут, время ожидания — это механизм безопасности, чтобы не допустить его бесконечное время ожидания и задержки конвейера фабрика данных Azure.

Это время ожидания является источником в конфигурации кластера HDInsight, связаны в пользовательском действии. Параметр **mapred.task.timeout**, который по умолчанию 600000 миллисекунд, как указано в параметрах по умолчанию Apache: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Можно переопределять это значение по умолчанию путем изменения значения по умолчанию во время подготовки подготовки кластера HDInsight. При использовании фабрики данных Azure и **HDInsight по требованию** связанные службы, можно добавить свойство JSON рядом свойств HDInsightOnDemandLinkedService JSON. Например можно увеличить значение 20 минут с помощью этого свойства JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Дополнительный контекст и полный пример JSON, чтобы изменить эти карты уменьшить см. свойства конфигурации пример 3 в в документации MSDN https://msdn.microsoft.com/library/azure/dn893526.aspx

## Проблема: PowerShell произойдет сбой запроса с ошибка 400 Неверный запрос «Поставщик зарегистрированного ресурса не найден...»

Начиная с 10 марта 2015 г. будут поддерживаться фабрики данных Azure PowerShell раннее закрытого просмотра версии 2014-05-01-preview, 2014-07-01-preview и 2014-08-01-preview. Рекомендуется использовать последнюю версию командлетов ADF, которые теперь являются частью Azure PowerShell загрузить, например загрузку с этого URL-адреса http://go.microsoft.com/?linkid=9811175&clcid=0x409

При использовании неподдерживаемых версий пакета SDK для Azure PowerShell могут возникать следующие ошибки:

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


## <a name="copywalkthrough"></a> Пошаговое руководство: Устранение ошибки с копированием данных
В этом пошаговом руководстве на примере ошибки, которая будет умышленно сделана при выполнении инструкций статьи «Приступая к работе с фабрикой данных», вы узнайте, как можно использовать портал Azure для устранения ошибок.

### Предварительные требования
1. Работа с учебником в [Приступая к работе с фабрикой данных Azure][adfgetstarted] статьи.
2. Убедитесь, что **ADFTutorialDataFactory** создает данные в **emp** таблицы в базе данных SQL Azure.  
3. Теперь, удалить **emp** таблицы (** перетаскивания таблицы emp **) из базы данных SQL Azure. Это приведет к ошибке.
4. Выполните следующую команду **Azure PowerShell** для обновления активного периода для конвейера, чтобы он пытается записать данные на **emp** таблицу, которая больше не существует.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Замените <b>StartDateTime</b> значение с текущего дня и <b>EndDateTime</b> значение со следующего дня.


### Устранения ошибки с помощью портала предварительной версии Azure

1.	Имя входа для [предварительную версию портала Azure][azure-preview-portal]. 
2.	Щелкните **ADFTutorialDataFactory** из **начальной**. Если вы не видите фабрику данных ссылку на **начальной**, щелкните **Обзор** концентратор и нажмите кнопку **все**. Щелкните **данных фабрики...** в **Обзор** стоечный модуль и нажмите кнопку **ADFTutorialDataFactory**.
3.	Обратите внимание, что отображается **с ошибками** на **наборы данных** плитки. Щелкните **с ошибками**. Вы увидите **наборов данных с ошибками** выноски.

	![Фабрика данных со ссылкой на ошибки][image-data-factory-troubleshoot-with-error-link]

4. В **наборы данных** выноска ошибки щелкните **EmpSQLTable** для просмотра **таблицы** выноски.

	![Наборы данных с ошибками выноска][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. В **таблицы** выноску, вы увидите фрагменты проблему, т. е. фрагменты с ошибкой в **фрагменты проблемы** списка в нижней части. Можно также просмотреть все последние фрагменты с ошибками в **последние фрагменты** списка. Щелкните срез в **фрагменты проблемы** списка.

	![Выноска таблицы с фрагментами проблемы][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	При нажатии кнопки **фрагменты проблемы** (но не в определенной проблемы), вы увидите **СРЕЗЫ данных** выноску, а затем нажмите кнопку **срез конкретную проблему** для просмотра **СРЕЗ данных** слайдов для среза выбранных данных.

6. В **СРЕЗ данных** выноски для **EmpSQLTable**, можно просмотреть все **действие выполняется** для среза в списке в нижней. Щелкните **Действие запуска** из списка, который не удалось.

	![Выноска срез данных с Активные запуски][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. В **сведения о выполнении действия** выноски для действия запуска выбранного, следует просмотреть сведения об ошибке. В этом случае можно увидеть: **недопустимое имя объекта «emp»**.

	![Сведения о выполнении с ошибкой действия][image-data-factory-troubleshoot-activity-run-with-error]

Чтобы устранить эту проблему, создайте **emp** таблицы SQL с помощью скрипта из [Приступая к работе с фабрикой данных][adfgetstarted] статьи.


### Устранение ошибки с помощью командлетов Azure PowerShell
1.	Запустите **Azure PowerShell**. 
2.	Переключитесь в **AzureResourceManager** режиме как командлеты фабрика данных доступны только в этом режиме.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы увидите срез с состоянием: не удалось.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Замените **StartDateTime** StartDateTime значением, указанным для **AzureDataFactoryPipelineActivePeriod набора**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Примечание **Запуск** время для среза проблемы (срез с **состояние** значение **Сбой**) в выходных данных. 
4. Теперь запустите **Get AzureDataFactoryRun** для получения сведений о действии, запустите для диаграммы.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Значение **StartDateTime** время начала для среза ошибки или проблемы, записанными на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
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

 

## <a name="pighivewalkthrough"></a> Пошаговое руководство: Устранение ошибки обработки Hive и Pig
Это пошаговое руководство содержит инструкции по устранению ошибки с помощью обработки Hive/Pig, используя предварительную версию портала Azure и Azure PowerShell.


### Пошаговое руководство: Использование портале для устранения ошибки обработки Pig и Hive
В этом сценарии набор данных находится в состоянии ошибки из-за сбоя обработки Hive в кластере HDInsight.

1. Щелкните **с ошибками** на **наборы данных** на плитке **ФАБРИКА данных** домашней страницы.

	![Ошибки ссылку на плитке наборов данных][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. В **наборов данных с ошибками** стоечный модуль, щелкните **таблицы** заинтересованы в.

	![Наборы данных с ошибками выноска][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. В **таблицы** стоечный модуль, щелкните **срез проблемы** с **состояние** значение **Failed**.

	![Таблицы с фрагментами проблемы][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. В **СРЕЗ данных** стоечный модуль, щелкните **действия выполняются в** вызвавшего сбой.

	![Срез данных с сбой запуска][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. В **сведения о выполнении действия** стоечный модуль, можно загрузить файлы, связанные с обработкой HDInsight. Щелкните **загрузки** для **состояние и stderr** для загрузки файла журнала ошибок, содержащий сведения об ошибке.

	![Действия, сведения о выполнении с ссылку для загрузки][image-data-factory-troubleshoot-activity-run-details]

    
### Пошаговое руководство: Использование Azure PowerShell для устранения ошибки обработки Pig и Hive
1.	Запустите **Azure PowerShell**. 
2.	Переключитесь в **AzureResourceManager** режиме как командлеты фабрика данных доступны только в этом режиме.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы увидите срез с состоянием: не удалось.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Замените **StartDateTime** StartDateTime значением, указанным для **AzureDataFactoryPipelineActivePeriod набора**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Примечание **Запуск** время для среза проблемы (срез с **состояние** значение **Сбой**) в выходных данных. 
4. Теперь запустите **Get AzureDataFactoryRun** для получения сведений о действии, запустите для диаграммы.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Значение **StartDateTime** время начала для среза ошибки или проблемы, записанными на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
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

6. Можно запустить **Сохранить AzureDataFactoryLog** командлет с значение идентификатора, см. выше в выходных данных и журнал загрузить файлы с помощью **- DownloadLogs** параметр командлета.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

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

<!---HONumber=GIT-SubDir-->