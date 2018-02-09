---
title: "Свойства Actions и NotActions в компоненте управления доступом на основе ролей (RBAC) Azure | Документация Майкрософт"
description: "В этом разделе описаны встроенные роли для управления доступом на основе ролей (RBAC). Роли постоянно добавляются, поэтому проверяйте актуальность документации."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 43a958129b3c86f5e7a596b992d793a600c46dfd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Встроенные роли управления доступом на основе ролей в Azure
Управление доступом на основе ролей Azure (RBAC) поставляется со следующими встроенными ролями, которые могут быть назначены пользователям, группам и службам. Изменить определения встроенных ролей нельзя. Однако можно создать [настраиваемые роли в Azure RBAC](role-based-access-control-custom-roles.md) в соответствии с потребностями вашей организации.

## <a name="roles-in-azure"></a>Роли в Azure
В таблице ниже содержатся краткие описания встроенных ролей. Щелкните имя роли, чтобы просмотреть подробный список свойств **actions** и **notactions** для этой роли. Свойство **actions** указывает разрешенные действия с ресурсами Azure. В строках действий можно использовать подстановочные знаки. Свойство **notactions** определения роли указывает действия, которые должны быть исключены из списка разрешенных действий.

Действие определяет, какие типы операций можно выполнять с ресурсом заданного типа. Например: 
- **Запись** позволяет выполнять операции PUT, POST, PATCH и DELETE.
- **Чтение** позволяет выполнять операции GET.

В данной статье рассматриваются только различные роли, которые существуют на сегодняшний день. При назначении роли пользователю можно точнее ограничить разрешенные действия, определив их область. Это удобно, если вы хотите привлечь какого-либо пользователя к работе над веб-сайтом, но только для одной группы ресурсов.

> [!NOTE]
> Определения ролей Azure постоянно развиваются. Эта статья регулярно обновляется для поддержания актуальности сведений, но вы всегда можете найти последние определения ролей в Azure PowerShell. Используйте командлет [Get AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition), чтобы получить список всех текущих ролей. Можно подробнее изучить определенную роль с помощью `(get-azurermroledefinition "<role name>").actions` или `(get-azurermroledefinition "<role name>").notactions`, если это возможно. Выполните командлет [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation), чтобы вывести список операций определенных поставщиков ресурсов Azure.


| Имя роли | ОПИСАНИЕ |
| --- | --- |
| [Участник службы управления API](#api-management-service-contributor) |Может управлять службой управления API и интерфейсами API. |
| [Роль оператора службы управления API](#api-management-service-operator-role) | Может управлять службой управления API, но не интерфейсами API. |
| [Роль читателя данных службы управления API](#api-management-service-reader-role) | Имеет доступ на чтение к службе управления API и интерфейсам API. |
| [Участник компонента Application Insights](#application-insights-component-contributor) |Может управлять компонентами Application Insights |
| [Оператор службы автоматизации](#automation-operator) |Может запускать, останавливать, приостанавливать и возобновлять задания |
| [Участник резервного копирования](#backup-contributor) | Может управлять резервным копированием в хранилище служб восстановления |
| [Оператор резервного копирования](#backup-operator) | Может управлять резервным копированием в хранилище служб восстановления, но не может удалять резервные копии |
| [Читатель резервных копий](#backup-reader) | Может просматривать все службы управления резервными копиями  |
| [Читатель счетов](#billing-reader) | Может просматривать все данные о выставлении счетов  |
| [Участник BizTalk](#biztalk-contributor) |Может управлять службами BizTalk |
| [Участник базы данных ClearDB MySQL](#cleardb-mysql-db-contributor) |Может создавать базы данных ClearDB MySQL |
| [Участник](#contributor) |Может управлять всем, кроме доступа |
| [Участник фабрики данных](#data-factory-contributor) |Вы можете создавать фабрики данных и дочерние ресурсы внутри их, а также управлять ими. |
| [Пользователь DevTest Labs](#devtest-labs-user) |Может просматривать все, а также подключать, запускать, перезагружать виртуальные машины и завершать их работу |
| [Участник зоны DNS](#dns-zone-contributor) |Может управлять зонами и записями DNS. |
| [Участник учетной записи DocumentDB](#documentdb-account-contributor) |Может управлять учетными записями Azure Cosmos DB |
| [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) |Может управлять учетными записями интеллектуальных систем |
| Создатель приложений логики | Может управлять всеми аспектами приложений логики, но не может создавать их. |
| Оператор приложений логики |Может запускать и останавливать рабочие процессы, определенные в приложении логики. |
| [Monitoring Reader](#monitoring-reader) (Читатель данных мониторинга) |Может читать все данные мониторинга. |
| [Monitoring Contributor](#monitoring-contributor) (Участник мониторинга) |Может читать данные мониторинга и изменять параметры мониторинга. |
| [Участник сети](#network-contributor) |Может управлять всеми сетевыми ресурсами |
| [Участник учетной записи New Relic APM](#new-relic-apm-account-contributor) |Может управлять учетными записями и приложениями для управления производительностью приложений New Relic |
| [Владелец](#owner) |Может управлять всем, включая доступ |
| [Читатель](#reader) |Может все просматривать, но не может вносить изменения |
| [Участник кэша Redis](#redis-cache-contributor) |Может управлять кэшами Redis |
| [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) |Может управлять коллекциями заданий планировщика |
| [Участник службы поиска](#search-service-contributor) |Может управлять службами поиска |
| [Администратор безопасности](#security-administrator) | Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации |
| [Диспетчер безопасности](#security-manager) | Может управлять компонентами безопасности, политиками безопасности и виртуальными машинами |
| [Читатель безопасности](#security-reader) | Только в центре безопасности: может просматривать оповещения и рекомендации, просматривать политики безопасности и состояния безопасности, но не может вносить изменения |
| [Участник Site Recovery](#site-recovery-contributor) | Может управлять Site Recovery в хранилище служб восстановления. |
| [Оператор Site Recovery](#site-recovery-operator) | Может управлять операциями отработки отказа и восстановления размещения Site Recovery в хранилище служб восстановления. |
| [Читатель Site Recovery](#site-recovery-reader) | Может просматривать все операции управления Site Recovery.  |
| [Участник базы данных SQL](#sql-db-contributor) |Может управлять базами данных SQL, но не их политиками безопасности |
| [Диспетчер безопасности SQL](#sql-security-manager) |Может управлять политиками безопасности для серверов и баз данных SQL |
| [Участник SQL Server](#sql-server-contributor) |Может управлять серверами и базами данных SQL, но не их политиками безопасности |
| [Участник классической учетной записи хранения](#classic-storage-account-contributor) |Может управлять классическими учетными записями хранения |
| [Участник учетной записи хранения](#storage-account-contributor) |Может управлять учетными записями хранения |
| [Support Request Contributor](#support-request-contributor) (Участник с правом создавать запросы на поддержку) | Может создавать запросы на поддержку и управлять ими. |
| [Администратор доступа пользователей](#user-access-administrator) |Может управлять доступом пользователей к ресурсам Azure |
| [Участник классической виртуальной машины](#classic-virtual-machine-contributor) |Может управлять классическими виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены |
| [Участник виртуальной машины](#virtual-machine-contributor) |Может управлять виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены |
| [Участник классической сети](#classic-network-contributor) |Может управлять классическими виртуальными сетями и зарезервированными IP-адресами |
| [Участник веб-плана](#web-plan-contributor) |Может управлять веб-планами |
| [Участник веб-сайта](#website-contributor) |Может управлять веб-сайтами, но не веб-планами, к которым они подключены |

## <a name="role-permissions"></a>Разрешения ролей
В следующих таблицах описываются разрешения, предоставленные каждой роли. Это могут быть свойства **Actions**, которые предоставляют разрешения, и свойства **NotActions**, которые их ограничивают.

### <a name="api-management-service-contributor"></a>Участник службы управления API
Может управлять службами управления API

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |Создание службы управления API и управление ею |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение ролей и назначений ролей |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="api-management-service-operator-role"></a>Роль оператора службы управления API
Может управлять службами управления API

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Чтение экземпляров службы управления API. |
| Microsoft.ApiManagement/Service/backup/action | Архивация службы управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
| Microsoft.ApiManagement/Service/delete | Удаление экземпляра службы управления API. |
| Microsoft.ApiManagement/Service/managedeployments/action | Изменение номера SKU или единиц. Добавление или удаление региональных развертываний службы управления API. |
| Microsoft.ApiManagement/Service/read | Чтение метаданных для экземпляра службы управления API. |
| Microsoft.ApiManagement/Service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
| Microsoft.ApiManagement/Service/updatehostname/action | Настройка, обновление или удаление имен личных доменов для службы управления API. |
| Microsoft.ApiManagement/Service/write | Создание экземпляра службы управления API. |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение ролей и назначений ролей |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="api-management-service-reader-role"></a>Роль читателя данных службы управления API
Может управлять службами управления API

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Чтение экземпляров службы управления API. |
| Microsoft.ApiManagement/Service/read | Чтение метаданных для экземпляра службы управления API. |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение ролей и назначений ролей |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="application-insights-component-contributor"></a>Участник компонента Application Insights
Может управлять компонентами Application Insights

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.Insights/components/* |Создание компонентов Insights и управление ими |
| Microsoft.Insights/webtests/* |Создание веб-тестов и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="automation-operator"></a>Оператор службы автоматизации
Может запускать, останавливать, приостанавливать и возобновлять задания

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Automation/automationAccounts/jobs/read |Чтение заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Возобновление задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Остановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Чтение потоков заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Приостановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/write |Запись заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Чтение расписания задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Чтение расписания задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/read |Чтение учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/runbooks/read |Чтение модулей Runbook службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/read |Чтение расписаний учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/write |Запись расписаний учетных записей службы автоматизации |
| Microsoft.Insights/components/* |Создание компонентов Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="backup-contributor"></a>Участник резервного копирования
Может управлять всеми операциями управления резервным копированием, за исключением создания хранилища служб восстановления и предоставления доступа другим пользователям.

| **Действия** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Управление результатами операций управления резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Создание контейнеров резервных копий внутри структуры резервного копирования хранилища служб восстановления и управление этими контейнерами |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий резервного копирования в Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Создание метаданных, связанных с управлением резервным копированием, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Создание политик резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Создание элементов, включаемых в резервную копию, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Создание контейнеров с элементами, включаемыми в резервную копию, и управление такими контейнерами |
| Microsoft.RecoveryServices/Vaults/certificates/* | Создание сертификатов, связанных с резервной копией в хранилище служб восстановления, и управление ими |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
| Microsoft.RecoveryServices/Vaults/read | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
| Microsoft.RecoveryServices/Vaults/usages/* | Создание хранилища служб восстановления и управление его использованием |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="backup-operator"></a>Оператор резервного копирования
Может управлять всеми операциями управления резервным копированием, за исключением создания хранилища, удаления резервных копий и предоставления доступа другим пользователям

| **Действия** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Чтение результатов операций управления резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Чтение результатов операций с контейнерами защиты |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Выполнение операции резервного копирования по запросу для элемента, включенного в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Чтение результата операции с элементом, включенным в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | Чтение состояния операции с элементом, включенным в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Чтение элементов, включенных в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Чтение точки восстановления элемента, включенного в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Выполнение операции восстановления с помощью точки восстановления элемента, включенного в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создание элемента, включенного в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Чтение контейнеров, содержащих элемент, включенный в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий резервного копирования в Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Чтение метаданных, связанных с управлением резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Чтение результатов операций, выполняемых с политиками резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Чтение политик резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Чтение элементов, включенных в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Чтение контейнеров, содержащих элементы резервного копирования |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Чтение расширенных сведений, связанных с хранилищем |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Запись расширенных сведений, связанных с хранилищем |
| Microsoft.RecoveryServices/Vaults/read | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Чтение результатов операции, выполняемой для зарегистрированных элементов хранилища |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Чтение зарегистрированных элементов хранилища |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Запись зарегистрированных элементов хранилища |
| Microsoft.RecoveryServices/Vaults/usages/read | Чтение данных об использовании хранилища служб восстановления |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### <a name="backup-reader"></a>Читатель резервных копий
Может отслеживать управление резервным копированием в хранилище служб восстановления

| **Действия** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | Чтение результатов операций управления резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | Чтение результатов операций с контейнерами защиты |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | Чтение результата операции с элементом, включенным в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | Чтение состояния операции с элементом, включенным в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | Чтение элементов, включенных в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | Чтение контейнеров, содержащих элемент, включенный в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | Чтение результатов выполнения заданий резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | Чтение заданий резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспорт заданий резервного копирования в Excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | Чтение метаданных, связанных с управлением резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | Чтение результатов операции управления резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | Чтение результатов операций, выполняемых с политиками резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | Чтение политик резервного копирования |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  Чтение элементов, включенных в резервную копию |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | Чтение контейнеров, содержащих элементы резервного копирования |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Чтение расширенных сведений, связанных с хранилищем |
| Microsoft.RecoveryServices/Vaults/read  | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Чтение результатов операции обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Чтение результатов операции, выполняемой для зарегистрированных элементов хранилища |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Чтение зарегистрированных элементов хранилища |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Чтение данных об использовании хранилища служб восстановления |

### <a name="billing-reader"></a>Читатель счетов
Может просматривать все данные о выставлении счетов

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Billing/*/read |Чтение сведений о выставлении счетов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="biztalk-contributor"></a>Участник BizTalk
Может управлять службами BizTalk

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.BizTalkServices/BizTalk/* |Создание служб BizTalk и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="cleardb-mysql-db-contributor"></a>Участник ClearDB MySQL DB
Может создавать базы данных ClearDB MySQL

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |
| successbricks.cleardb/databases/* |Создание баз данных ClearDB MySQL и управление ими |

### <a name="contributor"></a>участник;
Может управлять всем, кроме доступа

| **Действия** |  |
| --- | --- |
| * |Создание ресурсов всех типов и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |Не может удалять роли и назначения ролей |
| Microsoft.Authorization/*/Write |Не может создавать роли и назначения ролей |

### <a name="data-factory-contributor"></a>Участник фабрики данных
Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.DataFactory/dataFactories/* |Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="devtest-labs-user"></a>Пользователь DevTest Labs
Может просматривать все, а также подключать, запускать, перезагружать виртуальные машины и завершать их работу

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Compute/availabilitySets/read |Чтение свойств групп доступности |
| Microsoft.Compute/virtualMachines/*/read |Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
| Microsoft.Compute/virtualMachines/deallocate/action |Отмена распределения виртуальных машин |
| Microsoft.Compute/virtualMachines/read |Чтение свойств виртуальной машины |
| Microsoft.Compute/virtualMachines/restart/action |Перезапуск виртуальных машин |
| Microsoft.Compute/virtualMachines/start/action |Запуск виртуальных машин |
| Microsoft.DevTestLab/*/read |Чтение свойств лаборатории |
| Microsoft.DevTestLab/labs/createEnvironment/action |Создание лабораторной среды |
| Microsoft.DevTestLab/labs/formulas/delete |Удаление формул |
| Microsoft.DevTestLab/labs/formulas/read |Чтение формул |
| Microsoft.DevTestLab/labs/formulas/write |Добавление или изменение формул |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Оценка политик лаборатории |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Присоединение серверных пулов адресов балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Присоединение правила NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/networkInterfaces/*/read |Чтение свойств сетевого интерфейса (например, всех балансировщиков нагрузки, частью которых является сетевой интерфейс) |
| Microsoft.Network/networkInterfaces/join/action |Присоединение виртуальной машины к сетевому интерфейсу |
| Microsoft.Network/networkInterfaces/read |Чтение сетевых интерфейсов |
| Microsoft.Network/networkInterfaces/write |Запись сетевых интерфейсов |
| Microsoft.Network/publicIPAddresses/*/read |Чтение свойств общедоступного IP-адреса |
| Microsoft.Network/publicIPAddresses/join/action |Присоединение общедоступного IP-адреса |
| Microsoft.Network/publicIPAddresses/read |Чтение сетевых общедоступных IP-адресов |
| Microsoft.Network/virtualNetworks/subnets/join/action |Присоединение виртуальной сети |
| Microsoft.Resources/deployments/operations/read |Чтение операций развертывания |
| Microsoft.Resources/deployments/read |Чтение развертываний |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/listKeys/action |Вывод списка ключей учетной записи хранения |

### <a name="dns-zone-contributor"></a>Участник зоны DNS
Может управлять зонами и записями DNS.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/\* |Создание правил оповещения и управление ими |
| Microsoft.Network/dnsZones/\* |Создание зон и записей DNS и управление ими. |
| Microsoft.ResourceHealth/availabilityStatuses/read |Чтение данных о работоспособности ресурсов. |
| Microsoft.Resources/deployments/\* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/\* |Создание запросов в службу поддержки и управление ими. |

### <a name="documentdb-account-contributor"></a>Участник учетной записи DocumentDB
Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.DocumentDb/databaseAccounts/* |Создание учетных записей Azure Cosmos DB и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="intelligent-systems-account-contributor"></a>Участник учетной записи интеллектуальных систем
Может управлять учетными записями интеллектуальных систем

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.IntelligentSystems/accounts/* |Создание учетных записей интеллектуальных систем и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="monitoring-reader"></a>Роль Monitoring Reader
Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Действия** |  |
| --- | --- |
| */чтение |Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.OperationalInsights/workspaces/search/action |Поиск данных Log Analytics |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="monitoring-contributor"></a>Monitoring Contributor
Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Действия** |  |
| --- | --- |
| */чтение |Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.Insights/AlertRules/* |Чтение, запись и удаление правила генерации оповещений. |
| Microsoft.Insights/components/* |Чтение, запись и удаление компонентов Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* |Чтение, запись и удаление параметров диагностики. |
| Microsoft.Insights/eventtypes/* |Вывод списка событий журнала действий (событий управления) в подписке. Это разрешение применяется для доступа к журналу действий посредством кода или портала. |
| Microsoft.Insights/LogDefinitions/* |Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
| Microsoft.Insights/MetricDefinitions/* |Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
| Microsoft.Insights/Metrics/* |Чтение метрик для ресурса. |
| Microsoft.Insights/Register/Action |Регистрация поставщика Microsoft.Insights. |
| Microsoft.Insights/webtests/* |Чтение, запись и удаление веб-тестов Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Чтение, запись и удаление пакетов решений Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Чтение, запись и удаление сохраненных поисков Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action |Поиск в рабочих областях Log Analytics. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Получение списка ключей для рабочей области Log Analytics. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Чтение, запись и удаление конфигураций подробных данных хранилища Log Analytics. |

### <a name="network-contributor"></a>Участник сети
Может управлять всеми сетевыми ресурсами

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.Network/* |Создание сетей и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="new-relic-apm-account-contributor"></a>Участник учетной записи New Relic APM
Может управлять учетными записями и приложениями для управления производительностью приложений New Relic

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |
| NewRelic.APM/accounts/* |Создание учетных записей для управления производительностью приложений New Relic и управление ими |

### <a name="owner"></a>Владелец.
Может управлять всем, включая доступ

| **Действия** |  |
| --- | --- |
| * |Создание ресурсов всех типов и управление ими |

### <a name="reader"></a>Читатель
Может все просматривать, но не может вносить изменения

| **Действия** |  |
| --- | --- |
| */чтение |Чтение ресурсов всех типов, кроме секретов. |

### <a name="redis-cache-contributor"></a>Участник кэша Redis
Может управлять кэшами Redis

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Cache/redis/* |Создание кэшей Redis и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="scheduler-job-collections-contributor"></a>Участник коллекции заданий планировщика
Может управлять коллекциями заданий планировщика

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Scheduler/jobcollections/* |Создание коллекциями заданий и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="search-service-contributor"></a>Участник службы поиска
Может управлять службами поиска

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Search/searchServices/* |Создание служб поиска и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="security-administrator"></a>Администратор безопасности
Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Authorization/policyAssignments/* | Создание назначений политик и управление ими |
| Microsoft.Authorization/policySetDefinitions/* | Создание наборов политик и управление ими |
| Microsoft.Authorization/policyDefinitions/* | Создание определений политик и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="security-manager"></a>Диспетчер безопасности
Может управлять компонентами безопасности, политиками безопасности и виртуальными машинами

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.ClassicCompute/*/read |Чтение сведений о конфигурации классических виртуальных машин |
| Microsoft.ClassicCompute/virtualMachines/*/write |Запись сведений о конфигурации классических виртуальных машин |
| Microsoft.ClassicNetwork/*/read |Чтение сведений о конфигурации для классической сети |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Security/* |Создание компонентов и политик безопасности и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="security-reader"></a>Читатель безопасности
Только в центре безопасности: может просматривать оповещения и рекомендации, просматривать политики безопасности и состояния безопасности, но не может вносить изменения

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |

### <a name="site-recovery-contributor"></a>Участник Site Recovery
Может управлять всеми операциями управления Site Recovery, за исключением создания хранилища служб восстановления и назначения прав доступа другим пользователям.

| **Действия** | |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.RecoveryServices/Vaults/certificates/write | Обновление сертификата учетных данных хранилища |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Чтение оповещений для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Чтение конфигурации уведомлений хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/read | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Создание или обновление параметров оповещения репликации |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Чтение событий репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Создание структур репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Создание политик репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Создание планов восстановления и управление ими |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Создание конфигурации службы хранилища для хранилища служб восстановления и управление ею |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Чтение сведений о маркере для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/usages/read | Чтение данных об использовании хранилища служб восстановления |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="site-recovery-operator"></a>Оператор Site Recovery
Может выполнять отработку отказа и восстановление размещения, но не может выполнять другие действия управления Site Recovery или назначать доступ другим пользователям.

| **Действия** | |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Чтение расширенных сведений, связанных с хранилищем |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Чтение оповещений для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Чтение конфигурации уведомлений хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/read | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Чтение состояния и результата отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Чтение контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Чтение параметров оповещений репликации. |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Чтение событий репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверка согласованности структуры |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Чтение структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/action | Изменение назначения шлюза репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Чтение сетей структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Чтение сопоставления сетей структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Чтение контейнеров защиты |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Возврат списка всех защищаемых элементов |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ applyRecoveryPoint/action | Применение определенной точки восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ failoverCommit/action | Фиксация отработки отказа элемента после отработки отказа |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ plannedFailover/action | Запуск плановой отработки отказа защищенного элемента |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Возврат списка всех защищенных элементов |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Возврат списка доступных точек восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ repairReplication/action | Исправление репликации защищенного элемента |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Запуск повторной защиты защищенного элемента|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Запуск тестовой отработки отказа защищенного элемента |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ testFailoverCleanup/action | Запуск очистки тестовой отработки отказа |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ unplannedFailover/action | Запуск внеплановой отработки отказа защищенного элемента |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ updateMobilityService/action | Обновление службы Mobility Service |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Чтение сопоставлений контейнера защиты |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Чтение поставщиков служб восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Обновление поставщика служб восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Чтение классификаций хранилища для структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Чтение сопоставлений классификаций хранилища |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Чтение сведений о зарегистрированном сервере vCenter Server |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Чтение политик репликации |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/action | Фиксация отработки отказа плана восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/action | Запуск отработки отказа плана восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Чтение планов восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Запуск повторной защиты плана восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Запуск тестовой отработки отказа плана восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/action | Запуск очистки тестовой отработки отказа плана восстановления |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/action | Запуск внеплановой отработки отказа плана восстановления |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Чтение конфигурации службы хранилища для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Чтение сведений о маркере для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/usages/read | Чтение данных об использовании хранилища служб восстановления |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### <a name="site-recovery-reader"></a>Читатель Site Recovery
Может отслеживать состояние Site Recovery в хранилище служб восстановления и отправлять запросы в службу поддержки.

| **Действия** | |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Чтение расширенных сведений, связанных с хранилищем |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Чтение оповещений для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Чтение конфигурации уведомлений хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/read  | Чтение хранилищ служб восстановления |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Чтение состояния и результата отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Чтение контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Чтение параметров оповещений репликации. |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | Чтение событий репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | Чтение структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | Чтение сетей структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read  | Чтение сопоставления сетей структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read  |  Чтение контейнеров защиты |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read  | Возврат списка всех защищаемых элементов |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read  | Возврат списка всех защищенных элементов |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read  | Возврат списка доступных точек восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read  | Чтение сопоставлений контейнера защиты |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read  | Чтение поставщиков служб восстановления |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read  | Чтение классификаций хранилища для структуры репликации |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read  |  Чтение сопоставлений классификаций хранилища |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  Чтение сведений о зарегистрированном сервере vCenter Server |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  Чтение состояния заданий репликации |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  Чтение политик репликации |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  Чтение планов восстановления |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Чтение конфигурации службы хранилища для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Чтение сведений о маркере для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Чтение данных об использовании хранилища служб восстановления |
| Microsoft.Support/*  |  Создание запросов в службу поддержки и управление ими |

### <a name="sql-db-contributor"></a>Участник БД SQL
Может управлять базами данных SQL, но не их политиками безопасности

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Sql/servers/databases/* |Создание баз данных SQL и управление ими |
| Microsoft.Sql/servers/read |Чтение серверов SQL Server |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Не может изменять политики аудита |
| Microsoft.Sql/servers/databases/auditingSettings/* |Не может изменять параметры аудита |
| Microsoft.Sql/servers/databases/auditRecords/read |Не может читать записи аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Не может изменять политики подключения |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Не может изменять политики маскирования данных |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Не может изменять политики оповещения системы безопасности |
| Microsoft.Sql/servers/databases/securityMetrics/* |Не может изменять метрики безопасности |

### <a name="sql-security-manager"></a>Диспетчер безопасности SQL
Может управлять политиками безопасности для серверов и баз данных SQL

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение авторизации Майкрософт |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Sql/servers/auditingPolicies/* |Создание политик аудита SQL Server и управление ими |
| Microsoft.Sql/servers/auditingSettings/* |Создание параметров аудита SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Создание политик аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingSettings/* |Создание параметров аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditRecords/read |Чтение записей аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Создание политик подключения баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Создание политик маскирования данных баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/read |Чтение баз данных SQL |
| Microsoft.Sql/servers/databases/schemas/read |Чтение схем баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Чтение столбцов таблиц баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read |Чтение таблиц баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Создание политик оповещения системы безопасности баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/securityMetrics/* |Создание метрик безопасности базы данных SQL и управление ими |
| Microsoft.Sql/servers/read |Чтение серверов SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* |Создание политик оповещения системы безопасности SQL Server и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="sql-server-contributor"></a>Участник SQL Server
Может управлять серверами и базами данных SQL, но не их политиками безопасности

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Sql/servers/* |Создание серверов SQL Server и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |Не может изменять политики аудита SQL Server |
| Microsoft.Sql/servers/auditingSettings/* |Не может изменять параметры аудита SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Не может изменять политики аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* |Не может изменять параметры аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read |Не может читать записи аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Не может изменять политики подключения баз данных SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Не может изменять политики маскирования данных баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Не может изменять политики оповещения системы безопасности баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* |Не может изменять метрики безопасности баз данных SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* |Не может изменять политики оповещения системы безопасности SQL Server |

### <a name="classic-storage-account-contributor"></a>Участник классической учетной записи хранения
Может управлять классическими учетными записями хранения

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.ClassicStorage/storageAccounts/* |Создание учетных записей хранения и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="storage-account-contributor"></a>Участник учетной записи хранения
Может управлять учетными записями хранения, но не имеет к ним доступа.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение всех элементов |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* |Управление параметрами диагностики |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/* |Создание учетных записей хранения и управление ими |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="support-request-contributor"></a>Support Request Contributor (Участник с правом создавать запросы на поддержку)
Может создавать запросы в службу поддержки на уровне подписки и управлять ими.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение ролей и назначений ролей |

### <a name="user-access-administrator"></a>Администратор доступа пользователей
Может управлять доступом пользователей к ресурсам Azure

| **Действия** |  |
| --- | --- |
| */чтение |Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.Authorization/* |Управление авторизацией |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="classic-virtual-machine-contributor"></a>Участник классической виртуальной машины
Может управлять классическими виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.ClassicCompute/domainNames/* |Создание доменных имен для классических вычислений и управление ими |
| Microsoft.ClassicCompute/virtualMachines/* |Создание виртуальных машин и управление ими |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Присоединение групп безопасности сети |
| Microsoft.ClassicNetwork/reservedIps/link/action |Ссылка на зарезервированные IP-адреса |
| Microsoft.ClassicNetwork/reservedIps/read |Чтение зарезервированных IP-адресов |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Присоединение виртуальных сетей |
| Microsoft.ClassicNetwork/virtualNetworks/read |Чтение виртуальных сетей |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Чтение дисков учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/images/read |Чтение образов учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Вывод списка ключей учетной записи хранения |
| Microsoft.ClassicStorage/storageAccounts/read |Чтение классических учетных записей хранения |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="virtual-machine-contributor"></a>Участник виртуальной машины
Может управлять виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Compute/availabilitySets/* |Создание групп доступности вычислений и управление ими |
| Microsoft.Compute/locations/* |Создание расположений вычислений и управление ими |
| Microsoft.Compute/virtualMachines/* |Создание виртуальных машин и управление ими |
| Microsoft.Compute/virtualMachineScaleSets/* |Создание наборов масштабирования виртуальных машин и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Присоединение серверных пулов адресов сетевого шлюза приложений |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Присоединение серверных пулов адресов балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Присоединение пулов NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Присоединение правил NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/read |Чтение балансировщиков нагрузки |
| Microsoft.Network/locations/* |Создание сетевых расположений и управление ими |
| Microsoft.Network/networkInterfaces/* |Создание сетевых интерфейсов и управление ими |
| Microsoft.Network/networkSecurityGroups/join/action |Присоединение групп безопасности сети |
| Microsoft.Network/networkSecurityGroups/read |Чтение групп безопасности сети |
| Microsoft.Network/publicIPAddresses/join/action |Присоединение сетевых общедоступных IP-адресов |
| Microsoft.Network/publicIPAddresses/read |Чтение сетевых общедоступных IP-адресов |
| Microsoft.Network/virtualNetworks/read |Чтение виртуальных сетей |
| Microsoft.Network/virtualNetworks/subnets/join/action |Присоединение подсетей виртуальных сетей |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/listKeys/action |Вывод списка ключей учетной записи хранения |
| Microsoft.Storage/storageAccounts/read |Чтение учетных записей хранения |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="classic-network-contributor"></a>Участник классической сети
Может управлять классическими виртуальными сетями и зарезервированными IP-адресами

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.ClassicNetwork/* |Создание классических сетей и управление ими |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |

### <a name="web-plan-contributor"></a>Участник веб-плана
Может управлять веб-планами

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/serverFarms/* |Создание ферм серверов и управление ими |

### <a name="website-contributor"></a>Участник веб-сайта
Может управлять веб-сайтами, но не веб-планами, к которым они подключены

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Авторизация на чтение |
| Microsoft.Insights/alertRules/* |Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/components/* |Создание компонентов Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read |Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* |Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read |Чтение группы ресурсов |
| Microsoft.Support/* |Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/certificates/* |Создание сертификатов веб-сайтов и управление ими |
| Microsoft.Web/listSitesAssignedToHostName/read |Чтение сайтов, назначенных имени узла |
| Microsoft.Web/serverFarms/join/action |Присоединение ферм серверов |
| Microsoft.Web/serverFarms/read |Чтение ферм серверов |
| Microsoft.Web/sites/* |Создание веб-сайтов и управление ими (создание сайта также требует разрешений на запись к связанному плану службы приложений). |

## <a name="see-also"></a>См. также
* [Управление доступом на основе ролей](role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
* [Пользовательские роли в Azure RBAC](role-based-access-control-custom-roles.md). Сведения о создании пользовательских ролей в соответствии с потребностями доступа.
* [Создание отчета по журналу изменений доступа](role-based-access-control-access-change-history-report.md). Отслеживание изменения назначений ролей в RBAC.
* [Устранение неполадок при управлении доступом на основе ролей](role-based-access-control-troubleshooting.md). Рекомендации по устранению распространенных проблем.
* [Разрешения в центре безопасности Azure](../security-center/security-center-permissions.md)
