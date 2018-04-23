---
title: Операции поставщиков Azure Resource Manager | Документация Майкрософт
description: Описание операций, доступных в поставщиках ресурсов Microsoft Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Операции поставщиков ресурсов Azure Resource Manager

В этом документе перечислены операции, доступные в каждом поставщике ресурсов Microsoft Azure Resource Manager. Они могут использоваться в настраиваемых ролях, чтобы обеспечить детальные разрешения управления доступом на основе ролей (RBAC) для ресурсов в Azure. Обратите внимание на то, что это не полный список, и операции могут добавляться или удаляться по мере обновления поставщиков. Строки операций используют формат `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Чтобы получить полный актуальный список, используйте командлет `Get-AzureRmProviderOperation` (в PowerShell) или команду `az provider operation list` (в Azure CLI версии 2).

## <a name="microsoftaad"></a>Microsoft.AAD

| Операция | ОПИСАНИЕ |
|---|---|
|/domainServices/delete|Удаление доменных служб.|
|/domainServices/read|Чтение доменных служб.|
|/domainServices/write|Запись доменных служб.|
|/locations/operationresults/read|Чтение состояния асинхронной операции.|
|/Operations/read|Локализованное понятное описание операции, которое будет отображаться пользователю.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Операция | ОПИСАНИЕ |
|---|---|
|/diagnosticsettings/delete|Удаление параметра диагностики.|
|/diagnosticsettings/read|Чтение параметра диагностики.|
|/diagnosticsettings/write|Запись параметра диагностики.|
|/diagnosticsettingscategories/read|Чтение категорий параметра диагностики.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов клиентов.|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Операция | ОПИСАНИЕ |
|---|---|
|/configuration/action|Обновляет конфигурацию клиента.|
|/configuration/read|Считывает конфигурацию клиента.|
|/configuration/write|Создает конфигурацию клиента.|
|/services/action|Обновляет экземпляр службы в клиенте.|
|/services/alerts/read|Считывает оповещения для службы.|
|/services/alerts/read|Считывает оповещения для службы.|
|/services/delete|Удаляет экземпляр службы из клиента.|
|/services/read|Считывает экземпляры службы в клиенте.|
|/services/servicemembers/action|Создает экземпляр члена службы в клиенте.|
|/services/servicemembers/alerts/read|Считывает оповещения для члена службы.|
|/services/servicemembers/delete|Удаляет экземпляр члена службы из клиента.|
|/services/servicemembers/read|Считывает экземпляр члена службы в клиенте.|
|/services/write|Создает экземпляр службы в клиенте.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Операция | ОПИСАНИЕ |
|---|---|
|/configurations/read|Получение конфигураций|
|/configurations/write|Создание или обновление конфигурации.|
|/generateRecommendations/action|Создает рекомендации.|
|/generateRecommendations/read|Возвращает состояние создания рекомендаций.|
|/operations/read|Получение операций для Microsoft Advisor.|
|/recommendations/read|Считывает рекомендации.|
|/recommendations/suppressions/delete|Удаляет подавления.|
|/recommendations/suppressions/read|Возвращает подавления.|
|/recommendations/suppressions/write|Создает или обновляет подавления.|
|/register/action|Регистрирует подписку для Microsoft Advisor.|
|/suppressions/delete|Удаляет подавления.|
|/suppressions/read|Возвращает подавления.|
|/suppressions/write|Создает или обновляет подавления.|
|/unregister/action|Отмена регистрации подписки для Microsoft Advisor.|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/alerts/read|Получение всех оповещений для фильтров входа.|
|/alerts/resolve/action|Изменение состояния оповещения на Resolve.|
|/alertsSummary/read|Получение сводки оповещений.|
|/Operations/read|Чтение предоставленных операций.|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Операция | ОПИСАНИЕ |
|---|---|
|/locations/checkNameAvailability/action|Проверяет, является ли данное имя сервера анализа данных допустимым и неиспользуемым.|
|/locations/operationresults/read|Получение сведений о результате указанной операции.|
|/locations/operationstatuses/read|Извлечение сведений о состоянии указанной операции.|
|/operations/read|Извлечение сведений об операциях.|
|/register/action|Регистрация поставщика ресурсов для служб Analysis Services.|
|/servers/delete|Удаляет сервер анализа данных.|
|/servers/listGatewayStatus/action|Отображение состояния шлюза, связанного с сервером.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для сервера анализа данных.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для сервера анализа данных.|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов серверов.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для сервера анализа данных.|
|/servers/read|Извлекает информацию об указанном сервере анализа данных.|
|/servers/resume/action|Возобновляет работу сервера анализа данных.|
|/servers/skus/read|Получение доступных сведений о номере SKU для сервера.|
|/servers/suspend/action|Приостанавливает сервер анализа данных.|
|/servers/write|Создает или обновляет указанный сервер анализа данных.|
|/skus/read|Извлечение сведений о номерах SKU.|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/read|Проверяет, доступно ли указанное имя службы.|
|/operations/read|Чтение всех операций API для ресурса Microsoft.ApiManagement.|
|/register/action|Регистрирует подписку для поставщика ресурсов Microsoft.ApiManagement.|
|/reports/read|Получение отчетов, объединенных по периодам времени, географическим регионам, разработчикам, продуктам, API-интерфейсам, операциям, подпискам и запросам.|
|/service/apis/delete|Удаляет существующий API.|
|/service/apis/diagnostics/delete|Удаление существующих данных диагностики.|
|/service/apis/diagnostics/loggers/delete|Удаление сопоставления средства ведения журнала с параметром диагностики.|
|/service/apis/diagnostics/loggers/read|Получение списка существующих диагностических средств ведения журнала.|
|/service/apis/diagnostics/loggers/write|Сопоставление средства ведения журнала с параметром диагностики.|
|/service/apis/diagnostics/read|Получение списка диагностики или сведений о диагностике.|
|/service/apis/diagnostics/write|Добавление новых данных диагностики или обновление существующих диагностических сведений.|
|/service/apis/operations/delete|Удаляет существующую операцию API.|
|/service/apis/operations/policies/delete|Удаление конфигурации политики из политик операций API.|
|/service/apis/operations/policies/read|Получение политик для операций API или получение сведений о конфигурации политики для операций API.|
|/service/apis/operations/policies/write|Определение сведений о конфигурации политики для операции API.|
|/service/apis/operations/policy/delete|Удаляет конфигурацию политики из операции.|
|/service/apis/operations/policy/read|Возвращает сведения о конфигурации политики для операции.|
|/service/apis/operations/policy/write|Задает сведения о конфигурации политики для операции.|
|/service/apis/operations/read|Возвращает список существующих операций API или сведения о конкретной операции API.|
|/service/apis/operations/tags/delete|Удаление связи существующего тега с существующей операцией.|
|/service/apis/operations/tags/read|Получение тегов, связанных с операцией, или сведений о теге.|
|/service/apis/operations/tags/write|Связывание существующего тега с существующей операцией.|
|/service/apis/operations/write|Создает новую операцию API или обновляет существующую.|
|/service/apis/operationsByTags/read|Получение списка сопоставлений операций или тегов.|
|/service/apis/policies/delete|Удаление конфигурации политики из политик API.|
|/service/apis/policies/read|Получение политик для API или получение сведений о конфигурации политики для API.|
|/service/apis/policies/write|Задает сведения о конфигурации политики для API.|
|/service/apis/policy/delete|Удаляет конфигурацию политики из API.|
|/service/apis/policy/read|Возвращает сведения о конфигурации политики для API.|
|/service/apis/policy/write|Задает сведения о конфигурации политики для API.|
|/service/apis/products/read|Получение всех продуктов, частью которых является API.|
|/service/apis/read|Возвращает список всех зарегистрированных интерфейсов API или сведения об API.|
|/service/apis/releases/delete|Удаление всех версий API или выпуска API.|
|/service/apis/releases/read|Получение списка выпусков API или сведений о выпуске API.|
|/service/apis/releases/write|Создание нового выпуска API или обновление существующего.|
|/service/apis/revisions/delete|Удаление всех редакций API.|
|/service/apis/revisions/read|Получение списка редакций, относящихся к API.|
|/service/apis/schemas/delete|Удаление существующей схемы.|
|/service/apis/schemas/document/read|Получение документа, описывающего схему.|
|/service/apis/schemas/document/write|Обновление документа, описывающего схему.|
|/service/apis/schemas/read|Получение всех схем для данного API или получение схем, используемых API.|
|/service/apis/schemas/write|Определение схем, используемых API.|
|/service/apis/tagDescriptions/delete|Удаление описания тега из API.|
|/service/apis/tagDescriptions/read|Получение описаний тегов или описания тега в области API.|
|/service/apis/tagDescriptions/write|Создание или изменение описания тега в области API.|
|/service/apis/tags/delete|Удаление существующего сопоставления API и тега.|
|/service/apis/tags/read|Получение всех сопоставлений API и тега или сведений о сопоставлении API и тега.|
|/service/apis/tags/write|Добавление нового сопоставления API и тега.|
|/service/apis/write|Создает новый API или обновляет данные существующего API.|
|/service/apisByTags/read|Получение списка сопоставлений API и тега.|
|/service/api-version-sets/delete|Удаление существующего набора версий.|
|/service/api-version-sets/read|Получение списка сущностей группы версий или получение сведений о наборе версий.|
|/service/api-version-sets/versions/read|Получение списка сущностей версии.|
|/service/api-version-sets/write|Создание нового набора версий или обновление сведений о существующем наборе.|
|/service/applynetworkconfigurationupdates/action|Обновляет ресурсы Microsoft.ApiManagement, работающие в виртуальной сети, для применения обновленных параметров сети.|
|/service/authorizationServers/delete|Удаляет существующий сервер авторизации.|
|/service/authorizationServers/read|Возвращает список серверов авторизации или сведения о сервере авторизации.|
|/service/authorizationServers/write|Создает новый сервер авторизации или обновляет данные существующего.|
|/service/backends/delete|Удаляет существующую серверную часть.|
|/service/backends/read|Возвращает список серверных частей или сведения о серверной части.|
|/service/backends/reconnect/action|Создание запроса на повторное подключение.|
|/service/backends/write|Добавляет новую серверную часть или обновляет данные существующей.|
|/service/backup/action|Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения.|
|/service/certificates/delete|Удаляет существующий сертификат.|
|/service/certificates/read|Возвращает список сертификатов или сведения о сертификате.|
|/service/certificates/write|Добавляет новый сертификат.|
|/service/delete|Удаляет экземпляр службы управления API.|
|/service/diagnostics/delete|Удаление существующих данных диагностики.|
|/service/diagnostics/loggers/delete|Удаление сопоставления средства ведения журнала с параметром диагностики.|
|/service/diagnostics/loggers/read|Получение списка существующих диагностических средств ведения журнала.|
|/service/diagnostics/loggers/write|Сопоставление средства ведения журнала с параметром диагностики.|
|/service/diagnostics/read|Получение списка диагностики или сведений о диагностике.|
|/service/diagnostics/write|Добавление новых данных диагностики или обновление существующих диагностических сведений.|
|/service/getssotoken/action|Возвращает маркер единого входа, который может использоваться для входа на портал прежней версии службы управления API от имени администратора.|
|/service/groups/delete|Удаляет существующую группу.|
|/service/groups/read|Возвращает список групп или сведения о группе.|
|/service/groups/users/delete|Удаляет пользователя из существующей группы.|
|/service/groups/users/read|Возвращает список пользователей группы.|
|/service/groups/users/write|Добавляет существующего пользователя в существующий продукт.|
|/service/groups/write|Создает новую группу или обновляет данные существующей.|
|/service/identityProviders/delete|Удаляет существующий поставщик удостоверений.|
|/service/identityProviders/read|Возвращает список поставщиков удостоверений или сведения о поставщике удостоверений.|
|/service/identityProviders/write|Создает новый поставщик удостоверений или обновляет данные существующего.|
|/service/locations/networkstatus/read|Получение состояния доступа к сетевым ресурсам, от которых зависит служба в расположении.|
|/service/loggers/delete|Удаляет существующее средство ведения журнала.|
|/service/loggers/read|Возвращает список средств ведения журнала или сведения о средстве ведения журнала.|
|/service/loggers/write|Добавляет новое средство ведения журнала или обновляет существующее.|
|/service/managedeployments/action|Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API.|
|/service/networkstatus/read|Получение состояния доступа к сетевым ресурсам, от которых зависит служба.|
|/service/notifications/action|Отправление уведомления указанному пользователю.|
|/service/notifications/read|Получение всех уведомлений издателя управления API или сведений об этих уведомлениях.|
|/service/notifications/recipientEmails/delete|Удаление существующего адреса электронной почты, связанного с уведомлением.|
|/service/notifications/recipientEmails/read|Получение списка получателей, связанных с уведомлением издателя управления API.|
|/service/notifications/recipientEmails/write|Создание нового получателя уведомлений электронной почты.|
|/service/notifications/recipientUsers/delete|Удаление пользователя, связанного с получателями уведомлений.|
|/service/notifications/recipientUsers/read|Получение списка получателей, связанных с уведомлением.|
|/service/notifications/recipientUsers/write|Добавление пользователя в список получателей уведомлений.|
|/service/notifications/write|Создание или обновление уведомления издателя управления API.|
|/service/openidConnectProviders/delete|Удаляет существующий поставщик OpenID Connect.|
|/service/openidConnectProviders/read|Возвращает список поставщиков OpenID Connect или сведения о поставщике OpenID Connect.|
|/service/openidConnectProviders/write|Создает новый поставщик OpenID Connect или обновляет данные существующего.|
|/service/operationresults/read|Возвращает текущее состояние длительной операции.|
|/service/policies/delete|Удаление конфигурации политики из политик клиента.|
|/service/policies/read|Получение политик клиента или сведений о конфигурации политик клиента.|
|/service/policies/write|Определение сведений о конфигурации политик клиента.|
|/service/policySnippets/read|Получение всех фрагментов кода политики.|
|/service/portalsettings/read|Получение параметров регистрации на портале, параметров для входа на портал или параметров делегирования для портала.|
|/service/portalsettings/write|Обновление параметров регистрации на портале, параметров для входа на портал или параметров делегирования для портала.|
|/service/products/apis/delete|Удаляет API из существующего продукта.|
|/service/products/apis/read|Возвращает список интерфейсов API, добавленных в существующий продукт.|
|/service/products/apis/write|Добавляет API в существующий продукт.|
|/service/products/delete|Удаляет существующий продукт.|
|/service/products/groups/delete|Удаляет связь между существующими продуктом и группой разработчиков.|
|/service/products/groups/read|Возвращает список групп разработчиков, связанных с продуктом.|
|/service/products/groups/write|Устанавливает связь между существующими продуктом и группой разработчиков.|
|/service/products/policies/delete|Удаление конфигурации политики из политик продукта.|
|/service/products/policies/read|Получение политик продукта или сведений о конфигурации политики продукта.|
|/service/products/policies/write|Настройка сведений о конфигурации политики продукта.|
|/service/products/policy/delete|Удаляет конфигурацию политики из существующего продукта.|
|/service/products/policy/read|Возвращает конфигурацию политики существующего продукта.|
|/service/products/policy/write|Задает конфигурацию политики существующего продукта.|
|/service/products/read|Возвращает список продуктов или сведения о продукте.|
|/service/products/subscriptions/read|Возвращает список подписок на продукт.|
|/service/products/tags/delete|Удаление связи существующего тега с существующим продуктом.|
|/service/products/tags/read|Получение тегов, связанных с продуктом, или сведений о теге.|
|/service/products/tags/write|Связывание существующего тега с существующим продуктом.|
|/service/products/write|Создает новый продукт или обновляет данные существующего.|
|/service/properties/delete|Удаляет существующее свойство.|
|/service/properties/read|Возвращает список всех свойств или сведения об указанном свойстве.|
|/service/properties/write|Создает новое свойство или обновляет значение указанного свойства.|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для службы управления API.|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для службы управления API.|
|/service/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для службы управления API.|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для службы управления API.|
|/service/quotas/periods/read|Получение значения счетчика квоты за период.|
|/service/quotas/periods/write|Настройка текущего значения счетчика квоты.|
|/service/quotas/read|Получение значений квоты.|
|/service/quotas/write|Настройка текущего значения счетчика квоты.|
|/service/read|Чтение метаданных для экземпляра службы управления API.|
|/service/reports/read|Получение отчета, объединенного по периодам времени, по географическим регионам или по разработчикам; получение отчета, объединенного по продуктам; получение отчета, объединенного по API-интерфейсам, операциям или по подпискам; получение запросов, передающих данные.|
|/service/restore/action|Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения.|
|/service/subscriptions/delete|Удаляет подписку. С помощью этой операции можно удалить подписку.|
|/service/subscriptions/read|Возвращает список подписок на продукт или сведения о подписке на продукт.|
|/service/subscriptions/regeneratePrimaryKey/action|Повторно создает первичный ключ подписки.|
|/service/subscriptions/regenerateSecondaryKey/action|Повторно создает вторичный ключ подписки.|
|/service/subscriptions/write|Подписывает существующего пользователя на имеющийся продукт или обновляет данные существующей подписки. С помощью этой операции можно обновить подписку.|
|/service/tagResources/read|Получение списка тегов со связанными ресурсами.|
|/service/tags/delete|Удаление существующего тега.|
|/service/tags/read|Получение списка тегов или сведений о теге.|
|/service/tags/write|Добавление нового тега или обновление сведений о существующем.|
|/service/templates/delete|Сброс к шаблону электронного сообщения по умолчанию службы управления API.|
|/service/templates/read|Получение всех шаблонов сообщений или сведений о шаблоне сообщения службы управления API.|
|/service/templates/write|Создание или обновление шаблона сообщения службы управления API.|
|/service/tenant/delete|Удаляет конфигурацию политики для клиента.|
|/service/tenant/deploy/action|Выполняет задачу развертывания для применения изменений из указанной ветви Git к конфигурации в базе данных.|
|/service/tenant/operationResults/read|Возвращает список результатов операций или результат определенной операции.|
|/service/tenant/read|Получение конфигурации политики для клиента или сведений о доступе к клиенту.|
|/service/tenant/regeneratePrimaryKey/action|Повторно создает первичный ключ доступа.|
|/service/tenant/regenerateSecondaryKey/action|Повторно создает вторичный ключ доступа.|
|/service/tenant/save/action|Создает фиксацию с использованием моментального снимка конфигурации для указанной ветви в репозитории.|
|/service/tenant/syncState/read|Возвращает состояние последней синхронизации с Git.|
|/service/tenant/validate/action|Проверяет изменения из указанной ветви Git.|
|/service/tenant/write|Установка конфигурации политики для клиента или обновление сведений о доступе к клиенту.|
|/service/updatecertificate/action|Передает SSL-сертификата для службы управления API.|
|/service/updatehostname/action|Позволяет настроить, обновить или удалить имена личных доменов для службы управления API.|
|/service/users/action|Регистрация нового пользователя.|
|/service/users/applications/attachments/delete|Удаление вложения.|
|/service/users/applications/attachments/read|Получение вложений приложения или вложения.|
|/service/users/applications/attachments/write|Добавление вложения в приложение.|
|/service/users/applications/delete|Удаление существующего приложения.|
|/service/users/applications/read|Получение списка всех пользовательских приложений или сведений о приложении службы управления API.|
|/service/users/applications/write|Регистрация приложения в службе управления API или обновление сведений о приложении.|
|/service/users/delete|Удаляет учетную запись пользователя.|
|/service/users/generateSsoUrl/action|Создает URL-адрес единого входа. URL-адрес, который можно использовать для доступа к порталу администрирования.|
|/service/users/groups/read|Возвращает список групп пользователя.|
|/service/users/keys/read|Возвращает список ключей пользователя.|
|/service/users/read|Возвращает список зарегистрированных пользователей или сведения об учетной записи пользователя.|
|/service/users/subscriptions/read|Возвращает список подписок пользователя.|
|/service/users/token/action|Получение токена доступа для пользователя.|
|/service/users/write|Регистрирует нового пользователя или обновляет данные учетной записи существующего пользователя.|
|/service/write|Создание экземпляра службы управления API.|
|/unregister/action|Отменяет регистрацию подписки для поставщика ресурсов Microsoft.ApiManagement.|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Операция | ОПИСАНИЕ |
|---|---|
|/checkAccess/action|Проверка того, может ли вызывающий объект выполнять определенное действие.|
|/classicAdministrators/delete|Удаляет учетные данные администратора из подписки.|
|/classicAdministrators/read|Считывает администраторов для подписки.|
|/classicAdministrators/write|Добавляет или изменяет администратора подписки.|
|/elevateAccess/action|Предоставляет вызывающему доступ с правами администратора для области действия клиента.|
|/locks/delete|Удаляет блокировки в указанной области.|
|/locks/read|Возвращает блокировки в указанной области.|
|/locks/write|Добавляет блокировки в указанной области.|
|/permissions/read|Выводит список всех разрешений, которые есть у вызывающего в заданной области.|
|/policyAssignments/delete|Удаляет назначение политики в указанной области.|
|/policyAssignments/read|Возвращает сведения о назначении политики.|
|/policyAssignments/write|Создает назначение политики в указанной области.|
|/policyDefinitions/delete|Удаляет определение политики.|
|/policyDefinitions/read|Возвращает сведения об определении политики.|
|/policyDefinitions/write|Создает определение настраиваемой политики.|
|/policySetDefinitions/delete|Удаление определения набора политик.|
|/policySetDefinitions/read|Получение сведений об определении набора политик.|
|/policySetDefinitions/write|Создание пользовательского определения набора политик.|
|/providerOperations/read|Возвращает операции для всех поставщиков ресурсов, которые могут быть использованы в определениях ролей.|
|/roleAssignments/delete|Здесь описывается удаление назначения роли в указанной области.|
|/roleAssignments/read|Возвращает сведения о назначении роли.|
|/roleAssignments/write|Создает назначение роли в указанной области.|
|/roleDefinitions/delete|Удаляет указанное определение настраиваемой роли.|
|/roleDefinitions/read|Возвращает сведения об определении роли.|
|/roleDefinitions/write|Создает или обновляет определение настраиваемой роли с помощью указанных разрешений и назначаемых областей.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Операция | ОПИСАНИЕ |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Чтение сведений о регистрации Azure Automation DSC.|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Запись запроса на повторное создание ключей Azure Automation DSC.|
|/automationAccounts/certificates/delete|Удаляет ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/certificates/read|Возвращает ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/certificates/write|Создает или обновляет ресурс сертификата службы автоматизации Azure.|
|/automationAccounts/compilationjobs/read|Чтение содержимого компиляции Azure Automation DSC.|
|/automationAccounts/compilationjobs/write|Запись содержимого компиляции Azure Automation DSC.|
|/automationAccounts/configurations/delete|Удаление содержимого Azure Automation DSC.|
|/automationAccounts/configurations/getCount/action|Чтение количества содержимого Azure Automation DSC.|
|/automationAccounts/configurations/read|Возвращает содержимое Azure Automation DSC.|
|/automationAccounts/configurations/write|Запись содержимого Azure Automation DSC.|
|/automationAccounts/connections/delete|Удаляет ресурс подключения службы автоматизации Azure.|
|/automationAccounts/connections/read|Возвращает ресурс подключения службы автоматизации Azure.|
|/automationAccounts/connections/write|Создает или обновляет ресурс подключения службы автоматизации Azure.|
|/automationAccounts/connectionTypes/delete|Удаляет ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/connectionTypes/read|Возвращает ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/connectionTypes/write|Создает ресурс типа подключения службы автоматизации Azure.|
|/automationAccounts/credentials/delete|Удаляет ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/credentials/read|Возвращает ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/credentials/write|Создает или обновляет ресурс учетных данных службы автоматизации Azure.|
|/automationAccounts/delete|Удаляет учетную запись службы автоматизации Azure.|
|/automationAccounts/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/automationAccounts/diagnosticSettings/write|Настройка параметра диагностики для ресурса.|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Удаляет ресурсы гибридной рабочей роли Runbook.|
|/automationAccounts/hybridRunbookWorkerGroups/read|Считывает ресурсы гибридной рабочей роли Runbook.|
|/automationAccounts/jobs/output/action|Возвращает выходные данные задания.|
|/automationAccounts/jobs/output/action|Возвращает выходные данные задания.|
|/automationAccounts/jobs/read|Возвращает задание службы автоматизации Azure.|
|/automationAccounts/jobs/read|Возвращает задание службы автоматизации Azure.|
|/automationAccounts/jobs/resume/action|Возобновляет выполнение задания службы автоматизации Azure.|
|/automationAccounts/jobs/resume/action|Возобновляет выполнение задания службы автоматизации Azure.|
|/automationAccounts/jobs/runbookContent/action|Возвращает содержимое runbook службы автоматизации Azure во время выполнения задания.|
|/automationAccounts/jobs/runbookContent/action|Возвращает содержимое runbook службы автоматизации Azure во время выполнения задания.|
|/automationAccounts/jobs/stop/action|Останавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/stop/action|Останавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/streams/read|Возвращает поток задания службы автоматизации Azure.|
|/automationAccounts/jobs/streams/read|Возвращает поток задания службы автоматизации Azure.|
|/automationAccounts/jobs/suspend/action|Приостанавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/suspend/action|Приостанавливает задание службы автоматизации Azure.|
|/automationAccounts/jobs/write|Создает задание службы автоматизации Azure.|
|/automationAccounts/jobs/write|Создает задание службы автоматизации Azure.|
|/automationAccounts/jobSchedules/delete|Удаляет расписание заданий службы автоматизации Azure.|
|/automationAccounts/jobSchedules/read|Возвращает расписание заданий службы автоматизации Azure.|
|/automationAccounts/jobSchedules/write|Создает расписание заданий службы автоматизации Azure.|
|/automationAccounts/linkedWorkspace/read|Получение рабочей области, связанной с учетной записью автоматизации.|
|/automationAccounts/logDefinitions/read|Получение доступных журналов учетной записи автоматизации.|
|/automationAccounts/modules/activities/read|Получение действий службы автоматизации Azure.|
|/automationAccounts/modules/delete|Удаляет модуль службы автоматизации Azure.|
|/automationAccounts/modules/read|Возвращает модуль службы автоматизации Azure.|
|/automationAccounts/modules/write|Создает или обновляет модуль службы автоматизации Azure.|
|/automationAccounts/nodeConfigurations/delete|Удаление конфигурации узла Azure Automation DSC.|
|/automationAccounts/nodeConfigurations/read|Чтение конфигурации узла Azure Automation DSC.|
|/automationAccounts/nodeConfigurations/readContent/action|Чтение содержимого конфигурации узла Azure Automation DSC.|
|/automationAccounts/nodeConfigurations/write|Запись конфигурации узла Azure Automation DSC.|
|/automationAccounts/nodes/delete|Удаление узлов Azure Automation DSC.|
|/automationAccounts/nodes/read|Чтение узлов Azure Automation DSC.|
|/automationAccounts/nodes/reports/read|Чтение содержимого отчета Azure Automation DSC.|
|/automationAccounts/nodes/reports/read|Чтение отчетов Azure Automation DSC.|
|/automationAccounts/objectDataTypes/fields/read|Получение полей типов службы автоматизации Azure.|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Получение определений метрик службы автоматизации.|
|/automationAccounts/read|Возвращает учетную запись службы автоматизации Azure.|
|/automationAccounts/runbooks/delete|Удаляет runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/publish/action|Публикует черновик runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/read|Возвращает черновик runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/readContent/action|Возвращает содержимое черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/read|Возвращает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/resume/action|Возобновляет выполнение тестового задания черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/stop/action|Останавливает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Приостанавливает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/testJob/write|Создает тестовое задание черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/undoEdit/action|Отменяет правки в черновике runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/draft/writeContent/action|Создает содержимое черновика runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/read|Возвращает runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/readContent/action|Возвращает содержимое runbook службы автоматизации Azure.|
|/automationAccounts/runbooks/write|Создает или обновляет runbook службы автоматизации Azure.|
|/automationAccounts/schedules/delete|Удаляет ресурс расписания службы автоматизации Azure.|
|/automationAccounts/schedules/read|Возвращает ресурс расписания службы автоматизации Azure.|
|/automationAccounts/schedules/write|Создает или обновляет ресурс расписания службы автоматизации Azure.|
|/automationAccounts/statistics/read|Получение статистики из службы автоматизации Azure.|
|/automationAccounts/usages/read|Получение данных использования из службы автоматизации Azure.|
|/automationAccounts/variables/delete|Удаляет ресурс переменной службы автоматизации Azure.|
|/automationAccounts/variables/read|Считывает ресурс переменной службы автоматизации Azure.|
|/automationAccounts/variables/write|Создает или обновляет ресурс переменной службы автоматизации Azure.|
|/automationAccounts/watchers/streams/read|Получение потока заданий наблюдателя из службы автоматизации Azure.|
|/automationAccounts/webhooks/delete|Удаляет webhook службы автоматизации Azure. |
|/automationAccounts/webhooks/generateUri/action|Создает универсальный код ресурса (URI) для webhook службы автоматизации Azure.|
|/automationAccounts/webhooks/read|Считывает runbook службы автоматизации Azure.|
|/automationAccounts/webhooks/write|Создает или обновляет webhook службы автоматизации Azure.|
|/automationAccounts/write|Создает или обновляет учетную запись службы автоматизации Azure.|
|/automationAccounts/write|Создает или обновляет учетную запись службы автоматизации Azure.|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Операция | ОПИСАНИЕ |
|---|---|
|/b2cDirectories/delete|Удаление ресурса каталога B2C.|
|/b2cDirectories/read|Просмотр ресурса каталога B2C.|
|/b2cDirectories/write|Создание или обновление ресурса каталога B2C.|
|/operations/read|Чтение всех операций API, доступных для поставщика ресурсов Microsoft.AzureActiveDirectory.|
|/register/action|Регистрация подписки в поставщике ресурсов Microsoft.AzureActiveDirectory.|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Операция | ОПИСАНИЕ |
|---|---|
|/Operations/read|Получение свойств операции поставщика ресурсов.|
|/register/action|Регистрация подписки в поставщике ресурсов Microsoft.AzureStack.|
|/registrations/customerSubscriptions/delete|Удаление подписки клиента Azure Stack.|
|/registrations/customerSubscriptions/read|Получение свойств подписки клиента Azure Stack.|
|/registrations/customerSubscriptions/write|Создание или обновление подписки клиента Azure Stack.|
|/registrations/delete|Удаление регистрации в Azure Stack.|
|/registrations/getActivationKey/action|Получение последнего созданного ключа активации Azure Stack.|
|/registrations/products/listDetails/action|Получение подробных сведений о продукте из Azure Stack Marketplace.|
|/registrations/products/read|Получение свойств продукта из Azure Stack Marketplace.|
|/registrations/read|Получение свойств регистрации в Azure Stack.|
|/registrations/write|Создание или обновление регистрации в Azure Stack.|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Операция | ОПИСАНИЕ |
|---|---|
|/batchAccounts/applications/delete|Удаляет приложение.|
|/batchAccounts/applications/read|Выводит список приложений или возвращает свойства приложения.|
|/batchAccounts/applications/versions/activate/action|Активирует пакет приложения.|
|/batchAccounts/applications/versions/delete|Удаляет пакет приложения.|
|/batchAccounts/applications/versions/read|Возвращает свойства пакета приложения.|
|/batchAccounts/applications/versions/write|Создает новый пакет приложения или обновляет существующий.|
|/batchAccounts/applications/write|Создает новое приложение или обновляет существующее.|
|/batchAccounts/certificateOperationResults/read|Получение результатов длительных операций с сертификатом в учетной записи пакетной службы.|
|/batchAccounts/certificates/cancelDelete/action|Отмена неудачного удаления сертификата в учетной записи пакетной службы.|
|/batchAccounts/certificates/delete|Удаление сертификата из учетной записи пакетной службы.|
|/batchAccounts/certificates/read|Вывод списка сертификатов в учетной записи пакетной службы или получение свойств сертификата.|
|/batchAccounts/certificates/write|Создание нового сертификата в учетной записи пакетной службы или обновление существующего сертификата.|
|/batchAccounts/delete|Удаляет учетную запись пакетной службы.|
|/batchAccounts/listkeys/action|Отображает ключи доступа для учетной записи пакетной службы.|
|/batchAccounts/operationResults/read|Получение результатов длительных операций в учетной записи пакетной службы.|
|/batchAccounts/poolOperationResults/read|Получение результатов длительных операций с пулом в учетной записи пакетной службы.|
|/batchAccounts/pools/delete|Удаление пула из учетной записи пакетной службы.|
|/batchAccounts/pools/disableAutoscale/action|Отключение автоматического масштабирования в пуле учетной записи пакетной службы.|
|/batchAccounts/pools/read|Вывод списка пулов в учетной записи пакетной службы или свойств пула.|
|/batchAccounts/pools/stopResize/action|Остановка текущей операции изменения размера в пуле учетной записи пакетной службы.|
|/batchAccounts/pools/upgradeOs/action|Обновление операционной системы в пуле учетной записи пакетной службы.|
|/batchAccounts/pools/write|Создание нового пула в учетной записи пакетной службы или обновление существующего пула.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов пакетной службы.|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик пакетной службы.|
|/batchAccounts/read|Выводит список учетных записей пакетной службы или возвращает свойства учетной записи пакетной службы.|
|/batchAccounts/regeneratekeys/action|Повторно создает ключи доступа для учетной записи пакетной службы.|
|/batchAccounts/syncAutoStorageKeys/action|Синхронизирует ключи доступа для автоматической учетной записи хранения, настроенной для учетной записи пакетной службы.|
|/batchAccounts/write|Создает новую учетную запись пакетной службы или обновляет существующую.|
|/locations/checkNameAvailability/action|Проверка того, является ли имя учетной записи допустимым и неиспользуемым.|
|/locations/quotas/read|Возвращает квоты пакетной службы для указанной подписки в заданном регионе Azure.|
|/register/action|Регистрирует подписку для поставщика ресурсов пакетной службы и позволяет создавать учетные записи пакетной службы.|
|/unregister/action|Отмена регистрации подписки для поставщика ресурсов пакетной службы, которая не позволяет создавать учетные записи пакетной службы.|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Операция | ОПИСАНИЕ |
|---|---|
|/clusters/delete|Удаление кластера Batch AI.|
|/clusters/read|Вывод списка кластеров Batch AI или получение свойств кластера.|
|/clusters/remoteLoginInformation/action|Получение сведений об удаленном входе для кластера Batch AI.|
|/clusters/write|Создание нового кластера Batch AI или обновление существующего.|
|/fileservers/delete|Удаление файлового сервера Batch AI.|
|/fileservers/read|Вывод списка файловых серверов Batch AI или получение свойств такого сервера.|
|/fileservers/resume/action|Возобновление работы файлового сервера Batch AI.|
|/fileservers/suspend/action|Приостановка работы файлового сервера Batch AI.|
|/fileservers/write|Создание нового файлового сервера Batch AI или обновление существующего.|
|/jobs/delete|Удаление задания Batch AI.|
|/jobs/read|Вывод списка заданий Batch AI или получение свойств такого задания.|
|/jobs/remoteLoginInformation/action|Получение сведений об удаленном входе для задания Batch AI.|
|/jobs/terminate/action|Завершение задания Batch AI.|
|/jobs/write|Создание нового задания Batch AI или обновление существующего.|
|/register/action|Регистрация подписки поставщика ресурсов Batch AI и разрешение создания ресурсов.|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Операция | ОПИСАНИЕ |
|---|---|
|/billingPeriods/read|Вывод списка доступных интервалов выставления счетов.|
|/invoices/read|Выводит список доступных счетов.|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Операция | ОПИСАНИЕ |
|---|---|
|/mapApis/Delete|Операция удаления.|
|/mapApis/listSecrets/action|Отображает секреты.|
|/mapApis/listSingleSignOnToken/action|Считывает маркер авторизации единого входа для ресурса.|
|/mapApis/Read|Операция чтения.|
|/mapApis/regenerateKey/action|Повторно создает ключ.|
|/mapApis/Write|Операции записи.|
|/Operations/read|Описание операции.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Операция | ОПИСАНИЕ |
|---|---|
|/checknameavailability/action|Проверяет, доступно ли имя для нового кэша Redis.|
|/operations/read|Вывод списка операций, которые поддерживает поставщик Microsoft.Cache.|
|/redis/delete|Удаляет весь кэш Redis.|
|/redis/export/action|Экспортирует данные Redis в большие двоичные объекты с префиксом в службе хранилища в указанном формате.|
|/redis/firewallRules/delete|Удаляет правила IP-адресов брандмауэра для кэша Redis.|
|/redis/firewallRules/read|Возвращает правила IP-адресов брандмауэра для кэша Redis.|
|/redis/firewallRules/write|Изменяет правила IP-адресов брандмауэра для кэша Redis.|
|/redis/forceReboot/action|Принудительно перезапускает экземпляр кэша. При этом возможна потеря данных.|
|/redis/import/action|Импортирует в Redis данные в указанном формате из нескольких больших двоичных объектов.|
|/redis/linkedservers/delete|Удаляет связанный сервер из кэша Redis.|
|/redis/linkedservers/read|Возвращает связанные серверы для кэша Redis.|
|/redis/linkedservers/write|Добавляет связанный сервер для кэша Redis.|
|/redis/listKeys/action|Отображает значения ключей доступа к кэшу Redis на портале управления.|
|/redis/listUpgradeNotifications/read|Выводит список последних уведомлений об обновлениях для клиента кэша.|
|/redis/locations/operationresults/read|Получение результата длительных операций, для которых заголовок Location был ранее возвращен клиенту.|
|/redis/metricDefinitions/read|Возвращает доступные метрики для кэша Redis.|
|/redis/patchSchedules/delete|Удаляет расписание исправления для кэша Redis.|
|/redis/patchSchedules/read|Возвращает расписание исправления для кэша Redis.|
|/redis/patchSchedules/write|Изменяет расписание исправления для кэша Redis.|
|/redis/read|Отображает параметры и конфигурацию кэша Redis на портале управления.|
|/redis/regenerateKey/action|Изменяет значения ключей доступа к кэшу Redis на портале управления.|
|/redis/start/action|Запускает экземпляр кэша.|
|/redis/stop/action|Останавливает экземпляр кэша.|
|/redis/write|Изменяет параметры и конфигурацию кэша Redis на портале управления.|
|/register/action|Регистрирует поставщик ресурсов Microsoft.Cache для подписки.|
|/unregister/action|Отменяет регистрацию поставщика ресурсов Microsoft.Cache для подписки.|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Регистрация поставщика ресурсов емкости и разрешение создания ресурсов емкости.|
|/reservationorders/action|Обновление резервирования.|
|/reservationorders/delete|Удаление резервирования.|
|/reservationorders/read|Чтение всех резервирований.|
|/reservationorders/reservations/action|Обновление резервирования.|
|/reservationorders/reservations/delete|Удаление резервирования.|
|/reservationorders/reservations/read|Чтение всех резервирований.|
|/reservationorders/reservations/revisions/read|Чтение всех резервирований.|
|/reservationorders/reservations/write|Создание резервирования.|
|/reservationorders/write|Создание резервирования.|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Операция | ОПИСАНИЕ |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики для ресурса.|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики для ресурса.|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для Microsoft.Cdn.|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Регистрация подписки для поставщика ресурсов CDN и разрешение создания профилей CDN.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Операция | ОПИСАНИЕ |
|---|---|
|/certificateOrders/certificates/Delete|Удаляет существующий сертификат.|
|/certificateOrders/certificates/Read|Возвращает список сертификатов.|
|/certificateOrders/certificates/Write|Добавляет новый сертификат или обновляет существующий.|
|/certificateOrders/Delete|Удаляет существующий сертификат службы приложений.|
|/certificateOrders/operations/Read|Вывод списка всех операций из регистрации сертификата службы приложений.|
|/certificateOrders/Read|Возвращает список заказов сертификатов.|
|/certificateOrders/reissue/Action|Повторно отправляет существующий заказ сертификата.|
|/certificateOrders/renew/Action|Обновляет существующий заказ сертификата.|
|/certificateOrders/resendEmail/Action|Повторно отправляет сообщение сертификата.|
|/certificateOrders/resendRequestEmails/Action|Повторно отправляет сообщения запроса на другой электронный адрес.|
|/certificateOrders/resendRequestEmails/Action|Извлекает печать сайта для выданного сертификата службы приложений.|
|/certificateOrders/retrieveCertificateActions/Action|Извлекает список действий сертификата.|
|/certificateOrders/retrieveEmailHistory/Action|Извлекает журнал электронных сообщений для сертификата.|
|/certificateOrders/verifyDomainOwnership/Action|проверка принадлежности домена;|
|/certificateOrders/Write|Добавляет новый заказ сертификата или обновляет существующий.|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Подготавливает субъект-службу для субъекта приложения-службы.|
|/register/action|Регистрирует поставщик сертификатов Майкрософт для подписки.|
|/validateCertificateRegistrationInformation/Action|Проверяет объект покупки сертификата без его отправки.|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Операция | ОПИСАНИЕ |
|---|---|
|/capabilities/read|Отображает возможности.|
|/checkDomainNameAvailability/action|Проверяет доступность заданного доменного имени.|
|/domainNames/active/write|Задает активное доменное имя.|
|/domainNames/availabilitySets/read|Отображает группу доступности для ресурса.|
|/domainNames/capabilities/read|Отображает возможности доменного имени.|
|/domainNames/delete|Удаляет доменные имена для ресурсов.|
|/domainNames/extensions/delete|Удаляет расширения доменных имен.|
|/domainNames/extensions/operationStatuses/read|Считывает состояние операции расширений доменных имен.|
|/domainNames/extensions/read|Возвращает расширения доменных имен.|
|/domainNames/extensions/write|Добавляет расширения доменных имен.|
|/domainNames/internalLoadBalancers/delete|Удаляет внутреннюю подсистему балансировки нагрузки.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Считывает состояние операции внутренних подсистем балансировки нагрузки доменных имен.|
|/domainNames/internalLoadBalancers/read|Возвращает внутренние подсистемы балансировки нагрузки.|
|/domainNames/internalLoadBalancers/write|Создает внутреннюю подсистему балансировки нагрузки.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Считывает состояние операции наборов конечных точек с балансировкой нагрузки доменных имен.|
|/domainNames/loadBalancedEndpointSets/read|Отображает наборы конечных точек с балансировкой нагрузки.|
|/domainNames/read|Возвращает доменные имена для ресурсов.|
|/domainNames/serviceCertificates/delete|Удаляет используемые сертификаты службы.|
|/domainNames/serviceCertificates/operationStatuses/read|Считывает состояние операции сертификатов службы доменных имен.|
|/domainNames/serviceCertificates/read|Возвращает используемые сертификаты службы.|
|/domainNames/serviceCertificates/write|Добавляет или изменяет используемые сертификаты службы.|
|/domainNames/slots/delete|Удаляет заданный слот развертывания.|
|/domainNames/slots/operationStatuses/read|Считывает состояние операции слотов доменных имен.|
|/domainNames/slots/read|Отображает слоты развертывания.|
|/domainNames/slots/roles/extensionReferences/delete|Удаляет ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Считывает состояние операции ссылок на расширение ролей слотов доменных имен.|
|/domainNames/slots/roles/extensionReferences/read|Возвращает ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/extensionReferences/write|Добавляет или изменяет ссылку на расширение для роли слота развертывания.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметры диагностики.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Добавляет или изменяет параметры диагностики.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Возвращает определения метрик.|
|/domainNames/slots/roles/read|Возвращает роль для слота развертывания.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Считывает состояние операции экземпляров роли для ролей слотов доменных имен.|
|/domainNames/slots/roles/roleInstances/read|Возвращает экземпляр роли.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Повторная сборка экземпляра роли.|
|/domainNames/slots/roles/roleInstances/reimage/action|Пересоздает образ экземпляра роли.|
|/domainNames/slots/roles/roleInstances/restart/action|Перезапускает экземпляры роли.|
|/domainNames/slots/start/action|Запускает слот развертывания.|
|/domainNames/slots/state/start/write|Задает состояние остановки для слота развертывания.|
|/domainNames/slots/state/stop/write|Задает состояние запуска для слота развертывания.|
|/domainNames/slots/stop/action|Приостанавливает слот развертывания.|
|/domainNames/slots/upgradeDomain/write|Обновляет домен.|
|/domainNames/slots/write|Создает или обновляет развертывание.|
|/domainNames/swap/action|Переключает промежуточный слот на рабочий слот.|
|/domainNames/write|Добавляет или изменяет доменные имена для ресурсов.|
|/moveSubscriptionResources/action|Перемещает все классические ресурсы в другую подписку.|
|/operatingSystemFamilies/read|Вывод списка семейств гостевых операционных систем, доступных в Microsoft Azure, а также версий операционных систем в каждом таком семействе.|
|/operatingSystems/read|Выводит список версий гостевой операционной системы, доступных в Microsoft Azure.|
|/quotas/read|Возвращает квоту для подписки.|
|/register/action|Выполняет регистрацию в классической вычислительной среде.|
|/resourceTypes/skus/read|Возвращает список номеров SKU для поддерживаемых типов ресурсов.|
|/validateSubscriptionMoveAvailability/action|Проверяет доступность классической операции перемещения для подписки.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с виртуальной машиной.|
|/virtualMachines/asyncOperations/read|Возвращает возможные асинхронные операции.|
|/virtualMachines/attachDisk/action|Подключает диск данных к виртуальной машине.|
|/virtualMachines/delete|Удаляет виртуальные машины.|
|/virtualMachines/detachDisk/action|Отключает диск данных от виртуальной машины.|
|/virtualMachines/disks/read|Извлекает список дисков данных.|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Скачивает RDP-файл для виртуальной машины.|
|/virtualMachines/extensions/operationStatuses/read|Считывает состояние операции расширений виртуальной машины.|
|/virtualMachines/extensions/read|Возвращает расширение виртуальной машины.|
|/virtualMachines/extensions/write|Размещает расширение виртуальной машины.|
|/virtualMachines/metrics/read|Возвращает метрики.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с сетевым интерфейсом.|
|/virtualMachines/operationStatuses/read|Считывает состояние операции виртуальных машин.|
|/virtualMachines/performMaintenance/action|Выполнение обслуживания виртуальной машины.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметры диагностики.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Добавляет или изменяет параметры диагностики.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Возвращает определения метрик.|
|/virtualMachines/read|Извлекает список виртуальных машин.|
|/virtualMachines/redeploy/action|Повторно развертывает виртуальную машину.|
|/virtualMachines/restart/action|Перезапускает виртуальные машины.|
|/virtualMachines/shutdown/action|Завершает работу виртуальной машины.|
|/virtualMachines/start/action|Запустите виртуальную машину.|
|/virtualMachines/stop/action|Останавливает виртуальную машину.|
|/virtualMachines/write|Добавляет или изменяет виртуальные машины.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Операция | ОПИСАНИЕ |
|---|---|
|/gatewaySupportedDevices/read|Извлекает список поддерживаемых устройств.|
|/networkSecurityGroups/delete|Удаляет группу безопасности сети.|
|/networkSecurityGroups/operationStatuses/read|Считывает состояние операции группы безопасности сети.|
|/networkSecurityGroups/read|Возвращает группу безопасности сети.|
|/networkSecurityGroups/securityRules/delete|Удаляет правило безопасности.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Считывает состояние операции правил безопасности группы безопасности сети.|
|/networkSecurityGroups/securityRules/read|Возвращает правило безопасности.|
|/networkSecurityGroups/securityRules/write|Добавляет или обновляет правило безопасности.|
|/networkSecurityGroups/write|Добавляет новую группу безопасности сети.|
|/quotas/read|Возвращает квоту для подписки.|
|/register/action|Выполняет регистрацию в классической сети.|
|/reservedIps/delete|Удаляет зарезервированный IP-адрес.|
|/reservedIps/join/action|Присоединяет зарезервированный IP-адрес.|
|/reservedIps/link/action|Привязывает зарезервированный IP-адрес.|
|/reservedIps/operationStatuses/read|Считывает состояние операции для зарезервированных IP-адресов.|
|/reservedIps/read|Возвращает зарезервированные IP-адреса.|
|/reservedIps/write|Добавляет новый зарезервированный IP-адрес.|
|/virtualNetworks/capabilities/read|Отображает возможности.|
|/virtualNetworks/checkIPAddressAvailability/action|Проверяет доступность заданного IP-адреса в виртуальной сети.|
|/virtualNetworks/delete|Удаляет виртуальную сеть.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Отменяет отмену сертификата клиента.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Считывает отозванные сертификаты клиента.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Отменяет сертификат клиента.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Удаляет сертификат клиента шлюза виртуальной сети.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Скачивает сертификат по отпечатку.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Отображает пакет сертификата шлюза виртуальной сети.|
|/virtualNetworks/gateways/clientRootCertificates/read|Выполняет поиск корневых сертификатов клиента.|
|/virtualNetworks/gateways/clientRootCertificates/write|Передает новый корневой сертификат клиента.|
|/virtualNetworks/gateways/connections/connect/action|Устанавливает подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/connections/disconnect/action|Отключает подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/connections/read|Извлекает список подключений.|
|/virtualNetworks/gateways/connections/test/action|Тестирует подключение шлюза типа "сеть — сеть".|
|/virtualNetworks/gateways/delete|Удаляет шлюз виртуальной сети.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Скачивает сценарий конфигурации устройства.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Скачивает данные диагностики шлюза.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Извлекает ключ службы канала.|
|/virtualNetworks/gateways/listPackage/action|Отображает пакет шлюза виртуальной сети.|
|/virtualNetworks/gateways/operationStatuses/read|Считывает состояние операции шлюзов виртуальной сети.|
|/virtualNetworks/gateways/packages/read|Возвращает пакет шлюза виртуальной сети.|
|/virtualNetworks/gateways/read|Возвращает шлюзы виртуальной сети.|
|/virtualNetworks/gateways/startDiagnostics/action|Запускает диагностику шлюза виртуальной сети.|
|/virtualNetworks/gateways/stopDiagnostics/action|Останавливает диагностику шлюза виртуальной сети.|
|/virtualNetworks/gateways/write|Добавляет шлюз виртуальной сети.|
|/virtualNetworks/join/action|Подключает виртуальную сеть.|
|/virtualNetworks/operationStatuses/read|Считывает состояние операции виртуальных сетей.|
|/virtualNetworks/peer/action|Создает пиринг между виртуальными сетями.|
|/virtualNetworks/read|Возвращает виртуальную сеть.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Удаляет группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Считывает состояние операции группы безопасности сети, связанной с подсетью виртуальной сети.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Возвращает группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Добавляет группу безопасности сети, связанную с подсетью.|
|/virtualNetworks/write|Добавляет новую виртуальную сеть.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Операция | ОПИСАНИЕ |
|---|---|
|/capabilities/read|Отображает возможности.|
|/checkStorageAccountAvailability/action|Проверяет доступность учетной записи хранения.|
|/disks/read|Возвращает диск в учетной записи хранения.|
|/images/read|Возвращает образ.|
|/osImages/read|Возвращает образ операционной системы.|
|/publicImages/read|Возвращает общедоступный образ виртуальной машины.|
|/quotas/read|Возвращает квоту для подписки.|
|/register/action|Выполняет регистрацию в классической службе хранилища.|
|/storageAccounts/delete|Удаляет учетную запись хранения.|
|/storageAccounts/disks/delete|Удаляет заданный диск из учетной записи хранения.|
|/storageAccounts/disks/operationStatuses/read|Считывает состояние операции ресурса.|
|/storageAccounts/disks/read|Возвращает диск в учетной записи хранения.|
|/storageAccounts/disks/write|Добавляет диск учетной записи хранения.|
|/storageAccounts/images/delete|Удаляет заданный образ из учетной записи хранения.|
|/storageAccounts/images/read|Возвращает образ в учетной записи хранения.|
|/storageAccounts/listKeys/action|Отображает ключи доступа для учетных записей хранения.|
|/storageAccounts/operationStatuses/read|Считывает состояние операции ресурса.|
|/storageAccounts/osImages/delete|Удаляет заданный образ операционной системы из учетной записи хранения.|
|/storageAccounts/osImages/read|Возвращает заданный образ операционной системы в учетной записи хранения.|
|/storageAccounts/read|Возвращает учетную запись хранения для заданной учетной записи.|
|/storageAccounts/regenerateKey/action|Повторно создает существующие ключи доступа для учетной записи хранения.|
|/storageAccounts/services/diagnosticSettings/read|Возвращает параметры диагностики.|
|/storageAccounts/services/diagnosticSettings/write|Добавляет или изменяет параметры диагностики.|
|/storageAccounts/services/metricDefinitions/read|Возвращает определения метрик.|
|/storageAccounts/services/metrics/read|Возвращает метрики.|
|/storageAccounts/services/read|Возвращает доступные службы.|
|/storageAccounts/write|Добавляет новую учетную запись хранения.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/delete|Удаляет учетные записи API.|
|/accounts/listKeys/action|Отображает ключи.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для Cognitive Services.|
|/accounts/read|Считывает учетные записи API.|
|/accounts/regenerateKey/action|Повторно создает ключ.|
|/accounts/skus/read|Считывает доступные номера SKU для существующего ресурса.|
|/accounts/usages/read|Возвращает данные об использовании квоты для существующего ресурса.|
|/accounts/write|Записывает учетные записи API.|
|/locations/checkSkuAvailability/action|Чтение доступных номеров SKU для подписки.|
|/Operations/read|Вывод списка всех доступных операций.|
|/register/action|Регистрация подписки для Cognitive Services.|
|/skus/read|Чтение доступных номеров SKU для Cognitive Services.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Операция | ОПИСАНИЕ |
|---|---|
|/RateCard/read|Возвращает данные предложения, метаданные ресурсов и единиц измерения и тарифы для заданной подписки.|
|/UsageAggregates/read|Извлекает данные о потреблении ресурсов Microsoft Azure по подписке. Результат содержит статистические данные об использовании, а также сведения о подписке и ресурсах за определенный диапазон времени.|

## <a name="microsoftcompute"></a>Microsoft.Compute;

| Операция | ОПИСАНИЕ |
|---|---|
|/availabilitySets/delete|Удаляет группу доступности.|
|/availabilitySets/read|Возвращает свойства группы доступности.|
|/availabilitySets/vmSizes/read|Выводит список доступных размеров для создания или обновления виртуальной машины в группе доступности.|
|/availabilitySets/write|Создает новую группу доступности или обновляет существующую.|
|/disks/beginGetAccess/action|Возвращает универсальный код ресурса (URI) SAS диска для доступа к большому двоичному объекту.|
|/disks/delete|Удаляет диск.|
|/disks/endGetAccess/action|Отменяет универсальный код ресурса (URI) SAS диска.|
|/disks/read|Возвращает свойства диска.|
|/disks/write|Создает новый диск или обновляет существующий.|
|/images/delete|Удаляет кластер.|
|/images/read|Возвращает свойства образа.|
|/images/write|Создает новый образ или обновляет существующий.|
|/locations/capsOperations/read|Получение состояния асинхронной операции ограничения.|
|/locations/diskOperations/read|Получение состояния асинхронной операции диска.|
|/locations/operations/read|Возвращает состояние асинхронной операции.|
|/locations/publishers/artifacttypes/offers/read|Получение свойств предложения образа платформы.|
|/locations/publishers/artifacttypes/offers/skus/read|Получение свойств номера Sku образа платформы.|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Получение свойств версии образа платформы.|
|/locations/publishers/artifacttypes/types/read|Получение свойств типа расширения виртуальной машины.|
|/locations/publishers/artifacttypes/types/versions/read|Получение свойств версии расширения виртуальной машины.|
|/locations/publishers/read|Получение свойств издателя.|
|/locations/runCommands/read|Вывод списка доступных команд запуска в расположении.|
|/locations/usages/read|Возвращает ограничения службы и текущее количество использованных вычислительных ресурсов подписки в расположении.|
|/locations/vmSizes/read|Выводит список доступных размеров виртуальных машин в расположении.|
|/operations/read|Выводит список операций, доступных в поставщике ресурсов Microsoft.Compute.|
|/register/action|Регистрирует подписку в поставщике ресурсов Microsoft.Compute.|
|/restorePointCollections/delete|Удаляет коллекцию точек восстановления и содержащиеся в ней точки восстановления.|
|/restorePointCollections/read|Возвращает свойства коллекции точек восстановления.|
|/restorePointCollections/restorePoints/delete|Удаляет точку восстановления.|
|/restorePointCollections/restorePoints/read|Возвращает свойства точки восстановления.|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Возвращает свойства точки восстановления вместе с универсальными кодами ресурсов (URI) SAS больших двоичных объектов.|
|/restorePointCollections/restorePoints/write|Создает точку восстановления.|
|/restorePointCollections/write|Создает новую коллекцию точек восстановления или обновляет существующую.|
|/sharedVMImages/delete|Удаление образа общей виртуальной машины.|
|/sharedVMImages/read|Получение свойств образа общей виртуальной машины.|
|/sharedVMImages/versions/delete|Удаление версии образа общей виртуальной машины.|
|/sharedVMImages/versions/read|Получение свойств версии образа общей виртуальной машины.|
|/sharedVMImages/versions/replicate/action|Репликация версии образа общей виртуальной машины в целевые регионы.|
|/sharedVMImages/versions/write|Создание новой версии образа общей виртуальной машины или обновление существующей.|
|/sharedVMImages/write|Создание нового образа общей виртуальной машины или обновление существующего.|
|/skus/read|Получение списка номеров SKU Microsoft.Compute для вашей подписки.|
|/snapshots/beginGetAccess/action|Получение универсального кода ресурса (URI) SAS моментального снимка для доступа к большому двоичному объекту.|
|/snapshots/delete|Удаляет моментальный снимок.|
|/snapshots/endGetAccess/action|Отмена универсального кода ресурса (URI) SAS моментального снимка.|
|/snapshots/read|Возвращает свойства моментального снимка.|
|/snapshots/write|Создает новый моментальный снимок или обновляет существующий.|
|/virtualMachines/capture/action|Записывает виртуальную машину, копируя ее виртуальных жесткие диски, и создает шаблон, который может использоваться для создания идентичных виртуальных машин.|
|/virtualMachines/convertToManagedDisks/action|Преобразует диски на основе больших двоичных объектов виртуальной машины в управляемые диски.|
|/virtualMachines/deallocate/action|Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы.|
|/virtualMachines/delete|Удаляет виртуальную машину.|
|/virtualMachines/extensions/delete|Удаляет расширение виртуальной машины.|
|/virtualMachines/extensions/read|Возвращает свойства расширения виртуальной машины.|
|/virtualMachines/extensions/write|Создает новое расширение виртуальной машины или обновляет существующее.|
|/virtualMachines/generalize/action|Задает состояние "Универсальная" для виртуальной машины и подготавливает ее для записи.|
|/virtualMachines/instanceView/read|Возвращает состояние выполнения виртуальной машины и ее ресурсов.|
|/virtualMachines/performMaintenance/action|Выполнение операции обслуживания виртуальной машины.|
|/virtualMachines/powerOff/action|Завершает работу виртуальной машины. Обратите внимание, что плата за виртуальную машину по-прежнему будет взиматься.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Чтение определений метрик виртуальной машины.|
|/virtualMachines/read|Возвращает свойства виртуальной машины.|
|/virtualMachines/redeploy/action|Повторно развертывает виртуальную машину.|
|/virtualMachines/reimage/action|Пересоздание образа виртуальной машины, которая использует разностный диск.|
|/virtualMachines/restart/action|Перезапускает виртуальную машину.|
|/virtualMachines/runCommand/action|Выполнение предопределенного скрипта на виртуальной машине.|
|/virtualMachines/start/action|Запускает виртуальную машину.|
|/virtualMachines/vmSizes/read|Выводит список доступных размеров для обновления виртуальной машины.|
|/virtualMachines/write|Создает новую виртуальную машину или обновляет существующую.|
|/virtualMachineScaleSets/deallocate/action|Завершение работы вычислительных ресурсов и их освобождение для экземпляров виртуальной машины в масштабируемом наборе виртуальных машин. |
|/virtualMachineScaleSets/delete|Удаление масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/delete/action|Удаление экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/extensions/delete|Удаление расширения масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/extensions/read|Получение свойств расширения масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/extensions/write|Создание расширения масштабируемого набора виртуальных машин или обновление существующего.|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Ручное выполнение зависшего обновления доменов обновления платформы в масштабируемом наборе виртуальных машин Service Fabric.|
|/virtualMachineScaleSets/instanceView/read|Получение представления экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/manualUpgrade/action|Выполнение ручного обновления экземпляров до последней модели масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/networkInterfaces/read|Получение свойства всех сетевых интерфейсов масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/osUpgradeHistory/read|Получение журнала обновлений ОС для масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/performMaintenance/action|Выполнение планового обслуживания экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/powerOff/action|Завершение работы экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Чтение определений метрик масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/publicIPAddresses/read|Получение свойств всех общедоступных IP-адресов масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/read|Получение свойств масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/redeploy/action|Повторное развертывание экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/reimage/action|Повторное создание образов экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/restart/action|Перезапуск экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Отмена последовательного обновления масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/rollingUpgrades/read|Получение состояния последнего последовательного обновления масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/scale/action|Проверка того, возможно ли увеличение или уменьшение числа экземпляров масштабируемого набора виртуальных машин до указанного значения.|
|/virtualMachineScaleSets/skus/read|Вывод списка действительных номеров SKU для существующего масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/start/action|Запуск экземпляров масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Завершает работу вычислительных ресурсов и освобождает их для виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/delete|Удаляет конкретную виртуальную машину в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Извлекает представление экземпляров виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Получение свойства общедоступного IP-адреса, созданного с использованием масштабируемого набора виртуальных машин. Масштабируемый набор виртуальных машин может создать не более одного общедоступного IP-адреса на IP-конфигурацию (частный IP-адрес).|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Получение свойств одной или всех IP-конфигураций сетевого интерфейса, созданного с помощью масштабируемого набора виртуальных машин. IP-конфигурации представляют частные IP-адреса.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Получение свойств одного или всех сетевых интерфейсов виртуальной машины, созданных с помощью масштабируемого набора виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Выполнение планового обслуживания экземпляров виртуальных машин в масштабируемом наборе.|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Завершает работу экземпляра виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Чтение определения метрик виртуальной машины в масштабируемом наборе.|
|/virtualMachineScaleSets/virtualMachines/read|Извлекает свойства виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Повторное развертывание образа виртуальной машины в масштабируемом наборе.|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Повторное создание образа экземпляра виртуальной машины в масштабируемом наборе.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Перезапускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/start/action|Запускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/virtualMachines/write|Обновление свойства виртуальной машины в масштабируемом наборе виртуальных машин.|
|/virtualMachineScaleSets/write|Создание нового масштабируемого набора виртуальных машин или обновление существующего.|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Операция | ОПИСАНИЕ |
|---|---|
|/balances/read|Получение списка сводки использования за период выставления счетов для группы управления.|
|/budgets/read|Получение списка бюджетов подписки или группы управления.|
|/budgets/write|Создание, обновление и удаление бюджетов подписки или группы управления.|
|/marketplaces/read|Вывод сведений об использовании ресурсов Marketplace для области подписок EA и WebDirect.|
|/operations/read|Вывод списка всех поддерживаемых операций для поставщика ресурсов Microsoft.Consumption.|
|/pricesheets/read|Вывод расценок для подписки или группы управления.|
|/reservationDetails/read|Вывод сведений об использовании зарезервированных экземпляров по порядку резервирования или группам управления. Данные относятся к одному экземпляру на уровне дня.|
|/reservationSummaries/read|Вывод сводки использования зарезервированных экземпляров по порядку резервирования или группам управления. Сводка формируется на ежемесячном или ежедневном уровне.|
|/reservationTransactions/read|Вывод журнала транзакций для зарезервированных экземпляров по группам управления.|
|/terms/read|Вывод условий подписки или группы управления.|
|/usageDetails/read|Вывод сведений об использовании для области подписок EA и WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Операция | ОПИСАНИЕ |
|---|---|
|/containerGroups/containers/logs/read|Получение журналов для конкретного контейнера.|
|/containerGroups/delete|Удаление конкретной группы контейнеров.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для группы контейнеров.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для группы контейнеров.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для группы контейнеров.|
|/containerGroups/read|Получение всех групп контейнеров.|
|/containerGroups/write|Создание или обновление конкретной группы контейнеров.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/read|Проверка возможности использования имени реестра контейнеров.|
|/locations/operationResults/read|Получение результата асинхронной операции.|
|/operations/read|Вывод списка всех доступных операций API REST реестра контейнеров Azure.|
|/register/action|Регистрирует подписку для поставщика ресурсов реестра контейнеров и позволяет создавать реестры контейнеров.|
|/registries/delete|Удаление реестра контейнеров.|
|/registries/eventGridFilters/delete|Удаление фильтра сетки событий из реестра контейнеров.|
|/registries/eventGridFilters/read|Получение свойств указанного фильтра сетки событий или списка всех фильтров сетки событий для указанного реестра контейнеров.|
|/registries/eventGridFilters/write|Создание или обновление фильтра сетки событий для реестра контейнеров с указанными параметрами.|
|/registries/listCredentials/action|Выводит список учетных данных входа для указанного реестра контейнеров.|
|/registries/listUsages/read|Получение сведений об использовании квот для указанного реестра контейнеров.|
|/registries/operationStatuses/read|Получение состояния асинхронной операции реестра.|
|/registries/read|Получение свойств указанного реестра контейнеров или вывод списка всех реестров контейнеров в указанной группе ресурсов или подписке.|
|/registries/regenerateCredential/action|Повторное создание учетных данных входа для указанного реестра контейнеров.|
|/registries/replications/delete|Удаление репликации из реестра контейнеров.|
|/registries/replications/operationStatuses/read|Получение состояния асинхронной операции репликации.|
|/registries/replications/read|Получение свойств указанной репликации или списка репликаций для указанного реестра контейнеров.|
|/registries/replications/write|Создание или обновление репликаций для реестра контейнеров с указанными параметрами.|
|/registries/webhooks/delete|Удаление веб-перехватчика из реестра контейнеров.|
|/registries/webhooks/getCallbackConfig/action|Получение конфигурации URI службы и пользовательских заголовков для веб-перехватчика.|
|/registries/webhooks/listEvents/action|Получение списка последних событий для указанного веб-перехватчика.|
|/registries/webhooks/operationStatuses/read|Получение состояния асинхронной операции веб-перехватчика.|
|/registries/webhooks/ping/action|Запуск события проверки связи для отправки веб-перехватчику.|
|/registries/webhooks/read|Получение свойств указанного веб-перехватчика или списка веб-перехватчиков для указанного реестра контейнеров.|
|/registries/webhooks/write|Создание или обновление веб-перехватчика для реестра контейнеров с указанными параметрами.|
|/registries/write|Создание или обновление реестра контейнеров с указанными параметрами.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Операция | ОПИСАНИЕ |
|---|---|
|/containerServices/delete|Удаляет указанную службу контейнеров.|
|/containerServices/read|Возвращает указанную службу контейнеров.|
|/containerServices/write|Размещает или обновляет указанную службу контейнеров.|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Операция | ОПИСАНИЕ |
|---|---|
|/applications/delete|Операция удаления.|
|/applications/listSecrets/action|Выводит список секретов.|
|/applications/listSingleSignOnToken/action|Считывает маркеры единого входа.|
|/applications/read|Операция чтения.|
|/applications/write|Операции записи.|
|/applications/write|Операции записи.|
|/listCommunicationPreference/action|Отображает параметры связи.|
|/operations/read|Считывает операции.|
|/updateCommunicationPreference/action|Обновляет параметры связи.|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Операция | ОПИСАНИЕ |
|---|---|
|/hubs/adobemetadata/action|Создание или обновление метаданных Adobe для Azure Customer Insights.|
|/hubs/adobemetadata/read|Чтение метаданных Adobe для Azure Customer Insights.|
|/hubs/authorizationPolicies/delete|Удаляет политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/read|Считывает политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Повторно создает первичный ключ политики подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Повторно создает вторичный ключ политики подписанного URL-адреса Azure Customer Insights.|
|/hubs/authorizationPolicies/write|Создает или обновляет политику подписанного URL-адреса Azure Customer Insights.|
|/hubs/connectors/activate/action|Активация соединителя Azure Customer Insights.|
|/hubs/connectors/activate/action|Активация соединителя Azure Customer Insights.|
|/hubs/connectors/delete|Удаляет соединитель Azure Customer Insights.|
|/hubs/connectors/getruntimestatus/action|Получение состояния выполнения соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/activate/action|Активация сопоставления соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/delete|Удаляет сопоставление соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/operations/read|Чтение результата операции сопоставления соединителя для Azure Customer Insights.|
|/hubs/connectors/mappings/read|Считывает сопоставление соединителя Azure Customer Insights.|
|/hubs/connectors/mappings/write|Создает или обновляет сопоставление соединителя Azure Customer Insights.|
|/hubs/connectors/operations/read|Чтение результата операции соединителя для Azure Customer Insights.|
|/hubs/connectors/read|Считывает соединитель Azure Customer Insights.|
|/hubs/connectors/saveauthinfo/action|Создание или обновление данных аутентификации соединителя Azure Customer Insights.|
|/hubs/connectors/update/action|Обновление соединителя Azure Customer Insights.|
|/hubs/connectors/write|Создает или обновляет соединитель Azure Customer Insights.|
|/hubs/crmmetadata/action|Создание или обновление метаданных CRM для Azure Customer Insights.|
|/hubs/crmmetadata/read|Чтение метаданных CRM для Azure Customer Insights.|
|/hubs/delete|Удаляет концентратор Azure Customer Insights.|
|/hubs/gdpr/delete|Удаление GDPR Azure Customer Insights.|
|/hubs/gdpr/read|Чтение GDPR Azure Customer Insights.|
|/hubs/gdpr/write|Создание или обновление GDPR Azure Customer Insights.|
|/hubs/getbillingcredits/read|Получение данных о кредитах на концентратор Azure Insights.|
|/hubs/getbillinghistory/read|Получение журнала выставленных счетов за использование концентратора Azure Insights.|
|/hubs/images/delete|Удаление образа Azure Customer Insights.|
|/hubs/images/read|Чтение образа Azure Customer Insights.|
|/hubs/images/write|Создание или обновление образа Azure Customer Insights.|
|/hubs/interactions/delete|Удаление взаимодействий Azure Customer Insights.|
|/hubs/interactions/operations/read|Чтение результата операции взаимодействия для Azure Customer Insights.|
|/hubs/interactions/read|Считывает взаимодействие Azure Customer Insights.|
|/hubs/interactions/suggestrelationshiplinks/action|Предложение ссылок связи для взаимодействий Azure Customer Insights.|
|/hubs/interactions/write|Создает или обновляет взаимодействие Azure Customer Insights.|
|/hubs/kpi/delete|Удаляет ключевой показатель эффективности Azure Customer Insights.|
|/hubs/kpi/operations/read|Чтение результата операции получения ключевых показателей эффективности для Azure Customer Insights.|
|/hubs/kpi/read|Считывает ключевой показатель эффективности Azure Customer Insights.|
|/hubs/kpi/reprocess/action|Повторная обработка ключевых показателей эффективности Azure Customer Insights.|
|/hubs/kpi/write|Создает или обновляет ключевой показатель эффективности Azure Customer Insights.|
|/hubs/links/delete|Удаление ссылок Azure Customer Insights.|
|/hubs/links/operations/read|Чтение результата операции связей соединителя для Azure Customer Insights.|
|/hubs/links/read|Чтение ссылок Azure Customer Insights.|
|/hubs/links/write|Создание или обновление ссылок Azure Customer Insights.|
|/hubs/msemetadata/action|Создание или обновление метаданных MSE для Azure Customer Insights.|
|/hubs/msemetadata/read|Чтение метаданных MSE для Azure Customer Insights.|
|/hubs/operationresults/read|Получение результатов операции концентратора для Azure Customer Insights.|
|/hubs/predictions/delete|Удаление прогнозов Azure Customer Insights.|
|/hubs/predictions/operations/read|Чтение результата операции получения прогнозов для Azure Customer Insights.|
|/hubs/predictions/read|Чтение прогнозов Azure Customer Insights.|
|/hubs/predictions/write|Создание или обновление прогнозов Azure Customer Insights.|
|/hubs/predictivematchpolicies/delete|Удаление политик прогнозного сопоставления Azure Customer Insights.|
|/hubs/predictivematchpolicies/operations/read|Чтение результата операции политик прогнозного сопоставления для Azure Customer Insights.|
|/hubs/predictivematchpolicies/read|Чтение политик прогнозного сопоставления Azure Customer Insights.|
|/hubs/predictivematchpolicies/write|Создание или обновление политик прогнозного сопоставления Azure Customer Insights.|
|/hubs/profiles/delete|Удаление профиля Azure Customer Insights.|
|/hubs/profiles/operations/read|Чтение результата операции профиля для Azure Customer Insights.|
|/hubs/profiles/read|Считывает профиль Azure Customer Insights.|
|/hubs/profiles/write|Записывает профиль Azure Customer Insights.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Возвращает доступные журналы для ресурса.|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Возвращает доступные метрики для ресурса.|
|/hubs/read|Считывает концентратор Azure Customer Insights.|
|/hubs/relationshiplinks/delete|Удаление ссылок связи Azure Customer Insights.|
|/hubs/relationshiplinks/operations/read|Чтение результата операции ссылок связи для Azure Customer Insights.|
|/hubs/relationshiplinks/read|Чтение ссылок связи Azure Customer Insights.|
|/hubs/relationshiplinks/write|Создание или обновление ссылок связи Azure Customer Insights.|
|/hubs/relationships/delete|Удаление связей Azure Customer Insights.|
|/hubs/relationships/operations/read|Чтение результата операции связей для Azure Customer Insights.|
|/hubs/relationships/read|Чтение связей Azure Customer Insights.|
|/hubs/relationships/write|Создание или обновление связей Azure Customer Insights.|
|/hubs/roleAssignments/delete|Удаляет назначение RBAC для Azure Customer Insights.|
|/hubs/roleAssignments/operations/read|Чтение результатов операции назначения RBAC для Azure Customer Insights.|
|/hubs/roleAssignments/read|Считывает назначение RBAC для Azure Customer Insights.|
|/hubs/roleAssignments/write|Создает или обновляет назначение RBAC для Azure Customer Insights.|
|/hubs/roles/read|Чтение ролей RBAC для Azure Customer Insights.|
|/hubs/salesforcemetadata/action|Создание или обновление метаданных SalesForce для Azure Customer Insights.|
|/hubs/salesforcemetadata/read|Чтение метаданных SalesForce для Azure Customer Insights.|
|/hubs/segments/delete|Удаление сегментов Azure Customer Insights.|
|/hubs/segments/dynamic/action|Управление динамическими сегментами Azure Customer Insights.|
|/hubs/segments/read|Чтение сегментов Azure Customer Insights.|
|/hubs/segments/static/action|Управление статическими сегментами Azure Customer Insights.|
|/hubs/segments/write|Создание или обновление сегментов Azure Customer Insights.|
|/hubs/sqlconnectionstrings/delete|Удаление строк подключения SQL для Azure Customer Insights.|
|/hubs/sqlconnectionstrings/read|Чтение строк подключения SQL для Azure Customer Insights.|
|/hubs/sqlconnectionstrings/write|Создание или обновление строк подключения SQL для Azure Customer Insights.|
|/hubs/suggesttypeschema/action|Создание предложения схемы типа из примера данных.|
|/hubs/tenantmanagement/read|Управление параметрами концентратора Azure Customer Insights.|
|/hubs/views/delete|Удаляет представление приложений Azure Customer Insights.|
|/hubs/views/read|Считывает представление приложений Azure Customer Insights.|
|/hubs/views/write|Создает или обновляет представление приложений Azure Customer Insights.|
|/hubs/widgettypes/read|Чтение типов мини-приложений приложения Azure Customer Insights.|
|/hubs/write|Создает или обновляет концентратор Azure Customer Insights.|
|/operations/read|Чтение метаданных API для Azure Customer Insights.|
|/register/action|Регистрация подписки для поставщика ресурсов Customer Insights и разрешение создания ресурсов Customer Insights.|
|/unregister/action|Отмена регистрации подписки для поставщика ресурсов Customer Insights.|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Операция | ОПИСАНИЕ |
|---|---|
|/catalogs/delete|Удаляет каталог.|
|/catalogs/read|Возвращает свойства каталога или каталогов в подписке или группе ресурсов.|
|/catalogs/write|Создает каталог или обновляет теги и свойства каталога.|
|/checkNameAvailability/action|Проверяет доступность имени каталога для клиента.|
|/operations/read|Получение списка операций, доступных в поставщике ресурсов Microsoft.DataCatalog.|
|/register/action|Регистрация подписки в поставщике ресурсов Microsoft.DataCatalog.|
|/unregister/action|Отмена регистрации подписки в поставщике ресурсов Microsoft.DataCatalog.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Операция | ОПИСАНИЕ |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для фабрик данных.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для фабрик данных.|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для фабрик данных.|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/computePolicies/delete|Удаление политики вычислений.|
|/accounts/computePolicies/read|Получение сведений о политике вычислений.|
|/accounts/computePolicies/write|Создание или обновление политики вычислений.|
|/accounts/dataLakeStoreAccounts/delete|Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/read|Получение данных о связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics.|
|/accounts/dataLakeStoreAccounts/write|Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics.|
|/accounts/delete|Удаление учетной записи Data Lake Analytics.|
|/accounts/firewallRules/delete|Удаляет правило брандмауэра.|
|/accounts/firewallRules/read|Возвращает сведения о правиле брандмауэра.|
|/accounts/firewallRules/write|Создает или обновляет правило брандмауэра.|
|/accounts/operationResults/read|Получение результата операции учетной записи Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики для учетной записи Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики для учетной записи Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для учетной записи Data Lake Analytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для учетной записи Data Lake Analytics.|
|/accounts/read|Получение сведений о существующей учетной записи Data Lake Analytics.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Получение списка токенов SAS для контейнеров хранилища учетной записи хранения, связанной с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/Containers/read|Получение контейнеров учетной записи хранения, связанной с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/delete|Удаление связи учетной записи хранения с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/read|Получение сведений об учетной записи хранения, связанной с учетной записью Data Lake Analytics.|
|/accounts/storageAccounts/write|Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics.|
|/accounts/TakeOwnership/action|Предоставление разрешений на отмену заданий, отправленных другими пользователями.|
|/accounts/write|Создание или обновление учетной записи Data Lake Analytics.|
|/locations/capability/read|Получение сведений о возможностях подписки в отношении использования Data Lake Analytics.|
|/locations/checkNameAvailability/action|Проверка доступности имени учетной записи Data Lake Analytics.|
|/locations/operationResults/read|Получение результата операции учетной записи Data Lake Analytics.|
|/operations/read|Получение доступных операций Data Lake Analytics.|
|/register/action|Регистрация подписки для Data Lake Analytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/delete|Удаление учетной записи Data Lake Store.|
|/accounts/enableKeyVault/action|Включение хранилища ключей для учетной записи Data Lake Store.|
|/accounts/firewallRules/delete|Удаляет правило брандмауэра.|
|/accounts/firewallRules/read|Возвращает сведения о правиле брандмауэра.|
|/accounts/firewallRules/write|Создает или обновляет правило брандмауэра.|
|/accounts/operationResults/read|Получение результата операции учетной записи Data Lake Store.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики для учетной записи Data Lake Store.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики для учетной записи Data Lake Store.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для учетной записи Data Lake Store.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для учетной записи Data Lake Store.|
|/accounts/read|Получение сведений о существующей учетной записи Data Lake Store.|
|/accounts/Superuser/action|Предоставление прав суперпользователя в хранилище Data Lake Store при предоставлении разрешения Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Удаляет доверенный поставщик удостоверений.|
|/accounts/trustedIdProviders/read|Возвращает сведения о доверенном поставщике удостоверений.|
|/accounts/trustedIdProviders/write|Создает или обновляет доверенный поставщик удостоверений.|
|/accounts/write|Создание или обновление учетной записи Data Lake Store.|
|/locations/capability/read|Получение сведений о возможностях подписки в отношении использования Data Lake Store.|
|/locations/checkNameAvailability/action|Проверка доступности имени учетной записи Data Lake Store.|
|/locations/operationResults/read|Получение результата операции учетной записи Data Lake Store.|
|/operations/read|Получение доступных операций Data Lake Store.|
|/register/action|Регистрация подписки для Data Lake Store.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Операция | ОПИСАНИЕ |
|---|---|
|/locations/performanceTiers/read|Получение списка доступных уровней производительности.|
|/performanceTiers/read|Получение списка доступных уровней производительности.|
|/servers/delete|Удаление существующего сервера.|
|/servers/firewallRules/delete|Удаление существующего правила брандмауэра.|
|/servers/firewallRules/read|Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра.|
|/servers/firewallRules/write|Создание правила брандмауэра с указанными параметрами или обновление существующего правила.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Возвращает типы метрик, доступных для баз данных.|
|/servers/read|Возвращение списка серверов или получение свойств для указанного сервера.|
|/servers/recoverableServers/read|Возвращение восстанавливаемых данных MySQL Server.|
|/servers/virtualNetworkRules/delete|Удаление существующего правила виртуальной сети.|
|/servers/virtualNetworkRules/read|Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети.|
|/servers/virtualNetworkRules/write|Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети.|
|/servers/write|Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Операция | ОПИСАНИЕ |
|---|---|
|/locations/performanceTiers/read|Получение списка доступных уровней производительности.|
|/performanceTiers/read|Получение списка доступных уровней производительности.|
|/servers/delete|Удаление существующего сервера.|
|/servers/firewallRules/delete|Удаление существующего правила брандмауэра.|
|/servers/firewallRules/read|Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра.|
|/servers/firewallRules/write|Создание правила брандмауэра с указанными параметрами или обновление существующего правила.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Возвращение типов журналов, доступных для баз данных.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Возвращает типы метрик, доступных для баз данных.|
|/servers/read|Возвращение списка серверов или получение свойств для указанного сервера.|
|/servers/recoverableServers/read|Возвращение восстанавливаемых данных PostgreSQL Server.|
|/servers/virtualNetworkRules/delete|Удаление существующего правила виртуальной сети.|
|/servers/virtualNetworkRules/read|Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети.|
|/servers/virtualNetworkRules/write|Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети.|
|/servers/write|Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/Action|Проверяет, доступно ли имя Центра Интернета вещей.|
|/checkProvisioningServiceNameAvailability/Action|Проверяет, доступно ли имя Центра Интернета вещей.|
|/ElasticPools/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/ElasticPools/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/elasticPools/iotHubTenants/Delete|Удаление ресурса клиента Центра Интернета вещей.|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Удаляет группу потребителей концентратора событий.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Возвращает группы потребителей концентратора событий.|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Создает группу потребителей концентратора событий.|
|/elasticPools/iotHubTenants/exportDevices/Action|Экспортирует устройства.|
|/elasticPools/iotHubTenants/getStats/Read|Получение ресурса статистики клиента Центра Интернета вещей.|
|/elasticPools/iotHubTenants/importDevices/Action|Импортирует устройства.|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Получение ключа клиента Центра Интернета вещей.|
|/elasticPools/iotHubTenants/jobs/Read|Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей.|
|/elasticPools/iotHubTenants/listKeys/Action|Получение ключей клиента Центра Интернета вещей.|
|/ElasticPools/IotHubTenants/logDefinitions/read|Получение доступных определений журналов для службы Центра Интернета вещей.|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Возвращает доступные метрики для службы Центра Интернета вещей.|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Возвращает метрики квоты.|
|/elasticPools/iotHubTenants/Read|Получение ресурса клиента Центра Интернета вещей.|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Тестирует сообщение с помощью всех существующих маршрутов.|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Тестирует сообщение с помощью предоставленного тестового маршрута.|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей.|
|/elasticPools/iotHubTenants/Write|Создание или обновление ресурса клиента Центра Интернета вещей.|
|/ElasticPools/metricDefinitions/read|Возвращает доступные метрики для службы Центра Интернета вещей.|
|/iotHubs/certificates/generateVerificationCode/Action|Создание кода проверки.|
|/iotHubs/certificates/verify/Action|Проверка ресурса сертификата.|
|/iotHubs/Delete|Удаляет ресурс Центра Интернета вещей.|
|/IotHubs/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/IotHubs/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/iotHubs/eventGridFilters/Delete|Удаление фильтра сетки событий.|
|/iotHubs/eventGridFilters/Read|Получение фильтра сетки событий.|
|/iotHubs/eventGridFilters/Write|Создание фильтра сетки событий или обновление существующего.|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Удаляет группу потребителей концентратора событий.|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Возвращает группы потребителей концентратора событий.|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Создает группу потребителей концентратора событий.|
|/iotHubs/exportDevices/Action|Экспортирует устройства.|
|/iotHubs/importDevices/Action|Импортирует устройства.|
|/iotHubs/iotHubKeys/listkeys/Action|Возвращает ключ Центра Интернета вещей для указанного имени.|
|/iotHubs/iotHubStats/Read|Возвращает статистику Центра Интернета вещей.|
|/iotHubs/jobs/Read|Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей.|
|/iotHubs/listkeys/Action|Возвращает все ключи Центра Интернета вещей.|
|/IotHubs/logDefinitions/read|Получение доступных определений журналов для службы Центра Интернета вещей.|
|/IotHubs/metricDefinitions/read|Возвращает доступные метрики для службы Центра Интернета вещей.|
|/iotHubs/quotaMetrics/Read|Возвращает метрики квоты.|
|/iotHubs/Read|Возвращает ресурсы Центра Интернета вещей.|
|/iotHubs/routing/$testall/Action|Тестирует сообщение с помощью всех существующих маршрутов.|
|/iotHubs/routing/$testnew/Action|Тестирует сообщение с помощью предоставленного тестового маршрута.|
|/iotHubs/routingEndpointsHealth/Read|Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей.|
|/iotHubs/skus/Read|Возвращает допустимые номера SKU Центра Интернета вещей.|
|/iotHubs/Write|Создает или обновляет ресурс Центра Интернета вещей.|
|/operations/Read|Возвращает все операции поставщика ресурсов.|
|/provisioningServices/certificates/generateVerificationCode/Action|Создание кода проверки.|
|/provisioningServices/certificates/verify/Action|Проверка ресурса сертификата.|
|/provisioningServices/Delete|Удаление ресурса службы политики диагностики (DPS) Центра Интернета вещей.|
|/provisioningServices/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/provisioningServices/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/provisioningServices/listkeys/Action|Получение ключей DPS Центра Интернета вещей.|
|/provisioningServices/logDefinitions/read|Получение доступных определений журналов для службы подготовки.|
|/provisioningServices/metricDefinitions/read|Получение доступных метрик для службы подготовки.|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Получение ключей DPS Центра Интернета вещей для имени ключа.|
|/provisioningServices/Read|Получение ресурса DPS Центра Интернета вещей.|
|/provisioningServices/skus/Read|Получение действительных номеров SKU для DPS Центра Интернета вещей.|
|/provisioningServices/Write|Создание ресурса DPS для Центра Интернета вещей.|
|/register/action|Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей.|
|/register/action|Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей.|
|/usages/Read|Возвращает данные об использовании подписки для данного поставщика.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Операция | ОПИСАНИЕ |
|---|---|
|/labCenters/delete|Удаление центров лабораторий.|
|/labCenters/read|Чтение центров лабораторий.|
|/labCenters/write|Добавление или изменение центров лабораторий.|
|/labs/artifactSources/armTemplates/read|Считывает шаблоны Azure Resource Manager.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Создает шаблон ARM для заданного артефакта, передает необходимые файлы в учетную запись хранения и проверяет созданный артефакт.|
|/labs/artifactSources/artifacts/read|Считывает артефакты.|
|/labs/artifactSources/delete|Удаляет источники артефактов.|
|/labs/artifactSources/read|Считывает источники артефактов.|
|/labs/artifactSources/write|Добавляет или изменяет источники артефактов.|
|/labs/ClaimAnyVm/action|Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории.|
|/labs/costs/read|Считывает затраты.|
|/labs/costs/write|Добавляет или изменяет затраты.|
|/labs/CreateEnvironment/action|Создает виртуальные машины в лаборатории.|
|/labs/customImages/delete|Удаляет пользовательские образы.|
|/labs/customImages/read|Считывает пользовательские образы.|
|/labs/customImages/write|Добавляет или изменяет пользовательские образы.|
|/labs/delete|Удаляет лаборатории.|
|/labs/ExportResourceUsage/action|Экспортирует данные об использовании ресурсов лаборатории в учетную запись хранения.|
|/labs/formulas/delete|Удаляет формулы.|
|/labs/formulas/read|Считывает формулы.|
|/labs/formulas/write|Добавляет или изменяет формулы.|
|/labs/galleryImages/read|Считывает образы из коллекции.|
|/labs/GenerateUploadUri/action|Создает универсальный код ресурса (URI) для передачи настраиваемых образов дисков в лабораторию.|
|/labs/ImportVirtualMachine/action|Импорт виртуальной машины в другую лабораторию.|
|/labs/ListVhds/action|Выводит список образов дисков, доступных для создания пользовательского образа.|
|/labs/notificationChannels/delete|Удаляет каналы уведомлений.|
|/labs/notificationChannels/Notify/action|Отправляет уведомление в указанный канал.|
|/labs/notificationChannels/read|Считывает каналы уведомлений.|
|/labs/notificationChannels/write|Добавляет или изменяет каналы уведомлений.|
|/labs/policySets/EvaluatePolicies/action|Оценивает политику лаборатории.|
|/labs/policySets/policies/delete|Удаляет политики.|
|/labs/policySets/policies/read|Считывает политики.|
|/labs/policySets/policies/write|Добавляет или изменяет политики.|
|/labs/read|Считывает лаборатории.|
|/labs/schedules/delete|Удаляет расписания.|
|/labs/schedules/Execute/action|Выполняет расписание.|
|/labs/schedules/ListApplicable/action|Выводит список всех применимых расписаний.|
|/labs/schedules/read|Считывает расписания.|
|/labs/schedules/write|Добавляет или изменяет расписания.|
|/labs/serviceRunners/delete|Удаляет средства выполнения служб.|
|/labs/serviceRunners/read|Считывает средства выполнения служб.|
|/labs/serviceRunners/write|Добавляет или изменяет средства выполнения служб.|
|/labs/users/delete|Удаляет профили пользователей.|
|/labs/users/disks/Attach/action|Подключает диск к виртуальной машине и создает аренду для него.|
|/labs/users/disks/delete|Удаляет диски.|
|/labs/users/disks/Detach/action|Отключает диск от виртуальной машины и прерывает его аренду.|
|/labs/users/disks/read|Считывает диски.|
|/labs/users/disks/write|Добавляет или изменяет диски.|
|/labs/users/environments/delete|Удаляет среды.|
|/labs/users/environments/read|Считывает среды.|
|/labs/users/environments/write|Добавляет или изменяет среды.|
|/labs/users/read|Считывает профили пользователей.|
|/labs/users/secrets/delete|Удаляет секреты.|
|/labs/users/secrets/read|Считывает секреты.|
|/labs/users/secrets/write|Добавляет или изменяет секреты.|
|/labs/users/serviceFabrics/delete|Удаление платформ Service Fabric.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Выводит список всех применимых расписаний.|
|/labs/users/serviceFabrics/read|Чтение платформ Service Fabric.|
|/labs/users/serviceFabrics/schedules/delete|Удаляет расписания.|
|/labs/users/serviceFabrics/schedules/Execute/action|Выполняет расписание.|
|/labs/users/serviceFabrics/schedules/read|Считывает расписания.|
|/labs/users/serviceFabrics/schedules/write|Добавляет или изменяет расписания.|
|/labs/users/serviceFabrics/Start/action|Запуск платформы Service Fabric.|
|/labs/users/serviceFabrics/Stop/action|Остановка платформы Service Fabric.|
|/labs/users/serviceFabrics/write|Добавление или изменение платформ Service Fabric.|
|/labs/users/write|Добавляет или изменяет профили пользователей.|
|/labs/virtualMachines/AddDataDisk/action|Подключает новый или существующий диск данных к виртуальной машине.|
|/labs/virtualMachines/ApplyArtifacts/action|Применяет артефакты к виртуальной машине.|
|/labs/virtualMachines/Claim/action|Присваивает владение существующей виртуальной машиной.|
|/labs/virtualMachines/delete|Удаляет виртуальные машины.|
|/labs/virtualMachines/DetachDataDisk/action|Отключает указанный диск от виртуальной машины.|
|/labs/virtualMachines/ListApplicableSchedules/action|Выводит список всех применимых расписаний.|
|/labs/virtualMachines/read|Считывает виртуальные машины.|
|/labs/virtualMachines/Restart/action|Перезапуск виртуальной машины.|
|/labs/virtualMachines/schedules/delete|Удаляет расписания.|
|/labs/virtualMachines/schedules/Execute/action|Выполняет расписание.|
|/labs/virtualMachines/schedules/read|Считывает расписания.|
|/labs/virtualMachines/schedules/write|Добавляет или изменяет расписания.|
|/labs/virtualMachines/Start/action|Запускает виртуальную машину.|
|/labs/virtualMachines/Stop/action|Остановка виртуальной машины|
|/labs/virtualMachines/TransferDisks/action|Передача прав владения дисками данных виртуальной машины себе.|
|/labs/virtualMachines/UnClaim/action|Отказ от владения существующей виртуальной машиной.|
|/labs/virtualMachines/write|Добавляет или изменяет виртуальные машины.|
|/labs/virtualNetworks/delete|Удаляет виртуальные сети.|
|/labs/virtualNetworks/read|Считывает виртуальные сети.|
|/labs/virtualNetworks/write|Добавляет или изменяет виртуальные сети.|
|/labs/write|Добавляет или изменяет лаборатории.|
|/locations/operations/read|Считывает операции.|
|/register/action|Регистрирует подписку.|
|/schedules/delete|Удаляет расписания.|
|/schedules/Execute/action|Выполняет расписание.|
|/schedules/read|Считывает расписания.|
|/schedules/Retarget/action|Обновляет идентификатор целевого ресурса расписания.|
|/schedules/write|Добавляет или изменяет расписания.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Операция | ОПИСАНИЕ |
|---|---|
|/databaseAccountNames/read|Проверяет доступность имени.|
|/databaseAccounts/changeResourceGroup/action|Изменяет группу ресурсов для учетной записи базы данных.|
|/databaseAccounts/databases/collections/metricDefinitions/read|Считывает определения метрик коллекции.|
|/databaseAccounts/databases/collections/metrics/read|Считывает метрики коллекции.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Чтение метрик уровня ключа раздела для учетной записи базы данных.|
|/databaseAccounts/databases/collections/partitions/metrics/read|Чтение метрик уровня раздела для учетной записи базы данных.|
|/databaseAccounts/databases/collections/partitions/usages/read|Чтение сведений об использовании уровня раздела для учетной записи базы данных.|
|/databaseAccounts/databases/collections/usages/read|Считывает данные об использовании коллекции.|
|/databaseAccounts/databases/metricDefinitions/read|Считывает определения метрик базы данных.|
|/databaseAccounts/databases/metrics/read|Считывает метрики базы данных.|
|/databaseAccounts/databases/usages/read|Считывает данные об использовании базы данных.|
|/databaseAccounts/delete|Удаляет учетные записи базы данных.|
|/databaseAccounts/failoverPriorityChange/action|Изменяет приоритеты отработки отказа для регионов учетной записи базы данных. Используется для выполнения операции отработки отказа вручную.|
|/databaseAccounts/listConnectionStrings/action|Возвращает строки подключения для учетной записи базы данных.|
|/databaseAccounts/listKeys/action|Выводит список ключей учетной записи базы данных.|
|/databaseAccounts/metricDefinitions/read|Считывает определения метрик учетной записи базы данных.|
|/databaseAccounts/metrics/read|Считывает метрики учетной записи базы данных.|
|/databaseAccounts/operationResults/read|Чтение состояния асинхронной операции.|
|/databaseAccounts/percentile/metrics/read|Чтение метрик задержки.|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Чтение метрик задержки для определенного источника и целевого региона.|
|/databaseAccounts/percentile/targetRegion/metrics/read|Чтение метрик задержки для определенного целевого региона.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Получение категорий доступных журналов для учетной записи базы данных.|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик учетной записи базы данных.|
|/databaseAccounts/read|Считывает учетную запись базы данных.|
|/databaseAccounts/readonlykeys/action|Считывает ключи только для чтения учетной записи базы данных.|
|/databaseAccounts/readonlykeys/read|Считывает ключи только для чтения учетной записи базы данных.|
|/databaseAccounts/regenerateKey/action|Выполняет ротацию ключей учетной записи базы данных.|
|/databaseAccounts/region/databases/collections/metrics/read|Чтение метрик региональной коллекции.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Чтение метрик уровня ключа раздела для учетной записи региональной базы данных.|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Чтение метрик уровня раздела для учетной записи региональной базы данных.|
|/databaseAccounts/region/databases/collections/partitions/read|Чтение разделов учетной записи базы данных в коллекции.|
|/databaseAccounts/region/metrics/read|Чтение метрик учетной записи базы данных и региона.|
|/databaseAccounts/usages/read|Считывает данные об использовании учетной записи базы данных.|
|/databaseAccounts/write|Обновляет учетные записи базы данных.|
|/locations/deleteVirtualNetworkOrSubnets/action|Уведомление Microsoft.DocumentDB о том, что идет удаление виртуальной сети или подсети.|
|/operationResults/read|Чтение состояния асинхронной операции.|
|/operations/read|Чтение операций, доступных для Microsoft DocumentDB. |
|/register/action| Регистрация поставщика ресурсов Microsoft DocumentDB для подписки.|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Операция | ОПИСАНИЕ |
|---|---|
|/checkDomainAvailability/Action|Проверяет, доступен ли домен для приобретения.|
|/domains/Delete|Удаляет существующий домен.|
|/domains/domainownershipidentifiers/Delete|Удаление идентификатора владения.|
|/domains/domainownershipidentifiers/Read|Вывод списка идентификаторов владения.|
|/domains/domainownershipidentifiers/Read|Получение идентификатора владения.|
|/domains/domainownershipidentifiers/Write|Создание или обновление идентификатора.|
|/domains/operationresults/Read|Возвращает операцию домена.|
|/domains/operations/Read|Вывод списка всех операций из регистрации домена службы приложений.|
|/domains/Read|Возвращает список доменов.|
|/domains/Read|Получение домена.|
|/domains/renew/Action|Обновление существующего домена.|
|/domains/Write|Добавляет новый домен или обновляет существующий.|
|/generateSsoRequest/Action|Создает запрос на вход в центр управления доменами.|
|/listDomainRecommendations/Action|Извлекает список рекомендуемых доменов на основе ключевых слов.|
|/register/action|Регистрирует поставщик ресурсов доменов Майкрософт для подписки.|
|/topLevelDomains/listAgreements/Action|Вывод списка соглашений.|
|/topLevelDomains/Read|Получение доменов верхнего уровня.|
|/topLevelDomains/Read|Получение домена верхнего уровня.|
|/validateDomainRegistrationInformation/Action|Проверяет объект покупки домена без его отправки.|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Операция | ОПИСАНИЕ |
|---|---|
|/lcsprojects/clouddeployments/read|Отображает развертывания Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю.|
|/lcsprojects/clouddeployments/write|Создает развертывание Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю. Развертываниями можно управлять на портале управления Azure.|
|/lcsprojects/connectors/read|Считывает соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services.|
|/lcsprojects/connectors/write|Создает или обновляет соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services.|
|/lcsprojects/delete|Удаляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю.|
|/lcsprojects/read|Отображает проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю.|
|/lcsprojects/write|Создает или обновляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю. Можно обновить только свойства имени и описания. Невозможно обновить подписку и расположение, связанные с проектом, после его создания.|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Операция | ОПИСАНИЕ |
|---|---|
|/eventSubscriptions/delete|Удаление подписки на события.|
|/eventSubscriptions/getFullUrl/action|Получение полного URL-адреса подписки на события.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для подписок на события.|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Получение или обновление параметра диагностики для подписок на события.|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для подписок на события.|
|/eventSubscriptions/read|Чтение подписки на события.|
|/eventSubscriptions/write|Создание или обновление подписки на события.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для разделов.|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для разделов.|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для разделов.|
|/register/action|Регистрация подписки на события для поставщика ресурсов сетки событий и разрешение создавать подписки сетки событий.|
|/topics/delete|Удаление раздела|
|/topics/listKeys/action|Вывод списка ключей для раздела.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для разделов.|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для разделов.|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для разделов.|
|/topics/read|Чтение раздела.|
|/topics/regenerateKey/action|Повторное создание ключа для раздела.|
|/topics/write|Создание или обновление раздела.|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/checkNamespaceAvailability/action|Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability.|
|/namespaces/authorizationRules/action|Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/namespaces/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Получение ключей правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Получение правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Проверка доступности псевдонима пространства имен в заданной подписке.|
|/namespaces/disasterRecoveryConfigs/delete|Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен.|
|/namespaces/disasterRecoveryConfigs/failover/action|Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство.|
|/namespaces/disasterRecoveryConfigs/read|Получение конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/disasterRecoveryConfigs/write|Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/eventhubs/authorizationRules/action|Операция обновления концентратора событий. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT.|
|/namespaces/eventhubs/authorizationRules/delete|Операция удаления правил авторизации концентратора событий.|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Возвращает строку подключения к концентратору событий.|
|/namespaces/eventhubs/authorizationRules/read| Возвращает список правил авторизации концентратора событий.|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/eventhubs/authorizationRules/write|Создает правила авторизации концентратора событий и обновляет его свойства. Можно обновить права доступа к правилам авторизации.|
|/namespaces/eventHubs/consumergroups/Delete|Операция удаления ресурса группы потребителей.|
|/namespaces/eventHubs/consumergroups/read|Возвращает список описаний ресурсов группы потребителей.|
|/namespaces/eventHubs/consumergroups/write|Создает или обновляет свойства группы потребителей.|
|/namespaces/eventhubs/Delete|Операция удаления ресурса концентратора событий.|
|/namespaces/eventhubs/read|Возвращает список описаний ресурсов концентратора событий.|
|/namespaces/eventhubs/write|Создает или обновляет свойства концентратора событий.|
|/namespaces/messagingPlan/read|Получение плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/messagingPlan/write|Обновление плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/operationresults/read|Получение состояния операции пространства имен.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Возвращает список описаний ресурсов журналов пространства имен.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен.|
|/operations/read|Получение операций.|
|/register/action|Регистрирует подписку для поставщика ресурсов концентратора событий и позволяет создавать ресурсы концентратора событий.|
|/sku/read|Получение списка описаний ресурсов номеров SKU.|
|/sku/regions/read|Получение списка описаний ресурсов регионов для номеров SKU.|
|/unregister/action|Регистрация поставщика ресурсов концентратора событий.|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Операция | ОПИСАНИЕ |
|---|---|
|/features/read|Возвращает функции подписки.|
|/providers/features/read|Возвращает функцию подписки в заданном поставщике ресурсов.|
|/providers/features/register/action|Регистрирует функцию для подписки в заданном поставщике ресурсов.|
|/providers/features/unregister/action|Отмена регистрации функции для подписки в заданном поставщике ресурсов.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Операция | ОПИСАНИЕ |
|---|---|
|/clusters/changerdpsetting/action|Изменяет параметры RDP для кластера HDInsight.|
|/clusters/configurations/action|Обновляет конфигурацию кластера HDInsight.|
|/clusters/configurations/read|Возвращает конфигурации кластеров HDInsight.|
|/clusters/delete|Удаляет кластер HDInsight.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса кластера HDInsight.|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса кластера HDInsight.|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для кластера HDInsight.|
|/clusters/read|Возвращает сведения о кластере HDInsight.|
|/clusters/roles/resize/action|Изменяет размер кластера HDInsight.|
|/clusters/write|Создает или обновляет кластер HDInsight.|
|/locations/capabilities/read|Возвращает возможности подписки.|
|/locations/checkNameAvailability/read|Проверяет доступность имени.|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Операция | ОПИСАНИЕ |
|---|---|
|/jobs/delete|Удаляет существующее задание.|
|/jobs/listBitLockerKeys/action|Возвращает ключи BitLocker для указанного задания.|
|/jobs/read|Возвращает свойства для указанного задания или возвращает список заданий.|
|/jobs/write|Создает задание с указанными параметрами либо обновляет свойства или теги указанного задания.|
|/locations/read|Возвращает свойства для указанного расположения или возвращает список расположений.|
|/register/action|Регистрирует подписку для поставщика ресурсов импорта и экспорта и позволяет создавать задания импорта и экспорта.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Операция | ОПИСАНИЕ |
|---|---|
|/ActionGroups/Delete|Удаление группы действий.|
|/ActionGroups/Read|Чтение группы действий.|
|/ActionGroups/Write|Запись группы действий.|
|/ActivityLogAlerts/Activated/Action|Активирует оповещение журнала действий.|
|/ActivityLogAlerts/Delete|Удаление оповещения журнала действий.|
|/ActivityLogAlerts/Read|Чтение оповещения журнала действий.|
|/ActivityLogAlerts/Write|Чтение оповещения журнала действий.|
|/AlertRules/Activated/Action|Правило генерации оповещений активировано.|
|/AlertRules/Delete|Удаляет конфигурацию правила генерации оповещений.|
|/AlertRules/Incidents/Read|Считывает конфигурацию инцидентов правила генерации оповещений.|
|/AlertRules/Read|Считывает конфигурацию правила генерации оповещений.|
|/AlertRules/Resolved/Action|Правило генерации оповещений разрешено.|
|/AlertRules/Throttled/Action|Правило генерации оповещений отрегулировано.|
|/AlertRules/Write|Выполняет запись в конфигурацию правила генерации оповещений.|
|/AutoscaleSettings/Delete|Удаляет конфигурацию параметров автомасштабирования.|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Считывает определения метрик.|
|/AutoscaleSettings/Read|Считывает конфигурацию параметров автомасштабирования.|
|/AutoscaleSettings/Scaledown/Action|Операция автоматического уменьшения масштаба.|
|/AutoscaleSettings/Scaleup/Action|Операция автоматического увеличения масштаба.|
|/AutoscaleSettings/Write|Выполняет запись в конфигурацию параметров автомасштабирования.|
|/Components/AnalyticsItems/Delete|Удаление элемента аналитики Application Insights.|
|/Components/AnalyticsItems/Read|Чтение элемента аналитики Application Insights.|
|/Components/AnalyticsItems/Write|Запись элемента аналитики Application Insights.|
|/Components/AnalyticsTables/Action|Действие таблицы аналитики Application Insights.|
|/Components/AnalyticsTables/Delete|Удаление схемы таблицы аналитики Application Insights.|
|/Components/AnalyticsTables/Read|Чтение схемы таблицы аналитики Application Insights.|
|/Components/AnalyticsTables/Write|Запись схемы таблицы аналитики Application Insights.|
|/Components/Annotations/Delete|Удаление заметки Application Insights.|
|/Components/Annotations/Read|Чтение заметки Application Insights.|
|/Components/Annotations/Write|Запись заметки Application Insights.|
|/Components/Api/Read|Чтение API данных компонента Application Insights.|
|/Components/ApiKeys/Action|Создание ключа API Application Insights.|
|/Components/ApiKeys/Delete|Удаление ключа API Application Insights.|
|/Components/ApiKeys/Read|Чтение ключа API Application Insights.|
|/Components/BillingPlanForComponent/Read|Чтение плана выставления счетов для компонента Application Insights.|
|/Components/CurrentBillingFeatures/Read|Чтение текущих функций выставления счетов для компонента Application Insights.|
|/Components/CurrentBillingFeatures/Write|Запись текущих функций выставления счетов для компонента Application Insights.|
|/Components/DefaultWorkItemConfig/Read|Чтение конфигурации интеграции управления жизненным циклом приложений по умолчанию для Application Insights.|
|/Components/Delete|Удаление конфигурации компонента Application Insights.|
|/Components/ExportConfiguration/Action|Действие параметров экспорта Application Insights.|
|/Components/ExportConfiguration/Delete|Удаление параметров экспорта Application Insights.|
|/Components/ExportConfiguration/Read|Чтение параметров экспорта Application Insights.|
|/Components/ExportConfiguration/Write|Запись параметров экспорта Application Insights.|
|/Components/ExtendQueries/Read|Чтение результатов расширенного запроса компонента Application Insights.|
|/Components/Favorites/Delete|Удаление избранного объекта Application Insights.|
|/Components/Favorites/Read|Чтение избранного объекта Application Insights.|
|/Components/Favorites/Write|Запись избранного объекта Application Insights.|
|/Components/FeatureCapabilities/Read|Чтение возможностей функций компонента Application Insights.|
|/Components/GetAvailableBillingFeatures/Read|Чтение доступных функций выставления счетов для компонента Application Insights.|
|/Components/GetToken/Read|Чтение токена компонента Application Insights.|
|/Components/ListMigrationDate/Action|Возврат даты перевода подписки.|
|/Components/ListMigrationDate/Read|Возврат даты перевода подписки.|
|/Components/MetricDefinitions/Read|Чтение определений метрик компонента Application Insights.|
|/Components/Metrics/Read|Чтение метрик компонента Application Insights.|
|/Components/MigrateToNewpricingModel/Action|Перевод подписки на новую модель ценообразования.|
|/Components/Move/Action|Перемещение компонента Application Insights в другую группу ресурсов или подписку.|
|/Components/MyAnalyticsItems/Delete|Удаление персонального элемента аналитики Application Insights.|
|/Components/MyAnalyticsItems/Read|Чтение персонального элемента аналитики Application Insights.|
|/Components/MyAnalyticsItems/Write|Запись персонального элемента аналитики Application Insights.|
|/Components/MyFavorites/Read|Чтение персонального избранного объекта Application Insights.|
|/Components/PricingPlans/Read|Чтение плана ценообразования компонента Application Insights.|
|/Components/PricingPlans/Write|Запись плана ценообразования компонента Application Insights.|
|/Components/ProactiveDetectionConfigs/Read|Чтение конфигурации упреждающего обнаружения Application Insights.|
|/Components/ProactiveDetectionConfigs/Write|Запись конфигурации упреждающего обнаружения Application Insights.|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Считывает определения метрик.|
|/Components/QuotaStatus/Read|Чтение состояния квоты компонента Application Insights.|
|/Components/Read|Чтение конфигурации компонента Application Insights.|
|/Components/RollbackToLegacyPricingModel/Action|Откат подписки к прежней версии модели ценообразования.|
|/Components/SyntheticMonitorLocations/Read|Чтение расположений веб-тестов Application Insights.|
|/Components/WorkItemConfigs/Delete|Удаление конфигурации интеграции управления жизненным циклом приложений для Application Insights.|
|/Components/WorkItemConfigs/Read|Чтение конфигурации интеграции управления жизненным циклом приложений для Application Insights.|
|/Components/WorkItemConfigs/Write|Запись конфигурации интеграции управления жизненным циклом приложений для Application Insights.|
|/Components/Write|Запись конфигурации компонента Application Insights.|
|/DiagnosticSettings/Delete|Удаляет конфигурацию параметров диагностики.|
|/DiagnosticSettings/Read|Считывает конфигурацию параметров диагностики.|
|/DiagnosticSettings/Write|Выполняет запись в конфигурацию параметров диагностики.|
|/EventCategories/Read|Чтение категории событий.|
|/eventtypes/digestevents/Read|Считывает дайджест типов событий управления.|
|/eventtypes/values/Read|Считывает значения типов событий управления.|
|/ExtendedDiagnosticSettings/Delete|Удаляет конфигурацию дополнительных параметров диагностики.|
|/ExtendedDiagnosticSettings/Read|Считывает конфигурацию дополнительных параметров диагностики.|
|/ExtendedDiagnosticSettings/Write|Выполняет запись в конфигурацию дополнительных параметров диагностики.|
|/LogDefinitions/Read|Считывает определения журналов.|
|/LogProfiles/Delete|Удаляет конфигурацию профилей журнала.|
|/LogProfiles/Read|Считывает профили журналов.|
|/LogProfiles/Write|Выполняет запись в конфигурацию профиля журнала.|
|/MetricAlerts/Delete|Удаление оповещения метрики.|
|/MetricAlerts/Read|Чтение оповещения метрики.|
|/MetricAlerts/Write|Запись оповещения метрики.|
|/MetricDefinitions/Microsoft.Insights/Read|Считывает определения метрик.|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Считывает определения метрик.|
|/MetricDefinitions/Read|Считывает определения метрик.|
|/Metrics/providers/Metrics/Read|Считывает метрики.|
|/Metrics/Read|Считывает метрики.|
|/Metrics/Write|Запись метрик.|
|/Operations/Read|Чтение операций.|
|/Register/Action|Регистрирует поставщик Microsoft Insights.|
|/Tenants/Register/Action|Инициализация поставщика Microsoft Insights.|
|/Unregister/Action|Регистрирует поставщик Microsoft Insights.|
|/Webtests/Delete|Удаление конфигурации веб-теста.|
|/Webtests/GetToken/Read|Чтение токена веб-теста.|
|/Webtests/MetricDefinitions/Read|Чтение определений метрик веб-теста.|
|/Webtests/Metrics/Read|Чтение метрик веб-теста.|
|/Webtests/Read|Чтение конфигурации веб-теста.|
|/Webtests/Write|Запись конфигурации веб-теста.|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/read|Проверяет, является ли имя Key Vault допустимым и неиспользуемым.|
|/deletedVaults/read|Отображает свойства обратимо удаленных хранилищ Key Vault.|
|/hsmPools/delete|Удаление пула HSM.|
|/hsmPools/joinVault/action|Присоединение хранилища ключей к пулу HSM.|
|/hsmPools/read|Просмотр свойств пула HSM.|
|/hsmPools/write|Создание нового пула HSM или обновление свойств существующего пула HSM.|
|/locations/deletedVaults/purge/action|Очищает обратимо удаленное хранилище Key Vault.|
|/locations/deletedVaults/read|Отображает свойства обратимо удаленного Key Vault.|
|/locations/deleteVirtualNetworkOrSubnets/action|Уведомление Microsoft.KeyVault о том, что идет удаление виртуальной сети или подсети.|
|/locations/operationResults/read|Проверяет результат длительной операции.|
|/operations/read|Получение списка операций, доступных в поставщике ресурсов Microsoft.KeyVault.|
|/register/action|Регистрирует подписку.|
|/unregister/action|Отмена регистрации подписки.|
|/vaults/accessPolicies/write|Обновляет существующую политику доступа путем объединения, замены или добавления новой политики доступа в хранилище.|
|/vaults/delete|Удаляет Key Vault.|
|/vaults/deploy/action|Включает доступ к секретам в Key Vault при развертывании ресурсов Azure.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Возвращает параметр диагностики для ресурса.|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Создает или обновляет параметр диагностики для ресурса.|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Получение доступных журналов для хранилища ключей.|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для хранилища ключей.|
|/vaults/read|Отображает свойства Key Vault.|
|/vaults/secrets/read|Отображает свойства секрета, но не его значение.|
|/vaults/secrets/write|Создает новый секрет или обновляет значение существующего.|
|/vaults/write|Создает новое хранилище Key Vault или обновить свойства существующего.|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Операция | ОПИСАНИЕ |
|---|---|
|/labAccounts/CreateLab/action|Создание лаборатории в учетной записи лаборатории.|
|/labAccounts/delete|Удаление учетных записей лабораторий.|
|/labAccounts/labs/delete|Удаляет лаборатории.|
|/labAccounts/labs/environmentSettings/delete|Удаление параметра среды.|
|/labAccounts/labs/environmentSettings/environments/delete|Удаляет среды.|
|/labAccounts/labs/environmentSettings/environments/read|Считывает среды.|
|/labAccounts/labs/environmentSettings/environments/write|Добавляет или изменяет среды.|
|/labAccounts/labs/environmentSettings/Publish/action|Подготовка и отзыв необходимых ресурсов для параметра среды на основе текущего состояния параметра лаборатории или среды.|
|/labAccounts/labs/environmentSettings/read|Чтение параметра среды.|
|/labAccounts/labs/environmentSettings/write|Добавление или изменение параметра среды.|
|/labAccounts/labs/read|Считывает лаборатории.|
|/labAccounts/labs/users/delete|Удаление пользователей.|
|/labAccounts/labs/users/read|Чтение пользователей.|
|/labAccounts/labs/users/write|Добавление или изменение пользователей.|
|/labAccounts/labs/write|Добавляет или изменяет лаборатории.|
|/labAccounts/read|Чтение учетных записей лабораторий.|
|/labAccounts/write|Добавление или изменение учетных записей лабораторий.|
|/locations/operations/read|Считывает операции.|
|/register/action|Регистрирует подписку.|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/delete|Удаление учетной записи Location Based Services.|
|/accounts/listKeys/action|Вывод списка ключей учетной записи Location Based Services.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для учетных записей Location Based Services.|
|/accounts/read|Получение учетной записи Location Based Services.|
|/accounts/regenerateKey/action|Создание нового первичного или вторичного ключа учетной записи Location Based Services.|
|/accounts/write|Создание или обновление учетной записи Location Based Services.|
|/register/action|Регистрация поставщика.|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Операция | ОПИСАНИЕ |
|---|---|
|/integrationAccounts/agreements/delete|Удаление соглашения в учетной записи интеграции.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Получение URL-адреса обратного вызова для содержимого соглашения в учетной записи интеграции.|
|/integrationAccounts/agreements/read|Чтение соглашения в учетной записи интеграции.|
|/integrationAccounts/agreements/write|Создание или обновление соглашения в учетной записи интеграции.|
|/integrationAccounts/assemblies/delete|Удаление сборки в учетной записи интеграции.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Получение URL-адреса обратного вызова для содержимого сборки в учетной записи интеграции.|
|/integrationAccounts/assemblies/read|Чтение сборки в учетной записи интеграции.|
|/integrationAccounts/assemblies/write|Создание или обновление сборки в учетной записи интеграции.|
|/integrationAccounts/batchConfigurations/delete|Удаление конфигурации пакета в учетной записи интеграции.|
|/integrationAccounts/batchConfigurations/read|Чтение конфигурации пакета в учетной записи интеграции.|
|/integrationAccounts/batchConfigurations/write|Создание или обновление конфигурации пакета в учетной записи интеграции.|
|/integrationAccounts/certificates/delete|Удаление сертификата в учетной записи интеграции.|
|/integrationAccounts/certificates/read|Чтение сертификата в учетной записи интеграции.|
|/integrationAccounts/certificates/write|Создание или обновление сертификата в учетной записи интеграции.|
|/integrationAccounts/delete|Удаление учетной записи интеграции.|
|/integrationAccounts/listCallbackUrl/action|Получение URL-адреса обратного вызова для учетной записи интеграции.|
|/integrationAccounts/listKeyVaultKeys/action|Получение ключей в хранилище ключей.|
|/integrationAccounts/logTrackingEvents/action|Регистрация событий отслеживания в учетной записи интеграции.|
|/integrationAccounts/maps/delete|Удаление сопоставления в учетной записи интеграции.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Получение URL-адреса обратного вызова для содержимого сопоставления в учетной записи интеграции.|
|/integrationAccounts/maps/read|Чтение сопоставления в учетной записи интеграции.|
|/integrationAccounts/maps/write|Создание или обновление сопоставления в учетной записи интеграции.|
|/integrationAccounts/partners/delete|Удаление партнера в учетной записи интеграции.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Получение URL-адреса обратного вызова для содержимого партнера в учетной записи интеграции.|
|/integrationAccounts/partners/read|Чтение партнера в учетной записи интеграции.|
|/integrationAccounts/partners/write|Создание или обновление партнера в учетной записи интеграции.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Чтение определений журналов для учетной записи интеграции.|
|/integrationAccounts/read|Чтение учетной записи интеграции.|
|/integrationAccounts/regenerateAccessKey/action|Повторно создает секреты ключей доступа.|
|/integrationAccounts/schemas/delete|Удаление схемы в учетной записи интеграции.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Получение URL-адреса обратного вызова для содержимого схемы в учетной записи интеграции.|
|/integrationAccounts/schemas/read|Чтение схемы в учетной записи интеграции.|
|/integrationAccounts/schemas/write|Создание или обновление схемы в учетной записи интеграции.|
|/integrationAccounts/sessions/delete|Удаление сеанса в учетной записи интеграции.|
|/integrationAccounts/sessions/read|Чтение конфигурации пакета в учетной записи интеграции.|
|/integrationAccounts/sessions/write|Создание или обновление сеанса в учетной записи интеграции.|
|/integrationAccounts/write|Создание или обновление учетной записи интеграции.|
|/locations/workflows/validate/action|Проверяет рабочий процесс.|
|/operations/read|Получение операции.|
|/register/action|Регистрация поставщика ресурсов Microsoft.Logic для заданной подписки.|
|/workflows/accessKeys/delete|Удаляет ключ доступа.|
|/workflows/accessKeys/list/action|Выводит список секретов ключей доступа.|
|/workflows/accessKeys/read|Считывает ключ доступа.|
|/workflows/accessKeys/regenerate/action|Повторно создает секреты ключей доступа.|
|/workflows/accessKeys/write|Создает или обновляет ключ доступа.|
|/workflows/delete|Удаляет рабочий процесс.|
|/workflows/disable/action|Отключает рабочий процесс.|
|/workflows/enable/action|Включает рабочий процесс.|
|/workflows/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для рабочего процесса.|
|/workflows/listSwagger/action|Возвращает определения Swagger рабочего процесса.|
|/workflows/move/action|Назначает рабочему процессу, для которого задан идентификатор существующей подписки, группа ресурсов и (или) имя, другой идентификатор подписки, группу ресурсов и (или) имя.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Чтение параметров диагностики рабочего процесса.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики рабочего процесса.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Чтение определений журналов рабочих процессов.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Чтение определений метрик рабочих процессов.|
|/workflows/read|Считывает рабочий процесс.|
|/workflows/regenerateAccessKey/action|Повторно создает секреты ключей доступа.|
|/workflows/run/action|Начинает выполнение рабочего процесса.|
|/workflows/runs/actions/listExpressionTraces/action|Получение трассировок выражений для действия запуска рабочего процесса.|
|/workflows/runs/actions/read|Считывает действие выполнения рабочего процесса.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Получение трассировок выражений повторов для действия запуска рабочего процесса.|
|/workflows/runs/actions/repetitions/read|Чтение повтора действия запуска рабочего процесса.|
|/workflows/runs/actions/scoperepetitions/read|Чтение повтора области для действия запуска рабочего процесса.|
|/workflows/runs/cancel/action|Отменяет выполнение рабочего процесса.|
|/workflows/runs/operations/read|Считывает состояние операции выполнения рабочего процесса.|
|/workflows/runs/read|Считывает данные о выполнении рабочего процесса.|
|/workflows/suspend/action|Приостановка рабочего процесса.|
|/workflows/triggers/histories/read|Считывает журналы триггера.|
|/workflows/triggers/histories/resubmit/action|Повторяет отправку триггера рабочего процесса.|
|/workflows/triggers/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для триггера.|
|/workflows/triggers/read|Считывает триггер.|
|/workflows/triggers/reset/action|Сброс триггера.|
|/workflows/triggers/run/action|Выполняет триггер.|
|/workflows/triggers/setState/action|Настройка состояния триггера.|
|/workflows/validate/action|Проверяет рабочий процесс.|
|/workflows/versions/read|Считывает версию рабочего процесса.|
|/workflows/versions/triggers/listCallbackUrl/action|Возвращает URL-адрес обратного вызова для триггера.|
|/workflows/write|Создает или обновляет рабочий процесс.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Операция | ОПИСАНИЕ |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Перемещает связь плана предложения машинного обучения.|
|/commitmentPlans/commitmentAssociations/read|Считывает связь плана предложения машинного обучения.|
|/commitmentPlans/delete|Удаляет план предложения машинного обучения.|
|/commitmentPlans/join/action|Присоединяет план предложения машинного обучения.|
|/commitmentPlans/read|Считывает план предложения машинного обучения.|
|/commitmentPlans/write|Создает или обновляет план предложения машинного обучения.|
|/locations/operationresults/read|Получение результата операции машинного обучения.|
|/locations/operationsstatus/read|Получение состояния текущей операции машинного обучения.|
|/operations/read|Получение операций машинного обучения.|
|/register/action|Регистрирует подписку для поставщика ресурсов веб-службы машинного обучения и позволяет создавать веб-службы.|
|/skus/read|Получение номеров SKU плана предложения машинного обучения.|
|/webServices/action|Создает свойства региональной веб-службы для поддерживаемых регионов.|
|/webServices/delete|Удаляет веб-службу машинного обучения.|
|/webServices/read|Считывает веб-службу машинного обучения.|
|/webServices/write|Создает или обновляет веб-службу машинного обучения.|
|/Workspaces/delete|Удаляет рабочую область машинного обучения.|
|/Workspaces/listworkspacekeys/action|Выводит список ключей для рабочей области машинного обучения.|
|/Workspaces/read|Считывает рабочую область машинного обучения.|
|/Workspaces/resyncstoragekeys/action|Повторно синхронизирует ключи учетной записи хранения, настроенной для рабочей области машинного обучения.|
|/Workspaces/write|Создает или обновляет рабочую область машинного обучения.|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Операция | ОПИСАНИЕ |
|---|---|
|/operationalizationClusters/checkUpdate/action|Проверка наличия обновлений для системных служб кластера операционализации.|
|/operationalizationClusters/delete|Удаление учетной записи размещения.|
|/operationalizationClusters/listKeys/action|Вывод списка ключей, связанных с кластером операционализации.|
|/operationalizationClusters/read|Чтение учетной записи размещения.|
|/operationalizationClusters/updateSystem/action|Обновление системных служб в кластере операционализации.|
|/operationalizationClusters/write|Создание или обновление учетной записи размещения.|
|/register/action|Регистрация идентификатора подписки в поставщике ресурсов и разрешение создания вычислительных ресурсов машинного обучения.|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/accounts/delete|Удаление учетной записи размещения.|
|/accounts/read|Чтение учетной записи размещения.|
|/accounts/write|Создание или обновление учетной записи размещения.|
|/register/action|Регистрация идентификатора подписки в поставщике ресурсов и разрешение создания учетной записи размещения.|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Операция | ОПИСАНИЕ |
|---|---|
|/userAssignedIdentities/assign/action|Действие RBAC для назначения существующего пользовательского удостоверения для ресурса.|
|/userAssignedIdentities/delete|Удаление существующего пользовательского удостоверения.|
|/userAssignedIdentities/read|Получение существующего пользовательского удостоверения.|
|/userAssignedIdentities/write|Создание существующего пользовательского удостоверения или обновление связанных с ним тегов.|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Операция | ОПИСАНИЕ |
|---|---|
|/labAccounts/CreateLab/action|Создание лаборатории в учетной записи лаборатории.|
|/labAccounts/delete|Удаление учетных записей лабораторий.|
|/labAccounts/labs/delete|Удаляет лаборатории.|
|/labAccounts/labs/environmentSettings/delete|Удаление параметра среды.|
|/labAccounts/labs/environmentSettings/environments/delete|Удаляет среды.|
|/labAccounts/labs/environmentSettings/environments/read|Считывает среды.|
|/labAccounts/labs/environmentSettings/environments/write|Добавляет или изменяет среды.|
|/labAccounts/labs/environmentSettings/read|Чтение параметра среды.|
|/labAccounts/labs/environmentSettings/write|Добавление или изменение параметра среды.|
|/labAccounts/labs/labVms/delete|Удаление виртуальных машин лаборатории.|
|/labAccounts/labs/labVms/read|Чтение виртуальных машин лаборатории.|
|/labAccounts/labs/labVms/write|Добавление или изменение виртуальных машин лаборатории.|
|/labAccounts/labs/read|Считывает лаборатории.|
|/labAccounts/labs/write|Добавляет или изменяет лаборатории.|
|/labAccounts/read|Чтение учетных записей лабораторий.|
|/labAccounts/write|Добавление или изменение учетных записей лабораторий.|
|/locations/operations/read|Считывает операции.|
|/register/action|Регистрирует подписку.|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверка того, является ли имя указанной группы управления допустимым и уникальным.|
|/getEntities/action|Вывод списка всех сущностей (групп управления, подписок и т. д.) для прошедшего проверку подлинности пользователя.|
|/managementGroups/delete|Удаление группы управления.|
|/managementGroups/read|Вывод списка групп управления для пользователя, прошедшего проверку подлинности.|
|/managementGroups/subscriptions/delete|Отмена связывания подписки с группой управления.|
|/managementGroups/subscriptions/write|Связывание существующей подписки с группой управления.|
|/managementGroups/write|Создание или обновление группы управления.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Операция | ОПИСАНИЕ |
|---|---|
|/ClassicDevServices/delete|Выполнение операции DELETE в ресурсе классической службы разработки.|
|/ClassicDevServices/listSecrets/action|Получение ключей управления для ресурса классической службы разработки.|
|/ClassicDevServices/listSingleSignOnToken/action|Получение URL-адреса единого входа для классической службы разработки.|
|/ClassicDevServices/read|Выполнение операции GET в классической службе разработки.|
|/ClassicDevServices/regenerateKey/action|Создание ключей управления для ресурса классической службы разработки.|
|/Operations/read|Чтение операций для всех типов ресурсов.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Операция | ОПИСАНИЕ |
|---|---|
|/agreements/offers/plans/cancel/action|Отменяет соглашение для заданного элемента Marketplace.|
|/agreements/offers/plans/read|Возвращает соглашение для заданного элемента Marketplace.|
|/agreements/offers/plans/sign/action|Подписывает соглашение для заданного элемента Marketplace.|
|/agreements/read|Возвращение всех соглашений в указанной подписке.|
|/offertypes/publishers/offers/plans/agreements/read|Получение соглашения для заданного элемента виртуальной машины Marketplace.|
|/offertypes/publishers/offers/plans/agreements/write|Подписывание соглашения или его отмена для заданного элемента виртуальной машины Marketplace.|

## <a name="microsoftmedia"></a>Microsoft.Media

| Операция | ОПИСАНИЕ |
|---|---|
|/checknameavailability/action|Проверка доступности имени учетной записи служб мультимедиа.|
|/mediaservices/delete|Удаление учетной записи служб мультимедиа.|
|/mediaservices/listKeys/action|Вывод списка ключей ACS для учетной записи служб мультимедиа.|
|/mediaservices/read|Удаление учетной записи служб мультимедиа.|
|/mediaservices/regenerateKey/action|Повторное создание ключа ACS служб мультимедиа.|
|/mediaservices/syncStorageKeys/action|Синхронизация ключей хранилища для присоединенной учетной записи хранения Azure.|
|/mediaservices/write|Создание или обновление учетной записи служб мультимедиа.|
|/operations/read|Удаление учетной записи служб мультимедиа.|
|/register/action|Регистрация подписки для поставщика ресурсов служб мультимедиа и разрешение создания учетных записей служб мультимедиа.|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Операция | ОПИСАНИЕ |
|---|---|
|/Operations/read|Чтение предоставленных операций.|

## <a name="microsoftnetwork"></a>Microsoft.Network.

| Операция | ОПИСАНИЕ |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Предопределенная политика SSL шлюза приложений.|
|/applicationGatewayAvailableSslOptions/read|Доступные параметры SSL шлюза приложений.|
|/applicationGatewayAvailableWafRuleSets/read|Возвращает доступные наборы правил WAF шлюза приложений.|
|/applicationGateways/backendAddressPools/join/action|Выполняет присоединение к внутреннему пулу адресов шлюза приложений.|
|/applicationGateways/backendhealth/action|Возвращает сведения о работоспособности серверной части шлюза приложений.|
|/applicationGateways/delete|Удаляет шлюз приложений.|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Получение таблицы маршрутов, настроенной в шлюзе приложений.|
|/applicationGateways/effectiveRouteTable/action|Получение таблицы маршрутов, настроенной в шлюзе приложений.|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Получение событий для шлюза приложений.|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для шлюза приложений.|
|/applicationGateways/read|Возвращает шлюз приложений.|
|/applicationGateways/setSecurityCenterConfiguration/action|Настройка конфигурации центра безопасности шлюза приложений.|
|/applicationGateways/start/action|Запускает шлюз приложений.|
|/applicationGateways/stop/action|Останавливает шлюз приложений.|
|/applicationGateways/write|Создает новый шлюз приложений или обновляет существующий.|
|/applicationSecurityGroups/delete|Удаление группы безопасности приложений.|
|/applicationSecurityGroups/joinIpConfiguration/action|Присоединение конфигурации IP-адреса к группам безопасности приложений.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Присоединение правила безопасности к группам безопасности приложений.|
|/applicationSecurityGroups/read|Получение идентификатора группы безопасности приложений.|
|/applicationSecurityGroups/write|Создание группы безопасности приложений или обновление существующей группы.|
|/bgpServiceCommunities/read|Возвращает сообщества службы BGP.|
|/checkTrafficManagerNameAvailability/action|Проверяет доступность относительного DNS-имени диспетчера трафика.|
|/connections/delete|Удаляет подключение шлюза виртуальной сети.|
|/connections/read|Возвращает подключение шлюза виртуальной сети.|
|/connections/sharedkey/action|Получение общего ключа для подключения шлюза виртуальной сети.|
|/connections/sharedKey/read|Возвращает общий ключ для подключения шлюза виртуальной сети.|
|/connections/sharedKey/write|Создает новый общий ключ для подключения шлюза виртуальной сети или обновляет существующий.|
|/connections/vpndeviceconfigurationscript/read|Получение конфигурации VPN-устройства для подключения шлюза виртуальной сети.|
|/connections/write|Создает новое подключение шлюза виртуальной сети или обновляет существующее.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Удаление прокси-сервера плана защиты от атак DDoS.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Получение определения прокси-сервера плана защиты от атак DDoS.|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Создание прокси-сервера плана защиты от атак DDoS или обновление существующего.|
|/ddosProtectionPlans/delete|Удаление плана защиты от атак DDoS.|
|/ddosProtectionPlans/join/action|Присоединение плана защиты от атак DDoS.|
|/ddosProtectionPlans/read|Получение плана защиты от атак DDoS.|
|/ddosProtectionPlans/write|Создание или обновление плана защиты от атак DDoS. |
|/dnsoperationresults/read|Возвращает результаты операции DNS.|
|/dnsoperationstatuses/read|Возвращает состояние операции DNS. |
|/dnszones/A/delete|Удаляет из зоны DNS набор записей типа A с заданным именем.|
|/dnszones/A/read|Возвращает набор записей типа A в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/A/write|Создает или обновляет набора записей типа A в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/AAAA/delete|Удаляет из зоны DNS набор записей типа AAAA с заданным именем.|
|/dnszones/AAAA/read|Возвращает набор записей типа AAAA в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/AAAA/write|Создает или обновляет набора записей типа AAAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/all/read|Возвращает набор записей DNS разного типа.|
|/dnszones/CAA/delete|Удаление из зоны DNS набора записей типа CAA с заданным именем.|
|/dnszones/CAA/read|Получение набора записей типа CAA в формате JSON. Набор записей содержит срок жизни, теги и ETag.|
|/dnszones/CAA/write|Создание или обновление набора записей типа CAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/CNAME/delete|Удаляет из зоны DNS набор записей типа CNAME с заданным именем.|
|/dnszones/CNAME/read|Возвращает набор записей типа CNAME в формате JSON. Набор записей содержит срок жизни, теги и ETag.|
|/dnszones/CNAME/write|Создает или обновляет набора записей типа CNAME в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/delete|Удаляет зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets.|
|/dnszones/MX/delete|Удаляет из зоны DNS набор записей типа MX с заданным именем.|
|/dnszones/MX/read|Возвращает набор записей типа MX в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/MX/write|Создает или обновляет набора записей типа MX в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/NS/delete|Удаляет набор записей DNS типа NS.|
|/dnszones/NS/read|Возвращает набор записей DNS типа NS.|
|/dnszones/NS/write|Создает или обновляет набор записей DNS типа NS.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики зоны DNS.|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики зоны DNS.|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Получение определений метрик зоны DNS.|
|/dnszones/PTR/delete|Удаляет из зоны DNS набор записей типа PTR с заданным именем.|
|/dnszones/PTR/read|Возвращает набор записей типа PTR в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/PTR/write|Создает или обновляет набора записей типа PTR в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/read|Возвращает зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. Обратите внимание, что эта команда не извлекает наборы записей, содержащиеся в зоне.|
|/dnszones/recordsets/read|Возвращает набор записей DNS разного типа.|
|/dnszones/SOA/read|Возвращает набор записей DNS типа SOA.|
|/dnszones/SOA/write|Создает или обновляет набор записей DNS типа SOA.|
|/dnszones/SRV/delete|Удаляет из зоны DNS набор записей типа SRV с заданным именем.|
|/dnszones/SRV/read|Возвращает набор записей типа SRV в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/SRV/write|Создает или обновляет набор записей типа SRV.|
|/dnszones/TXT/delete|Удаляет из зоны DNS набор записей типа TXT с заданным именем.|
|/dnszones/TXT/read|Возвращает набор записей типа TXT в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag.|
|/dnszones/TXT/write|Создает или обновляет набора записей типа TXT в зоне DNS. Указанные записи заменят текущие записи в наборе записей.|
|/dnszones/write|Создает или обновляет зону DNS в группе ресурсов.  Используется для обновления тегов в ресурсе зоны DNS. Обратите внимание, что эту команду невозможно использовать для создания или обновления наборов записей в зоне.|
|/expressRouteCircuits/authorizations/delete|Удаляет данные авторизации канала ExpressRoute.|
|/expressRouteCircuits/authorizations/read|Возвращает данные авторизации канала ExpressRoute.|
|/expressRouteCircuits/authorizations/write|Создает новые данные авторизации канала ExpressRoute или обновляет существующие.|
|/expressRouteCircuits/delete|Удаляет канал ExpressRoute.|
|/expressRouteCircuits/peerings/arpTables/action|Возвращает таблицу ARP для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/connections/delete|Удаление подключения канала ExpressRoute.|
|/expressRouteCircuits/peerings/connections/read|Получение подключения канала ExpressRoute.|
|/expressRouteCircuits/peerings/connections/write|Создание нового ресурса подключения канала ExpressRoute или обновление существующего.|
|/expressRouteCircuits/peerings/delete|Удаляет пиринг канала ExpressRoute.|
|/expressRouteCircuits/peerings/read|Возвращает пиринг канала ExpressRoute.|
|/expressRouteCircuits/peerings/routeTables/action|Возвращает таблицу маршрутов для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Возвращает сводку по таблице маршрутов для пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/stats/read|Возвращает статистику пиринга канала ExpressRoute.|
|/expressRouteCircuits/peerings/write|Создает новый пиринг канала ExpressRoute или обновляет существующий.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики для каналов ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики для каналов ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Получение событий для каналов ExpressRoute.|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Получение определений метрик для каналов ExpressRoute.|
|/expressRouteCircuits/read|Возвращает канал ExpressRoute.|
|/expressRouteCircuits/stats/read|Возвращает статистику канала ExpressRoute.|
|/expressRouteCircuits/write|Создает новый канал ExpressRoute или обновляет существующий.|
|/expressRouteCrossConnections/delete|Удаление перекрестного подключения ExpressRoute.|
|/expressRouteCrossConnections/join/action|Присоединение перекрестного подключения Express Route.|
|/expressRouteCrossConnections/peerings/arpTables/action|Получение таблицы ARP перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/delete|Удаление перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/read|Получение перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/routeTables/action|Получение таблицы маршрутов перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Получение сводки таблицы маршрутов перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/stats/read|Получение статистики перекрестного пирингового подключения ExpressRoute.|
|/expressRouteCrossConnections/peerings/write|Создание перекрестного пирингового подключения ExpressRoute или обновление существующего.|
|/expressRouteCrossConnections/read|Получение перекрестного подключения ExpressRoute.|
|/expressRouteCrossConnections/write|Создание или обновление перекрестного подключения ExpressRoute.|
|/expressRouteServiceProviders/read|Возвращает поставщики службы ExpressRoute.|
|/loadBalancers/backendAddressPools/join/action|Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки.|
|/loadBalancers/backendAddressPools/read|Возвращает определение внутреннего пула адресов подсистемы балансировки нагрузки.|
|/loadBalancers/delete|Удаляет подсистему балансировки нагрузки.|
|/loadBalancers/frontendIPConfigurations/read|Возвращает определение внешней IP-конфигурации подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatPools/join/action|Присоединяет пул входящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatPools/read|Возвращает определение пула входящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/delete|Удаляет правило NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/join/action|Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/read|Возвращает определение правила NAT для входящего трафика подсистемы балансировки нагрузки.|
|/loadBalancers/inboundNatRules/write|Создает новое правило NAT для входящего трафика подсистемы балансировки нагрузки или обновляет существующее.|
|/loadBalancers/loadBalancingRules/read|Возвращает определение правила подсистемы балансировки нагрузки.|
|/loadBalancers/networkInterfaces/read|Возвращает ссылки на все сетевые интерфейсы в подсистеме балансировки нагрузки.|
|/loadBalancers/outboundNatRules/read|Возвращает определение правила пула исходящих подключений NAT подсистемы балансировки нагрузки.|
|/loadBalancers/probes/join/action|Разрешение использования зондов подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд.|
|/loadBalancers/probes/read|Возвращает пробу подсистемы балансировки нагрузки.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Возвращение параметров диагностики подсистемы балансировки нагрузки.|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики подсистемы балансировки нагрузки.|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Возвращение событий для подсистемы балансировки нагрузки.|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для подсистемы балансировки нагрузки.|
|/loadBalancers/read|Возвращает определение подсистемы балансировки нагрузки.|
|/loadBalancers/virtualMachines/read|Возвращает ссылки на все виртуальные машины в подсистеме балансировки нагрузки.|
|/loadBalancers/write|Создает новую подсистему балансировки нагрузки или обновляет существующую.|
|/localnetworkgateways/delete|Удаляет шлюз локальной сети.|
|/localnetworkgateways/read|Возвращает шлюз локальной сети.|
|/localnetworkgateways/write|Создает новый шлюз локальной сети или обновляет существующий.|
|/locations/checkDnsNameAvailability/read|Проверяет, доступна ли метка DNS в указанном расположении.|
|/locations/operationResults/read|Возвращает результат асинхронной операции POST или DELETE.|
|/locations/operations/read|Возвращает ресурс операции, представляющий состояние асинхронной операции.|
|/locations/usages/read|Возвращает метрики использования ресурсов.|
|/locations/virtualNetworkAvailableEndpointServices/read|Получение списка доступных служб конечных точек виртуальной сети.|
|/networkInterfaces/delete|Удаляет сетевой интерфейс.|
|/networkInterfaces/diagnosticIdentity/read|Получение удостоверения диагностики ресурса.|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Возвращает группы безопасности сети, настроенные для сетевого интерфейса виртуальной машины.|
|/networkInterfaces/effectiveRouteTable/action|Возвращает таблицу маршрутов, настроенную для сетевого интерфейса виртуальной машины.|
|/networkInterfaces/ipconfigurations/read|Возвращает определение IP-конфигурации сетевого интерфейса. |
|/networkInterfaces/join/action|Подключает виртуальную машину к сетевому интерфейсу.|
|/networkInterfaces/loadBalancers/read|Возвращает все подсистемы балансировки нагрузки, в которых используется сетевой интерфейс.|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для сетевого интерфейса.|
|/networkInterfaces/read|Возвращает определение сетевого интерфейса. |
|/networkInterfaces/write|Создает новый сетевой интерфейс или обновляет существующий. |
|/networkSecurityGroups/defaultSecurityRules/read|Возвращает определение правила безопасности по умолчанию.|
|/networkSecurityGroups/delete|Удаляет группу безопасности сети.|
|/networkSecurityGroups/join/action|Присоединяет группу безопасности сети.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики групп безопасности сети.|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики групп безопасности сети. Эта операция осуществляется при задействовании поставщика ресурсов аналитики.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Получение событий группы безопасности сети.|
|/networkSecurityGroups/read|Возвращает определение группы безопасности сети.|
|/networkSecurityGroups/securityRules/delete|Удаляет правило безопасности.|
|/networkSecurityGroups/securityRules/read|Возвращает определение правила безопасности.|
|/networkSecurityGroups/securityRules/write|Создает новое правило безопасности или обновляет существующее.|
|/networkSecurityGroups/write|Создает новую группу безопасности сети или обновляет существующую.|
|/networkWatchers/availableProvidersList/action|Получение всех доступных поставщиков услуг Интернета в заданном регионе Azure.|
|/networkWatchers/azureReachabilityReport/action|Получение оценки относительной задержки для поставщиков услуг Интернета из указанного расположения в регионы Azure.|
|/networkWatchers/configureFlowLog/action|Настраивает ведение журнала потоков для целевого ресурса.|
|/networkWatchers/connectionMonitors/delete|Удаление монитора подключения.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|Получение параметров диагностики монитора подключения.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|Создание или обновление параметров диагностики монитора подключения.|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|Получение доступных метрик для монитора подключения.|
|/networkWatchers/connectionMonitors/query/action|Запрос мониторинга подключения между указанными конечными точками.|
|/networkWatchers/connectionMonitors/read|Получение сведений о мониторе подключения.|
|/networkWatchers/connectionMonitors/start/action|Запуск мониторинга подключения между указанными конечными точками.|
|/networkWatchers/connectionMonitors/stop/action|Остановка или приостановка мониторинга подключения между указанными конечными точками.|
|/networkWatchers/connectionMonitors/write|Создание монитора подключения.|
|/networkWatchers/connectivityCheck/action|Проверка возможности прямого подключения TCP между виртуальной машиной и определенной конечной точкой, при этом используется другая виртуальная машина или произвольный удаленный сервер.|
|/networkWatchers/delete|Удаляет Наблюдатель за сетями.|
|/networkWatchers/ipFlowVerify/action|Проверяет, разрешена ли передача или прием пакета для определенного места назначения.|
|/networkWatchers/lenses/delete|Удаление группы связанных элементов.|
|/networkWatchers/lenses/query/action|Запрос мониторинга трафика в указанной конечной точке.|
|/networkWatchers/lenses/read|Получение сведений о группе связанных элементов.|
|/networkWatchers/lenses/start/action|Запуск мониторинга трафика в указанной конечной точке.|
|/networkWatchers/lenses/stop/action|Остановка или приостановка мониторинга трафика в указанной конечной точке.|
|/networkWatchers/lenses/write|Создание группы связанных элементов.|
|/networkWatchers/nextHop/action|Для указанного целевого объекта и конечного IP-адреса возвращает тип следующего прыжка и его IP-адрес.|
|/networkWatchers/packetCaptures/delete|Удаляет запись пакетов.|
|/networkWatchers/packetCaptures/queryStatus/action|Возвращает сведения о свойствах и состоянии ресурса записи пакетов.|
|/networkWatchers/packetCaptures/read|Возвращает определение записи пакетов.|
|/networkWatchers/packetCaptures/stop/action|Останавливает выполняемый сеанс записи пакетов.|
|/networkWatchers/packetCaptures/write|Создает запись пакетов.|
|/networkWatchers/queryFlowLogStatus/action|Возвращает состояние ведения журнала потоков для ресурса.|
|/networkWatchers/queryTroubleshootResult/action|Возвращает результат устранения неполадок предыдущей или текущей операции по устранению неполадок.|
|/networkWatchers/read|Возвращает определение Наблюдателя за сетями.|
|/networkWatchers/securityGroupView/action|Отображает настроенные и действующие правила группы безопасности сети для виртуальной машины.|
|/networkWatchers/topology/action|Возвращает представление уровня сети для ресурсов и их связей в группе ресурсов.|
|/networkWatchers/troubleshoot/action|Начинает устранение неполадок сетевого ресурса в Azure.|
|/networkWatchers/write|Создает новый Наблюдатель за сетями или обновляет существующий.|
|/operations/read|Возвращает доступные операции.|
|/publicIPAddresses/delete|Удаляет общедоступный IP-адрес.|
|/publicIPAddresses/join/action|Присоединяет общедоступный IP-адрес.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики общедоступного IP-адреса.|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики общедоступного IP-адреса.|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Получение определений журнала общедоступного IP-адреса.|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Получение определений метрик общедоступного IP-адреса.|
|/publicIPAddresses/read|Возвращает определение общедоступного IP-адреса.|
|/publicIPAddresses/write|Создает новый общедоступный IP-адрес или обновляет существующий. |
|/register/action|Регистрирует подписку.|
|/routeFilters/delete|Удаляет определение фильтра маршрутов.|
|/routeFilters/join/action|Присоединяет фильтр маршрутов.|
|/routeFilters/read|Возвращает определение фильтра маршрутов.|
|/routeFilters/routeFilterRules/delete|Удаляет определение правила фильтра маршрутов.|
|/routeFilters/routeFilterRules/read|Возвращает определение правила фильтра маршрутов.|
|/routeFilters/routeFilterRules/write|Создает новое правило фильтра маршрутов или обновляет существующее.|
|/routeFilters/write|Создает новый фильтр маршрутов или обновляет существующий.|
|/routeTables/delete|Удаляет определение таблицы маршрутов.|
|/routeTables/join/action|Присоединяет таблицу маршрутов.|
|/routeTables/read|Возвращает определение таблицы маршрутов.|
|/routeTables/routes/delete|Удаляет определение маршрута.|
|/routeTables/routes/read|Возвращает определение маршрута.|
|/routeTables/routes/write|Создает новый маршрут или обновляет существующий.|
|/routeTables/write|Создает новую таблицу маршрутов или обновляет существующую.|
|/securegateways/applicationRuleCollections/delete|Удаление коллекции правил приложения для защищенного шлюза.|
|/securegateways/applicationRuleCollections/read|Получение коллекции правил приложения для данного защищенного шлюза.|
|/securegateways/applicationRuleCollections/write|Создание или обновление коллекции правил приложения для защищенного шлюза.|
|/securegateways/delete|Удаление защищенного шлюза.|
|/securegateways/networkRuleCollections/delete|Удаление коллекции правил сети для защищенного шлюза.|
|/securegateways/networkRuleCollections/read|Получение коллекции правил сети для данного защищенного шлюза.|
|/securegateways/networkRuleCollections/write|Создание или обновление коллекции правил сети для защищенного шлюза.|
|/securegateways/read|Получение защищенного шлюза.|
|/securegateways/write|Создание или обновление защищенного шлюза.|
|/serviceEndpointPolicies/delete|Удаление политики конечной точки службы.|
|/serviceEndpointPolicies/join/action|Присоединение политики конечной точки службы.|
|/serviceEndpointPolicies/joinSubnet/action|Присоединение подсети к политикам конечной точки службы.|
|/serviceEndpointPolicies/read|Получение описания политики конечной точки службы.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Удаление определения политики конечной точки службы.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Получение описания определения политики конечной точки службы.|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Создание определения политики конечной точки службы или обновление существующего.|
|/serviceEndpointPolicies/write|Создание политики конечной точки службы или обновление существующей.|
|/trafficManagerGeographicHierarchies/read|Возвращает географическую иерархию диспетчера трафика, содержащую регионы, которые могут быть использованы для метода географической маршрутизации трафика.|
|/trafficManagerProfiles/azureEndpoints/delete|Удаление конечной точки Azure из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную конечную точку Azure.|
|/trafficManagerProfiles/azureEndpoints/read|Получение конечной точки Azure, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки.|
|/trafficManagerProfiles/azureEndpoints/write|Добавление новой конечной точки Azure в существующий профиль диспетчера трафика или обновление свойств существующей конечной точки Azure в этом профиле.|
|/trafficManagerProfiles/delete|Удаляет профиль диспетчера трафика. Будут утрачены все параметры, связанные с профилем диспетчера трафика, и профиль больше не сможет использоваться для маршрутизации трафика.|
|/trafficManagerProfiles/externalEndpoints/delete|Удаление внешней конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную внешнюю конечную точку.|
|/trafficManagerProfiles/externalEndpoints/read|Получение внешней конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки.|
|/trafficManagerProfiles/externalEndpoints/write|Добавление новой внешней конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей внешней конечной точки в этом профиле.|
|/trafficManagerProfiles/heatMaps/read|Получение тепловой карты диспетчера трафика для данного профиля диспетчера трафика, которая содержит количество запросов и данные задержек по исходному IP-адресу и расположению.|
|/trafficManagerProfiles/nestedEndpoints/delete|Удаление вложенной конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную вложенную конечную точку.|
|/trafficManagerProfiles/nestedEndpoints/read|Получение вложенной конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки.|
|/trafficManagerProfiles/nestedEndpoints/write|Добавление новой вложенной конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей вложенной конечной точки в этом профиле.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики диспетчера трафика.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики диспетчера трафика. Эта операция осуществляется при задействовании поставщика ресурсов аналитики.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Получение событий для диспетчера трафика.|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для диспетчера трафика.|
|/trafficManagerProfiles/read|Возвращает конфигурацию профиля диспетчера трафика. Он содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек, маршрутизируемых данным профилем диспетчера трафика.|
|/trafficManagerProfiles/write|Создает профиль диспетчера трафика или изменяет конфигурацию существующего профиля диспетчера трафика. Сюда входит включение или отключение профиля и изменение параметров DNS, параметров маршрутизации трафика или параметров мониторинга конечных точек. Конечные точки, маршрутизируемые с помощью профиля диспетчера трафика, можно добавлять, удалять, включать и отключать.|
|/trafficManagerUserMetricsKeys/delete|Удаление ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени.|
|/trafficManagerUserMetricsKeys/read|Получение ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени.|
|/trafficManagerUserMetricsKeys/write|Создание нового ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени.|
|/unregister/action|Отменяет регистрацию подписки.|
|/virtualHubs/delete|Удаление виртуального концентратора.|
|/virtualHubs/hubVirtualNetworkConnections/delete|Удаление подключения к виртуальной сети концентратора.|
|/virtualHubs/hubVirtualNetworkConnections/read|Получение подключения к виртуальной сети концентратора.|
|/virtualHubs/hubVirtualNetworkConnections/write|Создание или обновление подключения к виртуальной сети концентратора.|
|/virtualHubs/read|Получение виртуального концентратора.|
|/virtualHubs/write|Создание или обновление виртуального концентратора.|
|/virtualnetworkgateways/connections/read|Получение подключения к шлюзу виртуальной сети.|
|/virtualNetworkGateways/delete|Удаление шлюза виртуальной сети.|
|/virtualnetworkgateways/generatevpnclientpackage/action|Создание пакета VPN-клиента для шлюза виртуальной сети.|
|/virtualnetworkgateways/generatevpnprofile/action|Создание пакета VPN-профиля для шлюза виртуальной сети.|
|/virtualnetworkgateways/getadvertisedroutes/action|Получение объявленных маршрутов шлюза виртуальной сети.|
|/virtualnetworkgateways/getbgppeerstatus/action|Получение состояния кэширующего узла BGP шлюза виртуальной сети.|
|/virtualnetworkgateways/getlearnedroutes/action|Получение обнаруженных маршрутов шлюза виртуальной сети.|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Получение параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Получение URL-адреса предварительно созданного пакета профиля VPN-клиента.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики для шлюза виртуальной сети.|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики шлюза виртуальной сети. Эта операция осуществляется при задействовании поставщика ресурсов аналитики.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Получение событий шлюза виртуальной сети.|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик шлюза виртуальной сети.|
|/virtualNetworkGateways/read|Получение шлюза виртуальной сети.|
|/virtualnetworkgateways/reset/action|Сброс шлюза виртуальной сети.|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Настройка параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети.|
|/virtualnetworkgateways/supportedvpndevices/action|Получение списка поддерживаемых VPN-устройств.|
|/virtualNetworkGateways/write|Создание или обновление шлюза виртуальной сети.|
|/virtualNetworks/checkIpAddressAvailability/read|Проверяет, доступен ли IP-адрес в указанной виртуальной сети.|
|/virtualNetworks/customViews/get/action|Получение содержимого пользовательского представления виртуальной сети.|
|/virtualNetworks/customViews/read|Получение определения пользовательского представления виртуальной сети.|
|/virtualNetworks/delete|Удаляет виртуальную сеть.|
|/virtualNetworks/peer/action|Создает пиринг между виртуальными сетями.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметров диагностики виртуальной сети.|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметров диагностики виртуальной сети.|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Получение определений журналов виртуальной сети.|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Получение определений метрик виртуальной сети.|
|/virtualNetworks/read|Возвращает определение виртуальной сети.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Удаление прокси-сервера пиринга виртуальной сети.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Получение определения прокси-сервера пиринга виртуальной сети.|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Создание прокси-сервера пиринга виртуальной сети или обновление существующего.|
|/virtualNetworks/subnets/delete|Удаляет подсеть виртуальной сети.|
|/virtualNetworks/subnets/join/action|Присоединяет виртуальную сеть.|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL.|
|/virtualNetworks/subnets/read|Возвращает определение подсети виртуальной сети.|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Удаление ссылки для перехода к ресурсу.|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Получение определения ссылки для перехода к ресурсу.|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Создание ссылки для перехода к ресурсу или обновление существующей ссылки.|
|/virtualNetworks/subnets/virtualMachines/read|Возвращает ссылки на все виртуальные машины в подсети виртуальной сети.|
|/virtualNetworks/subnets/write|Создает новую подсеть пиринг виртуальной сети или обновляет существующую.|
|/virtualNetworks/taggedTrafficConsumers/delete|Удаление помеченного объекта-получателя трафика.|
|/virtualNetworks/taggedTrafficConsumers/read|Получение определения помеченного объекта-получателя трафика.|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Проверка помеченного объекта-получателя трафика.|
|/virtualNetworks/taggedTrafficConsumers/write|Создание помеченного объекта-получателя трафика или обновление существующего.|
|/virtualNetworks/usages/read|Получение сведения об использовании IP-адресов для каждой подсети виртуальной сети.|
|/virtualNetworks/virtualMachines/read|Возвращает ссылки на все виртуальные машины в виртуальной сети.|
|/virtualNetworks/virtualNetworkPeerings/delete|Удаляет пиринг виртуальной сети.|
|/virtualNetworks/virtualNetworkPeerings/read|Возвращает определение пиринга виртуальной сети.|
|/virtualNetworks/virtualNetworkPeerings/write|Создает новый пиринг виртуальной сети или обновляет существующий.|
|/virtualNetworks/write|Создает новую виртуальную сеть или обновляет существующую.|
|/virtualNetworkTaps/delete|Удаление устройства перехватчика трафика (TAP) виртуальной сети.|
|/virtualNetworkTaps/join/action|Присоединение TAP виртуальной сети.|
|/virtualNetworkTaps/read|Получение TAP виртуальной сети.|
|/virtualNetworkTaps/write|Создание или обновление TAP виртуальной сети.|
|/virtualwans/delete|Удаление виртуальной глобальной сети.|
|/virtualwans/read|Получение виртуальной глобальной сети.|
|/virtualWans/virtualHubProxies/delete|Удаление прокси-сервера виртуального концентратора.|
|/virtualWans/virtualHubProxies/read|Получение определения прокси-сервера виртуального концентратора.|
|/virtualWans/virtualHubProxies/write|Создание или обновление прокси-сервера виртуального концентратора.|
|/virtualwans/virtualHubs/read|Получение всех виртуальных концентраторов, связанных с виртуальной глобальной сетью.|
|/virtualwans/vpnconfiguration/read|Получение конфигурации VPN.|
|/virtualWans/vpnSiteProxies/delete|Удаление прокси-сервера сети VPN.|
|/virtualWans/vpnSiteProxies/read|Получение определения прокси-сервера сети VPN.|
|/virtualWans/vpnSiteProxies/write|Создание или обновление прокси-сервера сети VPN.|
|/virtualwans/vpnSites/read|Получение всех сетей VPN, связанных с виртуальной глобальной сетью.|
|/virtualwans/write|Создание или обновление виртуальной глобальной сети.|
|/vpnGateways/read|Получение шлюза VPN.|
|/vpnGateways/vpnConnections/read|Получение VPN-подключения.|
|/vpnGateways/vpnConnections/write|Установка VPN-подключения.|
|/vpnGateways/write|Установка VPN-шлюза.|
|/vpnsites/delete|Удаление ресурса сети VPN.|
|/vpnsites/read|Получение ресурса сети VPN.|
|/vpnsites/write|Создание или обновление ресурса сети VPN.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Операция | ОПИСАНИЕ |
|---|---|
|/CheckNamespaceAvailability/action|Проверяет, доступно ли имя заданного ресурса пространства имен в службе центра уведомлений.|
|/Namespaces/authorizationRules/action|Возвращает список описаний правил авторизации пространства имен.|
|/Namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/Namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/Namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/Namespaces/authorizationRules/regenerateKeys/action|Правило авторизации пространства имен повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно.|
|/Namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/Namespaces/CheckNotificationHubAvailability/action|Проверяет, доступно ли заданное имя центра уведомлений в пространстве имен.|
|/Namespaces/Delete|Удаляет ресурс пространства имен.|
|/Namespaces/NotificationHubs/authorizationRules/action|Возвращает список правил авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/authorizationRules/delete|Удаляет правила авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Возвращает строку подключения к центру уведомлений.|
|/Namespaces/NotificationHubs/authorizationRules/read|Возвращает список правил авторизации центра уведомлений.|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Правило авторизации центра уведомлений повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно.|
|/Namespaces/NotificationHubs/authorizationRules/write|Создает правила авторизации центра уведомлений и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/Namespaces/NotificationHubs/debugSend/action|Отправляет тестовое push-уведомление.|
|/Namespaces/NotificationHubs/Delete|Удаляет ресурс центра уведомлений.|
|/Namespaces/NotificationHubs/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/Namespaces/NotificationHubs/pnsCredentials/action|Возвращает все учетные данные PNS центра уведомлений. Сюда входят учетные данные WNS, MPNS, APNS, GCM и Baidu.|
|/Namespaces/NotificationHubs/read|Возвращает список описаний ресурсов центра уведомлений.|
|/Namespaces/NotificationHubs/write|Создает центр уведомлений и обновляет его свойства. К этим свойствам в основном относятся учетные данные PNS. Правила авторизации и срок жизни.|
|/Namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/Namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен.|
|/register/action|Регистрирует подписку для поставщика ресурсов центров уведомлений и позволяет создавать пространства имен и центры уведомлений.|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Операция | ОПИСАНИЕ |
|---|---|
|/linkTargets/read|Выводит список существующих учетных записей, которые не связаны с подпиской Azure. Чтобы связать эту подписку Azure с рабочей областью, укажите идентификатор клиента, возвращаемый этой операцией, в свойстве идентификатора клиента в операции создания рабочей области.|
|/register/action|Регистрирует подписку для поставщика ресурсов.|
|/workspaces/analytics/query/action|Поиск с помощью нового механизма.|
|/workspaces/analytics/query/schema/read|Получение схемы поиска версии 2.|
|/workspaces/api/query/action|Поиск с помощью нового механизма.|
|/workspaces/api/query/schema/read|Получение схемы поиска версии 2.|
|/workspaces/configurationScopes/delete|Удаляет область конфигурации.|
|/workspaces/configurationScopes/read|Возвращает область конфигурации.|
|/workspaces/configurationScopes/write|Задает область конфигурации.|
|/workspaces/datasources/delete|Удаляет источники данных в рабочей области.|
|/workspaces/datasources/read|Возвращает источники данных в рабочей области.|
|/workspaces/datasources/write|Создает или обновляет источники данных в рабочей области.|
|/workspaces/delete|Удаляет рабочую область. Если при создании рабочая область была связана с существующей рабочей областью, то эта связанная рабочая область не удаляется.|
|/workspaces/generateregistrationcertificate/action|Создает сертификат регистрации для рабочей области. Этот сертификат используется для подключения Microsoft System Center Operations Manager к рабочей области.|
|/workspaces/intelligencepacks/disable/action|Отключает пакет аналитики для заданной рабочей области.|
|/workspaces/intelligencepacks/enable/action|Включает пакет аналитики для заданной рабочей области.|
|/workspaces/intelligencepacks/read|Выводит список всех пакетов аналитики, которые являются видимыми для заданной рабочей области, а также сведения о том, включен или отключен пакет для этой рабочей области.|
|/workspaces/linkedServices/delete|Удаление связанной службы в заданной рабочей области.|
|/workspaces/linkedServices/read|Получение связанных служб в заданной рабочей области.|
|/workspaces/linkedServices/write|Создание или обновление связанных служб в заданной рабочей области.|
|/workspaces/listKeys/action|Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/listKeys/read|Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/managementGroups/read|Возвращает имена и метаданные для групп управления System Center Operations Manager, подключенных к этой рабочей области.|
|/workspaces/metricDefinitions/read|Получение определений метрик в рабочей области.|
|/workspaces/notificationSettings/delete|Удаление параметров уведомления пользователя для рабочей области.|
|/workspaces/notificationSettings/read|Получение параметров уведомления пользователя для рабочей области.|
|/workspaces/notificationSettings/write|Настройка параметров уведомления пользователя для рабочей области.|
|/workspaces/purge/action|Удаление указанных данных из рабочей области.|
|/workspaces/read|Возвращает существующую рабочую область.|
|/workspaces/savedSearches/delete|Удаляет сохраненный поисковый запрос.|
|/workspaces/savedSearches/read|Возвращает сохраненный поисковый запрос.|
|/workspaces/savedSearches/write|Создает сохраненный поисковый запрос.|
|/workspaces/schema/read|Возвращает схему поиска для рабочей области.  Схема поиска содержит предоставленные поля и их типы.|
|/workspaces/search/action|Выполняет поисковый запрос.|
|/workspaces/sharedKeys/action|Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/sharedKeys/read|Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области.|
|/workspaces/storageinsightconfigs/delete|Удаляет конфигурацию хранилища. Это остановит чтение данных из учетной записи хранения, выполняемое Microsoft Operational Insights.|
|/workspaces/storageinsightconfigs/read|Возвращает конфигурацию хранилища.|
|/workspaces/storageinsightconfigs/write|Создает конфигурацию учетной записи хранения. Эти конфигурации используются для извлечения данных из расположения в существующей учетной записи хранения.|
|/workspaces/usages/read|Возвращает данные об использовании для рабочей области, включая объем данных, считываемых рабочей областью.|
|/workspaces/write|Создает новую рабочую область или устанавливает связь с существующей рабочей областью с помощью указанного идентификатора клиента, полученного из нее.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Операция | ОПИСАНИЕ |
|---|---|
|/managementAssociations/delete|Удаление существующей связи управления.|
|/managementAssociations/read|Получение существующей связи управления.|
|/managementAssociations/write|Создание новой связи управления.|
|/managementConfigurations/delete|Удаление существующей конфигурации управления.|
|/managementConfigurations/read|Получение существующей конфигурации управления.|
|/managementConfigurations/write|Создание новой конфигурации управления.|
|/register/action|Регистрирует подписку для поставщика ресурсов.|
|/solutions/delete|Удаляет существующее решение OMS.|
|/solutions/read|Возвращает существующее решение OMS.|
|/solutions/write|Создает решение OMS.|

## <a name="microsoftportal"></a>Microsoft.Portal

| Операция | ОПИСАНИЕ |
|---|---|
|/dashboards/delete|Удаление панели мониторинга из подписки.|
|/dashboards/read|Чтение панели мониторинга для подписки.|
|/dashboards/write|Добавление или изменение панели мониторинга для подписки.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Операция | ОПИСАНИЕ |
|---|---|
|/capacities/checkNameAvailability/action|Проверка того, что имя выделенной емкости Power BI является допустимым и не используется.|
|/capacities/delete|Удаление выделенной емкости Power BI.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для выделенной емкости Power BI.|
|/capacities/read|Извлечение сведений об указанной выделенной емкости Power BI.|
|/capacities/write|Создание или обновление указанной выделенной емкости Power BI.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Операция | ОПИСАНИЕ |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp является внутренней операцией, используемой службой.|
|/locations/allocateStamp/action|AllocatedStamp является внутренней операцией, используемой службой.|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Проверка состояния архивации хранилищ служб восстановления.|
|/locations/backupValidateFeatures/action|Проверка компонентов.|
|/operations/read|Получение списка операций для поставщика ресурсов.|
|/register/action|Регистрация подписки для заданного поставщика ресурсов.|
|/Vaults/backupconfig/read|Возвращает конфигурацию хранилища служб восстановления.|
|/Vaults/backupconfig/write|Обновляет конфигурацию хранилища служб восстановления.|
|/Vaults/backupEngines/read|Возвращение всех серверов управления архивацией, зарегистрированных в хранилище.|
|/Vaults/backupFabrics/{имя_фабрики}/protectionContainers/{имя_контейнера}/items/read|Получение всех элементов в контейнере.|
|/Vaults/backupFabrics/backupProtectionIntent/write|Создание цели защиты для резервной копии.|
|/Vaults/backupFabrics/operationResults/read|Возвращает состояние операции.|
|/Vaults/backupFabrics/protectableContainers/read|Получение всех защищаемых контейнеров.|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Запрос рабочих нагрузок в контейнере.|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Возвращает результат операции, выполненной с контейнером защиты.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Архивирует защищенный элемент.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Удаляет защищенный элемент.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Возвращает результат операции, выполненной с защищенными элементами.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Возвращает состояние операции, выполненной с защищенными элементами.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Возвращает сведения об объекте для защищенного элемента.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Подготавливает мгновенное восстановление для защищенного элемента.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Возвращает точки восстановления для защищенных элементов.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Восстанавливает точки восстановления для защищенных элементов.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Отменяет мгновенное восстановление для защищенного элемента.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Создает элемент, защищенный службой архивации.|
|/Vaults/backupFabrics/protectionContainers/read|Возвращает все зарегистрированные контейнеры.|
|/Vaults/backupFabrics/protectionContainers/write|Создание зарегистрированного контейнера.|
|/Vaults/backupFabrics/refreshContainers/action|Обновляет список контейнеров.|
|/Vaults/backupJobs/cancel/action|Отменяет задание.|
|/Vaults/backupJobs/operationResults/read|Возвращает результат операции задания.|
|/Vaults/backupJobs/read|Возвращает все объекты заданий.|
|/Vaults/backupJobsExport/action|Экспортирует задания.|
|/Vaults/backupJobsExport/operationResults/read|Возвращает результат операции задания экспорта.|
|/Vaults/backupManagementMetaData/read|Возвращает метаданные управления архивацией для хранилища служб восстановления.|
|/Vaults/backupOperationResults/read|Возвращает результат операции архивации для хранилища служб восстановления.|
|/Vaults/backupOperations/read|Возвращает состояние операции архивации для хранилища служб восстановления.|
|/Vaults/backupPolicies/delete|Удаляет политику защиты.|
|/Vaults/backupPolicies/operationResults/read|Возвращает результаты операции политики.|
|/Vaults/backupPolicies/operations/read|Возвращает состояние операции политики.|
|/Vaults/backupPolicies/read|Возвращает все политики защиты.|
|/Vaults/backupPolicies/write|Создает политику защиты.|
|/Vaults/backupProtectableItems/read|Возвращает список всех защищаемых элементов.|
|/Vaults/backupProtectedItems/read|Возвращает список всех защищенных элементов.|
|/Vaults/backupProtectionContainers/read|Возвращает все контейнеры, принадлежащие подписке.|
|/Vaults/backupSecurityPIN/action|Возвращает данные ПИН-кода безопасности для хранилища служб восстановления.|
|/Vaults/backupstorageconfig/read|Возвращает конфигурацию службы хранилища для хранилища служб восстановления.|
|/Vaults/backupstorageconfig/write|Обновляет конфигурацию службы хранилища для хранилища служб восстановления.|
|/Vaults/backupUsageSummaries/read|Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления.|
|/Vaults/certificates/write|Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища.|
|/Vaults/delete|Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище".|
|/Vaults/extendedInformation/delete|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/extendedInformation/read|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/extendedInformation/write|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Vaults/monitoringAlerts/read|Возвращает оповещения для хранилища служб восстановления.|
|/Vaults/monitoringAlerts/write|Разрешение оповещения.|
|/Vaults/monitoringConfigurations/read|Возвращает конфигурацию уведомлений хранилища служб восстановления.|
|/Vaults/monitoringConfigurations/write|Настройка уведомлений по электронной почте в хранилище служб восстановления.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Диагностика в службе Azure Backup.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Диагностика в службе Azure Backup.|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Журналы службы Azure Backup.|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Метрики службы Azure Backup.|
|/Vaults/read|Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище".|
|/Vaults/registeredIdentities/delete|Операцию отмены регистрации контейнера можно использовать для отмены регистрации контейнера.|
|/Vaults/registeredIdentities/operationResults/read|Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции.|
|/Vaults/registeredIdentities/read|Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса.|
|/Vaults/registeredIdentities/write|Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления.|
|/vaults/replicationAlertSettings/read|Считывает параметры оповещений.|
|/vaults/replicationAlertSettings/write|Создает или обновляет параметры оповещений.|
|/vaults/replicationEvents/read|Считывает события.|
|/vaults/replicationFabrics/checkConsistency/action|Проверяет согласованность структуры.|
|/vaults/replicationFabrics/delete|Удаляет структуры.|
|/vaults/replicationFabrics/deployProcessServerImage/action|Развертывает образ сервера обработки.|
|/vaults/replicationFabrics/read|Считывает структуры.|
|/vaults/replicationFabrics/reassociateGateway/action|Повторно задает связь шлюза.|
|/vaults/replicationFabrics/remove/action|Удаляет структуру.|
|/vaults/replicationFabrics/renewcertificate/action|Обновление сертификата для структуры.|
|/vaults/replicationFabrics/replicationNetworks/read|Считывает сети.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Удаляет сетевые сопоставления.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Считывает сетевые сопоставления.|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Создает или обновляет сетевые сопоставления.|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Обнаруживает защищаемый элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Считывает контейнеры защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Удаляет контейнер защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Считывает защищаемые элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Применяет точку восстановления.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Удаляет защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Выполняет отработку отказа с фиксацией.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Выполняет плановую отработку отказа.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Считывает защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Считывает точки восстановления для репликации.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Удаляет защищенный элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Исправляет репликацию.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Повторно защищает защищенный элемент.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Тестирование отработки отказа|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Тестирует очистку отработки отказа.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Отработка отказа|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Обновляет службу Mobility Service.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Создает или обновляет защищенные элементы.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Удаляет сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Считывает сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Удаляет сопоставление контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Создает или обновляет сопоставления контейнера защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Переключает контейнер защиты.|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Создает или обновляет контейнеры защиты.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Удаляет поставщики служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Считывает поставщики служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Обновляет поставщик.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Удаляет поставщик служб восстановления.|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Удаление или обновление поставщиков служб восстановления.|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Считывает классификации хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Удаляет сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Считывает сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Создает или обновляет сопоставления классификаций хранилища.|
|/vaults/replicationFabrics/replicationvCenters/delete|Удаляет задания.|
|/vaults/replicationFabrics/replicationvCenters/read|Считывает задания.|
|/vaults/replicationFabrics/replicationvCenters/write|Создает или обновляет задания.|
|/vaults/replicationFabrics/write|Создает или обновляет структуры.|
|/vaults/replicationJobs/cancel/action|Отменяет задание.|
|/vaults/replicationJobs/read|Считывает задания.|
|/vaults/replicationJobs/restart/action|Перезапускает задание.|
|/vaults/replicationJobs/resume/action|Возобновляет выполнение задания.|
|/vaults/replicationPolicies/delete|Удаляет политики.|
|/vaults/replicationPolicies/read|Считывает политики.|
|/vaults/replicationPolicies/write|Создает или обновляет политики.|
|/vaults/replicationRecoveryPlans/delete|Удаляет планы восстановления.|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Выполняет отработку отказа с фиксацией для плана восстановления.|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Выполняет плановую отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/read|Считывает планы восстановления.|
|/vaults/replicationRecoveryPlans/reProtect/action|Повторно защищает план восстановления.|
|/vaults/replicationRecoveryPlans/testFailover/action|Тестирует отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Тестирует очистку отработки отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Выполняет отработку отказа для плана восстановления.|
|/vaults/replicationRecoveryPlans/write|Создает или обновляет планы восстановления.|
|/Vaults/tokenInfo/read|Возвращает сведения о маркере для хранилища служб восстановления.|
|/vaults/usages/read|Считывает данные об использовании хранилищ.|
|/Vaults/usages/read|Возвращает данные об использовании хранилища служб восстановления.|
|/Vaults/vaultTokens/read|Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища.|
|/Vaults/write|Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище".|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/checkNamespaceAvailability/action|Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability.|
|/namespaces/authorizationRules/action|Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/namespaces/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Получение ключей правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Получение правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Проверка доступности псевдонима пространства имен в заданной подписке.|
|/namespaces/disasterRecoveryConfigs/delete|Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен.|
|/namespaces/disasterRecoveryConfigs/failover/action|Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство.|
|/namespaces/disasterRecoveryConfigs/read|Получение конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/disasterRecoveryConfigs/write|Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/HybridConnections/authorizationRules/action|Операция обновления гибридного подключения. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT.|
|/namespaces/HybridConnections/authorizationRules/delete|Операция удаления правил авторизации гибридного подключения.|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Возвращает строку подключения для гибридного подключения.|
|/namespaces/HybridConnections/authorizationRules/read| Получение списка правил авторизации гибридного подключения.|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/HybridConnections/authorizationRules/write|Создает правила авторизации гибридного подключения и обновляет его свойства. Можно обновить права доступа к правилам авторизации.|
|/namespaces/HybridConnections/Delete|Операция удаления ресурса гибридного подключения.|
|/namespaces/HybridConnections/read|Возвращает список описаний ресурсов гибридного подключения.|
|/namespaces/HybridConnections/write|Создает или обновляет свойства гибридного подключения.|
|/namespaces/messagingPlan/read|Получение плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/messagingPlan/write|Обновление плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/operationresults/read|Получение состояния операции пространства имен.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/WcfRelays/authorizationRules/action|Операция обновления ретранслятора WCF. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT.|
|/namespaces/WcfRelays/authorizationRules/delete|Операция удаления правил авторизации ретранслятора WCF.|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Возвращает строку подключения к ретранслятору WCF.|
|/namespaces/WcfRelays/authorizationRules/read| Получение списка правил авторизации ретранслятора WCF.|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/WcfRelays/authorizationRules/write|Создает правила авторизации ретранслятора WCF и обновляет его свойства. Можно обновить права доступа к правилам авторизации.|
|/namespaces/WcfRelays/Delete|Операция удаления ресурса ретранслятора WCF.|
|/namespaces/WcfRelays/read|Возвращает список описаний ресурсов ретранслятора WCF.|
|/namespaces/WcfRelays/write|Создает или обновляет свойства ретранслятора WCF.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен.|
|/operations/read|Получение операций.|
|/register/action|Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора.|
|/unregister/action|Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора.|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Операция | ОПИСАНИЕ |
|---|---|
|/AvailabilityStatuses/current/read|Возвращает состояние доступности для указанного ресурса.|
|/AvailabilityStatuses/read|Возвращает состояния доступности для всех ресурсов в указанной области.|
|/healthevent/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/healthevent/Activated/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/healthevent/InProgress/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/healthevent/Pending/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/healthevent/Resolved/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/healthevent/Updated/action|Обозначение изменения состояния работоспособности для указанного ресурса.|
|/register/action|Регистрация подписки на службу "Работоспособность ресурсов Microsoft".|

## <a name="microsoftresources"></a>Microsoft.Resources

| Операция | ОПИСАНИЕ |
|---|---|
|/checkResourceName/action|Проверяет допустимость имени ресурса.|
|/deployments/cancel/action|Отменяет развертывание.|
|/deployments/delete|Удаляет развертывание.|
|/deployments/operations/read|Возвращает операции развертывания или выводит их список.|
|/deployments/read|Возвращает развернутые службы или выводит их список.|
|/deployments/validate/action|Проверяет развертывание.|
|/deployments/write|Создает или обновляет развертывание.|
|/links/delete|Удаляет ссылку на ресурс.|
|/links/read|Возвращает ссылки на ресурсы или выводит их список.|
|/links/write|Создает или обновляет ссылку на ресурс.|
|/marketplace/purchase/action|Покупка ресурса на Marketplace.|
|/providers/read|Возвращает список поставщиков.|
|/resources/read|Возвращает список ресурсов на основе фильтров.|
|/subscriptions/locations/read|Возвращает список поддерживаемых расположений.|
|/subscriptions/operationresults/read|Возвращает результаты операции подписки.|
|/subscriptions/providers/read|Возвращает поставщики ресурсов или выводит их список.|
|/subscriptions/read|Возвращает список подписок.|
|/subscriptions/resourceGroups/delete|Удаляет группу ресурсов со всеми ресурсами.|
|/subscriptions/resourcegroups/deployments/operations/read|Возвращает операции развертывания или выводит их список.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Возвращает состояния операций развертывания или выводит их список.|
|/subscriptions/resourcegroups/deployments/read|Возвращает развернутые службы или выводит их список.|
|/subscriptions/resourcegroups/deployments/write|Создает или обновляет развертывание.|
|/subscriptions/resourceGroups/moveResources/action|Перемещает ресурсы из одной группы ресурсов в другую.|
|/subscriptions/resourceGroups/read|Возвращает группы ресурсов или выводит их список.|
|/subscriptions/resourcegroups/resources/read|Возвращает ресурсы группы ресурсов.|
|/subscriptions/resourceGroups/validateMoveResources/action|Проверяет перемещение ресурсов из одной группы ресурсов в другую.|
|/subscriptions/resourceGroups/write|Создает или обновляет группу ресурсов.|
|/subscriptions/resources/read|Возвращает ресурсы подписки.|
|/subscriptions/tagNames/delete|Удаляет тег подписки.|
|/subscriptions/tagNames/read|Возвращает теги подписки или выводит их список.|
|/subscriptions/tagNames/tagValues/delete|Удаляет значение тега подписки.|
|/subscriptions/tagNames/tagValues/read|Возвращает значения тегов подписки или выводит их список.|
|/subscriptions/tagNames/tagValues/write|Добавляет значение тега подписки.|
|/subscriptions/tagNames/write|Добавляет тег подписки.|
|/tenants/read|Возвращает список клиентов.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Операция | ОПИСАНИЕ |
|---|---|
|/jobcollections/delete|Удаляет коллекцию заданий.|
|/jobcollections/disable/action|Отключает коллекцию заданий.|
|/jobcollections/enable/action|Включает коллекцию заданий.|
|/jobcollections/jobs/delete|Удаляет задание.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Создает определение приложения логики на основе задания планировщика.|
|/jobcollections/jobs/jobhistories/read|Получает журнал заданий.|
|/jobcollections/jobs/read|Возвращает задание.|
|/jobcollections/jobs/run/action|Выполняет задание.|
|/jobcollections/jobs/write|Создает или обновляет задание.|
|/jobcollections/read|Возвращает коллекцию заданий.|
|/jobcollections/write|Создает или обновляет коллекцию заданий.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность имени службы.|
|/register/action|Регистрирует подписку для поставщика ресурсов службы поиска и позволяет создавать службы поиска.|
|/searchServices/createQueryKey/action|Создает ключ запроса.|
|/searchServices/delete|Удаляет службу поиска.|
|/searchServices/diagnosticSettings/read|Чтение параметра диагностики для ресурса.|
|/searchServices/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/searchServices/listAdminKeys/action|Считывает ключи администратора.|
|/searchServices/logDefinitions/read|Получение доступных журналов службы поиска.|
|/searchServices/metricDefinitions/read|Получение доступных метрик службы поиска.|
|/searchServices/queryKey/delete|Удаляет ключ запроса.|
|/searchServices/queryKey/read|Считывает ключи запросов.|
|/searchServices/read|Считывает службу поиска.|
|/searchServices/regenerateAdminKey/action|Повторно создает ключ администратора.|
|/searchServices/start/action|Запускает службу поиска.|
|/searchServices/stop/action|Останавливает службу поиска.|
|/searchServices/write|Создает или обновляет службу поиска.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Операция | ОПИСАНИЕ |
|---|---|
|/alerts/read|Получение всех доступных оповещений системы безопасности.|
|/applicationWhitelistings/read|Возвращает список разрешений приложения.|
|/applicationWhitelistings/write|Создает новый список разрешений приложения или обновляет существующий.|
|/complianceResults/read|Получение результатов соответствия для ресурса.|
|/locations/alerts/activate/action|Активация оповещения системы безопасности.|
|/locations/alerts/dismiss/action|Отклонение оповещения системы безопасности.|
|/locations/alerts/read|Получение всех доступных оповещений системы безопасности.|
|/locations/jitNetworkAccessPolicies/initiate/action|Инициирует политику JIT-доступа к сети.|
|/locations/jitNetworkAccessPolicies/read|Возвращает политики JIT-доступа к сети.|
|/locations/jitNetworkAccessPolicies/write|Создает новую политику JIT-доступа к сети или обновляет существующую.|
|/locations/read|Получение расположения данных безопасности.|
|/locations/tasks/activate/action|Активирует рекомендацию по безопасности.|
|/locations/tasks/dismiss/action|Закрывает рекомендацию по безопасности.|
|/locations/tasks/read|Возвращает все доступные рекомендации по безопасности.|
|/locations/tasks/resolve/action|Разрешение рекомендации по безопасности.|
|/locations/tasks/start/action|Запуск рекомендации по безопасности.|
|/policies/read|Возвращает политику безопасности.|
|/policies/write|Обновляет политику безопасности.|
|/pricings/delete|Удаление параметров ценообразования для области.|
|/pricings/read|Получение параметров ценообразования для области.|
|/pricings/write|Обновление параметров ценообразования для области.|
|/register/action|Регистрация подписки в центре безопасности Azure.|
|/securityContacts/delete|Удаление контактного лица по вопросам безопасности.|
|/securityContacts/read|Получение контактного лица по вопросам безопасности.|
|/securityContacts/write|Обновление контактного лица по вопросам безопасности.|
|/securitySolutions/delete|Удаляет решение безопасности.|
|/securitySolutions/read|Возвращает решения безопасности.|
|/securitySolutions/write|Создает новое решение безопасности или обновляет существующее.|
|/securitySolutionsReferenceData/read|Возвращает эталонные данные решений безопасности.|
|/securityStatuses/read|Возвращает состояния работоспособности ресурсов Azure.|
|/securityStatusesSummaries/read|Получение сводок состояния безопасности для области.|
|/tasks/read|Возвращает все доступные рекомендации по безопасности.|
|/webApplicationFirewalls/delete|Удаляет брандмауэр веб-приложения.|
|/webApplicationFirewalls/read|Возвращает брандмауэры веб-приложения.|
|/webApplicationFirewalls/write|Создает новый брандмауэр веб-приложения или обновляет существующий.|
|/workspaceSettings/connect/action|Изменение параметров повторного подключения к рабочей области.|
|/workspaceSettings/delete|Удаление параметров рабочей области.|
|/workspaceSettings/read|Получение параметров рабочей области.|
|/workspaceSettings/write|Обновление параметров рабочей области.|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверяет доступность пространства имен в заданной подписке.|
|/checkNamespaceAvailability/action|Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability.|
|/namespaces/authorizationRules/action|Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/authorizationRules/delete|Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию. |
|/namespaces/authorizationRules/listkeys/action|Возвращает строку подключения к пространству имен.|
|/namespaces/authorizationRules/read|Возвращает список описаний правил авторизации пространства имен.|
|/namespaces/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/authorizationRules/write|Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации.|
|/namespaces/Delete|Удаляет ресурс пространства имен.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Получение ключей правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Получение правил авторизации основного пространства имен для аварийного восстановления.|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Проверка доступности псевдонима пространства имен в заданной подписке.|
|/namespaces/disasterRecoveryConfigs/delete|Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен.|
|/namespaces/disasterRecoveryConfigs/failover/action|Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство.|
|/namespaces/disasterRecoveryConfigs/read|Получение конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/disasterRecoveryConfigs/write|Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен.|
|/namespaces/eventGridFilters/delete|Удаление фильтра сетки событий, связанного с пространством имен.|
|/namespaces/eventGridFilters/read|Получение фильтра сетки событий, связанного с пространством имен.|
|/namespaces/eventGridFilters/write|Создание или обновление фильтра сетки событий, связанного с пространством имен.|
|/namespaces/eventhubs/read|Возвращает список описаний ресурсов концентратора событий.|
|/namespaces/messagingPlan/read|Получение плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/messagingPlan/write|Обновление плана обмена сообщениями для пространства имен. Это нерекомендуемый API. Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API. Эта операция не поддерживается для API версии 2017-04-01.|
|/namespaces/migrate/action|Операция переноса пространства имен.|
|/namespaces/operationresults/read|Получение состояния операции пространства имен.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Возвращает список описаний параметров диагностики пространства имен.|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Возвращает список описаний ресурсов журналов пространства имен.|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Возвращает список описаний ресурсов метрик пространства имен.|
|/namespaces/queues/authorizationRules/action|Операция обновления очереди. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT.|
|/namespaces/queues/authorizationRules/delete|Операция удаления правил авторизации очереди.|
|/namespaces/queues/authorizationRules/listkeys/action|Возвращает строку подключения к очереди.|
|/namespaces/queues/authorizationRules/read| Возвращает список правил авторизации очереди.|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/queues/authorizationRules/write|Создает правила авторизации очереди и обновляет ее свойства. Можно обновить права доступа к правилам авторизации.|
|/namespaces/queues/Delete|Операция удаления ресурса очереди.|
|/namespaces/queues/read|Возвращает список описаний ресурсов очереди.|
|/namespaces/queues/write|Создает или обновляет свойства очереди.|
|/namespaces/read|Возвращает список описаний ресурсов пространства имен.|
|/namespaces/topics/authorizationRules/action|Операция обновления раздела. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT.|
|/namespaces/topics/authorizationRules/delete|Операция удаления правил авторизации раздела.|
|/namespaces/topics/authorizationRules/listkeys/action|Возвращает строку подключения к разделу.|
|/namespaces/topics/authorizationRules/read| Возвращает список правил авторизации раздела.|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Повторно создает первичный или вторичный ключ для ресурса.|
|/namespaces/topics/authorizationRules/write|Создает правила авторизации раздела и обновляет его свойства. Можно обновить права доступа к правилам авторизации.|
|/namespaces/topics/Delete|Операция удаления ресурса раздела.|
|/namespaces/topics/read|Возвращает список описаний ресурсов раздела.|
|/namespaces/topics/subscriptions/Delete|Операция удаления ресурса подписки на раздел.|
|/namespaces/topics/subscriptions/read|Возвращает список описаний ресурсов подписки на раздел.|
|/namespaces/topics/subscriptions/rules/Delete|Операция удаления ресурса правила.|
|/namespaces/topics/subscriptions/rules/read|Возвращает список описаний ресурсов правила.|
|/namespaces/topics/subscriptions/rules/write|Создает или обновляет свойства правила.|
|/namespaces/topics/subscriptions/write|Создает или обновляет свойства подписки на раздел.|
|/namespaces/topics/write|Создает или обновляет свойства раздела.|
|/namespaces/write|Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен.|
|/operations/read|Получение операций.|
|/register/action|Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины.|
|/sku/read|Получение списка описаний ресурсов номеров SKU.|
|/sku/regions/read|Получение списка описаний ресурсов регионов для номеров SKU.|
|/unregister/action|Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины.|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Операция | ОПИСАНИЕ |
|---|---|
|/clusters/applications/delete|Удаление любого приложения.|
|/clusters/applications/read|Чтение любого приложения.|
|/clusters/applications/services/delete|Удаление любой службы.|
|/clusters/applications/services/partitions/read|Чтение любого раздела.|
|/clusters/applications/services/partitions/replicas/read|Чтение любой реплики.|
|/clusters/applications/services/read|Чтение любой службы.|
|/clusters/applications/services/statuses/read|Чтение состояния любой службы.|
|/clusters/applications/services/write|Создание или обновление любой службы.|
|/clusters/applications/write|Создание или обновление любого приложения.|
|/clusters/applicationTypes/delete|Удаление любого типа приложения.|
|/clusters/applicationTypes/read|Чтение любого типа приложения.|
|/clusters/applicationTypes/versions/delete|Удаление любой версии типа приложения.|
|/clusters/applicationTypes/versions/read|Чтение любой версии типа приложения.|
|/clusters/applicationTypes/versions/write|Создание или обновление любой версии типа приложения.|
|/clusters/applicationTypes/write|Создание или обновление любого типа приложения.|
|/clusters/delete|Удаление любого кластера.|
|/clusters/nodes/read|Чтение любого узла.|
|/clusters/read|Чтение любого кластера.|
|/clusters/statuses/read|Чтение состояния любого кластера.|
|/clusters/write|Создание или обновление любого кластера.|
|/locations/clusterVersions/read|Чтение любой версии кластера.|
|/locations/environments/clusterVersions/read|Чтение любой версии кластера для конкретной среды.|
|/locations/operationresults/read|Чтение результатов операций.|
|/locations/operations/read|Чтение операций по расположению.|
|/operations/read|Чтение доступных операций.|
|/register/action|Регистрация любого действия.|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Операция | ОПИСАНИЕ |
|---|---|
|/applicationDefinitions/delete|Удаление определения приложения.|
|/applicationDefinitions/read|Извлечение списка определений приложений.|
|/applicationDefinitions/write|Добавление или изменение определения приложения.|
|/applications/delete|Удаление приложения.|
|/applications/read|Извлечение списка приложений.|
|/applications/write|Создает приложение.|
|/locations/operationStatuses/read|Считывает состояние операции ресурса.|
|/register/action|Регистрация в решениях.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Операция | ОПИСАНИЕ |
|---|---|
|/checkNameAvailability/action|Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки.|
|/locations/auditingSettingsAzureAsyncOperation/read|Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере.|
|/locations/auditingSettingsOperationResults/read|Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере.|
|/locations/capabilities/read|Получение сведений о возможностях этой подписки в данном расположении.|
|/locations/databaseAzureAsyncOperation/read|Получение состояния операции базы данных.|
|/locations/databaseOperationResults/read|Получение состояния операции базы данных.|
|/locations/deletedServerAsyncOperation/read|Возвращение выполняемых операций на удаленном сервере.|
|/locations/deletedServerOperationResults/read|Возвращение выполняемых операций на удаленном сервере.|
|/locations/deletedServers/read|Возвращение списка удаленных серверов или получение свойств для указанного удаленного сервера.|
|/locations/deletedServers/recover/action|Восстановление удаленного сервера.|
|/locations/deleteVirtualNetworkOrSubnets/action|Удаление правил виртуальной сети, связанных с виртуальной сетью или подсетью.|
|/locations/elasticPoolAzureAsyncOperation/read|Возвращение асинхронной операции Azure для эластичного пула.|
|/locations/elasticPoolOperationResults/read|Возвращение результата операции эластичного пула.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере.|
|/locations/extendedAuditingSettingsOperationResults/read|Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере.|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Завершение операции восстановления управляемой базы данных.|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных.|
|/locations/managedTransparentDataEncryptionOperationResults/read|Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных.|
|/locations/read|Получение расположений, доступных для данной подписки.|
|/locations/syncAgentOperationResults/read|Получение результата операции ресурса агента синхронизации.|
|/locations/syncDatabaseIds/read|Получение идентификаторов базы данных синхронизации для конкретного региона и подписки.|
|/locations/syncGroupOperationResults/read|Получение результата операции синхронизации группы.|
|/locations/syncMemberOperationResults/read|Получение результата операции синхронизации элемента.|
|/locations/usages/read|Получение коллекции метрик использования для этой подписки в расположении.|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Возвращение сведений об асинхронной операции Azure для указанных правил виртуальной сети. |
|/locations/virtualNetworkRulesOperationResults/read|Возвращение сведений о правилах операции для указанных правил виртуальной сети. |
|/managedInstances/administrators/delete|Удаление существующего администратора управляемого экземпляра.|
|/managedInstances/administrators/read|Получение списка администраторов управляемого экземпляра.|
|/managedInstances/administrators/write|Создание или обновление администратора управляемого экземпляра с указанными параметрами.|
|/managedInstances/databases/delete|Удаление существующей управляемой базы данных.|
|/managedInstances/databases/read|Получение существующей управляемой базы данных.|
|/managedInstances/databases/securityAlertPolicies/read|Извлечение сведений о политике обнаружения угроз для базы данных, настроенной для заданной управляемой базы данных.|
|/managedInstances/databases/securityAlertPolicies/write|Изменение политики обнаружения угроз для заданной управляемой базы данных.|
|/managedInstances/databases/securityEvents/read|Получение событий безопасности управляемой базы данных.|
|/managedInstances/databases/transparentDataEncryption/read|Получение сведений о прозрачном шифровании данных в данной управляемой базе данных.|
|/managedInstances/databases/transparentDataEncryption/write|Изменение прозрачного шифрования данных в данной управляемой базе данных.|
|/managedInstances/databases/write|Создает новую базу данных или обновляет имеющуюся.|
|/managedInstances/delete|Удаление существующего управляемого экземпляра.|
|/managedInstances/metricDefinitions/read|Получение определений метрик управляемого экземпляра.|
|/managedInstances/metrics/read|Получение метрик управляемого экземпляра.|
|/managedInstances/read|Возвращение списка управляемых экземпляров или получение свойств указанного управляемого экземпляра.|
|/managedInstances/securityAlertPolicies/read|Извлечение сведений о политике обнаружения угроз, настроенной на данном управляемом сервере.|
|/managedInstances/securityAlertPolicies/write|Изменение политики обнаружения угроз на данном управляемом сервере.|
|/managedInstances/write|Создание управляемого экземпляра с указанными параметрами либо обновление свойств или тегов указанного управляемого экземпляра.|
|/operations/read|Получение доступных операций REST.|
|/register/action|Регистрация подписки для поставщика ресурсов базы данных SQL Azure и разрешение создания баз данных Microsoft SQL.|
|/servers/administratorOperationResults/read|Возвращение выполняемых операций администраторами серверов.|
|/servers/administrators/delete|Удаление администратора сервера.|
|/servers/administrators/read|Извлекает сведения об администраторе сервера.|
|/servers/administrators/write|Создает или обновляет учетные данные администратора сервера.|
|/servers/advisors/read|Возвращает список доступных помощников для сервера.|
|/servers/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для сервера.|
|/servers/advisors/recommendedActions/write|Применяет рекомендуемое действие к серверу.|
|/servers/advisors/write|Обновляет состояние автовыполнения Помощника на уровне сервера.|
|/servers/auditingPolicies/read|Извлекает сведения о политике аудита таблиц по умолчанию, настроенной на заданном сервере.|
|/servers/auditingPolicies/write|Изменяет политику аудита таблиц по умолчанию для заданного сервера.|
|/servers/auditingSettings/operationResults/read|Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере.|
|/servers/auditingSettings/read|Извлекает сведения о политике аудита больших двоичных объектов, настроенной на заданном сервере.|
|/servers/auditingSettings/write|Изменяет политику аудита больших двоичных объектов для заданного сервера.|
|/servers/automaticTuning/read|Возвращение параметров автоматической настройки для сервера.|
|/servers/automaticTuning/write|Обновление параметров автоматической настройки для сервера и возвращение обновленных параметров.|
|/servers/backupLongTermRetentionVaults/delete|Удаление существующего архивного хранилища резервных копий.|
|/servers/backupLongTermRetentionVaults/read|С помощью этой операции можно получить хранилище резервных копий долгосрочного хранения. Возвращает сведения о хранилище, зарегистрированном на данном сервере.|
|/servers/backupLongTermRetentionVaults/write|С помощью этой операции можно зарегистрировать на сервере хранилище резервных копий долгосрочного хранения.|
|/servers/communicationLinks/delete|Удаление существующего подключения сервера.|
|/servers/communicationLinks/read|Возвращение списка подключений для указанного сервера.|
|/servers/communicationLinks/write|Создание или обновление подключения сервера.|
|/servers/connectionPolicies/read|Возвращение списка политик подключений для указанного сервера.|
|/servers/connectionPolicies/write|Создание или обновление политики подключения сервера.|
|/servers/databases/advisors/read|Возвращает список доступных помощников для базы данных.|
|/servers/databases/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для базы данных.|
|/servers/databases/advisors/recommendedActions/write|Применяет рекомендуемое действие к базе данных.|
|/servers/databases/advisors/write|Обновляет состояние автовыполнения Помощника на уровне базы данных.|
|/servers/databases/auditingPolicies/read|Извлекает сведения о политике аудита таблиц, настроенной для заданной базы данных.|
|/servers/databases/auditingPolicies/write|Изменяет политику аудита таблиц для заданной базы данных.|
|/servers/databases/auditingSettings/read|Извлекает сведения о политике аудита больших двоичных объектов, настроенной для заданной базы данных.|
|/servers/databases/auditingSettings/write|Изменяет политику аудита больших двоичных объектов для заданной базы данных.|
|/servers/databases/auditRecords/read|Извлекает записи аудита больших двоичных объектов для базы данных.|
|/servers/databases/automaticTuning/read|Возвращение параметров автоматической настройки базы данных.|
|/servers/databases/automaticTuning/write|Обновление параметров автоматической настройки базы данных и возвращение обновленных параметров.|
|/servers/databases/azureAsyncOperation/read|Получение состояния операции базы данных.|
|/servers/databases/backupLongTermRetentionPolicies/read|Возвращение списка политик хранения резервных копий для указанной базы данных.|
|/servers/databases/backupLongTermRetentionPolicies/write|Создание или обновление политики хранения резервных копий базы данных.|
|/servers/databases/connectionPolicies/read|Извлекает сведения о политике подключений, настроенной для заданной базы данных.|
|/servers/databases/connectionPolicies/write|Изменяет политику подключений для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/read|Получение списка политик маскировки данных базы данных.|
|/servers/databases/dataMaskingPolicies/rules/delete|Удаление правила политики маскировки данных для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/rules/read|Извлекает сведения о правиле политики маскирования данных, настроенной для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/rules/write|Изменяет правило политики маскирования данных для заданной базы данных.|
|/servers/databases/dataMaskingPolicies/write|Изменяет политику маскирования данных для заданной базы данных.|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Возвращает сведения о шаге распределенного запроса к хранилищу данных для выбранного идентификатора шага.|
|/servers/databases/dataWarehouseQueries/read|Возвращает данные запроса на распределение к хранилищу данных для выбранного идентификатора запроса.|
|/servers/databases/dataWarehouseUserActivities/read|Получение действий пользователя в экземпляре хранилища данных SQL, включая выполняемые и приостановленные запросы.|
|/servers/databases/delete|Удаление имеющейся базы данных.|
|/servers/databases/export/action|Экспорт базы данных SQL Azure.|
|/servers/databases/extendedAuditingSettings/read|Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной для заданной базы данных.|
|/servers/databases/extendedAuditingSettings/write|Изменение расширенной политики аудита больших двоичных объектов для заданной базы данных.|
|/servers/databases/extensions/read|Получение коллекции расширений для базы данных.|
|/servers/databases/extensions/write|Изменение расширения для конкретной базы данных.|
|/servers/databases/geoBackupPolicies/read|Получение политик геоизбыточного резервного копирования для конкретной базы данных.|
|/servers/databases/geoBackupPolicies/write|Создание или обновление политики геоизбыточного резервного копирования базы данных.|
|/servers/databases/importExportOperationResults/read|Получение выполняемых операций импорта и экспорта.|
|/servers/databases/metricDefinitions/read|Возвращает типы метрик, доступных для баз данных.|
|/servers/databases/metrics/read|Получение метрик для баз данных.|
|/servers/databases/move/action|Переименование базы данных SQL Azure.|
|/servers/databases/operationResults/read|Получение состояния операции базы данных.|
|/servers/databases/operations/cancel/action|Отмена приостановленной асинхронной операции базы данных SQL Azure, которая еще не завершена.|
|/servers/databases/operations/read|Возвращение списка операций, выполняемых в базе данных.|
|/servers/databases/pause/action|Приостановка работы базы данных хранилища данных SQL Azure.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Возвращает доступные журналы для баз данных.|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Возвращает типы метрик, доступных для баз данных.|
|/servers/databases/queryStore/queryTexts/read|Возвращение коллекции текстов запросов, которые соответствуют заданным параметрам.|
|/servers/databases/queryStore/read|Возвращение текущих значений параметров хранилища запросов для базы данных.|
|/servers/databases/queryStore/write|Обновляет параметр хранилища запросов для базы данных.|
|/servers/databases/read|Возвращение списка баз данных или возвращение свойств указанной базы данных.|
|/servers/databases/replicationLinks/delete|Принудительно удаляет отношение репликации. При этом возможна потеря данных.|
|/servers/databases/replicationLinks/failover/action|Выполнение отработки отказа после синхронизации всех изменений из базы данных-источника, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник.|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Выполнение немедленной отработки отказа с потенциальной потерей данных, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник.|
|/servers/databases/replicationLinks/read|Возвращает сведения о каналах репликации, установленных для определенной базы данных.|
|/servers/databases/replicationLinks/unlink/action|Удаляет отношение репликации в принудительном порядке или после синхронизации с партнером.|
|/servers/databases/replicationLinks/updateReplicationMode/action|Обновляет режим репликации для ссылки на синхронный или асинхронный режим.|
|/servers/databases/restorePoints/action|Создает точку восстановления.|
|/servers/databases/restorePoints/read|Возвращение списка точек восстановления для базы данных.|
|/servers/databases/resume/action|Возобновление работы базы данных хранилища данных SQL Azure.|
|/servers/databases/schemas/read|Получение списка схем базы данных.|
|/servers/databases/schemas/tables/columns/read|Извлекает список столбцов таблицы.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Удаление метки конфиденциальности данного столбца.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Получение метки конфиденциальности данного столбца.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Создание или обновление метки конфиденциальности данного столбца.|
|/servers/databases/schemas/tables/read|Получение списка таблиц базы данных.|
|/servers/databases/schemas/tables/recommendedIndexes/read|Извлекает список рекомендаций по индексам для базы данных.|
|/servers/databases/schemas/tables/recommendedIndexes/write|Применяет рекомендацию по индексу.|
|/servers/databases/securityAlertPolicies/read|Извлекает сведения о политике обнаружения угроз, настроенной для заданной базы данных.|
|/servers/databases/securityAlertPolicies/write|Изменяет политику обнаружения угроз для заданной базы данных.|
|/servers/databases/securityMetrics/read|Возвращение коллекции метрик безопасности базы данных.|
|/servers/databases/sensitivityLabels/read|Получение списка меток конфиденциальности для конкретной базы данных.|
|/servers/databases/serviceTierAdvisors/read|Возвращает предложение по масштабированию базы данных на основе статистики выполнения запросов для повышения производительности или снижения затрат.|
|/servers/databases/syncGroups/cancelSync/action|Отмена синхронизации группы синхронизации.|
|/servers/databases/syncGroups/delete|Удаление существующей группы синхронизации.|
|/servers/databases/syncGroups/hubSchemas/read|Возвращение списка схем баз данных концентратора синхронизации.|
|/servers/databases/syncGroups/logs/read|Возвращение списка журналов группы синхронизации.|
|/servers/databases/syncGroups/read|Возвращение списка групп синхронизации или свойств для указанной группы.|
|/servers/databases/syncGroups/refreshHubSchema/action|Обновление схемы базы данных концентратора синхронизации.|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Получение результата операции обновления схемы концентратора синхронизации.|
|/servers/databases/syncGroups/syncMembers/delete|Удаление существующего элемента синхронизации.|
|/servers/databases/syncGroups/syncMembers/read|Возвращение списка элементов синхронизации или свойств для указанного элемента.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Обновление схемы элемента синхронизации.|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Получение результата операции обновления схемы элемента синхронизации.|
|/servers/databases/syncGroups/syncMembers/schemas/read|Возвращение списка схем баз данных элемента синхронизации.|
|/servers/databases/syncGroups/syncMembers/write|Создание элемента синхронизации с указанными параметрами либо обновление свойств указанного элемента.|
|/servers/databases/syncGroups/triggerSync/action|Активация синхронизации группы синхронизации.|
|/servers/databases/syncGroups/write|Создание группы синхронизации с указанными параметрами либо обновление свойств указанной группы.|
|/servers/databases/topQueries/queryText/action|Возвращает текст Transact-SQL для выбранного идентификатора запроса.|
|/servers/databases/topQueries/read|Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период.|
|/servers/databases/topQueries/statistics/read|Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период.|
|/servers/databases/transparentDataEncryption/operationResults/read|Возвращение выполняемых операций прозрачного шифрования данных.|
|/servers/databases/transparentDataEncryption/read|Извлекает состояние и сведения о функции безопасности "прозрачное шифрование данных" для заданной базы данных.|
|/servers/databases/transparentDataEncryption/write|Изменение состояния прозрачного шифрования данных.|
|/servers/databases/upgradeDataWarehouse/action|Обновление работы базы данных хранилища данных SQL Azure.|
|/servers/databases/usages/read|Возвращение сведений об использовании базы данных SQL Azure.|
|/servers/databases/vulnerabilityAssessments/delete|Удаление оценки уязвимости указанной базы данных.|
|/servers/databases/vulnerabilityAssessments/read|Извлечение сведений об оценке уязвимости, настроенной для заданной базы данных.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Удаление базовых показателей правила оценки уязвимости для указанной базы данных.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Получение базовых показателей правила оценки уязвимости для указанной базы данных.|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Изменение базовых показателей правила оценки уязвимости для указанной базы данных.|
|/servers/databases/vulnerabilityAssessments/scans/action|Выполняет поиск уязвимостей базы данных.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Преобразование существующего результата проверки в удобочитаемый формат. Если он уже существует, ничего не происходит.|
|/servers/databases/vulnerabilityAssessments/scans/read|Возвращение списка записей сканирования оценки уязвимости базы данных или получение записи для указанного идентификатора сканирования.|
|/servers/databases/vulnerabilityAssessments/write|Изменение оценки уязвимости указанной базы данных.|
|/servers/databases/vulnerabilityAssessmentScans/action|Выполняет поиск уязвимостей базы данных.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Получение результата выполнения сканирования оценки уязвимости базы данных.|
|/servers/databases/vulnerabilityAssessmentSettings/read|Извлечение сведений об оценке уязвимости, настроенной для заданной базы данных.|
|/servers/databases/vulnerabilityAssessmentSettings/write|Изменение оценки уязвимости указанной базы данных.|
|/servers/databases/write|Создание базы данных с указанными параметрами либо обновление свойств или тегов для указанной базы данных.|
|/servers/delete|Удаление существующего сервера.|
|/servers/disasterRecoveryConfiguration/delete|Удаление существующих конфигураций аварийного восстановления для указанного сервера.|
|/servers/disasterRecoveryConfiguration/failover/action|Отработка отказа конфигурации аварийного восстановления.|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Принудительная отработка отказа конфигурации аварийного восстановления.|
|/servers/disasterRecoveryConfiguration/read|Получение коллекции конфигураций аварийного восстановления, в которых содержится данный сервер.|
|/servers/disasterRecoveryConfiguration/write|Изменение конфигурации аварийного восстановления сервера.|
|/servers/elasticPoolEstimates/read|Возвращает список оценок эластичных пулов, уже созданных на этом сервере.|
|/servers/elasticPoolEstimates/write|Создает оценку эластичного пула для указанных баз данных.|
|/servers/elasticPools/advisors/read|Возвращает список доступных помощников для эластичного пула.|
|/servers/elasticPools/advisors/recommendedActions/read|Возвращает список рекомендуемых действий указанного помощника для эластичного пула.|
|/servers/elasticPools/advisors/recommendedActions/write|Применяет рекомендуемое действие к эластичному пулу.|
|/servers/elasticPools/advisors/write|Обновляет состояние автовыполнения Помощника на уровне эластичного пула.|
|/servers/elasticPools/databases/read|Получение списка баз данных в эластичном пуле.|
|/servers/elasticPools/delete|Удаление имеющегося эластичного пула.|
|/servers/elasticPools/elasticPoolActivity/read|Извлекает действия и сведения о заданном пуле эластичных баз данных.|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Извлекает действия заданной базы данных, входящей в пул эластичных баз данных, а также сведения о ней.|
|/servers/elasticPools/metricDefinitions/read|Возвращает типы метрик, доступных для пулов эластичных баз данных.|
|/servers/elasticPools/metrics/read|Возвращение метрик для пулов эластичных баз данных.|
|/servers/elasticPools/operations/cancel/action|Отмена приостановленной асинхронной операции эластичного пула SQL Azure, которая еще не завершена.|
|/servers/elasticPools/operations/read|Возвращение списка операций, выполняемых в эластичном пуле.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Возвращает типы метрик, доступных для пулов эластичных баз данных.|
|/servers/elasticPools/read|Извлечение сведений о пуле эластичных баз данных для заданного сервера.|
|/servers/elasticPools/skus/read|Возвращение коллекции доступных номеров SKU для этого пула эластичных баз данных.|
|/servers/elasticPools/write|Создание эластичного пула или изменение свойств существующего.|
|/servers/encryptionProtector/read|Возвращение списка предохранителей шифрования сервера или получение свойств для указанного предохранителя.|
|/servers/encryptionProtector/write|Обновление свойств указанного предохранителя шифрования сервера.|
|/servers/extendedAuditingSettings/read|Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной на заданном сервере.|
|/servers/extendedAuditingSettings/write|Изменение расширенного аудита больших двоичных объектов для заданного сервера.|
|/servers/failoverGroups/delete|Удаление существующей группы отработки отказа.|
|/servers/failoverGroups/failover/action|Выполнение плановой отработки отказа в существующей группе отработки отказа.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Выполнение принудительной отработки отказа в существующей группе отработки отказа.|
|/servers/failoverGroups/read|Возвращение списка групп отработки отказа или свойств для указанной группы.|
|/servers/failoverGroups/write|Создание группы отработки отказа с указанными параметрами либо обновление свойств или тегов указанной группы.|
|/servers/firewallRules/delete|Удаление существующего правила брандмауэра сервера.|
|/servers/firewallRules/read|Возвращение списка правил брандмауэра сервера или получение свойства для указанного правила.|
|/servers/firewallRules/write|Создание правила брандмауэра сервера с указанными параметрами, обновление свойств указанного правила или перезапись всех существующих правил новыми правилами брандмауэра сервера.|
|/servers/import/action|Создает базу данных на сервере и развертывает схему и данные из пакета DACPAC.|
|/servers/importExportOperationResults/read|Получение выполняемых операций импорта и экспорта.|
|/servers/keys/delete|Удаление существующего ключа сервера.|
|/servers/keys/read|Возвращение списка ключей сервера или возвращение свойств указанного ключа сервера.|
|/servers/keys/write|Создание ключа с указанными параметрами либо обновление свойств или тегов указанного ключа сервера.|
|/servers/operationResults/read|Возвращение выполняемых операций сервера.|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Возвращение типов метрик, доступных для серверов.|
|/servers/read|Возвращение списка серверов или получение свойств для указанного сервера.|
|/servers/recommendedElasticPools/databases/read|Извлекает метрики для рекомендуемых пулов эластичных баз данных для заданного сервера.|
|/servers/recommendedElasticPools/read|Извлекает рекомендацию для пулов эластичных баз данных, позволяющую снизить затраты или повысить производительность, на основе журнала использования ресурсов.|
|/servers/recoverableDatabases/read|Эта операция используется для аварийного восстановления динамической базы данных. Она позволяет восстановить базу данных до последней известной корректной точки. Возвращение сведений о последней корректной резервной копии, но без восстановления базы данных.|
|/servers/restorableDroppedDatabases/read|Получение списка баз данных, которые были удалены на заданном сервере, но остались в политике хранения.|
|/servers/securityAlertPolicies/operationResults/read|Извлечение результатов операции записи политики обнаружения угроз на сервере.|
|/servers/securityAlertPolicies/read|Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере.|
|/servers/securityAlertPolicies/write|Изменение политики обнаружения угроз для заданного сервера.|
|/servers/serviceObjectives/read|Извлекает список целей уровня обслуживания (также известных как уровни производительности), доступных на данном сервере.|
|/servers/syncAgents/delete|Удаление существующего агента синхронизации.|
|/servers/syncAgents/generateKey/action|Создание ключа регистрации агента синхронизации.|
|/servers/syncAgents/linkedDatabases/read|Получение списка баз данных, связанных с агентом синхронизации.|
|/servers/syncAgents/read|Возвращение списка агентов синхронизации или свойств для указанного агента.|
|/servers/syncAgents/write|Создание агента синхронизации с указанными параметрами либо обновление свойств указанного агента.|
|/servers/usages/read|Возвращает квоту DTU сервера и текущие данные об использовании DTU для всех баз данных на сервере.|
|/servers/virtualNetworkRules/delete|Удаление существующего правила виртуальной сети.|
|/servers/virtualNetworkRules/read|Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети.|
|/servers/virtualNetworkRules/write|Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети.|
|/servers/write|Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера.|
|/unregister/action|Отмена регистрации подписки для поставщика ресурсов базы данных SQL Azure с возможностью создания баз данных Microsoft SQL.|
|/virtualClusters/read|Возвращение списка виртуальных кластеров или свойств указанного виртуального кластера.|
|/virtualClusters/write|Обновление тегов виртуального кластера.|

## <a name="microsoftstorage"></a>Microsoft.Storage;

| Операция | ОПИСАНИЕ |
|---|---|
|/checknameavailability/read|Проверяет, является ли имя учетной записи допустимым и неиспользуемым.|
|/locations/deleteVirtualNetworkOrSubnets/action|Уведомляет поставщик ресурсов Microsoft.Storage о том, что удаляется виртуальная сеть или подсеть.|
|/operations/read|Опрашивает состояние асинхронной операции.|
|/register/action|Регистрирует подписку для поставщика ресурсов службы хранилища и позволяет создавать учетные записи хранения.|
|/skus/read|Выводит список номеров SKU, поддерживаемых Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Снятие с контейнера больших двоичных объектов удержания по юридическим причинам.|
|/storageAccounts/blobServices/containers/delete|Возвращение результата удаления контейнера.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Удаление политики неизменяемости контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Расширение политики неизменяемости контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Блокировка политики неизменяемости контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Получение политики неизменяемости контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Размещение политики неизменяемости контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/containers/read|Возвращение контейнера или списка контейнеров.|
|/storageAccounts/blobServices/containers/setLegalHold/action|Постановка контейнера больших двоичных объектов на удержание по юридическим причинам.|
|/storageAccounts/blobServices/containers/write|Возвращение результата размещения или аренды контейнера больших двоичных объектов.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Получение списка определений метрик хранилища Майкрософт.|
|/storageAccounts/blobServices/read|Возвращение свойств или статистики службы BLOB-объектов.|
|/storageAccounts/blobServices/write|Возвращение результата настройки свойств службы BLOB-объектов.|
|/storageAccounts/delete|Удаляет существующую учетную запись хранения.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Получение списка определений метрик хранилища Майкрософт.|
|/storageAccounts/listAccountSas/action|Возвращает маркер SAS для указанной учетной записи хранения.|
|/storageAccounts/listkeys/action|Возвращает ключи доступа для указанной учетной записи хранения.|
|/storageAccounts/listServiceSas/action|Возвращение токена SAS службы для указанной учетной записи хранения.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Получение списка определений метрик хранилища Майкрософт.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Получение списка определений метрик хранилища Майкрософт.|
|/storageAccounts/queueServices/queues/delete|Возвращение результата удаления очереди.|
|/storageAccounts/queueServices/queues/read|Возвращение очереди или списка очередей.|
|/storageAccounts/queueServices/queues/write|Возвращение результата записи очереди.|
|/storageAccounts/queueServices/read|Возвращение свойств или статистики службы очередей.|
|/storageAccounts/queueServices/write|Возвращение результата настройки свойств службы очередей.|
|/storageAccounts/read|Возвращает список учетных записей хранения или свойства указанной учетной записи хранения.|
|/storageAccounts/regeneratekey/action|Повторно создает ключи доступа для указанной учетной записи хранения.|
|/storageAccounts/services/diagnosticSettings/write|Создает или обновляет параметры диагностики учетной записи хранения.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Получение параметра диагностики для ресурса.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Создание или обновление параметра диагностики для ресурса.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Получение списка определений метрик хранилища Майкрософт.|
|/storageAccounts/write|Создает новую учетную запись хранения с указанными параметрами, обновляет свойства или теги указанной существующей учетной записи хранения или добавляет в нее личный домен.|
|/usages/read|Возвращает предельное и текущее число используемых ресурсов для указанной подписки.|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Операция | ОПИСАНИЕ |
|---|---|
|/storageSyncServices/delete|Удаление служб синхронизации хранилища.|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для служб синхронизации хранилища.|
|/storageSyncServices/read|Чтение служб синхронизации хранилища.|
|/storageSyncServices/registeredServers/delete|Удаление любого зарегистрированного сервера.|
|/storageSyncServices/registeredServers/read|Чтение зарегистрированного сервера.|
|/storageSyncServices/registeredServers/write|Создание или обновление любого зарегистрированного сервера.|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Удаление конечных точек облака.|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|API расположения для асинхронных вызовов резервного копирования.|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Это действие следует вызывать после резервного копирования.|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Это действие следует вызывать после восстановления.|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Это действие следует вызывать до резервного копирования.|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Это действие следует вызывать до восстановления.|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Чтение конечных точек облака.|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Восстановление пакетов пульса.|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Создание или обновление конечных точек облака.|
|/storageSyncServices/syncGroups/delete|Удаление любой группы синхронизации.|
|/storageSyncServices/syncGroups/read|Чтение всех групп синхронизации.|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Удаление любой конечной точки сервера.|
|/storageSyncServices/syncGroups/serverEndpoints/read|Чтение конечных точек сервера.|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Действие для отзыва файлов на сервере.|
|/storageSyncServices/syncGroups/serverEndpoints/write|Создание или обновление конечных точек сервера.|
|/storageSyncServices/syncGroups/write|Создание или обновление групп синхронизации.|
|/storageSyncServices/write|Создание или обновление служб синхронизации хранилища.|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Операция | ОПИСАНИЕ |
|---|---|
|/managers/accessControlRecords/delete|Удаляет записи контроля доступа.|
|/managers/accessControlRecords/read|Выводит список записей контроля доступа или возвращает их.|
|/managers/accessControlRecords/write|Создает или обновляет записи контроля доступа.|
|/managers/alerts/read|Выводит список оповещений или возвращает их.|
|/managers/bandwidthSettings/delete|Удаляет существующие параметры пропускной способности (только для серии 8000).|
|/managers/bandwidthSettings/read|Выводит список параметров пропускной способности (только для серии 8000).|
|/managers/bandwidthSettings/write|Создает или обновляет параметры пропускной способности (только для серии 8000).|
|/Managers/certificates/write|Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища.|
|/managers/clearAlerts/action|Очищает все оповещения, связанные с диспетчером устройств.|
|/managers/cloudApplianceConfigurations/read|Выводит список конфигураций, поддерживаемых облачным устройством.|
|/managers/configureDevice/action|Настраивает устройство.|
|/managers/delete|Удаляет диспетчеры устройств.|
|/Managers/delete|Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище".|
|/managers/devices/alertSettings/read|Возвращает параметры оповещения или выводит их список.|
|/managers/devices/alertSettings/write|Создает или обновляет параметры оповещения.|
|/managers/devices/backupPolicies/backup/action|Выполняет ручную архивацию всех томов, защищенных с помощью политики архивации, для создания их резервной копии по запросу.|
|/managers/devices/backupPolicies/delete|Удаляет существующие политики архивации (только для серии 8000).|
|/managers/devices/backupPolicies/read|Выводит список политик архивации (только для серии 8000).|
|/managers/devices/backupPolicies/schedules/delete|Удаляет существующие расписания.|
|/managers/devices/backupPolicies/schedules/read|Выводит список расписаний.|
|/managers/devices/backupPolicies/schedules/write|Создает или обновляет расписания.|
|/managers/devices/backupPolicies/write|Создает или обновляет политики архивации (только для серии 8000).|
|/managers/devices/backups/delete|Удаляет набор архивации.|
|/managers/devices/backups/elements/clone/action|Клонирует общедоступный ресурс или том с помощью элемента архива.|
|/managers/devices/backups/read|Возвращает набор архивации или выводит его.|
|/managers/devices/backups/restore/action|Восстанавливает все тома из набора архивации.|
|/managers/devices/backupScheduleGroups/delete|Удаляет группы расписаний архивации.|
|/managers/devices/backupScheduleGroups/read|Выводит список групп расписаний архивации или возвращает их.|
|/managers/devices/backupScheduleGroups/write|Создает или обновляет группы расписаний архивации.|
|/managers/devices/chapSettings/delete|Удаляет параметры CHAP.|
|/managers/devices/chapSettings/read|Возвращает параметры CHAP или выводит их список.|
|/managers/devices/chapSettings/write|Создает или обновляет параметры CHAP.|
|/managers/devices/deactivate/action|Деактивирует устройство.|
|/managers/devices/delete|Удаляет устройства.|
|/managers/devices/download/action|Скачивает обновления для устройства.|
|/managers/devices/failover/action|Выполняет отработку отказа устройства.|
|/managers/devices/fileservers/backup/action|Архивирует файловый сервер.|
|/managers/devices/fileservers/delete|Удаляет файловые серверы.|
|/managers/devices/fileservers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/fileservers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/fileservers/read|Выводит список файловых серверов или возвращает их.|
|/managers/devices/fileservers/shares/delete|Удаляет общедоступные ресурсы.|
|/managers/devices/fileservers/shares/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/fileservers/shares/read|Выводит список общедоступных ресурсов или возвращает их.|
|/managers/devices/fileservers/shares/write|Создает или обновляет общедоступные ресурсы.|
|/managers/devices/fileservers/write|Создает или обновляет файловые серверы.|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Изменяет состояние питания контроллера для групп компонентов оборудования.|
|/managers/devices/hardwareComponentGroups/read|Выводит список групп компонентов оборудования.|
|/managers/devices/install/action|Устанавливает обновления на устройство.|
|/managers/devices/installUpdates/action|Устанавливает обновления на устройства.|
|/managers/devices/iscsiservers/backup/action|Архивирует сервер iSCSI.|
|/managers/devices/iscsiservers/delete|Удаляет серверы iSCSI.|
|/managers/devices/iscsiservers/disks/delete|Удаляет диски.|
|/managers/devices/iscsiservers/disks/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/iscsiservers/disks/read|Выводит список дисков или возвращает их.|
|/managers/devices/iscsiservers/disks/write|Создает или обновляет диски.|
|/managers/devices/iscsiservers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/iscsiservers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/iscsiservers/read|Выводит список серверов iSCSI или возвращает их.|
|/managers/devices/iscsiservers/write|Создает или обновляет серверы iSCSI.|
|/managers/devices/jobs/cancel/action|Отменяет выполнение задания.|
|/managers/devices/jobs/read|Выводит список заданий или возвращает их.|
|/managers/devices/listFailoverSets/action|Выводит список групп перехода на другой ресурс для существующего устройства.|
|/managers/devices/listFailoverTargets/action|Выводит список целей отработки отказа для устройств.|
|/managers/devices/metrics/read|Выводит список метрик или возвращает их.|
|/managers/devices/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Подтверждает успешный перенос и фиксирует его.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Извлекает состояние подтверждения переноса.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Извлекает состояние задания оценки переноса.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Извлекает состояние переноса.|
|/managers/devices/migrationSourceConfigurations/import/action|Импортирует конфигурации источников для переноса.|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Начинает перенос с использованием конфигураций источников.|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Запускает задание для оценки длительности процесса переноса.|
|/managers/devices/networkSettings/read|Возвращает параметры сети или выводит их список.|
|/managers/devices/networkSettings/write|Создает или обновляет параметры сети.|
|/managers/devices/publicEncryptionKey/action|Отображает открытый ключ шифрования для диспетчера устройств.|
|/managers/devices/publishSupportPackage/action|Публикует пакет поддержки устройства для устранения неполадок службой поддержки Майкрософт.|
|/managers/devices/read|Выводит список устройств или возвращает их.|
|/managers/devices/scanForUpdates/action|Выполняет поиск обновлений в устройстве.|
|/managers/devices/securitySettings/read|Выводит список параметров безопасности.|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Синхронизирует сертификат удаленного управления для устройства.|
|/managers/devices/securitySettings/update/action|Обновляет параметры безопасности.|
|/managers/devices/securitySettings/write|Создает или обновляет параметры безопасности.|
|/managers/devices/sendTestAlertEmail/action|Отправляет тестовое сообщение оповещения настроенным получателям электронной почты.|
|/managers/devices/timeSettings/read|Возвращает параметры времени или выводит их список.|
|/managers/devices/timeSettings/write|Создает или обновляет параметры времени.|
|/managers/devices/updateSummary/read|Возвращает сводку обновлений или выводит их список.|
|/managers/devices/volumeContainers/delete|Удаляет существующие контейнеры томов (только для серии 8000).|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Выводит список ключей шифрования контейнеров томов.|
|/managers/devices/volumeContainers/metrics/read|Выводит список метрик.|
|/managers/devices/volumeContainers/metricsDefinitions/read|Выводит список определений метрик.|
|/managers/devices/volumeContainers/read|Выводит список контейнеров томов (только для серии 8000).|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Переключает ключи шифрования контейнеров томов.|
|/managers/devices/volumeContainers/volumes/delete|Удаляет существующие тома.|
|/managers/devices/volumeContainers/volumes/metrics/read|Выводит список метрик.|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Выводит список определений метрик.|
|/managers/devices/volumeContainers/volumes/read|Выводит список томов.|
|/managers/devices/volumeContainers/volumes/write|Создает или обновляет тома.|
|/managers/devices/volumeContainers/write|Создает или обновляет контейнеры томов (только для серии 8000).|
|/managers/devices/write|Создает или обновляет устройства.|
|/managers/encryptionSettings/read|Возвращает параметры шифрования или выводит их список.|
|/Managers/extendedInformation/delete|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Managers/extendedInformation/read|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/Managers/extendedInformation/write|Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище".|
|/managers/getActivationKey/action|Возвращает ключ активации для диспетчера устройств.|
|/managers/getEncryptionKey/action|Возвращает ключ шифрования для диспетчера устройств.|
|/managers/listActivationKey/action|Возвращает ключ активации диспетчера устройств StorSimple.|
|/managers/listPrivateEncryptionKey/action|Возвращает ключ шифрования для диспетчера устройств StorSimple.|
|/managers/listPublicEncryptionKey/action|Выводит список открытых ключей шифрования для диспетчера устройств StorSimple.|
|/managers/metrics/read|Выводит список метрик или возвращает их.|
|/managers/metricsDefinitions/read|Выводит список определений метрик или возвращает их.|
|/managers/provisionCloudAppliance/action|Создает облачное устройство.|
|/managers/read|Выводит список диспетчеров устройств или возвращает их.|
|/Managers/read|Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище".|
|/managers/regenarateRegistationCertificate/action|Повторно создает ключ шифрования для диспетчеров устройств.|
|/managers/regenerateActivationKey/action|Повторно создает ключ активации для диспетчера устройств.|
|/managers/storageAccountCredentials/delete|Удаляет данные учетной записи хранения.|
|/managers/storageAccountCredentials/listAccessKey/action|Выводит список ключей доступа для данных учетной записи хранения.|
|/managers/storageAccountCredentials/read|Выводит список данных учетной записи хранения или возвращает их.|
|/managers/storageAccountCredentials/write|Создает или обновляет данные учетной записи хранения.|
|/managers/storageDomains/delete|Удаляет домены хранилища.|
|/managers/storageDomains/read|Выводит список доменов хранилища или возвращает их.|
|/managers/storageDomains/write|Создает или обновляет домены хранилища.|
|/managers/write|Создает или обновляет диспетчеры устройств.|
|/Managers/write|Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище".|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Операция | ОПИСАНИЕ |
|---|---|
|/locations/quotas/Read|Чтение квоты подписки Stream Analytics.|
|/operations/Read|Чтение операций Stream Analytics.|
|/Register/action|Регистрация подписки в поставщике ресурсов Stream Analytics.|
|/streamingjobs/Delete|Удаляет задание Stream Analytics.|
|/streamingjobs/functions/Delete|Удаление функции задания Stream Analytics.|
|/streamingjobs/functions/operationresults/Read|Чтение результатов операции функции задания Stream Analytics.|
|/streamingjobs/functions/Read|Чтение функции задания Stream Analytics.|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Получение определения по умолчанию для функции задания Stream Analytics.|
|/streamingjobs/functions/Test/action|Тестирование функции задания Stream Analytics.|
|/streamingjobs/functions/Write|Запись функции задания Stream Analytics.|
|/streamingjobs/inputs/Delete|Удаляет входные данные задания Stream Analytics.|
|/streamingjobs/inputs/operationresults/Read|Чтение результатов операции для получения входных данных задания Stream Analytics.|
|/streamingjobs/inputs/Read|Считывает входные данные задания Stream Analytics.|
|/streamingjobs/inputs/Sample/action|Пример входных данных задания Stream Analytics.|
|/streamingjobs/inputs/Test/action|Тестирование входных данных задания Stream Analytics.|
|/streamingjobs/inputs/Write|Записывает входные данные задания Stream Analytics.|
|/streamingjobs/metricdefinitions/Read|Чтение определений метрик.|
|/streamingjobs/operationresults/Read|Чтение результатов операции задания Stream Analytics.|
|/streamingjobs/outputs/Delete|Удаляет выходные данные задания Stream Analytics.|
|/streamingjobs/outputs/operationresults/Read|Чтение результатов операции для получения выходных данных задания Stream Analytics.|
|/streamingjobs/outputs/Read|Считывает выходные данные задания Stream Analytics.|
|/streamingjobs/outputs/Test/action|Тестирование выходных данных задания Stream Analytics.|
|/streamingjobs/outputs/Write|Записывает выходные данные задания Stream Analytics.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Считывает параметр диагностики.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Записывает параметр диагностики.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Возвращает доступные журналы для заданий потоковой передачи.|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Возвращает доступные метрики для заданий потоковой передачи.|
|/streamingjobs/Read|Считывает задание Stream Analytics.|
|/streamingjobs/Start/action|Запускает задание Stream Analytics.|
|/streamingjobs/Stop/action|Останавливает задание Stream Analytics.|
|/streamingjobs/transformations/Delete|Удаляет преобразование задания Stream Analytics.|
|/streamingjobs/transformations/Read|Считывает преобразование задания Stream Analytics.|
|/streamingjobs/transformations/Write|Записывает преобразование задания Stream Analytics.|
|/streamingjobs/Write|Записывает задание Stream Analytics.|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Операция | ОПИСАНИЕ |
|---|---|
|/SubscriptionDefinitions/read|Получение определения подписки Azure в группе управления.|
|/SubscriptionDefinitions/write|Создание определения подписки Azure.|

## <a name="microsoftsupport"></a>Microsoft.Support

| Операция | ОПИСАНИЕ |
|---|---|
|/register/action|Выполняет регистрацию в поставщике ресурсов поддержки.|
|/supportTickets/read|Возвращает подробные сведения о запросе в службу поддержки (включая состояние, серьезность, контактную информацию и сообщения) или возвращает список запросов в службу поддержки в подписках.|
|/supportTickets/write|Создает или обновляет запрос в службу поддержки. Можно создавать запросы в службу поддержки по техническим проблемам, а также проблемам, связанным с выставлением счетов, квотами или управлением подписками. Вы можете изменить уровень серьезности, контактную информацию и сообщения для существующих запросов в службу поддержки.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Операция | ОПИСАНИЕ |
|---|---|
|/environments/accesspolicies/delete|Удаление политики доступа.|
|/environments/accesspolicies/read|Получение свойств политики доступа.|
|/environments/accesspolicies/write|Создание новой политики доступа для среды или обновление существующей политики.|
|/environments/delete|Удаление среды.|
|/environments/eventsources/delete|Удаление источника события.|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для источников событий.|
|/environments/eventsources/read|Получение свойств источника событий.|
|/environments/eventsources/write|Создание нового источника событий для среды или обновление существующего.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Получение доступных метрик для сред.|
|/environments/read|Получение свойств среды.|
|/environments/referencedatasets/delete|Удаление эталонного набора данных.|
|/environments/referencedatasets/read|Получение свойств эталонного набора данных.|
|/environments/referencedatasets/write|Создание нового эталонного набора данных для среды или обновление существующего.|
|/environments/status/read|Получение состояния среды и связанных с ней операций, таких как получение входящего трафика.|
|/environments/write|Создание новой среды или обновление существующей.|
|/register/action|Регистрация подписки для поставщика ресурсов службы "Аналитика временных рядов" с возможностью создания сред данной службы.|

## <a name="microsoftweb"></a>microsoft.web

| Операция | ОПИСАНИЕ |
|---|---|
|/apimanagementaccounts/apiacls/read|Получение списков ACL для учетных записей управления API.|
|/apimanagementaccounts/apis/apiacls/delete|Удаление списков ACL для API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/apiacls/read|Получение списков ACL для API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/apiacls/write|Обновление списков ACL для API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connectionacls/read|Получение списков ACL для подключения API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Подтверждение кода согласия на подключение для API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Удаление списков ACL для подключения API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Получение списков ACL для подключения API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Обновление списков ACL для подключения API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/delete|Удаление подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Получение ссылок на согласие для подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Получение ключей подключения для подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Получение списка секретов для подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/read|Получение подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/connections/write|Обновление подключений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/delete|Удаление API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Удаление локализованных определений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Получение локализованных определений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Обновление локализованных определений API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/read|Получение API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/apis/write|Обновление API-интерфейсов учетных записей управления API.|
|/apimanagementaccounts/connectionacls/read|Получение списков ACL для подключения учетных записей управления API.|
|/availablestacks/read|Возвращает доступные стеки.|
|/billingmeters/read|Получение списка счетчиков цен.|
|/certificates/Delete|Удаляет существующий сертификат.|
|/certificates/Read|Возвращает список сертификатов.|
|/certificates/Write|Добавляет новый сертификат или обновляет существующий.|
|/checknameavailability/read|Проверяет, доступно ли имя ресурса.|
|/classicmobileservices/read|Возвращает классические мобильные службы.|
|/connectionGateways/Delete|Удаляет шлюз подключения.|
|/connectionGateways/Join/Action|Присоединяет шлюз подключения.|
|/connectiongateways/liststatus/action|Получение списка состояний шлюзов подключения.|
|/connectionGateways/ListStatus/Action|Получение состояния шлюза подключения.|
|/connectionGateways/Move/Action|Перемещение шлюза подключения.|
|/connectionGateways/Read|Возвращает список шлюзов подключений.|
|/connectionGateways/Write|Создает или обновляет шлюз для подключения.|
|/connections/confirmconsentcode/action|Подтверждает код согласия на подключение.|
|/connections/Delete|Удаляет подключение.|
|/connections/Join/Action|Присоединяет подключение.|
|/connections/listconsentlinks/action|Выводит список ссылок на согласие для подключений.|
|/connections/Move/Action|Перемещение подключения.|
|/connections/Read|Возвращает список подключений.|
|/connections/Write|Создает или обновляет подключение.|
|/customApis/Delete|Удаление пользовательского API.|
|/customApis/extractApiDefinitionFromWsdl/Action|Извлечение определения API из формата WSDL.|
|/customApis/Join/Action|Присоединение пользовательского API.|
|/customApis/listWsdlInterfaces/Action|Получение списка интерфейсов языка WSDL для пользовательского API.|
|/customApis/Move/Action|Перемещение пользовательского API.|
|/customApis/Read|Получение списка пользовательских API.|
|/customApis/Write|Создание или обновление пользовательского API.|
|/deploymentlocations/read|Возвращает расположения развертываний.|
|/geoRegions/Read|Возвращает список географических регионов.|
|/hostingenvironments/capacities/read|Возвращает емкости сред внешнего размещения.|
|/hostingEnvironments/Delete|Удаляет среду службы приложений.|
|/hostingenvironments/diagnostics/read|Возвращает данные диагностики сред внешнего размещения.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Получение конечных точек сети всех входящих зависимостей.|
|/hostingenvironments/metricdefinitions/read|Возвращает определения метрик сред внешнего размещения.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Возвращает определения метрик пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/multirolepools/metrics/read|Возвращает метрики пулов множественных ролей в средах внешнего размещения.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Получение доступных метрик для множественных ролей среды службы приложений.|
|/hostingEnvironments/multiRolePools/Read|Возвращает свойства интерфейсного пула в среде службы приложений.|
|/hostingenvironments/multirolepools/skus/read|Возвращает номера SKU пулов множественных ролей в средах внешнего размещения.|
|/hostingenvironments/multirolepools/usages/read|Возвращает данные об использовании пулов множественных ролей в средах внешнего размещения.|
|/hostingEnvironments/multiRolePools/Write|Создает новый интерфейсный пул в среде службы приложений или обновляет существующий.|
|/hostingenvironments/operations/read|Возвращает операции сред внешнего размещения.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Получение конечных точек сети всех исходящих зависимостей.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/hostingEnvironments/Read|Возвращает свойства среды службы приложений.|
|/hostingEnvironments/reboot/Action|Перезагружает все компьютеры в среде службы приложений.|
|/hostingenvironments/resume/action|Возобновляет работу сред внешнего размещения.|
|/hostingenvironments/serverfarms/read|Возвращает планы службы приложений в средах внешнего размещения.|
|/hostingenvironments/sites/read|Возвращает веб-приложения в средах внешнего размещения.|
|/hostingenvironments/suspend/action|Приостанавливает работу сред внешнего размещения.|
|/hostingenvironments/usages/read|Возвращает данные об использовании сред внешнего размещения.|
|/hostingenvironments/workerpools/metricdefinitions/read|Возвращает определения метрик рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/workerpools/metrics/read|Возвращает метрики рабочих пулов в средах внешнего размещения.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Получение доступных метрик для пула рабочих ролей среды службы приложений.|
|/hostingEnvironments/workerPools/Read|Возвращает свойства рабочего пула в среде службы приложений.|
|/hostingenvironments/workerpools/skus/read|Возвращает номера SKU рабочих пулов в средах внешнего размещения.|
|/hostingenvironments/workerpools/usages/read|Возвращает данные об использовании рабочих пулов в средах внешнего размещения.|
|/hostingEnvironments/workerPools/Write|Создает новый рабочий пул в среде службы приложений или обновляет существующий.|
|/hostingEnvironments/Write|Создает новую среду службы приложений или обновляет существующую.|
|/ishostingenvironmentnameavailable/read|Проверяет, доступно ли имя среды внешнего размещения.|
|/ishostnameavailable/read|Проверяет, доступно ли имя узла.|
|/isusernameavailable/read|Проверяет, доступно ли имя пользователя.|
|/listSitesAssignedToHostName/Read|Возвращает имена сайтов, назначенные имени узла.|
|/locations/apioperations/read|Возвращает операции API расположений.|
|/locations/connectiongatewayinstallations/read|Возвращает данные об установленных шлюзах подключений в расположениях.|
|/locations/extractapidefinitionfromwsdl/action|Извлечение определения API для расположений из синтаксиса WSDL.|
|/locations/listwsdlinterfaces/action|Получение списка интерфейсов языка WSDL для расположений.|
|/locations/managedapis/apioperations/read|Получение операций с управляемыми API для расположений.|
|/locations/managedapis/Join/Action|Присоединение управляемого API.|
|/locations/managedapis/read|Возвращает расположения управляемых интерфейсов API.|
|/operations/read|Возвращает операции.|
|/publishingusers/read|Возвращает публикующих пользователей.|
|/publishingusers/write|Обновляет публикующих пользователей.|
|/recommendations/Read|Возвращает список рекомендаций для подписок.|
|/register/action|Регистрирует поставщик ресурсов Microsoft.Web для подписки.|
|/resourcehealthmetadata/read|Получение метаданных работоспособности ресурса.|
|/serverfarms/capabilities/read|Возвращает возможности планов службы приложений.|
|/serverfarms/Delete|Удаление существующего плана службы приложений|
|/serverfarms/firstpartyapps/settings/delete|Удаляет параметры основных приложений в планах службы приложений.|
|/serverfarms/firstpartyapps/settings/read|Возвращает параметры основных приложений в планах службы приложений.|
|/serverfarms/firstpartyapps/settings/write|Обновляет параметры основных приложений в планах службы приложений.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Удаление ретрансляторов в пространствах имен гибридных подключений для плана службы приложений.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Получение ретрансляторов в пространствах имен гибридных подключений для плана службы приложений.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Возвращает веб-приложения ретрансляторов в пространствах имен гибридных подключений для плана службы приложений.|
|/serverfarms/hybridconnectionplanlimits/read|Возвращает ограничения плана гибридных подключений для плана службы приложений.|
|/serverfarms/hybridconnectionrelays/read|Возвращает ретрансляторы гибридных подключений для планов службы приложений.|
|/serverfarms/metricdefinitions/read|Возвращает определения метрик планов службы приложений.|
|/serverfarms/metrics/read|Возвращает метрики планов службы приложений.|
|/serverfarms/operationresults/read|Возвращает результаты операций планов службы приложений.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Получение доступных метрик для плана службы приложений.|
|/serverfarms/Read|Возвращает свойства плана службы приложений.|
|/serverfarms/restartSites/Action|Перезапустите все веб-приложения в плане службы приложений.|
|/serverfarms/sites/read|Возвращает веб-приложения в планах службы приложений.|
|/serverfarms/skus/read|Возвращает номера SKU в планах службы приложений.|
|/serverfarms/usages/read|Возвращает данные об использовании для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/read|Возвращает подключения виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/delete|Удаляет маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/read|Возвращает маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/virtualnetworkconnections/routes/write|Обновляет маршруты подключений виртуальной сети для планов службы приложений.|
|/serverfarms/workers/reboot/action|Перезапускает рабочие роли планов службы приложений.|
|/serverfarms/Write|Создает новый план службы приложений или обновляет существующий.|
|/sites/analyzecustomhostname/read|Анализирует пользовательское имя узла.|
|/sites/applySlotConfig/Action|Применяет конфигурацию слота веб-приложения из целевого слота к текущему веб-приложению.|
|/sites/backup/Action|Создает резервную копию веб-приложения.|
|/sites/backup/read|Возвращает резервную копию веб-приложений.|
|/sites/backup/write|Обновляет резервную копию веб-приложений.|
|/sites/backups/delete|Удаление резервных копий веб-приложений.|
|/sites/backups/list/action|Выводит список резервных копий веб-приложений.|
|/sites/backups/Read|Возвращает свойства резервной копии веб-приложения.|
|/sites/backups/restore/action|Восстанавливает резервные копии веб-приложений.|
|/sites/config/delete|Удаляет конфигурацию веб-приложений.|
|/sites/config/list/Action|Отображает влияющие на безопасность параметры веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения.|
|/sites/config/Read|Возвращает параметры конфигурации веб-приложения.|
|/sites/config/Write|Обновляет параметры конфигурации веб-приложения.|
|/sites/continuouswebjobs/delete|Удаляет непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/read|Возвращает непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/start/action|Запускает непрерывные веб-задания веб-приложений.|
|/sites/continuouswebjobs/stop/action|Останавливает непрерывные веб-задания веб-приложений.|
|/sites/Delete|Удаление существующего веб-приложения|
|/sites/deployments/delete|Удаляет развертывания веб-приложений.|
|/sites/deployments/log/read|Возвращает журнал развертываний веб-приложений.|
|/sites/deployments/read|Возвращает развертывания веб-приложений.|
|/sites/deployments/write|Обновляет развертывания веб-приложений.|
|/sites/diagnostics/analyses/execute/Action|Запуск анализа данных диагностики веб-приложений.|
|/sites/diagnostics/analyses/read|Получение анализа данных диагностики веб-приложений.|
|/sites/diagnostics/aspnetcore/read|Получение данных диагностики веб-приложений для приложения ASP.NET Core.|
|/sites/diagnostics/autoheal/read|Получение данных диагностики о функции Autoheal для веб-приложений.|
|/sites/diagnostics/deployment/read|Получение данных диагностики о развертывании для веб-приложений.|
|/sites/diagnostics/deployments/read|Получение данных диагностики о развертываниях для веб-приложений.|
|/sites/diagnostics/detectors/execute/Action|Запуск средства обнаружения диагностики веб-приложений.|
|/sites/diagnostics/detectors/read|Получение данных средства обнаружения диагностики веб-приложений.|
|/sites/diagnostics/failedrequestsperuri/read|Получение данных диагностики веб-приложений о неудачных запросах для каждого URI.|
|/sites/diagnostics/frebanalysis/read|Возвращает данные диагностики об анализе FREB для веб-приложений.|
|/sites/diagnostics/loganalyzer/read|Получение данных диагностики веб-приложений об анализаторе журналов.|
|/sites/diagnostics/read|Получение категорий данных диагностики веб-приложений.|
|/sites/diagnostics/runtimeavailability/read|Возвращает данные диагностики о доступности среды выполнения для веб-приложений.|
|/sites/diagnostics/servicehealth/read|Возвращает данные диагностики о работоспособности служб для веб-приложений.|
|/sites/diagnostics/sitecpuanalysis/read|Получение данных диагностики веб-приложений об анализе ЦП для сайта.|
|/sites/diagnostics/sitecrashes/read|Получение данных диагностики веб-приложений о сбоях сайта.|
|/sites/diagnostics/sitelatency/read|Получение данных диагностики веб-приложений о задержках сайта.|
|/sites/diagnostics/sitememoryanalysis/read|Получение данных диагностики веб-приложений об анализе памяти для сайта.|
|/sites/diagnostics/siterestartsettingupdate/read|Получение данных диагностики веб-приложений об обновлении параметра перезапуска сайта.|
|/sites/diagnostics/siterestartuserinitiated/read|Получение данных диагностики веб-приложений об инициированном пользователем перезапуске сайта.|
|/sites/diagnostics/siteswap/read|Получение данных диагностики веб-приложений о переключении сайта.|
|/sites/diagnostics/threadcount/read|Получение данных диагностики веб-приложений о количестве потоков.|
|/sites/diagnostics/workeravailability/read|Возвращает данные диагностики о доступности рабочих процессов для веб-приложений.|
|/sites/diagnostics/workerprocessrecycle/read|Возвращает данные диагностики перезапуска рабочих процессов для веб-приложений.|
|/sites/domainownershipidentifiers/read|Возвращает идентификаторы владения доменом веб-приложений.|
|/sites/domainownershipidentifiers/write|Обновляет идентификаторы владения доменом веб-приложений.|
|/sites/functions/action|Выполняет функции веб-приложений.|
|/sites/functions/delete|Удаляет функции веб-приложений.|
|/sites/functions/listsecrets/action|Выводит список секретов для функций веб-приложений.|
|/sites/functions/masterkey/read|Получение главного ключа функций веб-приложений.|
|/sites/functions/read|Возвращает функции веб-приложений.|
|/sites/functions/token/read|Получение токена функций веб-приложений.|
|/sites/functions/write|Обновляет функции веб-приложений.|
|/sites/hostnamebindings/delete|Удаляет привязки имен узлов для веб-приложений.|
|/sites/hostnamebindings/read|Возвращает привязки имен узлов для веб-приложений.|
|/sites/hostnamebindings/write|Обновляет привязки имен узлов для веб-приложений.|
|/sites/hybridconnection/delete|Удаляет гибридное подключение для веб-приложений.|
|/sites/hybridconnection/read|Возвращает гибридное подключение для веб-приложений.|
|/sites/hybridconnection/write|Обновляет гибридное подключение для веб-приложений.|
|/sites/hybridconnectionnamespaces/relays/delete|Удаление ретрансляторов в пространствах имен гибридных подключений для веб-приложений.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Получение списка ключей ретрансляторов в пространствах имен гибридных подключений для веб-приложений.|
|/sites/hybridconnectionnamespaces/relays/read|Получение ретрансляторов в пространствах имен гибридных подключений для веб-приложений.|
|/sites/hybridconnectionnamespaces/relays/write|Обновление ретрансляторов в пространствах имен гибридных подключений для веб-приложений.|
|/sites/hybridconnectionrelays/read|Возвращает ретрансляторы гибридных подключений для веб-приложений.|
|/sites/instances/deployments/delete|Удаление развертываний экземпляров веб-приложений.|
|/sites/instances/deployments/read|Возвращает развертывания экземпляров веб-приложений.|
|/sites/instances/extensions/log/read|Получение журнала расширений экземпляров веб-приложений.|
|/sites/instances/extensions/read|Получение расширений экземпляров веб-приложений.|
|/sites/instances/processes/delete|Удаляет процессы экземпляров веб-приложений.|
|/sites/instances/processes/read|Возвращает процессы экземпляров веб-приложений.|
|/sites/instances/read|Возвращает экземпляры веб-приложений.|
|/sites/listsyncfunctiontriggerstatus/action|Отображает состояние триггера функции синхронизации для веб-приложений.|
|/sites/metricdefinitions/read|Возвращает определения метрик веб-приложений.|
|/sites/metrics/read|Возвращает метрики веб-приложений.|
|/sites/metricsdefinitions/read|Получение определений метрик веб-приложений.|
|/sites/migratemysql/action|Перенос веб-приложений MySQL.|
|/sites/migratemysql/read|Получение сведений о переносе веб-приложений MySQL.|
|/sites/networktrace/action|Трассирует сеть для веб-приложений.|
|/sites/newpassword/action|Задает новый пароль для веб-приложений.|
|/sites/operationresults/read|Возвращает результаты операций веб-приложений.|
|/sites/operations/read|Получение операций веб-приложений.|
|/sites/perfcounters/read|Возвращает счетчики производительности веб-приложений.|
|/sites/premieraddons/delete|Удаляет надстройки Premier для веб-приложений.|
|/sites/premieraddons/read|Возвращает надстройки Premier для веб-приложений.|
|/sites/premieraddons/write|Обновляет надстройки Premier для веб-приложений.|
|/sites/processes/read|Получение процессов веб-приложений.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Получение доступных метрик для веб-приложений.|
|/sites/publiccertificates/delete|Удаление открытых сертификатов веб-приложений.|
|/sites/publiccertificates/read|Получение открытых сертификатов веб-приложений.|
|/sites/publiccertificates/write|Обновление открытых сертификатов веб-приложений.|
|/sites/publish/Action|Публикует веб-приложение.|
|/sites/publishxml/Action|Возвращает XML-файл профиля публикации для веб-приложения.|
|/sites/publishxml/read|Возвращает XML-файл профиля публикации для веб-приложений.|
|/sites/Read|Возвращает свойства веб-приложения.|
|/sites/recommendationhistory/read|Возвращает журнал рекомендаций для веб-приложения.|
|/sites/recommendations/disable/action|Отключает рекомендации для веб-приложений.|
|/sites/recommendations/Read|Возвращает список рекомендаций для веб-приложения.|
|/sites/recover/action|Восстановление веб-приложений.|
|/sites/resetSlotConfig/Action|Сбрасывает конфигурацию веб-приложения.|
|/sites/resourcehealthmetadata/read|Получение метаданных работоспособности ресурсов веб-приложений.|
|/sites/restart/Action|Перезапускает веб-приложение.|
|/sites/restore/read|Возвращает данные о восстановлении веб-приложений.|
|/sites/restore/write|Восстановление веб-приложений.|
|/sites/siteextensions/delete|Удаление расширений сайта веб-приложений.|
|/sites/siteextensions/read|Получение расширений сайта веб-приложений.|
|/sites/siteextensions/write|Обновление расширений сайта веб-приложений.|
|/sites/slots/analyzecustomhostname/read|Возвращает результаты анализа пользовательского имени узла для слотов веб-приложений.|
|/sites/slots/applySlotConfig/Action|Применяет конфигурацию слота веб-приложения из целевого слота к текущему слоту.|
|/sites/slots/backup/Action|Создает резервную копию слота веб-приложения.|
|/sites/slots/backup/read|Получение резервной копии слотов для веб-приложений.|
|/sites/slots/backup/write|Обновляет резервную копию слотов веб-приложений.|
|/sites/slots/backups/delete|Удаление резервной копии слотов для веб-приложений.|
|/sites/slots/backups/list/action|Выводит список резервных копий слотов веб-приложений.|
|/sites/slots/backups/Read|Возвращает свойства резервной копии слотов веб-приложения.|
|/sites/slots/backups/restore/action|Восстанавливает резервные копии слотов веб-приложений.|
|/sites/slots/config/delete|Удаляет конфигурацию слотов веб-приложений.|
|/sites/slots/config/list/Action|Отображает влияющие на безопасность параметры слота веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения.|
|/sites/slots/config/Read|Возвращает параметры конфигурации слота веб-приложения.|
|/sites/slots/config/Write|Обновляет параметры конфигурации слота веб-приложения.|
|/sites/slots/continuouswebjobs/delete|Удаляет непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/read|Возвращает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/start/action|Запускает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/continuouswebjobs/stop/action|Останавливает непрерывные веб-задания слотов веб-приложений.|
|/sites/slots/Delete|Удаляет существующий слот веб-приложения.|
|/sites/slots/deployments/delete|Удаляет развертывания слотов веб-приложений.|
|/sites/slots/deployments/log/read|Возвращает журнал развертываний слотов веб-приложений.|
|/sites/slots/deployments/read|Возвращает развертывания слотов веб-приложений.|
|/sites/slots/deployments/write|Обновляет развертывания слотов веб-приложений.|
|/sites/slots/diagnostics/analyses/execute/Action|Запуск анализа данных диагностики слотов для веб-приложений.|
|/sites/slots/diagnostics/analyses/read|Получение анализа данных диагностики слотов для веб-приложений.|
|/sites/slots/diagnostics/aspnetcore/read|Получение данных диагностики слотов для веб-приложений для приложения ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Получение данных диагностики слотов о функции Autoheal для веб-приложений.|
|/sites/slots/diagnostics/deployment/read|Получение данных диагностики слотов о развертывании для веб-приложений.|
|/sites/slots/diagnostics/deployments/read|Получение данных диагностики слотов о развертываниях для веб-приложений.|
|/sites/slots/diagnostics/detectors/execute/Action|Запуск средства обнаружения данных диагностики слотов для веб-приложений.|
|/sites/slots/diagnostics/detectors/read|Получение сведений средства обнаружения данных диагностики слотов для веб-приложений.|
|/sites/slots/diagnostics/frebanalysis/read|Возвращение данных диагностики об анализе FREB для слотов веб-приложений.|
|/sites/slots/diagnostics/loganalyzer/read|Получение данных диагностики слотов для веб-приложений об анализаторе журналов.|
|/sites/slots/diagnostics/read|Получение данных диагностики слотов для веб-приложений.|
|/sites/slots/diagnostics/runtimeavailability/read|Получение данных диагностики слотов о доступности среды выполнения для веб-приложений.|
|/sites/slots/diagnostics/servicehealth/read|Получение данных диагностики слотов о работоспособности служб для веб-приложений.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Получение данных диагностики слотов об анализе ЦП сайта для веб-приложений.|
|/sites/slots/diagnostics/sitecrashes/read|Получение данных диагностики слотов о сбоях сайта для веб-приложений.|
|/sites/slots/diagnostics/sitelatency/read|Получение данных диагностики слотов о задержке сайта для веб-приложений.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Получение данных диагностики слотов об анализе памяти сайта для веб-приложений.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Получение данных диагностики слотов для веб-приложений об обновлении параметра перезапуска сайта.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Получение данных диагностики слотов для веб-приложений об инициированном пользователем перезапуске сайта.|
|/sites/slots/diagnostics/siteswap/read|Получение данных диагностики слотов для веб-приложений о переключении сайта.|
|/sites/slots/diagnostics/threadcount/read|Получение данных диагностики слотов для веб-приложений о количестве потоков.|
|/sites/slots/diagnostics/workeravailability/read|Получение данных диагностики слотов для веб-приложений о доступности рабочих процессов.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Получение данных диагностики слотов для веб-приложений о перезапуске рабочих процессов.|
|/sites/slots/domainownershipidentifiers/read|Получение идентификаторов владения доменом слотов для веб-приложений.|
|/sites/slots/hostnamebindings/delete|Удаляет привязки имен узлов для слотов веб-приложений.|
|/sites/slots/hostnamebindings/read|Возвращает привязки имен узлов для слотов веб-приложений.|
|/sites/slots/hostnamebindings/write|Обновляет привязки имен узлов для слотов веб-приложений.|
|/sites/slots/hybridconnection/delete|Удаляет гибридное подключение для слотов веб-приложений.|
|/sites/slots/hybridconnection/read|Возвращает гибридное подключение для слотов веб-приложений.|
|/sites/slots/hybridconnection/write|Обновляет гибридное подключение для слотов веб-приложений.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Удаление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Обновление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений.|
|/sites/slots/hybridconnectionrelays/read|Получение ретрансляторов гибридных подключений слотов для веб-приложений.|
|/sites/slots/instances/deployments/read|Возвращает развертывания экземпляры слотов веб-приложений.|
|/sites/slots/instances/processes/delete|Удаление процессов экземпляров слотов для веб-приложений.|
|/sites/slots/instances/processes/read|Возвращает процессы экземпляры слотов веб-приложений.|
|/sites/slots/instances/read|Возвращает экземпляры слотов веб-приложений.|
|/sites/slots/metricdefinitions/read|Возвращает определения метрик слотов веб-приложений.|
|/sites/slots/metrics/read|Возвращает метрики слотов веб-приложений.|
|/sites/slots/migratemysql/read|Получение сведений о переносе слотов для веб-приложений MySQL.|
|/sites/slots/networktrace/action|Трассировка сети слотов для веб-приложений.|
|/sites/slots/newpassword/action|Задает новый пароль для слотов веб-приложений.|
|/sites/slots/operationresults/read|Возвращает результаты операций слотов веб-приложений.|
|/sites/slots/operations/read|Получение операций слотов для веб-приложений.|
|/sites/slots/perfcounters/read|Получение счетчиков производительности слотов для веб-приложений.|
|/sites/slots/phplogging/read|Возвращает сведения о ведении журнала PHP для слотов веб-приложений.|
|/sites/slots/premieraddons/delete|Удаляет надстройки Premier для слотов веб-приложений.|
|/sites/slots/premieraddons/read|Возвращает надстройки Premier для слотов веб-приложений.|
|/sites/slots/premieraddons/write|Обновляет надстройки Premier для слотов веб-приложений.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Возвращает параметр диагностики для ресурса.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Создает или обновляет параметр диагностики для ресурса.|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Получение доступных метрик слотов для веб-приложений.|
|/sites/slots/publiccertificates/read|Получение открытых сертификатов слотов для веб-приложений.|
|/sites/slots/publiccertificates/write|Создание или обновление открытых сертификатов слотов для веб-приложений.|
|/sites/slots/publish/Action|Публикует слот веб-приложения.|
|/sites/slots/publishxml/Action|Возвращает XML-файл профиля публикации для слота веб-приложения.|
|/sites/slots/Read|Возвращает свойства слота развертывания веб-приложения.|
|/sites/slots/resetSlotConfig/Action|Сбрасывает конфигурацию слота веб-приложения.|
|/sites/slots/resourcehealthmetadata/read|Получение метаданных работоспособности ресурса слотов для веб-приложений.|
|/sites/slots/restart/Action|Перезапускает слот веб-приложения.|
|/sites/slots/restore/read|Возвращает данные о восстановлении слотов веб-приложений.|
|/sites/slots/restore/write|Восстановление слотов для веб-приложений.|
|/sites/slots/siteextensions/delete|Удаление расширений сайта слотов для веб-приложений.|
|/sites/slots/siteextensions/read|Получение расширений сайта слотов для веб-приложений.|
|/sites/slots/siteextensions/write|Обновление расширений сайта слотов для веб-приложений.|
|/sites/slots/slotsdiffs/Action|Возвращает различия конфигураций веб-приложения и слотов.|
|/sites/slots/slotsswap/Action|Переключает слоты развертывания веб-приложения.|
|/sites/slots/snapshots/read|Получение моментальных снимков слотов для веб-приложений.|
|/sites/slots/sourcecontrols/Delete|Удаляет параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/sourcecontrols/Read|Возвращает параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/sourcecontrols/Write|Обновляет параметры конфигурации системы управления версиями для слота веб-приложения.|
|/sites/slots/start/Action|Запускает слот веб-приложения.|
|/sites/slots/stop/Action|Останавливает слот веб-приложения.|
|/sites/slots/sync/action|Синхронизирует слоты веб-приложений.|
|/sites/slots/triggeredwebjobs/delete|Удаляет активированные веб-задания слотов веб-приложений.|
|/sites/slots/triggeredwebjobs/read|Возвращает активированные веб-задания слотов веб-приложений.|
|/sites/slots/triggeredwebjobs/run/action|Выполняет активированные веб-задания слотов веб-приложений.|
|/sites/slots/usages/read|Возвращает данные об использовании слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/delete|Удаляет подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/read|Возвращает подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/virtualnetworkconnections/write|Обновляет подключения виртуальных сетей для слотов веб-приложений.|
|/sites/slots/webjobs/read|Возвращает веб-задания слотов веб-приложений.|
|/sites/slots/Write|Создает новый слот веб-приложения или обновляет существующий.|
|/sites/slotsdiffs/Action|Возвращает различия конфигураций веб-приложения и слотов.|
|/sites/slotsswap/Action|Переключает слоты развертывания веб-приложения.|
|/sites/snapshots/read|Возвращает моментальные снимки веб-приложений.|
|/sites/sourcecontrols/Delete|Удаляет параметры конфигурации системы управления версиями веб-приложения.|
|/sites/sourcecontrols/Read|Возвращает параметры конфигурации системы управления версиями веб-приложения.|
|/sites/sourcecontrols/Write|Обновляет параметры конфигурации системы управления версиями веб-приложения.|
|/sites/start/Action|Запускает веб-приложение.|
|/sites/stop/Action|Останавливает веб-приложение.|
|/sites/sync/action|Синхронизирует веб-приложения.|
|/sites/syncfunctiontriggers/action|Активация функции синхронизации для веб-приложений.|
|/sites/triggeredwebjobs/delete|Удаляет активированные веб-задания веб-приложений.|
|/sites/triggeredwebjobs/history/read|Получение истории активированных веб-заданий для веб-приложений.|
|/sites/triggeredwebjobs/read|Возвращает активированные веб-задания веб-приложений.|
|/sites/triggeredwebjobs/run/action|Выполняет активированные веб-задания веб-приложений.|
|/sites/usages/read|Возвращает данные об использовании веб-приложений.|
|/sites/virtualnetworkconnections/delete|Удаляет подключения виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/gateways/read|Возвращает шлюзы подключений виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/gateways/write|Обновляет шлюзы подключений виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/read|Возвращает подключения виртуальных сетей веб-приложений.|
|/sites/virtualnetworkconnections/write|Обновляет подключения виртуальных сетей веб-приложений.|
|/sites/webjobs/read|Возвращает веб-задания веб-приложений.|
|/sites/Write|Создает новое веб-приложение или обновляет существующее.|
|/skus/read|Возвращает номера SKU.|
|/sourcecontrols/read|Возвращает систему управления версиями.|
|/sourcecontrols/write|Обновляет системы управления версиями.|
|/unregister/action|Отменяет регистрацию поставщика ресурсов Microsoft.Web для подписки.|
|/validate/action|Выполняет проверку.|
|/verifyhostingenvironmentvnet/action|Проверка виртуальной сети среды размещения.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Операция | ОПИСАНИЕ |
|---|---|
|/components/read|Чтение ресурсов операций.|
|/healthInstances/read|Чтение ресурсов операций.|
|/Operations/read|Чтение ресурсов операций.|
|/workloads/delete|Удаление ресурса рабочей нагрузки.|
|/workloads/read|Чтение ресурса рабочей нагрузки.|
|/workloads/write|Запись ресурса рабочей нагрузки.|

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [создать настраиваемую роль](custom-roles.md).
- Ознакомьтесь со [встроенными ролями RBAC](built-in-roles.md).
- Узнайте, как управлять назначением доступа [по пользователям](role-assignments-users.md) или [по ресурсам](role-assignments-portal.md). 
- Узнайте, как [просмотреть журналы действий для аудита действий с ресурсами](~/articles/azure-resource-manager/resource-group-audit.md).
