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
	ms.date="05/10/2016"
	ms.author="kgremban"/>

#RBAC: встроенные роли

## Встроенные роли

Управление доступом на основе ролей Azure (RBAC) поставляется со следующими встроенными ролями, которые могут быть назначены пользователям, группам и службам. Изменить определения встроенных ролей нельзя. Однако можно создать [настраиваемые роли в Azure RBAC](role-based-access-control-custom-roles.md) в соответствии с потребностями вашей организации.

В таблице ниже содержатся краткие описания встроенных ролей. Щелкните имя роли, чтобы просмотреть подробный список свойств **actions** и **not actions** для роли. Свойство **actions** указывает разрешенные действия с ресурсами Azure. В строках действий можно использовать подстановочные знаки. Свойство **not actions** определения роли указывает действия, которые должны быть исключены из списка разрешенных действий.


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
| [Участник учетной записи DocumentDB](#document-db-account-contributor) | Может управлять учетными записями DocumentDB |
| [Участник учетной записи интеллектуальных систем](#intelligent-systems-account-contributor) | Может управлять учетными записями интеллектуальных систем |
| [Участник сети](#network-contributor) | Может управлять всеми сетевыми ресурсами |
| [Участник учетной записи NewRelic APM](#newrelic-apm-account-contributor) | Может управлять учетными записями и приложениями для управления производительностью приложений NewRelic |
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

### Участник службы управления API
Может управлять службами управления API

| **Действия** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | Создание служб управления API и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник компонента Application Insights
Может управлять компонентами Application Insights

| **Действия** | |
| ------- | ------ |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
| Microsoft.Insights/webtests/* | Создание веб-тестов и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Оператор службы автоматизации
Может запускать, останавливать, приостанавливать и возобновлять задания

| **Действия** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | Чтение учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/runbooks/read | Чтение модулей Runbook службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/read | Чтение расписаний учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/schedules/write | Запись расписаний учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/read | Чтение заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/write | Запись заданий учетных записей службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Остановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостановка задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновление задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Чтение расписания задания учетной записи службы автоматизации |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Чтение расписания задания учетной записи службы автоматизации |

### Участник BizTalk
Может управлять службами BizTalk

| **Действия** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник ClearDB MySQL DB
Может создавать базы данных ClearDB MySQL

| **Действия** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | Создание баз данных ClearDB MySQL и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник
Может управлять всем, кроме доступа

| **Действия** ||
| ------- | ------ |
| * | Создание ресурсов всех типов и управление ими |

| **Запрещенные действия** ||
| ------- | ------ |
| Microsoft.Authorization/*/Write | Не может создавать роли и назначения ролей |
| Microsoft.Authorization/*/Delete | Не может удалять роли и назначения ролей |

### Участник фабрики данных
Может управлять фабриками данных

| **Действия** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | Создание фабрик данных и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Пользователь DevTest Labs
Может просматривать все, а также подключать, запускать, перезагружать виртуальные машины и завершать их работу

| **Действия** ||
| ------- | ------ |
| **/read | Чтение ресурсов всех типов | | Microsoft.DevTestLab/labs/createEnvironment/action | Создание лабораторной среды | | Microsoft.DevTestLab/labs/formulas/delete | Удаление формул | | Microsoft.DevTestLab/labs/formulas/write | Добавление или изменение формул | | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Оценка политик лаборатории | | Microsoft.Compute/virtualMachines/start/action | Запуск виртуальных машин | | Microsoft.Compute/virtualMachines/restart/action | Перезапуск виртуальных машин | | Microsoft.Compute/virtualMachines/deallocate/action | Отмена распределения виртуальных машин | | Microsoft.Storage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения | | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединение виртуальной сети | | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединение серверного пула адресов балансировщиков нагрузки | | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединение правила NAT для входящего трафика балансировщиков нагрузки | | Microsoft.Network/publicIPAddresses/join/action | Присоединение общедоступного IP-адреса | | Microsoft.Network/networkInterfaces/join/action | Добавление виртуальной машины в сетевой интерфейс | | Microsoft.Network/networkInterfaces/write | Запись сетевых интерфейсов |

### Участник учетной записиDocument DB
Может управлять учетными записями DocumentDB

| **Действия** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей DocumentDB и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записи интеллектуальных систем
Может управлять учетными записями интеллектуальных систем

| **Действия** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник сети
Может управлять всеми сетевыми ресурсами

| **Действия** ||
| ------- | ------ |
| Microsoft.Network/* | Создание сетей и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записиNewRelic APM
Может управлять учетными записями и приложениями для управления производительностью приложений NewRelic

| **Действия** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | Создание учетных записей для управления производительностью приложений NewRelic и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

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
| Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник коллекции заданий планировщика
Может управлять коллекциями заданий планировщика

| **Действия** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник службы поиска
Может управлять службами поиска

| **Действия** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Диспетчер безопасности
Может управлять компонентами безопасности, политиками безопасности и виртуальными машинами

| **Действия** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
| Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации для виртуальных машин для классических вычислений |
| Microsoft.ClassicCompute/virtualMachines/*/write | Запись конфигурации для виртуальных машин |
| Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник БД SQL
Может управлять базами данных SQL, но не их политиками безопасности

| **Действия** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Чтение серверов SQL Server |
| Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
| Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **Запрещенные действия** ||
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности |

### Диспетчер безопасности SQL
Может управлять политиками безопасности для серверов и баз данных SQL

| **Действия** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Чтение серверов SQL Server |
| Microsoft.Sql/servers/auditingPolicies/* | Создание политик аудита SQL Server и управление ими |
| Microsoft.Sql/servers/databases/read | Чтение баз данных SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Создание политик аудита баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Создание политик подключения баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Создание политик маскирования данных баз данных SQL Server и управление ими |
| Microsoft.Sql/servers/databases/securityMetrics/* | Создание метрик безопасности базы данных SQL и управление ими |
| Microsoft.Authorization/*/read | Чтение авторизации Майкрософт |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписок и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Sql/servers/databases/schemas/read | Чтение схем баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Чтение таблиц баз данных SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Чтение столбцов таблиц баз данных SQL Server |

### Участник SQL Server
Может управлять серверами и базами данных SQL, но не их политиками безопасности

| **Действия** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение|
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов группы ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписок и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

| **Запрещенные действия** ||
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | Не может изменять политики аудита SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита баз данных SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения баз данных SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных баз данных SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности баз данных SQL Server |

### Участник классической учетной записи хранения
Может управлять классическими учетными записями хранения

| **Действия** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resources/read | Чтение ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник учетной записи хранения
Может управлять учетными записями хранения, но не имеет к ним доступа.

| **Действия** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение всех элементов |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.ResourceHealth/availabilityStatuses/read | Получение данных о работоспособности ресурсов |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Insights/diagnosticSettings/* | Управление параметрами диагностики |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Администратор доступа пользователей
Может управлять доступом пользователей к ресурсам Azure

| **Действия** ||
| ------- | ------ |
| */read | Чтение ресурсов всех типов, кроме секретов. | | Microsoft.Authorization/* | Авторизация на чтение |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник классической виртуальной машины
Может управлять классическими виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | Чтение классических учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Чтение дисков учетных записей хранения |
| Microsoft.ClassicStorage/storageAccounts/images/read | Чтение образов учетных записей хранения |
| Microsoft.ClassicNetwork/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.ClassicNetwork/reservedIps/read | Чтение зарезервированных IP-адресов |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Присоединение виртуальных сетей |
| Microsoft.ClassicNetwork/reservedIps/link/action | Ссылка на зарезервированные IP-адреса |
| Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
| Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник виртуальной машины
Может управлять виртуальными машинами, но не виртуальными сетями и учетными записями хранения, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | Чтение учетных записей хранения |
| Microsoft.Storage/storageAccounts/listKeys/action | Вывод списка ключей учетной записи хранения |
| Microsoft.Network/virtualNetworks/read | Чтение виртуальных сетей |
| Microsoft.Network/virtualNetworks/subnets/join/action | Присоединение подсетей виртуальных сетей |
| Microsoft.Network/loadBalancers/read | Чтение балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Присоединение серверных пулов адресов балансировщиков нагрузки |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединение правил NAT для входящего трафика балансировщиков нагрузки |
| Microsoft.Network/publicIPAddresses/read | Чтение сетевых общедоступных IP-адресов |
| Microsoft.Network/publicIPAddresses/join/action | Присоединение сетевых общедоступных IP-адресов |
| Microsoft.Network/networkSecurityGroups/read | Чтение групп безопасности сети |
| Microsoft.Network/networkSecurityGroups/join/action | Присоединение групп безопасности сети |
| Microsoft.Network/networkInterfaces/* | Создание сетевых интерфейсов и управление ими |
| Microsoft.Network/locations/* | Создание сетевых расположений и управление ими |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Присоединение серверных пулов адресов сетевого шлюза приложений |
| Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
| Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
| Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник классической сети
Может управлять классическими виртуальными сетями и зарезервированными IP-адресами

| **Действия** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник веб-плана
Может управлять веб-планами

| **Действия** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

### Участник веб-сайта
Может управлять веб-сайтами, но не веб-планами, к которым они подключены

| **Действия** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | Чтение ферм серверов |
| Microsoft.Web/serverFarms/join/action | Присоединение ферм серверов |
| Microsoft.Web/sites/* | Создание веб-сайтов и управление ими |
| Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
| Microsoft.Web/listSitesAssignedToHostName/read | Чтение сайтов, назначенных имени узла |
| Microsoft.Authorization/*/read | Авторизация на чтение |
| Microsoft.Resources/subscriptions/resourceGroups/read | Чтение групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Чтение ресурсов групп ресурсов подписки |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Создание развертываний группы ресурсов подписки и управление ими |
| Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
| Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
| Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |

## См. также
- [Управление доступом на основе ролей в Azure](role-based-access-control-configure.md). Начало работы с RBAC на портале Azure.
- [Пользовательские роли в Azure RBAC](role-based-access-control-custom-roles.md). Сведения о создании пользовательских ролей в соответствии с потребностями доступа.
- [Создание отчета по журналу изменения доступа](role-based-access-control-access-change-history-report.md). Отслеживание изменения назначений ролей в RBAC.
- [Устранение неполадок контроля доступа на основе ролей](role-based-access-control-troubleshooting.md). Рекомендации по устранению распространенных проблем.

<!---HONumber=AcomDC_0511_2016-->