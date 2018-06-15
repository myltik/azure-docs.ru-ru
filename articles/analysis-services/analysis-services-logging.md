---
title: Ведение журнала диагностики для Azure Analysis Services | Документация Майкрософт
description: Узнайте том, как настроить журнал ведения диагностики для Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0baeca982cc19d327a58b7223555f2afcb4c35e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596725"
---
# <a name="setup-diagnostic-logging"></a>Настройка журнала ведения диагностики

Важной частью любого решения Analysis Services является мониторинг работы серверов. С помощью [журналов диагностики ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) вы можете отслеживать и отправлять журналы в [службу хранилища Azure](https://azure.microsoft.com/services/storage/), выполнять их потоковую передачу в [концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/) и экспортировать их в службу [Log Analytics](https://azure.microsoft.com/services/log-analytics/) как службу [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Процесс ведения журнала диагностики в хранилище, концентраторе событий и Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Какие данные регистрируются?

Можно выбрать категории **Подсистема**, **Служба** и **Метрики**.

### <a name="engine"></a>Двигатель

При выборе категории **Подсистема** в журнале регистрируется все события [xEvent](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Невозможно будет выбрать отдельные события. 

|Категории событий xEvent |Имя события  |
|---------|---------|
|Аудит безопасности    |   Audit Login      |
|Аудит безопасности    |   Audit Logout      |
|Аудит безопасности    |   Audit Server Starts And Stops      |
|Отчеты о ходе выполнения     |   Progress Report Begin      |
|Отчеты о ходе выполнения     |   Progress Report End      |
|Отчеты о ходе выполнения     |   Progress Report Current      |
|Запросы     |  Query Begin       |
|Запросы     |   Query End      |
|Команды     |  Command Begin       |
|Команды     |  Command End       |
|Ошибки и предупреждения     |   Ошибка      |
|Поиск     |   Discover End      |
|Уведомление     |    Уведомление     |
|Сеанс     |  Session Initialize       |
|Блокировки    |  Deadlock       |
|Обработка запросов     |   VertiPaq SE Query Begin      |
|Обработка запросов     |   VertiPaq SE Query End      |
|Обработка запросов     |   VertiPaq SE Query Cache Match      |
|Обработка запросов     |   Direct Query Begin      |
|Обработка запросов     |  Direct Query End       |

### <a name="service"></a>Service

|Имя операции  |Когда выполняется  |
|---------|---------|
|CreateGateway     |   Пользователь настраивает шлюза на сервере.      |
|ResumeServer     |    Возобновление работы сервера.     |
|SuspendServer    |   Приостановка работы сервера.      |
|DeleteServer     |    Удаление сервера     |
|RestartServer    |     Пользователь перезапускает сервер с помощью SSMS или PowerShell.    |
|GetServerLogFiles    |    Пользователь экспортирует журнал сервера с помощью PowerShell.     |
|ExportModel     |   Пользователь экспортирует модель на портал с помощью функции открытия в Visual Studio.     |

### <a name="all-metrics"></a>Все метрики

Категория "Метрики" позволяет регистрировать [метрики сервера](analysis-services-monitor.md#server-metrics), отображенные в списке "Метрики".

## <a name="setup-diagnostics-logging"></a>Настройка ведения журнала диагностики

### <a name="azure-portal"></a>Портал Azure

1. На [портале Azure](https://portal.azure.com) выберите сервер, в левой области навигации выберите **Журналы диагностики**, а затем — **Включить диагностику**.

    ![Включение журнала ведения диагностики для Azure Cosmos DB на портале Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. На странице **Параметры диагностики** настройте следующие параметры: 

    * **Имя**. Введите имя для создаваемых журналов.

    * **Archive to a storage account** (Архивировать в учетной записи хранения). Чтобы использовать этот параметр, необходима учетная запись хранения для подключения. Ознакомьтесь со статьей [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md). Следуйте указаниям для создания диспетчера ресурсов и учетной записи общего назначения, а затем выберите учетную запись хранения, вернувшись к этой странице портала. Возможно, потребуется подождать несколько минут, пока созданная учетная запись хранения отобразится в раскрывающемся меню.
    * **Stream to an event hub** (Потоковая передача в концентратор событий). Чтобы использовать этот параметр, вам понадобится пространство имен концентратора событий и концентратор событий для подключения. Дополнительные сведения см. в статье [Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure](../event-hubs/event-hubs-create.md). Затем на портале вернитесь на эту страницу, чтобы выбрать пространство имен концентратора событий и имя политики.
    * **Send to Log Analytics** (Отправить в Log Analytics). Чтобы использовать этот параметр, воспользуйтесь одной из имеющихся рабочих областей или создайте рабочую область Log Analytics, следуя инструкциям по [созданию рабочей области](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) на портале. Дополнительные сведения о просмотре журналов в Log Analytics см. в статье [Журнал ведения диагностики Azure Cosmos DB](#view-in-loganalytics).

    * **Подсистема**. Выберите этот параметр для ведения журнала xEvents. Если выполняется архивация в учетную запись хранения, можно выбрать период хранения журналов диагностики. По окончании периода хранения журналы удаляются автоматически.
    * **Служба**. Выберите этот параметр, чтоб вести журнал событий уровня службы. Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.
    * **Метрики**. Выберите этот параметр, чтобы хранить подробные данные в разделе [Метрики](analysis-services-monitor.md#server-metrics). Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.

3. Выберите команду **Сохранить**.

    Если отобразится сообщение об ошибке Failed to update diagnostics for \<workspace name>. The subscription \<subscription id> is not registered to use Microsoft.Insights (Не удалось обновить данные диагностики для <имя_рабочей_области>. Подписку <идентификатор_подписки> не зарегистрировано для использования Microsoft.Insights), следуйте инструкциям статьи [Сбор журналов и метрик для служб Azure для использования в Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage), чтобы зарегистрировать учетную запись, а затем повторите процедуру.

    Если вы хотите изменить способ сохранения журналов диагностики в дальнейшем, можно в любое время вернуться на эту страницу, чтобы изменить параметры.

### <a name="powershell"></a>PowerShell

Ниже приведены основные команды для ознакомления. Если вам требуются пошаговые указания по настройке ведения журнала в учетной записи хранения с помощью PowerShell, ознакомьтесь с руководством далее в этой статье.

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

Узнайте, как [изменить параметры диагностики с помощью REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Шаблон Resource Manager

Узнайте, как [включить параметры диагностики при создании ресурса из шаблона Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Управление журналами

Журналы обычно доступны через пару часов после настройки ведения журнала. Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.
* Не забудьте установить срок хранения, чтобы устаревшие журналы удалялись из вашей учетной записи хранения.

## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Метрики и события сервера объединяются с событиями xEvent в Log Analytics для параллельного анализа. Службу Log Analytics можно также настроить для получения событий из других служб Azure, обеспечив целостное представление о данных журнала ведения диагностики в своей архитектуре.

Чтобы просмотреть данные диагностики в Log Analytics, откройте страницу Log Analytics в меню слева или в области управления, как показано ниже.

![Параметры поиска по журналам на портале Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Теперь, когда вы включили сбор данных, в разделе **Поиск по журналу** щелкните **Все собранные данные**.

Из раскрывающегося списка **Тип** выберите **AzureDiagnostics**, а затем нажмите кнопку **Применить**. AzureDiagnostics включает в себя события "Подсистема" и "Служба". Обратите внимание на то, что запрос Log Analytics создается в режиме реального времени. Поле "EventClass\_s" содержит имена событий xEvent, которые могут быть вам знакомы, если вы использовали события xEvent для локального входа.

Щелкните поле "**EventClass\_s**" или одно из имен событий, и Log Analytics продолжит создание запроса. Не забудьте сохранить запросы для последующего повторного использования.

Обязательно ознакомьтесь со службой Log Analytics, которая предоставляет веб-сайт с расширенными возможностями создания запросов, панелей мониторинга и оповещений на основе собранных данных.

### <a name="queries"></a>Запросы

Можно использовать сотни запросов. Ниже приведено несколько запросов для ознакомления.
Чтобы узнать больше об использовании нового языка запросов поиска по журналам, прочитайте статью [Основные сведения о поисках по журналам в Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Запрос, возвращающий число запросов, которые были отправлены в Azure Analysis Services и на выполнение которых потребовалось более пяти минут (300 000 миллисекунд).

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Определение развернутых реплик.

    ```
    search * | summarize count() by ServerName_s
    ```
    При использовании горизонтального масштабирования можно определить реплики только для чтения, так как значения поля "ServerName\_s" содержат номер экземпляра реплики, добавленный к имени. Поле "resource" содержит имя ресурса Azure, которое соответствует имени сервера, видимому для пользователей. Поле "IsQueryScaleoutReadonlyInstance_s" для реплик имеет значение true.



> [!TIP]
> У вас есть отличный запрос Log Analytics, которым вы хотите поделиться? При наличии учетной записи GitHub его можно добавить в эту статью. Просто щелкните **Изменить** в правой верхней части этой страницы.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Руководство по включению ведения журнала с помощью PowerShell
В этом кратком руководстве вы создаете учетную запись хранения в тех же подписке и группе ресурсов, что и сервер Analysis Services. Затем с помощью Set-AzureRmDiagnosticSetting вы включаете журнал ведения диагностики, отправляя выходные данные в новую учетную запись хранения.

### <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуются следующие ресурсы:

* Существующий сервер Azure Analysis Services. Инструкции по созданию ресурса сервера см. в разделе [Создание сервера Azure Analysis Services на портале Azure](analysis-services-create-server.md) или [Создание сервера Azure Analysis Services с помощью PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Подключение к своим подпискам

Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

```powershell
Connect-AzureRmAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записью, и по умолчанию использует первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzureRmSubscription
```

Затем укажите подписку, связанную с учетной записью Azure Analysis Services, данные которой будут регистрироваться. Для этого введите следующую команду.

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Если с учетной записью связано несколько подписок, очень важно указать подписку, которую нужно использовать.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Создание учетной записи хранения для журналов

Можно использовать существующую учетную запись хранения для журналов, если она находится в той же подписке, что и ваш сервер. В этом руководстве создается новая учетная запись хранения для журналов Analysis Services. Для удобства вы сохраните сведения об учетной записи хранения в переменной **sa**.

Вы также используете группу ресурсов, которая содержит сервер Analysis Services. Замените значения `awsales_resgroup`, `awsaleslogs` и `West Central US` собственными значениями.

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Определение учетной записи сервера для журналов

Задайте в качестве имени учетной записи переменную **account**, где ResourceName — это имя учетной записи.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Включение ведения журналов

Чтобы включить ведение журнала, выполните командлет Set-AzureRmDiagnosticSetting с переменными для новой учетной записи хранения, учетной записи сервера и категории. Выполните следующую команду, задав для флага **-Enabled** значение **$true**:

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

Это значит, что ведение журнала для сервера включено, и данные сохраняются в учетной записи хранения.

Можно также задать политику хранения для журналов, чтобы устаревшие журналы автоматически удалялись. Например, задайте политику хранения, установив для флага **-RetentionEnabled** значение **$true**, а для параметра **-RetentionInDays** — значение **90**. Журналы, которым больше 90 дней, будут автоматически удаляться.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о [журнал ведения диагностики ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Ознакомьтесь с описанием [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) в справке PowerShell.
