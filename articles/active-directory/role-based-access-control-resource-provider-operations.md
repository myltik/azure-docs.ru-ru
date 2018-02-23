---
title: "Операции поставщиков Azure Resource Manager | Документация Майкрософт"
description: "Описание операций, доступных в поставщиках ресурсов Microsoft Azure Resource Manager."
services: active-directory
documentationcenter: 
author: jboeshart
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 27880402d377701448d095a1295ece875729cd67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Операции поставщиков ресурсов Azure Resource Manager

В этом документе перечислены операции, доступные в каждом поставщике ресурсов Microsoft Azure Resource Manager. Они могут использоваться в настраиваемых ролях, чтобы обеспечить детальные разрешения управления доступом на основе ролей (RBAC) для ресурсов в Azure. Обратите внимание на то, что это не полный список, и операции могут добавляться или удаляться по мере обновления поставщиков. Строки операций используют формат `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Чтобы получить полный актуальный список, используйте командлет `Get-AzureRmProviderOperation` (в PowerShell) или команду `az provider operation list` (в Azure CLI версии 2).

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Операция | ОПИСАНИЕ |
|---|---|
|/configuration/action|Обновляет конфигурацию клиента.|
|/services/action|Обновляет экземпляр службы в клиенте.|
|/configuration/write|Создает конфигурацию клиента.|
|/configuration/read|Считывает конфигурацию клиента.|
|/services/write|Создает экземпляр службы в клиенте.|
|/services/read|Считывает экземпляры службы в клиенте.|
|/services/delete|Удаляет экземпляр службы из клиента.|
|/services/servicemembers/action|Создает экземпляр члена службы в клиенте.|
|/services/servicemembers/read|Считывает экземпляр члена службы в клиенте.|
|/services/servicemembers/delete|Удаляет экземпляр члена службы из клиента.|
|/services/servicemembers/alerts/read|Считывает оповещения для члена службы.|
|/services/alerts/read|Считывает оповещения для службы.|
|/services/alerts/read|Считывает оповещения для службы.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Операция | ОПИСАНИЕ |
|---|---|
|/generateRecommendations/action|Создает рекомендации.|
|/suppressions/action|Создает или обновляет подавления.|
|/register/action|Регистрирует подписку для Microsoft Advisor.|
|/generateRecommendations/read|Возвращает состояние создания рекомендаций.|
|/recommendations/read|Считывает рекомендации.|
|/suppressions/read|Возвращает подавления.|
|/suppressions/delete|Удаляет подавления.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Операция | ОПИСАНИЕ |
|---|---|
|/servers/read|Извлекает информацию об указанном сервере анализа данных.|
|/servers/write|Создает или обновляет указанный сервер анализа данных.|
|/servers/delete|Удаляет сервер анализа данных.|
|/servers/suspend/action|Приостанавливает сервер анализа данных.|
|/servers/resume/action|Возобновляет работу сервера анализа данных.|
|/servers/checkNameAvailability<br>/action|Проверяет, является ли данное имя сервера анализа данных допустимым и неиспользуемым.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет, доступно ли указанное имя службы.|
|/register/action|Регистрирует подписку для поставщика ресурсов Microsoft.ApiManagement.|
|/unregister/action|Отменяет регистрацию подписки для поставщика ресурсов Microsoft.ApiManagement.|
|/service/write|Создает экземпляр службы управления API.|
|/service/read|Считывает метаданные для экземпляра службы управления API.|
|/service/delete|Удаляет экземпляр службы управления API.|
|/service/updatehostname/action|Позволяет настроить, обновить или удалить имена личных доменов для службы управления API.|
|/service/uploadcertificate/action|Передает SSL-сертификата для службы управления API.|
|/service/backup/action|Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения.|
|/service/restore/action|Восстанавливает службу управления API из указанного контейнера в предоставленной пользователем учетной записи хранения.|
|/service/managedeployments/action|Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API.|
|/service/getssotoken/action|Возвращает маркер единого входа, который может использоваться для входа на портал прежней версии службы управления API от имени администратора.|
|/service/applynetworkconfigurationupdates/action|Обновляет ресурсы Microsoft.ApiManagement, работающие в виртуальной сети, для применения обновленных параметров сети.|
|/service/operationresults/read|Возвращает текущее состояние длительной операции.|
|/service/networkStatus/read|Возвращает состояние сетевого доступа для ресурсов.|
|/service/loggers/read|Возвращает список средств ведения журнала или сведения о средстве ведения журнала.|
|/service/loggers/write|Добавляет новое средство ведения журнала или обновляет существующее.|
|/service/loggers/delete|Удаляет существующее средство ведения журнала.|
|/service/users/read|Возвращает список зарегистрированных пользователей или сведения об учетной записи пользователя.|
|/service/users/write|Регистрирует нового пользователя или обновляет данные учетной записи существующего пользователя.|
|/service/users/delete|Удаляет учетную запись пользователя.|
|/service/users/generateSsoUrl/action|Создает URL-адрес единого входа. URL-адрес, который можно использовать для доступа к порталу администрирования.|
|/service/users/subscriptions/read|Возвращает список подписок пользователя.|
|/service/users/keys/read|Возвращает список ключей пользователя.|
|/service/users/groups/read|Возвращает список групп пользователя.|
|/service/tenant/operationResults/read|Возвращает список результатов операций или результат определенной операции.|
|/service/tenant/policy/read|Возвращает конфигурацию политики для клиента.|
|/service/tenant/policy/write|Задает конфигурацию политики для клиента.|
|/service/tenant/policy/delete|Удаляет конфигурацию политики для клиента.|
|/service/tenant/configuration/save/action|Создает фиксацию с использованием моментального снимка конфигурации для указанной ветви в репозитории.|
|/service/tenant/configuration/deploy/action|Выполняет задачу развертывания для применения изменений из указанной ветви Git к конфигурации в базе данных.|
|/service/tenant/configuration/validate/action|Проверяет изменения из указанной ветви Git.|
|/service/tenant/configuration/operationResults/read|Возвращает список результатов операций или результат определенной операции.|
|/service/tenant/configuration/syncState/read|Возвращает состояние последней синхронизации с Git.|
|/service/tenant/access/read|Возвращает сведения о доступе клиента.|
|/service/tenant/access/write|Обновляет сведения о доступе клиента.|
|/service/tenant/access/regeneratePrimaryKey/action|Повторно создает первичный ключ доступа.|
|/service/tenant/access/regenerateSecondaryKey/action|Повторно создает вторичный ключ доступа.|
|/service/identityProviders/read|Возвращает список поставщиков удостоверений или сведения о поставщике удостоверений.|
|/service/identityProviders/write|Создает новый поставщик удостоверений или обновляет данные существующего.|
|/service/identityProviders/delete|Удаляет существующий поставщик удостоверений.|
|/service/subscriptions/read|Возвращает список подписок на продукт или сведения о подписке на продукт.|
|/service/subscriptions/write|Подписывает существующего пользователя на имеющийся продукт или обновляет данные существующей подписки. С помощью этой операции можно обновить подписку.|
|/service/subscriptions/delete|Удаляет подписку. С помощью этой операции можно удалить подписку.|
|/service/subscriptions/regeneratePrimaryKey/action|Повторно создает первичный ключ подписки.|
|/service/subscriptions/regenerateSecondaryKey/action|Повторно создает вторичный ключ подписки.|
|/service/backends/read|Возвращает список серверных частей или сведения о серверной части.|
|/service/backends/write|Добавляет новую серверную часть или обновляет данные существующей.|
|/service/backends/delete|Удаляет существующую серверную часть.|
|/service/apis/read|Возвращает список всех зарегистрированных интерфейсов API или сведения об API.|
|/service/apis/write|Создает новый API или обновляет данные существующего API.|
|/service/apis/delete|Удаляет существующий API.|
|/service/apis/policy/read|Возвращает сведения о конфигурации политики для API.|
|/service/apis/policy/write|Задает сведения о конфигурации политики для API.|
|/service/apis/policy/delete|Удаляет конфигурацию политики из API.|
|/service/apis/operations/read|Возвращает список существующих операций API или сведения о конкретной операции API.|
|/service/apis/operations/write|Создает новую операцию API или обновляет существующую.|
|/service/apis/operations/delete|Удаляет существующую операцию API.|
|/service/apis/operations/policy/read|Возвращает сведения о конфигурации политики для операции.|
|/service/apis/operations/policy/write|Задает сведения о конфигурации политики для операции.|
|/service/apis/operations/policy/delete|Удаляет конфигурацию политики из операции.|
|/service/products/read|Возвращает список продуктов или сведения о продукте.|
|/service/products/write|Создает новый продукт или обновляет данные существующего.|
|/service/products/delete|Удаляет существующий продукт.|
|/service/products/subscriptions/read|Возвращает список подписок на продукт.|
|/service/products/apis/read|Возвращает список интерфейсов API, добавленных в существующий продукт.|
|/service/products/apis/write|Добавляет API в существующий продукт.|
|/service/products/apis/delete|Удаляет API из существующего продукта.|
|/service/products/policy/read|Возвращает конфигурацию политики существующего продукта.|
|/service/products/policy/write|Задает конфигурацию политики существующего продукта.|
|/service/products/policy/delete|Удаляет конфигурацию политики из существующего продукта.|
|/service/products/groups/read|Возвращает список групп разработчиков, связанных с продуктом.|
|/service/products/groups/write|Устанавливает связь между существующими продуктом и группой разработчиков.|
|/service/products/groups/delete|Удаляет связь между существующими продуктом и группой разработчиков.|
|/service/openidConnectProviders/read|Возвращает список поставщиков OpenID Connect или сведения о поставщике OpenID Connect.|
|/service/openidConnectProviders/write|Создает новый поставщик OpenID Connect или обновляет данные существующего.|
|/service/openidConnectProviders/delete|Удаляет существующий поставщик OpenID Connect.|
|/service/certificates/read|Возвращает список сертификатов или сведения о сертификате.|
|/service/certificates/write|Добавляет новый сертификат.|
|/service/certificates/delete|Удаляет существующий сертификат.|
|/service/properties/read|Возвращает список всех свойств или сведения об указанном свойстве.|
|/service/properties/write|Создает новое свойство или обновляет значение указанного свойства.|
|/service/properties/delete|Удаляет существующее свойство.|
|/service/groups/read|Возвращает список групп или сведения о группе.|
|/service/groups/write|Создает новую группу или обновляет данные существующей.|
|/service/groups/delete|Удаляет существующую группу.|
|/service/groups/users/read|Возвращает список пользователей группы.|
|/service/groups/users/write|Добавляет существующего пользователя в существующий продукт.|
|/service/groups/users/delete|Удаляет пользователя из существующей группы.|
|/service/authorizationServers/read|Возвращает список серверов авторизации или сведения о сервере авторизации.|
|/service/authorizationServers/write|Создает новый сервер авторизации или обновляет данные существующего.|
|/service/authorizationServers/delete|Удаляет существующий сервер авторизации.|
|/service/reports/bySubscription/read|Возвращает отчет, объединенный по подписке.|
|/service/reports/byRequest/read|Возвращает запросы, передающие данные.|
|/service/reports/byOperation/read|Возвращает отчет, объединенный по операциям.|
|/service/reports/byGeo/read|Возвращает отчет, объединенный по географическому региону.|
|/service/reports/byUser/read|Возвращает отчет, объединенный по разработчикам.|
|/service/reports/byTime/read|Возвращает отчет, объединенный по временным периодам.|
|/service/reports/byApi/read|Возвращает отчет, объединенный по интерфейсам API.|
|/service/reports/byProduct/read|Возвращает отчет, объединенный по продуктам.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Операция | ОПИСАНИЕ |
|---|---|
|/elevateAccess/action|Предоставляет вызывающему доступ с правами администратора для области действия клиента.|
|/classicAdministrators/read|Считывает администраторов для подписки.|
|/classicAdministrators/write|Добавляет или изменяет администратора подписки.|
|/classicAdministrators/delete|Удаляет учетные данные администратора из подписки.|
|/locks/read|Возвращает блокировки в указанной области.|
|/locks/write|Добавляет блокировки в указанной области.|
|/locks/delete|Удаляет блокировки в указанной области.|
|/policyAssignments/read|Возвращает сведения о назначении политики.|
|/policyAssignments/write|Создает назначение политики в указанной области.|
|/policyAssignments/delete|Удаляет назначение политики в указанной области.|
|/permissions/read|Выводит список всех разрешений, которые есть у вызывающего в заданной области.|
|/roleDefinitions/read|Возвращает сведения об определении роли.|
|/roleDefinitions/write|Создает или обновляет определение настраиваемой роли с помощью указанных разрешений и назначаемых областей.|
|/roleDefinitions/delete|Удаляет указанное определение настраиваемой роли.|
|/providerOperations/read|Возвращает операции для всех поставщиков ресурсов, которые могут быть использованы в определениях ролей.|
|/policyDefinitions/read|Возвращает сведения об определении политики.|
|/policyDefinitions/write|Создает определение настраиваемой политики.|
|/policyDefinitions/delete|Удаляет определение политики.|
|/roleAssignments/read|Возвращает сведения о назначении роли.|
|/roleAssignments/write|Создает назначение роли в указанной области.|
|/roleAssignments/delete|Здесь описывается удаление назначения роли в указанной области.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Операция | ОПИСАНИЕ |
|---|---|
|/automationAccounts/read|Возвращает учетную запись службы автоматизации Azure.|
|/automationAccounts/write|Создает или обновляет учетную запись службы автоматизации Azure.|
|/automationAccounts/delete|Удаляет учетную запись службы автоматизации Azure.|
|/automationAccounts/configurations/readContent/action|Возвращает содержимое Azure Automation DSC.|
|/automationAccounts/hybridRunbookWorkerGroups/read|Считывает ресурсы гибридной рабочей роли Runbook.|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Удаляет ресурсы гибридной рабочей роли Runbook.|
|/automationAccounts/jobSchedules/read|Возвращает расписание заданий службы автоматизации Azure.|
|/automationAccounts/jobSchedules/write|Создает расписание заданий службы автоматизации Azure.|
|/automationAccounts/jobSchedules/delete|Удаляет расписание заданий службы автоматизации Azure.|
|/automationAccounts/connectionTypes/read|Возвращает ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/connectionTypes/write|Создает ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/connectionTypes/delete|Удаляет ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/modules/read|Возвращает модуль службы автоматизации Azure.|
|/automationAccounts/modules/write|Создает или обновляет модуль службы автоматизации Azure.|
|/automationAccounts/modules/delete|Удаляет модуль службы автоматизации Azure.|
|/automationAccounts/credentials/read|Возвращает ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/credentials/write|Создает или обновляет ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/credentials/delete|Удаляет ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/certificates/read|Возвращает ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/certificates/write|Создает или обновляет ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/certificates/delete|Удаляет ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/schedules/read|Возвращает ресурс расписания службы автоматизации Azure.|
|/automationAccounts/schedules/write|Создает или обновляет ресурс расписания службы автоматизации Azure.|
|/automationAccounts/schedules/delete|Удаляет ресурс расписания службы автоматизации Azure.|
|/automationAccounts/jobs/read|Возвращает задание службы автоматизации Azure.|
|/automationAccounts/jobs/write|Создает задание службы автоматизации Azure.|
|/automationAccounts/jobs/stop/action|Останавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/suspend/action|Приостанавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/resume/action|Возобновляет выполнение задания службы автоматизации Azure.|
|/automationAccounts/jobs/runbookContent/action|Возвращает содержимое runbook службы автоматизации Azure во время выполнения задания.|
|/automationAccounts/jobs/output/action|Возвращает выходные данные задания.|
|/automationAccounts/jobs/read|Возвращает задание службы автоматизации Azure.|
|/automationAccounts/jobs/write|Создает задание службы автоматизации Azure.|
|/automationAccounts/jobs/stop/action|Останавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/suspend/action|Приостанавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/resume/action|Возобновляет выполнение задания службы автоматизации Azure.|
|/automationAccounts/jobs/streams/read|Возвращает поток задания службы автоматизации Azure.|
|/automationAccounts/connections/read|Возвращает ресурс подключения службы автоматизации Azure.|
|/automationAccounts/connections/write|Создает или обновляет ресурс подключения службы автоматизации Azure.|
|/automationAccounts/connections/delete|Удаляет ресурс подключения службы автоматизации Azure.|
|/automationAccounts/variables/read|Считывает ресурс переменной службы автоматизации Azure.|
|/automationAccounts/variables/write|Создает или обновляет ресурс переменной службы автоматизации Azure.|
|/automationAccounts/variables/delete|Удаляет ресурс переменной службы автоматизации Azure.|
|/automationAccounts/runbooks/readContent/action|Возвращает содержимое runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/read|Возвращает runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/write|Создает или обновляет runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/delete|Удаляет runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/readContent/action|Возвращает содержимое черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/writeContent/action|Создает содержимое черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/read|Возвращает черновик runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/publish/action|Публикует черновик runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/undoEdit/action|Отменяет правки в черновике runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/read|Возвращает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/write|Создает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/stop/action|Останавливает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Приостанавливает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/resume/action|Возобновляет выполнение тестового задания черновика runbook службы автоматизации Azure.|
|/automationAccounts/webhooks/read|Считывает runbook службы автоматизации Azure.|
|/automationAccounts/webhooks/write|Создает или обновляет webhook службы автоматизации Azure.|
|/automationAccounts/webhooks/delete|Удаляет webhook службы автоматизации Azure. |
|/automationAccounts/webhooks/generateUri/action|Создает универсальный код ресурса (URI) для webhook службы автоматизации Azure.|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

Это не полный поставщик ARM, и он не предоставляет какие-либо операции ARM.

## <a name="microsoftbatch"></a>Microsoft.Batch

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов пакетной службы и позволяет создавать учетные записи пакетной службы.|
|/batchAccounts/write|Создает новую учетную запись пакетной службы или обновляет существующую.|
|/batchAccounts/read|Выводит список учетных записей пакетной службы или возвращает свойства учетной записи пакетной службы.|
|/batchAccounts/delete|Удаляет учетную запись пакетной службы.|
|/batchAccounts/listkeys/action|Отображает ключи доступа для учетной записи пакетной службы.|
|/batchAccounts/regeneratekeys/action|Повторно создает ключи доступа для учетной записи пакетной службы.|
|/batchAccounts/syncAutoStorageKeys/action|Синхронизирует ключи доступа для автоматической учетной записи хранения, настроенной для учетной записи пакетной службы.|
|/batchAccounts/applications/read|Выводит список приложений или возвращает свойства приложения.|
|/batchAccounts/applications/write|Создает новое приложение или обновляет существующее.|
|/batchAccounts/applications/delete|Удаляет приложение.|
|/batchAccounts/applications/versions/read|Возвращает свойства пакета приложения.|
|/batchAccounts/applications/versions/write|Создает новый пакет приложения или обновляет существующий.|
|/batchAccounts/applications/versions/activate/action|Активирует пакет приложения.|
|/batchAccounts/applications/versions/delete|Удаляет пакет приложения.|
|/locations/quotas/read|Возвращает квоты пакетной службы для указанной подписки в заданном регионе Azure.|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Операция | ОПИСАНИЕ |
|---|---|
|/invoices/read|Выводит список доступных счетов.|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Операция | ОПИСАНИЕ |
|---|---|
|/mapApis/Read|Операция чтения.|
|/mapApis/Write|Операции записи.|
|/mapApis/Delete|Операция удаления.|
|/mapApis/regenerateKey/action|Повторно создает ключ.|
|/mapApis/listSecrets/action|Отображает секреты.|
|/mapApis/listSingleSignOnToken/action|Считывает маркер авторизации единого входа для ресурса.|
|/Operations/read|Описание операции.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Операция | ОПИСАНИЕ |
|---|---|
|/checknameavailability/action|Проверяет, доступно ли имя для нового кэша Redis.|
|/register/action|Регистрирует поставщик ресурсов Microsoft.Cache для подписки.|
|/unregister/action|Отменяет регистрацию поставщика ресурсов Microsoft.Cache для подписки.|
|/redis/write|Изменяет параметры и конфигурацию кэша Redis на портале управления.|
|/redis/read|Отображает параметры и конфигурацию кэша Redis на портале управления.|
|/redis/delete|Удаляет весь кэш Redis.|
|/redis/listKeys/action|Отображает значения ключей доступа к кэшу Redis на портале управления.|
|/redis/regenerateKey/action|Изменяет значения ключей доступа к кэшу Redis на портале управления.|
|/redis/import/action|Импортирует в Redis данные в указанном формате из нескольких больших двоичных объектов.|
|/redis/export/action|Экспортирует данные Redis в большие двоичные объекты с префиксом в службе хранилища в указанном формате.|
|/redis/forceReboot/action|Принудительно перезапускает экземпляр кэша. При этом возможна потеря данных.|
|/redis/stop/action|Останавливает экземпляр кэша.|
|/redis/start/action|Запускает экземпляр кэша.|
|/redis/metricDefinitions/read|Возвращает доступные метрики для кэша Redis.|
|/redis/firewallRules/read|Возвращает правила IP-адресов брандмауэра для кэша Redis.|
|/redis/firewallRules/write|Изменяет правила IP-адресов брандмауэра для кэша Redis.|
|/redis/firewallRules/delete|Удаляет правила IP-адресов брандмауэра для кэша Redis.|
|/redis/listUpgradeNotifications/read|Выводит список последних уведомлений об обновлениях для клиента кэша.|
|/redis/linkedservers/read|Возвращает связанные серверы для кэша Redis.|
|/redis/linkedservers/write|Добавляет связанный сервер для кэша Redis.|
|/redis/linkedservers/delete|Удаляет связанный сервер из кэша Redis.|
|/redis/patchSchedules/read|Возвращает расписание исправления для кэша Redis.|
|/redis/patchSchedules/write|Изменяет расписание исправления для кэша Redis.|
|/redis/patchSchedules/delete|Удаляет расписание исправления для кэша Redis.|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Операция | ОПИСАНИЕ |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Подготавливает субъект-службу для субъекта приложения-службы.|
|/validateCertificateRegistrationInformation/Action|Проверяет объект покупки сертификата без его отправки.|
|/register/action|Регистрирует поставщик сертификатов Майкрософт для подписки.|
|/certificateOrders/Write|Добавляет новый заказ сертификата или обновляет существующий.|
|/certificateOrders/Delete|Удаляет существующий сертификат службы приложений.|
|/certificateOrders/Read|Возвращает список заказов сертификатов.|
|/certificateOrders/reissue/Action|Повторно отправляет существующий заказ сертификата.|
|/certificateOrders/renew/Action|Обновляет существующий заказ сертификата.|
|/certificateOrders/retrieveCertificateActions/Action|Извлекает список действий сертификата.|
|/certificateOrders/retrieveEmailHistory/Action|Извлекает журнал электронных сообщений для сертификата.|
|/certificateOrders/resendEmail/Action|Повторно отправляет сообщение сертификата.|
|/certificateOrders/verifyDomainOwnership/Action|проверка принадлежности домена;|
|/certificateOrders/resendRequestEmails/Action|Повторно отправляет сообщения запроса на другой электронный адрес.|
|/certificateOrders/resendRequestEmails/Action|Извлекает печать сайта для выданного сертификата службы приложений.|
|/certificateOrders/certificates/Write|Добавляет новый сертификат или обновляет существующий.|
|/certificateOrders/certificates/Delete|Удаляет существующий сертификат.|
|/certificateOrders/certificates/Read|Возвращает список сертификатов.|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Выполняет регистрацию в классической вычислительной среде.|
|/checkDomainNameAvailability/action|Проверяет доступность заданного доменного имени.|
|/moveSubscriptionResources/action|Перемещает все классические ресурсы в другую подписку.|
|/validateSubscriptionMoveAvailability/action|Проверяет доступность классической операции перемещения для подписки.|
|/operatingSystemFamilies/read|Выводит список семейств гостевых операционных систем, доступных в Microsoft Azure, а также список версий операционной системы, доступных для каждого семейства.
|/capabilities/read|Отображает возможности.|
|/operatingSystems/read|Выводит список версий гостевой операционной системы, доступных в Microsoft Azure.|
|/resourceTypes/skus/read|Возвращает список номеров SKU для поддерживаемых типов ресурсов.|
|/domainNames/read|Возвращает доменные имена для ресурсов.|
|/domainNames/write|Добавляет или изменяет доменные имена для ресурсов.|
|/domainNames/delete|Удаляет доменные имена для ресурсов.|
|/domainNames/swap/action|Переключает промежуточный слот на рабочий слот.|
|/domainNames/serviceCertificates/read|Возвращает используемые сертификаты службы.|
|/domainNames/serviceCertificates/write|Добавляет или изменяет используемые сертификаты службы.|
|/domainNames/serviceCertificates/delete|Удаляет используемые сертификаты службы.|
|/domainNames/serviceCertificates/operationStatuses/read|Считывает состояние операции сертификатов службы доменных имен.|
|/domainNames/capabilities/read|Отображает возможности доменного имени.|
|/domainNames/extensions/read|Возвращает расширения доменных имен.|
|/domainNames/extensions/write|Добавляет расширения доменных имен.|
|/domainNames/extensions/delete|Удаляет расширения доменных имен.|
|/domainNames/extensions/operationStatuses/read|Считывает состояние операции расширений доменных имен.|
|/domainNames/active/write|Задает активное доменное имя.|
|/domainNames/slots/read|Отображает слоты развертывания.|
|/domainNames/slots/write|Создает или обновляет развертывание.|
|/domainNames/slots/delete|Удаляет заданный слот развертывания.|
|/domainNames/slots/start/action|Запускает слот развертывания.|
|/domainNames/slots/stop/action|Приостанавливает слот развертывания.|
|/domainNames/slots/operationStatuses/read|Считывает состояние операции слотов доменных имен.|
|/domainNames/slots/roles/read|Возвращает роль для слота развертывания.|
|/domainNames/slots/roles/extensionReferences/read|Возвращает ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/extensionReferences/write|Добавляет или изменяет ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/extensionReferences/delete|Удаляет ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Считывает состояние операции ссылок на расширение ролей слотов доменных имен.|
|/domainNames/slots/roles/roleInstances/read|Возвращает экземпляр роли.|
|/domainNames/slots/roles/roleInstances/restart/action|Перезапускает экземпляры роли.|
|/domainNames/slots/roles/roleInstances/reimage/action|Пересоздает образ экземпляра роли.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Считывает состояние операции экземпляров роли для ролей слотов доменных имен.|
|/domainNames/slots/state/start/write|Задает состояние остановки для слота развертывания.|
|/domainNames/slots/state/stop/write|Задает состояние запуска для слота развертывания.|
|/domainNames/slots/upgradeDomain/write|Обновляет домен.|
|/domainNames/internalLoadBalancers/read|Возвращает внутренние подсистемы балансировки нагрузки.|
|/domainNames/internalLoadBalancers/write|Создает внутреннюю подсистему балансировки нагрузки.|
|/domainNames/internalLoadBalancers/delete|Удаляет внутреннюю подсистему балансировки нагрузки.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Считывает состояние операции внутренних подсистем балансировки нагрузки доменных имен.|
|/domainNames/loadBalancedEndpointSets/read|Отображает наборы конечных точек с балансировкой нагрузки.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Считывает состояние операции наборов конечных точек с балансировкой нагрузки доменных имен.|
|/domainNames/availabilitySets/read|Отображает группу доступности для ресурса.|
|/quotas/read|Возвращает квоту для подписки.|
|/virtualMachines/read|Извлекает список виртуальных машин.|
|/virtualMachines/write|Добавляет или изменяет виртуальные машины.|
|/virtualMachines/delete|Удаляет виртуальные машины.|
|/virtualMachines/start/action|Запустите виртуальную машину.|
|/virtualMachines/redeploy/action|Повторно развертывает виртуальную машину.|
|/virtualMachines/restart/action|Перезапускает виртуальные машины.|
|/virtualMachines/stop/action|Останавливает виртуальную машину.|
|/virtualMachines/shutdown/action|Завершает работу виртуальной машины.|
|/virtualMachines/attachDisk/action|Подключает диск данных к виртуальной машине.|
|/virtualMachines/detachDisk/action|Отключает диск данных от виртуальной машины.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Скачивает RDP-файл для виртуальной машины.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Возвращает определения метрик.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметры диагностики.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Добавляет или изменяет параметры диагностики.|
|/virtualMachines/metrics/read|Возвращает метрики.|
|/virtualMachines/operationStatuses/read|Считывает состояние операции виртуальных машин.|
|/virtualMachines/extensions/read|Возвращает расширение виртуальной машины.|
|/virtualMachines/extensions/write|Размещает расширение виртуальной машины.|
|/virtualMachines/extensions/operationStatuses/read|Считывает состояние операции расширений виртуальной машины.|
|/virtualMachines/asyncOperations/read|Возвращает возможные асинхронные операции.|
|/virtualMachines/disks/read|Извлекает список дисков данных.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Выполняет регистрацию в классической сети.|
|/gatewaySupportedDevices/read|Извлекает список поддерживаемых устройств.|
|/reservedIps/read|Возвращает зарезервированные IP-адреса.|
|/reservedIps/write|Добавляет новый зарезервированный IP-адрес.|
|/reservedIps/delete|Удаляет зарезервированный IP-адрес.|
|/reservedIps/link/action|Привязывает зарезервированный IP-адрес.|
|/reservedIps/join/action|Присоединяет зарезервированный IP-адрес.|
|/reservedIps/operationStatuses/read|Считывает состояние операции для зарезервированных IP-адресов.|
|/virtualNetworks/read|Возвращает виртуальную сеть.|
|/virtualNetworks/write|Добавляет новую виртуальную сеть.|
|/virtualNetworks/delete|Удаляет виртуальную сеть.|
|/virtualNetworks/peer/action|Создает пиринг между виртуальными сетями.|
|/virtualNetworks/join/action|Подключает виртуальную сеть.|
|/virtualNetworks/checkIPAddressAvailability/action|Проверяет доступность заданного IP-адреса в виртуальной сети.|
|/virtualNetworks/capabilities/read|Отображает возможности.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|Считывает состояние операции группы безопасности сети, связанной с подсетью виртуальной сети.|
|/virtualNetworks/operationStatuses/read|Считывает состояние операции виртуальных сетей.|
|/virtualNetworks/gateways/read|Возвращает шлюзы виртуальной сети.|
|/virtualNetworks/gateways/write|Добавляет шлюз виртуальной сети.|
|/virtualNetworks/gateways/delete|Удаляет шлюз виртуальной сети.|
|/virtualNetworks/gateways/startDiagnostics/action|Запускает диагностику шлюза виртуальной сети.|
|/virtualNetworks/gateways/stopDiagnostics/action|Останавливает диагностику шлюза виртуальной сети.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Скачивает данные диагностики шлюза.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Извлекает ключ службы канала.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Скачивает сценарий конфигурации устройства.|
|/virtualNetworks/gateways/listPackage/action|Отображает пакет шлюза виртуальной сети.|
|/virtualNetworks/gateways/operationStatuses/read|Считывает состояние операции шлюзов виртуальной сети.|
|/virtualNetworks/gateways/packages/read|Возвращает пакет шлюза виртуальной сети.|
|/virtualNetworks/gateways/connections/read|Извлекает список подключений.|
|/virtualNetworks/gateways/connections/connect/action|Устанавливает подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/connections/disconnect/action|Отключает подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/connections/test/action|Тестирует подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Считывает отозванные сертификаты клиента.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Отменяет сертификат клиента.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Отменяет отмену сертификата клиента.|
|/virtualNetworks/gateways/clientRootCertificates/read|Выполняет поиск корневых сертификатов клиента.|
|/virtualNetworks/gateways/clientRootCertificates/write|Передает новый корневой сертификат клиента.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Удаляет сертификат клиента шлюза виртуальной сети.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Скачивает сертификат по отпечатку.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Отображает пакет сертификата шлюза виртуальной сети.|
|/networkSecurityGroups/read|Возвращает группу безопасности сети.|
|/networkSecurityGroups/write|Добавляет новую группу безопасности сети.|
|/networkSecurityGroups/delete|Удаляет группу безопасности сети.|
|/networkSecurityGroups/operationStatuses/read|Считывает состояние операции группы безопасности сети.|
|/networkSecurityGroups/securityRules/read|Возвращает правило безопасности.|
|/networkSecurityGroups/securityRules/write|Добавляет или обновляет правило безопасности.|
|/networkSecurityGroups/securityRules/delete|Удаляет правило безопасности.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Считывает состояние операции правил безопасности группы безопасности сети.|
|/quotas/read|Возвращает квоту для подписки.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Выполняет регистрацию в классической службе хранилища.|
|/checkStorageAccountAvailability/action|Проверяет доступность учетной записи хранения.|
|/capabilities/read|Отображает возможности.|
|/publicImages/read|Возвращает общедоступный образ виртуальной машины.|
|/images/read|Возвращает образ.|
|/storageAccounts/read|Возвращает учетную запись хранения для заданной учетной записи.|
|/storageAccounts/write|Добавляет новую учетную запись хранения.|
|/storageAccounts/delete|Удаляет учетную запись хранения.|
|/storageAccounts/listKeys/action|Отображает ключи доступа для учетных записей хранения.|
|/storageAccounts/regenerateKey/action|Повторно создает существующие ключи доступа для учетной записи хранения.|
|/storageAccounts/operationStatuses/read|Считывает состояние операции ресурса.|
|/storageAccounts/images/read|Возвращает образ в учетной записи хранения.|
|/storageAccounts/images/delete|Удаляет заданный образ из учетной записи хранения.|
|/storageAccounts/disks/read|Возвращает диск в учетной записи хранения.|
|/storageAccounts/disks/write|Добавляет диск учетной записи хранения.|
|/storageAccounts/disks/delete|Удаляет заданный диск из учетной записи хранения.|
|/storageAccounts/disks/operationStatuses/read|Считывает состояние операции ресурса.|
|/storageAccounts/osImages/read|Возвращает заданный образ операционной системы в учетной записи хранения.|
|/storageAccounts/osImages/delete|Удаляет заданный образ операционной системы из учетной записи хранения.|
|/storageAccounts/services/read|Возвращает доступные службы.|
|/storageAccounts/services/metricDefinitions/read|Возвращает определения метрик.|
|/storageAccounts/services/metrics/read|Возвращает метрики.|
|/storageAccounts/services/diagnosticSettings/read|Возвращает параметры диагностики.|
|/storageAccounts/services/diagnosticSettings/write|Добавляет или изменяет параметры диагностики.|
|/disks/read|Возвращает диск в учетной записи хранения.|
|/osImages/read|Возвращает образ операционной системы.|
|/quotas/read|Возвращает квоту для подписки.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/read|Считывает учетные записи API.|
|/accounts/write|Записывает учетные записи API.|
|/accounts/delete|Удаляет учетные записи API.|
|/accounts/listKeys/action|Отображает ключи.|
|/accounts/regenerateKey/action|Повторно создает ключ.|
|/accounts/skus/read|Считывает доступные номера SKU для существующего ресурса.|
|/accounts/usages/read|Возвращает данные об использовании квоты для существующего ресурса.|
|/Operations/read|Описание операции.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Операция | ОПИСАНИЕ |
|---|---|
|/RateCard/read|Возвращает данные предложения, метаданные ресурсов и единиц измерения и тарифы для заданной подписки.|
|/UsageAggregates/read|Извлекает данные о потреблении ресурсов Microsoft Azure по подписке. Результат содержит статистические данные об использовании, а также сведения о подписке и ресурсах за определенный диапазон времени.|

## <a name="microsoftcompute"></a>Microsoft.Compute;

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку в поставщике ресурсов Microsoft.Compute.|
|/restorePointCollections/read|Возвращает свойства коллекции точек восстановления.|
|/restorePointCollections/write|Создает новую коллекцию точек восстановления или обновляет существующую.|
|/restorePointCollections/delete|Удаляет коллекцию точек восстановления и содержащиеся в ней точки восстановления.|
|/restorePointCollections/restorePoints/read|Возвращает свойства точки восстановления.|
|/restorePointCollections/restorePoints/write|Создает точку восстановления.|
|/restorePointCollections/restorePoints/delete|Удаляет точку восстановления.|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Возвращает свойства точки восстановления вместе с универсальными кодами ресурсов (URI) SAS больших двоичных объектов.|
|/virtualMachineScaleSets/read|Возвращает свойства масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/write|Создает новый масштабируемый набор виртуальных машин или обновляет существующий.|
|/virtualMachineScaleSets/delete|Удаляет масштабируемый набор виртуальных машин.|
|/virtualMachineScaleSets/start/action|Запускает экземпляры масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/powerOff/action|Завершает работу экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/restart/action|Перезапускает экземпляры масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/deallocate/action|Завершает работу вычислительных ресурсов и освобождает их для экземпляров виртуальной машины в масштабируемом наборе виртуальных машин. |
|/virtualMachineScaleSets/manualUpgrade/action|Выполняет ручное обновление экземпляров до последней модели масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/scale/action|Уменьшает или увеличивает количество экземпляров в существующем масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/instanceView/read|Возвращает представление экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/skus/read|Отображает допустимые номера SKU для существующего масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/read|Извлекает свойства виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/delete|Удаляет конкретную виртуальную машину в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/start/action|Запускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Завершает работу экземпляра виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Перезапускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Завершает работу вычислительных ресурсов и освобождает их для виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Извлекает представление экземпляров виртуальной машины в масштабируемом наборе виртуальных машин.|
|/images/read|Возвращает свойства образа.|
|/images/write|Создает новый образ или обновляет существующий.|
|/images/delete|Удаляет кластер.|
|/operations/read|Выводит список операций, доступных в поставщике ресурсов Microsoft.Compute.|
|/disks/read|Возвращает свойства диска.|
|/disks/write|Создает новый диск или обновляет существующий.|
|/disks/delete|Удаляет диск.|
|/disks/beginGetAccess/action|Возвращает универсальный код ресурса (URI) SAS диска для доступа к большому двоичному объекту.|
|/disks/endGetAccess/action|Отменяет универсальный код ресурса (URI) SAS диска.|
|/snapshots/read|Возвращает свойства моментального снимка.|
|/snapshots/write|Создает новый моментальный снимок или обновляет существующий.|
|/snapshots/delete|Удаляет моментальный снимок.|
|/availabilitySets/read|Возвращает свойства группы доступности.|
|/availabilitySets/write|Создает новую группу доступности или обновляет существующую.|
|/availabilitySets/delete|Удаляет группу доступности.|
|/availabilitySets/vmSizes/read|Выводит список доступных размеров для создания или обновления виртуальной машины в группе доступности.|
|/virtualMachines/read|Возвращает свойства виртуальной машины.|
|/virtualMachines/write|Создает новую виртуальную машину или обновляет существующую.|
|/virtualMachines/delete|Удаляет виртуальную машину.|
|/virtualMachines/start/action|Запускает виртуальную машину.|
|/virtualMachines/powerOff/action|Завершает работу виртуальной машины. Обратите внимание, что плата за виртуальную машину по-прежнему будет взиматься.|
|/virtualMachines/redeploy/action|Повторно развертывает виртуальную машину.|
|/virtualMachines/restart/action|Перезапускает виртуальную машину.|
|/virtualMachines/deallocate/action|Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы.|
|/virtualMachines/generalize/action|Задает состояние "Универсальная" для виртуальной машины и подготавливает ее для записи.|
|/virtualMachines/capture/action|Записывает виртуальную машину, копируя ее виртуальных жесткие диски, и создает шаблон, который может использоваться для создания идентичных виртуальных машин.|
|/virtualMachines/convertToManagedDisks/action|Преобразует диски на основе больших двоичных объектов виртуальной машины в управляемые диски.|
|/virtualMachines/vmSizes/read|Выводит список доступных размеров для обновления виртуальной машины.|
|/virtualMachines/instanceView/read|Возвращает состояние выполнения виртуальной машины и ее ресурсов.|
|/virtualMachines/extensions/read|Возвращает свойства расширения виртуальной машины.|
|/virtualMachines/extensions/write|Создает новое расширение виртуальной машины или обновляет существующее.|
|/virtualMachines/extensions/delete|Удаляет расширение виртуальной машины.|
|/locations/vmSizes/read|Выводит список доступных размеров виртуальных машин в расположении.|
|/locations/usages/read|Возвращает ограничения службы и текущее количество использованных вычислительных ресурсов подписки в расположении.|
|/locations/operations/read|Возвращает состояние асинхронной операции.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов реестра контейнеров и позволяет создавать реестры контейнеров.|
|/checknameavailability/read|Проверяет, является ли имя реестра допустимым и неиспользуемым.|
|/registries/read|Возвращает список реестров контейнеров или свойства указанного реестра контейнеров.|
|/registries/write|Создает реестр контейнеров с указанными параметрами либо обновляет свойства или теги указанного реестра контейнеров.|
|/registries/delete|Удаляет существующий реестр контейнеров.|
|/registries/listCredentials/action|Выводит список учетных данных входа для указанного реестра контейнеров.|
|/registries/regenerateCredential/action|Повторно создает учетные данные входа для указанного реестра контейнеров.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Операция | ОПИСАНИЕ |
|---|---|
|/containerServices/subscriptions/read|Возвращает указанные службы контейнеров по подписке.|
|/containerServices/resourceGroups/read|Возвращает указанные службы контейнеров по группе ресурсов.|
|/containerServices/resourceGroups/ContainerServiceName/read|Возвращает указанную службу контейнеров.|
|/containerServices/resourceGroups/ContainerServiceName/write|Размещает или обновляет указанную службу контейнеров.|
|/containerServices/resourceGroups/ContainerServiceName/delete|Удаляет указанную службу контейнеров.|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Операция | ОПИСАНИЕ |
|---|---|
|/updateCommunicationPreference/action|Обновляет параметры связи.|
|/listCommunicationPreference/action|Отображает параметры связи.|
|/applications/read|Операция чтения.|
|/applications/write|Операции записи.|
|/applications/write|Операции записи.|
|/applications/delete|Операция удаления.|
|/applications/listSecrets/action|Выводит список секретов.|
|/applications/listSingleSignOnToken/action|Считывает маркеры единого входа.|
|/operations/read|Считывает операции.|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Операция | ОПИСАНИЕ |
|---|---|
|/hubs/read|Считывает концентратор Azure Customer Insights.|
|/hubs/write|Создает или обновляет концентратор Azure Customer Insights.|
|/hubs/delete|Удаляет концентратор Azure Customer Insights.|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Возвращает доступные метрики для ресурса.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Возвращает доступные журналы для ресурса.|
|/hubs/authorizationPolicies/read|Считывает политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/write|Создает или обновляет политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/delete|Удаляет политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Повторно создает первичный ключ политики подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Повторно создает вторичный ключ политики подписанного URL-адреса Azure Customer Insights.|
|/hubs/profiles/read|Считывает профиль Azure Customer Insights.|
|/hubs/profiles/write|Записывает профиль Azure Customer Insights.|
|/hubs/kpi/read|Считывает ключевой показатель эффективности Azure Customer Insights.|
|/hubs/kpi/write|Создает или обновляет ключевой показатель эффективности Azure Customer Insights.|
|/hubs/kpi/delete|Удаляет ключевой показатель эффективности Azure Customer Insights.|
|/hubs/views/read|Считывает представление приложений Azure Customer Insights.|
|/hubs/views/write|Создает или обновляет представление приложений Azure Customer Insights.|
|/hubs/views/delete|Удаляет представление приложений Azure Customer Insights.|
|/hubs/interactions/read|Считывает взаимодействие Azure Customer Insights.|
|/hubs/interactions/write|Создает или обновляет взаимодействие Azure Customer Insights.|
|/hubs/roleAssignments/read|Считывает назначение RBAC для Azure Customer Insights.|
|/hubs/roleAssignments/write|Создает или обновляет назначение RBAC для Azure Customer Insights.|
|/hubs/roleAssignments/delete|Удаляет назначение RBAC для Azure Customer Insights.|
|/hubs/connectors/read|Считывает соединитель Azure Customer Insights.|
|/hubs/connectors/write|Создает или обновляет соединитель Azure Customer Insights.|
|/hubs/connectors/delete|Удаляет соединитель Azure Customer Insights.|
|/hubs/connectors/mappings/read|Считывает сопоставление соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/write|Создает или обновляет сопоставление соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/delete|Удаляет сопоставление соединителя Azure Customer Insights.|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность имени каталога для клиента.|
|/catalogs/read|Возвращает свойства каталога или каталогов в подписке или группе ресурсов.|
|/catalogs/write|Создает каталог или обновляет теги и свойства каталога.|
|/catalogs/delete|Удаляет каталог.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Операция | ОПИСАНИЕ |
|---|---|
|/datafactories/read|Считывает фабрику данных.|
|/datafactories/write|Создает или обновляет фабрику данных.|
|/datafactories/delete|Удаляет фабрику данных.|
|/datafactories/datapipelines/read|Считывает конвейер.|
|/datafactories/datapipelines/delete|Удаляет конвейер.|
|/datafactories/datapipelines/pause/action|Приостанавливает конвейер.|
|/datafactories/datapipelines/resume/action|Возобновляет работу конвейера.|
|/datafactories/datapipelines/update/action|Обновляет конвейер.|
|/datafactories/datapipelines/write|Создает или обновляет конвейер.|
|/datafactories/linkedServices/read|Считывает связанную службу.|
|/datafactories/linkedServices/delete|Удаляет связанную службу.|
|/datafactories/linkedServices/write|Создает или обновляет связанную службу.|
|/datafactories/tables/read|Считывает таблицу.|
|/datafactories/tables/delete|Удаляет таблицу.|
|/datafactories/tables/write|Создает или обновляет таблицу.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/read|Возвращает сведения об учетной записи Data Lake Analytics|
|/accounts/write|Создает или обновляет учетную запись Data Lake Analytics.|
|/accounts/delete|Удаляет учетную запись Data Lake Analytics.|
|/accounts/firewallRules/read|Возвращает сведения о правиле брандмауэра.|
|/accounts/firewallRules/write|Создает или обновляет правило брандмауэра.|
|/accounts/firewallRules/delete|Удаляет правило брандмауэра.|
|/accounts/storageAccounts/read|Возвращает связанную учетную запись хранения для учетной записи Data Lake Analytics.|
|/accounts/storageAccounts/write|Связывает учетную запись хранения с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/delete|Удаляет связь учетной записи хранения с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/Containers/read|Возвращает контейнеры в учетной записи хранения.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Выводит список маркеров SAS для контейнера службы хранилища.|
|/accounts/dataLakeStoreAccounts/read|Возвращает связанную учетную запись Data Lake Store для учетной записи Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/write|Связывает учетную запись Data Lake Store с учетной записью Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/delete|Удаляет связь учетной записи Data Lake Store с учетной записью Data Lake Analytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/read|Возвращает сведения о существовавшей учетной записи Data Lake Store.|
|/accounts/write|Создает новую учетную запись Data Lake Store или обновляет существующую.|
|/accounts/delete|Удаляет существующую учетную запись Data Lake Store.|
|/accounts/firewallRules/read|Возвращает сведения о правиле брандмауэра.|
|/accounts/firewallRules/write|Создает или обновляет правило брандмауэра.|
|/accounts/firewallRules/delete|Удаляет правило брандмауэра.|
|/accounts/trustedIdProviders/read|Возвращает сведения о доверенном поставщике удостоверений.|
|/accounts/trustedIdProviders/write|Создает или обновляет доверенный поставщик удостоверений.|
|/accounts/trustedIdProviders/delete|Удаляет доверенный поставщик удостоверений.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей.|
|/checkNameAvailability/Action|Проверяет, доступно ли имя Центра Интернета вещей.|
|/usages/Read|Возвращает данные об использовании подписки для данного поставщика.|
|/operations/Read|Возвращает все операции поставщика ресурсов.|
|/iotHubs/Read|Возвращает ресурсы Центра Интернета вещей.|
|/iotHubs/Write|Создает или обновляет ресурс Центра Интернета вещей.|
|/iotHubs/Delete|Удаляет ресурс Центра Интернета вещей.|
|/iotHubs/listkeys/Action|Возвращает все ключи Центра Интернета вещей.|
|/iotHubs/exportDevices/Action|Экспортирует устройства.|
|/iotHubs/importDevices/Action|Импортирует устройства.|
|/IotHubs/metricDefinitions/read|Возвращает доступные метрики для службы Центра Интернета вещей.|
|/iotHubs/iotHubKeys/listkeys/Action|Возвращает ключ Центра Интернета вещей для указанного имени.|
|/iotHubs/iotHubStats/Read|Возвращает статистику Центра Интернета вещей.|
|/iotHubs/quotaMetrics/Read|Возвращает метрики квоты.|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Создает группу потребителей концентратора событий.|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Возвращает группы потребителей концентратора событий.|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Удаляет группу потребителей концентратора событий.|
|/iotHubs/routing/routes/$testall/Action|Тестирует сообщение с помощью всех существующих маршрутов.|
|/iotHubs/routing/routes/$testnew/Action|Тестирует сообщение с помощью предоставленного тестового маршрута.|
|/IotHubs/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/IotHubs/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/iotHubs/skus/Read|Возвращает допустимые номера SKU Центра Интернета вещей.|
|/iotHubs/jobs/Read|Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей.|
|/iotHubs/routingEndpointsHealth/Read|Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Операция | ОПИСАНИЕ |
|---|---|
|/Subscription/register/action|Регистрирует подписку.|
|/labs/delete|Удаляет лаборатории.|
|/labs/read|Считывает лаборатории.|
|/labs/write|Добавляет или изменяет лаборатории.|
|/labs/ListVhds/action|Выводит список образов дисков, доступных для создания пользовательского образа.|
|/labs/GenerateUploadUri/action|Создает универсальный код ресурса (URI) для передачи настраиваемых образов дисков в лабораторию.|
|/labs/CreateEnvironment/action|Создает виртуальные машины в лаборатории.|
|/labs/ClaimAnyVm/action|Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории.|
|/labs/ExportResourceUsage/action|Экспортирует данные об использовании ресурсов лаборатории в учетную запись хранения.|
|/labs/users/delete|Удаляет профили пользователей.|
|/labs/users/read|Считывает профили пользователей.|
|/labs/users/write|Добавляет или изменяет профили пользователей.|
|/labs/users/secrets/delete|Удаляет секреты.|
|/labs/users/secrets/read|Считывает секреты.|
|/labs/users/secrets/write|Добавляет или изменяет секреты.|
|/labs/users/environments/delete|Удаляет среды.|
|/labs/users/environments/read|Считывает среды.|
|/labs/users/environments/write|Добавляет или изменяет среды.|
|/labs/users/disks/delete|Удаляет диски.|
|/labs/users/disks/read|Считывает диски.|
|/labs/users/disks/write|Добавляет или изменяет диски.|
|/labs/users/disks/Attach/action|Подключает диск к виртуальной машине и создает аренду для него.|
|/labs/users/disks/Detach/action|Отключает диск от виртуальной машины и прерывает его аренду.|
|/labs/customImages/delete|Удаляет пользовательские образы.|
|/labs/customImages/read|Считывает пользовательские образы.|
|/labs/customImages/write|Добавляет или изменяет пользовательские образы.|
|/labs/serviceRunners/delete|Удаляет средства выполнения служб.|
|/labs/serviceRunners/read|Считывает средства выполнения служб.|
|/labs/serviceRunners/write|Добавляет или изменяет средства выполнения служб.|
|/labs/artifactSources/delete|Удаляет источники артефактов.|
|/labs/artifactSources/read|Считывает источники артефактов.|
|/labs/artifactSources/write|Добавляет или изменяет источники артефактов.|
|/labs/artifactSources/artifacts/read|Считывает артефакты.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Создает шаблон ARM для заданного артефакта, передает необходимые файлы в учетную запись хранения и проверяет созданный артефакт.|
|/labs/artifactSources/armTemplates/read|Считывает шаблоны Azure Resource Manager.|
|/labs/costs/read|Считывает затраты.|
|/labs/costs/write|Добавляет или изменяет затраты.|
|/labs/virtualNetworks/delete|Удаляет виртуальные сети.|
|/labs/virtualNetworks/read|Считывает виртуальные сети.|
|/labs/virtualNetworks/write|Добавляет или изменяет виртуальные сети.|
|/labs/formulas/delete|Удаляет формулы.|
|/labs/formulas/read|Считывает формулы.|
|/labs/formulas/write|Добавляет или изменяет формулы.|
|/labs/schedules/delete|Удаляет расписания.|
|/labs/schedules/read|Считывает расписания.|
|/labs/schedules/write|Добавляет или изменяет расписания.|
|/labs/schedules/Execute/action|Выполняет расписание.|
|/labs/schedules/ListApplicable/action|Выводит список всех применимых расписаний.|
|/labs/galleryImages/read|Считывает образы из коллекции.|
|/labs/policySets/EvaluatePolicies/action|Оценивает политику лаборатории.|
|/labs/policySets/policies/delete|Удаляет политики.|
|/labs/policySets/policies/read|Считывает политики.|
|/labs/policySets/policies/write|Добавляет или изменяет политики.|
|/labs/virtualMachines/delete|Удаляет виртуальные машины.|
|/labs/virtualMachines/read|Считывает виртуальные машины.|
|/labs/virtualMachines/write|Добавляет или изменяет виртуальные машины.|
|/labs/virtualMachines/Start/action|Запускает виртуальную машину.|
|/labs/virtualMachines/Stop/action|Остановка виртуальной машины|
|/labs/virtualMachines/ApplyArtifacts/action|Применяет артефакты к виртуальной машине.|
|/labs/virtualMachines/AddDataDisk/action|Подключает новый или существующий диск данных к виртуальной машине.|
|/labs/virtualMachines/DetachDataDisk/action|Отключает указанный диск от виртуальной машины.|
|/labs/virtualMachines/Claim/action|Присваивает владение существующей виртуальной машиной.|
|/labs/virtualMachines/ListApplicableSchedules/action|Выводит список всех применимых расписаний.|
|/labs/virtualMachines/schedules/delete|Удаляет расписания.|
|/labs/virtualMachines/schedules/read|Считывает расписания.|
|/labs/virtualMachines/schedules/write|Добавляет или изменяет расписания.|
|/labs/virtualMachines/schedules/Execute/action|Выполняет расписание.|
|/labs/notificationChannels/delete|Удаляет каналы уведомлений.|
|/labs/notificationChannels/read|Считывает каналы уведомлений.|
|/labs/notificationChannels/write|Добавляет или изменяет каналы уведомлений.|
|/labs/notificationChannels/Notify/action|Отправляет уведомление в указанный канал.|
|/schedules/delete|Удаляет расписания.|
|/schedules/read|Считывает расписания.|
|/schedules/write|Добавляет или изменяет расписания.|
|/schedules/Execute/action|Выполняет расписание.|
|/schedules/Retarget/action|Обновляет идентификатор целевого ресурса расписания.|
|/locations/operations/read|Считывает операции.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Операция | ОПИСАНИЕ |
|---|---|
|/databaseAccountNames/read|Проверяет доступность имени.|
|/databaseAccounts/read|Считывает учетную запись базы данных.|
|/databaseAccounts/write|Обновляет учетные записи базы данных.|
|/databaseAccounts/listKeys/action|Выводит список ключей учетной записи базы данных.|
|/databaseAccounts/regenerateKey/action|Выполняет ротацию ключей учетной записи базы данных.|
|/databaseAccounts/listConnectionStrings/action|Возвращает строки подключения для учетной записи базы данных.|
|/databaseAccounts/changeResourceGroup/action|Изменяет группу ресурсов для учетной записи базы данных.|
|/databaseAccounts/failoverPriorityChange/action|Изменяет приоритеты отработки отказа для регионов учетной записи базы данных. Используется для выполнения операции отработки отказа вручную.|
|/databaseAccounts/delete|Удаляет учетные записи базы данных.|
|/databaseAccounts/metricDefinitions/read|Считывает определения метрик учетной записи базы данных.|
|/databaseAccounts/metrics/read|Считывает метрики учетной записи базы данных.|
|/databaseAccounts/usages/read|Считывает данные об использовании учетной записи базы данных.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Считывает определения метрик коллекции.|
|/databaseAccounts/databases/collections/metrics/read|Считывает метрики коллекции.|
|/databaseAccounts/databases/collections/usages/read|Считывает данные об использовании коллекции.|
|/databaseAccounts/databases/metricDefinitions/read|Считывает определения метрик базы данных.|
|/databaseAccounts/databases/metrics/read|Считывает метрики базы данных.|
|/databaseAccounts/databases/usages/read|Считывает данные об использовании базы данных.|
|/databaseAccounts/readonlykeys/read|Считывает ключи только для чтения учетной записи базы данных.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Операция | ОПИСАНИЕ |
|---|---|
|/generateSsoRequest/Action|Создает запрос на вход в центр управления доменами.|
|/validateDomainRegistrationInformation/Action|Проверяет объект покупки домена без его отправки.|
|/checkDomainAvailability/Action|Проверяет, доступен ли домен для приобретения.|
|/listDomainRecommendations/Action|Извлекает список рекомендуемых доменов на основе ключевых слов.|
|/register/action|Регистрирует поставщик ресурсов доменов Майкрософт для подписки.|
|/domains/Read|Возвращает список доменов.|
|/domains/Write|Добавляет новый домен или обновляет существующий.|
|/domains/Delete|Удаляет существующий домен.|
|/domains/operationresults/Read|Возвращает операцию домена.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Операция | ОПИСАНИЕ |
|---|---|
|/lcsprojects/read|Отображает проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю.|
|/lcsprojects/write|Создает или обновляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю. Можно обновить только свойства имени и описания. Невозможно обновить подписку и расположение, связанные с проектом, после его создания.|
|/lcsprojects/delete|Удаляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю.|
|/lcsprojects/clouddeployments/read|Отображает развертывания Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю.|
|/lcsprojects/clouddeployments/write|Создает развертывание Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю. Развертываниями можно управлять на портале управления Azure.|
|/lcsprojects/connectors/read|Считывает соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services.|
|/lcsprojects/connectors/write|Создает или обновляет соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services.|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/register/action|Регистрирует подписку для поставщика ресурсов концентратора событий и позволяет создавать ресурсы концентратора событий.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и состояние пространства имен.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/eventhubs/write|Создает или обновляет свойства концентратора событий.|
|/namespaces/eventhubs/read|Возвращает список описаний ресурсов концентратора событий.|
|/namespaces/eventhubs/Delete|Операция удаления ресурса концентратора событий.|
|/namespaces/eventHubs/consumergroups/write|Создает или обновляет свойства группы потребителей.|
|/namespaces/eventHubs/consumergroups/read|Возвращает список описаний ресурсов группы потребителей.|
|/namespaces/eventHubs/consumergroups/Delete|Операция удаления ресурса группы потребителей.|
|/namespaces/eventhubs/authorizationRules/read| Возвращает список правил авторизации концентратора событий.|
|/namespaces/eventhubs/authorizationRules/write|Создает правила авторизации концентратора событий и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/eventhubs/authorizationRules/delete|Операция удаления правил авторизации концентратора событий.|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Возвращает строку подключения к концентратору событий.|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/diagnosticSettings/read|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/diagnosticSettings/write|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/logDefinitions/read|Возвращает список описаний ресурсов журналов пространства имен.|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Операция | ОПИСАНИЕ |
|---|---|
|/providers/features/read|Возвращает функцию подписки в заданном поставщике ресурсов.|
|/providers/features/register/action|Регистрирует функцию для подписки в заданном поставщике ресурсов.|
|/features/read|Возвращает функции подписки.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Операция | ОПИСАНИЕ |
|---|---|
|/clusters/write|Создает или обновляет кластер HDInsight.|
|/clusters/read|Возвращает сведения о кластере HDInsight.|
|/clusters/delete|Удаляет кластер HDInsight.|
|/clusters/changerdpsetting/action|Изменяет параметры RDP для кластера HDInsight.|
|/clusters/configurations/action|Обновляет конфигурацию кластера HDInsight.|
|/clusters/configurations/read|Возвращает конфигурации кластеров HDInsight.|
|/clusters/roles/resize/action|Изменяет размер кластера HDInsight.|
|/locations/capabilities/read|Возвращает возможности подписки.|
|/locations/checkNameAvailability/read|Проверяет доступность имени.|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов импорта и экспорта и позволяет создавать задания импорта и экспорта.|
|/jobs/write|Создает задание с указанными параметрами либо обновляет свойства или теги указанного задания.|
|/jobs/read|Возвращает свойства для указанного задания или возвращает список заданий.|
|/jobs/listBitLockerKeys/action|Возвращает ключи BitLocker для указанного задания.|
|/jobs/delete|Удаляет существующее задание.|
|/locations/read|Возвращает свойства для указанного расположения или возвращает список расположений.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Операция | ОПИСАНИЕ |
|---|---|
|/Register/Action|Регистрирует поставщик Microsoft Insights.|
|/AlertRules/Write|Выполняет запись в конфигурацию правила генерации оповещений.|
|/AlertRules/Delete|Удаляет конфигурацию правила генерации оповещений.|
|/AlertRules/Read|Считывает конфигурацию правила генерации оповещений.|
|/AlertRules/Activated/Action|Правило генерации оповещений активировано.|
|/AlertRules/Resolved/Action|Правило генерации оповещений разрешено.|
|/AlertRules/Throttled/Action|Правило генерации оповещений отрегулировано.|
|/AlertRules/Incidents/Read|Считывает конфигурацию инцидентов правила генерации оповещений.|
|/MetricDefinitions/Read|Считывает определения метрик.|
|/eventtypes/values/Read|Считывает значения типов событий управления.|
|/eventtypes/digestevents/Read|Считывает дайджест типов событий управления.|
|/Metrics/Read|Считывает метрики.|
|/LogProfiles/Write|Выполняет запись в конфигурацию профиля журнала.|
|/LogProfiles/Delete|Удаляет конфигурацию профилей журнала.|
|/LogProfiles/Read|Считывает профили журналов.|
|/AutoscaleSettings/Write|Выполняет запись в конфигурацию параметров автомасштабирования.|
|/AutoscaleSettings/Delete|Удаляет конфигурацию параметров автомасштабирования.|
|/AutoscaleSettings/Read|Считывает конфигурацию параметров автомасштабирования.|
|/AutoscaleSettings/Scaleup/Action|Операция автоматического увеличения масштаба.|
|/AutoscaleSettings/Scaledown/Action|Операция автоматического уменьшения масштаба.|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Считывает определения метрик.|
|/ActivityLogAlerts/Activated/Action|Активирует оповещение журнала действий.|
|/DiagnosticSettings/Write|Выполняет запись в конфигурацию параметров диагностики.|
|/DiagnosticSettings/Delete|Удаляет конфигурацию параметров диагностики.|
|/DiagnosticSettings/Read|Считывает конфигурацию параметров диагностики.|
|/LogDefinitions/Read|Считывает определения журналов.|
|/ExtendedDiagnosticSettings/Write|Выполняет запись в конфигурацию дополнительных параметров диагностики.|
|/ExtendedDiagnosticSettings/Delete|Удаляет конфигурацию дополнительных параметров диагностики.|
|/ExtendedDiagnosticSettings/Read|Считывает конфигурацию дополнительных параметров диагностики.|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку.|
|/checkNameAvailability/read|Проверяет, является ли имя Key Vault допустимым и неиспользуемым.|
|/vaults/read|Отображает свойства Key Vault.|
|/vaults/write|Создает новое хранилище Key Vault или обновить свойства существующего.|
|/vaults/delete|Удаляет Key Vault.|
|/vaults/deploy/action|Включает доступ к секретам в Key Vault при развертывании ресурсов Azure.|
|/vaults/secrets/read|Отображает свойства секрета, но не его значение.|
|/vaults/secrets/write|Создает новый секрет или обновляет значение существующего.|
|/vaults/accessPolicies/write|Обновляет существующую политику доступа путем объединения, замены или добавления новой политики доступа в хранилище.|
|/deletedVaults/read|Отображает свойства обратимо удаленных хранилищ Key Vault.|
|/locations/operationResults/read|Проверяет результат длительной операции.|
|/locations/deletedVaults/read|Отображает свойства обратимо удаленного Key Vault.|
|/locations/deletedVaults/purge/action|Очищает обратимо удаленное хранилище Key Vault.|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Операция | ОПИСАНИЕ |
|---|---|
|/workflows/read|Считывает рабочий процесс.|
|/workflows/write|Создает или обновляет рабочий процесс.|
|/workflows/delete|Удаляет рабочий процесс.|
|/workflows/run/action|Начинает выполнение рабочего процесса.|
|/workflows/disable/action|Отключает рабочий процесс.|
|/workflows/enable/action|Включает рабочий процесс.|
|/workflows/validate/action|Проверяет рабочий процесс.|
|/workflows/move/action|Назначает рабочему процессу, для которого задан идентификатор существующей подписки, группа ресурсов и (или) имя, другой идентификатор подписки, группу ресурсов и (или) имя.|
|/workflows/listSwagger/action|Возвращает определения Swagger рабочего процесса.|
|/workflows/regenerateAccessKey/action|Повторно создает секреты ключей доступа.|
|/workflows/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для рабочего процесса.|
|/workflows/versions/read|Считывает версию рабочего процесса.|
|/workflows/versions/triggers/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для триггера.|
|/workflows/runs/read|Считывает данные о выполнении рабочего процесса.|
|/workflows/runs/cancel/action|Отменяет выполнение рабочего процесса.|
|/workflows/runs/actions/read|Считывает действие выполнения рабочего процесса.|
|/workflows/runs/operations/read|Считывает состояние операции выполнения рабочего процесса.|
|/workflows/triggers/read|Считывает триггер.|
|/workflows/triggers/run/action|Выполняет триггер.|
|/workflows/triggers/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для триггера.|
|/workflows/triggers/histories/read|Считывает журналы триггера.|
|/workflows/triggers/histories/resubmit/action|Повторяет отправку триггера рабочего процесса.|
|/workflows/accessKeys/read|Считывает ключ доступа.|
|/workflows/accessKeys/write|Создает или обновляет ключ доступа.|
|/workflows/accessKeys/delete|Удаляет ключ доступа.|
|/workflows/accessKeys/list/action|Выводит список секретов ключей доступа.|
|/workflows/accessKeys/regenerate/action|Повторно создает секреты ключей доступа.|
|/locations/workflows/validate/action|Проверяет рабочий процесс.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов веб-службы машинного обучения и позволяет создавать веб-службы.|
|/webServices/action|Создает свойства региональной веб-службы для поддерживаемых регионов.|
|/commitmentPlans/read|Считывает план предложения машинного обучения.|
|/commitmentPlans/write|Создает или обновляет план предложения машинного обучения.|
|/commitmentPlans/delete|Удаляет план предложения машинного обучения.|
|/commitmentPlans/join/action|Присоединяет план предложения машинного обучения.|
|/commitmentPlans/commitmentAssociations/read|Считывает связь плана предложения машинного обучения.|
|/commitmentPlans/commitmentAssociations/move/action|Перемещает связь плана предложения машинного обучения.|
|/Workspaces/read|Считывает рабочую область машинного обучения.|
|/Workspaces/write|Создает или обновляет рабочую область машинного обучения.|
|/Workspaces/delete|Удаляет рабочую область машинного обучения.|
|/Workspaces/listworkspacekeys/action|Выводит список ключей для рабочей области машинного обучения.|
|/Workspaces/resyncstoragekeys/action|Повторно синхронизирует ключи учетной записи хранения, настроенной для рабочей области машинного обучения.|
|/webServices/read|Считывает веб-службу машинного обучения.|
|/webServices/write|Создает или обновляет веб-службу машинного обучения.|
|/webServices/delete|Удаляет веб-службу машинного обучения.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Операция | ОПИСАНИЕ |
|---|---|
|/agreements/offers/plans/read|Возвращает соглашение для заданного элемента Marketplace.|
|/agreements/offers/plans/sign/action|Подписывает соглашение для заданного элемента Marketplace.|
|/agreements/offers/plans/cancel/action|Отменяет соглашение для заданного элемента Marketplace.|

## <a name="microsoftmedia"></a>Microsoft.Media

| Операция | ОПИСАНИЕ |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network.

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку.|
|/unregister/action|Отменяет регистрацию подписки.|
|/checkTrafficManagerNameAvailability/action|Проверяет доступность относительного DNS-имени диспетчера трафика.|
|/dnszones/read|Возвращает зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. Обратите внимание, что эта команда не извлекает наборы записей, содержащиеся в зоне.|
|/dnszones/write|Создает или обновляет зону DNS в группе ресурсов.  Используется для обновления тегов в ресурсе зоны DNS. Обратите внимание, что эту команду невозможно использовать для создания или обновления наборов записей в зоне.|
|/dnszones/delete|Удаляет зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets.|
|/dnszones/MX/read|Возвращает набор записей типа MX в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/MX/write|Создает или обновляет набора записей типа MX в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/MX/delete|Удаляет из зоны DNS набор записей типа MX с заданным именем.|
|/dnszones/NS/read|Возвращает набор записей DNS типа NS.|
|/dnszones/NS/write|Создает или обновляет набор записей DNS типа NS.|
|/dnszones/NS/delete|Удаляет набор записей DNS типа NS.|
|/dnszones/AAAA/read|Возвращает набор записей типа AAAA в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/AAAA/write|Создает или обновляет набора записей типа AAAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/AAAA/delete|Удаляет из зоны DNS набор записей типа AAAA с заданным именем.|
|/dnszones/CNAME/read|Возвращает набор записей типа CNAME в формате JSON. Набор записей содержит срок жизни, теги и ETag.|
|/dnszones/CNAME/write|Создает или обновляет набора записей типа CNAME в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/CNAME/delete|Удаляет из зоны DNS набор записей типа CNAME с заданным именем.|
|/dnszones/SOA/read|Возвращает набор записей DNS типа SOA.|
|/dnszones/SOA/write|Создает или обновляет набор записей DNS типа SOA.|
|/dnszones/SRV/read|Возвращает набор записей типа SRV в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/SRV/write|Создает или обновляет набор записей типа SRV.|
|/dnszones/SRV/delete|Удаляет из зоны DNS набор записей типа SRV с заданным именем.|
|/dnszones/PTR/read|Возвращает набор записей типа PTR в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/PTR/write|Создает или обновляет набора записей типа PTR в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/PTR/delete|Удаляет из зоны DNS набор записей типа PTR с заданным именем.|
|/dnszones/A/read|Возвращает набор записей типа A в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/A/write|Создает или обновляет набора записей типа A в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/A/delete|Удаляет из зоны DNS набор записей типа A с заданным именем.|
|/dnszones/TXT/read|Возвращает набор записей типа TXT в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/TXT/write|Создает или обновляет набора записей типа TXT в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/TXT/delete|Удаляет из зоны DNS набор записей типа TXT с заданным именем.|
|/dnszones/recordsets/read|Возвращает набор записей DNS разного типа.|
|/networkInterfaces/read|Возвращает определение сетевого интерфейса. |
|/networkInterfaces/write|Создает новый сетевой интерфейс или обновляет существующий. |
|/networkInterfaces/join/action|Подключает виртуальную машину к сетевому интерфейсу.|
|/networkInterfaces/delete|Удаляет сетевой интерфейс.|
|/networkInterfaces/effectiveRouteTable/action|Возвращает таблицу маршрутов, настроенную для сетевого интерфейса виртуальной машины.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Возвращает группы безопасности сети, настроенные для сетевого интерфейса виртуальной машины.|
|/networkInterfaces/loadBalancers/read|Возвращает все подсистемы балансировки нагрузки, в которых используется сетевой интерфейс.|
|/networkInterfaces/ipconfigurations/read|Возвращает определение IP-конфигурации сетевого интерфейса. |
|/publicIPAddresses/read|Возвращает определение общедоступного IP-адреса.|
|/publicIPAddresses/write|Создает новый общедоступный IP-адрес или обновляет существующий. |
|/publicIPAddresses/delete|Удаляет общедоступный IP-адрес.|
|/publicIPAddresses/join/action|Присоединяет общедоступный IP-адрес.|
|/routeFilters/read|Возвращает определение фильтра маршрутов.|
|/routeFilters/join/action|Присоединяет фильтр маршрутов.|
|/routeFilters/delete|Удаляет определение фильтра маршрутов.|
|/routeFilters/write|Создает новый фильтр маршрутов или обновляет существующий.|
|/routeFilters/rules/read|Возвращает определение правила фильтра маршрутов.|
|/routeFilters/rules/write|Создает новое правило фильтра маршрутов или обновляет существующее.|
|/routeFilters/rules/delete|Удаляет определение правила фильтра маршрутов.|
|/networkWatchers/read|Возвращает определение Наблюдателя за сетями.|
|/networkWatchers/write|Создает новый Наблюдатель за сетями или обновляет существующий.|
|/networkWatchers/delete|Удаляет Наблюдатель за сетями.|
|/networkWatchers/configureFlowLog/action|Настраивает ведение журнала потоков для целевого ресурса.|
|/networkWatchers/ipFlowVerify/action|Проверяет, разрешена ли передача или прием пакета для определенного места назначения.|
|/networkWatchers/nextHop/action|Для указанного целевого объекта и конечного IP-адреса возвращает тип следующего прыжка и его IP-адрес.|
|/networkWatchers/queryFlowLogStatus/action|Возвращает состояние ведения журнала потоков для ресурса.|
|/networkWatchers/queryTroubleshootResult/action|Возвращает результат устранения неполадок предыдущей или текущей операции по устранению неполадок.|
|/networkWatchers/securityGroupView/action|Отображает настроенные и действующие правила группы безопасности сети для виртуальной машины.|
|/networkWatchers/topology/action|Возвращает представление уровня сети для ресурсов и их связей в группе ресурсов.|
|/networkWatchers/troubleshoot/action|Начинает устранение неполадок сетевого ресурса в Azure.|
|/networkWatchers/packetCaptures/queryStatus/action|Возвращает сведения о свойствах и состоянии ресурса записи пакетов.|
|/networkWatchers/packetCaptures/stop/action|Останавливает выполняемый сеанс записи пакетов.|
|/networkWatchers/packetCaptures/read|Возвращает определение записи пакетов.|
|/networkWatchers/packetCaptures/write|Создает запись пакетов.|
|/networkWatchers/packetCaptures/delete|Удаляет запись пакетов.|
|/loadBalancers/read|Возвращает определение подсистемы балансировки нагрузки.|
|/loadBalancers/write|Создает новую подсистему балансировки нагрузки или обновляет существующую.|
|/loadBalancers/delete|Удаляет подсистему балансировки нагрузки.|
|/loadBalancers/networkInterfaces/read|Возвращает ссылки на все сетевые интерфейсы в подсистеме балансировки нагрузки.|
|/loadBalancers/loadBalancingRules/read|Возвращает определение правила подсистемы балансировки нагрузки.|
|/loadBalancers/backendAddressPools/read|Возвращает определение внутреннего пула адресов подсистемы балансировки нагрузки.|
|/loadBalancers/backendAddressPools/join/action|Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatPools/read|Возвращает определение пула входящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatPools/join/action|Присоединяет пул входящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/read|Возвращает определение правила NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/write|Создает новое правило NAT для входящего трафика подсистемы балансировки нагрузки или обновляет существующее.|
|/loadBalancers/inboundNatRules/delete|Удаляет правило NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/join/action|Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/outboundNatRules/read|Возвращает определение правила пула исходящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/probes/read|Возвращает пробу подсистемы балансировки нагрузки.|
|/loadBalancers/virtualMachines/read|Возвращает ссылки на все виртуальные машины в подсистеме балансировки нагрузки.|
|/loadBalancers/frontendIPConfigurations/read|Возвращает определение внешней IP-конфигурации подсистемы балансировки нагрузки.|
|/trafficManagerGeographicHierarchies/read|Возвращает географическую иерархию диспетчера трафика, содержащую регионы, которые могут быть использованы для метода географической маршрутизации трафика.|
|/bgpServiceCommunities/read|Возвращает сообщества службы BGP.|
|/applicationGatewayAvailableWafRuleSets/read|Возвращает доступные наборы правил WAF шлюза приложений.|
|/virtualNetworks/read|Возвращает определение виртуальной сети.|
|/virtualNetworks/write|Создает новую виртуальную сеть или обновляет существующую.|
|/virtualNetworks/delete|Удаляет виртуальную сеть.|
|/virtualNetworks/peer/action|Создает пиринг между виртуальными сетями.|
|/virtualNetworks/virtualNetworkPeerings/read|Возвращает определение пиринга виртуальной сети.|
|/virtualNetworks/virtualNetworkPeerings/write|Создает новый пиринг виртуальной сети или обновляет существующий.|
|/virtualNetworks/virtualNetworkPeerings/delete|Удаляет пиринг виртуальной сети.|
|/virtualNetworks/subnets/read|Возвращает определение подсети виртуальной сети.|
|/virtualNetworks/subnets/write|Создает новую подсеть пиринг виртуальной сети или обновляет существующую.|
|/virtualNetworks/subnets/delete|Удаляет подсеть виртуальной сети.|
|/virtualNetworks/subnets/join/action|Присоединяет виртуальную сеть.|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|Присоединяет ресурс, например учетную запись хранения или базу данных SQL, к подсети с поддержкой туннелирования службы.|
|/virtualNetworks/subnets/virtualMachines/read|Возвращает ссылки на все виртуальные машины в подсети виртуальной сети.|
|/virtualNetworks/checkIpAddressAvailability/read|Проверяет, доступен ли IP-адрес в указанной виртуальной сети.|
|/virtualNetworks/virtualMachines/read|Возвращает ссылки на все виртуальные машины в виртуальной сети.|
|/expressRouteServiceProviders/read|Возвращает поставщики службы ExpressRoute.|
|/dnsoperationresults/read|Возвращает результаты операции DNS.|
|/localnetworkgateways/read|Возвращает шлюз локальной сети.|
|/localnetworkgateways/write|Создает новый шлюз локальной сети или обновляет существующий.|
|/localnetworkgateways/delete|Удаляет шлюз локальной сети.|
|/trafficManagerProfiles/read|Возвращает конфигурацию профиля диспетчера трафика. Он содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек, маршрутизируемых данным профилем диспетчера трафика.|
|/trafficManagerProfiles/write|Создает профиль диспетчера трафика или изменяет конфигурацию существующего профиля диспетчера трафика. Сюда входит включение или отключение профиля и изменение параметров DNS, параметров маршрутизации трафика или параметров мониторинга конечных точек. Конечные точки, маршрутизируемые с помощью профиля диспетчера трафика, можно добавлять, удалять, включать и отключать.|
|/trafficManagerProfiles/delete|Удаляет профиль диспетчера трафика. Будут утрачены все параметры, связанные с профилем диспетчера трафика, и профиль больше не сможет использоваться для маршрутизации трафика.|
|/dnsoperationstatuses/read|Возвращает состояние операции DNS. |
|/operations/read|Возвращает доступные операции.|
|/expressRouteCircuits/read|Возвращает канал ExpressRoute.|
|/expressRouteCircuits/write|Создает новый канал ExpressRoute или обновляет существующий.|
|/expressRouteCircuits/delete|Удаляет канал ExpressRoute.|
|/expressRouteCircuits/stats/read|Возвращает статистику канала ExpressRoute.|
|/expressRouteCircuits/peerings/read|Возвращает пиринг канала ExpressRoute.|
|/expressRouteCircuits/peerings/write|Создает новый пиринг канала ExpressRoute или обновляет существующий.|
|/expressRouteCircuits/peerings/delete|Удаляет пиринг канала ExpressRoute.|
|/expressRouteCircuits/peerings/arpTables/action|Возвращает таблицу ARP для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/routeTables/action|Возвращает таблицу маршрутов для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|Возвращает сводку по таблице маршрутов для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/stats/read|Возвращает статистику пиринга канала ExpressRoute.|
|/expressRouteCircuits/authorizations/read|Возвращает данные авторизации канала ExpressRoute.|
|/expressRouteCircuits/authorizations/write|Создает новые данные авторизации канала ExpressRoute или обновляет существующие.|
|/expressRouteCircuits/authorizations/delete|Удаляет данные авторизации канала ExpressRoute.|
|/connections/read|Возвращает подключение шлюза виртуальной сети.|
|/connections/write|Создает новое подключение шлюза виртуальной сети или обновляет существующее.|
|/connections/delete|Удаляет подключение шлюза виртуальной сети.|
|/connections/sharedKey/read|Возвращает общий ключ для подключения шлюза виртуальной сети.|
|/connections/sharedKey/write|Создает новый общий ключ для подключения шлюза виртуальной сети или обновляет существующий.|
|/networkSecurityGroups/read|Возвращает определение группы безопасности сети.|
|/networkSecurityGroups/write|Создает новую группу безопасности сети или обновляет существующую.|
|/networkSecurityGroups/delete|Удаляет группу безопасности сети.|
|/networkSecurityGroups/join/action|Присоединяет группу безопасности сети.|
|/networkSecurityGroups/defaultSecurityRules/read|Возвращает определение правила безопасности по умолчанию.|
|/networkSecurityGroups/securityRules/read|Возвращает определение правила безопасности.|
|/networkSecurityGroups/securityRules/write|Создает новое правило безопасности или обновляет существующее.|
|/networkSecurityGroups/securityRules/delete|Удаляет правило безопасности.|
|/applicationGateways/read|Возвращает шлюз приложений.|
|/applicationGateways/write|Создает новый шлюз приложений или обновляет существующий.|
|/applicationGateways/delete|Удаляет шлюз приложений.|
|/applicationGateways/backendhealth/action|Возвращает сведения о работоспособности серверной части шлюза приложений.|
|/applicationGateways/start/action|Запускает шлюз приложений.|
|/applicationGateways/stop/action|Останавливает шлюз приложений.|
|/applicationGateways/backendAddressPools/join/action|Выполняет присоединение к внутреннему пулу адресов шлюза приложений.|
|/routeTables/read|Возвращает определение таблицы маршрутов.|
|/routeTables/write|Создает новую таблицу маршрутов или обновляет существующую.|
|/routeTables/delete|Удаляет определение таблицы маршрутов.|
|/routeTables/join/action|Присоединяет таблицу маршрутов.|
|/routeTables/routes/read|Возвращает определение маршрута.|
|/routeTables/routes/write|Создает новый маршрут или обновляет существующий.|
|/routeTables/routes/delete|Удаляет определение маршрута.|
|/locations/operationResults/read|Возвращает результат асинхронной операции POST или DELETE.|
|/locations/checkDnsNameAvailability/read|Проверяет, доступна ли метка DNS в указанном расположении.|
|/locations/usages/read|Возвращает метрики использования ресурсов.|
|/locations/operations/read|Возвращает ресурс операции, представляющий состояние асинхронной операции.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов центров уведомлений и позволяет создавать пространства имен и центры уведомлений.|
|/CheckNamespaceAvailability/action|Проверяет, доступно ли имя заданного ресурса пространства имен в службе центра уведомлений.|
|/Namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и состояние пространства имен.|
|/Namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/Namespaces/Delete|Удаляет ресурс пространства имен.|
|/Namespaces/authorizationRules/action|Возвращает список описаний правил авторизации пространства имен.|
|/Namespaces/CheckNotificationHubAvailability/action|Проверяет, доступно ли заданное имя центра уведомлений в пространстве имен.|
|/Namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/Namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/Namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/Namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/Namespaces/authorizationRules/regenerateKeys/action|Правило авторизации пространства имен повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно.|
|/Namespaces/NotificationHubs/write|Создает центр уведомлений и обновляет его свойства. К этим свойствам в основном относятся учетные данные PNS. Правила авторизации и срок жизни.|
|/Namespaces/NotificationHubs/read|Возвращает список описаний ресурсов центра уведомлений.|
|/Namespaces/NotificationHubs/Delete|Удаляет ресурс центра уведомлений.|
|/Namespaces/NotificationHubs/authorizationRules/action|Возвращает список правил авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/pnsCredentials/action|Возвращает все учетные данные PNS центра уведомлений. Сюда входят учетные данные WNS, MPNS, APNS, GCM и Baidu.|
|/Namespaces/NotificationHubs/debugSend/action|Отправляет тестовое push-уведомление.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Создает правила авторизации центра уведомлений и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Возвращает список правил авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Удаляет правила авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Возвращает строку подключения к центру уведомлений.|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|Правило авторизации центра уведомлений повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов.|
|/linkTargets/read|Выводит список существующих учетных записей, которые не связаны с подпиской Azure. Чтобы связать эту подписку Azure с рабочей областью, укажите идентификатор клиента, возвращаемый этой операцией, в свойстве идентификатора клиента в операции создания рабочей области.|
|/workspaces/write|Создает новую рабочую область или устанавливает связь с существующей рабочей областью с помощью указанного идентификатора клиента, полученного из нее.|
|/workspaces/read|Возвращает существующую рабочую область.|
|/workspaces/delete|Удаляет рабочую область. Если при создании рабочая область была связана с существующей рабочей областью, то эта связанная рабочая область не удаляется.|
|/workspaces/generateregistrationcertificate/action|Создает сертификат регистрации для рабочей области. Этот сертификат используется для подключения Microsoft System Center Operations Manager к рабочей области.|
|/workspaces/sharedKeys/action|Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/search/action|Выполняет поисковый запрос.|
|/workspaces/datasources/read|Возвращает источники данных в рабочей области.|
|/workspaces/datasources/write|Создает или обновляет источники данных в рабочей области.|
|/workspaces/datasources/delete|Удаляет источники данных в рабочей области.|
|/workspaces/managementGroups/read|Возвращает имена и метаданные для групп управления System Center Operations Manager, подключенных к этой рабочей области.|
|/workspaces/schema/read|Возвращает схему поиска для рабочей области.  Схема поиска содержит предоставленные поля и их типы.|
|/workspaces/usages/read|Возвращает данные об использовании для рабочей области, включая объем данных, считываемых рабочей областью.|
|/workspaces/intelligencepacks/read|Выводит список всех пакетов аналитики, которые являются видимыми для заданной рабочей области, а также сведения о том, включен или отключен пакет для этой рабочей области.|
|/workspaces/intelligencepacks/enable/action|Включает пакет аналитики для заданной рабочей области.|
|/workspaces/intelligencepacks/disable/action|Отключает пакет аналитики для заданной рабочей области.|
|/workspaces/sharedKeys/read|Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/savedSearches/read|Возвращает сохраненный поисковый запрос.|
|/workspaces/savedSearches/write|Создает сохраненный поисковый запрос.|
|/workspaces/savedSearches/delete|Удаляет сохраненный поисковый запрос.|
|/workspaces/storageinsightconfigs/write|Создает конфигурацию учетной записи хранения. Эти конфигурации используются для извлечения данных из расположения в существующей учетной записи хранения.|
|/workspaces/storageinsightconfigs/read|Возвращает конфигурацию хранилища.|
|/workspaces/storageinsightconfigs/delete|Удаляет конфигурацию хранилища. Это остановит чтение данных из учетной записи хранения, выполняемое Microsoft Operational Insights.|
|/workspaces/configurationScopes/read|Возвращает область конфигурации.|
|/workspaces/configurationScopes/write|Задает область конфигурации.|
|/workspaces/configurationScopes/delete|Удаляет область конфигурации.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов.|
|/solutions/write|Создает решение OMS.|
|/solutions/read|Возвращает существующее решение OMS.|
|/solutions/delete|Удаляет существующее решение OMS.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Операция | ОПИСАНИЕ |
|---|---|
|/Vaults/backupJobsExport/action|Экспортирует задания.|
|/Vaults/write|Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище".|
|/Vaults/read|Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище".|
|/Vaults/delete|Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище".|
|/Vaults/refreshContainers/read|Обновляет список контейнеров.|
|/Vaults/backupJobsExport/operationResults/read|Возвращает результат операции задания экспорта.|
|/Vaults/backupOperationResults/read|Возвращает результат операции архивации для хранилища служб восстановления.|
|/Vaults/monitoringAlerts/read|Возвращает оповещения для хранилища служб восстановления.|
|/Vaults/monitoringAlerts/{уникальный_ИД_оповещения}/read|Возвращает сведения об оповещении.|
|/Vaults/backupSecurityPIN/read|Возвращает данные ПИН-кода безопасности для хранилища служб восстановления.|
|/vaults/replicationEvents/read|Считывает события.|
|/Vaults/backupProtectableItems/read|Возвращает список всех защищаемых элементов.|
|/vaults/replicationFabrics/read|Считывает структуры.|
|/vaults/replicationFabrics/write|Создает или обновляет структуры.|
|/vaults/replicationFabrics/remove/action|Удаляет структуру.|
|/vaults/replicationFabrics/checkConsistency/action|Проверяет согласованность структуры.|
|/vaults/replicationFabrics/delete|Удаляет структуры.|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|Развертывает образ сервера обработки.|
|/vaults/replicationFabrics/reassociateGateway/action|Повторно задает связь шлюза.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Считывает поставщики служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Удаляет поставщик служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>удалить|Удаляет поставщики служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|Обновляет поставщик.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Считывает классификации хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|Считывает сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|Создает или обновляет сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|Удаляет сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationvCenters/read|Считывает задания.|
|/vaults/replicationFabrics/replicationvCenters/write|Создает или обновляет задания.|
|/vaults/replicationFabrics/replicationvCenters/delete|Удаляет задания.|
|/vaults/replicationFabrics/replicationNetworks/read|Считывает сети.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|Считывает сетевые сопоставления.|
|/vaults/replicationFabrics/replicationNetworks/<br>/write|Создает или обновляет сетевые сопоставления.|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|Удаляет сетевые сопоставления.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|Считывает контейнеры защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|Обнаруживает защищаемый элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|Создает или обновляет контейнеры защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|Удаляет контейнер защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|Переключает контейнер защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|Считывает защищаемые элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|Считывает сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|Создает или обновляет сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|Удаляет сопоставление контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|Удаляет сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|Считывает защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|Создает или обновляет защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|Удаляет защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|Удаляет защищенный элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|Выполняет плановую отработку отказа.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|Отработка отказа|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Тестирование отработки отказа|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|Тестирует очистку отработки отказа.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|Выполняет отработку отказа с фиксацией.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|Повторно защищает защищенный элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|Обновляет службу Mobility Service.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|Исправляет репликацию.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|Применяет точку восстановления.|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|Считывает точки восстановления для репликации.|
|/vaults/replicationPolicies/read|Считывает политики.|
|/vaults/replicationPolicies/write|Создает или обновляет политики.|
|/vaults/replicationPolicies/delete|Удаляет политики.|
|/vaults/replicationRecoveryPlans/read|Считывает планы восстановления.|
|/vaults/replicationRecoveryPlans/write|Создает или обновляет планы восстановления.|
|/vaults/replicationRecoveryPlans/delete|Удаляет планы восстановления.|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Выполняет плановую отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Выполняет отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/testFailover/action|Тестирует отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Тестирует очистку отработки отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Выполняет отработку отказа с фиксацией для плана восстановления.|
|/vaults/replicationRecoveryPlans/reProtect/action|Повторно защищает план восстановления.|
|/Vaults/extendedInformation/read|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/extendedInformation/write|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/extendedInformation/delete|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/backupManagementMetaData/read|Возвращает метаданные управления архивацией для хранилища служб восстановления.|
|/Vaults/backupProtectionContainers/read|Возвращает все контейнеры, принадлежащие подписке.|
|/Vaults/backupFabrics/operationResults/read|Возвращает состояние операции.|
|/Vaults/backupFabrics/protectionContainers/read|Возвращает все зарегистрированные контейнеры.|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|Возвращает результат операции, выполненной с контейнером защиты.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|Возвращает сведения об объекте для защищенного элемента.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|Создает элемент, защищенный службой архивации.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|Удаляет защищенный элемент.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|Архивирует защищенный элемент.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|Возвращает результат операции, выполненной с защищенными элементами.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|Возвращает состояние операции, выполненной с защищенными элементами.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|Возвращает точки восстановления для защищенных элементов.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|Восстанавливает точки восстановления для защищенных элементов.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|Подготавливает мгновенное восстановление для защищенного элемента.|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|Отменяет мгновенное восстановление для защищенного элемента.|
|/Vaults/usages/read|Возвращает данные об использовании хранилища служб восстановления.|
|/vaults/usages/read|Считывает данные об использовании хранилищ.|
|/Vaults/certificates/write|Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища.|
|/Vaults/tokenInfo/read|Возвращает сведения о маркере для хранилища служб восстановления.|
|/vaults/replicationAlertSettings/read|Считывает параметры оповещений.|
|/vaults/replicationAlertSettings/write|Создает или обновляет параметры оповещений.|
|/Vaults/backupOperations/read|Возвращает состояние операции архивации для хранилища служб восстановления.|
|/Vaults/storageConfig/read|Возвращает конфигурацию службы хранилища для хранилища служб восстановления.|
|/Vaults/storageConfig/write|Обновляет конфигурацию службы хранилища для хранилища служб восстановления.|
|/Vaults/backupUsageSummaries/read|Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления.|
|/Vaults/backupProtectedItems/read|Возвращает список всех защищенных элементов.|
|/Vaults/backupconfig/vaultconfig/read|Возвращает конфигурацию хранилища служб восстановления.|
|/Vaults/backupconfig/vaultconfig/write|Обновляет конфигурацию хранилища служб восстановления.|
|/Vaults/registeredIdentities/write|Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления.|
|/Vaults/registeredIdentities/read|Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса.|
|/Vaults/registeredIdentities/delete|Операцию отмены регистрации контейнера можно использовать для отмены регистрации контейнера.|
|/Vaults/registeredIdentities/operationResults/read|Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции.|
|/vaults/replicationJobs/read|Считывает задания.|
|/vaults/replicationJobs/cancel/action|Отменяет задание.|
|/vaults/replicationJobs/restart/action|Перезапускает задание.|
|/vaults/replicationJobs/resume/action|Возобновляет выполнение задания.|
|/Vaults/backupPolicies/read|Возвращает все политики защиты.|
|/Vaults/backupPolicies/write|Создает политику защиты.|
|/Vaults/backupPolicies/delete|Удаляет политику защиты.|
|/Vaults/backupPolicies/operationResults/read|Возвращает результаты операции политики.|
|/Vaults/backupPolicies/operationStatus/read|Возвращает состояние операции политики.|
|/Vaults/vaultTokens/read|Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища.|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Возвращает конфигурацию уведомлений хранилища служб восстановления.|
|/Vaults/backupJobs/read|Возвращает все объекты заданий.|
|/Vaults/backupJobs/cancel/action|Отменяет задание.|
|/Vaults/backupJobs/operationResults/read|Возвращает результат операции задания.|
|/locations/allocateStamp/action|AllocatedStamp является внутренней операцией, используемой службой.|
|/locations/allocatedStamp/read|GetAllocatedStamp является внутренней операцией, используемой службой.|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNamespaceAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/register/action|Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и состояние пространства имен.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/HybridConnections/write|Создает или обновляет свойства гибридного подключения.|
|/namespaces/HybridConnections/read|Возвращает список описаний ресурсов гибридного подключения.|
|/namespaces/HybridConnections/Delete|Операция удаления ресурса гибридного подключения.|
|/namespaces/HybridConnections/authorizationRules/write|Создает правила авторизации гибридного подключения и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/HybridConnections/authorizationRules/delete|Операция удаления правил авторизации гибридного подключения.|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Возвращает строку подключения для гибридного подключения.|
|/namespaces/WcfRelays/write|Создает или обновляет свойства ретранслятора WCF.|
|/namespaces/WcfRelays/read|Возвращает список описаний ресурсов ретранслятора WCF.|
|/namespaces/WcfRelays/Delete|Операция удаления ресурса ретранслятора WCF.|
|/namespaces/WcfRelays/authorizationRules/write|Создает правила авторизации ретранслятора WCF и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/WcfRelays/authorizationRules/delete|Операция удаления правил авторизации ретранслятора WCF.|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Возвращает строку подключения к ретранслятору WCF.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Операция | ОПИСАНИЕ |
|---|---|
|/AvailabilityStatuses/read|Возвращает состояния доступности для всех ресурсов в указанной области.|
|/AvailabilityStatuses/current/read|Возвращает состояние доступности для указанного ресурса.|

## <a name="microsoftresources"></a>Microsoft.Resources

| Операция | ОПИСАНИЕ |
|---|---|
|/checkResourceName/action|Проверяет допустимость имени ресурса.|
|/providers/read|Возвращает список поставщиков.|
|/subscriptions/read|Возвращает список подписок.|
|/subscriptions/operationresults/read|Возвращает результаты операции подписки.|
|/subscriptions/providers/read|Возвращает поставщики ресурсов или выводит их список.|
|/subscriptions/tagNames/read|Возвращает теги подписки или выводит их список.|
|/subscriptions/tagNames/write|Добавляет тег подписки.|
|/subscriptions/tagNames/delete|Удаляет тег подписки.|
|/subscriptions/tagNames/tagValues/read|Возвращает значения тегов подписки или выводит их список.|
|/subscriptions/tagNames/tagValues/write|Добавляет значение тега подписки.|
|/subscriptions/tagNames/tagValues/delete|Удаляет значение тега подписки.|
|/subscriptions/resources/read|Возвращает ресурсы подписки.|
|/subscriptions/resourceGroups/read|Возвращает группы ресурсов или выводит их список.|
|/subscriptions/resourceGroups/write|Создает или обновляет группу ресурсов.|
|/subscriptions/resourceGroups/delete|Удаляет группу ресурсов со всеми ресурсами.|
|/subscriptions/resourceGroups/moveResources/action|Перемещает ресурсы из одной группы ресурсов в другую.|
|/subscriptions/resourceGroups/validateMoveResources/action|Проверяет перемещение ресурсов из одной группы ресурсов в другую.|
|/subscriptions/resourcegroups/resources/read|Возвращает ресурсы группы ресурсов.|
|/subscriptions/resourcegroups/deployments/read|Возвращает развернутые службы или выводит их список.|
|/subscriptions/resourcegroups/deployments/write|Создает или обновляет развертывание.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Возвращает состояния операций развертывания или выводит их список.|
|/subscriptions/resourcegroups/deployments/operations/read|Возвращает операции развертывания или выводит их список.|
|/subscriptions/locations/read|Возвращает список поддерживаемых расположений.|
|/links/read|Возвращает ссылки на ресурсы или выводит их список.|
|/links/write|Создает или обновляет ссылку на ресурс.|
|/links/delete|Удаляет ссылку на ресурс.|
|/tenants/read|Возвращает список клиентов.|
|/resources/read|Возвращает список ресурсов на основе фильтров.|
|/deployments/read|Возвращает развернутые службы или выводит их список.|
|/deployments/write|Создает или обновляет развертывание.|
|/deployments/delete|Удаляет развертывание.|
|/deployments/cancel/action|Отменяет развертывание.|
|/deployments/validate/action|Проверяет развертывание.|
|/deployments/operations/read|Возвращает операции развертывания или выводит их список.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Операция | ОПИСАНИЕ |
|---|---|
|/jobcollections/read|Возвращает коллекцию заданий.|
|/jobcollections/write|Создает или обновляет коллекцию заданий.|
|/jobcollections/delete|Удаляет коллекцию заданий.|
|/jobcollections/enable/action|Включает коллекцию заданий.|
|/jobcollections/disable/action|Отключает коллекцию заданий.|
|/jobcollections/jobs/read|Возвращает задание.|
|/jobcollections/jobs/write|Создает или обновляет задание.|
|/jobcollections/jobs/delete|Удаляет задание.|
|/jobcollections/jobs/run/action|Выполняет задание.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Создает определение приложения логики на основе задания планировщика.|
|/jobcollections/jobs/jobhistories/read|Получает журнал заданий.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов службы поиска и позволяет создавать службы поиска.|
|/checkNameAvailability/action|Проверяет доступность имени службы.|
|/searchServices/write|Создает или обновляет службу поиска.|
|/searchServices/read|Считывает службу поиска.|
|/searchServices/delete|Удаляет службу поиска.|
|/searchServices/start/action|Запускает службу поиска.|
|/searchServices/stop/action|Останавливает службу поиска.|
|/searchServices/listAdminKeys/action|Считывает ключи администратора.|
|/searchServices/regenerateAdminKey/action|Повторно создает ключ администратора.|
|/searchServices/createQueryKey/action|Создает ключ запроса.|
|/searchServices/queryKey/read|Считывает ключи запросов.|
|/searchServices/queryKey/delete|Удаляет ключ запроса.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Операция | ОПИСАНИЕ |
|---|---|
|/jitNetworkAccessPolicies/read|Возвращает политики JIT-доступа к сети.|
|/jitNetworkAccessPolicies/write|Создает новую политику JIT-доступа к сети или обновляет существующую.|
|/jitNetworkAccessPolicies/initiate/action|Инициирует политику JIT-доступа к сети.|
|/securitySolutionsReferenceData/read|Возвращает эталонные данные решений безопасности.|
|/securityStatuses/read|Возвращает состояния работоспособности ресурсов Azure.|
|/webApplicationFirewalls/read|Возвращает брандмауэры веб-приложения.|
|/webApplicationFirewalls/write|Создает новый брандмауэр веб-приложения или обновляет существующий.|
|/webApplicationFirewalls/delete|Удаляет брандмауэр веб-приложения.|
|/securitySolutions/read|Возвращает решения безопасности.|
|/securitySolutions/write|Создает новое решение безопасности или обновляет существующее.|
|/securitySolutions/delete|Удаляет решение безопасности.|
|/tasks/read|Возвращает все доступные рекомендации по безопасности.|
|/tasks/dismiss/action|Закрывает рекомендацию по безопасности.|
|/tasks/activate/action|Активирует рекомендацию по безопасности.|
|/policies/read|Возвращает политику безопасности.|
|/policies/write|Обновляет политику безопасности.|
|/applicationWhitelistings/read|Возвращает список разрешений приложения.|
|/applicationWhitelistings/write|Создает новый список разрешений приложения или обновляет существующий.|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/subscriptions/write|Создает или обновляет подписку.|
|/gateways/write|Создает или обновляет шлюз.|
|/gateways/delete|Удаляет шлюз.|
|/gateways/read|Возвращает шлюз.|
|/gateways/regenerateprofile/action|Повторно создает профиль шлюза.|
|/gateways/upgradetolatest/action|Обновляет шлюз до последней версии.|
|/nodes/write|Создает или обновляет узел.|
|/nodes/delete|Удаляет узел.|
|/nodes/read|Возвращает узел.|
|/sessions/write|Создает или обновляет сеанс.|
|/sessions/read|Возвращает сеанс.|
|/sessions/delete|Удаляет сеанс.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/register/action|Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и состояние пространства имен.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/diagnosticSettings/read|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/diagnosticSettings/write|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/queues/write|Создает или обновляет свойства очереди.|
|/namespaces/queues/read|Возвращает список описаний ресурсов очереди.|
|/namespaces/queues/Delete|Операция удаления ресурса очереди.|
|/namespaces/queues/authorizationRules/write|Создает правила авторизации очереди и обновляет ее свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/queues/authorizationRules/read| Возвращает список правил авторизации очереди.|
|/namespaces/queues/authorizationRules/delete|Операция удаления правил авторизации очереди.|
|/namespaces/queues/authorizationRules/listkeys/action|Возвращает строку подключения к очереди.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/logDefinitions/read|Возвращает список описаний ресурсов журналов пространства имен.|
|/namespaces/topics/write|Создает или обновляет свойства раздела.|
|/namespaces/topics/read|Возвращает список описаний ресурсов раздела.|
|/namespaces/topics/Delete|Операция удаления ресурса раздела.|
|/namespaces/topics/authorizationRules/write|Создает правила авторизации раздела и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/topics/authorizationRules/read| Возвращает список правил авторизации раздела.|
|/namespaces/topics/authorizationRules/delete|Операция удаления правил авторизации раздела.|
|/namespaces/topics/authorizationRules/listkeys/action|Возвращает строку подключения к разделу.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/topics/subscriptions/write|Создает или обновляет свойства подписки на раздел.|
|/namespaces/topics/subscriptions/read|Возвращает список описаний ресурсов подписки на раздел.|
|/namespaces/topics/subscriptions/Delete|Операция удаления ресурса подписки на раздел.|
|/namespaces/topics/subscriptions/rules/write|Создает или обновляет свойства правила.|
|/namespaces/topics/subscriptions/rules/read|Возвращает список описаний ресурсов правила.|
|/namespaces/topics/subscriptions/rules/Delete|Операция удаления ресурса правила.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Операция | ОПИСАНИЕ |
|---|---|
|/servers/read|Возвращает список серверов в группе ресурсов для подписки.|
|/servers/write|Создает новый сервер или изменяет свойства существующего сервера в группе ресурсов в подписке.|
|/servers/delete|Удаляет сервер и все содержащиеся в нем базы данных и эластичные пулы.|
|/servers/import/action|Создает базу данных на сервере и развертывает схему и данные из пакета DACPAC.|
|/servers/upgrade/action|Включает новые функциональные возможности, доступные в последней версии сервера, и задает сопоставление преобразования выпусков базы данных.|
|/servers/VulnerabilityAssessmentScans/action|Выполняет поиск уязвимостей сервера.|
|/servers/operationResults/read|Операция используется для отслеживания хода установки новой версии сервера.|
|/servers/operationResults/delete|Прерывает выполняемое обновление версии сервера.|
|/servers/securityAlertPolicies/read|Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере.|
|/servers/securityAlertPolicies/write|Изменяет политику обнаружения угроз для заданного сервера.|
|/servers/securityAlertPolicies/operationResults/read|Извлекает результаты операции настройки политики обнаружения угроз на сервере.|
|/servers/administrators/read|Извлекает сведения об администраторе сервера.|
|/servers/administrators/write|Создает или обновляет учетные данные администратора сервера.|
|/servers/administrators/delete|Удаляет с сервера учетные данные администратора сервера.|
|/servers/recoverableDatabases/read|Эта операция используется для аварийного восстановления динамической базы данных. Она позволяет восстановить базу данных до последней известной корректной точки. Возвращает информацию о последней корректной резервной копии, но не восстанавливает базу данных.|
|/servers/serviceObjectives/read|Извлекает список целей уровня обслуживания (также известных как уровни производительности), доступных на данном сервере.|
|/servers/firewallRules/read|Извлекает сведения о правиле брандмауэра для сервера.|
|/servers/firewallRules/write|Создает или обновляет правило брандмауэра для сервера, управляющее диапазоном IP-адресов, с которых разрешено подключаться к серверу.|
|/servers/firewallRules/delete|Удаляет с сервера правило брандмауэра.|
|/servers/administratorOperationResults/read|Извлекает сведения об операции администратора сервера.|
|/servers/recommendedElasticPools/read|Извлекает рекомендацию для пулов эластичных баз данных, позволяющую снизить затраты или повысить производительность, на основе журнала использования ресурсов.|
|/servers/recommendedElasticPools/metrics/read|Извлекает метрики для рекомендуемых пулов эластичных баз данных для заданного сервера.|
|/servers/recommendedElasticPools/databases/read|Извлекает базы данных, которые следует добавить в рекомендуемые пулы эластичных баз данных для заданного сервера.|
|/servers/elasticPools/read|Извлекает сведения о пуле эластичных баз данных для заданного сервера.|
|/servers/elasticPools/write|Создает новый пул эластичных баз данных или изменяет свойства существующего.|
|/servers/elasticPools/delete|Удаляет существующий пул эластичных баз данных.|
|/servers/elasticPools/operationResults/read|Извлекает сведения о заданной операции пула эластичных баз данных.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|Возвращает типы метрик, доступных для пулов эластичных баз данных.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/elasticPools/metrics/read|Возвращает метрики использования ресурсов пула эластичных баз данных.|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Извлекает действия заданной базы данных, входящей в пул эластичных баз данных, а также сведения о ней.|
|/servers/elasticPools/advisors/read|Возвращает список доступных помощников для эластичного пула.|
|/servers/elasticPools/advisors/write|Обновляет состояние автовыполнения Помощника на уровне эластичного пула.|
|/servers/elasticPools/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для эластичного пула.|
|/servers/elasticPools/advisors/recommendedActions/write|Применяет рекомендуемое действие к эластичному пулу.|
|/servers/elasticPools/elasticPoolActivity/read|Извлекает действия и сведения о заданном пуле эластичных баз данных.|
|/servers/elasticPools/databases/read|Извлекает список баз данных, которые входят в пул эластичных баз данных на заданном сервере, а также сведения о них.|
|/servers/auditingPolicies/read|Извлекает сведения о политике аудита таблиц по умолчанию, настроенной на заданном сервере.|
|/servers/auditingPolicies/write|Изменяет политику аудита таблиц по умолчанию для заданного сервера.|
|/servers/disasterRecoveryConfiguration/operationResults/read|Возвращает результаты операции настройки аварийного восстановления.|
|/servers/advisors/read|Возвращает список доступных помощников для сервера.|
|/servers/advisors/write|Обновляет состояние автовыполнения Помощника на уровне сервера.|
|/servers/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для сервера.|
|/servers/advisors/recommendedActions/write|Применяет рекомендуемое действие к серверу.|
|/servers/usages/read|Возвращает квоту DTU сервера и текущие данные об использовании DTU для всех баз данных на сервере.|
|/servers/elasticPoolEstimates/read|Возвращает список оценок эластичных пулов, уже созданных на этом сервере.|
|/servers/elasticPoolEstimates/write|Создает оценку эластичного пула для указанных баз данных.|
|/servers/auditingSettings/read|Извлекает сведения о политике аудита больших двоичных объектов, настроенной на заданном сервере.|
|/servers/auditingSettings/write|Изменяет политику аудита больших двоичных объектов для заданного сервера.|
|/servers/auditingSettings/operationResults/read|Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере.|
|/servers/backupLongTermRetentionVaults/read|С помощью этой операции можно получить хранилище резервных копий долгосрочного хранения. Она возвращает сведения о хранилище, зарегистрированном на данном сервере.|
|/servers/backupLongTermRetentionVaults/write|Регистрирует хранилище резервных копий долгосрочного хранения.|
|/servers/restorableDroppedDatabases/read|Извлекает список баз данных, которые были удалены на заданном сервере, но остались в политике хранения. С помощью этой операции можно получить список баз данных и связанные метаданные, например даты удаления.|
|/servers/databases/read|Возвращает список серверов в группе ресурсов для подписки.|
|/servers/databases/write|Создает новый сервер или изменяет свойства существующего сервера в группе ресурсов в подписке.|
|/servers/databases/delete|Удаляет сервер и все содержащиеся в нем базы данных и эластичные пулы.|
|/servers/databases/export/action|Создает базу данных на сервере и развертывает схему и данные из пакета DACPAC.|
|/servers/databases/VulnerabilityAssessmentScans/action|Выполняет поиск уязвимостей базы данных.|
|/servers/databases/pause/action|Приостанавливает базу данных выпуска хранилища данных.|
|/servers/databases/resume/action|Возобновляет работу базы данных выпуска хранилища данных.|
|/servers/databases/operationResults/read|Операция используется для отслеживания хода выполнения длительных операций базы данных, таких как масштабирование.|
|/servers/databases/replicationLinks/read|Возвращает сведения о каналах репликации, установленных для определенной базы данных.|
|/servers/databases/replicationLinks/delete|Принудительно удаляет отношение репликации. При этом возможна потеря данных.|
|/servers/databases/replicationLinks/unlink/action|Удаляет отношение репликации в принудительном порядке или после синхронизации с партнером.|
|/servers/databases/replicationLinks/failover/action|Выполняет отработку отказа после синхронизации всех изменений из базы данных-источника, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник.|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Выполняет немедленную отработку отказа с потенциальной потерей данных, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник.|
|/servers/databases/replicationLinks/updateReplicationMode/action|Обновляет режим репликации для ссылки на синхронный или асинхронный режим.|
|/servers/databases/replicationLinks/operationResults/read|Возвращает состояние длительных операций с каналами репликации базы данных.|
|/servers/databases/dataMaskingPolicies/read|Извлекает сведения о политике маскирования данных, настроенной для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/write|Изменяет политику маскирования данных для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/rules/read|Извлекает сведения о правиле политики маскирования данных, настроенной для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/rules/write|Изменяет правило политики маскирования данных для заданной базы данных.|
|/servers/databases/securityAlertPolicies/read|Извлекает сведения о политике обнаружения угроз, настроенной для заданной базы данных.|
|/servers/databases/securityAlertPolicies/write|Изменяет политику обнаружения угроз для заданной базы данных.|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|Возвращает типы метрик, доступных для баз данных.|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|Возвращает доступные журналы для баз данных.|
|/servers/databases/topQueries/read|Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период.|
|/servers/databases/topQueries/queryText/read|Возвращает текст Transact-SQL для выбранного идентификатора запроса.|
|/servers/databases/topQueries/statistics/read|Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период.|
|/servers/databases/connectionPolicies/read|Извлекает сведения о политике подключений, настроенной для заданной базы данных.|
|/servers/databases/connectionPolicies/write|Изменяет политику подключений для заданной базы данных.|
|/servers/databases/metrics/read|Возвращает метрики использования ресурсов базы данных.|
|/servers/databases/auditRecords/read|Извлекает записи аудита больших двоичных объектов для базы данных.|
|/servers/databases/transparentDataEncryption/read|Извлекает состояние и сведения о функции безопасности "прозрачное шифрование данных" для заданной базы данных.|
|/servers/databases/transparentDataEncryption/write|Включает или отключает прозрачное шифрование данных для заданной базы данных.|
|/servers/databases/transparentDataEncryption/operationResults/read|Извлекает состояние и сведения о функции безопасности "прозрачное шифрование данных" для заданной базы данных.|
|/servers/databases/auditingPolicies/read|Извлекает сведения о политике аудита таблиц, настроенной для заданной базы данных.|
|/servers/databases/auditingPolicies/write|Изменяет политику аудита таблиц для заданной базы данных.|
|/servers/databases/dataWarehouseQueries/read|Возвращает данные запроса на распределение к хранилищу данных для выбранного идентификатора запроса.|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|Возвращает сведения о шаге распределенного запроса к хранилищу данных для выбранного идентификатора шага.|
|/servers/databases/serviceTierAdvisors/read|Возвращает предложение по масштабированию базы данных на основе статистики выполнения запросов для повышения производительности или снижения затрат.|
|/servers/databases/advisors/read|Возвращает список доступных помощников для базы данных.|
|/servers/databases/advisors/write|Обновляет состояние автовыполнения Помощника на уровне базы данных.|
|/servers/databases/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для базы данных.|
|/servers/databases/advisors/recommendedActions/write|Применяет рекомендуемое действие к базе данных.|
|/servers/databases/usages/read|Возвращает максимальный размер базы данных, который может быть достигнут, и текущую емкость, занимаемую данными.|
|/servers/databases/queryStore/read|Возвращает текущие значения параметров хранилища запросов для базы данных.|
|/servers/databases/queryStore/write|Обновляет параметр хранилища запросов для базы данных.|
|/servers/databases/auditingSettings/read|Извлекает сведения о политике аудита больших двоичных объектов, настроенной для заданной базы данных.|
|/servers/databases/auditingSettings/write|Изменяет политику аудита больших двоичных объектов для заданной базы данных.|
|/servers/databases/schemas/tables/recommendedIndexes/read|Извлекает список рекомендаций по индексам для базы данных.|
|/servers/databases/schemas/tables/recommendedIndexes/write|Применяет рекомендацию по индексу.|
|/servers/databases/schemas/tables/columns/read|Извлекает список столбцов таблицы.|
|/servers/databases/missingindexes/read|Возвращает предложения по созданию, изменению или удалению индексов базы данных для повышения производительности запросов.|
|/servers/databases/missingindexes/write|Применяет рекомендацию по индексу базы данных к определенной базе данных.|
|/servers/databases/importExportOperationResults/read|Возвращает сведения об операции импорта или экспорта базы данных с использованием пакета DACPAC, расположенного в учетной записи хранения.|
|/servers/importExportOperationResults/read|Возвращает список со сведениями об операциях импорта базы данных из учетной записи хранения на заданном сервере.|

## <a name="microsoftstorage"></a>Microsoft.Storage;

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрирует подписку для поставщика ресурсов службы хранилища и позволяет создавать учетные записи хранения.|
|/checknameavailability/read|Проверяет, является ли имя учетной записи допустимым и неиспользуемым.|
|/storageAccounts/write|Создает новую учетную запись хранения с указанными параметрами, обновляет свойства или теги указанной существующей учетной записи хранения или добавляет в нее личный домен.|
|/storageAccounts/delete|Удаляет существующую учетную запись хранения.|
|/storageAccounts/listkeys/action|Возвращает ключи доступа для указанной учетной записи хранения.|
|/storageAccounts/regeneratekey/action|Повторно создает ключи доступа для указанной учетной записи хранения.|
|/storageAccounts/read|Возвращает список учетных записей хранения или свойства указанной учетной записи хранения.|
|/storageAccounts/listAccountSas/action|Возвращает маркер SAS для указанной учетной записи хранения.|
|/storageAccounts/listServiceSas/action|Маркер SAS службы хранилища.|
|/storageAccounts/services/diagnosticSettings/write|Создает или обновляет параметры диагностики учетной записи хранения.|
|/skus/read|Выводит список номеров SKU, поддерживаемых Microsoft.Storage.|
|/usages/read|Возвращает предельное и текущее число используемых ресурсов для указанной подписки.|
|/operations/read|Опрашивает состояние асинхронной операции.|
|/locations/deleteVirtualNetworkOrSubnets/action|Уведомляет поставщик ресурсов Microsoft.Storage о том, что удаляется виртуальная сеть или подсеть.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Операция | ОПИСАНИЕ |
|---|---|
|/managers/clearAlerts/action|Очищает все оповещения, связанные с диспетчером устройств.|
|/managers/getActivationKey/action|Возвращает ключ активации для диспетчера устройств.|
|/managers/regenerateActivationKey/action|Повторно создает ключ активации для диспетчера устройств.|
|/managers/regenarateRegistationCertificate/action|Повторно создает ключ шифрования для диспетчеров устройств.|
|/managers/getEncryptionKey/action|Возвращает ключ шифрования для диспетчера устройств.|
|/managers/read|Выводит список диспетчеров устройств или возвращает их.|
|/managers/delete|Удаляет диспетчеры устройств.|
|/managers/write|Создает или обновляет диспетчеры устройств.|
|/managers/configureDevice/action|Настраивает устройство.|
|/managers/listActivationKey/action|Возвращает ключ активации диспетчера устройств StorSimple.|
|/managers/listPublicEncryptionKey/action|Выводит список открытых ключей шифрования для диспетчера устройств StorSimple.|
|/managers/listPrivateEncryptionKey/action|Возвращает ключ шифрования для диспетчера устройств StorSimple.|
|/managers/provisionCloudAppliance/action|Создает облачное устройство.|
|/Managers/write|Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище".|
|/Managers/read|Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище".|
|/Managers/delete|Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище".|
|/managers/storageAccountCredentials/write|Создает или обновляет данные учетной записи хранения.|
|/managers/storageAccountCredentials/read|Выводит список данных учетной записи хранения или возвращает их.|
|/managers/storageAccountCredentials/delete|Удаляет данные учетной записи хранения.|
|/managers/storageAccountCredentials/listAccessKey/action|Выводит список ключей доступа для данных учетной записи хранения.|
|/managers/accessControlRecords/read|Выводит список записей контроля доступа или возвращает их.|
|/managers/accessControlRecords/write|Создает или обновляет записи контроля доступа.|
|/managers/accessControlRecords/delete|Удаляет записи контроля доступа.|
|/managers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/bandwidthSettings/read|Выводит список параметров пропускной способности (только для серии 8000).|
|/managers/bandwidthSettings/write|Создает или обновляет параметры пропускной способности (только для серии 8000).|
|/managers/bandwidthSettings/delete|Удаляет существующие параметры пропускной способности (только для серии 8000).|
|/Managers/extendedInformation/read|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Managers/extendedInformation/write|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Managers/extendedInformation/delete|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/managers/alerts/read|Выводит список оповещений или возвращает их.|
|/managers/storageDomains/read|Выводит список доменов хранилища или возвращает их.|
|/managers/storageDomains/write|Создает или обновляет домены хранилища.|
|/managers/storageDomains/delete|Удаляет домены хранилища.|
|/managers/devices/scanForUpdates/action|Выполняет поиск обновлений в устройстве.|
|/managers/devices/download/action|Скачивает обновления для устройства.|
|/managers/devices/install/action|Устанавливает обновления на устройство.|
|/managers/devices/read|Выводит список устройств или возвращает их.|
|/managers/devices/write|Создает или обновляет устройства.|
|/managers/devices/delete|Удаляет устройства.|
|/managers/devices/deactivate/action|Деактивирует устройство.|
|/managers/devices/publishSupportPackage/action|Публикует пакет поддержки устройства для устранения неполадок службой поддержки Майкрософт.|
|/managers/devices/failover/action|Выполняет отработку отказа устройства.|
|/managers/devices/sendTestAlertEmail/action|Отправляет тестовое сообщение оповещения настроенным получателям электронной почты.|
|/managers/devices/installUpdates/action|Устанавливает обновления на устройства.|
|/managers/devices/listFailoverSets/action|Выводит список групп перехода на другой ресурс для существующего устройства.|
|/managers/devices/listFailoverTargets/action|Выводит список целей отработки отказа для устройств.|
|/managers/devices/publicEncryptionKey/action|Отображает открытый ключ шифрования для диспетчера устройств.|
|/managers/devices/hardwareComponentGroups/<br>read|Выводит список групп компонентов оборудования.|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|Изменяет состояние питания контроллера для групп компонентов оборудования.|
|/managers/devices/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/chapSettings/write|Создает или обновляет параметры CHAP.|
|/managers/devices/chapSettings/read|Возвращает параметры CHAP или выводит их список.|
|/managers/devices/chapSettings/delete|Удаляет параметры CHAP.|
|/managers/devices/backupScheduleGroups/read|Выводит список групп расписаний архивации или возвращает их.|
|/managers/devices/backupScheduleGroups/write|Создает или обновляет группы расписаний архивации.|
|/managers/devices/backupScheduleGroups/delete|Удаляет группы расписаний архивации.|
|/managers/devices/updateSummary/read|Возвращает сводку обновлений или выводит их список.|
|/managers/devices/migrationSourceConfigurations/<br>import/action|Импортирует конфигурации источников для переноса.|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|Запускает задание для оценки длительности процесса переноса.|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|Начинает перенос с использованием конфигураций источников.|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|Подтверждает успешный перенос и фиксирует его.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|Извлекает состояние задания оценки переноса.|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|Извлекает состояние переноса.|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|Извлекает состояние подтверждения переноса.|
|/managers/devices/alertSettings/read|Возвращает параметры оповещения или выводит их список.|
|/managers/devices/alertSettings/write|Создает или обновляет параметры оповещения.|
|/managers/devices/networkSettings/read|Возвращает параметры сети или выводит их список.|
|/managers/devices/networkSettings/write|Создает или обновляет параметры сети.|
|/managers/devices/jobs/read|Выводит список заданий или возвращает их.|
|/managers/devices/jobs/cancel/action|Отменяет выполнение задания.|
|/managers/devices/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/volumeContainers/write|Создает или обновляет контейнеры томов (только для серии 8000).|
|/managers/devices/volumeContainers/read|Выводит список контейнеров томов (только для серии 8000).|
|/managers/devices/volumeContainers/delete|Удаляет существующие контейнеры томов (только для серии 8000).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Выводит список ключей шифрования контейнеров томов.|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Переключает ключи шифрования контейнеров томов.|
|/managers/devices/volumeContainers/metrics/read|Выводит список метрик.|
|/managers/devices/volumeContainers/volumes/read|Выводит список томов.|
|/managers/devices/volumeContainers/volumes/write|Создает или обновляет тома.|
|/managers/devices/volumeContainers/volumes/delete|Удаляет существующие тома.|
|/managers/devices/volumeContainers/volumes/metrics/read|Выводит список метрик.|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Выводит список определений метрик.|
|/managers/devices/volumeContainers/metricsDefinitions/read|Выводит список определений метрик.|
|/managers/devices/iscsiservers/read|Выводит список серверов iSCSI или возвращает их.|
|/managers/devices/iscsiservers/write|Создает или обновляет серверы iSCSI.|
|/managers/devices/iscsiservers/delete|Удаляет серверы iSCSI.|
|/managers/devices/iscsiservers/backup/action|Архивирует сервер iSCSI.|
|/managers/devices/iscsiservers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/iscsiservers/disks/read|Выводит список дисков или возвращает их.|
|/managers/devices/iscsiservers/disks/write|Создает или обновляет диски.|
|/managers/devices/iscsiservers/disks/delete|Удаляет диски.|
|/managers/devices/iscsiservers/disks/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/iscsiservers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/backups/read|Возвращает набор архивации или выводит его.|
|/managers/devices/backups/delete|Удаляет набор архивации.|
|/managers/devices/backups/restore/action|Восстанавливает все тома из набора архивации.|
|/managers/devices/backups/elements/clone/action|Клонирует общедоступный ресурс или том с помощью элемента архива.|
|/managers/devices/backupPolicies/write|Создает или обновляет политики архивации (только для серии 8000).|
|/managers/devices/backupPolicies/read|Выводит список политик архивации (только для серии 8000).|
|/managers/devices/backupPolicies/delete|Удаляет существующие политики архивации (только для серии 8000).|
|/managers/devices/backupPolicies/backup/action|Выполняет ручную архивацию всех томов, защищенных с помощью политики архивации, для создания их резервной копии по запросу.|
|/managers/devices/backupPolicies/schedules/write|Создает или обновляет расписания.|
|/managers/devices/backupPolicies/schedules/read|Выводит список расписаний.|
|/managers/devices/backupPolicies/schedules/delete|Удаляет существующие расписания.|
|/managers/devices/securitySettings/update/action|Обновляет параметры безопасности.|
|/managers/devices/securitySettings/read|Выводит список параметров безопасности.|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|Синхронизирует сертификат удаленного управления для устройства.|
|/managers/devices/securitySettings/write|Создает или обновляет параметры безопасности.|
|/managers/devices/fileservers/read|Выводит список файловых серверов или возвращает их.|
|/managers/devices/fileservers/write|Создает или обновляет файловые серверы.|
|/managers/devices/fileservers/delete|Удаляет файловые серверы.|
|/managers/devices/fileservers/backup/action|Архивирует файловый сервер.|
|/managers/devices/fileservers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/fileservers/shares/write|Создает или обновляет общедоступные ресурсы.|
|/managers/devices/fileservers/shares/read|Выводит список общедоступных ресурсов или возвращает их.|
|/managers/devices/fileservers/shares/delete|Удаляет общедоступные ресурсы.|
|/managers/devices/fileservers/shares/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/fileservers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/timeSettings/read|Возвращает параметры времени или выводит их список.|
|/managers/devices/timeSettings/write|Создает или обновляет параметры времени.|
|/Managers/certificates/write|Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища.|
|/managers/cloudApplianceConfigurations/read|Выводит список конфигураций, поддерживаемых облачным устройством.|
|/managers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/encryptionSettings/read|Возвращает параметры шифрования или выводит их список.|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Операция | ОПИСАНИЕ |
|---|---|
|/streamingjobs/Start/action|Запускает задание Stream Analytics.|
|/streamingjobs/Stop/action|Останавливает задание Stream Analytics.|
|/streamingjobs/Read|Считывает задание Stream Analytics.|
|/streamingjobs/Write|Записывает задание Stream Analytics.|
|/streamingjobs/Delete|Удаляет задание Stream Analytics.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Возвращает доступные метрики для заданий потоковой передачи.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Считывает параметр диагностики.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Записывает параметр диагностики.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Возвращает доступные журналы для заданий потоковой передачи.|
|/streamingjobs/transformations/Read|Считывает преобразование задания Stream Analytics.|
|/streamingjobs/transformations/Write|Записывает преобразование задания Stream Analytics.|
|/streamingjobs/transformations/Delete|Удаляет преобразование задания Stream Analytics.|
|/streamingjobs/inputs/Read|Считывает входные данные задания Stream Analytics.|
|/streamingjobs/inputs/Write|Записывает входные данные задания Stream Analytics.|
|/streamingjobs/inputs/Delete|Удаляет входные данные задания Stream Analytics.|
|/streamingjobs/outputs/Read|Считывает выходные данные задания Stream Analytics.|
|/streamingjobs/outputs/Write|Записывает выходные данные задания Stream Analytics.|
|/streamingjobs/outputs/Delete|Удаляет выходные данные задания Stream Analytics.|

## <a name="microsoftsupport"></a>Microsoft.Support

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Выполняет регистрацию в поставщике ресурсов поддержки.|
|/supportTickets/read|Возвращает подробные сведения о запросе в службу поддержки (включая состояние, серьезность, контактную информацию и сообщения) или возвращает список запросов в службу поддержки в подписках.|
|/supportTickets/write|Создает или обновляет запрос в службу поддержки. Можно создавать запросы в службу поддержки по техническим проблемам, а также проблемам, связанным с выставлением счетов, квотами или управлением подписками. Вы можете изменить уровень серьезности, контактную информацию и сообщения для существующих запросов в службу поддержки.|

## <a name="microsoftweb"></a>Microsoft.Web

| Операция | ОПИСАНИЕ |
|---|---|
|/unregister/action|Отменяет регистрацию поставщика ресурсов Microsoft.Web для подписки.|
|/validate/action|Выполняет проверку.|
|/register/action|Регистрирует поставщик ресурсов Microsoft.Web для подписки.|
|/hostingEnvironments/Read|Возвращает свойства среды службы приложений.|
|/hostingEnvironments/Write|Создает новую среду службы приложений или обновляет существующую.|
|/hostingEnvironments/Delete|Удаляет среду службы приложений.|
|/hostingEnvironments/reboot/Action|Перезагружает все компьютеры в среде службы приложений.|
|/hostingenvironments/resume/action|Возобновляет работу сред внешнего размещения.|
|/hostingenvironments/suspend/action|Приостанавливает работу сред внешнего размещения.|
|/hostingenvironments/metricdefinitions/read|Возвращает определения метрик сред внешнего размещения.|
|/hostingEnvironments/workerPools/Read|Возвращает свойства рабочего пула в среде службы приложений.|
|/hostingEnvironments/workerPools/Write|Создает новый рабочий пул в среде службы приложений или обновляет существующий.|
|/hostingenvironments/workerpools/metricdefinitions/read|Возвращает определения метрик рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/workerpools/metrics/read|Возвращает метрики рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/workerpools/skus/read|Возвращает номера SKU рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/workerpools/usages/read|Возвращает данные об использовании рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/sites/read|Возвращает веб-приложения в средах внешнего размещения.|
|/hostingenvironments/serverfarms/read|Возвращает планы службы приложений в средах внешнего размещения.|
|/hostingenvironments/usages/read|Возвращает данные об использовании сред внешнего размещения.|
|/hostingenvironments/capacities/read|Возвращает емкости сред внешнего размещения.|
|/hostingenvironments/operations/read|Возвращает операции сред внешнего размещения.|
|/hostingEnvironments/multiRolePools/Read|Возвращает свойства интерфейсного пула в среде службы приложений.|
|/hostingEnvironments/multiRolePools/Write|Создает новый интерфейсный пул в среде службы приложений или обновляет существующий.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Возвращает определения метрик пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/multirolepools/metrics/read|Возвращает метрики пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/multirolepools/skus/read|Возвращает номера SKU пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/multirolepools/usages/read|Возвращает данные об использовании пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/diagnostics/read|Возвращает данные диагностики сред внешнего размещения.|
|/publishingusers/read|Возвращает публикующих пользователей.|
|/publishingusers/write|Обновляет публикующих пользователей.|
|/checknameavailability/read|Проверяет, доступно ли имя ресурса.|
|/geoRegions/Read|Возвращает список географических регионов.|
|/sites/Read|Возвращает свойства веб-приложения.|
|/sites/Write|Создает новое веб-приложение или обновляет существующее.|
|/sites/Delete|Удаление существующего веб-приложения|
|/sites/backup/Action|Создает резервную копию веб-приложения.|
|/sites/publishxml/Action|Возвращает XML-файл профиля публикации для веб-приложения.|
|/sites/publish/Action|Публикует веб-приложение.|
|/sites/restart/Action|Перезапускает веб-приложение.|
|/sites/start/Action|Запускает веб-приложение.|
|/sites/stop/Action|Останавливает веб-приложение.|
|/sites/slotsswap/Action|Переключает слоты развертывания веб-приложения.|
|/sites/slotsdiffs/Action|Возвращает различия конфигураций веб-приложения и слотов.|
|/sites/applySlotConfig/Action|Применяет конфигурацию слота веб-приложения из целевого слота к текущему веб-приложению.|
|/sites/resetSlotConfig/Action|Сбрасывает конфигурацию веб-приложения.|
|/sites/functions/action|Выполняет функции веб-приложений.|
|/sites/listsyncfunctiontriggerstatus/action|Отображает состояние триггера функции синхронизации для веб-приложений.|
|/sites/networktrace/action|Трассирует сеть для веб-приложений.|
|/sites/newpassword/action|Задает новый пароль для веб-приложений.|
|/sites/sync/action|Синхронизирует веб-приложения.|
|/sites/operationresults/read|Возвращает результаты операций веб-приложений.|
|/sites/webjobs/read|Возвращает веб-задания веб-приложений.|
|/sites/backup/read|Возвращает резервную копию веб-приложений.|
|/sites/backup/write|Обновляет резервную копию веб-приложений.|
|/sites/metricdefinitions/read|Возвращает определения метрик веб-приложений.|
|/sites/metrics/read|Возвращает метрики веб-приложений.|
|/sites/continuouswebjobs/delete|Удаляет непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/read|Возвращает непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/start/action|Запускает непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/stop/action|Останавливает непрерывные веб-задания веб-приложений.|
|/sites/domainownershipidentifiers/read|Возвращает идентификаторы владения доменом веб-приложений.|
|/sites/domainownershipidentifiers/write|Обновляет идентификаторы владения доменом веб-приложений.|
|/sites/premieraddons/delete|Удаляет надстройки Premier для веб-приложений.|
|/sites/premieraddons/read|Возвращает надстройки Premier для веб-приложений.|
|/sites/premieraddons/write|Обновляет надстройки Premier для веб-приложений.|
|/sites/triggeredwebjobs/delete|Удаляет активированные веб-задания веб-приложений.|
|/sites/triggeredwebjobs/read|Возвращает активированные веб-задания веб-приложений.|
|/sites/triggeredwebjobs/run/action|Выполняет активированные веб-задания веб-приложений.|
|/sites/hostnamebindings/delete|Удаляет привязки имен узлов для веб-приложений.|
|/sites/hostnamebindings/read|Возвращает привязки имен узлов для веб-приложений.|
|/sites/hostnamebindings/write|Обновляет привязки имен узлов для веб-приложений.|
|/sites/virtualnetworkconnections/delete|Удаляет подключения виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/read|Возвращает подключения виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/write|Обновляет подключения виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/gateways/read|Возвращает шлюзы подключений виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальных сетей веб-приложений.|
|/sites/publishxml/read|Возвращает XML-файл профиля публикации для веб-приложений.|
|/sites/hybridconnectionrelays/read|Возвращает ретрансляторы гибридных подключений для веб-приложений.|
|/sites/perfcounters/read|Возвращает счетчики производительности веб-приложений.|
|/sites/usages/read|Возвращает данные об использовании веб-приложений.|
|/sites/slots/Write|Создает новый слот веб-приложения или обновляет существующий.|
|/sites/slots/Delete|Удаляет существующий слот веб-приложения.|
|/sites/slots/backup/Action|Создает резервную копию слота веб-приложения.|
|/sites/slots/publishxml/Action|Возвращает XML-файл профиля публикации для слота веб-приложения.|
|/sites/slots/publish/Action|Публикует слот веб-приложения.|
|/sites/slots/restart/Action|Перезапускает слот веб-приложения.|
|/sites/slots/start/Action|Запускает слот веб-приложения.|
|/sites/slots/stop/Action|Останавливает слот веб-приложения.|
|/sites/slots/slotsswap/Action|Переключает слоты развертывания веб-приложения.|
|/sites/slots/slotsdiffs/Action|Возвращает различия конфигураций веб-приложения и слотов.|
|/sites/slots/applySlotConfig/Action|Применяет конфигурацию слота веб-приложения из целевого слота к текущему слоту.|
|/sites/slots/resetSlotConfig/Action|Сбрасывает конфигурацию слота веб-приложения.|
|/sites/slots/Read|Возвращает свойства слота развертывания веб-приложения.|
|/sites/slots/newpassword/action|Задает новый пароль для слотов веб-приложений.|
|/sites/slots/sync/action|Синхронизирует слоты веб-приложений.|
|/sites/slots/operationresults/read|Возвращает результаты операций слотов веб-приложений.|
|/sites/slots/webjobs/read|Возвращает веб-задания слотов веб-приложений.|
|/sites/slots/backup/write|Обновляет резервную копию слотов веб-приложений.|
|/sites/slots/metricdefinitions/read|Возвращает определения метрик слотов веб-приложений.|
|/sites/slots/metrics/read|Возвращает метрики слотов веб-приложений.|
|/sites/slots/continuouswebjobs/delete|Удаляет непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/read|Возвращает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/start/action|Запускает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/stop/action|Останавливает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/premieraddons/delete|Удаляет надстройки Premier для слотов веб-приложений.|
|/sites/slots/premieraddons/read|Возвращает надстройки Premier для слотов веб-приложений.|
|/sites/slots/premieraddons/write|Обновляет надстройки Premier для слотов веб-приложений.|
|/sites/slots/triggeredwebjobs/delete|Удаляет активированные веб-задания слотов веб-приложений.|
|/sites/slots/triggeredwebjobs/read|Возвращает активированные веб-задания слотов веб-приложений.|
|/sites/slots/triggeredwebjobs/run/action|Выполняет активированные веб-задания слотов веб-приложений.|
|/sites/slots/hostnamebindings/delete|Удаляет привязки имен узлов для слотов веб-приложений.|
|/sites/slots/hostnamebindings/read|Возвращает привязки имен узлов для слотов веб-приложений.|
|/sites/slots/hostnamebindings/write|Обновляет привязки имен узлов для слотов веб-приложений.|
|/sites/slots/phplogging/read|Возвращает сведения о ведении журнала PHP для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/delete|Удаляет подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/read|Возвращает подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/write|Обновляет подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальных сетей для слотов веб-приложений.|
|/sites/slots/usages/read|Возвращает данные об использовании слотов веб-приложений.|
|/sites/slots/hybridconnection/delete|Удаляет гибридное подключение для слотов веб-приложений.|
|/sites/slots/hybridconnection/read|Возвращает гибридное подключение для слотов веб-приложений.|
|/sites/slots/hybridconnection/write|Обновляет гибридное подключение для слотов веб-приложений.|
|/sites/slots/config/Read|Возвращает параметры конфигурации слота веб-приложения.|
|/sites/slots/config/list/Action|Отображает влияющие на безопасность параметры слота веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения.|
|/sites/slots/config/Write|Обновляет параметры конфигурации слота веб-приложения.|
|/sites/slots/config/delete|Удаляет конфигурацию слотов веб-приложений.|
|/sites/slots/instances/read|Возвращает экземпляры слотов веб-приложений.|
|/sites/slots/instances/processes/read|Возвращает процессы экземпляры слотов веб-приложений.|
|/sites/slots/instances/deployments/read|Возвращает развертывания экземпляры слотов веб-приложений.|
|/sites/slots/sourcecontrols/Read|Возвращает параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/sourcecontrols/Write|Обновляет параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/sourcecontrols/Delete|Удаляет параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/restore/read|Возвращает данные о восстановлении слотов веб-приложений.|
|/sites/slots/analyzecustomhostname/read|Возвращает результаты анализа пользовательского имени узла для слотов веб-приложений.|
|/sites/slots/backups/Read|Возвращает свойства резервной копии слотов веб-приложения.|
|/sites/slots/backups/list/action|Выводит список резервных копий слотов веб-приложений.|
|/sites/slots/backups/restore/action|Восстанавливает резервные копии слотов веб-приложений.|
|/sites/slots/deployments/delete|Удаляет развертывания слотов веб-приложений.|
|/sites/slots/deployments/read|Возвращает развертывания слотов веб-приложений.|
|/sites/slots/deployments/write|Обновляет развертывания слотов веб-приложений.|
|/sites/slots/deployments/log/read|Возвращает журнал развертываний слотов веб-приложений.|
|/sites/hybridconnection/delete|Удаляет гибридное подключение для веб-приложений.|
|/sites/hybridconnection/read|Возвращает гибридное подключение для веб-приложений.|
|/sites/hybridconnection/write|Обновляет гибридное подключение для веб-приложений.|
|/sites/recommendationhistory/read|Возвращает журнал рекомендаций для веб-приложения.|
|/sites/recommendations/Read|Возвращает список рекомендаций для веб-приложения.|
|/sites/recommendations/disable/action|Отключает рекомендации для веб-приложений.|
|/sites/config/Read|Возвращает параметры конфигурации веб-приложения.|
|/sites/config/list/Action|Отображает влияющие на безопасность параметры веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения.|
|/sites/config/Write|Обновляет параметры конфигурации веб-приложения.|
|/sites/config/delete|Удаляет конфигурацию веб-приложений.|
|/sites/instances/read|Возвращает экземпляры веб-приложений.|
|/sites/instances/processes/delete|Удаляет процессы экземпляров веб-приложений.|
|/sites/instances/processes/read|Возвращает процессы экземпляров веб-приложений.|
|/sites/instances/deployments/read|Возвращает развертывания экземпляров веб-приложений.|
|/sites/sourcecontrols/Read|Возвращает параметры конфигурации системы управления версиями веб-приложения.|
|/sites/sourcecontrols/Write|Обновляет параметры конфигурации системы управления версиями веб-приложения.|
|/sites/sourcecontrols/Delete|Удаляет параметры конфигурации системы управления версиями веб-приложения.|
|/sites/restore/read|Возвращает данные о восстановлении веб-приложений.|
|/sites/analyzecustomhostname/read|Анализирует пользовательское имя узла.|
|/sites/backups/Read|Возвращает свойства резервной копии веб-приложения.|
|/sites/backups/list/action|Выводит список резервных копий веб-приложений.|
|/sites/backups/restore/action|Восстанавливает резервные копии веб-приложений.|
|/sites/snapshots/read|Возвращает моментальные снимки веб-приложений.|
|/sites/functions/delete|Удаляет функции веб-приложений.|
|/sites/functions/listsecrets/action|Выводит список секретов для функций веб-приложений.|
|/sites/functions/read|Возвращает функции веб-приложений.|
|/sites/functions/write|Обновляет функции веб-приложений.|
|/sites/deployments/delete|Удаляет развертывания веб-приложений.|
|/sites/deployments/read|Возвращает развертывания веб-приложений.|
|/sites/deployments/write|Обновляет развертывания веб-приложений.|
|/sites/deployments/log/read|Возвращает журнал развертываний веб-приложений.|
|/sites/diagnostics/read|Возвращает данные диагностики веб-приложений.|
|/sites/diagnostics/workerprocessrecycle/read|Возвращает данные диагностики перезапуска рабочих процессов для веб-приложений.|
|/sites/diagnostics/workeravailability/read|Возвращает данные диагностики о доступности рабочих процессов для веб-приложений.|
|/sites/diagnostics/runtimeavailability/read|Возвращает данные диагностики о доступности среды выполнения для веб-приложений.|
|/sites/diagnostics/cpuanalysis/read|Возвращает данные диагностики об анализе ЦП для веб-приложений.|
|/sites/diagnostics/servicehealth/read|Возвращает данные диагностики о работоспособности служб для веб-приложений.|
|/sites/diagnostics/frebanalysis/read|Возвращает данные диагностики об анализе FREB для веб-приложений.|
|/availablestacks/read|Возвращает доступные стеки.|
|/isusernameavailable/read|Проверяет, доступно ли имя пользователя.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|Возвращает список интерфейсов API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|Добавляет новый API или обновляет существующий.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|Удаляет существующий API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|Возвращает список подключений.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|Сохраняет новое подключение или обновляет существующее.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|Удаляет существующее подключение.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|Считывает списки управления доступом подключения.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|Добавляет или обновляет список управления доступом подключения.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|Удаляет список управления доступом подключения.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|Считывает списки управления доступом подключения для API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|Считывает списки управления доступом подключения.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|Создает или обновляет списки управления доступом API.|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|Удаляет списки управления доступом API.|
|/serverfarms/Read|Возвращает свойства плана службы приложений.|
|/serverfarms/Write|Создает новый план службы приложений или обновляет существующий.|
|/serverfarms/Delete|Удаление существующего плана службы приложений|
|/serverfarms/restartSites/Action|Перезапустите все веб-приложения в плане службы приложений.|
|/serverfarms/operationresults/read|Возвращает результаты операций планов службы приложений.|
|/serverfarms/capabilities/read|Возвращает возможности планов службы приложений.|
|/serverfarms/metricdefinitions/read|Возвращает определения метрик планов службы приложений.|
|/serverfarms/metrics/read|Возвращает метрики планов службы приложений.|
|/serverfarms/hybridconnectionplanlimits/read|Возвращает ограничения плана гибридных подключений для плана службы приложений.|
|/serverfarms/virtualnetworkconnections/read|Возвращает подключения виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/delete|Удаляет маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/read|Возвращает маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/write|Обновляет маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальной сети для планов службы приложений.|
|/serverfarms/firstpartyapps/settings/delete|Удаляет параметры основных приложений в планах службы приложений.|
|/serverfarms/firstpartyapps/settings/read|Возвращает параметры основных приложений в планах службы приложений.|
|/serverfarms/firstpartyapps/settings/write|Обновляет параметры основных приложений в планах службы приложений.|
|/serverfarms/sites/read|Возвращает веб-приложения в планах службы приложений.|
|/serverfarms/workers/reboot/action|Перезапускает рабочие роли планов службы приложений.|
|/serverfarms/hybridconnectionrelays/read|Возвращает ретрансляторы гибридных подключений для планов службы приложений.|
|/serverfarms/skus/read|Возвращает номера SKU в планах службы приложений.|
|/serverfarms/usages/read|Возвращает данные об использовании для планов службы приложений.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Возвращает веб-приложения ретрансляторов в пространствах имен гибридных подключений для плана службы приложений.|
|/ishostnameavailable/read|Проверяет, доступно ли имя узла.|
|/connectionGateways/Read|Возвращает список шлюзов подключений.|
|/connectionGateways/Write|Создает или обновляет шлюз для подключения.|
|/connectionGateways/Delete|Удаляет шлюз подключения.|
|/connectionGateways/Join/Action|Присоединяет шлюз подключения.|
|/classicmobileservices/read|Возвращает классические мобильные службы.|
|/skus/read|Возвращает номера SKU.|
|/certificates/Read|Возвращает список сертификатов.|
|/certificates/Write|Добавляет новый сертификат или обновляет существующий.|
|/certificates/Delete|Удаляет существующий сертификат.|
|/operations/read|Возвращает операции.|
|/recommendations/Read|Возвращает список рекомендаций для подписок.|
|/ishostingenvironmentnameavailable/read|Проверяет, доступно ли имя среды внешнего размещения.|
|/apiManagementAccounts/Read|Возвращает список учетных записей управления API.|
|/apiManagementAccounts/Write|Добавляет новую учетную запись управления API или обновляет существующую.|
|/apiManagementAccounts/Delete|Удаляет существующую учетную запись управления API.|
|/apiManagementAccounts/connectionAcls/Read|Возвращает перечень списков управления доступом для подключений.|
|/apiManagementAccounts/apiAcls/Read|Считывает списки управления доступом подключения.|
|/connections/Read|Возвращает список подключений.|
|/connections/Write|Создает или обновляет подключение.|
|/connections/Delete|Удаляет подключение.|
|/connections/Join/Action|Присоединяет подключение.|
|/connections/confirmconsentcode/action|Подтверждает код согласия на подключение.|
|/connections/listconsentlinks/action|Выводит список ссылок на согласие для подключений.|
|/deploymentlocations/read|Возвращает расположения развертываний.|
|/sourcecontrols/read|Возвращает систему управления версиями.|
|/sourcecontrols/write|Обновляет системы управления версиями.|
|/managedhostingenvironments/read|Возвращает управляемые среды внешнего размещения.|
|/managedhostingenvironments/sites/read|Возвращает веб-приложения в управляемых средах внешнего размещения.|
|/managedhostingenvironments/serverfarms/read|Возвращает планы службы приложений в управляемых средах внешнего размещения.|
|/locations/managedapis/read|Возвращает расположения управляемых интерфейсов API.|
|/locations/apioperations/read|Возвращает операции API расположений.|
|/locations/connectiongatewayinstallations/read|Возвращает данные об установленных шлюзах подключений в расположениях.|
|/listSitesAssignedToHostName/Read|Возвращает имена сайтов, назначенные имени узла.|

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [создать настраиваемую роль](role-based-access-control-custom-roles.md).
- Ознакомьтесь со [встроенными ролями RBAC](role-based-access-built-in-roles.md).
- Узнайте, как управлять назначением доступа [по пользователям](role-based-access-control-manage-assignments.md) или [по ресурсам](role-based-access-control-configure.md). 
- Узнайте, как [просмотреть журналы действий для аудита действий с ресурсами](~/articles/azure-resource-manager/resource-group-audit.md).
