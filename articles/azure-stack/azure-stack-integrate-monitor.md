---
title: Интеграция внешнего решения для мониторинга с Azure Stack | Документация Майкрософт
description: Узнайте, как интегрировать Azure Stack с внешним решением для мониторинга в центре обработки данных.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e47141d31d3876264eaf2bcb7dc562a4711048cc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Интеграция внешнего решения для мониторинга с Azure Stack

Для внешнего мониторинга инфраструктуры Azure Stack требуется отслеживать программное обеспечение Azure Stack, физические компьютеры и физические сетевые коммутаторы. Каждая из этих областей предлагает свой метод извлечения сведений о работоспособности и оповещениях.

- Программное обеспечение Azure Stack предлагает API на основе REST для получения данных работоспособности и оповещений. (При использовании таких программно-определяемых технологий, как служба "Локальные дисковые пространства", отслеживание работоспособности хранилища и оповещения являются частью мониторинга программного обеспечения.)
- Физические компьютеры могут предоставлять данные работоспособности и оповещений через контроллеры управления основной платой (BMC).
- Физические сетевые устройства могут предоставлять данные работоспособности и оповещений по протоколу SNMP.

Каждое решение Azure Stack поставляется с узлом отслеживания жизненного цикла оборудования. На нем выполняется программное обеспечение для мониторинга физических серверов и сетевых устройств, предоставленное поставщиком изготовителя оборудования. При необходимости вы можете обойти эти решения для мониторинга и осуществить непосредственную интеграцию с существующими решениями для мониторинга в своем центре обработки данных.

> [!IMPORTANT]
> Внешние решения для мониторинга, которые вы используете, не должны использовать агенты. Нельзя устанавливать сторонние агенты внутри компонентов Azure Stack.

На следующей диаграмме показан поток трафика между интегрированной системой Azure Stack, узлом отслеживания жизненного цикла оборудования, внешним решением для мониторинга и внешней системой отправки запросов и сбора данных.

![Схема прохождения трафика между Azure Stack, решением для мониторинга и решением для отправки запросов](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

В этой статье объясняется, как интегрировать Azure Stack с внешними решениями для мониторинга, например System Center Operations Manager и Nagios. В ней также поясняется, как программно работать с оповещениями с помощью PowerShell или вызовов REST API.

## <a name="integrate-with-operations-manager"></a>Интеграция с Operations Manager

Operations Manager можно использовать для внешнего мониторинга Azure Stack. Пакет управления System Center для Microsoft Azure Stack позволяет отслеживать несколько развернутых служб Azure Stack с помощью одного экземпляра Operations Manager. Пакет управления использует интерфейсы REST API поставщика ресурсов работоспособности и поставщика ресурсов обновления для взаимодействия с Azure Stack. Если вы планируете обойти программное обеспечение для мониторинга, предоставленное изготовителем оборудования, которое выполняется на узле отслеживания жизненного цикла оборудования, то вы можете установить пакеты управления поставщика оборудования для отслеживания физических серверов. Можно также использовать обнаружение сетевых устройств Operations Manager для наблюдения за сетевыми коммутаторами.

Пакет управления для Azure Stack предоставляет следующие возможности.

- Можно управлять несколькими развернутыми службами Azure Stack.
- Поддерживаются Azure Active Directory (Azure AD) и службы федерации Active Directory (AD FS).
- Можно извлекать и закрывать оповещения.
- Доступна панель мониторинга работоспособности и производительности.
- Доступно автоматическое обнаружение режима обслуживания, в котором выполняется установка исправлений и обновлений.
- Доступны задачи принудительного обновления для развернутой службы и региона.
- Можно добавить пользовательские сведения для региона.
- Поддержка уведомлений и отчетов.

Скачать пакет управления System Center для Microsoft Azure Stack и соответствующее руководство пользователя можно [отсюда](https://www.microsoft.com/en-us/download/details.aspx?id=55184) или непосредственно из Operations Manager.

Чтобы реализовать решение для отправки запросов, можно интегрировать Operations Manager с System Center Service Manager. Интегрированный соединитель продукта обеспечивает двунаправленный обмен данными, который позволяет закрыть оповещение в Azure Stack и Operations Manager после обработки запроса на обслуживание в Service Manager.

На следующей схеме показана интеграция Azure Stack с существующим развертыванием System Center. Можно еще больше автоматизировать работу Service Manager с помощью System Center Orchestrator или Service Management Automation (SMA) для выполнения операций в Azure Stack.

![Схема интеграции с Operations Manager, Service Manager и SMA](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Интеграция с Nagios

Подключаемый модуль мониторинга Nagios был разработан вместе с партнерскими решениями Cloudbase, которые предоставляются по разрешенной лицензии на бесплатное программное обеспечение Массачусетского технологического института.

Этот подключаемый модуль создан на языке Python, и в нем используется REST API поставщика ресурсов работоспособности. Он обеспечивает базовые функции получения и закрытия оповещений в Azure Stack. Как и пакет управления System Center, он позволяет добавить несколько развернутых служб Azure Stack и отправлять уведомления.

Подключаемый модуль работает с Nagios Enterprise и Nagios Core. Его можно скачать [здесь](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Сайт скачивания также содержит сведения об установке и настройке.

### <a name="plugin-parameters"></a>Параметры подключаемого модуля

Настройте файл подключаемого модуля Azurestack_plugin.py, указав следующие параметры.

| Параметр | ОПИСАНИЕ | Пример |
|---------|---------|---------|
| *arm_endpoint* | Конечная точка Azure Resource Manager (администратор). |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Конечная точка Azure Resource Manager (администратор).  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Идентификатор подписки администратора. | Его можно получить с помощью портала администратора или PowerShell. |
| *User_name* | Имя пользователя в подписке оператора. | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Пароль подписки оператора. | mypassword |
| *Client_id* | Клиент | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Имя региона Azure Stack. | local |
|  |  |

* Указан универсальный глобальный уникальный идентификатор PowerShell. Его можно использовать для каждого развертывания.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Мониторинг работоспособности и оповещений с помощью PowerShell

Если вы не используете Operations Manager, Nagios или решение на основе Nagios, то можете использовать PowerShell, чтобы интегрировать широкий спектр решений для мониторинга с Azure Stack.
 
1. Чтобы использовать PowerShell, убедитесь, в среде оператора Azure Stack [установлен и настроен компонент PowerShell](azure-stack-powershell-configure-quickstart.md). Установите PowerShell на локальном компьютере с доступом к конечной точке Resource Manager (администратор) (https://adminmanagement.[регион].[внешнее_полное_доменное_имя]).

2. Выполните следующие команды для подключения к среде Azure Stack в качестве оператора Azure Stack.

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Перейдите в каталог, в который были установлены [инструменты Azure Stack](https://github.com/Azure/AzureStack-Tools) при установке PowerShell, например c:\azurestack-tools-master. Затем перейдите в каталог Infrastructure и выполните приведенную ниже команду, чтобы импортировать модуль Infrastructure.

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Ниже приведены примеры команд для работы с оповещениями.
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Мониторинг работоспособности и оповещений с помощью REST API

С помощью вызовов REST API можно получать оповещения, закрывать оповещения и получать данные работоспособности поставщиков ресурсов.

### <a name="get-alert"></a>Получение оповещения

**Запрос**

Запрос возвращает все активные и закрытые оповещения для подписки поставщика по умолчанию. Запроса не содержит текст.


|Метод  |URI запроса  |
|---------|---------|
|ПОЛУЧЕНИЕ     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Аргументы**

|Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|armendpoint     |  Конечная точка Azure Resource Manager среды Azure Stack в формате https://adminmanagement.{RegionName}.{External полное_доменное_имя}. Например, если внешнее полное доменное имя — *azurestack.external* и имя региона — *local*, то конечная точка Resource Manager — https://adminmanagement.local.azurestack.external.       |
|subid     |   Идентификатор подписки пользователя, который выполняет вызов. Этот API может использовать для запроса только пользователь, имеющий разрешение для подписки поставщика по умолчанию.      |
|RegionName     |    Имя региона развертывания Azure Stack.     |
|api-version     |  Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию 2016-05-01.      |
|     |         |

**Ответ**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Сведения об ответе**


|  Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|*id*     |      Уникальный идентификатор оповещения.   |
|*name*     |     Внутреннее имя оповещения.   |
|*type*     |     Определение ресурса.    |
|*расположение*     |       Имя региона.     |
|*теги*     |   Теги ресурсов.     |
|*closedtimestamp*    |  Время закрытия оповещения в формате UTC.    |
|*createdtimestamp*     |     Время создания оповещения в формате UTC.   |
|*description*     |    Описание оповещения.     |
|*faultid*     | Затронутый компонент.        |
|*alertid*     |  Уникальный идентификатор оповещения.       |
|*faulttypeid*     |  Уникальный тип неисправного компонента.       |
|*lastupdatedtimestamp*     |   Время последнего обновления сведений об оповещении в формате UTC.    |
|*healthstate*     | Общее состояние работоспособности.        |
|*name*     |   Имя определенного оповещения.      |
|*fabricname*     |    Зарегистрированное имя структуры неисправного компонента.   |
|*description*     |  Описание компонента зарегистрированной структуры.   |
|*servicetype*     |   Тип службы зарегистрированной структуры.   |
|*remediation*     |   Рекомендуемые действия по исправлению.    |
|*type*     |   Тип оповещения.    |
|*resourceRegistrationid*    |     Идентификатор затронутого зарегистрированного ресурса.    |
|*resourceProviderRegistrationID*   |    Идентификатор зарегистрированного поставщика ресурсов затронутого компонента.  |
|*serviceregistrationid*     |    Идентификатор зарегистрированной службы.   |
|*severity*     |     Серьезность оповещения.  |
|*state*     |    Состояние оповещения.   |
|*title*     |    Заголовок оповещения.   |
|*impactedresourceid*     |     Идентификатор затронутого ресурса.    |
|*ImpactedresourceDisplayName*     |     Имя затронутого ресурса.  |
|*closedByUserAlias*     |   Пользователь, который закрыл оповещение.      |

### <a name="close-alert"></a>Закрытие оповещения

**Запрос**

Запрос закрывает оповещение по уникальному идентификатору.

|Метод    |URI запроса  |
|---------|---------|
|ОТПРАВКА     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Аргументы**


|Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|*armendpoint*     |   Конечная точка Resource Manager среды Azure Stack в формате https://adminmanagement.{RegionName}.{External полное_доменное_имя}. Например, если внешнее полное доменное имя — *azurestack.external* и имя региона — *local*, то конечная точка Resource Manager — https://adminmanagement.local.azurestack.external.      |
|*subId*     |    Идентификатор подписки пользователя, который выполняет вызов. Этот API может использовать для запроса только пользователь, имеющий разрешение для подписки поставщика по умолчанию.     |
|*RegionName*     |   Имя региона развертывания Azure Stack.      |
|*api-version*     |    Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию 2016-05-01.     |
|*alertid*     |    Уникальный идентификатор оповещения.     |

**Текст**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Ответ**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Сведения об ответе**


|  Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|*id*     |      Уникальный идентификатор оповещения.   |
|*name*     |     Внутреннее имя оповещения.   |
|*type*     |     Определение ресурса.    |
|*расположение*     |       Имя региона.     |
|*теги*     |   Теги ресурсов.     |
|*closedtimestamp*    |  Время закрытия оповещения в формате UTC.    |
|*createdtimestamp*     |     Время создания оповещения в формате UTC.   |
|*Описание*     |    Описание оповещения.     |
|*faultid*     | Затронутый компонент.        |
|*alertid*     |  Уникальный идентификатор оповещения.       |
|*faulttypeid*     |  Уникальный тип неисправного компонента.       |
|*lastupdatedtimestamp*     |   Время последнего обновления сведений об оповещении в формате UTC.    |
|*healthstate*     | Общее состояние работоспособности.        |
|*name*     |   Имя определенного оповещения.      |
|*fabricname*     |    Зарегистрированное имя структуры неисправного компонента.   |
|*description*     |  Описание компонента зарегистрированной структуры.   |
|*servicetype*     |   Тип службы зарегистрированной структуры.   |
|*remediation*     |   Рекомендуемые действия по исправлению.    |
|*type*     |   Тип оповещения.    |
|*resourceRegistrationid*    |     Идентификатор затронутого зарегистрированного ресурса.    |
|*resourceProviderRegistrationID*   |    Идентификатор зарегистрированного поставщика ресурсов затронутого компонента.  |
|*serviceregistrationid*     |    Идентификатор зарегистрированной службы.   |
|*severity*     |     Серьезность оповещения.  |
|*state*     |    Состояние оповещения.   |
|*title*     |    Заголовок оповещения.   |
|*impactedresourceid*     |     Идентификатор затронутого ресурса.    |
|*ImpactedresourceDisplayName*     |     Имя затронутого ресурса.  |
|*closedByUserAlias*     |   Пользователь, который закрыл оповещение.      |

### <a name="get-resource-provider-health"></a>Получение данных работоспособности поставщиков ресурсов

**Запрос**

Запрос возвращает состояние работоспособности для всех зарегистрированных поставщиков ресурсов.


|Метод  |URI запроса  |
|---------|---------|
|ПОЛУЧЕНИЕ    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Аргументы**


|Аргументы  |ОПИСАНИЕ  |
|---------|---------|
|*armendpoint*     |    Конечная точка Resource Manager среды Azure Stack в формате https://adminmanagement.{RegionName}.{External полное_доменное_имя}. Например, если внешнее полное доменное имя — azurestack.external и имя региона — local, то конечная точка Resource Manager — https://adminmanagement.local.azurestack.external.     |
|*subId*     |     Идентификатор подписки пользователя, который выполняет вызов. Этот API может использовать для запроса только пользователь, имеющий разрешение для подписки поставщика по умолчанию.    |
|*RegionName*     |     Имя региона развертывания Azure Stack.    |
|*api-version*     |   Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию 2016-05-01.      |


**Ответ**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Сведения об ответе**


|Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|*Id*     |   Уникальный идентификатор оповещения.      |
|*name*     |  Внутреннее имя оповещения.       |
|*type*     |  Определение ресурса.       |
|*расположение*     |  Имя региона.       |
|*теги*     |     Теги ресурсов.    |
|*registrationId*     |   Уникальный идентификатор регистрации поставщика ресурсов.      |
|*displayName*     |Отображаемое имя поставщика ресурсов.        |
|*namespace*     |   Реализуемое поставщиком ресурсов пространство имен API.       |
|*routePrefix*     |    Универсальный код ресурса (URI) для взаимодействия с поставщиком ресурсов.     |
|*serviceLocation*     |   Регион, в котором зарегистрирован этот поставщик ресурсов.      |
|*infraURI*     |   Универсальный код ресурса (URI) поставщика ресурсов, отображенный в качестве роли инфраструктуры.      |
|*alertSummary*     |   Сводка по оповещениям и предупреждениям, связанным с поставщиком ресурсов.      |
|*healthState*     |    Состояние работоспособности поставщика ресурсов.     |


### <a name="get-resource-health"></a>Получение данных работоспособности ресурса

Запрос возвращает состояние работоспособности для определенного зарегистрированного поставщика ресурсов.

**Запрос**

|Метод  |URI запроса  |
|---------|---------|
|ПОЛУЧЕНИЕ     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Аргументы**

|Аргументы  |ОПИСАНИЕ  |
|---------|---------|
|*armendpoint*     |    Конечная точка Resource Manager среды Azure Stack в формате https://adminmanagement.{RegionName}.{External полное_доменное_имя}. Например, если внешнее полное доменное имя — azurestack.external и имя региона — local, то конечная точка Resource Manager — https://adminmanagement.local.azurestack.external.     |
|*subId*     |Идентификатор подписки пользователя, который выполняет вызов. Этот API может использовать для запроса только пользователь, имеющий разрешение для подписки поставщика по умолчанию.         |
|*RegionName*     |  Имя региона развертывания Azure Stack.       |
|*api-version*     |  Версия протокола, который используется для выполнения этого запроса. Необходимо использовать версию 2016-05-01.       |
|*RegistrationID* |Идентификатор регистрации конкретного поставщика ресурсов. |

**Ответ**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Сведения об ответе**

|Аргумент  |ОПИСАНИЕ  |
|---------|---------|
|*Id*     |   Уникальный идентификатор оповещения.      |
|*name*     |  Внутреннее имя оповещения.       |
|*type*     |  Определение ресурса.       |
|*расположение*     |  Имя региона.       |
|*теги*     |     Теги ресурсов.    |
|*registrationId*     |   Уникальный идентификатор регистрации поставщика ресурсов.      |
|*resourceType*     |Тип ресурса.        |
|*resourceName*     |   Имя ресурса.   |
|*usageMetrics*     |    Метрики использования ресурса.     |
|*resourceLocation*     |   Имя региона, где он развернут.      |
|*resourceURI*     |   Универсальный код ресурса (URI) для ресурса.   |
|*alertSummary*     |   Сводка по критическим и предупреждающим оповещениям, а также состояние работоспособности.     |

## <a name="learn-more"></a>Подробнее

Сведения о встроенных средствах мониторинга работоспособности см. в разделе [Мониторинг работоспособности и оповещений в Azure Stack](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Дополнительная информация

[Интеграция решений для обеспечения безопасности](azure-stack-integrate-security.md)
