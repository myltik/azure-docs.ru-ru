---
title: "Журнал ведения диагностики Azure Cosmos DB | Документация Майкрософт"
description: "Это руководство поможет вам приступить к работе с журналами Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: mimig
ms.openlocfilehash: 835f6ffce9b2e1bb4b6cfd7476bb3fdb24a4f092
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Журнал ведения диагностики Azure Cosmos DB

Когда вы начнете использовать одну или несколько баз данных Azure Cosmos DB, вам может потребоваться отследить, как и когда осуществляется доступ к вашим базам данных. В этом вам поможет журнал ведения диагностики в Azure Cosmos DB. Включив журнал ведения диагностики, вы можете отправить журналы в [службу хранилища Azure](https://azure.microsoft.com/services/storage/), настроить для них потоковую передачу в [концентраторы событий Azure](https://azure.microsoft.com/services/event-hubs/) и/или экспортировать их в [Log Analytics](https://azure.microsoft.com/services/log-analytics/), которая является частью [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

![Процесс ведения журнала диагностики в хранилище, концентраторе событий или Operations Management Suite через Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

Это руководство поможет приступить к работе с журналами Azure Cosmos DB с помощью портала Azure, интерфейса командной строки или PowerShell.

## <a name="what-is-logged"></a>Какие данные регистрируются в журнале?

* Все прошедшие аутентификацию запросы REST API SQL, включая запросы, ставшие неудачными из-за определенных разрешений на доступ, системных ошибок или неправильных запросов. Сейчас MongoDB, Graph и API таблицы не поддерживаются.
* Операции в самой базе данных, включая операции CRUD во всех документах, контейнерах и базах данных.
* Операции с ключами учетной записи, включая создание, изменение или удаление этих ключей.
* непроверенные запросы, которые приводят к появлению ответа 401 (например, запросы без токена носителя, с недействительным токеном, а также запросы неправильного формата или просроченные запросы).

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуются следующие ресурсы:

* Учетная запись, база данных и контейнер Azure Cosmos DB. Инструкции по созданию этих ресурсов см. в разделе [Создание учетной записи базы данных](create-sql-api-dotnet.md#create-a-database-account) или в статьях [Примеры Azure CLI для Azure Cosmos DB](cli-samples.md) и [Примеры Azure PowerShell для базы данных Azure Cosmos DB](powershell-samples.md).

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Включение ведения журнала на портале Azure

1. На [портале Azure](https://portal.azure.com) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации выберите **Журналы диагностики**, а затем — **Turn on diagnostics** (Включить диагностику).

    ![Включение журнала ведения диагностики для Azure Cosmos DB на портале Azure](./media/logging/turn-on-portal-logging.png)

2. На странице **Параметры диагностики** сделайте следующее: 

    * **Имя**. Введите имя для создаваемых журналов.

    * **Archive to a storage account** (Архивировать в учетной записи хранения). Чтобы использовать этот параметр, необходима учетная запись хранения для подключения. Чтобы создать учетную запись хранения на портале, ознакомьтесь со статьей [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md) и следуйте инструкциям по созданию учетной записи общего назначения Resource Manager. Затем на портале вернитесь на эту страницу, чтобы выбрать учетную запись хранения. Возможно, потребуется подождать несколько минут, пока созданная учетная запись хранения отобразится в раскрывающемся меню.
    * **Stream to an event hub** (Потоковая передача в концентратор событий). Чтобы использовать этот параметр, вам понадобится пространство имен концентратора событий и концентратор событий для подключения. Чтобы создать пространство имен концентратора событий, ознакомьтесь со статьей [Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure](../event-hubs/event-hubs-create.md). Затем на портале вернитесь на эту страницу, чтобы выбрать пространство имен концентратора событий и имя политики.
    * **Send to Log Analytics** (Отправить в Log Analytics).     Чтобы использовать этот параметр, воспользуйтесь одной из имеющихся рабочих областей или создайте рабочую область Log Analytics, следуя инструкциям по [созданию рабочей области](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) на портале. Дополнительные сведения о просмотре журналов в Log Analytics см. в статье [Журнал ведения диагностики Azure Cosmos DB](#view-in-loganalytics).
    * **Log DataPlaneRequests** (Журнал DataPlaneRequests). Выберите этот параметр, чтобы вести журнал диагностики для учетных записей API SQL, Graph и службы таблиц. Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.
    * **Log MongoRequests** (Журнал MongoRequest). Выберите этот параметр, чтобы вести журнал диагностики для учетных записей API MongoDB. Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.
    * **Metric Requests** (Запросы метрик). Выберите этот параметр, чтобы хранить подробные данные в [метриках Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Если выполняется архивация в учетную запись хранения, можно выбрать период удержания для журналов диагностики. По окончании периода хранения журналы удаляются автоматически.

3. Выберите команду **Сохранить**.

    Если отобразится сообщение об ошибке Failed to update diagnostics for \<workspace name>. The subscription \<subscription id> is not registered to use Microsoft.Insights (Не удалось обновить данные диагностики для <имя_рабочей_области>. Подписку <идентификатор_подписки> не зарегистрировано для использования Microsoft.Insights), следуйте инструкциям статьи [Сбор журналов и метрик для служб Azure для использования в Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage), чтобы зарегистрировать учетную запись, а затем повторите процедуру.

    Если вы хотите изменить способ сохранения журналов диагностики в дальнейшем, можно в любое время вернуться на эту страницу, чтобы изменить параметры журнала диагностики для своей учетной записи.

## <a name="turn-on-logging-using-cli"></a>Включение ведения журнала с помощью интерфейса командной строки

Чтобы включить метрики и журналы диагностики с помощью Azure CLI, используйте следующие команды:

- Эта команда включает отправку журналов диагностики в учетную запись хранения:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` — это имя учетной записи Azure Cosmos DB. `storageId` — это имя учетной записи хранения, в которую вы хотите отправить журналы.

- Эта команда включает потоковую передачу журналов диагностики в концентратор событий:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` — это имя учетной записи Azure Cosmos DB. `serviceBusRuleId` — это строка в таком формате:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

## <a name="turn-on-logging-using-powershell"></a>Включение ведения журнала с помощью PowerShell

Чтобы включить ведение журнала с помощью PowerShell, вам понадобится Azure PowerShell версии 1.0.1 или выше.

Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

Если инструмент Azure PowerShell уже установлен, но вы не знаете его версию, в консоли PowerShell введите `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Подключение к подпискам
Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду:  

```powershell
Login-AzureRmAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записью, и по умолчанию использует первую из них.

Если у вас есть несколько подписок, возможно, вам нужно будет указать ту, которая использовалась для создания хранилища ключей Azure. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzureRmSubscription
```

Затем укажите подписку, связанную с учетной записью Azure Cosmos DB, данные которой будут регистрироваться. Для этого введите следующую команду:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Если с учетной записью связано несколько подписок, очень важно указать подписку, которую нужно использовать.
>
>

Дополнительные сведения о настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Создание учетной записи хранения для журналов
Хотя вы можете использовать и имеющуюся учетную запись хранения для журналов, в этом руководстве мы создадим учетную запись хранения, которая будет использоваться для журналов Azure Cosmos DB. Для удобства мы сохраним сведения об учетной записи хранения в переменной с именем **sa**.

Чтобы упростить управление, в этом руководстве мы будем использовать ту же группу ресурсов, которая содержит базу данных Azure Cosmos DB. При необходимости замените значения ContosoResourceGroup, contosocosmosdblogs и "Северо-центральный регион США" своими:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Если вы планируете использовать имеющуюся учетную запись хранения, для нее должна использоваться та же подписка, что и для Azure Cosmos DB. Кроме того, она должна быть создана с использованием модели развертывания с помощью Resource Manager, а не классической модели развертывания.
>
>

### <a id="identify"></a>Определение учетной записи Azure Cosmos DB для журналов
Задайте в качестве имени учетной записи Azure Cosmos DB переменную **account**, где ResourceName — это имя учетной записи Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Включение ведения журналов
Чтобы включить ведение журнала для Azure Cosmos DB, используйте командлет Set-AzureRmDiagnosticSetting вместе с переменными для новой учетной записи хранения, учетной записью хранения Azure Cosmos DB и категорией, для которой необходимо включить журналы. Выполните следующую команду, задав для флага **-Enabled** значение **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Выходные данные команды должны выглядеть следующим образом:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Таким образом подтверждается включение регистрации данных для базы данных и их сохранение в учетной записи хранения.

При необходимости можно также задать политику хранения для журналов, например политику, в соответствии с которой старые журналы будут автоматически удаляться. Например, задайте политику хранения, установив для флага **-RetentionEnabled** значение **$true**, а для параметра **-RetentionInDays** — значение **90**, чтобы автоматически удалять журналы, которые хранятся более 90 дней.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Доступ к журналам
Журналы Azure Cosmos DB для категории **DataPlaneRequests** хранятся в контейнере **insights-logs-data-plane-requests** в указанной вами учетной записи хранения. 

Сначала создайте переменную для имени контейнера. Она будет использоваться для выполнения действий в этом пошаговом руководстве.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Чтобы получить список всех больших двоичных объектов (BLOB-объектов) в этом контейнере, введите следующее.

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Вы получите приблизительно такой результат:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Как видно из этих выходных данных, имена больших двоичных объектов соответствуют соглашению об именовании: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Для значений даты и времени используется время в формате UTC.

Так как ту же учетную запись можно использовать при сборе журналов для нескольких ресурсов, для доступа к нужным большим двоичным объектам (или для их загрузки) мы рекомендуем использовать полный идентификатор ресурса в имени большого двоичного объекта. Но сначала мы рассмотрим загрузку всех BLOB-объектов.

Во-первых, создайте папку для загрузки BLOB-объектов. Например: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Затем выведите список всех BLOB-объектов.  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Передайте этот список с помощью команды Get-AzureStorageBlobContent, чтобы загрузить BLOB-объекты в папку назначения.

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

При выполнении второй команды разделитель **/** в именах больших двоичных объектов используется для создания полной структуры папки в конечной папке. Эта структура папок будет использоваться для скачивания и хранения больших двоичных объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Например: 

* Если у вас есть несколько баз данных, но вы хотите скачать журналы только для одной базы данных с именем CONTOSOCOSMOSDB3, выполните следующую команду.

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Если у вас есть несколько групп ресурсов, но вы хотите загрузить журналы только для одной из них, используйте `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Если вы хотите загрузить все журналы за июль 2017 г., используйте `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Кроме того, сделайте следующее:

* Чтобы запросить сведения о состоянии параметров диагностики для ресурса базы данных, выполните следующую команду: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Чтобы отключить ведение журнала категории **DataPlaneRequests** для ресурса учетной записи базы данных, выполните следующую команду: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Большие двоичные объекты, возвращаемые в каждом из этих запросов, хранятся как текст в формате JSON, как показано в следующем коде. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Дополнительные сведения о данных в каждом большом двоичном объекте JSON см. в разделе [Интерпретация журналов Azure Cosmos DB](#interpret).

## <a name="managing-your-logs"></a>Управление журналами

Журналы становятся доступными в вашей учетной записи через два часа с момента выполнения операции Azure Cosmos DB. Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.
* Срок хранения запросов для плоскости данных, архивированных в учетной записи хранения, можно задать на портале при выборе параметра **Log DataPlaneRequests** (Журнал DataPlaneRequests). Чтобы изменить эту настройку, ознакомьтесь с разделом [Включение ведения журнала на портале Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Если вы выбрали параметр **Send to Log Analytics** (Отправить в Log Analytics) при включении ведения журнала, данные диагностики из вашей коллекции будут перенаправлены в Log Analytics в течение двух часов. Это означает, что если взглянуть на Log Analytics сразу после включения ведения журнала, данные отображаться не будут. Подождите два часа и повторите попытку. 

Перед просмотром журналов необходимо проверить, обновлена ли рабочая область Log Analytics для использования нового языка запросов Log Analytics. Чтобы проверить это, откройте [портал Azure](https://portal.azure.com), выберите **Log Analytics** слева и выберите имя рабочей области, как показано на рисунке ниже. Страница **рабочей области OMS** отображается, как показано на следующем рисунке.

![Log Analytics на портале Azure](./media/logging/azure-portal.png)

Если вы видите указанное ниже сообщение на странице **рабочей области OMS**, ваша рабочая область обновлена для использования нового языка. Дополнительные сведения об обновлении для использования нового языка запросов см. в статье [Обновление рабочей области Azure Log Analytics до нового поиска по журналам](../log-analytics/log-analytics-log-search-upgrade.md). 

![Уведомление об обновлении Log Analytics](./media/logging/upgrade-notification.png)

Чтобы просмотреть данные диагностики в Log Analytics, откройте страницу Log Analytics в меню слева или в области управления, как показано на следующем рисунке.

![Параметры поиска по журналам на портале Azure](./media/logging/log-analytics-open-log-search.png)

Теперь, когда вы включили сбор данных, выполните следующий пример поиска по журналам, используя новый язык запросов, чтобы увидеть десять последних журналов: `AzureDiagnostics | take 10`.

![Пример выполнения поиска 10 журналов](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Запросы

Ниже приведены некоторые дополнительные запросы, которые можно ввести в поле **поиска по журналам**, чтобы выполнить мониторинг контейнеров Azure Cosmos DB. Эти запросы поддерживают [новый язык](../log-analytics/log-analytics-log-search-upgrade.md). 

Дополнительные сведения о значении данных, возвращаемых каждым поиском по журналам, см. в статье [Интерпретация журналов Azure Cosmos DB](#interpret).

* Для вывода журналов диагностики из Azure Cosmos DB за указанный период времени:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Для вывода десяти последних событий журнала:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Для вывода всех операций, сгруппированных по типу операции:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Для вывода всех операций, сгруппированных по ресурсу:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Для вывода всех действий пользователя, сгруппированных по ресурсу. Обратите внимание, что это журнал действий, а не журнал диагностики.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Для вывода операций, которые выполнялись дольше 3 миллисекунд:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Для вывода агента, выполняющего операции:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Для вывода сведений о том, когда были выполнены долго выполняющиеся операции:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Дополнительные сведения об использовании нового языка поиска по журналам см. в статье [Основные сведения о поисках по журналам в Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Интерпретация журналов

Диагностические данные, хранящиеся в службе хранилища Azure и Log Analytics, используют схожую схему. 

В следующей таблице описано содержимое каждой записи журнала.

| Поле или свойство службы хранилища Azure | Свойство Log Analytics | ОПИСАНИЕ |
| --- | --- | --- |
| Twitter в режиме реального | TimeGenerated | Дата и время (UTC) выполнения операции. |
| ResourceId | Ресурс | Учетная запись Azure Cosmos DB, для которой включены журналы.|
| category | Категория | Для журналов Azure Cosmos DB единственным доступным значением является DataPlaneRequests. |
| operationName | OperationName | Имя операции. В качестве значения можно использовать любую из следующих операций: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed или Upsert.   |
| properties | Недоступно | Содержимое этого поля описано в указанных ниже строках. |
| activityId | activityId_g | Уникальный идентификатор GUID регистрируемой операции. |
| userAgent | userAgent_s | Строка, указывающая агент пользователя клиента, выполняющий запрос. Формат: {имя_агента_пользователя} / {версия}.|
| тип_ресурса | ResourceType | Тип доступного ресурса. В качестве значения можно использовать любой из следующих типов ресурсов: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction или Offer. |
| statusCode |statusCode_s | Состояние ответа операции. |
| requestResourceId | ResourceId | В зависимости от выполняемой операции параметр resourceId, относящийся к запросу, может указывать на databaseRid, collectionRid или documentRid.|
| clientIpAddress | clientIpAddress_s | IP-адрес клиента. |
| requestCharge | requestCharge_s | Количество ЕЗ, используемых при операции. |
| collectionRid | collectionId_s | Уникальный идентификатор коллекции.|
| длительность | duration_s | Длительность операции в тактах. |
| requestLength | requestLength_s | Длина запроса в байтах. |
| responseLength | responseLength_s | Длина ответа в байтах.|
| resourceTokenUserRid | resourceTokenUserRid_s | Это поле должно быть заполненным, если [токены ресурсов](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) используются для аутентификации и указывают на идентификатор ресурса пользователя. |

## <a name="next-steps"></a>Дополнительная информация

- Чтобы не только научиться включать ведение журнала, но и узнать, какие категории журналов поддерживаются различными службами Azure, прочитайте статьи [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) и [Сбор и использование диагностических данных из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Прочтите эти статьи, чтобы узнать о концентраторах событий:
   - [Что такое концентраторы событий?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Ознакомьтесь с разделом [Скачивание больших двоичных объектов](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).
- Ознакомьтесь со статьей [Основные сведения о поисках по журналам в Log Analytics](../log-analytics/log-analytics-log-search-new.md).
