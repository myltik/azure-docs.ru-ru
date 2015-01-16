<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Устранение неполадок фабрики данных Azure" description="Узнайте, как устранять неполадки при использовании фабрики данных Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Устранение неполадок фабрики данных
Вы можете устранять неполадки фабрики данных Azure с помощью портала Azure (или) командлетов Azure PowerShell. Эта статья содержит пошаговые инструкции, показывающие, как с помощью портала Azure быстро устранять ошибки, возникающие при работе с фабрикой данных. 

## Содержание

- [Пошаговое руководство: Устранение ошибки копирования данных](#copywalkthrough)
- [Пошаговое руководство: Устранение ошибки обработки Hive/Pig](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Пошаговое руководство: Устранение ошибки копирования данных
В этом пошаговом руководстве на примере ошибки, которая будет умышленно сделана при выполнении инструкций статьи "Приступая к работе с фабрикой данных", вы узнайте, как можно использовать портал Azure для устранения ошибок.

### Предварительные требования
1. Выполните инструкции, описанные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
2. Убедитесь, что **ADFTutorialDataFactory** производит данные в таблице **emp** в базе данных SQL Azure.  
3. Теперь удалите таблицу **emp** (**drop table emp**) из базы данных SQL Azure. Это приведет к ошибке.
4. Выполните следующую команду в **Azure PowerShell**, чтобы обновить период активности конвейера. Он попытается записать данные в таблицу **emp**, которая больше не существует.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Замените значение <b>StartDateTime</b> на текущий день, а значение <b>EndDateTime</b> - на следующий день. 


### Устранения ошибки с помощью портала предварительной версии Azure

1.	Войдите на [портал предварительной версии Azure][azure-preview-portal]. 
2.	Щелкните **ADFTutorialDataFactory** на **начальной панели**. Если вы не видите ссылку на фабрику данных на **начальной панели**, щелкните **BROWSE** (Обзор), а затем щелкните **Everything** (Все элементы). Щелкните **Data factories...** (Фабрики данных...) в колонке **Browse** (Обзор) и выберите **ADFTutorialDataFactory**.
3.	Обратите внимание на надпись **With errors** (С ошибками), которая отображается в области **Наборы данных**. Щелкните **With errors** (С ошибками). После этого должна отобразиться колонка **Datasets with errors** (Наборы данных с ошибками).

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. В колонке **Datasets** with errors (Наборы данных с ошибками) щелкните **EmpSQLTable**, чтобы просмотреть колонку **TABLE** (Таблица).	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. В колонке **TABLE** (Таблица) должны отобразиться проблемные срезы, то есть срезы с ошибкой в списке **Problem slices** (Проблемные срезы) внизу. Вы также можете видеть недавние срезы с ошибками в списке **Recent slices** (Недавние срезы). Щелкните срез в списке **Problem slices** (Проблемные срезы). 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Если вы выберите **Problem slices** (Проблемные срезы) (не по конкретной проблеме), то увидите колонку **DATA SLICES** (Срезы данных); затем, если щелкнуть **срез по конкретной проблеме**, то увидите слайд **DATA SLICE** (Срез данных) для выбранного среза данных.

6. В колонке **DATA SLICE** (Срез данных) для **EmpSQLTable** вы видите **выполняемые действия** для среза в списке внизу. Выберите из списка **выполняемое действие** с ошибкой.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. В колонке **Activity Run Details** (Подробности выполняемого действия) для выбранного вами выполняемого действия должны отобразиться подробные сведения об ошибке. В этом сценарии вы увидите: **Invalid object name 'emp'** (Недопустимое имя объекта "emp").

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Чтобы устранить эту проблему, с помощью скрипта SQL создайте таблицу **emp**, как описано в статье [Приступая к работе с фабрикой данных][adfgetstarted].


### Устранение ошибки с помощью командлетов Azure PowerShell
1.	Запустите **Azure PowerShell**. 
2.	Переключитесь в режим **AzureResourceManager**, поскольку командлеты фабрики данных доступны только в этом режиме.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием: Failed (Ошибка).	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Замените **StartDateTime** значением StartDateTime, которое вы указали для **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Запишите значение **Start** (Время начала) для проблемного среза (среза, у которого **Status** (Состояние) имеет значение **Failed** (Ошибка)) в выходных данных. 
4. Затем запустите командлет **Get-AzureDataFactoryRun**, чтобы получить подробные сведения о выполняемом действии для среза.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	Значение **StartDateTime** - это значение времени начала для проблемного (ошибочного) среза, которое вы записали в предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
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

 

## <a name="pighavewalkthrough"></a>Пошаговое руководство: Устранение ошибки обработки Hive/Pig
Это пошаговое руководство содержит инструкции по устранению ошибки с помощью обработки Hive/Pig, используя предварительную версию портала Azure и Azure PowerShell. 


### Пошаговое руководство: устранение ошибки с помощью обработки Pig/Hive, используя портал Azure
В этом сценарии набор данных находится в состоянии ошибки из-за сбоя обработки Hive в кластере HDInsight.

1. Щелкните **With errors** (С ошибками)в области **Наборы данных** на домашней странице **ФАБРИКА ДАННЫХ**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. В колонке **Datasets with errors** (Наборы данных с ошибками) щелкните **таблицу**, которая вас интересует.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. В колонке **ТАБЛИЦА** щелкните **проблемный срез**, имеющий **СОСТОЯНИЕ** **Failed** (Ошибка).

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. В колонке **СРЕЗ ДАННЫХ** щелкните **выполняемое действие**, которое выдало ошибку.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Вы можете скачать файлы, связанные с обработкой HDInsight, в колонке **ACTIVITY RUN DETAILS** (Подробности выполняемого действия). Чтобы скачать файл журнала ошибок, содержащий подробные сведения об ошибке, щелкните **Скачать** для **Status/stderr** (Состояние/stderr).

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Пошаговое руководство: устранение ошибки с помощью обработки Pig/Hive, используя Azure PowerShell
1.	Запустите **Azure PowerShell**. 
2.	Переключитесь в режим **AzureResourceManager**, поскольку командлеты фабрики данных доступны только в этом режиме.

         
		switch-azuremode AzureResourceManager

3. Выполните команду Get-AzureDataFactorySlice, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием: Failed (Ошибка).	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Замените **StartDateTime** значением StartDateTime, которое вы указали для **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Запишите значение **Start** (Время начала) для проблемного среза (среза, у которого **Status** (Состояние) имеет значение **Failed** (Ошибка)) в выходных данных. 
4. Затем запустите командлет **Get-AzureDataFactoryRun**, чтобы получить подробные сведения о выполняемом действии для среза.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	Значение **StartDateTime** - это значение времени начала для проблемного (ошибочного) среза, которое вы записали в предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
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

6. Вы можете запустить командлет **Save-AzureDataFactoryLog** со значением идентификатора, которое вы видели в приведенных выше выходных данных, и скачать файлы журнала, используя параметр **-DownloadLogs** для командлета.

## Советы по устранению неполадок

### Не удается подключиться к локальному серверу SQL Server 
Убедитесь, что сервер SQL Server доступен для компьютера, на котором установлен шлюз.


1. Проверьте связь компьютера, где установлен сервер SQL Server
2. На компьютере, на котором установлен шлюз, попробуйте подключиться к экземпляру SQL Server, используя учетные данные, которые вы указали с помощью SQL Server Management Studio (SSMS) на портале Azure.

### Сбой операции копирования
1. Запустите диспетчер конфигураций шлюза управления данными на компьютере, на котором установлен шлюз. Убедитесь, что для параметра **Gateway name** (Имя шлюза) задано значение имени логического шлюза на **портале Azure**, **Gateway key status** (Состояние ключа шлюза) задано значение **registered** (зарегистрировано), а **Service status** (Состояние службы) задано значение **Started** (Запущено). 
2. Запустите **Просмотр событий**. Разверните **Журналы приложений и служб** и щелкните **Шлюз управления данными**. Проверьте наличие ошибок, относящихся к шлюзу управления данными. 



## См. также

Статья | Описание
------ | ---------------
[Включение конвейеров для работы с локальными данными][use-onpremises-datasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Использование сценариев pig и hive с фабрикой данных][use-pig-and-hive-with-data-factory] | В этой статье показано, как использовать действия HDInsight для выполнения сценариев hive и pig, чтобы обработать входные данные и получить выходные данные. 
[Учебник. Перемещение и обработка файлов журналов с помощью фабрики данных][adf-tutorial] | В этой статье рассматриваются все стороны реализации близкого к реалистичному сценария с использованием фабрики данных Azure для преобразования данных из файлов журналов в аналитику.
[Использование настраиваемых действий в фабрике данных][use-custom-activities] | Эта статья представляет собой пошаговое руководство с указаниями по созданию настраиваемых действий и их использованию в конвейере. 
[Мониторинг и управление фабрикой данных Azure с помощью PowerShell][monitor-manage-using-powershell] | В этой статье описывается, как выполнять мониторинг фабрики данных Azure с помощью командлетов Azure PowerShell. 
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure. Вы можете использовать руководство в этой статье на ADFTutorialDataFactory путем введения ошибки (удалив таблицу в базе данных Azure SQL). 
[Справочник разработчика фабрик данных Azure][developer-reference] | Справочник разработчика содержит разнообразные материалы по использованию командлетов, сценариев JSON, функций и т. д. 
[Справочник по командлетам фабрик данных Azure][cmdlet-reference] | Этот справочник содержит информацию о всех **командлетах фабрики данных**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
