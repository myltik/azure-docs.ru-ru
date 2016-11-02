<properties
    pageTitle="Пересылка состояния задания и потоков заданий из службы автоматизации в Log Analytics (OMS) | Microsoft Azure"
    description="В этой статье показано, как отправлять состояние задания Runbook и потоки заданий в Log Analytics (Microsoft Operations Management Suite) для получения дополнительных сведений и возможностей управления."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="magoedte" />


# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-(oms)"></a>Пересылка состояния задания и потоков заданий из службы автоматизации в Log Analytics (OMS)

Служба автоматизации может отправлять состояние задания Runbook и потоки заданий в рабочую область Log Analytics в Microsoft Operations Management Suite (OMS).  Хотя с помощью портала Azure или PowerShell можно просмотреть состояние отдельных заданий или всех заданий для определенной учетной записи службы автоматизации, однако для любых дополнительных функций, необходимых в работе, требуется создавать настраиваемые сценарии PowerShell.  Теперь с помощью Log Analytics можно:

- получить информацию о заданиях службы автоматизации; 
- активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания Runbook (например, сбой или приостановка); 
- создавать сложные запросы для потоков заданий; 
- коррелировать задания и учетные записи службы автоматизации; 
- визуализировать журнал задания по прошествии времени.     

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию

Чтобы начать отправку журналов службы автоматизации в Log Analytics, необходимо следующее.

1. Наличие подписки OMS. Дополнительные сведения см. в статье [Начало работы с Log Analytics](../log-analytics/log-analytics-get-started.md).  

    >[AZURE.NOTE]Для правильной работы этой конфигурации рабочая область OMS и учетная запись службы автоматизации должны находиться в одной подписке Azure. 
  
2. [Учетная запись хранения Azure](../storage/storage-create-storage-account.md).  
   
    >[AZURE.NOTE]Эта учетная запись хранения *должна* находиться в том же регионе, что и учетная запись хранения Azure. 
 
3. Azure PowerShell с командлетами оперативной аналитики версии 1.0.8 или более поздней. Сведения об этом выпуске и его установке см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
4. Система диагностики Azure и PowerShell для Log Analytics.  Дополнительные сведения об этом выпуске и его установке см. в разделе [Azure Diagnostic and Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1) (Система диагностики Azure и Log Analytics).  
5. Скачайте сценарий PowerShell **Enable-AzureDiagnostics.ps1** из [коллекции PowerShell](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript). Он настроит:
 - учетную запись хранения для хранения состояний и данных потока задания Runbook для указанной учетной записи службы автоматизации;
 - включение сбора этих данных из вашей учетной записи службы автоматизации для сохранения в учетной записи хранилища BLOB-объектов Azure в формате JSON;
 - сбор данных из вашей учетной записи хранилища BLOB-объектов в Log Analytics (OMS);
 - использование рабочей области OMS для решения Log Analytics и службы автоматизации.   

Во время выполнения для сценария **Enable-AzureDiagnostics.ps1** необходимо задать следующие параметры:

- *AutomationAccountName* — имя учетной записи службы автоматизации;
- *LogAnalyticsWorkspaceName* — имя рабочей области OMS.

Чтобы найти значения *AutomationAccountName*, на портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**.  В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br> ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).


## <a name="setup-integration-with-log-analytics"></a>Настройка интеграции с Log Analytics

1. На своем компьютере запустите **Windows PowerShell** на **начальном** экране.  
2. В оболочке командной строки PowerShell перейдите к папке, которая содержит скачанный сценарий, и выполните его, изменив значения параметров *-AutomationAccountName* и *-LogAnalyticsWorkspaceName*.

    >[AZURE.NOTE] После выполнения сценария появится запрос на проверку подлинности в Azure.  Для входа **необходимо** использовать учетную запись, которая является участником роли администраторов подписки и соадминистратором подписки.   
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. После выполнения этого сценария в службе Log Analytics должны отобразиться записи, примерно через 30 минут после записи новых диагностических данных в хранилище.  Если по истечении этого времени записи не отобразились, обратитесь к разделу по устранению неполадок в статье [JSON files in blob storage](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json)(Использование JSON-файлов в хранилище BLOB-объектов).

### <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что сценарий успешно настроил вашу учетную запись службы автоматизации и рабочую область OMS, можно выполнить следующие действия в PowerShell.  Перед этим найдите имя рабочей области OMS и имя группы ресурсов. На портале Azure перейдите к колонке "Log Analytics" (OMS) и запишите отображаемые в ней значения **Имя** и **Группа ресурсов**.<br> ![Список рабочих областей Log Analytics (OMS)](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png). Мы будем использовать эти два значения при проверке конфигурации в рабочей области OMS с помощью командлета PowerShell [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx).

1.  На портале Azure перейдите к учетным записям хранения и найдите следующую учетную запись, которая использует соглашение об именовании: *AutomationAccountNameomsstorage*.  Вскоре после завершения задания Runbook вы должны увидеть два созданных контейнера больших двоичных объектов, **insights-logs-joblogs** и **insights-logs-jobstreams**.  

2.  В PowerShell выполните следующий код, изменив значения параметров **ResourceGroupName** и **WorkspaceName** данными, скопированными или записанными.  

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName" 

    Этот код возвращает подробные данные хранилища для заданной рабочей области OMS.  Нам нужно подтвердить подробные данные хранилища и убедиться, что указанная ранее учетная запись службы автоматизации существует и **состояние** объекта имеет значение **ОК**.<br> ![Результаты выполнения командлета Get-AzureRmOperationalInsightsStorageInsights](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png).


## <a name="log-analytics-records"></a>Записи Log Analytics

Служба автоматизации создает в репозитории OMS записи двух типов.

### <a name="job-logs"></a>Журналы заданий

Свойство | Описание|
----------|----------|
Время | Дата и время выполнения задания Runbook.|
resourceId | Указывает тип ресурса в Azure.  Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
operationName | Указывает тип операции, выполняемой в Azure.  Для службы автоматизации значением будет Job.|
resultType | Состояние задания Runbook.  Возможные значения:<br>- Started<br>- Остановлена<br>- Suspended<br>- Failed<br>- Succeeded.|
resultDescription | Описывает состояние результата задания Runbook.  Возможные значения:<br>- Job is started<br>- Job Failed<br>- Job Completed.|
CorrelationId | GUID, представляющий собой идентификатор корреляции задания Runbook.|
Категория | Классификация типа данных.  Для службы автоматизации значением является JobLogs.|
RunbookName | Имя Runbook.|
JobId | GUID, представляющий собой идентификатор задания Runbook.|
Caller |  Сторона, инициировавшая операцию.  Допустимые значения: электронный адрес или system для запланированных заданий.|

### <a name="job-streams"></a>Потоки заданий
Свойство | Описание|
----------|----------|
Время | Дата и время выполнения задания Runbook.|
resourceId | Указывает тип ресурса в Azure.  Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
operationName | Указывает тип операции, выполняемой в Azure.  Для службы автоматизации значением будет Job.|
resultType | Состояние задания Runbook.  Возможные значения:<br>- InProgress.|
resultDescription | Включает в себя выходной поток из Runbook.|
CorrelationId | GUID, представляющий собой идентификатор корреляции задания Runbook.|
Категория | Классификация типа данных.  Для службы автоматизации значением является JobStreams.|
RunbookName | Имя Runbook.|
JobId | GUID, представляющий собой идентификатор задания Runbook.|
Caller | Сторона, инициировавшая операцию.  Допустимые значения: электронный адрес или system для запланированных заданий.| 
StreamType | Тип потока задания. Возможные значения:<br>-Progress<br>- Output<br>- Warning<br>- Error<br>- Debug<br>- Verbose.|

## <a name="viewing-automation-logs-in-log-analytics"></a>Просмотр журналов службы автоматизации в Log Analytics 

Теперь, когда вы начали отправку журналов заданий службы автоматизации в Log Analytics, давайте узнаем, что с ними можно сделать в OMS.   

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook 

Один из наших основных клиентов запрашивает возможность отправлять электронное сообщение или текст при возникновении ошибки в работе задания Runbook.   

Чтобы создать правило генерации оповещений, начните с создания поиска в журнале записей заданий Runbook, которые должны вызывать оповещение.  После этого станет доступна кнопка **Оповещение** , позволяющая создать и настроить правило генерации оповещений.

1.  На странице обзора OMS щелкните **Log Search**(Поиск журналов).
2.  Создайте запрос поиска по журналу для оповещения, введя в поле запроса: `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`.  Можно также использовать группирование по RunbookName с помощью `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.   
  
    Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, возможно, понадобится также группировать оповещения по подписке или учетной записи службы автоматизации.  Имя учетной записи службы автоматизации можно получить из поля "Ресурс" для поиска JobLogs.  

3.  Щелкните **Оповещение** в верхней части страницы, чтобы открыть экран **Добавить правило оповещения**.  Дополнительные сведения о параметрах для настройки оповещения см. в разделе [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками 

Помимо оповещений об ошибках, возможно, вам хотелось бы знать, когда при выполнении задания Runbook возникает устранимая ошибка (PowerShell выводит поток сообщений об ошибках, но устранимые ошибки не приводят приостановке или сбою задания).    

1. На портале OMS щелкните **Поиск по журналам**.
2. В поле запроса введите `Category=JobStreams StreamType_s=Error | measure count() by JobId_g` и нажмите кнопку **Поиск**.


### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания  

При отладке задания можно также просмотреть потоки заданий.  Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.   

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription` 

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания 

Наконец, вам может понадобиться визуализировать журнал задания по прошествии времени.  Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос. 

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day`  
<br> ![Диаграмма хронологии состояния задания в OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>


## <a name="summary"></a>Сводка

Отправляя состояние и данные потока задания службы автоматизации в Log Analytics, можно получить более подробные сведения о состоянии заданий службы автоматизации. Для этого можно настроить оповещения, чтобы сразу узнавать о возникшей проблеме, и сформировать настраиваемые панели мониторинга с помощью сложных запросов, на которых можно визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  Это обеспечит повышенную оперативную видимость и поможет быстрее реагировать на инциденты.  


## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о том, как создавать различные поисковые запросы и просматривать журналы заданий службы автоматизации с помощью Log Analytics, ознакомьтесь с разделом [Поиск по журналам в Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Чтобы понять, как создавать и извлекать выходные данные и сообщения об ошибках из модулей Runbook, ознакомьтесь с разделом [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md) 
- Чтобы узнать больше о выполнении модулей Runbook, отслеживании заданий Runbook и других технических деталях, ознакомьтесь с [отслеживанием задания Runbook](automation-runbook-execution.md)
- Чтобы узнать больше о Log Analytics (OMS) и источниках сбора данных, ознакомьтесь с разделом [Подключение службы Azure к Log Analytics](../log-analytics/log-analytics-azure-storage.md)


<!--HONumber=Oct16_HO2-->


