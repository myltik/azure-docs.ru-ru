<properties
	pageTitle="RBAC: встроенные роли | Microsoft Azure"
	description="В этом разделе описаны встроенные роли для контроля доступа на основе ролей (RBAC)."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/20/2016"
	ms.author="kgremban"/>

#RBAC: встроенные роли

Управление доступом на основе ролей Azure (RBAC) поставляется со следующими встроенными ролями, которые могут быть назначены пользователям, группам и службам. Изменить определения встроенных ролей нельзя. Однако можно создать [настраиваемые роли в Azure RBAC](role-based-access-control-custom-roles.md) в соответствии с потребностями вашей организации.

## Роли в Azure

В таблице ниже содержатся краткие описания встроенных ролей. Щелкните имя роли, чтобы просмотреть подробный список свойств **actions** и **notactions** для роли. Свойство **actions** указывает разрешенные действия с ресурсами Azure. В строках действий можно использовать подстановочные знаки. Свойство **notactions** определения роли указывает действия, которые должны быть исключены из списка разрешенных действий.

>[AZURE.NOTE] Определения ролей Azure постоянно развиваются. Эта статья регулярно обновляется для поддержания актуальности сведений, но вы всегда можете найти последние определения ролей в Azure PowerShell. Используйте командлеты `(get-azurermroledefinition "<role name>").actions` и `(get-azurermroledefinition "<role name>").notactions` по мере необходимости.

| Имя роли | Описание |
| --------- | ----------- |
| [Участник службы управления API](#api-management-service-contributor) | Может управлять службами управления API |
| [Участник компонента Application Insights](#application-insights-component-contributor) | Может управлять компонентами Application Insights |
| [Оператор службы автоматизации](#automation-operator) | Может запускать, останавливать, приостанавливать и возобновлять задания |
| [Участник BizTalk](#biztalk-contributor) | Может управлять службами BizTalk |
| [Участник базы данных ClearDB MySQL](#cleardb-mysql-db-contributor) | Может создавать базы данных ClearDB MySQL |
| [Участник](#contributor) | Может управлять всем, кроме доступа |
| [Участник фабрики данных](#data-factory-contributor) | Может управлять фабриками данных |
| [Пользователь DevTest Labs](#devtest-labs-user) | Может просматривать все, а также подключать, запускать, перезагружать виртуальные машины и завершать их работу |
| [Участник учетной записи DocumentDB](#documentdb-account-contributor) | Может управлять учетными записями DocumentDB |
| [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Может управлять учетными записями интеллектуальных систем |
| [Участник сети](#network-contributor) | Может управлять всеми сетевыми ресурсами |
| [Участник учетной записи New Relic APM](#new-relic-apm-account-contributor) | Может управлять учетными записями и приложениями для управления производительностью приложений New Relic |
| [Владелец](#owner) | Может управлять всем, включая доступ |
| [Читатель](#reader) | Может все просматривать, но не может вносить изменения |
| [Участник кэша Redis](#redis-cache-contributor]) | Может управлять кэшами Redis |
| [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Может управлять коллекциями заданий планировщика |
| [Участник службы поиска](#search-service-contributor) | Может управлять службами поиска |
| [Диспетчер безопасности](#security-manager) | Может управлять компонентами безопасности, политиками безопасности и виртуальными машинами |
| [Участник базы данных SQL](#sql-db-contributor) | Может управлять базами данных SQL, но не их политиками безопасности |
| [Диспетчер безопасности SQL](#sql-security-manager) | Может управлять политиками безопасности для серверов и баз данных SQL |
| [Участник SQL Server](#sql-server-contributor) | Может управлять серверами и базами данных SQL, но не их политиками безопасности |
| [Участник классической учетной записи хранения](#classic-storage-account-contributor) | Может управлять классическими учетными записями хранения |
| [Участник учетной записи хранения](#storage-account-contributor) | Может управлять учетными записями хранения |
| [Администратор доступа пользователей](#user-access-administrator) | Может управлять доступом пользователей к ресурсам Azure |
| [Участник классической виртуальной машины](#classic-virtual-machine-contributor) | Может управлять классическими виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены |
| [Участник виртуальной машины](#virtual-machine-contributor) | Может управлять виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены |
| [Участник классической сети](#classic-network-contributor) | Может управлять классическими виртуальными сетями и зарезервированными IP-адресами |
| [Участник веб-плана](#web-plan-contributor) | Может управлять веб-планами |
| [Участник веб-сайта](#website-contributor) | Может управлять веб-сайтами, но не веб-планами, к которым они подключены |

## Разрешения ролей
В следующих таблицах описываются разрешения, предоставленные каждой роли. Это могут быть свойства **Actions**, которые предоставляют разрешения, и свойства **NotActions**, которые их ограничивают.

### Участник службы управления API
Может управлять службами управления API

| **Действия** | |
| ------- | ------ |
| Microsoft.ApiManagement/Service/* | Создание служб управления API и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение ролей и назначений ролей |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник компонента Application Insights
Может управлять компонентами Application Insights

| **Действия** | |
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.Insights/webtests/* | Создание веб-тестов и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Оператор службы автоматизации
Может запускать, останавливать, приостанавливать и возобновлять задания

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Automation/automationAccounts/jobs/read | Чтение заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновление задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Остановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Чтение потоков заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/write | Запись заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Чтение расписания задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Чтение расписания задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/read | Чтение учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/runbooks/read | Чтение модулей Runbook службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/read | Чтение расписаний учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/write | Запись расписаний учетных записей службы автоматизации |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник BizTalk
Может управлять службами BizTalk

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник ClearDB MySQL DB
Может создавать базы данных ClearDB MySQL

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| successbricks.cleardb/databases/* | Создание баз данных ClearDB MySQL и управление ими |

### Участник
Может управлять всем, кроме доступа

| **Действия** ||
| ------- | ------ |
| * | Создание ресурсов всех типов и управление ими |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | Не может создавать роли и назначения ролей |
| Microsoft.Authorization/*/Delete | Не может удалять роли и назначения ролей |

### Участник фабрики данных
Может управлять фабриками данных

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.DataFactory/dataFactories/* | Управление фабриками данных |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Пользователь DevTest Labs
Может просматривать все, а также подключать, запускать, перезагружать виртуальные машины и завершать их работу

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Compute/availabilitySets/read | Чтение свойств групп доступности |
| Microsoft.Compute/virtualMachines/*/read | Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Отмена распределения виртуальных машин |
| Microsoft.Compute/virtualMachines/read | Чтение свойств виртуальной машины |
| Microsoft.Compute/virtualMachines/restart/action | Перезапуск виртуальных машин |
| Microsoft.Compute/virtualMachines/start/action | Запуск виртуальных машин |
| Microsoft.DevTestLab/*/read | Чтение свойств лаборатории |
| Microsoft.DevTestLab/labs/createEnvironment/action | Создание лабораторной среды |
| Microsoft.DevTestLab/labs/formulas/delete | Удаление формул |
| Microsoft.DevTestLab/labs/formulas/read | Чтение формул |
| Microsoft.DevTestLab/labs/formulas/write | Добавление или изменение формул |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Оценка политик лаборатории |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединение серверных пулов адресов балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединение правила NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/networkInterfaces/*/read | Чтение свойств сетевого интерфейса (например, всех балансировщиков нагрузки, частью которых является сетевой интерфейс) |
| Microsoft.Network/networkInterfaces/join/action | Присоединение виртуальной машины к сетевому интерфейсу |
| Microsoft.Network/networkInterfaces/read | Чтение сетевых интерфейсов |
| Microsoft.Network/networkInterfaces/write | Запись сетевых интерфейсов |
| Microsoft.Network/publicIPAddresses/*/read | Чтение свойств общедоступного IP-адреса |
| Microsoft.Network/publicIPAddresses/join/action | Присоединение общедоступного IP-адреса |
| Microsoft.Network/publicIPAddresses/read | Чтение сетевых общедоступных IP-адресов |
| Microsoft.Network/virtualNetworks/subnets/join/action | Присоединение виртуальной сети |
| Microsoft.Resources/deployments/operations/read | Чтение операций развертывания |
| Microsoft.Resources/deployments/read | Чтение развертываний |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Storage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения |

### Участник учетной записи DocumentDB
Может управлять учетными записями DocumentDB

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей DocumentDB и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записи интеллектуальных систем
Может управлять учетными записями интеллектуальных систем

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник сети
Может управлять всеми сетевыми ресурсами

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Network/* | Создание сетей и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записи New Relic APM
Может управлять учетными записями и приложениями для управления производительностью приложений New Relic

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| NewRelic.APM/accounts/* | Создание учетных записей для управления производительностью приложений New Relic и управление ими |

### Владелец
Может управлять всем, включая доступ

| **Действия** ||
| ------- | ------ |
| * | Создание ресурсов всех типов и управление ими |

### Читатель
Может все просматривать, но не может вносить изменения

| **Действия** ||
| ------- | ------ |
| **/read | Чтение ресурсов всех типов, кроме секретов. |

### Участник кэша Redis
Может управлять кэшами Redis

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник коллекции заданий планировщика
Может управлять коллекциями заданий планировщика

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник службы поиска
Может управлять службами поиска

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Диспетчер безопасности
Может управлять компонентами безопасности, политиками безопасности и виртуальными машинами

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации для виртуальных машин для классических вычислений |
| Microsoft.ClassicCompute/virtualMachines/*/write | Запись конфигурации для виртуальных машин |
| Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник БД SQL
Может управлять базами данных SQL, но не их политиками безопасности

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
| Microsoft.Sql/servers/read | Чтение серверов SQL Server |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита |
| Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности |

### Диспетчер безопасности SQL
Может управлять политиками безопасности для серверов и баз данных SQL

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Чтение авторизации Майкрософт |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Sql/servers/auditingPolicies/* | Создание политик аудита SQL Server и управление ими |
| Microsoft.Sql/servers/auditingSettings/* | Создание параметров аудита SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Создание политик аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/auditingSettings/* | Создание параметров аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Создание политик подключения баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Создание политик маскирования данных баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/read | Чтение баз данных SQL |
| Microsoft.Sql/servers/databases/schemas/read | Чтение схем баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Чтение столбцов таблиц баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Чтение таблиц баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Создание политик оповещения системы безопасности баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/securityMetrics/* | Создание метрик безопасности базы данных SQL и управление ими |
| Microsoft.Sql/servers/read | Чтение серверов SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Создание политик оповещения системы безопасности SQL Server и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник SQL Server
Может управлять серверами и базами данных SQL, но не их политиками безопасности

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение|
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **NotActions** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Не может изменять политики аудита SQL Server |
| Microsoft.Sql/servers/auditingSettings/* | Не может изменять параметры аудита SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения баз данных SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности баз данных SQL Server |
| Microsoft.Sql/servers/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности SQL Server |

### Участник классической учетной записи хранения
Может управлять классическими учетными записями хранения

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записи хранения
Может управлять учетными записями хранения, но не имеет к ним доступа.

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение всех элементов |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* | Управление параметрами диагностики |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Администратор доступа пользователей
Может управлять доступом пользователей к ресурсам Azure

| **Действия** ||
| ------- | ------ |
| */read | Чтение ресурсов всех типов, кроме секретов. | | Microsoft.Authorization/* | Управление авторизацией |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник классической виртуальной машины
Может управлять классическими виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
| Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action | Присоединение групп безопасности сети |
| Microsoft.ClassicNetwork/reservedIps/link/action | Ссылка на зарезервированные IP-адреса |
| Microsoft.ClassicNetwork/reservedIps/read | Чтение зарезервированных IP-адресов |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Присоединение виртуальных сетей |
| Microsoft.ClassicNetwork/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Чтение дисков учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/images/read | Чтение образов учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения |
| Microsoft.ClassicStorage/storageAccounts/read | Чтение классических учетных записей хранения |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник виртуальной машины
Может управлять виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
| Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
| Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.Compute/virtualMachineScaleSets/* | Создание наборов масштабирования виртуальных машин и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Присоединение серверных пулов адресов сетевого шлюза приложений |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединение серверных пулов адресов балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединение пулов NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединение правил NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/read | Чтение балансировщиков нагрузки |
| Microsoft.Network/locations/* | Создание сетевых расположений и управление ими |
| Microsoft.Network/networkInterfaces/* | Создание сетевых интерфейсов и управление ими |
| Microsoft.Network/networkSecurityGroups/join/action | Присоединение групп безопасности сети |
| Microsoft.Network/networkSecurityGroups/read | Чтение групп безопасности сети |
| Microsoft.Network/publicIPAddresses/join/action | Присоединение сетевых общедоступных IP-адресов |
| Microsoft.Network/publicIPAddresses/read | Чтение сетевых общедоступных IP-адресов |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.Network/virtualNetworks/subnets/join/action | Присоединение подсетей виртуальных сетей |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Storage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник классической сети
Может управлять классическими виртуальными сетями и зарезервированными IP-адресами

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник веб-плана
Может управлять веб-планами

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |

### Участник веб-сайта
Может управлять веб-сайтами, но не веб-планами, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
| Microsoft.Web/listSitesAssignedToHostName/read | Чтение сайтов, назначенных имени узла |
| Microsoft.Web/serverFarms/join/action | Присоединение ферм серверов |
| Microsoft.Web/serverFarms/read | Чтение ферм серверов |
| Microsoft.Web/sites/* | Создание веб-сайтов и управление ими |

## См. также
- [Управление доступом на основе ролей в Azure](role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
- [Пользовательские роли в Azure RBAC](role-based-access-control-custom-roles.md). Сведения о создании пользовательских ролей в соответствии с потребностями доступа.
- [Создание отчета по журналу изменения доступа](role-based-access-control-access-change-history-report.md). Отслеживание изменения назначений ролей в RBAC.
- [Устранение неполадок контроля доступа на основе ролей](role-based-access-control-troubleshooting.md). Рекомендации по устранению распространенных проблем.

<!---HONumber=AcomDC_0601_2016-->