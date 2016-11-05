---
title: Пошаговое руководство по REST API Azure Monitor | Microsoft Docs
description: Описывается, как аутентифицировать запросы к интерфейсу REST API Azure Monitor и использовать его.
author: mcollier
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Пошаговое руководство по REST API Azure Monitor
В этой статье показано, как выполнять аутентификацию таким образом, чтобы в коде можно было использовать [справочник по REST API Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

API Azure Monitor позволяет программно получить определения доступных метрик по умолчанию (тип метрики, например "Время ЦП", "Запросы" и т. д.), детализацию и значения метрик. После получения эти данные можно сохранить в отдельном хранилище данных, в том числе в базе данных SQL Azure, DocumentDB или Azure Data Lake. Там при необходимости можно выполнить дополнительный анализ.

Помимо работы с различными точками данных метрик, как показано в этой статье, API Monitor позволяет получить список правил генерации оповещений, просмотреть журналы действий и многое другое. Полный список доступных операций см. в [справочнике по REST API для Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Аутентификация запросов Azure Monitor
В первую очередь нужно аутентифицировать запрос.

Все задачи, выполняемые с использованием API Azure Monitor, используют модель аутентификации Azure Resource Manager. Поэтому все запросы должны быть аутентифицированы в Azure Active Directory (Azure AD). Один из способов аутентификации клиентского приложения — создание субъекта-службы Azure AD и получение маркера аутентификации (JWT). Приведенный ниже пример сценария демонстрирует создание субъекта-службы Azure AD посредством PowerShell. Более подробные пошаговые инструкции приведены в документации по [использованию Azure PowerShell для создания субъекта-службы для доступа к ресурсам](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). Существует также возможность [создать субъект-службу на портале Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Insights" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Чтобы отправить запрос к API Azure Monitor, клиентское приложение должно использовать ранее созданный субъект-службу для прохождения аутентификации. В приведенном ниже примере сценария PowerShell показан один подход, в котором для получения маркера аутентификации JWT используется [библиотека аутентификации Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL). Этот маркер JWT передается как часть параметра аутентификации HTTP в запросах к API Azure Insights.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

После завершения настройки аутентификации можно будет выполнять запросы к REST API Azure Monitor. Можно использовать два полезных запроса:

1. вывод списка определений метрик для ресурса;
2. получение значений метрик.

## <a name="retrieve-metric-definitions"></a>Получение определений метрик
> [!NOTE]
> Чтобы получить определения метрик с помощью REST API Azure Monitor, используйте API версии 2016-03-01.
> 
> 

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Для приложения логики Azure определения метрик будут выглядеть как на следующем снимке экрана.

![Представление ответа JSON для определения метрики](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Дополнительные сведения см. в статье [List metric definitions for a resource in Azure Monitor REST API](https://msdn.microsoft.com/library/azure/mt743621.aspx) (Вывод списка определений метрик для ресурса в REST API Azure Monitor).

## <a name="retrieve-metric-values"></a>Получение значений метрик
Когда определения доступных метрик известны, можно получить соответствующие значения метрик. Используйте значение value имени метрики (а не localizedValue) для фильтрации запросов (например, получите точки данных метрик CpuTime и Requests). Если не указано ни одного фильтра, возвращается метрика по умолчанию.

> [!NOTE]
> Чтобы получить значения метрики с помощью REST API Azure Monitor, используйте API версии 2016-06-01.
> 
> 

**Метод**: GET

**URI запроса**: https://management.azure.com/subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/*{пространство_имен_поставщиков_ресурсов}*/*{тип_ресурса}*/*{имя_ресурса*/providers/microsoft.insights/metrics?$filter=*{фильтр}*&api-version=*{версия_API}*

Например, запрос для получения точек данных метрики RunsSucceeded в заданном диапазоне времени с интервалом в 1 час будет выглядеть следующим образом.

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Результат будет выглядеть как в примере, показанном на следующем снимке экрана.

![Ответ JSON, отображающий значение метрики "Среднее время ответа"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Чтобы получить несколько точек данных или точек статистической обработки, добавьте имена определений метрик и типы статистической обработки в фильтр, как показано в примере ниже.

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Использование ARMClient
Вместо PowerShell (описано выше) на компьютере Windows можно использовать [ARMClient](https://github.com/projectkudu/ARMClient) . ARMClient автоматически обрабатывает аутентификацию в Azure AD (и полученный маркер JWT). Ниже описано использование ARMClient для получения данных метрик.

1. Установите [Chocolatey](https://chocolatey.org/) и [ARMClient](https://github.com/projectkudu/ARMClient).
2. В окне терминала введите *armclient.exe login*. Отобразится запрос на вход в Azure.
3. Введите *armclient GET [ИД_ресурса]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*.
4. Введите *armclient GET [ИД_ресурса]/providers/microsoft.insights/metrics?api-version=2016-06-01*.

![Использование ARMClient для работы с REST API Azure Monitor](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>Получение идентификатора ресурса
Применение REST API действительно помогает понять доступные определения метрик, детализацию и связанные значения. Эта информация полезна при использовании [библиотеки управления Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Для приведенного выше кода используемый идентификатор ресурса — это полный путь к нужному ресурсу Azure. Например, для запроса к веб-приложению Azure идентификатор ресурса будет следующим.

*/subscriptions/{ИД_подписки}/resourceGroups/{имя_группы_ресурсов}/providers/Microsoft.Web/sites/{имя_сайта}/*

Приведенный ниже список содержит несколько примеров форматов идентификаторов различных ресурсов Azure.

* **Центр Интернета вещей (IoT)**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.Devices/IotHubs/*{имя_центра_IoT}*.
* **Пул эластичных баз данных SQL**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.Sql/servers/*{база_данных_в_пуле}*/elasticpools/*{имя_пула_SQL}*.
* **База данных SQL (версии 12)**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.Sql/servers/*{имя_сервера}*/databases/*{имя_базы_данных}*.
* **Служебная шина**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.ServiceBus/*{пространство_имен}*/*{имя_служебной_шины}*.
* **Наборы масштабирования ВМ**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{имя_ВМ}*.
* **Виртуальные машины**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.Compute/virtualMachines/*{имя_ВМ}*.
* **Концентраторы событий**: /subscriptions/*{ИД_подписки}*/resourceGroups/*{имя_группы_ресурсов}*/providers/Microsoft.EventHub/namespaces/*{пространство_имен_концентраторов_событий}*.

Существуют альтернативные подходы к извлечению идентификатора ресурса, включая использование Azure Resource Explorer, PowerShell, интерфейса командной строки Azure и просмотр требуемого ресурса на портале Azure.

### <a name="azure-resource-explorer"></a>Обозреватель ресурсов Azure
Чтобы найти идентификатор требуемого ресурса, удобно использовать инструмент [Azure Resource Explorer](https://resources.azure.com) . Перейдите к нужному ресурсу и просмотрите отображенный идентификатор, как показано на следующем снимке экрана.

![Azure Resource Explorer](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Портал Azure
Идентификатор ресурса можно также получить на портале Azure. Для этого перейдите к нужному ресурсу и выберите "Свойства". Идентификатор ресурса отображается в колонке "Свойства", как показано на следующем снимке экрана.

![Идентификатор ресурса в колонке "Свойства" на портале Azure](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Идентификатор ресурса можно получить и с помощью командлетов Azure PowerShell. Например, чтобы получить идентификатор ресурса для веб-приложения Azure, выполните командлет Get-AzureRmWebApp, как показано на следующем снимке экрана.

![Идентификатор ресурса, полученный с помощью PowerShell](./media\\monitoring-rest-api-walkthrough\\resourceid_powershell.png)

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы получить идентификатор ресурса с помощью интерфейса командной строки Azure, выполните команду azure webapp show, указав параметр --json, как показано на следующем снимке экрана.

![Идентификатор ресурса, полученный с помощью PowerShell](./media\\monitoring-rest-api-walkthrough\\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Получение данных журнала действий
Помимо работы с определениями метрик и связанными значениями, можно также получать дополнительные интересные сведения, связанные с ресурсами Azure. Например, можно запросить данные [журнала действий](https://msdn.microsoft.com/library/azure/dn931934.aspx) . В приведенном ниже примере показано использование REST API Azure Monitor для запроса данных журнала действий для подписки Azure в определенном диапазоне дат. 

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Дальнейшие действия
* Прочитайте [общие сведения о мониторинге](monitoring-overview.md).
* Ознакомьтесь с разделом [Метрики, поддерживаемые Azure Monitor](monitoring-supported-metrics.md).
* Ознакомьтесь со [справочником по REST API Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Ознакомьтесь с [библиотекой управления Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

<!--HONumber=Oct16_HO2-->


