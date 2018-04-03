---
title: Мониторинг конвейеров и управление ими с помощью портала Azure и PowerShell | Документация Майкрософт
description: Сведения о том, как с помощью портала Azure и Azure PowerShell отслеживать состояние созданных конвейеров и фабрик данных Azure и управлять ими.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f3fb7c0be6f69f15b5b761f0c36d983f008282e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Мониторинг конвейеров фабрики данных Azure и управление ими с помощью портала Azure и PowerShell
> [!div class="op_single_selector"]
> * [Использование портала Azure или Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Использование приложения для мониторинга и управления](data-factory-monitor-manage-app.md)

> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, ознакомьтесь с [отслеживанием конвейеров фабрики данных и управлением ими в версии 2](../monitor-visually.md).

> [!IMPORTANT]
> Приложение для мониторинга и управления предоставляет улучшенную поддержку мониторинга и управления конвейерами данных и усовершенствованное устранение проблем. Чтобы узнать больше об этом приложении, ознакомьтесь со статьей [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](data-factory-monitor-manage-app.md). 


В этой статье описываются мониторинг и отладка конвейеров, а также управление ими с помощью портала Azure и PowerShell. В ней также приводятся инструкции по настройке оповещений в случае сбоев.

## <a name="understand-pipelines-and-activity-states"></a>Состояния конвейеров и действий
Используя портал Azure, можно делать следующее:

* представлять фабрику данных в виде схемы;
* просматривать действия в конвейере;
* просматривать входные и выходные наборы данных.

В этом разделе также содержатся сведения о переходе среза набора данных из одного состояния в другое.   

### <a name="navigate-to-your-data-factory"></a>Переход к фабрике данных
1. Войдите на [портале Azure](https://portal.azure.com).
2. Щелкните колонку **Фабрики данных** в меню слева. Если вы ее не видите, выберите **Больше служб** и щелкните **Фабрики данных** в категории **Аналитика**.

   !["Просмотреть все" -> "Фабрики данных"](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. В колонке **Фабрики данных** выберите фабрику данных, которая вас интересует.

    ![Выбор фабрики данных](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   После этого откроется домашняя страница фабрики данных.

   ![Колонка "Фабрика данных"](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Представление фабрики данных в виде схемы
Представление **схемы** позволяет отслеживать состояние фабрики данных и всех ее ресурсов, а также управлять ими. Щелкните **Схема** на домашней странице фабрики данных, чтобы представить ее в виде **схемы**.

![Представление схемы](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Вы можете увеличивать и уменьшать масштаб, выбирать 100%-ный масштаб или масштаб по размеру, блокировать структуру схемы и автоматически размещать конвейеры и наборы данных. Кроме того, можно отображать сведения из журнала преобразований данных (выделение восходящих и нисходящих элементов).

### <a name="activities-inside-a-pipeline"></a>Действия в конвейере
1. Щелкните конвейер правой кнопкой мыши и выберите **Открыть конвейер**. Отобразятся все действия, а также входные и выходные наборы данных. Эта функция удобна, когда конвейер состоит из нескольких действий, а вы хотите понять структуру взаимосвязей во всем конвейере.

    ![Откройте меню конвейера](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. В приведенном ниже примере используется действие копирования в конвейере с входными и выходными данными. 

    ![Действия в конвейере](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Чтобы вернуться на домашнюю страницу фабрики данных, щелкните ссылку **Фабрика данных** в строке навигации в левом верхнем углу.

    ![Возврат к фабрике данных](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Просмотр состояния действий в конвейере
Вы можете просмотреть текущее состояние отдельного действия, отобразив состояние любого созданного им набора данных.

Чтобы отобразить все срезы, созданные в рамках отдельных циклов выполнения действия в конвейере, дважды щелкните **OutputBlobTable** в представлении **Схема**. Как видите, действие копирования успешно выполнялось последние восемь часов и создало срезы в состоянии **Готово**.  

![Состояние конвейера](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Срезы наборов данных в фабрике данных могут находиться в одном из следующих состояний:

<table>
<tr>
    <th align="left">Состояние</th><th align="left">Подсостояние</th><th align="left">ОПИСАНИЕ</th>
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
<td>Retry</td><td>Действие выполняется повторно.</td>
</tr>
<tr>
<td>Проверка</td><td>Проверка еще не начата.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Ожидание повторения проверки.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validating</td><td>Проверка выполняется.</td>
</tr>
<td>-</td>
<td>Срез обрабатывается.</td>
</tr>
<tr>
<td rowspan="4">Сбой</td><td>TimedOut</td><td>Выполнение действия заняло больше времени, чем разрешено для данного действия.</td>
</tr>
<tr>
<td>Canceled</td><td>Срез был отменен пользователем.</td>
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
<td>Skipped</td><td>Нет</td><td>Срез не обрабатывается.</td>
</tr>
<tr>
<td>Нет</td><td>-</td><td>Срез, который ранее существовал с другим состоянием, но был сброшен.</td>
</tr>
</table>



Чтобы просмотреть сведения о срезе, щелкните запись среза в колонке **Последние обновленные срезы**.

![Сведения о срезе](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Если срез выполнялся несколько раз, вы увидите несколько строк в списке **Выполнения действий** . Чтобы просмотреть сведения о выполнении действия, щелкните запись цикла в списке **Циклы выполнения действия** . Отобразятся все файлы журналов и сообщения об ошибках, если таковые были. Эта функция удобна для просмотра и обработки файлов журналов непосредственно из фабрики данных.

![СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Если срез не находится в состоянии **Готово**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**. Эта функция удобна для просмотра восходящих зависимостей, если срез находится в состоянии **Ожидание**.

![Неготовые восходящие срезы](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Схема состояний наборов данных
После развертывания фабрики данных и определения периода активности конвейера срезы наборов данных переходят из одного состояния в другое. В настоящее время переходы между состояниями выполняются в соответствии со следующей схемой:

![Схема состояний](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Поток переходов между состояниями выглядит так: "Ожидание" -> "Выполняется" или "Выполняется (проверка)" -> "Готово" или "Сбой".

Изначально срезы находятся в состоянии **Ожидание**, ожидая, что предварительные условия будут соблюдены до выполнения действий. Затем начинается выполнение действия, и срез переходит в состояние **Выполняется**. Выполнение действия может завершиться успешно или с ошибкой. В зависимости от того, как завершится действие, срез перейдет в состояние **Готово** или **Сбой**.

Вы можете сбросить состояние среза **Готово** или **Сбой** обратно в состояние **Ожидание**. Вы также можете установить для среза флажок **Пропустить** — действие со срезом не выполняется, и срез не обрабатывается.

## <a name="pause-and-resume-pipelines"></a>Приостановка и возобновление работы конвейеров
Управлять конвейерами можно с помощью Azure Powershell. Например, вы можете приостановить и возобновить работу конвейеров, используя командлеты Azure PowerShell. 

> [!NOTE] 
> Представление схемы не поддерживает приостановку и возобновление конвейеров. Если вы хотите использовать пользовательский интерфейс, используйте приложение для мониторинга и управления. Чтобы узнать больше об этом приложении, ознакомьтесь со статьей [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](data-factory-monitor-manage-app.md). 

Вы можете приостановить работу конвейеров с помощью командлета PowerShell **Suspend-AzureRmDataFactoryPipeline**. Этот командлет полезен, если не требуется запускать конвейеры до устранения проблемы. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Например: 

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Устранив проблему в конвейере, возобновите его работу. Для этого выполните следующую команду PowerShell:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Например: 

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Отладка конвейеров
Фабрика данных Azure предоставляет широкие возможности отладки и устранения неполадок с конвейерами с помощью портала Azure и Azure PowerShell.

> Примечание. Гораздо проще устранять ошибки и проблемы с помощью приложения для мониторинга и управления. Чтобы узнать больше об этом приложении, ознакомьтесь со статьей [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](data-factory-monitor-manage-app.md). 

### <a name="find-errors-in-a-pipeline"></a>Поиск ошибок в конвейере
Если при выполнении действия в конвейере происходит сбой, созданный конвейером набор данных будет находиться в состоянии "Ошибка". Вы можете устранить неполадки или выполнить отладку в фабрике данных Azure, используя следующие методы.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Отладка ошибок с помощью портала Azure
1. В колонке **Таблица** щелкните проблемный срез с **состоянием** **Сбой**.

   ![Колонка "Таблица" с проблемным срезом](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. В колонке **Срез данных** щелкните цикл выполнения действия, который завершился ошибкой.

   ![Срез данных с ошибкой](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. В колонке **Подробности о выполнении операции** вы можете скачать файлы, связанные с обработкой HDInsight. Чтобы скачать файл журнала ошибок c подробными сведениями об ошибке, щелкните **Скачать** в строке журнала Status/stderr.

   ![Колонка "Сведения о циклах выполнения действия" с ошибкой](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Отладка ошибок с помощью PowerShell
1. Запустите **PowerShell**.
2. Выполните команду **Get-AzureRmDataFactorySlice**, чтобы просмотреть срезы и их состояние. Вы должны увидеть срез с состоянием **Сбой**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Например: 

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Замените **StartDateTime** на время запуска конвейера. 
3. Теперь запустите командлет **Get-AzureRmDataFactoryRun**, чтобы получить подробные сведения о выполненном для среза действии.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Например: 

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    В качестве значения StartDateTime укажите время начала для проблемного (содержащего ошибку) среза, которое вы отметили на предыдущем шаге. Значение даты-времени необходимо заключить в двойные кавычки.
4. Вы должны увидеть выходные данные с подробными сведениями об ошибке, аналогичные следующим:

    ```   
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
    ```
5. Вы можете выполнить командлет **Save-AzureRmDataFactoryLog** с идентификатором, который отображается в результате команд, и скачать файлы журналов, используя параметр **-DownloadLogsoption** командлета.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Повторная обработка проблемных срезов в конвейере

> [!IMPORTANT]
> Использование приложения для мониторинга и управления облегчает устранение ошибок и перезапуск неудачных срезов. Чтобы узнать больше об этом приложении, ознакомьтесь со статьей [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью приложения для мониторинга и управления](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Использование портала Azure
После устранения неполадок и отладки ошибок в конвейере можно повторно обработать срез с ошибками. Для этого выберите срез и на панели команд нажмите кнопку **Запустить**.

![Повторная обработка среза](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Если срез не прошел проверку из-за ошибки политики (например, данные недоступны), вы можете исправить ошибку и повторно выполнить проверку, нажав на панели команд кнопку **Проверить**.

![Исправление ошибок и проверка](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Использование Azure PowerShell
Вы можете повторно обработать проблемные срезы, используя командлет **Set-AzureRmDataFactorySliceStatus**. Синтаксис и другие сведения об этом командлете см. в соответствующем разделе статьи [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx).

**Пример.**

В следующем примере состояние всех срезов в таблице DAWikiAggregatedData в фабрике данных WikiADF меняется на Waiting.

Для UpdateType задано значение UpstreamInPipeline. Это означает, что состояние каждого среза в таблице и всех зависимых (восходящих) таблиц переходит в состояние Waiting. Этот параметр может иметь еще одно значение — Individual.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Создание оповещений
Azure регистрирует пользовательские события, когда ресурс Azure (например, фабрика данных) создается, обновляется или удаляется. Вы можете создавать оповещения об этих событиях. Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Мы советуем использовать события для наблюдения в режиме реального времени, а метрики — для статистических целей.

### <a name="alerts-on-events"></a>Оповещения о событиях
События Azure позволяют получить представление о том, что происходит в ресурсах Azure. При использовании фабрики данных Azure события создаются, когда:

* фабрика данных создается, обновляется или удаляется;
* запускается или завершается обработка данных (цикл выполнения);
* создается и удаляется кластер HDInsight по требованию.

Вы можете создавать оповещения в этих пользовательских событиях и настраивать их для отправки уведомлений по электронной почте администраторам и соадминистраторам подписки. Кроме того, вы можете указать дополнительные адреса электронной почты пользователей, которые должны получать уведомления по электронной почте при выполнении определенных условий. Эта функция удобна, когда нужно получать уведомления о сбоях, но не требуется постоянно отслеживать состояние фабрики данных.

> [!NOTE]
> В настоящее время портал не отображает оповещения о событиях. Используйте [приложение по мониторингу и управлению](data-factory-monitor-manage-app.md) для просмотра всех оповещений.


#### <a name="specify-an-alert-definition"></a>Задание определения оповещения
Чтобы задать определение оповещения, создайте файл JSON, описывающий операции, о которых вы хотите получать оповещения. В следующем примере отправляется электронное уведомление об операции RunFinished. Другими словами, оно отправляется в случае завершения цикла выполнения действия в фабрике данных со сбоем (состояние = FailedExecution).

```JSON
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
```

Вы можете удалить **subStatus** в определении JSON, если не хотите получать оповещение при конкретном сбое.

Этот пример устанавливает оповещение для всех фабрик данных в вашей подписке. Если вам необходимо установить оповещение для определенной фабрики данных, можно указать ее значение **resourceUri** в **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

Список возможных операций и состояний (а также дополнительных состояний) см. в следующей таблице.

| Имя операции | Status | Подсостояние |
| --- | --- | --- |
| RunStarted |Started |Запуск |
| RunFinished |Failed / Succeeded |FailedResourceAllocation<br/><br/>Succeeded<br/><br/>FailedExecution<br/><br/>TimedOut<br/><br/><Canceled<br/><br/>FailedValidation<br/><br/>Abandoned |
| OnDemandClusterCreateStarted |Started | |
| OnDemandClusterCreateSuccessful |Succeeded | |
| OnDemandClusterDeleted |Succeeded | |

Дополнительные сведения об элементах JSON, используемых в примере, см. в статье о том, как [создать правило оповещения](https://msdn.microsoft.com/library/azure/dn510366.aspx).

#### <a name="deploy-the-alert"></a>Развертывание оповещения
Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureRmResourceGroupDeployment**, как показано в следующем примере.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

После успешного завершения развертывания группы ресурсов вы увидите следующие сообщения:

```
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
```

> [!NOTE]
> Можно использовать REST API [Создать правило оповещения](https://msdn.microsoft.com/library/azure/dn510366.aspx) для создания правила оповещения. Полезные данные JSON аналогичны JSON в примере.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Получение списка развертываний групп ресурсов Azure
Чтобы получить список развертываний групп ресурсов Azure, используйте командлет **Get-AzureRmResourceGroupDeployment**, как показано в следующем примере.

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Устранение неполадок пользовательских событий
1. Вы можете просмотреть все созданные события, щелкнув плитку **Метрики и операции**.

    ![Элемент "Метрики и операции"](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Щелкните плитку **События** для просмотра событий.

    ![Плитки "События"](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. В колонке **События** можно просмотреть подробные сведения о событиях, фильтровать их и т. д.

    ![Колонка "События"](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. В списке операций выберите **операцию**, которая вызывает ошибку.

    ![Выберите операцию](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Щелкните событие **ошибки**, чтобы просмотреть сведения об ошибке.

    ![Ошибка события](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Сведения о командлетах PowerShell, которые можно использовать для добавления, получения и удаления предупреждений, см. в статье о [командлетах Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx). Далее приведено несколько примеров использования командлета **Get-AlertRule**:

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
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
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
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
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Выполните следующие команды get-help, чтобы просмотреть сведения и примеры для командлета Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Если события-триггеры отображаются в колонке на портале, но вы не получаете электронных уведомлений, убедитесь, что ваш адрес электронной почты может получать сообщения от внешних отправителей. Получение оповещений может быть заблокировано в параметрах электронной почты.

### <a name="alerts-on-metrics"></a>Оповещения по метрикам
Фабрика данных позволяет собирать различные метрики и создавать по ним оповещения. Вы можете отслеживать и создавать оповещения по следующим метрикам срезов в фабрике данных:

* **циклы выполнения со сбоем**;
* **успешные циклы выполнения**.

Эти метрики позволяют получать общее представление обо всех успешных и проблемных циклах в фабрике данных. Метрики создаются при каждом цикле выполнения действия со срезом. Все созданные за час метрики собираются и передаются в вашу учетную запись хранения. Чтобы включить метрики, необходимо настроить учетную запись хранения.

#### <a name="enable-metrics"></a>Включить метрики
Чтобы включить метрики, в колонке **Фабрика данных** выберите следующее:

**Мониторинг** > **Метрика** > **Параметры диагностики** > **Диагностика**

![Ссылка диагностики](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

В колонке **Диагностика** щелкните **Вкл.**, выберите учетную запись хранения и щелкните **Сохранить**.

![Выноска "Диагностика"](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Так как метрики агрегируются ежечасно, между сохранением и отображением метрик в колонке **Мониторинг** может пройти час.

### <a name="set-up-an-alert-on-metrics"></a>Настройка оповещений по метрикам
Щелкните плитку **Метрики фабрики данных**.

![Элемент "Метрики фабрики данных"](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

В колонке **Метрика** щелкните **+ Add alert** (+ Добавить оповещение) на панели инструментов.
![Колонка "Метрики фабрики данных", добавление оповещения](./media/data-factory-monitor-manage-pipelines/add-alert.png)

На странице **Добавление правила оповещения** сделайте следующее и нажмите кнопку **ОК**.

* Введите имя для оповещения (например, оповещение о сбое).
* Введите описание для оповещения (например, отправка сообщения при сбое).
* Выберите метрику (неудачные и успешные выполнения).
* Укажите условие и пороговое значение.   
* Укажите период времени.
* Укажите, кому следует отправлять сообщение (владельцам, участникам или читателям).

![Колонка "Метрики фабрики данных", добавление правила оповещения](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

После успешного добавления правила оповещения колонка закрывается и в колонке **Метрика** появляется новое оповещение.

![Колонка "Метрики фабрики данных", добавление нового оповещения](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

В плитке **Правила оповещений** также появится количество оповещений. Щелкните плитку **Правила оповещений**.

![Колонка "Метрики фабрики данных", правила оповещения](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

В колонке **Правила оповещений** можно увидеть все имеющиеся оповещения. Чтобы добавить оповещение, нажмите кнопку **Добавить оповещение** на панели инструментов.

![Колонка "Правила оповещения"](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Оповещения
После возникновения условия, по которому срабатывает правило оповещения, вы будете получать соответствующее электронное уведомление о том, что оповещение активировано. После устранения условия (т. е. оно больше не соответствует правилу оповещения) вы получите электронное уведомление о том, что причина оповещения устранена.

Такое поведение отличается от ситуации, когда уведомление отправляется по каждому сбою, соответствующему правилу оповещения.

### <a name="deploy-alerts-by-using-powershell"></a>Развертывание оповещений с помощью PowerShell
Вы можете развернуть оповещения по метрикам так же, как и по событиям.

**Определение оповещения**

```JSON
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
```

Замените *subscriptionId*, *resourceGroupName* и *dataFactoryName* в примере на соответствующие значения.

*metricName* на данный момент поддерживает два значения:

* FailedRuns и
* SuccessfulRuns.

**Развертывание оповещения**

Для развертывания оповещения используйте командлет Azure PowerShell **New-AzureRmResourceGroupDeployment**, как показано в следующем примере.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

В случае успешного развертывания вы увидите следующее сообщение:

```
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
```

Вы также можете использовать командлет **Add-AlertRule**, чтобы развернуть правило оповещения. Дополнительные сведения и примеры см. в документации [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Перемещение фабрики данных в другую группу ресурсов или подписку
Фабрику данных можно переместить в другую группу ресурсов или подписку, воспользовавшись кнопкой **Переместить** в командной строке на домашней странице фабрики данных.

![Перемещение фабрики данных](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Вместе с фабрикой данных можно переместить все связанные ресурсы (например, связанные с ней оповещения).

![Диалоговое окно "Перемещение ресурсов"](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
