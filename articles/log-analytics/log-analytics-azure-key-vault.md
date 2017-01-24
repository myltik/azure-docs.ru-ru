---
title: "Решение хранилища ключей Azure в Log Analytics | Документация Майкрософт"
description: "Решение хранилища ключей Azure в Log Analytics позволяет просматривать журналы хранилища ключей Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: 5aae95a78e604acc5f0189d5df62620d65d29857
ms.openlocfilehash: bb9ece6382c22f4c1b7905048647434fc7cee98a


---
# <a name="azure-key-vault-analytics-preview-solution-in-log-analytics"></a>Решение для анализа хранилища ключей Azure (предварительная версия) в Log Analytics

Решение хранилища ключей Azure в Log Analytics позволяет просматривать журналы AuditEvent хранилища ключей Azure.

> [!NOTE]
> Решение для анализа хранилища ключей Azure доступно в [предварительной версии](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Чтобы использовать решение, необходимо включить ведение журнала диагностики хранилища ключей Azure и направить диагностику в рабочую область Log Analytics. Необязательно записывать журналы в хранилище BLOB-объектов Azure.

## <a name="install-and-configure-the-solution"></a>Установка и настройка решения
Установите и настройте решение хранилища ключей Azure, выполнив следующие указания:

1. Используйте `Set-AzureRmDiagnosticSetting`, чтобы включить ведение журнала диагностики для ресурсов хранилища ключей, для которых требуется выполнять мониторинг: 
2. Включите решение хранилища ключей Azure, как описано в статье [Добавление решений Log Analytics из каталога решений](log-analytics-add-solutions.md). 

Следующий сценарий PowerShell приведен в качестве примера того, как включить ведение журналов диагностики для хранилища ключей:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```
 
 

## <a name="review-azure-key-vault-data-collection-details"></a>Просмотр сведений о сборе данных хранилища ключей Azure
Решение хранилища ключей Azure собирает журналы диагностики напрямую из хранилища ключей.
Необязательно записывать журналы в хранилище BLOB-объектов Azure. Для сбора данных агенты не требуются.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для хранилища ключей Azure.

| Платформа | Direct Agent | Агент Systems Center Operations Manager | Таблицы Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Таблицы Azure |![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Да](./media/log-analytics-azure-keyvault/oms-bullet-green.png) |![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png) |![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png) | при получении |

## <a name="use-azure-key-vault"></a>Использование хранилища ключей Azure
После установки решения можно просмотреть сводку состояний запросов за определенный период для отслеживаемых хранилищ ключей на плитке **Хранилище ключей Azure** на странице **Обзор** в службе Log Analytics.

![изображение плитки "Хранилище ключей Azure"](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Выбрав плитку **Обзор**, можно просмотреть сводные данные журналов и подробные сведения по следующим категориям:

* количество всех операций хранилища ключей за определенный период;
* количество неудавшихся операций за определенный период;
* среднее время задержки для каждой операции;
* качество обслуживания для операций с количеством операций, выполнявшихся более 1000 мс, и списком этих операций.

![изображение панели мониторинга хранилища ключей Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![изображение панели мониторинга хранилища ключей Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Просмотр сведений об операциях
1. На странице **Обзор** щелкните плитку **Хранилище ключей Azure**.
2. На панели мониторинга **Хранилище ключей Azure** просмотрите сводные данные в одной из колонок, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.
   
    На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.

## <a name="log-analytics-records"></a>Записи Log Analytics
Решение хранилища ключей Azure анализирует записи типа **KeyVaults**, полученные из [журналов AuditEvent](../key-vault/key-vault-logging.md) системы диагностики Azure.  Свойства этих записей приведены в таблице ниже.  

| Свойство | Описание |
|:--- |:--- |
| Тип |*AzureDiagnostics* |
| SourceSystem |*Таблицы Azure* |
| CallerIpAddress |IP-адрес клиента, отправившего запрос. |
| Категория | *AuditEvent* |
| CorrelationId |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| DurationMs |Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| HttpStatusCode_d |Код состояния HTTP, возвращаемый запросом (например, *200*) |
| id_s |Уникальный идентификатор запроса. |
| identity_claim_appid_g | GUID для идентификатора приложения |
| OperationName |Имя операции, как описано в статье [Ведение журнала хранилища ключей Azure](../key-vault/key-vault-logging.md) |
| OperationVersion |Запрошенная клиентом версия REST API (например, *2015-06-01*) |
| requestUri_s |URI запроса |
| Ресурс |Имя хранилища ключей. |
| ResourceGroup |Группа ресурсов хранилища ключей. |
| ResourceId |Идентификатор ресурса диспетчера ресурсов Azure. Для журналов хранилища ключей это идентификатор ресурса хранилища ключей. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Код состояния HTTP (например, *ОК*) |
| ResultType |Результат запроса REST API (например, *Успешно*) |
| SubscriptionId |Идентификатор подписки Azure, которая содержит хранилище ключей. |

## <a name="next-steps"></a>Дальнейшие действия
* Используйте [поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных о хранилище ключей Azure.




<!--HONumber=Dec16_HO1-->


