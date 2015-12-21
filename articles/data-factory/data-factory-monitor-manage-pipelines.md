<properties 
	pageTitle="Мониторинг конвейеров фабрики данных Azure и управление ими" 
	description="Узнайте, как с помощью классического портала Azure и Azure PowerShell отслеживать состояние созданных конвейеров и фабрик данных Azure и управлять ими." 
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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Мониторинг конвейеров фабрики данных Azure и управление ими
Служба фабрики данных предоставляет полное и достоверное представление о службах хранения, обработки и перемещения данных. Она помогает быстро оценить работоспособность конвейера данных от начала до конца, выявить проблемы и при необходимости предпринять действия по исправлению. Вы также можете в визуальном режиме отслеживать происхождение данных и взаимосвязи между разными данными во всех ваших источниках, а также просматривать журналы по выполнению заданий, работоспособности системы и зависимостям, не покидая одной панели мониторинга.

В этой статье описываются мониторинг, управление и отладка конвейеров. В ней также приводятся инструкции по настройке оповещений в случае сбоев.

## Состояния конвейеров и действий
Используя портал Azure, вы можете представлять фабрику данных в виде схемы, просматривать действия в конвейере, входные и выходные наборы данных и не только. В этом разделе также содержится информация о переходе среза из одного состояния в другое.

### Переход к фабрике данных
1.	Войдите на [портал Azure](http://portal.azure.com).
2.	Щелкните **Просмотреть все** и выберите **Фабрики данных**.
	
	![Просмотреть все -> Фабрики данных](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	В колонке **Фабрики данных** должны отобразиться все фабрики данных. 
4. В колонке "Фабрики данных" выберите фабрику данных, которая вас интересует. Откроется ее домашняя страница (колонка **Фабрика данных**).

	![Колонка "Фабрика данных"](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Представление фабрики данных в виде схемы
Представление схемы позволяет отслеживать состояние фабрики данных и всех ее ресурсов, а также управлять ими.

Щелкните **Схема** на домашней странице фабрики данных, чтобы представить ее в виде схемы.

![Представление схемы](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Вы можете увеличивать и уменьшать масштаб, выбирать 100%-ный масштаб или масштаб по размеру, блокировать структуру схемы, автоматически размещать конвейеры и таблицы, а также отображать направления преобразований (выделение восходящих и нисходящих элементов).
 

### Действия в конвейере 
1. Щелкните конвейер правой кнопкой мыши и выберите команду **Открыть конвейер**. Отобразятся все действия вместе с их входными и выходными наборами данных. Это удобно, когда конвейер состоит из нескольких действий, а вы хотите понять структуру взаимосвязей во всем конвейере.

	![Откройте меню конвейера](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. В следующем примере вы увидите два действия в конвейере с их входными и выходными данными: действие **JoinData** типа "Действие Hive HDInsight" и действие **EgressDataAzure** типа "Действие копирования". 
	
	![Действия в конвейере](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Чтобы вернуться на домашнюю страницу фабрики данных, щелкните ссылку "Фабрика данных" в строке навигации в левом верхнем углу.

	![Возврат к фабрике данных](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### Просмотр состояния действий в конвейере
Вы можете просмотреть текущее состояние отдельного действия, отобразив состояние любого созданного им набора данных.

В примере ниже действие **BlobPartitionHiveActivity** успешно выполнено и создан набор данных **PartitionedProductsUsageTable** с состоянием **Ready** (Готов).

![Состояние конвейера](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Чтобы отобразить все срезы, созданные в рамках отдельных циклов выполнения действия в конвейере, дважды щелкните **PartitionedProductsUsageTable** в представлении схемы. Вы увидите, что действие **BlobPartitionHiveActivity** успешно выполнялось раз в месяц последние восемь месяцев и созданные срезы имеют состояние **Ready** (Готов).

Срезы наборов данных в фабрике данных могут находиться в одном из следующих состояний:

<table>
<tr>
	<th align="left">Состояние</th><th align="left">Подсостояние</th><th align="left">Описание</th>
</tr>
<tr>
	<td rowspan="8">Waiting</td><td>ScheduleTime</td><td>Время для выполнения среза еще не пришло.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Восходящие зависимости не готовы.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Вычислительные ресурсы недоступны.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Все экземпляры действия заняты выполнением других срезов.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Действие приостановлено, и до его возобновления выполнять срезы нельзя.</td>
</tr>
<tr>
<td>Retry</td><td>Действие будет выполнено повторно.</td>
</tr>
<tr>
<td>Проверка</td><td>Проверка еще не начата.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ожидание повторения проверки.</td>
</tr>
<tr>
&lt;tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Проверка выполняется.</td>
</tr>
<td></td>
<td>Срез обрабатывается.</td>
</tr>
<tr>
<td rowspan="4">Failed</td><td>TimedOut</td><td>Выполнение заняло больше времени, чем это разрешено для действия.</td>
</tr>
<tr>
<td>Canceled</td><td>Отменено пользователем.</td>
</tr>
<tr>
<td>Проверка</td><td>Сбой проверки.</td>
</tr>
<tr>
<td></td><td>Не удалось создать и/или проверить срез.</td>
</tr>
<td>Ready</td><td></td><td>Срез готов к использованию.</td>
</tr>
<tr>
<td>Skipped</td><td></td><td>Срез не обработан.</td>
</tr>
<tr>
<td>None</td><td></td><td>Срез, который ранее существовал с другим состоянием, но был сброшен.</td>
</tr>
</table>



Чтобы просмотреть сведения о срезе, щелкните запись среза в колонке **Недавно обновленные срезы**.

![Сведения о срезе](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Если срез выполнялся несколько раз, вы увидите несколько строк в списке **Циклы выполнения действия**.

![Циклы выполнения действия со срезом](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

Чтобы просмотреть сведения о выполнении действия, щелкните запись цикла в списке **Циклы выполнения действия**. Будут отображаться все файлы журналов и сообщения об ошибках, если таковые были. Это позволяет просмотреть и обработать файлы журналов непосредственно из фабрики данных.

![Сведения о цикле выполнения действия](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Если срез не находится в состоянии **Готов**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**. Это очень полезно, когда срез находится в состоянии **Ожидание** и требуется просмотреть восходящие зависимости, влияющие на состояние текущего неготового среза.

![Вышестоящие срезы в состоянии, отличном от Ready](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### Схема состояний наборов данных
После развертывания фабрики данных и определения периода активности конвейера срезы наборов данных переходят из одного состояния в другое. В настоящее время переходы между состояниями выполняются в соответствии со следующей схемой.

![Схема состояний](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Поток переходов между состояниями выглядит так: Waiting -> In-Progress/In-Progress (Validating) -> Ready/Failed

Изначально срезы находятся в состоянии **Ожидание**. Таким образом выполняются все предварительные условия. Затем начинается выполнение действия, и срез переходит в состояние **Выполняется**. В зависимости от того, как завершится действие, срез перейдет в состояние **Готов** или **Ошибка**.

Вы можете сбросить состояние среза **Готов** или **Ошибка** обратно в состояние **Ошибка**. Вы также можете установить для среза флажок **Пропустить** — действие со срезом не будет выполнено, и срез не будет обработан.


## Управление конвейерами
Управлять конвейерами можно с помощью Azure Powershell. Например, вы можете приостановить и возобновить работу конвейеров, используя командлеты Azure PowerShell.

### Приостановка и возобновление работы конвейеров
Вы можете приостановить работу конвейеров с помощью командлета PowerShell **Suspend-AzureRmDataFactoryPipeline**. Это полезно, например, если вы если выявили проблему с данными и не хотите обрабатывать их с помощью конвейера до ее устранения.

Например, на следующем снимке экрана показано, что в фабрике **productrecgamalbox1dev** в конвейере **PartitionProductsUsagePipeline** обнаружена проблема и конвейер нужно приостановить.

![Конвейер будет приостановлен](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Чтобы приостановить конвейер **PartitionProductsUsagePipeline**, выполните следующую команду PowerShell.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Например:

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Устранив проблему в конвейере **PartitionProductsUsagePipeline**, возобновите его работу. Для этого выполните следующую команду PowerShell.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Например:

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## Отладка конвейеров
Фабрика данных Azure предоставляет широкие возможности отладки и устранения неполадок с конвейерами с помощью классического портала Azure и Azure PowerShell.

### Поиск ошибок в конвейере
Если при выполнении действия в конвейере происходит сбой, созданный конвейером набор данных будет находиться в состоянии Error (Ошибка). Вы можете устранить неполадки или выполнить отладку в фабрике данных Azure, используя следующие механизмы.

#### Отладка ошибок с помощью классического портала Azure

1.	Щелкните ссылку **С ошибками** на плитке **Наборы данных** на домашней странице фабрики данных.
	
	![Плитка "Наборы данных с ошибками"](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	В колонке **Наборы данных с ошибками** щелкните интересующую вас таблицу.

	![Колонка "Наборы данных с ошибками"](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	В колонке **ТАБЛИЦА** щелкните проблемный срез, для которого в поле **СОСТОЯНИЕ** указано значение **Ошибка**.

	![Колонка "Таблица" с проблемным срезом](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	В колонке **СРЕЗ ДАННЫХ** щелкните цикл выполнения действия, который завершился ошибкой.
	
	![Срез данных с ошибкой](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	В колонке **СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ** вы можете скачать файлы, связанные с обработкой HDInsight. Чтобы скачать файл журнала ошибок, содержащий подробные сведения об ошибке, щелкните Скачать для Status/stderr (Состояние/stderr).

	![Колонка "Сведения о циклах выполнения действия" с ошибкой](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### Отладка ошибок с помощью PowerShell
1.	Запустите **Azure PowerShell**.
2.	Перейдите в режим **AzureResourceManager**, так как командлеты фабрики данных доступны только в нем.

		switch-azuremode AzureResourceManager
3.	Выполните команду **Get-AzureRmDataFactorySlice**, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием **Ошибка**.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Например:


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Замените **StartDateTime** на значение StartDateTime, которое вы указали для Set-AzureRmDataFactoryPipelineActivePeriod.
4. Теперь запустите командлет **Get-AzureRmDataFactoryRun**, чтобы получить подробные сведения о выполненном для среза действии.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Например:


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	В качестве значения StartDateTime укажите время начала для проблемного (содержащего ошибку) среза, которое вы отметили на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
5. 	Вы должны увидеть выходные данные с подробными сведениями об ошибке (аналогично следующему):

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	Вы можете выполнить командлет **Save-AzureRmDataFactoryLog** с идентификатором, который отображается в результате описанных выше команд, и скачать файлы журналов, используя параметр **-DownloadLogs** командлета.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## Повторная обработка проблемных срезов в конвейере

### Использование классического портала Azure.

После устранения неполадок и отладки ошибок в конвейере можно повторно обработать срез с ошибками. Для этого выберите срез и на панели команд нажмите кнопку **Запустить**.

![Повторная обработка среза](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Если срез не прошел проверку из-за ошибки политики (например, данные недоступны), вы можете исправить ошибку и повторно выполнить проверку, нажав на панели команд кнопку **Проверить**. ![Исправление ошибок и проверка](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Использование Azure PowerShell

Вы можете повторно обработать проблемные срезы, используя командлет Set-AzureRmDataFactorySliceStatus.

	Set-AzureRmDataFactorySliceStatus [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Status] <String> [[-UpdateType] <String> ] [-Profile <AzureProfile> ] [ <CommonParameters>]

**Пример.** В следующем примере состояние всех срезов в таблице DAWikiAggregatedData в фабрике данных WikiADF меняется на PendingExecution.

**Примечание.** Для UpdateType задано значение UpstreamInPipeline. Это означает, что каждый срез в этой таблице и всех зависимых (восходящих) таблицах, используемых в качестве входных таблиц для действия в конвейере, переходит в состояние PendingExecution. Этот параметр может иметь еще одно значение — Individual.

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status PendingExecution -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## Создание оповещений
Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. Вы можете создавать оповещения об этих событиях. Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Рекомендуется использовать события для наблюдения в режиме реального времени, а метрики — для статистических целей.

### Оповещения о событиях
События Azure позволяют получить представление о том, что происходит в ресурсах Azure. Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. При использовании фабрики данных Azure события создаются, когда:

- создается, обновляется или удаляется фабрика данных Azure;
- запускается или завершается обработка данных (которая называется циклом выполнения);
- создается и удаляется кластер HDInsight по требованию.

Вы можете создавать оповещения в этих пользовательских событиях и настраивать их для отправки уведомлений по электронной почте администраторам и соадминистраторам подписки. Кроме того, вы можете указать дополнительные адреса электронной почты пользователей, которые должны получать уведомления по электронной почте при выполнении определенных условий. Это очень полезно, когда нужно получать уведомления о сбоях, но не требуется постоянно отслеживать состояние фабрики данных.

#### Настройка определения оповещения
Чтобы задать определение оповещения, создайте файл JSON, описывающий операции, о которых вы хотите получать оповещения. В следующем примере отправляется электронное уведомление об операции RunFinished. Другими словами, оно отправляется в случае завершения цикла выполнения действия в фабрике данных со сбоем (состояние = FailedExecution).

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                            "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

В приведенном выше определении JSON **subStatus** можно удалить, если вы не хотите получать оповещение при конкретном сбое.

Приведенный выше пример устанавливает оповещения для всех фабрик данных в вашей подписке. Если вам необходимо установить оповещение для определенной фабрики данных, можно указать ее **resourceUri** в блоке **dataSource**, как показано ниже:

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

Список возможных операций и состояний (а также дополнительных состояний) см. в следующей таблице.

Имя операции | Состояние | Дополнительное состояние
-------------- | ------ | ----------
RunStarted | Started | Starting
RunFinished | Failed / Succeeded | <p>FailedResourceAllocation</p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p><Canceled/p><p>FailedValidation</p><p>Abandoned</p>
SliceOnTime | In Progress | Ontime
SliceDelayed | In Progress | Late
OnDemandClusterCreateStarted | Started
OnDemandClusterCreateSuccessful | Succeeded
OnDemandClusterDeleted | Succeeded

Подробные сведения об элементах JSON, используемые в примере выше, см. в разделе [Создание правила оповещения](https://msdn.microsoft.com/library/azure/dn510366.aspx).

#### Развертывание оповещения 
Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureResourceGroupDeployment**, как показано в следующем примере:

	New-AzureResourceGroupDeployment -ResourceGroupName adf     -TemplateFile .\ADFAlertFailedSlice.json  

После успешного завершения развертывания группы ресурсов вы увидите следующие сообщения:

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

#### Получение списка развертываний групп ресурсов Azure
Чтобы получить список развертываний групп ресурсов Azure, используйте командлет **Get-AzureResourceGroupDeployment**, как показано в следующем примере:

	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### Устранение неполадок пользовательских событий


- Вы можете просмотреть все созданные события, щелкнув элемент **Операции**, и настроить уведомления для любых операций, отображаемых в колонке **События**.

![Операции](./media/data-factory-monitor-manage-pipelines/operations.png)


- Сведения о командлетах PowerShell, которые можно использовать для добавления, получения и удаления предупреждений, см. в статье [Командлеты Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx). Далее приведено несколько примеров использования командлета **Get-AlertRule**.


	ResourceGroupName : mdwevent
	Location          : westus
	ProvisioningState : Succeeded
	Resources         :
                    Name                  Type                                 Location
                    ====================  ===================================  ========
                    abhieventtest1        Microsoft.DataFactory/dataFactories  westus
                    abhieventtest2        Microsoft.DataFactory/dataFactories  westus
                    FailedValidationRuns  microsoft.insights/alertrules        eastus

	Выполните следующие команды get-help, чтобы просмотреть сведения и примеры для командлета Get-AlertRule.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- Если события-триггеры отображаются в колонке на портале, но вы не получаете электронных уведомлений, убедитесь, что ваш адрес электронной почты может получать сообщения от внешних отправителей. Получение оповещений может быть заблокировано в параметрах электронной почты.

### Оповещения по метрикам
Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Вы можете отслеживать и создавать оповещения по следующим метрикам срезов в фабрике данных:
 
- циклы выполнения со сбоем;
- успешные циклы выполнения.

Эти метрики позволяют пользователям получать общее представление обо всех успешных и проблемных циклах в фабрике данных. Метрики создаются при каждом цикле выполнения действия со срезом. Все созданные за час метрики собираются и передаются в вашу учетную запись хранения. Поэтому, чтобы включить метрики, необходимо настроить учетную запись хранения.

#### Включение метрик
Чтобы включить метрики, в колонке "Фабрика данных" выберите следующее:

**Мониторинг** > **Метрика** > **Параметры диагностики** > **Диагностика**.

В колонке **Диагностика** щелкните **Вкл.**, выберите учетную запись хранения и нажмите кнопку "Сохранить".

![Включить метрики](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

Поскольку метрики агрегируются ежечасно, между сохранением и отображением метрик в колонке мониторинга может пройти час.


### Настройка оповещений по метрикам

Чтобы настроить оповещения о метриках, в колонке "Фабрика данных" выберите **Мониторинг** > **Метрика** > **Добавить оповещение** > **Добавить правило оповещения**.

Введите необходимые сведения для правила, укажите электронные адреса, на которые будут отправляться оповещения, и нажмите кнопку **ОК**.


![Настройка оповещений по метрикам](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

После этого вы увидите на плитке "Правила оповещений", что новое правило оповещения включено:

![Правила оповещений включены](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

Поздравляем! Вы настроили свое первое оповещение по метрикам. Теперь вы будете получать электронное уведомление каждый раз, когда будет срабатывать правило.

### Уведомления об оповещениях
При возникновения условия, по которому срабатывает правило оповещения, вы будете получать соответствующее электронное уведомление о том, что оповещение активировано. После устранения условия (т. е. оно больше не соответствует правилу оповещения) вы получите электронное уведомление о том, что причина оповещения устранена.

Такое поведение отличается от ситуации, когда уведомления отправляются по каждому сбою, соответствующему правилу оповещения.

### Развертывание оповещений с помощью PowerShell
Вы можете развернуть оповещения по метрикам так же, как и по событиям.

**Определение оповещения:**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
Замените subscriptionId, resourceGroupName и dataFactoryName в приведенном выше примере на соответствующие значения.

Сейчас для *metricName* можно указать только два значения:
- FailedRuns или
- SuccessfulRuns.

**Развертывание оповещения**

Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureResourceGroupDeployment**, как показано в следующем примере:

	New-AzureResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

В случае успешного развертывания вы увидите следующее сообщение:

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           

<!---HONumber=AcomDC_1210_2015-->