<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Мониторинг и управление фабрикой данных Azure с помощью Azure PowerShell" description="Узнайте, как отслеживать и контролировать созданные фабрики данных Azure с помощью Azure PowerShell." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Мониторинг и управление фабрикой данных Azure с помощью Azure PowerShell
В следующей таблице перечислены командлеты, которые вы можете использовать для мониторинга и управления фабриками данных Azure с помощью Azure PowerShell. 

> [WACOM.NOTE] Полная документация по командлетам фабрики данных содержится в [справочнике по командлетам фабрики данных][cmdlet-reference]. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Сообщает информацию об определенной фабрике данных или всех фабриках данных в подписке Azure в пределах указанной группы ресурсов.
 
###Пример 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Эта команда возвращает все фабрики данных в группе ресурсов ADFTutorialResourceGroup.
 
###Пример 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Эта команда возвращает информацию о фабрике данных ADFTutorialDataFactory в группе ресурсов ADFTutorialResourceGroup. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
Командлет Get-AzureDataFactoryLinkedService получает информацию об определенной связанной службе или о всех связанных службах в фабрике данных Azure.

### Пример 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Эта команда возвращает информацию обо всех связанных службах в фабрике данных Azure ADFTutorialDataFactory.


Вы можете использовать параметр -DataFactory вместо параметров DataFactoryName и ResourceGroupName. Это позволит ввести имена группы ресурсов и фабрики только один раз и использовать объект фабрики данных в качестве параметра для всех командлетов, которые принимают в качестве параметров ResourceGroupName и DataFactoryName.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Пример 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Эта команда возвращает информацию о связанной службе MyBlobStore в фабрике данных Azure ADFTutorialDataFactory. 

Вы можете использовать параметр -DataFactory вместо параметров -ResourceGroup и -DataFactoryName, как показано ниже: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Командлет Get-AzureDataFactoryTable получает информацию об определенной таблице или всех таблицах в фабрике данных Azure. 

### Пример 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Эта команда возвращает информацию обо всех таблицах в фабрике данных Azure ADFTutorialDataFactory.

Вы можете использовать параметр -DataFactory вместо параметров -ResourceGroup и -DataFactoryName, как показано ниже: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Пример 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Эта команда возвращает информацию о таблице EmpTableFromBlob в фабрике данных Azure ADFTutorialDataFactory.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Командлет Get-AzureDataFactoryPipeline получает информацию об определенном конвейере или всех конвейерах в фабрике данных Azure.

### Пример 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Эта команда возвращает информацию обо всех конвейерах в фабрике данных Azure ADFTutorialDataFactory.

### Пример 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Получает информацию о конвейере ADFTutorialPipeline в фабрике данных Azure ADFTutorialDataFactory.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Командлет Get-AzureDataFactorySlice получает все срезы для таблицы в фабрике данных Azure, которые производятся после StartDateTime и до EndDateTime. Срез данных с состоянием Ready уже готов для использования зависимыми срезами.

В следующей таблице перечислены все состояния среза и их описания.

<table border="1">	
	<tr>
		<th align="left">Состояние</th>
		<th align="left">Описание</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>Обработка данных еще не началась.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>Обработка данных выполняется.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>Обработка данных завершена, и срез данных готов.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>При выполнении запуска, который производит срез, произошла ошибка.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Пропуск обработки среза.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Повторная попытка запуска, который производит срез.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Истекло время ожидания обработки данных среза.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>Срез данных ожидает проверки на соответствие политикам проверки перед обработкой.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Повторная попытка проверки среза.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Не удалось проверить срез.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>Срез будет в этом состоянии, если в таблице JSON указано LongRetry, и при регулярных повторных попытках среза произошли ошибки.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>Выполняется проверка среза (с учетом политик, определенных в таблице JSON).</td>
	</tr>

</table>

Используя командлеты Get-AzureDataFactoryRun и Save-AzureDataFactoryLog, вы можете проанализировать каждый из срезов глубже и просмотреть дополнительную информацию о запуске, который производит срез.

### Пример

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Эта команда получает все срезы для таблицы EmpSQLTable в фабрике данных Azure ADFTutorialDataFactory, произведенные после 2014-05-20T10:00:00 (GMT). Замените значения даты и времени значениями даты и времени начала, которые вы указали при запуске Set-AzureDataFactoryPipelineActivePeriod.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Командлет Get-AzureDataFactoryRun получает все данные запусков для среза данных таблицы в фабрике данных Azure.  Таблица в фабрике данных Azure состоит из срезов, сделанных по оси времени. Ширина среза определяется расписанием - ежечасно или ежедневно. Запуск - это единица обработки среза. Может быть один или несколько запусков для среза, если выполняются повторные попытки, или вы повторно производите срез в случае сбоя. Срез идентифицируется по времени начала. Поэтому для командлета Get-AzureDataFactoryRun необходимо передать время начала среза из результатов командлета Get-AzureDataFactorySlice.

Например, чтобы получить запуск для следующего среза, используйте 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Пример

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Эта команда возвращает все запуски для срезов таблицы EmpSQLTable в фабрике данных Azure ADFTutorialDataFactory, начиная с 16:00 (4 PM GMT) 21.05.2014.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Командлет Save-AzureDataFactoryLog скачивает файлы журнала, связанные с обработкой Azure HDInsight проектов Pig или Hive, или для пользовательских действий на вашем локальном жестком диске. Сначала запустите командлет Get-AzureDataFactoryRun для получения идентификатора запуска действия для среза данных, а затем воспользуйтесь этим идентификатором для получения файлов журнала из хранилища больших двоичных объектов (BLOB), связанного с кластером HDInsight. 

Если не указать параметр **-DownloadLogs**, командлет будет возвращать только расположение файлов журнала. 

Если указать параметр **-DownloadLogs**, не задав при этом выходного каталога (параметр **-Output **), файлы журнала будут загружаться в папку **Documents** по умолчанию. 

Если указать параметр **-DownloadLogs** и выходную папку (**-Output**), файлы журнала будут загружаться в указанную папку. 


### Пример 1
Эта команда сохраняет файлы журнала для запуска действия с идентификатором 841b77c9-d56c-48d1-99a3-8c16c3e77d39, где действие принадлежит конвейеру в фабрике данных с именем LogProcessingFactory в группе ресурсов с именем ADF. Файлы журнала сохраняются в папке C:\Test. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Пример 2
Эта команда сохраняет файлы журнала в папке Documents (по умолчанию).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Пример 3
Эта команда возвращает расположение файлов журнала. Обратите внимание, что параметр -DownloadLogs не указан. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
Командлет Get-AzureDataFactoryGateway получает информацию об определенном шлюзе или всех шлюзах в фабрике данных Azure. Необходимо установить шлюз на локальном компьютере, чтобы иметь возможность добавлять локальный сервер SQL Server в фабрику данных как связанную службу.

### Пример 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Эта команда возвращает информацию обо всех шлюзах в фабрике данных Azure ADFTutorialDataFactory.

### Пример 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Эта команда возвращает информацию о шлюзе ADFTutorialGateway в фабрике данных Azure ADFTutorialDataFactory.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Этот командлет задает период активности для срезов данных, которые обрабатываются конвейером. При использовании Set-AzureDataFactorySliceStatus убедитесь, что дата начала и дата окончания среза находятся в периоде активности конвейера.

После создания конвейеров вы можете указать время, в течение которого будет выполняться обработка данных. Указывая период активности для конвейера, вы определяете интервал времени, в который будут производиться срезы данных на основе свойств доступности, определенных для каждой таблицы фабрики данных Azure.

### Пример

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Эта команда задает такой период активности для срезов данных, которые обрабатываются конвейером ADFTutoiralPipeline: с 16:00 (4 PM GMT) 21.05.2014 до 16:00 (4 PM GMT) 22.05.2014.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Задает состояние среза для таблицы. Дата начала и дата окончания среза должны находиться в периоде активности конвейера.

### Поддерживаемые значения для состояния
Каждый срез данных для таблицы проходит различные этапы. Эти этапы немного отличаются в зависимости от того, указаны ли политики проверки.


- Если политики проверки не указаны: PendingExecution -> InProgress -> Ready
- Если политики проверки указаны: PendingExecution -> Pending Validation -> InProgress -> Ready

В следующей таблице приводится описание возможных состояний среза, а также указывается, можно ли задавать состояние с помощью Set-AzureDataFactorySliceStatus.

<table border="1">	
	<tr>
		<th>Состояние</th>
		<th>Описание</th>
		<th>Может ли задаваться с помощью командлета></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>Обработка данных еще не началась.</td>
		<td>Да</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>Обработка данных выполняется.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>Обработка данных завершена, и срез данных готов.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>При выполнении запуска, который производит срез, произошла ошибка.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Пропуск обработки среза.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Повторная попытка запуска, который производит срез.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Истекло время ожидания обработки данных.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>Срез данных ожидает проверки на соответствие политикам проверки перед обработкой.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Повторная попытка проверки среза.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Не удалось проверить срез.</td>
		<td>Нет</td>
	</tr>
	</table>


### Поддерживаемые значения - тип обновления
Когда вы указываете состояние среза, для каждой таблицы в фабрике данных Azure необходимо указать, применяется ли обновление состояний только к таблице или распространяется на все затронутые срезы.

<table border="1">	
	<tr>
		<th>Тип обновления</th>
		<th>Описание</th>
		<th>Может ли задаваться с помощью командлета</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>Задает состояние каждого среза для таблицы в указанном диапазоне времени</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Задает состояние каждого среза для таблицы и всех зависимых (вышестоящих) таблиц, используемых в качестве входных таблиц для действий в конвейере.</td>
		<td>Да</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Командлет Suspend-AzureDataFactoryPipeline приостанавливает указанный конвейер в фабрике данных Azure. Вы можете возобновить работу конвейера позже с помощью командлета Resume-AzureDataFactoryPipeline.

### Пример

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Эта команда приостанавливает конвейер ADFTutorialPipeline в фабрике данных Azure ADFTutorialDataFactory.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Командлет Resume-AzureDataFactoryPipeline возобновляет работу указанного конвейера, который находится в приостановленном состоянии в фабрике данных Azure. 

### Пример

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Эта команда в фабрике данных Azure ADFTutorialDataFactory возобновляет работу конвейера ADFTutorialPipeline, который был приостановлен с помощью команды Suspend-AzureDataFactoryPipeline.

## См. также

Статья | Описание
------ | ---------------
[Мониторинг и управление фабрикой данных Azure с помощью портала предварительной версии Azure][monitor-manage-using-portal] | В этой статье описывается, как выполнять мониторинг и управление фабрикой данных Azure с помощью портала предварительной версии Azure.
[Включение конвейеров для работы с локальными данными][use-onpremises-datasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Использование сценариев Pig и Hive с фабрикой данных][use-pig-and-hive-with-data-factory] | В этой статье показано, как использовать действия HDInsight для выполнения сценариев Hive и Pig, чтобы обработать входные данные и получить выходные данные. 
[Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] | В этой статье содержится подробное пошаговое руководство, которое показывает, как реализовать практически реальный сценарий с использованием фабрики данных Azure для преобразования данных из файлов журнала в подробные данные.
[Использование пользовательских действий в фабрике данных][use-custom-activities] | В этой статье приводится подробное руководство с пошаговыми инструкциями по созданию пользовательских действий и использованию их в конвейере. 
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полную справку по командлетам, сценарию JSON, функциям и т. д. 
[Справочник по командлетам фабрики данных Azure][cmdlet-reference] | Этот справочник содержит подробную информацию обо всех **командлетах фабрики данных**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--HONumber=35.2-->
