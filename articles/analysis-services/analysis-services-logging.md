---
title: "Diganostic ведения журнала для служб Azure Analysis Services | Документы Microsoft"
description: "Дополнительные сведения о настройке ведения журнала диагностики Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/29/2017
ms.author: owend
ms.openlocfilehash: 02c25de980b399812676285ad3f87f60af93265f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="setup-diagnostic-logging"></a>Настройка ведения журнала диагностики

Важной частью любого решения Analysis Services контролирует способ работы вашего сервера. С [журналы диагностики Azure ресурсов](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), можно отслеживать и отправлять журналы, чтобы [хранилища Azure](https://azure.microsoft.com/services/storage/), потоковую передачу их [концентраторов событий Azure](https://azure.microsoft.com/services/event-hubs/)и экспортировать их в [журнала Аналитика](https://azure.microsoft.com/services/log-analytics/), а [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Процесс ведения журнала диагностики в хранилище, концентраторе событий или Operations Management Suite через Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Регистрируемых?

Можно выбрать **ядра**, **службы**, и **метрики** категории.

### <a name="engine"></a>Двигатель

При выборе **ядра** регистрирует все [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Нельзя выбрать отдельные события. 

|Категории XEvent |Имя события  |
|---------|---------|
|Аудит безопасности    |   Аудит входа в систему      |
|Аудит безопасности    |   Аудит выхода из системы      |
|Аудит безопасности    |   Аудит запусков и остановок сервера      |
|Отчеты о состоянии     |   Начало отчета о состоянии      |
|Отчеты о состоянии     |   Окончание отчета о состоянии      |
|Отчеты о состоянии     |   Текущий отчет о состоянии      |
|Запросы     |  Начало запроса       |
|Запросы     |   Окончание запроса      |
|Команды     |  Начало команды       |
|Команды     |  Окончание команды       |
|Ошибки и предупреждения     |   Ошибка      |
|Поиск     |   Завершение распознавания      |
|Уведомление     |    Уведомление     |
|Сеанс     |  Инициализация сеанса       |
|Блокировки    |  взаимоблокировки       |
|Обработка запросов     |   Начало запроса VertiPaq SE      |
|Обработка запросов     |   Конец запроса VertiPaq SE      |
|Обработка запросов     |   Совпадение кэша запросов SE VertiPaq      |
|Обработка запросов     |   Начало Direct Query      |
|Обработка запросов     |  Окончание Direct Query       |

### <a name="service"></a>Service

|Имя операции  |Происходит, когда  |
|---------|---------|
|CreateGateway     |   Пользователь настраивает шлюза на сервере      |
|ResumeServer     |    Возобновить сервер     |
|SuspendServer    |   Приостановка сервера      |
|DeleteServer     |    Удаление сервера     |
|RestartServer    |     На сервер через SSMS или PowerShell был перезагружен пользователем    |
|GetServerLogFiles    |    Пользователь экспортирует журнал сервера с помощью PowerShell     |
|ExportModel     |   Пользователь экспортирует модель на портале с помощью функции открытия в Visual Studio     |

### <a name="all-metrics"></a>Все метрики

Категория метрики журналы же [метрики сервера](analysis-services-monitor.md#server-metrics) отображаемых метрик.

## <a name="setup-diagnostics-logging"></a>Настройка ведения журнала диагностики

### <a name="azure-portal"></a>Портал Azure

1. В [портал Azure](https://portal.azure.com) > сервер, нажмите кнопку **журналы диагностики** в левой области навигации, а затем выберите **включить диагностику**.

    ![Включение журнала ведения диагностики для Azure Cosmos DB на портале Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. В **параметров диагностики**, выполните следующие действия: 

    * **Имя**. Введите имя для создаваемых журналов.

    * **Archive to a storage account** (Архивировать в учетной записи хранения). Чтобы использовать этот параметр, необходима учетная запись хранения для подключения. Ознакомьтесь со статьей [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md). Следуйте инструкциям для создания диспетчера ресурсов, учетная запись общего назначения. Затем на портале вернитесь на эту страницу, чтобы выбрать учетную запись хранения. Возможно, потребуется подождать несколько минут, пока созданная учетная запись хранения отобразится в раскрывающемся меню.
    * **Stream to an event hub** (Потоковая передача в концентратор событий). Чтобы использовать этот параметр, вам понадобится пространство имен концентратора событий и концентратор событий для подключения. Чтобы создать пространство имен концентратора событий, ознакомьтесь со статьей [Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure](../event-hubs/event-hubs-create.md). Затем на портале вернитесь на эту страницу, чтобы выбрать пространство имен концентратора событий и имя политики.
    * **Send to Log Analytics** (Отправить в Log Analytics). Чтобы использовать этот параметр, воспользуйтесь одной из имеющихся рабочих областей или создайте рабочую область Log Analytics, следуя инструкциям по [созданию рабочей области](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) на портале. Дополнительные сведения о просмотре журналов в Log Analytics см. в статье [Журнал ведения диагностики Azure Cosmos DB](#view-in-loganalytics).

    * **Модуль**. Выберите этот параметр для входа xEvents. Если архивация в учетную запись хранения, можно выбрать срок хранения для журналов диагностических данных. По окончании периода хранения журналы удаляются автоматически.
    * **Служба**. Выберите этот параметр, в журнал событий на уровне службы. Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.
    * **Метрики**. Выберите этот параметр, чтобы сохранить подробные данные в [метрики](analysis-services-monitor.md#server-metrics). Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.

3. Выберите команду **Сохранить**.

    Если отобразится сообщение об ошибке Failed to update diagnostics for \<workspace name>. The subscription \<subscription id> is not registered to use Microsoft.Insights (Не удалось обновить данные диагностики для <имя_рабочей_области>. Подписку <идентификатор_подписки> не зарегистрировано для использования Microsoft.Insights), следуйте инструкциям статьи [Сбор журналов и метрик для служб Azure для использования в Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage), чтобы зарегистрировать учетную запись, а затем повторите процедуру.

    Если вы хотите изменить, каким образом вашим журналам диагностики сохраняются в любой момент в будущем, можно вернуться на эту страницу для изменения параметров.

### <a name="powershell"></a>PowerShell

Ниже приведены основные команды для ознакомления. Если требуется пошаговые указания по настройке ведения журнала в учетную запись хранения с помощью PowerShell, см. в руководстве далее в этой статье.

Чтобы включить метрики и журналы диагностики с помощью PowerShell, используйте следующие команды:

- Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   StorageAccountId — это идентификатор ресурса учетной записи хранения, в которую будут отправляться журналы.

- Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Идентификатор правила служебной шины Azure — это строка в формате:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Идентификатор ресурса рабочей области Log Analytics можно получить с помощью следующей команды:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="rest-api"></a>ИНТЕРФЕЙС REST API

Узнайте, как [изменить параметры диагностики с помощью API REST Azure монитор](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Шаблон Resource Manager

Узнайте, как [включить параметры диагностики при создании ресурса с помощью шаблона диспетчера ресурсов](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Управление журналами

Журналы обычно доступны в настройке ведения журнала на несколько часов. Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.
* Не забудьте установить срок хранения для, поэтому старые журналы будут удалены из вашей учетной записи хранилища.

## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Метрики и события сервера, объединяются с xEvents в службе анализа журналов для анализа side-by-side. Служба аналитики журналов можно настроить также получать события из других служб Azure, обеспечивая целостное представление о диагностических данных журналов по всем архитектуры.

Чтобы просмотреть диагностические данные в службе анализа журналов, откройте страницу поиска журналов из меню слева или в области управления, как показано ниже.

![Параметры поиска по журналам на портале Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Теперь, когда вы включили функцию сбора данных в **поиска журналов**, нажмите кнопку **все собранные данные**.

В **тип**, нажмите кнопку **AzureDiagnostics**, а затем нажмите кнопку **применить**. AzureDiagnostics включает в себя события Engine и службы. Обратите внимание, что служба аналитики журналов запрос создается на лету. Класс событий\_s поле содержит имена xEvent, которые могут вам знакомы, если вы использовали xEvents для локального входа.

Нажмите кнопку **EventClass\_s** или одно из имен событий и анализа журналов продолжает построении запроса. Не забудьте сохранить запросы для последующего использования.

Убедитесь, что для извлечения Operations Management Suite, который предоставляет веб-сайт с расширенный запрос, уведомления и предупреждений возможности анализа журналов данных.

### <a name="queries"></a>Запросы

Имеются сотни запросы, которые можно использовать. Ниже приведены несколько можно приступить к работе.
Дополнительные сведения об использовании новый язык запросов поиска журналов см. в разделе [основные сведения о журнале ищет в службе анализа журналов](../log-analytics/log-analytics-log-search-new.md). 

* Запрос возвращает запросов передан Azure Analysis Services, потребовавшееся для выполнения более пяти минут (300 000 миллисекунд).

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Определите реплик для горизонтального масштабирования.

    ```
    search * | summarize count() by ServerName_s
    ```
    При использовании горизонтального масштабирования, можно определять только для чтения, поскольку ServerName\_значения полей s имеет номер экземпляра реплики, добавляемое к имени. Поле содержит имя ресурсов Azure, которое соответствует имени сервера, которое будет отображаться для пользователей. Поле IsQueryScaleoutReadonlyInstance_s имеет значение true, если для реплики.



> [!TIP]
> Имеется большой запрос анализа журналов, вы хотите поделиться? При наличии учетной записи GitHub, его можно добавить в эту статью. Просто щелкните **изменить** в правой верхней части этой страницы.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Учебник - включить ведение журнала с помощью PowerShell
В этом учебнике быстрого создания учетной записи хранения в той же подписке и группе ресурсов как сервер служб Analysis Services. Затем используйте AzureRmDiagnosticSetting набор, чтобы включить диагностики ведения журнала, отправляя выходные данные для учетной записи хранения.

### <a name="prerequisites"></a>Технические условия
Для работы с этим руководством вам потребуются следующие ресурсы:

* Существующий сервер Azure Analysis Services. Инструкции по созданию ресурсов сервера см. в разделе [создание сервера на портале Azure](analysis-services-create-server.md), или [создать сервер служб Azure Analysis Services с помощью PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Подключиться к подпискам

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

```powershell
Login-AzureRmAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записью, и по умолчанию использует первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzureRmSubscription
```

Чтобы указать подписку, связанную с учетной записью Azure Analysis Services, запись, введите:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Если у вас несколько подписок, связанных с учетной записью, важно определить подписку.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Создание новой учетной записи хранения для журналов

Можно использовать существующую учетную запись хранения для журналов, предоставляются в той же подписке, что и сервер. В этом учебнике создается новой учетной записи хранения для журналов служб Analysis Services. Чтобы упростить процесс, чтобы сохранить подробные сведения об учетной записи хранилища в переменной с именем **sa**.

Вы также использовать ту же группу ресурсов, которая содержит сервер служб Analysis Services. Заменить значения для `awsales_resgroup`, `awsaleslogs`, и `West Central US` собственными значениями:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Определение учетной записи сервера для журналов

Задайте имя учетной записи с именем переменной **учетной записи**, где ResourceName — имя учетной записи.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Включение ведения журналов

Ведения журнала, используйте командлет Set-AzureRmDiagnosticSetting вместе с переменными для новой учетной записи хранения, учетная запись сервера и категории. Выполните следующую команду, задав для флага **-Enabled** значение **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Результат должен выглядеть следующим образом.

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Это позволит убедиться, что для сервера, сохранять сведения учетной записи хранения, теперь включено ведение журнала.

Также можно задать политику хранения для журналов, старые журналы будут автоматически удалены. Например, задать политику хранения с помощью **- RetentionEnabled** флаг **$true**и задайте **- RetentionInDays** параметр **90**. Журналы, которые старше 90 дней, автоматически удаляются.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [ведения журнала диагностики Azure ресурсов](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

В разделе [AzureRmDiagnosticSetting набор](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) справки PowerShell.