---
title: Встроенные роли для управления доступом на основе ролей (RBAC) в Azure | Документация Майкрософт
description: В этом разделе описаны встроенные роли для управления доступом на основе ролей (RBAC). Роли постоянно добавляются, поэтому проверяйте актуальность документации.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 4d9df6743d84310b7db70034d1e84dd3591b3c21
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Встроенные роли управления доступом на основе ролей в Azure
Управление доступом на основе ролей Azure (RBAC) поставляется со следующими встроенными ролями, которые могут быть назначены пользователям, группам и службам. Изменить определения встроенных ролей нельзя. Однако можно создать [настраиваемые роли в Azure RBAC](role-based-access-control-custom-roles.md) в соответствии с потребностями вашей организации.

## <a name="built-in-role-descriptions"></a>Описание встроенных ролей
В таблице ниже содержатся краткие описания встроенных ролей. Щелкните имя роли, чтобы просмотреть подробный список свойств **actions** и **notactions** для этой роли. Свойство **actions** указывает разрешенные действия с ресурсами Azure. В строках действий можно использовать подстановочные знаки. Свойство **notactions** определения роли указывает действия, которые должны быть исключены из списка разрешенных действий.

Действие определяет, какие типы операций можно выполнять с ресурсом заданного типа. Например: 
- **Запись** позволяет выполнять операции PUT, POST, PATCH и DELETE.
- **Чтение** позволяет выполнять операции GET.

В данной статье рассматриваются только различные роли, которые существуют на сегодняшний день. При назначении роли пользователю можно точнее ограничить разрешенные действия, определив их область. Это удобно, если вы хотите привлечь какого-либо пользователя к работе над веб-сайтом, но только для одной группы ресурсов.

> [!NOTE]
> Определения ролей Azure постоянно развиваются. Эта статья регулярно обновляется для поддержания актуальности сведений, но вы всегда можете найти последние определения ролей в Azure PowerShell. Используйте командлет [Get AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition), чтобы получить список всех текущих ролей. Можно подробнее изучить определенную роль с помощью `(get-azurermroledefinition "<role name>").actions` или `(get-azurermroledefinition "<role name>").notactions`, если это возможно. Выполните командлет [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation), чтобы вывести список операций определенных поставщиков ресурсов Azure.


| Встроенные роли | ОПИСАНИЕ |
| --- | --- |
| [Владелец](#owner) | Позволяет управлять всем, в том числе доступом к ресурсам. |
| [Участник](#contributor) | Позволяет управлять всем, кроме доступа к ресурсам. |
| [Читатель](#reader) | Может просматривать все элементы, но не вносить изменения. |
| [Участник службы управления API](#api-management-service-contributor) | Может управлять службой и интерфейсами API. |
| [Роль оператора службы управления API](#api-management-service-operator-role) | Может управлять службой, но не интерфейсами API. |
| [Роль читателя данных службы управления API](#api-management-service-reader-role) | Доступ к службе и интерфейсам API в режиме "только для чтения". |
| [Участник компонента Application Insights](#application-insights-component-contributor) | Может управлять компонентами Application Insights |
| [Отладчик моментальных снимков Application Insights](#application-insights-snapshot-debugger) | Предоставляет пользователю разрешение на использование функций отладчика моментальных снимков Application Insights. |
| [Оператор задания автоматизации](#automation-job-operator) | Создание заданий и управление ими с помощью модулей Runbook службы автоматизации. |
| [Оператор службы автоматизации](#automation-operator) | Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания. |
| [Оператор Runbook автоматизации](#automation-runbook-operator) | Чтение свойств Runbook, позволяющее создавать задания Runbook. |
| [Владелец регистрации Azure Stack](#azure-stack-registration-owner) | Позволяет управлять регистрациями Azure Stack. |
| [Участник резервного копирования](#backup-contributor) | Позволяет управлять службой архивации, но не разрешает создавать хранилища и предоставлять доступ другим пользователям |
| [Оператор резервного копирования](#backup-operator) | Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям |
| [Читатель резервных копий](#backup-reader) | Может просматривать службы резервного копирования, но не может вносить изменения. |
| [Читатель счетов](#billing-reader) | Разрешает читать данные выставления счетов. |
| [Участник BizTalk](#biztalk-contributor) | Позволяет управлять службами BizTalk, но не доступом к ним. |
| [Участник конечных точек CDN](#cdn-endpoint-contributor) | Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям. |
| [Читатель конечной точки CDN](#cdn-endpoint-reader) | Может просматривать конечные точки CDN, но не может вносить изменения. |
| [Участник профиля CDN](#cdn-profile-contributor) | Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям. |
| [Читатель данных профиля CDN](#cdn-profile-reader) | Может просматривать профили CDN и их конечные точки, но не может вносить изменения. |
| [Участник классической сети](#classic-network-contributor) | Позволяет управлять классическими сетями, но не доступом к ним. |
| [Участник классической учетной записи хранения](#classic-storage-account-contributor) | Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним. |
| [Роль службы оператора ключей классических учетных записей хранения](#classic-storage-account-key-operator-service-role) | Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения. |
| [Участник классической виртуальной машины](#classic-virtual-machine-contributor) | Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены. |
| [Участник базы данных ClearDB MySQL](#cleardb-mysql-db-contributor) | Позволяет управлять базами данных ClearDB MySQL, но не доступом к ним. |
| [Роль читателя учетных записей Cosmos DB](#cosmos-db-account-reader-role) | Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor). |
| [Участник фабрики данных](#data-factory-contributor) | Создание фабрик данных и управление ими, а также их дочерними ресурсами. |
| [Разработчик Data Lake Analytics](#data-lake-analytics-developer) | Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics. |
| [Пользователь DevTest Labs](#devtest-labs-user) | Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs. |
| [Участник зоны DNS](#dns-zone-contributor) | Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны. |
| [Участник учетной записи DocumentDB](#documentdb-account-contributor) | Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB. |
| [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним. |
| [Участник Key Vault](#key-vault-contributor) | Позволяет управлять хранилищами ключей, но не доступом к ним. |
| [Создатель лаборатории](#lab-creator) | Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure. |
| [Участник Log Analytics](#log-analytics-contributor) | Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure. |
| [Читатель Log Analytics](#log-analytics-reader) | Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure. |
| [Создатель приложений логики](#logic-app-contributor) | Позволяет управлять приложениями логики, но не доступом к ним. |
| [Оператор приложений логики](#logic-app-operator) | Позволяет читать, включать и отключать приложения логики. |
| [Участник управляемого удостоверения](#managed-identity-contributor) | Создание, чтение, обновление и удаление пользовательских удостоверений. |
| [Оператор управляемого удостоверения](#managed-identity-operator) | Чтение и назначение пользовательских удостоверений. |
| [Monitoring Contributor](#monitoring-contributor) (Участник мониторинга) | Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitoring Reader](#monitoring-reader) (Читатель данных мониторинга) | Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Участник сети](#network-contributor) | Позволяет управлять сетями, но не доступом к ним. |
| [Участник учетной записи New Relic APM](#new-relic-apm-account-contributor) | Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним. |
| [Участник кэша Redis](#redis-cache-contributor) | Позволяет управлять кэшем Redis, но не доступом к нему. |
| [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. |
| [Участник службы поиска](#search-service-contributor) | Позволяет управлять службами поиска, но не доступом к ним. |
| [Администратор безопасности](#security-admin) | Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации |
| [Диспетчер безопасности](#security-manager) | Позволяет управлять компонентами и политиками безопасности, а также виртуальными машинами. |
| [Читатель безопасности](#security-reader) | Только в центре безопасности: может просматривать оповещения и рекомендации, просматривать политики безопасности и состояния безопасности, но не может вносить изменения |
| [Участник Site Recovery](#site-recovery-contributor) | Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей. |
| [Оператор Site Recovery](#site-recovery-operator) | Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery. |
| [Читатель Site Recovery](#site-recovery-reader) | Позволяет просматривать состояние Site Recovery без выполнения других операций управления. |
| [Участник базы данных SQL](#sql-db-contributor) | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server. |
| [Диспетчер безопасности SQL](#sql-security-manager) | Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним. |
| [Участник SQL Server](#sql-server-contributor) | Позволяет управлять серверами SQL Server и базами данных SQL, но не доступом к ним и их политиками безопасности. |
| [Участник учетной записи хранения](#storage-account-contributor) | Позволяет управлять учетными записями хранения, но не доступом к ним. |
| [Роль службы оператора ключей учетных записей хранения](#storage-account-key-operator-service-role) | Операторы ключей учетных записей хранения могут перечислять и повторно создавать ключи в учетных записях хранения. |
| [Support Request Contributor](#support-request-contributor) (Участник с правом создавать запросы на поддержку) | Позволяет создавать запросы в службу поддержки и управлять ими. |
| [Участник диспетчера трафика](#traffic-manager-contributor) | Позволяет управлять профилями диспетчера трафика, но не доступом к ним. |
| [Администратор доступа пользователей](#user-access-administrator) | Позволяет управлять доступом пользователей к ресурсам Azure. |
| [Администратор виртуальной машины](#virtual-machine-administrator-login) | Пользователи с этой ролью могут входить на виртуальную машину с правами администратора Windows или привилегированного пользователя Linux. |
| [Участник виртуальной машины](#virtual-machine-contributor) | Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены. |
| [Пользователь виртуальной машины](#virtual-machine-user-login) | Пользователи с этой ролью могут входить на виртуальную машину как обычные пользователи. |
| [Участник веб-плана](#web-plan-contributor) | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним. |
| [Участник веб-сайта](#website-contributor) | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним. |

В следующих таблицах описываются разрешения, предоставленные каждой роли. Это могут быть свойства **Actions**, которые предоставляют разрешения, и свойства **NotActions**, которые их ограничивают.

## <a name="owner"></a>Владелец.
Позволяет управлять всем, в том числе доступом к ресурсам.

| **Действия** |  |
| --- | --- |
| * | Создание ресурсов всех типов и управление ими |

## <a name="contributor"></a>участник;
Позволяет управлять всем, кроме доступа к ресурсам.

| **Действия** |  |
| --- | --- |
| * | Создание ресурсов всех типов и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Не может удалять роли и назначения ролей. |
| Microsoft.Authorization/*/Write | Не может создавать роли и назначения ролей. |
| Microsoft.Authorization/elevateAccess/Action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |

## <a name="reader"></a>Читатель
Может просматривать все элементы, но не вносить изменения.

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |

## <a name="api-management-service-contributor"></a>Участник службы управления API
Может управлять службой и интерфейсами API.

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Создание службы управления API и управление ею |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="api-management-service-operator-role"></a>Роль оператора службы управления API
Может управлять службой, но не интерфейсами API.

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
| Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
| Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
| Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
| Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
| Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
| Microsoft.ApiManagement/service/updatecertificate/action | Передает SSL-сертификата для службы управления API. |
| Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
| Microsoft.ApiManagement/service/write | Создание экземпляра службы управления API. |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Возвращает список ключей пользователя. |

## <a name="api-management-service-reader-role"></a>Роль читателя данных службы управления API
Доступ к службе и интерфейсам API в режиме "только для чтения".

| **Действия** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
| Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Возвращает список ключей пользователя. |

## <a name="application-insights-component-contributor"></a>Участник компонента Application Insights
Может управлять компонентами Application Insights

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.Insights/webtests/* | Создание веб-тестов и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="application-insights-snapshot-debugger"></a>Отладчик моментальных снимков Application Insights
Предоставляет пользователю разрешение на использование функций отладчика моментальных снимков Application Insights.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-job-operator"></a>Оператор заданий службы автоматизации
Создание заданий и управление ими с помощью модулей Runbook службы автоматизации.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-operator"></a>Оператор службы автоматизации
Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Возвращает расписание заданий службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Создает расписание заданий службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Получает рабочую область, связанную с учетной записью службы автоматизации. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
| Microsoft.Automation/automationAccounts/read | Возвращает учетную запись службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/schedules/read | Возвращает ресурс расписания службы автоматизации Azure. |
| Microsoft.Automation/automationAccounts/schedules/write | Создает или обновляет ресурс расписания службы автоматизации Azure. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-runbook-operator"></a>Оператор Runbook службы автоматизации
Чтение свойств Runbook, позволяющее создавать задания Runbook.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="azure-stack-registration-owner"></a>Владелец регистрации Azure Stack
Позволяет управлять регистрациями Azure Stack.

| **Действия** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Возвращает подробные сведения о продукте из Azure Stack Marketplace. |
| Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
| Microsoft.AzureStack/registrations/read | Получает свойства регистрации Azure Stack. |

## <a name="backup-contributor"></a>Участник резервного копирования
Позволяет управлять службой архивации, но не разрешает создавать хранилища и предоставлять доступ другим пользователям

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Управление результатами операций управления резервным копированием |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Создание контейнеров резервных копий внутри структуры резервного копирования хранилища служб восстановления и управление этими контейнерами |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Создание метаданных, связанных с управлением резервным копированием, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Создание политик резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Создание элементов, включаемых в резервную копию, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Создание контейнеров с элементами, включаемыми в резервную копию, и управление такими контейнерами |
| Microsoft.RecoveryServices/Vaults/certificates/* | Создание сертификатов, связанных с резервной копией в хранилище служб восстановления, и управление ими |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
| Microsoft.RecoveryServices/Vaults/usages/* | Создание хранилища служб восстановления и управление его использованием |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="backup-operator"></a>Оператор резервного копирования
Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/action | Архивирует защищенный элемент. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/restore/action | Восстанавливает точки восстановления для защищенных элементов. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Создает элемент, защищенный службой архивации. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Отменяет задание. |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Возвращает метаданные управления архивацией для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Возвращает список всех защищаемых элементов. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления. |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="backup-reader"></a>Читатель резервных копий
Может просматривать службы резервного копирования, но не может вносить изменения.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Возвращает метаданные управления архивацией для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возвращает результат операции архивации для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |

## <a name="billing-reader"></a>Читатель счетов
Разрешает читать данные выставления счетов.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Billing/*/read | Чтение сведений о выставлении счетов |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="biztalk-contributor"></a>Участник BizTalk
Позволяет управлять службами BizTalk, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-endpoint-contributor"></a>Участник конечных точек CDN
Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-endpoint-reader"></a>Читатель конечной точки CDN
Может просматривать конечные точки CDN, но не может вносить изменения.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-profile-contributor"></a>Участник профиля CDN
Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-profile-reader"></a>Читатель данных профиля CDN
Может просматривать профили CDN и их конечные точки, но не может вносить изменения.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-network-contributor"></a>Участник классической сети
Позволяет управлять классическими сетями, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-storage-account-contributor"></a>Участник классической учетной записи хранения
Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-storage-account-key-operator-service-role"></a>Роль службы оператора ключей классических учетных записей хранения
Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения.

| **Действия** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Отображает ключи доступа для учетных записей хранения. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |

## <a name="classic-virtual-machine-contributor"></a>Участник классической виртуальной машины
Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
| Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | Привязывает зарезервированный IP-адрес. |
| Microsoft.ClassicNetwork/reservedIps/read | Возвращает зарезервированные IP-адреса. |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
| Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
| Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
| Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cleardb-mysql-db-contributor"></a>Участник ClearDB MySQL DB
Позволяет управлять базами данных ClearDB MySQL, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| successbricks.cleardb/databases/* | Создание баз данных ClearDB MySQL и управление ими |

## <a name="cosmos-db-account-reader-role"></a>Роль читателя учетных записей Cosmos DB
Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor).

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей. Может считывать данные о разрешениях, предоставленных каждому пользователю |
| Microsoft.DocumentDB/*/read | Чтение любой коллекции |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
| Microsoft.Insights/MetricDefinitions/read | Считывает определения метрик. |
| Microsoft.Insights/Metrics/read | Считывает метрики. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="data-factory-contributor"></a>Участник фабрики данных
Создание фабрик данных и управление ими, а также их дочерними ресурсами.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.DataFactory/dataFactories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="data-lake-analytics-developer"></a>Разработчик Data Lake Analytics
Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Удаляет учетную запись Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставляет разрешения на отмену заданий, отправленных другими пользователями. |
| Microsoft.DataLakeAnalytics/accounts/Write | Создание или обновление учетной записи Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Удаление связи учетной записи хранения с учетной записью Data Lake Analytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Создает или обновляет правило брандмауэра. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Удаляет правило брандмауэра. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Создание или обновление политики вычислений. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Удаление политики вычислений. |

## <a name="devtest-labs-user"></a>Пользователь DevTest Labs
Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
| Microsoft.Compute/virtualMachines/*/read | Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
| Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
| Microsoft.Compute/virtualMachines/restart/action | Перезапускает виртуальную машину. |
| Microsoft.Compute/virtualMachines/start/action | Запускает виртуальную машину. |
| Microsoft.DevTestLab/*/read | Чтение свойств лаборатории |
| Microsoft.DevTestLab/labs/createEnvironment/action | Создает виртуальные машины в лаборатории. |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории. |
| Microsoft.DevTestLab/labs/formulas/delete | Удаляет формулы. |
| Microsoft.DevTestLab/labs/formulas/read | Считывает формулы. |
| Microsoft.DevTestLab/labs/formulas/write | Добавляет или изменяет формулы. |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Оценивает политику лаборатории. |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Присваивает владение существующей виртуальной машиной. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
| Microsoft.Network/networkInterfaces/*/read | Чтение свойств сетевого интерфейса (например, всех балансировщиков нагрузки, частью которых является сетевой интерфейс) |
| Microsoft.Network/networkInterfaces/join/action | Подключает виртуальную машину к сетевому интерфейсу. |
| Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
| Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
| Microsoft.Network/publicIPAddresses/*/read | Чтение свойств общедоступного IP-адреса |
| Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. |
| Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. |
| Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
| Microsoft.Resources/deployments/read | Возвращает развернутые службы или выводит их список. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Выводит список доступных размеров для обновления виртуальной машины. |

## <a name="dns-zone-contributor"></a>Участник зоны DNS
Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/dnsZones/* | Создание зон и записей DNS и управление ими. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими. |

## <a name="documentdb-account-contributor"></a>Участник учетной записи DocumentDB
Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей Azure Cosmos DB и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="intelligent-systems-account-contributor"></a>Участник учетной записи интеллектуальных систем
Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="key-vault-contributor"></a>Участник Key Vault
Позволяет управлять хранилищами ключей, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Создатель лаборатории
Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.ManagedLab/labAccounts/createLab/action | Создает лабораторию в учетной записи лаборатории. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="log-analytics-contributor"></a>участник Log Analytics.
Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure.

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="log-analytics-reader"></a>читатель Log Analytics;
Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure.

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
| Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |

## <a name="logic-app-contributor"></a>Создатель приложений логики
Позволяет управлять приложениями логики, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
| Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
| Microsoft.Insights/logdefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
| Microsoft.Insights/metricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
| Microsoft.Logic/* | Управляет ресурсами Logic Apps. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/connectionGateways/* | Создает шлюз подключения и управляет им. |
| Microsoft.Web/connections/* | Создает подключение и управляет им. |
| Microsoft.Web/customApis/* | Создает настраиваемый API и управляет им. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
| Microsoft.Web/sites/functions/listSecrets/action | Выводит список секретов для функций веб-приложений. |

## <a name="logic-app-operator"></a>Оператор приложений логики
Позволяет читать, включать и отключать приложения логики.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/*/read | Считывание правил оповещений Insights. |
| Microsoft.Insights/diagnosticSettings/*/read | Получает параметры диагностики для Logic Apps. |
| Microsoft.Insights/metricDefinitions/*/read | Получает доступные метрики для Logic Apps. |
| Microsoft.Logic/*/read | Считывает ресурсы Logic Apps. |
| Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
| Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
| Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
| Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
| Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/connectionGateways/*/read | Считывает шлюзы подключения. |
| Microsoft.Web/connections/*/read | Считывает подключения. |
| Microsoft.Web/customApis/*/read | Считывает настраиваемый API. |
| Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |

## <a name="managed-identity-contributor"></a>Участник управляемого удостоверения
Создание, чтение, обновление и удаление пользовательских удостоверений.

| **Действия** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="managed-identity-operator"></a>Оператор управляемого удостоверения
Чтение и назначение пользовательских удостоверений.

| **Действия** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="monitoring-contributor"></a>Monitoring Contributor
Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Чтение, запись и удаление правила генерации оповещений. |
| Microsoft.Insights/components/* | Чтение, запись и удаление компонентов Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Чтение, запись и удаление параметров диагностики. |
| Microsoft.Insights/eventtypes/* | Вывод списка событий журнала действий (событий управления) в подписке. Это разрешение применяется для доступа к журналу действий посредством кода или портала. |
| Microsoft.Insights/LogDefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
| Microsoft.Insights/MetricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
| Microsoft.Insights/Metrics/* | Чтение метрик для ресурса. |
| Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
| Microsoft.Insights/webtests/* | Чтение, запись и удаление веб-тестов Application Insights. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Чтение, запись и удаление пакетов решений Log Analytics. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Чтение, запись и удаление сохраненных поисков Log Analytics. |
| Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Чтение, запись и удаление конфигураций подробных данных хранилища Log Analytics. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Роль Monitoring Reader
Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="network-contributor"></a>Участник сети
Позволяет управлять сетями, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/* | Создание сетей и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="new-relic-apm-account-contributor"></a>Участник учетной записи New Relic APM
Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Участник кэша Redis
Позволяет управлять кэшем Redis, но не доступом к нему.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="scheduler-job-collections-contributor"></a>Участник коллекции заданий планировщика
Позволяет управлять коллекциями заданий планировщика, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="search-service-contributor"></a>Участник службы поиска
Позволяет управлять службами поиска, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-admin"></a>администратор безопасности;
Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Authorization/policyAssignments/* | Создание назначений политик и управление ими |
| Microsoft.Authorization/policyDefinitions/* | Создание определений политик и управление ими |
| Microsoft.Authorization/policySetDefinitions/* | Создание наборов политик и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
| Microsoft.Security/locations/alerts/dismiss/action | Отклонение оповещения системы безопасности. |
| Microsoft.Security/locations/tasks/dismiss/action | Закрывает рекомендацию по безопасности. |
| Microsoft.Security/policies/write | Обновляет политику безопасности. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-manager"></a>Диспетчер безопасности
Позволяет управлять компонентами и политиками безопасности, а также виртуальными машинами.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации классических виртуальных машин |
| Microsoft.ClassicCompute/virtualMachines/*/write | Запись сведений о конфигурации классических виртуальных машин |
| Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-reader"></a>Чтение данных безопасности
Только в центре безопасности: может просматривать оповещения и рекомендации, просматривать политики безопасности и состояния безопасности, но не может вносить изменения

| **Действия** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Security/*/read | Чтение компонентов и политик безопасности |

## <a name="site-recovery-contributor"></a>Участник Site Recovery
Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Создание или обновление параметров оповещения репликации |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Создание структур репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Создание политик репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Создание планов восстановления и управление ими |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Создание конфигурации службы хранилища для хранилища служб восстановления и управление ею |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="site-recovery-operator"></a>Оператор Site Recovery
Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывает параметры оповещений. |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывает структуры. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывает сети. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Считывает сетевые сопоставления. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Считывает контейнеры защиты. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Выполняет плановую отработку отказа. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Считывает защищенные элементы. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестирование отработки отказа |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Отработка отказа |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновляет службу Mobility Service. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывает сопоставления контейнера защиты. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Считывает поставщики служб восстановления. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Обновляет поставщик. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Считывает классификации хранилища. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывает сопоставления классификаций хранилища. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Считывает задания. |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывает политики. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Выполняет отработку отказа с фиксацией для плана восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Выполняет плановую отработку отказа для плана восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывает планы восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Повторно защищает план восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Тестирует отработку отказа для плана восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Тестирует очистку отработки отказа для плана восстановления. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Выполняет отработку отказа для плана восстановления. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="site-recovery-reader"></a>Читатель Site Recovery
Позволяет просматривать состояние Site Recovery без выполнения других операций управления.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывает параметры оповещений. |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывает структуры. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывает сети. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | Считывает сетевые сопоставления. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Считывает контейнеры защиты. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Считывает защищенные элементы. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывает сопоставления контейнера защиты. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Считывает поставщики служб восстановления. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Считывает классификации хранилища. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывает сопоставления классификаций хранилища. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Считывает задания. |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Считывает задания. |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывает политики. |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывает планы восстановления. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="sql-db-contributor"></a>Участник БД SQL
Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
| Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита |
| Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита |
| Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Диспетчер безопасности SQL
Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение авторизации Майкрософт |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Sql/servers/auditingPolicies/* | Создание политик аудита SQL Server и управление ими |
| Microsoft.Sql/servers/auditingSettings/* | Создание параметров аудита SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Создание политик аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingSettings/* | Создание параметров аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditRecords/read | Чтение записей аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Создание политик подключения баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Создание политик маскирования данных баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/read | Возвращение списка баз данных или возвращение свойств указанной базы данных. |
| Microsoft.Sql/servers/databases/schemas/read | Получение списка схем базы данных. |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Извлекает список столбцов таблицы. |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Получение списка таблиц базы данных. |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Создание политик оповещения системы безопасности баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/securityMetrics/* | Создание метрик безопасности базы данных SQL и управление ими |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
| Microsoft.Sql/servers/securityAlertPolicies/* | Создание политик оповещения системы безопасности SQL Server и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="sql-server-contributor"></a>Участник SQL Server
Позволяет управлять серверами SQL Server и базами данных SQL, но не доступом к ним и их политиками безопасности.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Не может изменять политики аудита SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | Не может изменять параметры аудита SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/auditRecords/read | Не может читать записи аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения баз данных SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных баз данных SQL Server |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности баз данных SQL Server |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности SQL Server |

## <a name="storage-account-contributor"></a>Участник учетной записи хранения
Позволяет управлять учетными записями хранения, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение всех элементов |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* | Управление параметрами диагностики |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="storage-account-key-operator-service-role"></a>Роль службы оператора ключей учетных записей хранения
Операторы ключей учетных записей хранения могут перечислять и повторно создавать ключи в учетных записях хранения.

| **Действия** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |

## <a name="support-request-contributor"></a>Support Request Contributor (Участник с правом создавать запросы на поддержку)
Позволяет создавать запросы в службу поддержки и управлять ими.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="traffic-manager-contributor"></a>Участник диспетчера трафика
Позволяет управлять профилями диспетчера трафика, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="user-access-administrator"></a>Администратор доступа пользователей
Позволяет управлять доступом пользователей к ресурсам Azure.

| **Действия** |  |
| --- | --- |
| */чтение | Чтение ресурсов всех типов, кроме секретов. |
| Microsoft.Authorization/* | Управление авторизацией |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="virtual-machine-administrator-login"></a>Администратор виртуальной машины
-   Пользователи с этой ролью могут входить на виртуальную машину с правами администратора Windows или привилегированного пользователя Linux.

| **Действия** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Участник виртуальной машины
Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
| Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
| Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.Compute/virtualMachineScaleSets/* | Создание наборов масштабирования виртуальных машин и управление ими |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов шлюза приложений. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки. |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяет пул входящих подключений NAT подсистемы балансировки нагрузки. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
| Microsoft.Network/loadBalancers/probes/join/action | Позволяет использовать зонды подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. |
| Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
| Microsoft.Network/locations/* | Создание сетевых расположений и управление ими |
| Microsoft.Network/networkInterfaces/* | Создание сетевых интерфейсов и управление ими |
| Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. |
| Microsoft.Network/networkSecurityGroups/read | Возвращает определение группы безопасности сети. |
| Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. |
| Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
| Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/*/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Создает элемент, защищенный службой архивации. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
| Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
| Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
| Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
| Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="virtual-machine-user-login"></a>Пользователь виртуальной машины
Пользователи с этой ролью могут входить на виртуальную машину как обычные пользователи.

| **Действия** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Участник веб-плана
Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |

## <a name="website-contributor"></a>Участник веб-сайта
Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним.

| **Действия** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
| Microsoft.Web/listSitesAssignedToHostName/read | Возвращает имена сайтов, назначенные имени узла. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
| Microsoft.Web/sites/* | Создание веб-сайтов и управление ими (создание сайта также требует разрешений на запись к связанному плану службы приложений). |

## <a name="see-also"></a>См. также
* [Управление доступом на основе ролей](role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
* [Пользовательские роли в Azure RBAC](role-based-access-control-custom-roles.md). Сведения о создании пользовательских ролей в соответствии с потребностями доступа.
* [Создание отчета по журналу изменений доступа](role-based-access-control-access-change-history-report.md). Отслеживание изменения назначений ролей в RBAC.
* [Устранение неполадок при управлении доступом на основе ролей](role-based-access-control-troubleshooting.md). Рекомендации по устранению распространенных проблем.
* [Разрешения в центре безопасности Azure](../security-center/security-center-permissions.md)
