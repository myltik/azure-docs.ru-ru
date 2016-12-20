---
title: "Мониторинг конвейеров фабрики данных Azure и управление ими"
description: "Узнайте, как с помощью портала Azure и Azure PowerShell отслеживать состояние созданных конвейеров и фабрик данных Azure и управлять ими."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6a816e56400abe281b0422abbcd3415f3762a96e
ms.openlocfilehash: 43b828bad5d21fe0f452aa23bbdf6fff758525f2


---
# <a name="monitor-and-manage-azure-data-factory-pipelines"></a>Мониторинг конвейеров фабрики данных Azure и управление ими
> [!div class="op_single_selector"]
> * [Использование портала Azure или Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Использование приложения по мониторингу и управлению](data-factory-monitor-manage-app.md)
> 
> 

Служба фабрики данных предоставляет полное и достоверное представление о службах хранения, обработки и перемещения данных. Эта служба содержит панель мониторинга, с помощью которой можно выполнять следующие задачи: 

* быстро оценить работоспособность конвейера данных от начала до конца;
* выявить проблемы и при необходимости предпринять действия по исправлению; 
* отслеживать журнал преобразований данных; 
* отслеживать связи между данными во всех источниках;
* просматривать полную историю выполнения заданий, работоспособности системы и зависимостей.

В этой статье описываются мониторинг и отладка конвейеров, а также управление ими. В ней также приводятся инструкции по настройке оповещений в случае сбоев.

## <a name="understand-pipelines-and-activity-states"></a>Состояния конвейеров и действий
Используя портал Azure, можно сделать следующее:

* представлять фабрику данных в виде схемы;
* просматривать действия в конвейере;
* просматривать входные и выходные наборы данных;
* другое. 

В этом разделе также содержится информация о переходе среза из одного состояния в другое.   

### <a name="navigate-to-your-data-factory"></a>Переход к фабрике данных
1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните колонку **Фабрики данных** в меню слева. Если вы ее не видите, выберите **Больше служб** и щелкните **Фабрики данных** в категории **Аналитика**. 
   
   ![Просмотреть все -> Фабрики данных](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
   
   В колонке **Фабрики данных** должны отобразиться все фабрики данных. 
3. Выберите фабрику данных, которая вас интересует, в соответствующей колонке.
   
    ![выбор фабрики данных](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)  
4. Вы увидите ее домашнюю страницу (колонка **Фабрика данных**).
   
   ![Колонка "Фабрика данных"](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Представление фабрики данных в виде схемы
Представление схемы позволяет отслеживать состояние фабрики данных и всех ее ресурсов, а также управлять ими.

Щелкните **Схема** на домашней странице фабрики данных, чтобы представить ее в виде схемы.

![Представление схемы](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Вы можете увеличивать и уменьшать масштаб, выбирать 100%-ный масштаб или масштаб по размеру, блокировать структуру схемы и автоматически размещать конвейеры и таблицы. Кроме того, можно отображать сведения из журнала преобразований данных (выделение восходящих и нисходящих элементов).

### <a name="activities-inside-a-pipeline"></a>Действия в конвейере
1. Щелкните конвейер правой кнопкой мыши и выберите **Открыть конвейер**. Отобразятся все действия, а также входные и выходные наборы данных. Эта функция удобна, когда конвейер состоит из нескольких действий, а вы хотите понять структуру взаимосвязей во всем конвейере.
   
    ![Откройте меню конвейера](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. В следующем примере вы увидите два действия в конвейере с их входными и выходными данными: Действие **JoinData** типа "Действие Hive HDInsight" и действие **EgressDataAzure** типа "Действие копирования". 
   
    ![Действия в конвейере](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Чтобы вернуться на домашнюю страницу фабрики данных, щелкните ссылку "Фабрика данных" в строке навигации в левом верхнем углу.
   
    ![Возврат к фабрике данных](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-state-of-each-activity-inside-a-pipeline"></a>Просмотр состояния действий в конвейере
Вы можете просмотреть текущее состояние отдельного действия, отобразив состояние любого созданного им набора данных. 

В примере ниже действие **BlobPartitionHiveActivity** успешно выполнено и создан набор данных **PartitionedProductsUsageTable** с состоянием **Готово**.

![Состояние конвейера](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Чтобы отобразить все срезы, созданные в рамках отдельных циклов выполнения действия в конвейере, дважды щелкните **PartitionedProductsUsageTable** в представлении схемы. Можно заметить, что действие **BlobPartitionHiveActivity** успешно выполнялось раз в месяц последние восемь месяцев и созданные срезы имеют состояние **Готово**.

Срезы наборов данных в фабрике данных могут находиться в одном из следующих состояний.

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
<tr
<td rowspan="2">InProgress</td><td>Validating</td><td>Проверка выполняется.</td>
</tr>
<td>-</td>
<td>Срез обрабатывается.</td>
</tr>
<tr>
<td rowspan="4">Сбой</td><td>TimedOut</td><td>Выполнение заняло больше времени, чем это разрешено для действия.</td>
</tr>
<tr>
<td>Canceled</td><td>Отменено пользователем.</td>
</tr>
<tr>
<td>Проверка</td><td>Сбой проверки.</td>
</tr>
<tr>
<td>-</td><td>Не удалось создать и/или проверить срез.</td>
</tr>
<td>Ready</td><td>-</td><td>Срез готов к использованию.</td>
</tr>
<tr>
<td>Skipped</td><td>None</td><td>Срез не обработан.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Срез, который ранее существовал с другим состоянием, но был сброшен.</td>
</tr>
</table>



Чтобы просмотреть сведения о срезе, щелкните запись среза в колонке **Недавно обновленные срезы** .

![Сведения о срезе](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Если срез выполнялся несколько раз, вы увидите несколько строк в списке **Выполнения действий** . Чтобы просмотреть сведения о выполнении действия, щелкните запись цикла в списке **Циклы выполнения действия** . Отобразятся все файлы журналов и сообщения об ошибках, если таковые были. Эта функция удобна для просмотра и обработки файлов журналов непосредственно из фабрики данных.

![СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Если срез не находится в состоянии **Готово**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**. Эта функция удобна для просмотра вышестоящих зависимостей, если срез находится в состоянии **Ожидание** .

![Вышестоящие срезы в состоянии, отличном от Ready](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Схема состояний наборов данных
После развертывания фабрики данных и определения периода активности конвейера срезы наборов данных переходят из одного состояния в другое. В настоящее время переходы между состояниями выполняются в соответствии со следующей схемой.

![Схема состояний](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Поток переходов между состояниями выглядит так: "Ожидание" -> "Выполняется" или "Выполняется (проверка)" -> "Готово" или "Сбой".

Изначально срезы находятся в состоянии **Ожидание**. Таким образом выполняются все предварительные условия. Затем начинается выполнение действия, и срез переходит в состояние **Выполняется**. Выполнение действия может завершиться успешно или с ошибкой. В зависимости от того, как завершится действие, срез перейдет в состояние **Готово** или **Сбой**. 

Вы можете сбросить состояние среза **Готово** или **Сбой** обратно в состояние **Ожидание**. Вы также можете установить для среза флажок **Пропустить**— действие со срезом не выполняется, и срез не обрабатывается.

## <a name="manage-pipelines"></a>Управление конвейерами
Управлять конвейерами можно с помощью Azure Powershell. Например, вы можете приостановить и возобновить работу конвейеров, используя командлеты Azure PowerShell. 

### <a name="pause-and-resume-pipelines"></a>Приостановка и возобновление работы конвейеров
Вы можете приостановить работу конвейеров с помощью командлета PowerShell **Suspend-AzureRmDataFactoryPipeline** . Этот командлет полезен, если не требуется запускать конвейеры до устранения проблемы.

Например, на следующем снимке экрана показано, что в фабрике **productrecgamalbox1dev** в конвейере **PartitionProductsUsagePipeline** обнаружена проблема и конвейер нужно приостановить.

![Конвейер будет приостановлен](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Чтобы приостановить конвейер, выполните следующую команду PowerShell:

    Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Например:

    Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Устранив проблему в конвейере **PartitionProductsUsagePipeline**, возобновите его работу. Для этого выполните следующую команду PowerShell:

    Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Например:

    Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## <a name="debug-pipelines"></a>Отладка конвейеров
Фабрика данных Azure предоставляет широкие возможности отладки и устранения неполадок с конвейерами с помощью портала Azure и Azure PowerShell.

### <a name="find-errors-in-a-pipeline"></a>Поиск ошибок в конвейере
Если при выполнении действия в конвейере происходит сбой, созданный конвейером набор данных будет находиться в состоянии Error (Ошибка). Вы можете устранить неполадки или выполнить отладку в фабрике данных Azure, используя следующие механизмы.

#### <a name="use-azure-portal-to-debug-an-error"></a>Отладка ошибок с помощью портала Azure
1. В колонке **Таблица** щелкните проблемный срез с **состоянием** **Сбой**.
   
   ![Колонка "Таблица" с проблемным срезом](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. В колонке **СРЕЗ ДАННЫХ** щелкните цикл выполнения действия, который завершился ошибкой.
   
   ![Срез данных с ошибкой](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. В колонке **СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ** вы можете скачать файлы, связанные с обработкой HDInsight. Чтобы скачать файл журнала ошибок, содержащий подробные сведения об ошибке, щелкните Скачать для Status/stderr (Состояние/stderr).
   
   ![Колонка "Сведения о циклах выполнения действия" с ошибкой](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-the-powershell-to-debug-an-error"></a>Отладка ошибок с помощью PowerShell
1. Запустите **Azure PowerShell**.
2. Выполните команду **Get-AzureRmDataFactorySlice** , чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием **Ошибка**.        
   
         Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
   
   Например:
   
         Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
   
   Замените **StartDateTime** на значение StartDateTime, которое вы указали для Set-AzureRmDataFactoryPipelineActivePeriod.
3. Теперь запустите командлет **Get-AzureRmDataFactoryRun** , чтобы получить подробные сведения о выполненном для среза действии.
   
        Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] 
        <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
   
    Например:
   
        Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
   
    В качестве значения StartDateTime укажите время начала для проблемного (содержащего ошибку) среза, которое вы отметили на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
4. Вы должны увидеть выходные данные с подробными сведениями об ошибке (аналогично следующему):
   
        Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
        ResourceGroupName       : ADF
        DataFactoryName         : LogProcessingFactory3
        DatasetName               : EnrichedGameEventsTable
        ProcessingStartTime     : 10/10/2014 3:04:52 AM
        ProcessingEndTime       : 10/10/2014 3:06:49 AM
        PercentComplete         : 0
        DataSliceStart          : 5/5/2014 12:00:00 AM
        DataSliceEnd            : 5/6/2014 12:00:00 AM
        Status                  : FailedExecution
        Timestamp               : 10/10/2014 3:04:52 AM
        RetryAttempt            : 0
        Properties              : {}
        ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                        Jobs/841b77c9-d56c-48d1-99a3-
                    8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                    more details.
        ActivityName            : PigEnrichLogs
        PipelineName            : EnrichGameLogsPipeline
        Type                    :
5. Вы можете выполнить командлет **Save-AzureRmDataFactoryLog** с идентификатором, который отображается в результате команд, и скачать файлы журналов, используя параметр **-DownloadLogsoption** командлета.
   
        Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"

## <a name="rerun-failures-in-a-pipeline"></a>Повторная обработка проблемных срезов в конвейере
### <a name="using-azure-portal"></a>Использование портала Azure
После устранения неполадок и отладки ошибок в конвейере можно повторно обработать срез с ошибками. Для этого выберите срез и на панели команд нажмите кнопку **Запустить**.

![Повторная обработка среза](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Если срез не прошел проверку из-за ошибки политики (например, данные недоступны), вы можете исправить ошибку и повторно выполнить проверку, нажав на панели команд кнопку **Проверить** .
![Исправление ошибок и проверка](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="using-azure-powershell"></a>Использование Azure PowerShell
Вы можете повторно обработать проблемные срезы, используя командлет Set-AzureRmDataFactorySliceStatus. Синтаксис и другие сведения об этом командлете см. в соответствующем разделе статьи [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx). 

**Пример.** В следующем примере состояние всех срезов в таблице DAWikiAggregatedData в фабрике данных WikiADF меняется на Waiting.

Для UpdateType задано значение UpstreamInPipeline. Это означает, что состояние каждого среза в таблице и всех зависимых (восходящих) таблиц переходит в состояние Waiting. Этот параметр может иметь еще одно значение — Individual.

    Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## <a name="create-alerts"></a>Создание оповещений
Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. Вы можете создавать оповещения об этих событиях. Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Мы советуем использовать события для наблюдения в режиме реального времени, а метрики — для статистических целей. 

### <a name="alerts-on-events"></a>Оповещения о событиях
События Azure позволяют получить представление о том, что происходит в ресурсах Azure. Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. При использовании фабрики данных Azure события создаются, когда:

* создается, обновляется или удаляется фабрика данных Azure;
* запускается или завершается обработка данных (которая называется циклом выполнения);
* создается и удаляется кластер HDInsight по требованию.

Вы можете создавать оповещения в этих пользовательских событиях и настраивать их для отправки уведомлений по электронной почте администраторам и соадминистраторам подписки. Кроме того, вы можете указать дополнительные адреса электронной почты пользователей, которые должны получать уведомления по электронной почте при выполнении определенных условий. Эта функция удобна, когда нужно получать уведомления о сбоях, но не требуется постоянно отслеживать состояние фабрики данных.

> [!NOTE]
> В настоящее время портал не отображает оповещения о событиях. Используйте [приложение по мониторингу и управлению](data-factory-monitor-manage-app.md) для просмотра всех оповещений.
> 
> 

#### <a name="specifying-an-alert-definition"></a>Настройка определения оповещения
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

В определении JSON **subStatus** можно удалить, если вы не хотите получать оповещение при конкретном сбое.

Этот пример устанавливает оповещение для всех фабрик данных в вашей подписке. Если вам необходимо установить оповещение для определенной фабрики данных, можно указать ее значение **resourceUri** в **dataSource**:

    "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

Список возможных операций и состояний (а также дополнительных состояний) см. в следующей таблице.

| Имя операции | Состояние | Дополнительное состояние |
| --- | --- | --- |
| RunStarted |Started |Starting |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |Started | |
| OnDemandClusterCreateSuccessful |Успешно | |
| OnDemandClusterDeleted |Успешно | |

Дополнительные сведения об элементах JSON, используемых в примере, см. в статье о том, как [создать правило сигнала тревоги](https://msdn.microsoft.com/library/azure/dn510366.aspx). 

#### <a name="deploying-the-alert"></a>Развертывание оповещения
Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureRmResourceGroupDeployment**, как показано в следующем примере.

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

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

> [!NOTE]
> Можно использовать REST API [Создать правило оповещения](https://msdn.microsoft.com/library/azure/dn510366.aspx) для создания правила оповещения. Полезные данные JSON аналогичны JSON в примере.  
> 
> 

#### <a name="retrieving-the-list-of-azure-resource-group-deployments"></a>Получение списка развертываний групп ресурсов Azure
Чтобы получить список развертываний групп ресурсов Azure, используйте командлет **Get-AzureRmResourceGroupDeployment**, как показано в следующем примере.

    Get-AzureRmResourceGroupDeployment -ResourceGroupName adf

    DeploymentName    : ADFAlertFailedSlice
    ResourceGroupName : adf
    ProvisioningState : Succeeded
    Timestamp         : 10/11/2014 2:01:00 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
    Outputs           :


#### <a name="troubleshooting-user-events"></a>Устранение неполадок пользовательских событий
1. Вы можете просмотреть все созданные события, щелкнув **Метрики и операции**.
   
    ![Элемент "Метрики и операции"](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Щелкните **События** для просмотра событий. 
   
    ![Плитки "События"](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. В колонке **События** можно просмотреть подробные сведения о событиях, фильтровать их и т. д. 
   
    ![Колонка "События"](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. В списке операций выберите **операцию**, которая вызывает ошибку.
   
    ![Выберите операцию](./media/data-factory-monitor-manage-pipelines/select-operation.png) 
5. Щелкните событие **ошибки**, чтобы просмотреть сведения об ошибке.
   
    ![Ошибка события](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Сведения о командлетах PowerShell, которые можно использовать для добавления, получения и удаления предупреждений, см. в статье [Azure Insight Cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) (Командлеты Azure Insight). Далее приведено несколько примеров использования командлета **Get-AlertRule**: 

    PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det

            Properties :
            Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
            Condition   :
            DataSource :
            EventName             :
            Category              :
            Level                 :
            OperationName         : RunFinished
            ResourceGroupName     :
            ResourceProviderName  :
            ResourceId            :
            Status                : Failed
            SubStatus             : FailedExecution
            Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
            Condition      :
            Description : One or more of the data slices for the Azure Data Factory has failed processing.
            Status      : Enabled
            Name:       : ADFAlertsSlice
            Tags       :
            $type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
            Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
            Location   : West US
            Name       : ADFAlertsSlice

    PS C:\> Get-AlertRule -res $resourceGroup

            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0

            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
            Location   : West US
            Name       : FailedExecutionRunsWest3

    PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0

            Properties : Microsoft.Azure.Management.Insights.Models.Rule
            Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
            Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
            Location   : West US
            Name       : FailedExecutionRunsWest0

Выполните следующие команды get-help, чтобы просмотреть сведения и примеры для командлета Get-AlertRule. 

    get-help Get-AlertRule -detailed 
    get-help Get-AlertRule -examples


* Если события-триггеры отображаются в колонке на портале, но вы не получаете электронных уведомлений, убедитесь, что ваш адрес электронной почты может получать сообщения от внешних отправителей. Получение оповещений может быть заблокировано в параметрах электронной почты.

### <a name="alerts-on-metrics"></a>Оповещения по метрикам
Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Вы можете отслеживать и создавать оповещения по следующим метрикам срезов в фабрике данных:

* циклы выполнения со сбоем;
* успешные циклы выполнения.

Эти метрики позволяют получать общее представление обо всех успешных и проблемных циклах в фабрике данных. Метрики создаются при каждом цикле выполнения действия со срезом. Все созданные за час метрики собираются и передаются в вашу учетную запись хранения. Поэтому, чтобы включить метрики, необходимо настроить учетную запись хранения.

#### <a name="enabling-metrics"></a>Включение метрик
Чтобы включить метрики, в колонке "Фабрика данных" выберите следующее:

**Мониторинг** -> **Метрика** -> **Параметры диагностики** -> **Диагностика**

![Ссылка диагностики](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

В колонке **Диагностика** щелкните **On** (Вкл.), выберите учетную запись хранения и сохраните ее.

![Выноска "Диагностика"](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Так как метрики агрегируются ежечасно, между сохранением и отображением метрик в колонке мониторинга может пройти час.

### <a name="setting-up-alert-on-metrics"></a>Настройка оповещений по метрикам
Щелкните колонку **Метрики фабрики данных**: 

![Элемент "Метрики фабрики данных"](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

В колонке **Метрика** щелкните **+ Add alert** (+ Добавить оповещение) на панели инструментов. 
![Колонка "Метрики фабрики данных", добавление оповещения](./media/data-factory-monitor-manage-pipelines/add-alert.png)

На странице **Добавление правила оповещения** сделайте следующее и нажмите кнопку **ОК**.

* Введите имя для оповещения (например, оповещение о сбое).
* Введите описание для оповещения (например, отправка сообщения при сбое).
* Выберите метрику (неудачные и успешные выполнения).
* Укажите условие и пороговое значение.   
* Укажите период. 
* Укажите, кому следует отправлять сообщение (владельцам, участникам или читателям).
* другое. 

![Колонка "Метрики фабрики данных", добавление оповещения](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

После успешного добавления правила оповещения колонка закрывается и на странице **Метрика** появляется новое оповещение. 

![Колонка "Метрики фабрики данных", добавление оповещения](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

В элементе **Оповещения** также появится количество оповещений. Щелкните элемент **Оповещения**.

![Колонка "Метрики фабрики данных", правила оповещения](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

В колонке **Оповещения** можно увидеть все имеющиеся оповещения. Чтобы добавить оповещение, нажмите кнопку **Добавить оповещение** на панели инструментов.

![Колонка "Правила оповещения"](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Уведомления об оповещениях
При возникновении условия, по которому срабатывает правило оповещения, вы будете получать соответствующее электронное уведомление о том, что оповещение активировано. После устранения условия (т. е. оно больше не соответствует правилу оповещения) вы получите электронное уведомление о том, что причина оповещения устранена.

Такое поведение отличается от ситуации, когда уведомление отправляется по каждому сбою, соответствующему правилу оповещения.

### <a name="deploying-alerts-using-powershell"></a>Развертывание оповещений с помощью PowerShell
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

Замените subscriptionId, resourceGroupName и dataFactoryName в примере на соответствующие значения.

*metricName* на данный момент поддерживает два значения:

* FailedRuns и
* SuccessfulRuns.

**Развертывание оповещения**

Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureRmResourceGroupDeployment**, как показано в следующем примере.

    New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

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


Вы также можете использовать командлет **Add-AlertRule**, чтобы развернуть правило оповещения. Дополнительные сведения и примеры см. в документации [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).  

## <a name="move-data-factory-to-a-different-resource-group-or-subscription"></a>Перемещение фабрики данных в другую группу ресурсов или подписку
Фабрику данных можно переместить в другую группу ресурсов или подписку, воспользовавшись кнопкой **Переместить** в командной строке на домашней странице фабрики данных. 

![Перемещение фабрики данных](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Вместе с фабрикой данных можно переместить все связанные ресурсы (например, связанные с ней оповещения).

![Диалоговое окно "Перемещение ресурсов"](./media/data-factory-monitor-manage-pipelines/MoveResources.png)



<!--HONumber=Nov16_HO3-->


