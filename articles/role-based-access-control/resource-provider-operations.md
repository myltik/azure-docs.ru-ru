---
title: Операции поставщиков ресурсов Azure Resource Manager | Документация Майкрософт
description: Списки операций, доступных для поставщиков ресурсов Microsoft Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: 08059da95baa9e70b8dba2dd847d0b28669778b7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161215"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Операции поставщиков ресурсов Azure Resource Manager

В этой статье перечислены операции, доступные в каждом поставщике ресурсов Azure Resource Manager. Эти операции могут использоваться в [настраиваемых ролях](custom-roles.md), чтобы обеспечить детализированное [управление доступом на основе ролей (RBAC)](overview.md) для ресурсов в Azure. Строки операций имеют следующий формат: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Операции в поставщиках ресурсов всегда развиваются. Чтобы получить список последних операций, используйте команду [Get AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) или [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.AAD/domainServices/delete | Удаление доменных служб. |
> | Действие | Microsoft.AAD/domainServices/read | Чтение доменных служб. |
> | Действие | Microsoft.AAD/domainServices/write | Запись доменных служб. |
> | Действие | Microsoft.AAD/locations/operationresults/read | Чтение состояния асинхронной операции. |
> | Действие | Microsoft.AAD/Operations/read | Локализованное понятное описание операции, которое будет отображаться пользователю. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | microsoft.aadiam/diagnosticsettings/delete | Удаление параметра диагностики. |
> | Действие | microsoft.aadiam/diagnosticsettings/read | Чтение параметра диагностики. |
> | Действие | microsoft.aadiam/diagnosticsettings/write | Запись параметра диагностики. |
> | Действие | microsoft.aadiam/diagnosticsettingscategories/read | Чтение категорий параметра диагностики. |
> | Действие | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов клиентов. |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Addons/operations/read | Получает поддерживаемые операции RP. |
> | Действие | Microsoft.Addons/register/action | Регистрирует указанную подписку в Microsoft.Addons. |
> | Действие | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Выводит сведения о текущем плане поддержки для указанной подписки. |
> | Действие | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Удаляет указанный план поддержки Canonical. |
> | Действие | Microsoft.Addons/supportProviders/supportPlanTypes/read | Получает состояние указанного плана поддержки Canonical. |
> | Действие | Microsoft.Addons/supportProviders/supportPlanTypes/write | Добавляет указанный тип плана поддержки Canonical. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ADHybridHealthService/addsservices/action | Создает лес для клиента. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Получает все серверы для указанного имени службы. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/alerts/read | Получает сведения об оповещениях для леса, например идентификатор оповещения, дата получения, последнее обнаружение, описание, последнее обновление, уровень и состояние оповещения, а также ссылки на устранение неполадок с оповещением и т. д. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/configuration/read | Получает конфигурацию службы для леса. Например, имя леса, функциональный уровень, роль хозяина именования доменов FSMO, роль хозяина схемы FSMO и т. д. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/delete | Удаляет службу и ее серверы, а также данные о работоспособности. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Получает сведения о доменах и сайтах для леса. Например, состояние работоспособности, активные оповещения, разрешенные оповещения, такие свойства, как функциональный уровень домена, лес, хозяин инфраструктуры, PDC, хозяин RID и т. д.  |
> | Действие | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Получает параметр настройки пользователя для леса.<br>Например, значения MetricCounterName, такие как ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Параметры диаграммы пользовательского интерфейса и т. д. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Получает сводку для данного леса, например имя леса, количество доменов в лесу, количество сайтов, сведения о сайтах и т. д. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Получает список поддерживаемых метрик для данной службы.<br>Например, число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы ADSync. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Этот API возвращает список всех подключенных служб ADDomainServices для клиента категории "Премиум". |
> | Действие | Microsoft.ADHybridHealthService/addsservices/read | Получает сведения о службе для указанного имени службы. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Получает сведения о репликации всех серверов для указанного имени службы. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Получает число контроллеров домена и их ошибок репликации, если таковые имеются. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Получает полный список контроллеров домена и сведений о репликации для данного леса. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Добавляет экземпляр сервера в службу. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Во время регистрации сервера ADDomainService этот API вызывается, чтобы получить учетные данные для подключения новых серверов. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Удаляет сервер для данной службы и клиента. |
> | Действие | Microsoft.ADHybridHealthService/addsservices/write | Создает или обновляет экземпляр ADDomainService для клиента. |
> | Действие | Microsoft.ADHybridHealthService/configuration/action | Обновляет конфигурацию клиента. |
> | Действие | Microsoft.ADHybridHealthService/configuration/read | Считывает конфигурацию клиента. |
> | Действие | Microsoft.ADHybridHealthService/configuration/write | Создает конфигурацию клиента. |
> | Действие | Microsoft.ADHybridHealthService/logs/contents/read | Получает содержимое журналов установки и регистрации агента, хранимых в большом двоичном объекте. |
> | Действие | Microsoft.ADHybridHealthService/logs/read | Получает журналы установки и регистрации агента для клиента. |
> | Действие | Microsoft.ADHybridHealthService/operations/read | Возвращает список операций, поддерживаемых системой. |
> | Действие | Microsoft.ADHybridHealthService/register/action | Регистрирует поставщик ресурсов службы работоспособности ADHybrid и позволяет создавать ресурсы службы работоспособности ADHybrid. |
> | Действие | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Получает список доступных регионов, используемых командой DevOps для поддержки инцидентов клиентов. |
> | Действие | Microsoft.ADHybridHealthService/reports/badpassword/read | Получает список попыток неправильного ввода пароля для всех пользователей в службе федерации Active Directory. |
> | Действие | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Получает универсальный код ресурса SAS большого двоичного объекта, содержащий состояние и конечный результат недавно поставленного в очередь задания отчета для определения частоты попыток неправильного ввода имени пользователя или пароля на каждый идентификатор пользователя по каждому IP-адресу в день для данного клиента. |
> | Действие | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Получает список клиентов, получивших согласие DevOps. Обычно используется для поддержки клиентов. |
> | Действие | Microsoft.ADHybridHealthService/reports/isdevops/read | Возвращает значение, указывающее, имеет ли клиент согласие DevOps. |
> | Действие | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Обновляет идентификатор пользователя (идентификатор объекта) выбранного клиента DevOps. |
> | Действие | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Возвращает выбранное развертывание для данного клиента. |
> | Действие | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Получает расположение хранилища клиента на основе идентификатора клиента. |
> | Действие | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Возвращает географическое расположение, из которого будет осуществляться доступ к данных. |
> | Действие | Microsoft.ADHybridHealthService/services/action | Обновляет экземпляр службы в клиенте. |
> | Действие | Microsoft.ADHybridHealthService/services/alerts/read | Считывает оповещения для службы. |
> | Действие | Microsoft.ADHybridHealthService/services/alerts/read | Считывает оповещения для службы. |
> | Действие | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | На основе имени функции проверяет, имеется ли у службы все необходимое для использования этой функции. |
> | Действие | Microsoft.ADHybridHealthService/services/delete | Удаляет экземпляр службы из клиента. |
> | Действие | Microsoft.ADHybridHealthService/services/exporterrors/read | Возвращает ошибки экспорта для данной службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/exportstatus/read | Возвращает состояние экспорта для данной службы. |
> | Действие | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Получает обратную связь об оповещениях для данной службы и сервера. |
> | Действие | Microsoft.ADHybridHealthService/services/metricmetadata/read | Получает список поддерживаемых метрик для данной службы.<br>Например, число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы ADSync. |
> | Действие | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Этот API получает среднее значение метрик для данной службы.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Этот API получает агрегированное представление о метриках для данной службы.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Добавляет или обновляет конфигурацию мониторинга для службы. |
> | Действие | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Получает конфигурации мониторинга для данной службы. |
> | Действие | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Добавляет или обновляет конфигурации мониторинга для службы. |
> | Действие | Microsoft.ADHybridHealthService/services/premiumcheck/read | Этот API возвращает список всех подключенных служб для клиента категории "Премиум". |
> | Действие | Microsoft.ADHybridHealthService/services/read | Считывает экземпляры службы в клиенте. |
> | Действие | Microsoft.ADHybridHealthService/services/reports/details/read | Возвращает отчет о первых 50 пользователях, вводивших неправильный пароль, за последние 7 дней. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/action | Создает экземпляр службы в клиенте. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Считывает оповещения для сервера. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Во время регистрации сервера этот API вызывается, чтобы получить учетные данные для подключения новых серверов. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Для данного сервера этот API возвращает список типов данных, которые передаются серверами, и самое позднее время каждой отправки. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/delete | Удаляет экземпляр службы из службы. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Возвращает сведения об ошибке экспорта данных синхронизации для указанной службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Для данной службы этот API получает информацию о метриках.<br>Например, этот API можно использовать для получения следующей информации: число блокировок учетных записей из экстрасети, общее число невыполненных запросов, число невыполненных запросов маркера (прокси), число запросов токенов/с и т. д. для службы ADFS.<br>Количество проверок подлинности NTLM/с, число успешных привязок LDAP/с, время привязки LDAP, количество активных потоков LDAP, число проверок подлинности Kerberos/с, общее количество потоков ATQ и т. д. для службы ADDomain.<br>Задержка запуска профиля, установленные TCP-подключения, частные байты агента получения информации, статистика по экспорту в Azure AD для службы синхронизации. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/read | Считывает экземпляр сервера в службе. |
> | Действие | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Получает конфигурацию службы для данного клиента. |
> | Действие | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Получает состояние разрешенных функций для данного клиента. |
> | Действие | Microsoft.ADHybridHealthService/services/write | Создает экземпляр службы в клиенте. |
> | Действие | Microsoft.ADHybridHealthService/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов службы работоспособности ADHybrid. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Advisor/configurations/read | Получение конфигураций |
> | Действие | Microsoft.Advisor/configurations/write | Создание или обновление конфигурации. |
> | Действие | Microsoft.Advisor/generateRecommendations/action | Создает рекомендации. |
> | Действие | Microsoft.Advisor/generateRecommendations/read | Возвращает состояние создания рекомендаций. |
> | Действие | Microsoft.Advisor/operations/read | Получение операций для Microsoft Advisor. |
> | Действие | Microsoft.Advisor/recommendations/read | Считывает рекомендации. |
> | Действие | Microsoft.Advisor/recommendations/suppressions/delete | Удаляет подавления. |
> | Действие | Microsoft.Advisor/recommendations/suppressions/read | Возвращает подавления. |
> | Действие | Microsoft.Advisor/recommendations/suppressions/write | Создает или обновляет подавления. |
> | Действие | Microsoft.Advisor/register/action | Регистрирует подписку для Microsoft Advisor. |
> | Действие | Microsoft.Advisor/suppressions/delete | Удаляет подавления. |
> | Действие | Microsoft.Advisor/suppressions/read | Возвращает подавления. |
> | Действие | Microsoft.Advisor/suppressions/write | Создает или обновляет подавления. |
> | Действие | Microsoft.Advisor/unregister/action | Отмена регистрации подписки для Microsoft Advisor. |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.AlertsManagement/alerts/changestate/action | Изменение состояния оповещения. |
> | Действие | Microsoft.AlertsManagement/alerts/read | Получение всех оповещений для фильтров входа. |
> | Действие | Microsoft.AlertsManagement/alertsSummary/read | Получение сводки оповещений. |
> | Действие | Microsoft.AlertsManagement/Operations/read | Чтение предоставленных операций. |
> | Действие | Microsoft.AlertsManagement/smartGroups/changestate/action | Изменение состояния смарт-группы. |
> | Действие | Microsoft.AlertsManagement/smartGroups/read | Получение всех смарт-групп для фильтров ввода. |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Проверяет, является ли данное имя сервера анализа данных допустимым и неиспользуемым. |
> | Действие | Microsoft.AnalysisServices/locations/operationresults/read | Получение сведений о результате указанной операции. |
> | Действие | Microsoft.AnalysisServices/locations/operationstatuses/read | Извлечение сведений о состоянии указанной операции. |
> | Действие | Microsoft.AnalysisServices/operations/read | Извлечение сведений об операциях. |
> | Действие | Microsoft.AnalysisServices/register/action | Регистрация поставщика ресурсов для служб Analysis Services. |
> | Действие | Microsoft.AnalysisServices/servers/delete | Удаляет сервер анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Отображение состояния шлюза, связанного с сервером. |
> | Действие | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для сервера анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для сервера анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов серверов. |
> | Действие | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для сервера анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/read | Извлекает информацию об указанном сервере анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/resume/action | Возобновляет работу сервера анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/skus/read | Получение доступных сведений о номере SKU для сервера. |
> | Действие | Microsoft.AnalysisServices/servers/suspend/action | Приостанавливает сервер анализа данных. |
> | Действие | Microsoft.AnalysisServices/servers/write | Создает или обновляет указанный сервер анализа данных. |
> | Действие | Microsoft.AnalysisServices/skus/read | Извлечение сведений о номерах SKU. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ApiManagement/checkNameAvailability/read | Проверяет, доступно ли указанное имя службы. |
> | Действие | Microsoft.ApiManagement/operations/read | Чтение всех операций API для ресурса Microsoft.ApiManagement. |
> | Действие | Microsoft.ApiManagement/register/action | Регистрирует подписку для поставщика ресурсов Microsoft.ApiManagement. |
> | Действие | Microsoft.ApiManagement/reports/read | Получение отчетов, объединенных по периодам времени, географическим регионам, разработчикам, продуктам, API-интерфейсам, операциям, подпискам и запросам. |
> | Действие | Microsoft.ApiManagement/service/apis/delete | Удаляет существующий API. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/delete | Удаление существующих данных диагностики. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Удаление сопоставления средства ведения журнала с параметром диагностики. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Получение списка существующих диагностических средств ведения журнала. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Сопоставление средства ведения журнала с параметром диагностики. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/read | Получение списка диагностики или сведений о диагностике. |
> | Действие | Microsoft.ApiManagement/service/apis/diagnostics/write | Добавление новых данных диагностики или обновление существующих диагностических сведений. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Удаление существующего вложения. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/attachments/read | Получение вложений, касающихся проблем, или сведений о вложениях, касающихся проблем со службой управления API. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/attachments/write | Добавление вложения, касающегося проблемы с API. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/comments/delete | Удаление имеющегося комментария. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/comments/read | Возвращение примечаний к проблеме или сведений о примечании к проблеме со службой управления API. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/comments/write | Добавление примечания к проблеме с API. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/delete | Удаление имеющейся проблемы. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/read | Получение сведений о проблемах, связанных с API, или сведений о проблеме со службой управления API. |
> | Действие | Microsoft.ApiManagement/service/apis/issues/write | Добавление или обновление проблемы с API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/delete | Удаляет существующую операцию API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policies/delete | Удаление конфигурации политики из политик операций API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policies/read | Получение политик для операций API или получение сведений о конфигурации политики для операций API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policies/write | Определение сведений о конфигурации политики для операции API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policy/delete | Удаляет конфигурацию политики из операции. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policy/read | Возвращает сведения о конфигурации политики для операции. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/policy/write | Задает сведения о конфигурации политики для операции. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/read | Возвращает список существующих операций API или сведения о конкретной операции API. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/tags/delete | Удаление связи существующего тега с существующей операцией. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/tags/read | Получение тегов, связанных с операцией, или сведений о теге. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/tags/write | Связывание существующего тега с существующей операцией. |
> | Действие | Microsoft.ApiManagement/service/apis/operations/write | Создает новую операцию API или обновляет существующую. |
> | Действие | Microsoft.ApiManagement/service/apis/operationsByTags/read | Получение списка сопоставлений операций или тегов. |
> | Действие | Microsoft.ApiManagement/service/apis/policies/delete | Удаление конфигурации политики из политик API. |
> | Действие | Microsoft.ApiManagement/service/apis/policies/read | Получение политик для API или получение сведений о конфигурации политики для API. |
> | Действие | Microsoft.ApiManagement/service/apis/policies/write | Задает сведения о конфигурации политики для API. |
> | Действие | Microsoft.ApiManagement/service/apis/policy/delete | Удаляет конфигурацию политики из API. |
> | Действие | Microsoft.ApiManagement/service/apis/policy/read | Возвращает сведения о конфигурации политики для API. |
> | Действие | Microsoft.ApiManagement/service/apis/policy/write | Задает сведения о конфигурации политики для API. |
> | Действие | Microsoft.ApiManagement/service/apis/products/read | Получение всех продуктов, частью которых является API. |
> | Действие | Microsoft.ApiManagement/service/apis/read | Возвращает список всех зарегистрированных интерфейсов API или сведения об API. |
> | Действие | Microsoft.ApiManagement/service/apis/releases/delete | Удаление всех версий API или выпуска API. |
> | Действие | Microsoft.ApiManagement/service/apis/releases/read | Получение списка выпусков API или сведений о выпуске API. |
> | Действие | Microsoft.ApiManagement/service/apis/releases/write | Создание нового выпуска API или обновление существующего. |
> | Действие | Microsoft.ApiManagement/service/apis/revisions/delete | Удаление всех редакций API. |
> | Действие | Microsoft.ApiManagement/service/apis/revisions/read | Получение списка редакций, относящихся к API. |
> | Действие | Microsoft.ApiManagement/service/apis/schemas/delete | Удаление существующей схемы. |
> | Действие | Microsoft.ApiManagement/service/apis/schemas/document/read | Получение документа, описывающего схему. |
> | Действие | Microsoft.ApiManagement/service/apis/schemas/document/write | Обновление документа, описывающего схему. |
> | Действие | Microsoft.ApiManagement/service/apis/schemas/read | Получение всех схем для данного API или получение схем, используемых API. |
> | Действие | Microsoft.ApiManagement/service/apis/schemas/write | Определение схем, используемых API. |
> | Действие | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Удаление описания тега из API. |
> | Действие | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Получение описаний тегов или описания тега в области API. |
> | Действие | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Создание или изменение описания тега в области API. |
> | Действие | Microsoft.ApiManagement/service/apis/tags/delete | Удаление существующего сопоставления API и тега. |
> | Действие | Microsoft.ApiManagement/service/apis/tags/read | Получение всех сопоставлений API и тега или сведений о сопоставлении API и тега. |
> | Действие | Microsoft.ApiManagement/service/apis/tags/write | Добавление нового сопоставления API и тега. |
> | Действие | Microsoft.ApiManagement/service/apis/write | Создает новый API или обновляет данные существующего API. |
> | Действие | Microsoft.ApiManagement/service/apisByTags/read | Получение списка сопоставлений API и тега. |
> | Действие | Microsoft.ApiManagement/service/api-version-sets/delete | Удаление существующего набора версий. |
> | Действие | Microsoft.ApiManagement/service/api-version-sets/read | Получение списка сущностей группы версий или получение сведений о наборе версий. |
> | Действие | Microsoft.ApiManagement/service/api-version-sets/versions/read | Получение списка сущностей версии. |
> | Действие | Microsoft.ApiManagement/service/api-version-sets/write | Создание нового набора версий или обновление сведений о существующем наборе. |
> | Действие | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Обновляет ресурсы Microsoft.ApiManagement, работающие в виртуальной сети, для применения обновленных параметров сети. |
> | Действие | Microsoft.ApiManagement/service/authorizationServers/delete | Удаляет существующий сервер авторизации. |
> | Действие | Microsoft.ApiManagement/service/authorizationServers/read | Возвращает список серверов авторизации или сведения о сервере авторизации. |
> | Действие | Microsoft.ApiManagement/service/authorizationServers/write | Создает новый сервер авторизации или обновляет данные существующего. |
> | Действие | Microsoft.ApiManagement/service/backends/delete | Удаляет существующую серверную часть. |
> | Действие | Microsoft.ApiManagement/service/backends/read | Возвращает список серверных частей или сведения о серверной части. |
> | Действие | Microsoft.ApiManagement/service/backends/reconnect/action | Создание запроса на повторное подключение. |
> | Действие | Microsoft.ApiManagement/service/backends/write | Добавляет новую серверную часть или обновляет данные существующей. |
> | Действие | Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
> | Действие | Microsoft.ApiManagement/service/certificates/delete | Удаляет существующий сертификат. |
> | Действие | Microsoft.ApiManagement/service/certificates/read | Возвращает список сертификатов или сведения о сертификате. |
> | Действие | Microsoft.ApiManagement/service/certificates/write | Добавляет новый сертификат. |
> | Действие | Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/delete | Удаление существующих данных диагностики. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Удаление сопоставления средства ведения журнала с параметром диагностики. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/loggers/read | Получение списка существующих диагностических средств ведения журнала. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/loggers/write | Сопоставление средства ведения журнала с параметром диагностики. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/read | Получение списка диагностики или сведений о диагностике. |
> | Действие | Microsoft.ApiManagement/service/diagnostics/write | Добавление новых данных диагностики или обновление существующих диагностических сведений. |
> | Действие | Microsoft.ApiManagement/service/getssotoken/action | Возвращает маркер единого входа, который может использоваться для входа на портал прежней версии службы управления API от имени администратора. |
> | Действие | Microsoft.ApiManagement/service/groups/delete | Удаляет существующую группу. |
> | Действие | Microsoft.ApiManagement/service/groups/read | Возвращает список групп или сведения о группе. |
> | Действие | Microsoft.ApiManagement/service/groups/users/delete | Удаляет пользователя из существующей группы. |
> | Действие | Microsoft.ApiManagement/service/groups/users/read | Возвращает список пользователей группы. |
> | Действие | Microsoft.ApiManagement/service/groups/users/write | Добавляет существующего пользователя в существующий продукт. |
> | Действие | Microsoft.ApiManagement/service/groups/write | Создает новую группу или обновляет данные существующей. |
> | Действие | Microsoft.ApiManagement/service/identityProviders/delete | Удаляет существующий поставщик удостоверений. |
> | Действие | Microsoft.ApiManagement/service/identityProviders/read | Возвращает список поставщиков удостоверений или сведения о поставщике удостоверений. |
> | Действие | Microsoft.ApiManagement/service/identityProviders/write | Создает новый поставщик удостоверений или обновляет данные существующего. |
> | Действие | Microsoft.ApiManagement/service/locations/networkstatus/read | Получение состояния доступа к сетевым ресурсам, от которых зависит служба в расположении. |
> | Действие | Microsoft.ApiManagement/service/loggers/delete | Удаляет существующее средство ведения журнала. |
> | Действие | Microsoft.ApiManagement/service/loggers/read | Возвращает список средств ведения журнала или сведения о средстве ведения журнала. |
> | Действие | Microsoft.ApiManagement/service/loggers/write | Добавляет новое средство ведения журнала или обновляет существующее. |
> | Действие | Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
> | Действие | Microsoft.ApiManagement/service/networkstatus/read | Получение состояния доступа к сетевым ресурсам, от которых зависит служба. |
> | Действие | Microsoft.ApiManagement/service/notifications/action | Отправление уведомления указанному пользователю. |
> | Действие | Microsoft.ApiManagement/service/notifications/read | Получение всех уведомлений издателя управления API или сведений об этих уведомлениях. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Удаление существующего адреса электронной почты, связанного с уведомлением. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Получение списка получателей, связанных с уведомлением издателя управления API. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Создание нового получателя уведомлений электронной почты. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Удаление пользователя, связанного с получателями уведомлений. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Получение списка получателей, связанных с уведомлением. |
> | Действие | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Добавление пользователя в список получателей уведомлений. |
> | Действие | Microsoft.ApiManagement/service/notifications/write | Создание или обновление уведомления издателя управления API. |
> | Действие | Microsoft.ApiManagement/service/openidConnectProviders/delete | Удаляет существующий поставщик OpenID Connect. |
> | Действие | Microsoft.ApiManagement/service/openidConnectProviders/read | Возвращает список поставщиков OpenID Connect или сведения о поставщике OpenID Connect. |
> | Действие | Microsoft.ApiManagement/service/openidConnectProviders/write | Создает новый поставщик OpenID Connect или обновляет данные существующего. |
> | Действие | Microsoft.ApiManagement/service/operationresults/read | Возвращает текущее состояние длительной операции. |
> | Действие | Microsoft.ApiManagement/service/policies/delete | Удаление конфигурации политики из политик клиента. |
> | Действие | Microsoft.ApiManagement/service/policies/read | Получение политик клиента или сведений о конфигурации политик клиента. |
> | Действие | Microsoft.ApiManagement/service/policies/write | Определение сведений о конфигурации политик клиента. |
> | Действие | Microsoft.ApiManagement/service/policySnippets/read | Получение всех фрагментов кода политики. |
> | Действие | Microsoft.ApiManagement/service/portalsettings/read | Получение параметров регистрации на портале, параметров для входа на портал или параметров делегирования для портала. |
> | Действие | Microsoft.ApiManagement/service/portalsettings/write | Обновление параметров регистрации на портале, параметров для входа на портал или параметров делегирования для портала. |
> | Действие | Microsoft.ApiManagement/service/products/apis/delete | Удаляет API из существующего продукта. |
> | Действие | Microsoft.ApiManagement/service/products/apis/read | Возвращает список интерфейсов API, добавленных в существующий продукт. |
> | Действие | Microsoft.ApiManagement/service/products/apis/write | Добавляет API в существующий продукт. |
> | Действие | Microsoft.ApiManagement/service/products/delete | Удаляет существующий продукт. |
> | Действие | Microsoft.ApiManagement/service/products/groups/delete | Удаляет связь между существующими продуктом и группой разработчиков. |
> | Действие | Microsoft.ApiManagement/service/products/groups/read | Возвращает список групп разработчиков, связанных с продуктом. |
> | Действие | Microsoft.ApiManagement/service/products/groups/write | Устанавливает связь между существующими продуктом и группой разработчиков. |
> | Действие | Microsoft.ApiManagement/service/products/policies/delete | Удаление конфигурации политики из политик продукта. |
> | Действие | Microsoft.ApiManagement/service/products/policies/read | Получение политик продукта или сведений о конфигурации политики продукта. |
> | Действие | Microsoft.ApiManagement/service/products/policies/write | Настройка сведений о конфигурации политики продукта. |
> | Действие | Microsoft.ApiManagement/service/products/policy/delete | Удаляет конфигурацию политики из существующего продукта. |
> | Действие | Microsoft.ApiManagement/service/products/policy/read | Возвращает конфигурацию политики существующего продукта. |
> | Действие | Microsoft.ApiManagement/service/products/policy/write | Задает конфигурацию политики существующего продукта. |
> | Действие | Microsoft.ApiManagement/service/products/read | Возвращает список продуктов или сведения о продукте. |
> | Действие | Microsoft.ApiManagement/service/products/subscriptions/read | Возвращает список подписок на продукт. |
> | Действие | Microsoft.ApiManagement/service/products/tags/delete | Удаление связи существующего тега с существующим продуктом. |
> | Действие | Microsoft.ApiManagement/service/products/tags/read | Получение тегов, связанных с продуктом, или сведений о теге. |
> | Действие | Microsoft.ApiManagement/service/products/tags/write | Связывание существующего тега с существующим продуктом. |
> | Действие | Microsoft.ApiManagement/service/products/write | Создает новый продукт или обновляет данные существующего. |
> | Действие | Microsoft.ApiManagement/service/properties/delete | Удаляет существующее свойство. |
> | Действие | Microsoft.ApiManagement/service/properties/read | Возвращает список всех свойств или сведения об указанном свойстве. |
> | Действие | Microsoft.ApiManagement/service/properties/write | Создает новое свойство или обновляет значение указанного свойства. |
> | Действие | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/quotas/periods/read | Получение значения счетчика квоты за период. |
> | Действие | Microsoft.ApiManagement/service/quotas/periods/write | Настройка текущего значения счетчика квоты. |
> | Действие | Microsoft.ApiManagement/service/quotas/read | Получение значений квоты. |
> | Действие | Microsoft.ApiManagement/service/quotas/write | Настройка текущего значения счетчика квоты. |
> | Действие | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Действие | Microsoft.ApiManagement/service/reports/read | Получение отчета, объединенного по периодам времени, по географическим регионам или по разработчикам;<br>получение отчета, объединенного по продуктам;<br>получение отчета, объединенного по API-интерфейсам, операциям или по подпискам;<br>получение запросов, передающих данные. |
> | Действие | Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
> | Действие | Microsoft.ApiManagement/service/subscriptions/delete | Удаляет подписку. С помощью этой операции можно удалить подписку. |
> | Действие | Microsoft.ApiManagement/service/subscriptions/read | Возвращает список подписок на продукт или сведения о подписке на продукт. |
> | Действие | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Повторно создает первичный ключ подписки. |
> | Действие | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Повторно создает вторичный ключ подписки. |
> | Действие | Microsoft.ApiManagement/service/subscriptions/write | Подписывает существующего пользователя на имеющийся продукт или обновляет данные существующей подписки. С помощью этой операции можно обновить подписку. |
> | Действие | Microsoft.ApiManagement/service/tagResources/read | Получение списка тегов со связанными ресурсами. |
> | Действие | Microsoft.ApiManagement/service/tags/delete | Удаление существующего тега. |
> | Действие | Microsoft.ApiManagement/service/tags/read | Получение списка тегов или сведений о теге. |
> | Действие | Microsoft.ApiManagement/service/tags/write | Добавление нового тега или обновление сведений о существующем. |
> | Действие | Microsoft.ApiManagement/service/templates/delete | Сброс к шаблону электронного сообщения по умолчанию службы управления API. |
> | Действие | Microsoft.ApiManagement/service/templates/read | Получение всех шаблонов сообщений или сведений о шаблоне сообщения службы управления API. |
> | Действие | Microsoft.ApiManagement/service/templates/write | Создание или обновление шаблона сообщения службы управления API. |
> | Действие | Microsoft.ApiManagement/service/tenant/delete | Удаляет конфигурацию политики для клиента. |
> | Действие | Microsoft.ApiManagement/service/tenant/deploy/action | Выполняет задачу развертывания для применения изменений из указанной ветви Git к конфигурации в базе данных. |
> | Действие | Microsoft.ApiManagement/service/tenant/operationResults/read | Возвращает список результатов операций или результат определенной операции. |
> | Действие | Microsoft.ApiManagement/service/tenant/read | Получение конфигурации политики для клиента или сведений о доступе к клиенту. |
> | Действие | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Повторно создает первичный ключ доступа. |
> | Действие | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Повторно создает вторичный ключ доступа. |
> | Действие | Microsoft.ApiManagement/service/tenant/save/action | Создает фиксацию с использованием моментального снимка конфигурации для указанной ветви в репозитории. |
> | Действие | Microsoft.ApiManagement/service/tenant/syncState/read | Возвращает состояние последней синхронизации с Git. |
> | Действие | Microsoft.ApiManagement/service/tenant/validate/action | Проверяет изменения из указанной ветви Git. |
> | Действие | Microsoft.ApiManagement/service/tenant/write | Установка конфигурации политики для клиента или обновление сведений о доступе к клиенту. |
> | Действие | Microsoft.ApiManagement/service/updatecertificate/action | Передает SSL-сертификата для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
> | Действие | Microsoft.ApiManagement/service/users/action | Регистрация нового пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/applications/attachments/delete | Удаление вложения. |
> | Действие | Microsoft.ApiManagement/service/users/applications/attachments/read | Получение вложений приложения или вложения. |
> | Действие | Microsoft.ApiManagement/service/users/applications/attachments/write | Добавление вложения в приложение. |
> | Действие | Microsoft.ApiManagement/service/users/applications/delete | Удаление существующего приложения. |
> | Действие | Microsoft.ApiManagement/service/users/applications/read | Получение списка всех пользовательских приложений или сведений о приложении службы управления API. |
> | Действие | Microsoft.ApiManagement/service/users/applications/write | Регистрация приложения в службе управления API или обновление сведений о приложении. |
> | Действие | Microsoft.ApiManagement/service/users/delete | Удаляет учетную запись пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Создает URL-адрес единого входа. URL-адрес, который можно использовать для доступа к порталу администрирования. |
> | Действие | Microsoft.ApiManagement/service/users/groups/read | Возвращает список групп пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/keys/read | Возвращает список ключей пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/read | Возвращает список зарегистрированных пользователей или сведения об учетной записи пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/subscriptions/read | Возвращает список подписок пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/token/action | Получение токена доступа для пользователя. |
> | Действие | Microsoft.ApiManagement/service/users/write | Регистрирует нового пользователя или обновляет данные учетной записи существующего пользователя. |
> | Действие | Microsoft.ApiManagement/service/write | Создание экземпляра службы управления API. |
> | Действие | Microsoft.ApiManagement/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов Microsoft.ApiManagement. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Authorization/checkAccess/action | Проверка того, может ли вызывающий объект выполнять определенное действие. |
> | Действие | Microsoft.Authorization/classicAdministrators/delete | Удаляет учетные данные администратора из подписки. |
> | Действие | Microsoft.Authorization/classicAdministrators/read | Считывает администраторов для подписки. |
> | Действие | Microsoft.Authorization/classicAdministrators/write | Добавляет или изменяет администратора подписки. |
> | Действие | Microsoft.Authorization/elevateAccess/action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |
> | Действие | Microsoft.Authorization/locks/delete | Удаляет блокировки в указанной области. |
> | Действие | Microsoft.Authorization/locks/read | Возвращает блокировки в указанной области. |
> | Действие | Microsoft.Authorization/locks/write | Добавляет блокировки в указанной области. |
> | Действие | Microsoft.Authorization/permissions/read | Выводит список всех разрешений, которые есть у вызывающего в заданной области. |
> | Действие | Microsoft.Authorization/policyAssignments/delete | Удаляет назначение политики в указанной области. |
> | Действие | Microsoft.Authorization/policyAssignments/read | Возвращает сведения о назначении политики. |
> | Действие | Microsoft.Authorization/policyAssignments/write | Создает назначение политики в указанной области. |
> | Действие | Microsoft.Authorization/policyDefinitions/delete | Удаляет определение политики. |
> | Действие | Microsoft.Authorization/policyDefinitions/read | Возвращает сведения об определении политики. |
> | Действие | Microsoft.Authorization/policyDefinitions/write | Создает определение настраиваемой политики. |
> | Действие | Microsoft.Authorization/policySetDefinitions/delete | Удаление определения набора политик. |
> | Действие | Microsoft.Authorization/policySetDefinitions/read | Получение сведений об определении набора политик. |
> | Действие | Microsoft.Authorization/policySetDefinitions/write | Создание пользовательского определения набора политик. |
> | Действие | Microsoft.Authorization/providerOperations/read | Возвращает операции для всех поставщиков ресурсов, которые могут быть использованы в определениях ролей. |
> | Действие | Microsoft.Authorization/roleAssignments/delete | Здесь описывается удаление назначения роли в указанной области. |
> | Действие | Microsoft.Authorization/roleAssignments/read | Возвращает сведения о назначении роли. |
> | Действие | Microsoft.Authorization/roleAssignments/write. | Создает назначение роли в указанной области. |
> | Действие | Microsoft.Authorization/roleDefinitions/delete | Удаляет указанное определение настраиваемой роли. |
> | Действие | Microsoft.Authorization/roleDefinitions/read | Возвращает сведения об определении роли. |
> | Действие | Microsoft.Authorization/roleDefinitions/write | Создает или обновляет определение настраиваемой роли с помощью указанных разрешений и назначаемых областей. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Чтение сведений о регистрации Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Запись запроса на повторное создание ключей Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/certificates/delete | Удаляет ресурс сертификата службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/certificates/getCount/action | Считывает число сертификатов. |
> | Действие | Microsoft.Automation/automationAccounts/certificates/read | Возвращает ресурс сертификата службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/certificates/write | Создает или обновляет ресурс сертификата службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/compilationjobs/read | Чтение содержимого компиляции Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/compilationjobs/read | Чтение содержимого компиляции Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/compilationjobs/write | Запись содержимого компиляции Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/compilationjobs/write | Запись содержимого компиляции Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/configurations/content/read | Считывает мультимедийное содержимое конфигурации. |
> | Действие | Microsoft.Automation/automationAccounts/configurations/delete | Удаление содержимого Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/configurations/getCount/action | Чтение количества содержимого Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/configurations/read | Возвращает содержимое Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/configurations/write | Запись содержимого Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/connections/delete | Удаляет ресурс подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/connections/getCount/action | Считывает число подключений. |
> | Действие | Microsoft.Automation/automationAccounts/connections/read | Возвращает ресурс подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/connections/write | Создает или обновляет ресурс подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/connectionTypes/delete | Удаляет ресурс типа подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/connectionTypes/read | Возвращает ресурс типа подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/connectionTypes/write | Создает ресурс типа подключения службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/credentials/delete | Удаляет ресурс учетных данных службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/credentials/getCount/action | Считывает количество учетных данных. |
> | Действие | Microsoft.Automation/automationAccounts/credentials/read | Возвращает ресурс учетных данных службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/credentials/write | Создает или обновляет ресурс учетных данных службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/delete | Удаляет учетную запись службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Настройка параметра диагностики для ресурса. |
> | Действие | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Удаляет ресурсы гибридной рабочей роли Runbook. |
> | Действие | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Возвращает содержимое runbook службы автоматизации Azure во время выполнения задания. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobSchedules/delete | Удаляет расписание заданий службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobSchedules/read | Возвращает расписание заданий службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/jobSchedules/write | Создает расписание заданий службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Получает рабочую область, связанную с учетной записью службы автоматизации. |
> | Действие | Microsoft.Automation/automationAccounts/listKeys/action | Считывает ключи для учетной записи службы автоматизации. |
> | Действие | Microsoft.Automation/automationAccounts/logDefinitions/read | Получение доступных журналов учетной записи автоматизации. |
> | Действие | Microsoft.Automation/automationAccounts/modules/activities/read | Получение действий службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/modules/delete | Удаляет модуль службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/modules/getCount/action | Возвращает число модулей в рамках учетной записи службы автоматизации. |
> | Действие | Microsoft.Automation/automationAccounts/modules/read | Возвращает модуль службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/modules/write | Создает или обновляет модуль службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Удаление конфигурации узла Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Чтение содержимого конфигурации узла Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Чтение конфигурации узла Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Запись конфигурации узла Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodecounts/read | Считывает общее число узлов указанного типа. |
> | Действие | Microsoft.Automation/automationAccounts/nodes/delete | Удаление узлов Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodes/read | Чтение узлов Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Считывает содержимое отчета Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodes/reports/read | Чтение отчетов Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/nodes/write | Создает или обновляет узлы Azure Automation DSC. |
> | Действие | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Получение полей типов службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Получение определений метрик службы автоматизации. |
> | Действие | Microsoft.Automation/automationAccounts/read | Возвращает учетную запись службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/content/read | Возвращает содержимое runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/delete | Удаляет runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Создает содержимое черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Возвращает результаты операции черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Публикует черновик runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/read | Возвращает черновик runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Возвращает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Возобновляет выполнение тестового задания черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Останавливает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Приостанавливает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Создает тестовое задание черновика runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Отменяет правки в черновике runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Возвращает число модулей runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/runbooks/write | Создает или обновляет runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/schedules/delete | Удаляет ресурс расписания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/schedules/getCount/action | Возвращает количество расписаний службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/schedules/read | Возвращает ресурс расписания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/schedules/write | Создает или обновляет ресурс расписания службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/statistics/read | Получение статистики из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Получает компьютер для развертывания обновления службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Получает задание исправления управления обновлениями для службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/usages/read | Получение данных использования из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/variables/delete | Удаляет ресурс переменной службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/variables/read | Считывает ресурс переменной службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/variables/write | Создает или обновляет ресурс переменной службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/delete | Удаляет задание наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/read | Получает задание наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/start/action | Запускает задание наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/stop/action | Останавливает выполнение заданий наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/streams/read | Получение потока заданий наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Удаляет действия заданий наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Получает действия заданий наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Создает действия заданий наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/watchers/write | Создает задание наблюдателя из службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/webhooks/action | Создает универсальный код ресурса (URI) для webhook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/webhooks/delete | Удаляет webhook службы автоматизации Azure.  |
> | Действие | Microsoft.Automation/automationAccounts/webhooks/read | Считывает runbook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/webhooks/write | Создает или обновляет webhook службы автоматизации Azure. |
> | Действие | Microsoft.Automation/automationAccounts/write | Создает или обновляет учетную запись службы автоматизации Azure. |
> | Действие | Microsoft.Automation/operations/read | Получает доступные операции для ресурсов службы автоматизации Azure. |
> | Действие | Microsoft.Automation/register/action | Регистрирует подписку в службе автоматизации Azure. |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Удаление ресурса каталога B2C. |
> | Действие | Microsoft.AzureActiveDirectory/b2cDirectories/read | Просмотр ресурса каталога B2C. |
> | Действие | Microsoft.AzureActiveDirectory/b2cDirectories/write | Создание или обновление ресурса каталога B2C. |
> | Действие | Microsoft.AzureActiveDirectory/operations/read | Чтение всех операций API, доступных для поставщика ресурсов Microsoft.AzureActiveDirectory. |
> | Действие | Microsoft.AzureActiveDirectory/register/action | Регистрация подписки в поставщике ресурсов Microsoft.AzureActiveDirectory. |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.AzureStack/Operations/read | Получение свойств операции поставщика ресурсов. |
> | Действие | Microsoft.AzureStack/register/action | Регистрация подписки в поставщике ресурсов Microsoft.AzureStack. |
> | Действие | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Удаление подписки клиента Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/customerSubscriptions/read | Получение свойств подписки клиента Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/customerSubscriptions/write | Создание или обновление подписки клиента Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/delete | Удаление регистрации в Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/getActivationKey/action | Получение последнего созданного ключа активации Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/products/listDetails/action | Возвращает подробные сведения о продукте из Azure Stack Marketplace. |
> | Действие | Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
> | Действие | Microsoft.AzureStack/registrations/read | Получает свойства регистрации Azure Stack. |
> | Действие | Microsoft.AzureStack/registrations/write | Создание или обновление регистрации в Azure Stack. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Batch/batchAccounts/applications/delete | Удаляет приложение. |
> | Действие | Microsoft.Batch/batchAccounts/applications/read | Выводит список приложений или возвращает свойства приложения. |
> | Действие | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Активирует пакет приложения. |
> | Действие | Microsoft.Batch/batchAccounts/applications/versions/delete | Удаляет пакет приложения. |
> | Действие | Microsoft.Batch/batchAccounts/applications/versions/read | Возвращает свойства пакета приложения. |
> | Действие | Microsoft.Batch/batchAccounts/applications/versions/write | Создает новый пакет приложения или обновляет существующий. |
> | Действие | Microsoft.Batch/batchAccounts/applications/write | Создает новое приложение или обновляет существующее. |
> | Действие | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Получение результатов длительных операций с сертификатом в учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Отмена неудачного удаления сертификата в учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/certificates/delete | Удаление сертификата из учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/certificates/read | Вывод списка сертификатов в учетной записи пакетной службы или получение свойств сертификата. |
> | Действие | Microsoft.Batch/batchAccounts/certificates/write | Создание нового сертификата в учетной записи пакетной службы или обновление существующего сертификата. |
> | Действие | Microsoft.Batch/batchAccounts/delete | Удаляет учетную запись пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/listkeys/action | Отображает ключи доступа для учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/operationResults/read | Получение результатов длительных операций в учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/poolOperationResults/read | Получение результатов длительных операций с пулом в учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/pools/delete | Удаление пула из учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Отключение автоматического масштабирования в пуле учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/pools/read | Вывод списка пулов в учетной записи пакетной службы или свойств пула. |
> | Действие | Microsoft.Batch/batchAccounts/pools/stopResize/action | Остановка текущей операции изменения размера в пуле учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Обновление операционной системы в пуле учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/pools/write | Создание нового пула в учетной записи пакетной службы или обновление существующего пула. |
> | Действие | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/read | Выводит список учетных записей пакетной службы или возвращает свойства учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/regeneratekeys/action | Повторно создает ключи доступа для учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Синхронизирует ключи доступа для автоматической учетной записи хранения, настроенной для учетной записи пакетной службы. |
> | Действие | Microsoft.Batch/batchAccounts/write | Создает новую учетную запись пакетной службы или обновляет существующую. |
> | Действие | Microsoft.Batch/locations/checkNameAvailability/action | Проверка того, является ли имя учетной записи допустимым и неиспользуемым. |
> | Действие | Microsoft.Batch/locations/quotas/read | Возвращает квоты пакетной службы для указанной подписки в заданном регионе Azure. |
> | Действие | Microsoft.Batch/register/action | Регистрирует подписку для поставщика ресурсов пакетной службы и позволяет создавать учетные записи пакетной службы. |
> | Действие | Microsoft.Batch/unregister/action | Отмена регистрации подписки для поставщика ресурсов пакетной службы, которая не позволяет создавать учетные записи пакетной службы. |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.BatchAI/clusters/delete | Удаление кластера Batch AI. |
> | Действие | Microsoft.BatchAI/clusters/read | Вывод списка кластеров Batch AI или получение свойств кластера. |
> | Действие | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Получение сведений об удаленном входе для кластера Batch AI. |
> | Действие | Microsoft.BatchAI/clusters/write | Создание нового кластера Batch AI или обновление существующего. |
> | Действие | Microsoft.BatchAI/fileservers/delete | Удаление файлового сервера Batch AI. |
> | Действие | Microsoft.BatchAI/fileservers/read | Вывод списка файловых серверов Batch AI или получение свойств такого сервера. |
> | Действие | Microsoft.BatchAI/fileservers/resume/action | Возобновление работы файлового сервера Batch AI. |
> | Действие | Microsoft.BatchAI/fileservers/suspend/action | Приостановка работы файлового сервера Batch AI. |
> | Действие | Microsoft.BatchAI/fileservers/write | Создание нового файлового сервера Batch AI или обновление существующего. |
> | Действие | Microsoft.BatchAI/jobs/delete | Удаление задания Batch AI. |
> | Действие | Microsoft.BatchAI/jobs/read | Вывод списка заданий Batch AI или получение свойств такого задания. |
> | Действие | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Получение сведений об удаленном входе для задания Batch AI. |
> | Действие | Microsoft.BatchAI/jobs/terminate/action | Завершение задания Batch AI. |
> | Действие | Microsoft.BatchAI/jobs/write | Создание нового задания Batch AI или обновление существующего. |
> | Действие | Microsoft.BatchAI/register/action | Регистрация подписки поставщика ресурсов Batch AI и разрешение создания ресурсов. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Billing/billingPeriods/read | Вывод списка доступных интервалов выставления счетов. |
> | Действие | Microsoft.Billing/invoices/read | Выводит список доступных счетов. |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.BingMaps/mapApis/Delete | Операция удаления. |
> | Действие | Microsoft.BingMaps/mapApis/listSecrets/action | Отображает секреты. |
> | Действие | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Считывает маркер авторизации единого входа для ресурса. |
> | Действие | Microsoft.BingMaps/mapApis/Read | Операция чтения. |
> | Действие | Microsoft.BingMaps/mapApis/regenerateKey/action | Повторно создает ключ. |
> | Действие | Microsoft.BingMaps/mapApis/Write | Операции записи. |
> | Действие | Microsoft.BingMaps/Operations/read | Описание операции. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Cache/checknameavailability/action | Проверяет, доступно ли имя для нового кэша Redis. |
> | Действие | Microsoft.Cache/locations/operationresults/read | Получение результата длительных операций, для которых заголовок Location был ранее возвращен клиенту. |
> | Действие | Microsoft.Cache/operations/read | Вывод списка операций, которые поддерживает поставщик Microsoft.Cache. |
> | Действие | Microsoft.Cache/redis/delete | Удаляет весь кэш Redis. |
> | Действие | Microsoft.Cache/redis/export/action | Экспортирует данные Redis в большие двоичные объекты с префиксом в службе хранилища в указанном формате. |
> | Действие | Microsoft.Cache/redis/firewallRules/delete | Удаляет правила IP-адресов брандмауэра для кэша Redis. |
> | Действие | Microsoft.Cache/redis/firewallRules/read | Возвращает правила IP-адресов брандмауэра для кэша Redis. |
> | Действие | Microsoft.Cache/redis/firewallRules/write | Изменяет правила IP-адресов брандмауэра для кэша Redis. |
> | Действие | Microsoft.Cache/redis/forceReboot/action | Принудительно перезапускает экземпляр кэша. При этом возможна потеря данных. |
> | Действие | Microsoft.Cache/redis/import/action | Импортирует в Redis данные в указанном формате из нескольких больших двоичных объектов. |
> | Действие | Microsoft.Cache/redis/linkedservers/delete | Удаляет связанный сервер из кэша Redis. |
> | Действие | Microsoft.Cache/redis/linkedservers/read | Возвращает связанные серверы для кэша Redis. |
> | Действие | Microsoft.Cache/redis/linkedservers/write | Добавляет связанный сервер для кэша Redis. |
> | Действие | Microsoft.Cache/redis/listKeys/action | Отображает значения ключей доступа к кэшу Redis на портале управления. |
> | Действие | Microsoft.Cache/redis/listUpgradeNotifications/read | Выводит список последних уведомлений об обновлениях для клиента кэша. |
> | Действие | Microsoft.Cache/redis/metricDefinitions/read | Возвращает доступные метрики для кэша Redis. |
> | Действие | Microsoft.Cache/redis/patchSchedules/delete | Удаляет расписание исправления для кэша Redis. |
> | Действие | Microsoft.Cache/redis/patchSchedules/read | Возвращает расписание исправления для кэша Redis. |
> | Действие | Microsoft.Cache/redis/patchSchedules/write | Изменяет расписание исправления для кэша Redis. |
> | Действие | Microsoft.Cache/redis/read | Отображает параметры и конфигурацию кэша Redis на портале управления. |
> | Действие | Microsoft.Cache/redis/regenerateKey/action | Изменяет значения ключей доступа к кэшу Redis на портале управления. |
> | Действие | Microsoft.Cache/redis/start/action | Запускает экземпляр кэша. |
> | Действие | Microsoft.Cache/redis/stop/action | Останавливает экземпляр кэша. |
> | Действие | Microsoft.Cache/redis/write | Изменяет параметры и конфигурацию кэша Redis на портале управления. |
> | Действие | Microsoft.Cache/register/action | Регистрирует поставщик ресурсов Microsoft.Cache для подписки. |
> | Действие | Microsoft.Cache/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.Cache для подписки. |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Capacity/register/action | Регистрация поставщика ресурсов емкости и разрешение создания ресурсов емкости. |
> | Действие | Microsoft.Capacity/reservationorders/action | Обновление резервирования. |
> | Действие | Microsoft.Capacity/reservationorders/delete | Удаление резервирования. |
> | Действие | Microsoft.Capacity/reservationorders/read | Чтение всех резервирований. |
> | Действие | Microsoft.Capacity/reservationorders/reservations/action | Обновление резервирования. |
> | Действие | Microsoft.Capacity/reservationorders/reservations/delete | Удаление резервирования. |
> | Действие | Microsoft.Capacity/reservationorders/reservations/read | Чтение всех резервирований. |
> | Действие | Microsoft.Capacity/reservationorders/reservations/revisions/read | Чтение всех резервирований. |
> | Действие | Microsoft.Capacity/reservationorders/reservations/write | Создание резервирования. |
> | Действие | Microsoft.Capacity/reservationorders/write | Создание резервирования. |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Действие | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Действие | Microsoft.Cdn/edgenodes/delete |  |
> | Действие | Microsoft.Cdn/edgenodes/read |  |
> | Действие | Microsoft.Cdn/edgenodes/write |  |
> | Действие | Microsoft.Cdn/operationresults/delete |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Действие | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Действие | Microsoft.Cdn/operationresults/read |  |
> | Действие | Microsoft.Cdn/operationresults/write |  |
> | Действие | Microsoft.Cdn/operations/read |  |
> | Действие | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Действие | Microsoft.Cdn/profiles/delete |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики для ресурса. |
> | Действие | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики для ресурса. |
> | Действие | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для Microsoft.Cdn. |
> | Действие | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/read |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Действие | Microsoft.Cdn/profiles/endpoints/write |  |
> | Действие | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Действие | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Действие | Microsoft.Cdn/profiles/read |  |
> | Действие | Microsoft.Cdn/profiles/write |  |
> | Действие | Microsoft.Cdn/register/action | Регистрация подписки для поставщика ресурсов CDN и разрешение создания профилей CDN. |
> | Действие | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Удаляет существующий сертификат. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Возвращает список сертификатов. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Добавляет новый сертификат или обновляет существующий. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/Delete | Удаляет существующий сертификат службы приложений. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/Read | Возвращает список заказов сертификатов. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Повторно отправляет существующий заказ сертификата. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Обновляет существующий заказ сертификата. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Повторно отправляет сообщение сертификата. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Повторно отправляет сообщения запроса на другой электронный адрес. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Извлекает печать сайта для выданного сертификата службы приложений. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Извлекает список действий сертификата. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Извлекает журнал электронных сообщений для сертификата. |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | проверка принадлежности домена; |
> | Действие | Microsoft.CertificateRegistration/certificateOrders/Write | Добавляет новый заказ сертификата или обновляет существующий. |
> | Действие | Microsoft.CertificateRegistration/operations/Read | Вывод списка всех операций из регистрации сертификата службы приложений. |
> | Действие | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Подготавливает субъект-службу для субъекта приложения-службы. |
> | Действие | Microsoft.CertificateRegistration/register/action | Регистрирует поставщик сертификатов Майкрософт для подписки. |
> | Действие | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Проверяет объект покупки сертификата без его отправки. |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ClassicCompute/capabilities/read | Отображает возможности. |
> | Действие | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Проверяет доступность заданного доменного имени. |
> | Действие | Microsoft.ClassicCompute/domainNames/active/write | Задает активное доменное имя. |
> | Действие | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Отображает группу доступности для ресурса. |
> | Действие | Microsoft.ClassicCompute/domainNames/capabilities/read | Отображает возможности доменного имени. |
> | Действие | Microsoft.ClassicCompute/domainNames/delete | Удаляет доменные имена для ресурсов. |
> | Действие | Microsoft.ClassicCompute/domainNames/extensions/delete | Удаляет расширения доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Считывает состояние операции расширений доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/extensions/read | Возвращает расширения доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/extensions/write | Добавляет расширения доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Удаляет внутреннюю подсистему балансировки нагрузки. |
> | Действие | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Считывает состояние операции внутренних подсистем балансировки нагрузки доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Возвращает внутренние подсистемы балансировки нагрузки. |
> | Действие | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Создает внутреннюю подсистему балансировки нагрузки. |
> | Действие | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Считывает состояние операции наборов конечных точек с балансировкой нагрузки доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Отображает наборы конечных точек с балансировкой нагрузки. |
> | Действие | Microsoft.ClassicCompute/domainNames/read | Возвращает доменные имена для ресурсов. |
> | Действие | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Удаляет используемые сертификаты службы. |
> | Действие | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Считывает состояние операции сертификатов службы доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Возвращает используемые сертификаты службы. |
> | Действие | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Добавляет или изменяет используемые сертификаты службы. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/delete | Удаляет заданный слот развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Считывает состояние операции слотов доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/read | Отображает слоты развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Удаляет ссылку на расширение для роли слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Считывает состояние операции ссылок на расширение ролей слотов доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Возвращает ссылку на расширение для роли слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Добавляет или изменяет ссылку на расширение для роли слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Возвращает определения метрик. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/read | Возвращает роль для слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Считывает состояние операции экземпляров роли для ролей слотов доменных имен. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Возвращает экземпляр роли. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Повторная сборка экземпляра роли. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Пересоздает образ экземпляра роли. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Перезапускает экземпляры роли. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/start/action | Запускает слот развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Задает состояние остановки для слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Задает состояние запуска для слота развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/stop/action | Приостанавливает слот развертывания. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Обновляет домен. |
> | Действие | Microsoft.ClassicCompute/domainNames/slots/write | Создает или обновляет развертывание. |
> | Действие | Microsoft.ClassicCompute/domainNames/swap/action | Переключает промежуточный слот на рабочий слот. |
> | Действие | Microsoft.ClassicCompute/domainNames/write | Добавляет или изменяет доменные имена для ресурсов. |
> | Действие | Microsoft.ClassicCompute/moveSubscriptionResources/action | Перемещает все классические ресурсы в другую подписку. |
> | Действие | Microsoft.ClassicCompute/operatingSystemFamilies/read | Вывод списка семейств гостевых операционных систем, доступных в Microsoft Azure, а также версий операционных систем в каждом таком семействе. |
> | Действие | Microsoft.ClassicCompute/operatingSystems/read | Выводит список версий гостевой операционной системы, доступных в Microsoft Azure. |
> | Действие | Microsoft.ClassicCompute/quotas/read | Возвращает квоту для подписки. |
> | Действие | Microsoft.ClassicCompute/register/action | Выполняет регистрацию в классической вычислительной среде. |
> | Действие | Microsoft.ClassicCompute/resourceTypes/skus/read | Возвращает список номеров SKU для поддерживаемых типов ресурсов. |
> | Действие | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Проверяет доступность классической операции перемещения для подписки. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с виртуальной машиной. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с виртуальной машиной. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с виртуальной машиной. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Возвращает возможные асинхронные операции. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Подключает диск данных к виртуальной машине. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/delete | Удаляет виртуальные машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Отключает диск данных от виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/disks/read | Извлекает список дисков данных. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Скачивает RDP-файл для виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Считывает состояние операции расширений виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/extensions/read | Возвращает расширение виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/extensions/write | Размещает расширение виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/metrics/read | Возвращает метрики. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции групп безопасности сети, связанных с виртуальными машинами. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с сетевым интерфейсом. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Считывает состояние операции виртуальных машин. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Выполнение обслуживания виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Возвращает определения метрик. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/read | Извлекает список виртуальных машин. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Повторно развертывает виртуальную машину. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/restart/action | Перезапускает виртуальные машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Завершает работу виртуальной машины. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/start/action | Запустите виртуальную машину. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/stop/action | Останавливает виртуальную машину. |
> | Действие | Microsoft.ClassicCompute/virtualMachines/write | Добавляет или изменяет виртуальные машины. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Извлекает список поддерживаемых устройств. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Удаляет группу безопасности сети. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Считывает состояние операции группы безопасности сети. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/read | Возвращает группу безопасности сети. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Удаляет правило безопасности. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Считывает состояние операции правил безопасности группы безопасности сети. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Возвращает правило безопасности. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Добавляет или обновляет правило безопасности. |
> | Действие | Microsoft.ClassicNetwork/networkSecurityGroups/write | Добавляет новую группу безопасности сети. |
> | Действие | Microsoft.ClassicNetwork/quotas/read | Возвращает квоту для подписки. |
> | Действие | Microsoft.ClassicNetwork/register/action | Выполняет регистрацию в классической сети. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/delete | Удаляет зарезервированный IP-адрес. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/join/action | Присоединяет зарезервированный IP-адрес. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/link/action | Привязывает зарезервированный IP-адрес. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Считывает состояние операции для зарезервированных IP-адресов. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/read | Возвращает зарезервированные IP-адреса. |
> | Действие | Microsoft.ClassicNetwork/reservedIps/write | Добавляет новый зарезервированный IP-адрес. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Отображает возможности. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Проверяет доступность заданного IP-адреса в виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/delete | Удаляет виртуальную сеть. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Отменяет отмену сертификата клиента. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Считывает отозванные сертификаты клиента. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Отменяет сертификат клиента. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Удаляет сертификат клиента шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Скачивает сертификат по отпечатку. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Отображает пакет сертификата шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Выполняет поиск корневых сертификатов клиента. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Передает новый корневой сертификат клиента. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Устанавливает подключение шлюза типа "сеть — сеть". |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Отключает подключение шлюза типа "сеть — сеть". |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Извлекает список подключений. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Тестирует подключение шлюза типа "сеть — сеть". |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Удаляет шлюз виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Скачивает сценарий конфигурации устройства. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Скачивает данные диагностики шлюза. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Извлекает ключ службы канала. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Отображает пакет шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Считывает состояние операции шлюзов виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Возвращает пакет шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Возвращает шлюзы виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Запускает диагностику шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Останавливает диагностику шлюза виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Добавляет шлюз виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Считывает состояние операции виртуальных сетей. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Создает пиринг между виртуальными сетями. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Удаляет группу безопасности сети, связанную с подсетью. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Считывает состояние операции группы безопасности сети, связанной с подсетью виртуальной сети. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Возвращает группу безопасности сети, связанную с подсетью. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Добавляет группу безопасности сети, связанную с подсетью. |
> | Действие | Microsoft.ClassicNetwork/virtualNetworks/write | Добавляет новую виртуальную сеть. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ClassicStorage/capabilities/read | Отображает возможности. |
> | Действие | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Проверяет доступность учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/disks/read | Возвращает диск в учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/images/read | Возвращает образ. |
> | Действие | Microsoft.ClassicStorage/osImages/read | Возвращает образ операционной системы. |
> | Действие | Microsoft.ClassicStorage/publicImages/read | Возвращает общедоступный образ виртуальной машины. |
> | Действие | Microsoft.ClassicStorage/quotas/read | Возвращает квоту для подписки. |
> | Действие | Microsoft.ClassicStorage/register/action | Выполняет регистрацию в классической службе хранилища. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/delete | Удаляет учетную запись хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/disks/delete | Удаляет заданный диск из учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/disks/write | Добавляет диск учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/images/delete | Удаляет заданный образ из учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Удаляет заданный образ операционной системы из учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/osImages/read | Возвращает заданный образ операционной системы в учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Возвращает параметры диагностики. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Добавляет или изменяет параметры диагностики. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Возвращает определения метрик. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Возвращает метрики. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/services/read | Возвращает доступные службы. |
> | Действие | Microsoft.ClassicStorage/storageAccounts/write | Добавляет новую учетную запись хранения. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.CognitiveServices/accounts/delete | Удаляет учетные записи API. |
> | Действие | Microsoft.CognitiveServices/accounts/listKeys/action | Отображает ключи. |
> | Действие | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для Cognitive Services. |
> | Действие | Microsoft.CognitiveServices/accounts/read | Считывает учетные записи API. |
> | Действие | Microsoft.CognitiveServices/accounts/regenerateKey/action | Повторно создает ключ. |
> | Действие | Microsoft.CognitiveServices/accounts/skus/read | Считывает доступные номера SKU для существующего ресурса. |
> | Действие | Microsoft.CognitiveServices/accounts/usages/read | Возвращает данные об использовании квоты для существующего ресурса. |
> | Действие | Microsoft.CognitiveServices/accounts/write | Записывает учетные записи API. |
> | Действие | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Чтение доступных номеров SKU для подписки. |
> | Действие | Microsoft.CognitiveServices/Operations/read | Вывод списка всех доступных операций. |
> | Действие | Microsoft.CognitiveServices/register/action | Регистрация подписки для Cognitive Services. |
> | Действие | Microsoft.CognitiveServices/skus/read | Чтение доступных номеров SKU для Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Commerce/RateCard/read | Возвращает данные предложения, метаданные ресурсов и единиц измерения и тарифы для заданной подписки. |
> | Действие | Microsoft.Commerce/UsageAggregates/read | Извлекает данные о потреблении ресурсов Microsoft Azure по подписке. Результат содержит статистические данные об использовании, а также сведения о подписке и ресурсах за определенный диапазон времени. |

## <a name="microsoftcompute"></a>Microsoft.Compute;

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Compute/availabilitySets/delete | Удаляет группу доступности. |
> | Действие | Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
> | Действие | Microsoft.Compute/availabilitySets/vmSizes/read | Выводит список доступных размеров для создания или обновления виртуальной машины в группе доступности. |
> | Действие | Microsoft.Compute/availabilitySets/write | Создает новую группу доступности или обновляет существующую. |
> | Действие | Microsoft.Compute/disks/beginGetAccess/action | Возвращает универсальный код ресурса (URI) SAS диска для доступа к большому двоичному объекту. |
> | Действие | Microsoft.Compute/disks/delete | Удаляет диск. |
> | Действие | Microsoft.Compute/disks/endGetAccess/action | Отменяет универсальный код ресурса (URI) SAS диска. |
> | Действие | Microsoft.Compute/disks/read | Возвращает свойства диска. |
> | Действие | Microsoft.Compute/disks/write | Создает новый диск или обновляет существующий. |
> | Действие | Microsoft.Compute/images/delete | Удаляет кластер. |
> | Действие | Microsoft.Compute/images/read | Возвращает свойства образа. |
> | Действие | Microsoft.Compute/images/write | Создает новый образ или обновляет существующий. |
> | Действие | Microsoft.Compute/locations/capsOperations/read | Получение состояния асинхронной операции ограничения. |
> | Действие | Microsoft.Compute/locations/diskOperations/read | Получение состояния асинхронной операции диска. |
> | Действие | Microsoft.Compute/locations/operations/read | Возвращает состояние асинхронной операции. |
> | Действие | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Получение свойств предложения образа платформы. |
> | Действие | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Получение свойств номера Sku образа платформы. |
> | Действие | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Получение свойств версии образа платформы. |
> | Действие | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Получение свойств типа расширения виртуальной машины. |
> | Действие | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Получение свойств версии расширения виртуальной машины. |
> | Действие | Microsoft.Compute/locations/publishers/read | Получение свойств издателя. |
> | Действие | Microsoft.Compute/locations/runCommands/read | Вывод списка доступных команд запуска в расположении. |
> | Действие | Microsoft.Compute/locations/usages/read | Возвращает ограничения службы и текущее количество использованных вычислительных ресурсов подписки в расположении. |
> | Действие | Microsoft.Compute/locations/vmSizes/read | Выводит список доступных размеров виртуальных машин в расположении. |
> | Действие | Microsoft.Compute/operations/read | Выводит список операций, доступных в поставщике ресурсов Microsoft.Compute. |
> | Действие | Microsoft.Compute/register/action | Регистрирует подписку в поставщике ресурсов Microsoft.Compute. |
> | Действие | Microsoft.Compute/restorePointCollections/delete | Удаляет коллекцию точек восстановления и содержащиеся в ней точки восстановления. |
> | Действие | Microsoft.Compute/restorePointCollections/read | Возвращает свойства коллекции точек восстановления. |
> | Действие | Microsoft.Compute/restorePointCollections/restorePoints/delete | Удаляет точку восстановления. |
> | Действие | Microsoft.Compute/restorePointCollections/restorePoints/read | Возвращает свойства точки восстановления. |
> | Действие | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Возвращает свойства точки восстановления вместе с универсальными кодами ресурсов (URI) SAS больших двоичных объектов. |
> | Действие | Microsoft.Compute/restorePointCollections/restorePoints/write | Создает точку восстановления. |
> | Действие | Microsoft.Compute/restorePointCollections/write | Создает новую коллекцию точек восстановления или обновляет существующую. |
> | Действие | Microsoft.Compute/sharedVMImages/delete | Удаление образа общей виртуальной машины. |
> | Действие | Microsoft.Compute/sharedVMImages/read | Получение свойств образа общей виртуальной машины. |
> | Действие | Microsoft.Compute/sharedVMImages/versions/delete | Удаление версии образа общей виртуальной машины. |
> | Действие | Microsoft.Compute/sharedVMImages/versions/read | Получение свойств версии образа общей виртуальной машины. |
> | Действие | Microsoft.Compute/sharedVMImages/versions/replicate/action | Репликация версии образа общей виртуальной машины в целевые регионы. |
> | Действие | Microsoft.Compute/sharedVMImages/versions/write | Создание новой версии образа общей виртуальной машины или обновление существующей. |
> | Действие | Microsoft.Compute/sharedVMImages/write | Создание нового образа общей виртуальной машины или обновление существующего. |
> | Действие | Microsoft.Compute/skus/read | Получение списка номеров SKU Microsoft.Compute для вашей подписки. |
> | Действие | Microsoft.Compute/snapshots/beginGetAccess/action | Получение универсального кода ресурса (URI) SAS моментального снимка для доступа к большому двоичному объекту. |
> | Действие | Microsoft.Compute/snapshots/delete | Удаляет моментальный снимок. |
> | Действие | Microsoft.Compute/snapshots/endGetAccess/action | Отмена универсального кода ресурса (URI) SAS моментального снимка. |
> | Действие | Microsoft.Compute/snapshots/read | Возвращает свойства моментального снимка. |
> | Действие | Microsoft.Compute/snapshots/write | Создает новый моментальный снимок или обновляет существующий. |
> | Действие | Microsoft.Compute/virtualMachines/capture/action | Записывает виртуальную машину, копируя ее виртуальных жесткие диски, и создает шаблон, который может использоваться для создания идентичных виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Преобразует диски на основе больших двоичных объектов виртуальной машины в управляемые диски. |
> | Действие | Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
> | Действие | Microsoft.Compute/virtualMachines/delete | Удаляет виртуальную машину. |
> | Действие | Microsoft.Compute/virtualMachines/extensions/delete | Удаляет расширение виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/extensions/read | Возвращает свойства расширения виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/extensions/write | Создает новое расширение виртуальной машины или обновляет существующее. |
> | Действие | Microsoft.Compute/virtualMachines/generalize/action | Задает состояние "Универсальная" для виртуальной машины и подготавливает ее для записи. |
> | Действие | Microsoft.Compute/virtualMachines/instanceView/read | Возвращает состояние выполнения виртуальной машины и ее ресурсов. |
> | Действие | Microsoft.Compute/virtualMachines/performMaintenance/action | Выполнение операции обслуживания виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/powerOff/action | Завершает работу виртуальной машины. Обратите внимание, что плата за виртуальную машину по-прежнему будет взиматься. |
> | Действие | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Чтение определений метрик виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/redeploy/action | Повторно развертывает виртуальную машину. |
> | Действие | Microsoft.Compute/virtualMachines/reimage/action | Пересоздание образа виртуальной машины, которая использует разностный диск. |
> | Действие | Microsoft.Compute/virtualMachines/restart/action | Перезапускает виртуальную машину. |
> | Действие | Microsoft.Compute/virtualMachines/runCommand/action | Выполнение предопределенного скрипта на виртуальной машине. |
> | Действие | Microsoft.Compute/virtualMachines/start/action | Запускает виртуальную машину. |
> | Действие | Microsoft.Compute/virtualMachines/vmSizes/read | Выводит список доступных размеров для обновления виртуальной машины. |
> | Действие | Microsoft.Compute/virtualMachines/write | Создает новую виртуальную машину или обновляет существующую. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Завершение работы вычислительных ресурсов и их освобождение для экземпляров виртуальной машины в масштабируемом наборе виртуальных машин.  |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/delete | Удаление масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/delete/action | Удаление экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Удаление расширения масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Получение свойств расширения масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Создание расширения масштабируемого набора виртуальных машин или обновление существующего. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Ручное выполнение зависшего обновления доменов обновления платформы в масштабируемом наборе виртуальных машин Service Fabric. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Получение представления экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Выполнение ручного обновления экземпляров до последней модели масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Получение свойства всех сетевых интерфейсов масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Получение журнала обновлений ОС для масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Выполнение планового обслуживания экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Завершение работы экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Чтение определений метрик масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Получение свойств всех общедоступных IP-адресов масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/read | Получение свойств масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Повторное развертывание экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Повторное создание образов экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/restart/action | Перезапуск экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Отмена последовательного обновления масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Получение состояния последнего последовательного обновления масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/scale/action | Проверка того, возможно ли увеличение или уменьшение числа экземпляров масштабируемого набора виртуальных машин до указанного значения. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/skus/read | Вывод списка действительных номеров SKU для существующего масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/start/action | Запуск экземпляров масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Завершает работу вычислительных ресурсов и освобождает их для виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Удаляет конкретную виртуальную машину в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Извлекает представление экземпляров виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Получение свойства общедоступного IP-адреса, созданного с использованием масштабируемого набора виртуальных машин. Масштабируемый набор виртуальных машин может создать не более одного общедоступного IP-адреса на IP-конфигурацию (частный IP-адрес). |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Получение свойств одной или всех IP-конфигураций сетевого интерфейса, созданного с помощью масштабируемого набора виртуальных машин. IP-конфигурации представляют частные IP-адреса. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Получение свойств одного или всех сетевых интерфейсов виртуальной машины, созданных с помощью масштабируемого набора виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Выполнение планового обслуживания экземпляров виртуальных машин в масштабируемом наборе. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Завершает работу экземпляра виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Чтение определения метрик виртуальной машины в масштабируемом наборе. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Извлекает свойства виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Повторное развертывание образа виртуальной машины в масштабируемом наборе. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Повторное создание образа экземпляра виртуальной машины в масштабируемом наборе. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Перезапускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Запускает экземпляр виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Обновление свойства виртуальной машины в масштабируемом наборе виртуальных машин. |
> | Действие | Microsoft.Compute/virtualMachineScaleSets/write | Создание нового масштабируемого набора виртуальных машин или обновление существующего. |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Consumption/balances/read | Получение списка сводки использования за период выставления счетов для группы управления. |
> | Действие | Microsoft.Consumption/budgets/read | Получение списка бюджетов подписки или группы управления. |
> | Действие | Microsoft.Consumption/budgets/write | Создание, обновление и удаление бюджетов подписки или группы управления. |
> | Действие | Microsoft.Consumption/marketplaces/read | Вывод сведений об использовании ресурсов Marketplace для области подписок EA и WebDirect. |
> | Действие | Microsoft.Consumption/operations/read | Вывод списка всех поддерживаемых операций для поставщика ресурсов Microsoft.Consumption. |
> | Действие | Microsoft.Consumption/pricesheets/read | Вывод расценок для подписки или группы управления. |
> | Действие | Microsoft.Consumption/reservationDetails/read | Вывод сведений об использовании зарезервированных экземпляров по порядку резервирования или группам управления. Данные относятся к одному экземпляру на уровне дня. |
> | Действие | Microsoft.Consumption/reservationRecommendations/read | Вывод одной рекомендации или нескольких общих рекомендаций для зарезервированных экземпляров в подписке. |
> | Действие | Microsoft.Consumption/reservationSummaries/read | Вывод сводки использования зарезервированных экземпляров по порядку резервирования или группам управления. Сводка формируется на ежемесячном или ежедневном уровне. |
> | Действие | Microsoft.Consumption/reservationTransactions/read | Вывод журнала транзакций для зарезервированных экземпляров по группам управления. |
> | Действие | Microsoft.Consumption/terms/read | Вывод условий подписки или группы управления. |
> | Действие | Microsoft.Consumption/usageDetails/read | Вывод сведений об использовании для области подписок EA и WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Получение журналов для конкретного контейнера. |
> | Действие | Microsoft.ContainerInstance/containerGroups/delete | Удаление конкретной группы контейнеров. |
> | Действие | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для группы контейнеров. |
> | Действие | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для группы контейнеров. |
> | Действие | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для группы контейнеров. |
> | Действие | Microsoft.ContainerInstance/containerGroups/read | Получение всех групп контейнеров. |
> | Действие | Microsoft.ContainerInstance/containerGroups/write | Создание или обновление конкретной группы контейнеров. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ContainerRegistry/checkNameAvailability/read | Проверка возможности использования имени реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/locations/operationResults/read | Получение результата асинхронной операции. |
> | Действие | Microsoft.ContainerRegistry/operations/read | Вывод списка всех доступных операций API REST реестра контейнеров Azure. |
> | Действие | Microsoft.ContainerRegistry/register/action | Регистрирует подписку для поставщика ресурсов реестра контейнеров и позволяет создавать реестры контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/delete | Удаление реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Удаление фильтра сетки событий из реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Получение свойств указанного фильтра сетки событий или списка всех фильтров сетки событий для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Создание или обновление фильтра сетки событий для реестра контейнеров с указанными параметрами. |
> | Действие | Microsoft.ContainerRegistry/registries/importImage/action | Импорт образа в реестр контейнеров с указанными параметрами. |
> | Действие | Microsoft.ContainerRegistry/registries/listCredentials/action | Выводит список учетных данных входа для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/listUsages/read | Получение сведений об использовании квот для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/operationStatuses/read | Получение состояния асинхронной операции реестра. |
> | Действие | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для реестра контейнеров Майкрософт. |
> | Действие | Microsoft.ContainerRegistry/registries/read | Получение свойств указанного реестра контейнеров или вывод списка всех реестров контейнеров в указанной группе ресурсов или подписке. |
> | Действие | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Повторное создание учетных данных входа для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/replications/delete | Удаление репликации из реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Получение состояния асинхронной операции репликации. |
> | Действие | Microsoft.ContainerRegistry/registries/replications/read | Получение свойств указанной репликации или списка репликаций для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/replications/write | Создание или обновление репликаций для реестра контейнеров с указанными параметрами. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/delete | Удаление веб-перехватчика из реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Получение конфигурации URI службы и пользовательских заголовков для веб-перехватчика. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Получение списка последних событий для указанного веб-перехватчика. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Получение состояния асинхронной операции веб-перехватчика. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Запуск события проверки связи для отправки веб-перехватчику. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/read | Получение свойств указанного веб-перехватчика или списка веб-перехватчиков для указанного реестра контейнеров. |
> | Действие | Microsoft.ContainerRegistry/registries/webhooks/write | Создание или обновление веб-перехватчика для реестра контейнеров с указанными параметрами. |
> | Действие | Microsoft.ContainerRegistry/registries/write | Создание или обновление реестра контейнеров с указанными параметрами. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ContainerService/containerServices/delete | Удаление службы контейнеров. |
> | Действие | Microsoft.ContainerService/containerServices/read | Получение службы контейнеров. |
> | Действие | Microsoft.ContainerService/containerServices/write | Создание новой службы контейнеров или обновление существующей. |
> | Действие | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Получение профиля доступа управляемого кластера по имени роли с помощью вывода учетных данных. |
> | Действие | Microsoft.ContainerService/managedClusters/accessProfiles/read | Получение профиля доступа управляемого кластера по имени роли. |
> | Действие | Microsoft.ContainerService/managedClusters/delete | Удаление управляемого кластера. |
> | Действие | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для управляемого кластерного ресурса. |
> | Действие | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для управляемого кластерного ресурса. |
> | Действие | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для управляемого кластера. |
> | Действие | Microsoft.ContainerService/managedClusters/read | Получение управляемого кластера. |
> | Действие | Microsoft.ContainerService/managedClusters/write | Создание нового управляемого кластера или обновление имеющегося. |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ContentModerator/applications/delete | Операция удаления. |
> | Действие | Microsoft.ContentModerator/applications/listSecrets/action | Выводит список секретов. |
> | Действие | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Считывает маркеры единого входа. |
> | Действие | Microsoft.ContentModerator/applications/read | Операция чтения. |
> | Действие | Microsoft.ContentModerator/applications/write | Операции записи. |
> | Действие | Microsoft.ContentModerator/applications/write | Операции записи. |
> | Действие | Microsoft.ContentModerator/listCommunicationPreference/action | Отображает параметры связи. |
> | Действие | Microsoft.ContentModerator/operations/read | Считывает операции. |
> | Действие | Microsoft.ContentModerator/updateCommunicationPreference/action | Обновляет параметры связи. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.CustomerInsights/hubs/adobemetadata/action | Создание или обновление метаданных Adobe для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/adobemetadata/read | Чтение метаданных Adobe для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Удаляет политику подписанного URL-адреса Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Считывает политику подписанного URL-адреса Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Повторно создает первичный ключ политики подписанного URL-адреса Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Повторно создает вторичный ключ политики подписанного URL-адреса Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Создает или обновляет политику подписанного URL-адреса Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/activate/action | Активация соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/activate/action | Активация соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/delete | Удаляет соединитель Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Получение состояния выполнения соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Активация сопоставления соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Удаляет сопоставление соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Чтение результата операции сопоставления соединителя для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Считывает сопоставление соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Создает или обновляет сопоставление соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/operations/read | Чтение результата операции соединителя для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/read | Считывает соединитель Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Создание или обновление данных аутентификации соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/update/action | Обновление соединителя Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/connectors/write | Создает или обновляет соединитель Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/crmmetadata/action | Создание или обновление метаданных CRM для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/crmmetadata/read | Чтение метаданных CRM для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/delete | Удаляет концентратор Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/gdpr/delete | Удаление GDPR Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/gdpr/read | Чтение GDPR Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/gdpr/write | Создание или обновление GDPR Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Получение данных о кредитах на концентратор Azure Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Получение журнала выставленных счетов за использование концентратора Azure Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/images/delete | Удаление образа Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/images/read | Чтение образа Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/images/write | Создание или обновление образа Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/interactions/delete | Удаление взаимодействий Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/interactions/operations/read | Чтение результата операции взаимодействия для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/interactions/read | Считывает взаимодействие Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Предложение ссылок связи для взаимодействий Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/interactions/write | Создает или обновляет взаимодействие Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/kpi/delete | Удаляет ключевой показатель эффективности Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/kpi/operations/read | Чтение результата операции получения ключевых показателей эффективности для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/kpi/read | Считывает ключевой показатель эффективности Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Повторная обработка ключевых показателей эффективности Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/kpi/write | Создает или обновляет ключевой показатель эффективности Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/links/delete | Удаление ссылок Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/links/operations/read | Чтение результата операции связей соединителя для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/links/read | Чтение ссылок Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/links/write | Создание или обновление ссылок Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/msemetadata/action | Создание или обновление метаданных MSE для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/msemetadata/read | Чтение метаданных MSE для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/operationresults/read | Получение результатов операции концентратора для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictions/delete | Удаление прогнозов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictions/operations/read | Чтение результата операции получения прогнозов для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictions/read | Чтение прогнозов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictions/write | Создание или обновление прогнозов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Удаление политик прогнозного сопоставления Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Чтение результата операции политик прогнозного сопоставления для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Чтение политик прогнозного сопоставления Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Создание или обновление политик прогнозного сопоставления Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/profiles/delete | Удаление профиля Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/profiles/operations/read | Чтение результата операции профиля для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/profiles/read | Считывает профиль Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/profiles/write | Записывает профиль Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Возвращает доступные журналы для ресурса. |
> | Действие | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Возвращает доступные метрики для ресурса. |
> | Действие | Microsoft.CustomerInsights/hubs/read | Считывает концентратор Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Удаление ссылок связи Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Чтение результата операции ссылок связи для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Чтение ссылок связи Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Создание или обновление ссылок связи Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationships/delete | Удаление связей Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationships/operations/read | Чтение результата операции связей для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationships/read | Чтение связей Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/relationships/write | Создание или обновление связей Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Удаляет назначение RBAC для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Чтение результатов операции назначения RBAC для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/roleAssignments/read | Считывает назначение RBAC для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/roleAssignments/write | Создает или обновляет назначение RBAC для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/roles/read | Чтение ролей RBAC для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Создание или обновление метаданных SalesForce для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Чтение метаданных SalesForce для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/segments/delete | Удаление сегментов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Управление динамическими сегментами Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/segments/read | Чтение сегментов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/segments/static/action | Управление статическими сегментами Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/segments/write | Создание или обновление сегментов Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Удаление строк подключения SQL для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Чтение строк подключения SQL для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Создание или обновление строк подключения SQL для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Создание предложения схемы типа из примера данных. |
> | Действие | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Управление параметрами концентратора Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/views/delete | Удаляет представление приложений Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/views/read | Считывает представление приложений Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/views/write | Создает или обновляет представление приложений Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/widgettypes/read | Чтение типов мини-приложений приложения Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/hubs/write | Создает или обновляет концентратор Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/operations/read | Чтение метаданных API для Azure Customer Insights. |
> | Действие | Microsoft.CustomerInsights/register/action | Регистрация подписки для поставщика ресурсов Customer Insights и разрешение создания ресурсов Customer Insights. |
> | Действие | Microsoft.CustomerInsights/unregister/action | Отмена регистрации подписки для поставщика ресурсов Customer Insights. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Databricks/workspaces/delete | Удаляет рабочую область. |
> | Действие | Microsoft.Databricks/workspaces/read | Извлекает список рабочих областей. |
> | Действие | Microsoft.Databricks/workspaces/write | Создает рабочую область. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DataCatalog/catalogs/delete | Удаляет каталог. |
> | Действие | Microsoft.DataCatalog/catalogs/read | Возвращает свойства каталога или каталогов в подписке или группе ресурсов. |
> | Действие | Microsoft.DataCatalog/catalogs/write | Создает каталог или обновляет теги и свойства каталога. |
> | Действие | Microsoft.DataCatalog/checkNameAvailability/action | Проверяет доступность имени каталога для клиента. |
> | Действие | Microsoft.DataCatalog/operations/read | Получение списка операций, доступных в поставщике ресурсов Microsoft.DataCatalog. |
> | Действие | Microsoft.DataCatalog/register/action | Регистрация подписки в поставщике ресурсов Microsoft.DataCatalog. |
> | Действие | Microsoft.DataCatalog/unregister/action | Отмена регистрации подписки в поставщике ресурсов Microsoft.DataCatalog. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DataFactory/datafactories/activitywindows/read | Считывает окна действий в фабрике данных с указанными параметрами. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Считывает окна действий для действия конвейера с указанными параметрами. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Считывает окна действий для конвейера с указанными параметрами. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/delete | Удаляет конвейер. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Приостанавливает конвейер. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/read | Считывает конвейер. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Возобновляет работу конвейера. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/update/action | Обновляет конвейер. |
> | Действие | Microsoft.DataFactory/datafactories/datapipelines/write | Создает или обновляет конвейер. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Считывает окна действий для набора данных с указанными параметрами. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/delete | Удаляет набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/read | Считывает набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Считывает выполнение среза данных для заданного набора данных с указанным временем запуска. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/slices/read | Возвращает срезы данных за указанный период. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/slices/write | Обновляет состояние среза данных. |
> | Действие | Microsoft.DataFactory/datafactories/datasets/write | Создает или обновляет набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/delete | Удаляет фабрику данных. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Считывает сведения о подключении для шлюза. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/delete | Удаляет шлюз. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Выводит список ключей аутентификации для шлюза. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/read | Считывает шлюз. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Повторно создает ключи аутентификации для шлюза. |
> | Действие | Microsoft.DataFactory/datafactories/gateways/write | Создает или обновляет шлюз. |
> | Действие | Microsoft.DataFactory/datafactories/linkedServices/delete | Удаляет связанную службу. |
> | Действие | Microsoft.DataFactory/datafactories/linkedServices/read | Считывает связанную службу. |
> | Действие | Microsoft.DataFactory/datafactories/linkedServices/write | Создает или обновляет связанную службу. |
> | Действие | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для фабрик данных. |
> | Действие | Microsoft.DataFactory/datafactories/read | Считывает фабрику данных. |
> | Действие | Microsoft.DataFactory/datafactories/runs/loginfo/read | Считывает универсальный код ресурса SAS контейнера больших двоичных объектов, содержащего журналы. |
> | Действие | Microsoft.DataFactory/datafactories/tables/delete | Удаляет набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/tables/read | Считывает набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/tables/write | Создает или обновляет набор данных. |
> | Действие | Microsoft.DataFactory/datafactories/write | Создает или обновляет фабрику данных. |
> | Действие | Microsoft.DataFactory/factories/cancelpipelinerun/action | Отменяет запуск конвейера, указанный идентификатором запуска. |
> | Действие | Microsoft.DataFactory/factories/datasets/delete | Удаляет набор данных. |
> | Действие | Microsoft.DataFactory/factories/datasets/read | Считывает набор данных. |
> | Действие | Microsoft.DataFactory/factories/datasets/write | Создает или обновляет набор данных. |
> | Действие | Microsoft.DataFactory/factories/delete | Удаляет фабрику данных. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/delete | Удаляет среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Считывает сведения о подключении к среде выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Считывает состояние среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Выводит список ключей аутентификации для среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Возвращает данные мониторинга для среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Удаляет узел для указанной среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Возвращает IP-адрес для указанного узла среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Обновляет узел локальной среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/read | Считывает среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Повторно создает ключи проверки подлинности для указанной среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/start/action | Запускает среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Останавливает среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Синхронизирует учетные данные для указанной среды выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Обновляет указанную среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/integrationruntimes/write | Создает или обновляет среду выполнения интеграции. |
> | Действие | Microsoft.DataFactory/factories/linkedServices/delete | Удаляет связанную службу. |
> | Действие | Microsoft.DataFactory/factories/linkedServices/read | Считывает связанную службу. |
> | Действие | Microsoft.DataFactory/factories/linkedServices/write | Создает или обновляет связанную службу. |
> | Действие | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Считывает выполнения действий для указанного идентификатора запуска конвейера. |
> | Действие | Microsoft.DataFactory/factories/pipelineruns/read | Считывает выполнения конвейера. |
> | Действие | Microsoft.DataFactory/factories/pipelines/createrun/action | Создает выполнение для конвейера. |
> | Действие | Microsoft.DataFactory/factories/pipelines/delete | Удаляет конвейер. |
> | Действие | Microsoft.DataFactory/factories/pipelines/read | Считывает конвейер. |
> | Действие | Microsoft.DataFactory/factories/pipelines/write | Создает или обновляет конвейер. |
> | Действие | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для фабрик данных. |
> | Действие | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для фабрик данных. |
> | Действие | Microsoft.DataFactory/factories/read | Считывает фабрику данных. |
> | Действие | Microsoft.DataFactory/factories/triggers/delete | Удаляет триггер. |
> | Действие | Microsoft.DataFactory/factories/triggers/read | Считывает триггер. |
> | Действие | Microsoft.DataFactory/factories/triggers/start/action | Запускает триггер. |
> | Действие | Microsoft.DataFactory/factories/triggers/stop/action | Останавливает триггер. |
> | Действие | Microsoft.DataFactory/factories/triggers/triggerruns/read | Считывает выполнения триггеров. |
> | Действие | Microsoft.DataFactory/factories/triggers/write | Создает или обновляет триггер. |
> | Действие | Microsoft.DataFactory/factories/write | Создает или обновляет фабрику данных. |
> | Действие | Microsoft.DataFactory/register/action | Регистрирует подписку для поставщика ресурсов фабрики данных. |
> | Действие | Microsoft.DataFactory/unregister/action | Отменяет регистрацию подписки для поставщика ресурсов фабрики данных. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Удаление политики вычислений. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Получение сведений о политике вычислений. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Создание или обновление политики вычислений. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Получение данных о связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/delete | Удаление учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Удаляет правило брандмауэра. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Возвращает сведения о правиле брандмауэра. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Создает или обновляет правило брандмауэра. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Получение результата операции учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/read | Получение сведений о существующей учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Получение списка токенов SAS для контейнеров хранилища учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Получение контейнеров учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Удаление связи учетной записи хранения с учетной записью Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Получение сведений об учетной записи хранения, связанной с учетной записью Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставление разрешений на отмену заданий, отправленных другими пользователями. |
> | Действие | Microsoft.DataLakeAnalytics/accounts/write | Создание или обновление учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/locations/capability/read | Получение сведений о возможностях подписки в отношении использования Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Проверка доступности имени учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/locations/operationResults/read | Получение результата операции учетной записи Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/operations/read | Получение доступных операций Data Lake Analytics. |
> | Действие | Microsoft.DataLakeAnalytics/register/action | Регистрация подписки для Data Lake Analytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DataLakeStore/accounts/delete | Удаление учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Включение хранилища ключей для учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/firewallRules/delete | Удаляет правило брандмауэра. |
> | Действие | Microsoft.DataLakeStore/accounts/firewallRules/read | Возвращает сведения о правиле брандмауэра. |
> | Действие | Microsoft.DataLakeStore/accounts/firewallRules/write | Создает или обновляет правило брандмауэра. |
> | Действие | Microsoft.DataLakeStore/accounts/operationResults/read | Получение результата операции учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики для учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики для учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/read | Получение сведений о существующей учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/accounts/Superuser/action; | Предоставление прав суперпользователя в хранилище Data Lake Store при предоставлении разрешения Microsoft.Authorization/roleAssignments/write. |
> | Действие | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Удаляет доверенный поставщик удостоверений. |
> | Действие | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Возвращает сведения о доверенном поставщике удостоверений. |
> | Действие | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Создает или обновляет доверенный поставщик удостоверений. |
> | Действие | Microsoft.DataLakeStore/accounts/write | Создание или обновление учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/locations/capability/read | Получение сведений о возможностях подписки в отношении использования Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Проверка доступности имени учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/locations/operationResults/read | Получение результата операции учетной записи Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/operations/read | Получение доступных операций Data Lake Store. |
> | Действие | Microsoft.DataLakeStore/register/action | Регистрация подписки для Data Lake Store. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DBforMySQL/locations/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действие | Microsoft.DBforMySQL/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действие | Microsoft.DBforMySQL/servers/delete | Удаление существующего сервера. |
> | Действие | Microsoft.DBforMySQL/servers/firewallRules/delete | Удаление существующего правила брандмауэра. |
> | Действие | Microsoft.DBforMySQL/servers/firewallRules/read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действие | Microsoft.DBforMySQL/servers/firewallRules/write | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действие | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действие | Microsoft.DBforMySQL/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действие | Microsoft.DBforMySQL/servers/recoverableServers/read | Возвращение восстанавливаемых данных MySQL Server. |
> | Действие | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действие | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действие | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действие | Microsoft.DBforMySQL/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действие | Microsoft.DBforPostgreSQL/performanceTiers/read | Получение списка доступных уровней производительности. |
> | Действие | Microsoft.DBforPostgreSQL/servers/delete | Удаление существующего сервера. |
> | Действие | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Удаление существующего правила брандмауэра. |
> | Действие | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Возвращение списка правил брандмауэра для сервера или получение свойств для указанного правила брандмауэра. |
> | Действие | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Создание правила брандмауэра с указанными параметрами или обновление существующего правила. |
> | Действие | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Получает доступные журналы серверов Postgres. |
> | Действие | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действие | Microsoft.DBforPostgreSQL/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действие | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Возвращение восстанавливаемых данных PostgreSQL Server. |
> | Действие | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере. |
> | Действие | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действие | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действие | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действие | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действие | Microsoft.DBforPostgreSQL/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Devices/checkNameAvailability/Action | Проверяет, доступно ли имя Центра Интернета вещей. |
> | Действие | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Проверяет, доступно ли имя Центра Интернета вещей. |
> | Действие | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Удаление ресурса клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Удаляет группу потребителей концентратора событий. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Возвращает группы потребителей концентратора событий. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Создает группу потребителей концентратора событий. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Экспортирует устройства. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Получение ресурса статистики клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Импортирует устройства. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Получение ключа клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Получение ключей клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Получение доступных определений журналов для службы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Возвращает метрики квоты. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/Read | Получение ресурса клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Тестирует сообщение с помощью всех существующих маршрутов. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Тестирует сообщение с помощью предоставленного тестового маршрута. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей. |
> | Действие | Microsoft.Devices/elasticPools/iotHubTenants/Write | Создание или обновление ресурса клиента Центра Интернета вещей. |
> | Действие | Microsoft.Devices/ElasticPools/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/certificates/Delete | Удаляет сертификат. |
> | Действие | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Создание кода проверки. |
> | Действие | Microsoft.Devices/iotHubs/certificates/Read | Возвращает сертификат. |
> | Действие | Microsoft.Devices/iotHubs/certificates/verify/Action | Проверка ресурса сертификата. |
> | Действие | Microsoft.Devices/iotHubs/certificates/Write | Создание или обновление сертификата. |
> | Действие | Microsoft.Devices/iotHubs/Delete | Удаляет ресурс Центра Интернета вещей. |
> | Действие | Microsoft.Devices/IotHubs/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/IotHubs/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Удаление фильтра сетки событий. |
> | Действие | Microsoft.Devices/iotHubs/eventGridFilters/Read | Получение фильтра сетки событий. |
> | Действие | Microsoft.Devices/iotHubs/eventGridFilters/Write | Создание фильтра сетки событий или обновление существующего. |
> | Действие | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Удаляет группу потребителей концентратора событий. |
> | Действие | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Возвращает группы потребителей концентратора событий. |
> | Действие | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Создает группу потребителей концентратора событий. |
> | Действие | Microsoft.Devices/iotHubs/exportDevices/Action | Экспортирует устройства. |
> | Действие | Microsoft.Devices/iotHubs/importDevices/Action | Импортирует устройства. |
> | Действие | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Возвращает ключ Центра Интернета вещей для указанного имени. |
> | Действие | Microsoft.Devices/iotHubs/iotHubStats/Read | Возвращает статистику Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/jobs/Read | Возвращает сведения о заданиях, отправленных в заданный Центр Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/listkeys/Action | Возвращает все ключи Центра Интернета вещей. |
> | Действие | Microsoft.Devices/IotHubs/logDefinitions/read | Получение доступных определений журналов для службы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/IotHubs/metricDefinitions/read | Возвращает доступные метрики для службы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/quotaMetrics/Read | Возвращает метрики квоты. |
> | Действие | Microsoft.Devices/iotHubs/Read | Возвращает ресурсы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/routing/$testall/Action | Тестирует сообщение с помощью всех существующих маршрутов. |
> | Действие | Microsoft.Devices/iotHubs/routing/$testnew/Action | Тестирует сообщение с помощью предоставленного тестового маршрута. |
> | Действие | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Возвращает сведения о работоспособности всех конечных точек маршрутизации для Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/skus/Read | Возвращает допустимые номера SKU Центра Интернета вещей. |
> | Действие | Microsoft.Devices/iotHubs/Write | Создает или обновляет ресурс Центра Интернета вещей. |
> | Действие | Microsoft.Devices/operations/Read | Возвращает все операции поставщика ресурсов. |
> | Действие | Microsoft.Devices/provisioningServices/certificates/Delete | Удаляет сертификат. |
> | Действие | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Создание кода проверки. |
> | Действие | Microsoft.Devices/provisioningServices/certificates/Read | Возвращает сертификат. |
> | Действие | Microsoft.Devices/provisioningServices/certificates/verify/Action | Проверка ресурса сертификата. |
> | Действие | Microsoft.Devices/provisioningServices/certificates/Write | Создание или обновление сертификата. |
> | Действие | Microsoft.Devices/provisioningServices/Delete | Удаление ресурса службы политики диагностики (DPS) Центра Интернета вещей. |
> | Действие | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Devices/provisioningServices/listkeys/Action | Получение ключей DPS Центра Интернета вещей. |
> | Действие | Microsoft.Devices/provisioningServices/logDefinitions/read | Получение доступных определений журналов для службы подготовки. |
> | Действие | Microsoft.Devices/provisioningServices/metricDefinitions/read | Получение доступных метрик для службы подготовки. |
> | Действие | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Получение ключей DPS Центра Интернета вещей для имени ключа. |
> | Действие | Microsoft.Devices/provisioningServices/Read | Получение ресурса DPS Центра Интернета вещей. |
> | Действие | Microsoft.Devices/provisioningServices/skus/Read | Получение действительных номеров SKU для DPS Центра Интернета вещей. |
> | Действие | Microsoft.Devices/provisioningServices/Write | Создание ресурса DPS для Центра Интернета вещей. |
> | Действие | Microsoft.Devices/register/action | Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/register/action | Регистрирует подписку в поставщике ресурсов Центра Интернета вещей и позволяет создавать ресурсы Центра Интернета вещей. |
> | Действие | Microsoft.Devices/usages/Read | Возвращает данные об использовании подписки для данного поставщика. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DevTestLab/labCenters/delete | Удаление центров лабораторий. |
> | Действие | Microsoft.DevTestLab/labCenters/read | Чтение центров лабораторий. |
> | Действие | Microsoft.DevTestLab/labCenters/write | Добавление или изменение центров лабораторий. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Считывает шаблоны Azure Resource Manager. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Создает шаблон ARM для заданного артефакта, передает необходимые файлы в учетную запись хранения и проверяет созданный артефакт. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Считывает артефакты. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/delete | Удаляет источники артефактов. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/read | Считывает источники артефактов. |
> | Действие | Microsoft.DevTestLab/labs/artifactSources/write | Добавляет или изменяет источники артефактов. |
> | Действие | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории. |
> | Действие | Microsoft.DevTestLab/labs/costs/read | Считывает затраты. |
> | Действие | Microsoft.DevTestLab/labs/costs/write | Добавляет или изменяет затраты. |
> | Действие | Microsoft.DevTestLab/labs/CreateEnvironment/action | Создает виртуальные машины в лаборатории. |
> | Действие | Microsoft.DevTestLab/labs/customImages/delete | Удаляет пользовательские образы. |
> | Действие | Microsoft.DevTestLab/labs/customImages/read | Считывает пользовательские образы. |
> | Действие | Microsoft.DevTestLab/labs/customImages/write | Добавляет или изменяет пользовательские образы. |
> | Действие | Microsoft.DevTestLab/labs/delete | Удаляет лаборатории. |
> | Действие | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Экспортирует данные об использовании ресурсов лаборатории в учетную запись хранения. |
> | Действие | Microsoft.DevTestLab/labs/formulas/delete | Удаляет формулы. |
> | Действие | Microsoft.DevTestLab/labs/formulas/read | Считывает формулы. |
> | Действие | Microsoft.DevTestLab/labs/formulas/write | Добавляет или изменяет формулы. |
> | Действие | Microsoft.DevTestLab/labs/galleryImages/read | Считывает образы из коллекции. |
> | Действие | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Создает универсальный код ресурса (URI) для передачи настраиваемых образов дисков в лабораторию. |
> | Действие | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Импорт виртуальной машины в другую лабораторию. |
> | Действие | Microsoft.DevTestLab/labs/ListVhds/action | Выводит список образов дисков, доступных для создания пользовательского образа. |
> | Действие | Microsoft.DevTestLab/labs/notificationChannels/delete | Удаляет каналы уведомлений. |
> | Действие | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Отправляет уведомление в указанный канал. |
> | Действие | Microsoft.DevTestLab/labs/notificationChannels/read | Считывает каналы уведомлений. |
> | Действие | Microsoft.DevTestLab/labs/notificationChannels/write | Добавляет или изменяет каналы уведомлений. |
> | Действие | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Оценивает политику лаборатории. |
> | Действие | Microsoft.DevTestLab/labs/policySets/policies/delete | Удаляет политики. |
> | Действие | Microsoft.DevTestLab/labs/policySets/policies/read | Считывает политики. |
> | Действие | Microsoft.DevTestLab/labs/policySets/policies/write | Добавляет или изменяет политики. |
> | Действие | Microsoft.DevTestLab/labs/read | Считывает лаборатории. |
> | Действие | Microsoft.DevTestLab/labs/schedules/delete | Удаляет расписания. |
> | Действие | Microsoft.DevTestLab/labs/schedules/Execute/action | Выполняет расписание. |
> | Действие | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Выводит список всех применимых расписаний. |
> | Действие | Microsoft.DevTestLab/labs/schedules/read | Считывает расписания. |
> | Действие | Microsoft.DevTestLab/labs/schedules/write | Добавляет или изменяет расписания. |
> | Действие | Microsoft.DevTestLab/labs/serviceRunners/delete | Удаляет средства выполнения служб. |
> | Действие | Microsoft.DevTestLab/labs/serviceRunners/read | Считывает средства выполнения служб. |
> | Действие | Microsoft.DevTestLab/labs/serviceRunners/write | Добавляет или изменяет средства выполнения служб. |
> | Действие | Microsoft.DevTestLab/labs/users/delete | Удаляет профили пользователей. |
> | Действие | Microsoft.DevTestLab/labs/users/disks/Attach/action | Подключает диск к виртуальной машине и создает аренду для него. |
> | Действие | Microsoft.DevTestLab/labs/users/disks/delete | Удаляет диски. |
> | Действие | Microsoft.DevTestLab/labs/users/disks/Detach/action | Отключает диск от виртуальной машины и прерывает его аренду. |
> | Действие | Microsoft.DevTestLab/labs/users/disks/read | Считывает диски. |
> | Действие | Microsoft.DevTestLab/labs/users/disks/write | Добавляет или изменяет диски. |
> | Действие | Microsoft.DevTestLab/labs/users/environments/delete | Удаляет среды. |
> | Действие | Microsoft.DevTestLab/labs/users/environments/read | Считывает среды. |
> | Действие | Microsoft.DevTestLab/labs/users/environments/write | Добавляет или изменяет среды. |
> | Действие | Microsoft.DevTestLab/labs/users/read | Считывает профили пользователей. |
> | Действие | Microsoft.DevTestLab/labs/users/secrets/delete | Удаляет секреты. |
> | Действие | Microsoft.DevTestLab/labs/users/secrets/read | Считывает секреты. |
> | Действие | Microsoft.DevTestLab/labs/users/secrets/write | Добавляет или изменяет секреты. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Удаление платформ Service Fabric. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Выводит список всех применимых расписаний. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Чтение платформ Service Fabric. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Удаляет расписания. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Выполняет расписание. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Считывает расписания. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Добавляет или изменяет расписания. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Запуск платформы Service Fabric. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Остановка платформы Service Fabric. |
> | Действие | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Добавление или изменение платформ Service Fabric. |
> | Действие | Microsoft.DevTestLab/labs/users/write | Добавляет или изменяет профили пользователей. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Подключает новый или существующий диск данных к виртуальной машине. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Применяет артефакты к виртуальной машине. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Присваивает владение существующей виртуальной машиной. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/delete | Удаляет виртуальные машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Отключает указанный диск от виртуальной машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Выводит список всех применимых расписаний. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/read | Считывает виртуальные машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Повторное развертывание виртуальной машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Изменение размера виртуальной машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Перезапуск виртуальной машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Удаляет расписания. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Выполняет расписание. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Считывает расписания. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Добавляет или изменяет расписания. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Запускает виртуальную машину. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Остановка виртуальной машины |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Передача прав владения дисками данных виртуальной машины себе. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Отказ от владения существующей виртуальной машиной. |
> | Действие | Microsoft.DevTestLab/labs/virtualMachines/write | Добавляет или изменяет виртуальные машины. |
> | Действие | Microsoft.DevTestLab/labs/virtualNetworks/delete | Удаляет виртуальные сети. |
> | Действие | Microsoft.DevTestLab/labs/virtualNetworks/read | Считывает виртуальные сети. |
> | Действие | Microsoft.DevTestLab/labs/virtualNetworks/write | Добавляет или изменяет виртуальные сети. |
> | Действие | Microsoft.DevTestLab/labs/vmPools/delete | Удаление пулов виртуальных машин. |
> | Действие | Microsoft.DevTestLab/labs/vmPools/read | Считывание пулов виртуальных машин. |
> | Действие | Microsoft.DevTestLab/labs/vmPools/write | Добавление или изменение пулов виртуальных машин. |
> | Действие | Microsoft.DevTestLab/labs/write | Добавляет или изменяет лаборатории. |
> | Действие | Microsoft.DevTestLab/locations/operations/read | Считывает операции. |
> | Действие | Microsoft.DevTestLab/register/action | Регистрирует подписку. |
> | Действие | Microsoft.DevTestLab/schedules/delete | Удаляет расписания. |
> | Действие | Microsoft.DevTestLab/schedules/Execute/action | Выполняет расписание. |
> | Действие | Microsoft.DevTestLab/schedules/read | Считывает расписания. |
> | Действие | Microsoft.DevTestLab/schedules/Retarget/action | Обновляет идентификатор целевого ресурса расписания. |
> | Действие | Microsoft.DevTestLab/schedules/write | Добавляет или изменяет расписания. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DocumentDB/databaseAccountNames/read | Проверяет доступность имени. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Изменяет группу ресурсов для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Считывает определения метрик коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Считывает метрики коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Чтение метрик уровня ключа раздела для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Чтение метрик уровня раздела для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Чтение разделов учетной записи базы данных в коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Чтение сведений об использовании уровня раздела для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Считывает данные об использовании коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Считывает определения метрик базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Считывает метрики базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Считывает данные об использовании базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/delete | Удаляет учетные записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Изменяет приоритеты отработки отказа для регионов учетной записи базы данных. Используется для выполнения операции отработки отказа вручную. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Возвращает строки подключения для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Выводит список ключей учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Считывает определения метрик учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/metrics/read | Считывает метрики учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Чтение состояния асинхронной операции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Чтение метрик задержки. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/percentile/read | Чтение процентилей задержек репликации. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Чтение метрик задержки для определенного источника и целевого региона. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Чтение метрик задержки для определенного целевого региона. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Получение категорий доступных журналов для учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/read | Считывает учетную запись базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Считывает ключи только для чтения учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Выполняет ротацию ключей учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Чтение метрик региональной коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Чтение метрик уровня ключа раздела для учетной записи региональной базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Чтение метрик уровня раздела для учетной записи региональной базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Чтение разделов учетной записи региональной базы данных в коллекции. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Чтение метрик учетной записи базы данных и региона. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/usages/read | Считывает данные об использовании учетной записи базы данных. |
> | Действие | Microsoft.DocumentDB/databaseAccounts/write | Обновляет учетные записи базы данных. |
> | Действие | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Уведомление Microsoft.DocumentDB о том, что идет удаление виртуальной сети или подсети. |
> | Действие | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Чтение состояния асинхронной операции удаления виртуальной сети или подсетей. |
> | Действие | Microsoft.DocumentDB/operationResults/read | Чтение состояния асинхронной операции. |
> | Действие | Microsoft.DocumentDB/operations/read | Чтение операций, доступных для Microsoft DocumentDB.  |
> | Действие | Microsoft.DocumentDB/register/action |  Регистрация поставщика ресурсов Microsoft DocumentDB для подписки. |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DomainRegistration/checkDomainAvailability/Action | Проверяет, доступен ли домен для приобретения. |
> | Действие | Microsoft.DomainRegistration/domains/Delete | Удаляет существующий домен. |
> | Действие | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Удаление идентификатора владения. |
> | Действие | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Вывод списка идентификаторов владения. |
> | Действие | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Получение идентификатора владения. |
> | Действие | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Создание или обновление идентификатора. |
> | Действие | Microsoft.DomainRegistration/domains/operationresults/Read | Возвращает операцию домена. |
> | Действие | Microsoft.DomainRegistration/domains/Read | Возвращает список доменов. |
> | Действие | Microsoft.DomainRegistration/domains/Read | Получение домена. |
> | Действие | Microsoft.DomainRegistration/domains/renew/Action | Обновление существующего домена. |
> | Действие | Microsoft.DomainRegistration/domains/Write | Добавляет новый домен или обновляет существующий. |
> | Действие | Microsoft.DomainRegistration/generateSsoRequest/Action | Создает запрос на вход в центр управления доменами. |
> | Действие | Microsoft.DomainRegistration/listDomainRecommendations/Action | Извлекает список рекомендуемых доменов на основе ключевых слов. |
> | Действие | Microsoft.DomainRegistration/operations/Read | Вывод списка всех операций из регистрации домена службы приложений. |
> | Действие | Microsoft.DomainRegistration/register/action | Регистрирует поставщик ресурсов доменов Майкрософт для подписки. |
> | Действие | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Вывод списка соглашений. |
> | Действие | Microsoft.DomainRegistration/topLevelDomains/Read | Получение доменов верхнего уровня. |
> | Действие | Microsoft.DomainRegistration/topLevelDomains/Read | Получение домена верхнего уровня. |
> | Действие | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Проверяет объект покупки домена без его отправки. |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Отображает развертывания Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Создает развертывание Microsoft Dynamics AX 2012 R3 Evaluation в проекте Microsoft Dynamics Lifecycle Services, принадлежащем пользователю. Развертываниями можно управлять на портале управления Azure. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Считывает соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Создает или обновляет соединители, относящиеся к проекту Microsoft Dynamics Lifecycle Services. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/delete | Удаляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/read | Отображает проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю. |
> | Действие | Microsoft.DynamicsLcs/lcsprojects/write | Создает или обновляет проекты Microsoft Dynamics Lifecycle Services, принадлежащие пользователю. Можно обновить только свойства имени и описания. Невозможно обновить подписку и расположение, связанные с проектом, после его создания. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.EventGrid/eventSubscriptions/delete | Удаление подписки на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Получение полного URL-адреса подписки на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для подписок на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Получение или обновление параметра диагностики для подписок на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для подписок на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/read | Чтение подписки на события. |
> | Действие | Microsoft.EventGrid/eventSubscriptions/write | Создание или обновление подписки на события. |
> | Действие | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для разделов. |
> | Действие | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для разделов. |
> | Действие | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для разделов. |
> | Действие | Microsoft.EventGrid/register/action | Регистрация подписки на события для поставщика ресурсов сетки событий и разрешение создавать подписки сетки событий. |
> | Действие | Microsoft.EventGrid/topics/delete | Удаление раздела |
> | Действие | Microsoft.EventGrid/topics/listKeys/action | Вывод списка ключей для раздела. |
> | Действие | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для разделов. |
> | Действие | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для разделов. |
> | Действие | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для разделов. |
> | Действие | Microsoft.EventGrid/topics/read | Чтение раздела. |
> | Действие | Microsoft.EventGrid/topics/regenerateKey/action | Повторное создание ключа для раздела. |
> | Действие | Microsoft.EventGrid/topics/write | Создание или обновление раздела. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.EventHub/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действие | Microsoft.EventHub/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability. |
> | Действие | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик кластера. |
> | Действие | Microsoft.EventHub/clusters/read | Получает описание кластерного ресурса. |
> | Действие | Microsoft.EventHub/clusters/write | Получает описание кластерного ресурса. |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.EventHub/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действие | Microsoft.EventHub/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действие | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Операция обновления концентратора событий. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Операция удаления правил авторизации концентратора событий. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Возвращает строку подключения к концентратору событий. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Возвращает список правил авторизации концентратора событий. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Создает правила авторизации концентратора событий и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действие | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Операция удаления ресурса группы потребителей. |
> | Действие | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Возвращает список описаний ресурсов группы потребителей. |
> | Действие | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Создает или обновляет свойства группы потребителей. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/Delete | Операция удаления ресурса концентратора событий. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/read | Возвращает список описаний ресурсов концентратора событий. |
> | Действие | Microsoft.EventHub/namespaces/eventhubs/write | Создает или обновляет свойства концентратора событий. |
> | Действие | Microsoft.EventHub/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.EventHub/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.EventHub/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает список описаний параметров диагностики пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Возвращает список описаний параметров диагностики пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Возвращает список описаний ресурсов журналов пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действие | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действие | Microsoft.EventHub/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действие | Microsoft.EventHub/operations/read | Получение операций. |
> | Действие | Microsoft.EventHub/register/action | Регистрирует подписку для поставщика ресурсов концентратора событий и позволяет создавать ресурсы концентратора событий. |
> | Действие | Microsoft.EventHub/sku/read | Получение списка описаний ресурсов номеров SKU. |
> | Действие | Microsoft.EventHub/sku/regions/read | Получение списка описаний ресурсов регионов для номеров SKU. |
> | Действие | Microsoft.EventHub/unregister/action | Регистрация поставщика ресурсов концентратора событий. |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Features/features/read | Возвращает функции подписки. |
> | Действие | Microsoft.Features/operations/read | Получает список операций. |
> | Действие | Microsoft.Features/providers/features/read | Возвращает функцию подписки в заданном поставщике ресурсов. |
> | Действие | Microsoft.Features/providers/features/register/action | Регистрирует функцию для подписки в заданном поставщике ресурсов. |
> | Действие | Microsoft.Features/providers/features/unregister/action | Отмена регистрации функции для подписки в заданном поставщике ресурсов. |
> | Действие | Microsoft.Features/register/action | Регистрирует компонент подписки. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.HDInsight/clusters/changerdpsetting/action | Изменяет параметры RDP для кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/configurations/action | Обновляет конфигурацию кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/configurations/read | Возвращает конфигурации кластеров HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/delete | Удаляет кластер HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/read | Возвращает сведения о кластере HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/roles/resize/action | Изменяет размер кластера HDInsight. |
> | Действие | Microsoft.HDInsight/clusters/write | Создает или обновляет кластер HDInsight. |
> | Действие | Microsoft.HDInsight/locations/capabilities/read | Возвращает возможности подписки. |
> | Действие | Microsoft.HDInsight/locations/checkNameAvailability/read | Проверяет доступность имени. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ImportExport/jobs/delete | Удаляет существующее задание. |
> | Действие | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Возвращает ключи BitLocker для указанного задания. |
> | Действие | Microsoft.ImportExport/jobs/read | Возвращает свойства для указанного задания или возвращает список заданий. |
> | Действие | Microsoft.ImportExport/jobs/write | Создает задание с указанными параметрами либо обновляет свойства или теги указанного задания. |
> | Действие | Microsoft.ImportExport/locations/read | Возвращает свойства для указанного расположения или возвращает список расположений. |
> | Действие | Microsoft.ImportExport/register/action | Регистрирует подписку для поставщика ресурсов импорта и экспорта и позволяет создавать задания импорта и экспорта. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Insights/ActionGroups/Delete | Удаление группы действий. |
> | Действие | Microsoft.Insights/ActionGroups/Read | Чтение группы действий. |
> | Действие | Microsoft.Insights/ActionGroups/Write | Запись группы действий. |
> | Действие | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Активирует оповещение журнала действий. |
> | Действие | Microsoft.Insights/ActivityLogAlerts/Delete | Удаление оповещения журнала действий. |
> | Действие | Microsoft.Insights/ActivityLogAlerts/Read | Чтение оповещения журнала действий. |
> | Действие | Microsoft.Insights/ActivityLogAlerts/Write | Чтение оповещения журнала действий. |
> | Действие | Microsoft.Insights/AlertRules/Activated/Action | Правило генерации оповещений активировано. |
> | Действие | Microsoft.Insights/AlertRules/Delete | Удаляет конфигурацию правила генерации оповещений. |
> | Действие | Microsoft.Insights/AlertRules/Incidents/Read | Считывает конфигурацию инцидентов правила генерации оповещений. |
> | Действие | Microsoft.Insights/AlertRules/Read | Считывает конфигурацию правила генерации оповещений. |
> | Действие | Microsoft.Insights/AlertRules/Resolved/Action | Правило генерации оповещений разрешено. |
> | Действие | Microsoft.Insights/AlertRules/Throttled/Action | Правило генерации оповещений отрегулировано. |
> | Действие | Microsoft.Insights/AlertRules/Write | Выполняет запись в конфигурацию правила генерации оповещений. |
> | Действие | Microsoft.Insights/AutoscaleSettings/Delete | Удаляет конфигурацию параметров автомасштабирования. |
> | Действие | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действие | Microsoft.Insights/AutoscaleSettings/Read | Считывает конфигурацию параметров автомасштабирования. |
> | Действие | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Операция автоматического уменьшения масштаба. |
> | Действие | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Операция автоматического увеличения масштаба. |
> | Действие | Microsoft.Insights/AutoscaleSettings/Write | Выполняет запись в конфигурацию параметров автомасштабирования. |
> | Действие | Microsoft.Insights/Components/AnalyticsItems/Delete | Удаление элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsItems/Read | Чтение элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsItems/Write | Запись элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsTables/Action | Действие таблицы аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsTables/Delete | Удаление схемы таблицы аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsTables/Read | Чтение схемы таблицы аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/AnalyticsTables/Write | Запись схемы таблицы аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/Annotations/Delete | Удаление заметки Application Insights. |
> | Действие | Microsoft.Insights/Components/Annotations/Read | Чтение заметки Application Insights. |
> | Действие | Microsoft.Insights/Components/Annotations/Write | Запись заметки Application Insights. |
> | Действие | Microsoft.Insights/Components/Api/Read | Чтение API данных компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/ApiKeys/Action | Создание ключа API Application Insights. |
> | Действие | Microsoft.Insights/Components/ApiKeys/Delete | Удаление ключа API Application Insights. |
> | Действие | Microsoft.Insights/Components/ApiKeys/Read | Чтение ключа API Application Insights. |
> | Действие | Microsoft.Insights/Components/BillingPlanForComponent/Read | Чтение плана выставления счетов для компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Чтение текущих функций выставления счетов для компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Запись текущих функций выставления счетов для компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Чтение конфигурации интеграции управления жизненным циклом приложений по умолчанию для Application Insights. |
> | Действие | Microsoft.Insights/Components/Delete | Удаление конфигурации компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/ExportConfiguration/Action | Действие параметров экспорта Application Insights. |
> | Действие | Microsoft.Insights/Components/ExportConfiguration/Delete | Удаление параметров экспорта Application Insights. |
> | Действие | Microsoft.Insights/Components/ExportConfiguration/Read | Чтение параметров экспорта Application Insights. |
> | Действие | Microsoft.Insights/Components/ExportConfiguration/Write | Запись параметров экспорта Application Insights. |
> | Действие | Microsoft.Insights/Components/ExtendQueries/Read | Чтение результатов расширенного запроса компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/Favorites/Delete | Удаление избранного объекта Application Insights. |
> | Действие | Microsoft.Insights/Components/Favorites/Read | Чтение избранного объекта Application Insights. |
> | Действие | Microsoft.Insights/Components/Favorites/Write | Запись избранного объекта Application Insights. |
> | Действие | Microsoft.Insights/Components/FeatureCapabilities/Read | Чтение возможностей функций компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Чтение доступных функций выставления счетов для компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/GetToken/Read | Чтение токена компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/MetricDefinitions/Read | Чтение определений метрик компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/Metrics/Read | Чтение метрик компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/Move/Action | Перемещение компонента Application Insights в другую группу ресурсов или подписку. |
> | Действие | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Удаление персонального элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/MyAnalyticsItems/Read | Чтение персонального элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/MyAnalyticsItems/Write | Запись персонального элемента аналитики Application Insights. |
> | Действие | Microsoft.Insights/Components/MyFavorites/Read | Чтение персонального избранного объекта Application Insights. |
> | Действие | Microsoft.Insights/Components/PricingPlans/Read | Чтение плана ценообразования компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/PricingPlans/Write | Запись плана ценообразования компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Чтение конфигурации упреждающего обнаружения Application Insights. |
> | Действие | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Запись конфигурации упреждающего обнаружения Application Insights. |
> | Действие | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действие | Microsoft.Insights/Components/QuotaStatus/Read | Чтение состояния квоты компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/Read | Чтение конфигурации компонента Application Insights. |
> | Действие | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Чтение расположений веб-тестов Application Insights. |
> | Действие | Microsoft.Insights/Components/WorkItemConfigs/Delete | Удаление конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действие | Microsoft.Insights/Components/WorkItemConfigs/Read | Чтение конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действие | Microsoft.Insights/Components/WorkItemConfigs/Write | Запись конфигурации интеграции управления жизненным циклом приложений для Application Insights. |
> | Действие | Microsoft.Insights/Components/Write | Запись конфигурации компонента Application Insights. |
> | Действие | Microsoft.Insights/DiagnosticSettings/Delete | Удаляет конфигурацию параметров диагностики. |
> | Действие | Microsoft.Insights/DiagnosticSettings/Read | Считывает конфигурацию параметров диагностики. |
> | Действие | Microsoft.Insights/DiagnosticSettings/Write | Выполняет запись в конфигурацию параметров диагностики. |
> | Действие | Microsoft.Insights/EventCategories/Read | Чтение категории событий. |
> | Действие | Microsoft.Insights/eventtypes/digestevents/Read | Считывает дайджест типов событий управления. |
> | Действие | Microsoft.Insights/eventtypes/values/Read | Считывает значения типов событий управления. |
> | Действие | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Удаляет конфигурацию дополнительных параметров диагностики. |
> | Действие | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Считывает конфигурацию дополнительных параметров диагностики. |
> | Действие | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Выполняет запись в конфигурацию дополнительных параметров диагностики. |
> | Действие | Microsoft.Insights/ListMigrationDate/Action | Возврат даты перевода подписки. |
> | Действие | Microsoft.Insights/ListMigrationDate/Read | Возврат даты перевода подписки. |
> | Действие | Microsoft.Insights/LogDefinitions/Read | Считывает определения журналов. |
> | Действие | Microsoft.Insights/LogProfiles/Delete | Удаляет конфигурацию профилей журнала. |
> | Действие | Microsoft.Insights/LogProfiles/Read | Считывает профили журналов. |
> | Действие | Microsoft.Insights/LogProfiles/Write | Выполняет запись в конфигурацию профиля журнала. |
> | Действие | Microsoft.Insights/MetricAlerts/Delete | Удаление оповещения метрики. |
> | Действие | Microsoft.Insights/MetricAlerts/Read | Чтение оповещения метрики. |
> | Действие | Microsoft.Insights/MetricAlerts/Status/Read | Считывание состояния оповещения метрики. |
> | Действие | Microsoft.Insights/MetricAlerts/Write | Запись оповещения метрики. |
> | Действие | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Считывает определения метрик. |
> | Действие | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Считывает определения метрик. |
> | Действие | Microsoft.Insights/MetricDefinitions/Read | Считывает определения метрик. |
> | Действие | Microsoft.Insights/Metrics/providers/Metrics/Read | Считывает метрики. |
> | Действие | Microsoft.Insights/Metrics/Read | Считывает метрики. |
> | Действие | Microsoft.Insights/Metrics/Write | Запись метрик. |
> | Действие | Microsoft.Insights/MigrateToNewpricingModel/Action | Перевод подписки на новую модель ценообразования. |
> | Действие | Microsoft.Insights/Operations/Read | Чтение операций. |
> | Действие | Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
> | Действие | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Откат подписки к прежней версии модели ценообразования. |
> | Действие | Microsoft.Insights/ScheduledQueryRules/Delete | Удаление запланированного правила запроса. |
> | Действие | Microsoft.Insights/ScheduledQueryRules/Read | Считывание запланированного правила запроса. |
> | Действие | Microsoft.Insights/ScheduledQueryRules/Write | Запись запланированного правила запроса. |
> | Действие | Microsoft.Insights/Tenants/Register/Action | Инициализация поставщика Microsoft Insights. |
> | Действие | Microsoft.Insights/Unregister/Action | Регистрирует поставщик Microsoft Insights. |
> | Действие | Microsoft.Insights/Webtests/Delete | Удаление конфигурации веб-теста. |
> | Действие | Microsoft.Insights/Webtests/GetToken/Read | Чтение токена веб-теста. |
> | Действие | Microsoft.Insights/Webtests/MetricDefinitions/Read | Чтение определений метрик веб-теста. |
> | Действие | Microsoft.Insights/Webtests/Metrics/Read | Чтение метрик веб-теста. |
> | Действие | Microsoft.Insights/Webtests/Read | Чтение конфигурации веб-теста. |
> | Действие | Microsoft.Insights/Webtests/Write | Запись конфигурации веб-теста. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.KeyVault/checkNameAvailability/read | Проверяет, является ли имя Key Vault допустимым и неиспользуемым. |
> | Действие | Microsoft.KeyVault/deletedVaults/read | Отображает свойства обратимо удаленных хранилищ Key Vault. |
> | Действие | Microsoft.KeyVault/hsmPools/delete | Удаление пула HSM. |
> | Действие | Microsoft.KeyVault/hsmPools/joinVault/action | Присоединение хранилища ключей к пулу HSM. |
> | Действие | Microsoft.KeyVault/hsmPools/read | Просмотр свойств пула HSM. |
> | Действие | Microsoft.KeyVault/hsmPools/write | Создание нового пула HSM или обновление свойств существующего пула HSM. |
> | Действие | Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
> | Действие | Microsoft.KeyVault/locations/deletedVaults/read | Отображает свойства обратимо удаленного Key Vault. |
> | Действие | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Уведомление Microsoft.KeyVault о том, что идет удаление виртуальной сети или подсети. |
> | Действие | Microsoft.KeyVault/locations/operationResults/read | Проверяет результат длительной операции. |
> | Действие | Microsoft.KeyVault/operations/read | Получение списка операций, доступных в поставщике ресурсов Microsoft.KeyVault. |
> | Действие | Microsoft.KeyVault/register/action | Регистрирует подписку. |
> | Действие | Microsoft.KeyVault/unregister/action | Отмена регистрации подписки. |
> | Действие | Microsoft.KeyVault/vaults/accessPolicies/write | Обновляет существующую политику доступа путем объединения, замены или добавления новой политики доступа в хранилище. |
> | Действие | Microsoft.KeyVault/vaults/delete | Удаляет Key Vault. |
> | Действие | Microsoft.KeyVault/vaults/deploy/action | Включает доступ к секретам в Key Vault при развертывании ресурсов Azure. |
> | Действие | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Получение доступных журналов для хранилища ключей. |
> | Действие | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для хранилища ключей. |
> | Действие | Microsoft.KeyVault/vaults/read | Отображает свойства Key Vault. |
> | Действие | Microsoft.KeyVault/vaults/secrets/read | Отображает свойства секрета, но не его значение. |
> | Действие | Microsoft.KeyVault/vaults/secrets/write | Создает новый секрет или обновляет значение существующего. |
> | Действие | Microsoft.KeyVault/vaults/write | Создает новое хранилище Key Vault или обновить свойства существующего. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.LabServices/labAccounts/CreateLab/action | Создание лаборатории в учетной записи лаборатории. |
> | Действие | Microsoft.LabServices/labAccounts/delete | Удаление учетных записей лабораторий. |
> | Действие | Microsoft.LabServices/labAccounts/labs/delete | Удаляет лаборатории. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Утверждение случайной среды пользователя в параметрах среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Удаление параметра среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Утверждение среды и назначение ее пользователю. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Удаляет среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Считывает среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Запускает среду, запустив все ресурсы в ней. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Останавливает выполнение среды, остановив все ресурсы в ней. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Добавляет или изменяет среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Подготовка и отзыв необходимых ресурсов для параметра среды на основе текущего состояния параметра лаборатории или среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Чтение параметра среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Запускает шаблон, запустив все ресурсы внутри него. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Запускает шаблон, запустив все ресурсы внутри него. |
> | Действие | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Добавление или изменение параметра среды. |
> | Действие | Microsoft.LabServices/labAccounts/labs/read | Считывает лаборатории. |
> | Действие | Microsoft.LabServices/labAccounts/labs/Register/action | Регистрация в управляемой лаборатории. |
> | Действие | Microsoft.LabServices/labAccounts/labs/users/delete | Удаление пользователей. |
> | Действие | Microsoft.LabServices/labAccounts/labs/users/read | Чтение пользователей. |
> | Действие | Microsoft.LabServices/labAccounts/labs/users/write | Добавление или изменение пользователей. |
> | Действие | Microsoft.LabServices/labAccounts/labs/write | Добавляет или изменяет лаборатории. |
> | Действие | Microsoft.LabServices/labAccounts/read | Чтение учетных записей лабораторий. |
> | Действие | Microsoft.LabServices/labAccounts/sizes/read | Чтение размеров. |
> | Действие | Microsoft.LabServices/labAccounts/write | Добавление или изменение учетных записей лабораторий. |
> | Действие | Microsoft.LabServices/locations/operations/read | Считывает операции. |
> | Действие | Microsoft.LabServices/register/action | Регистрирует подписку. |
> | Действие | Microsoft.LabServices/users/GetEnvironment/action | Получает сведения о виртуальных машинах. |
> | Действие | Microsoft.LabServices/users/ListEnvironments/action | Перечисление сред для пользователя. |
> | Действие | Microsoft.LabServices/users/ListLabs/action | Перечисление лабораторий для пользователя. |
> | Действие | Microsoft.LabServices/users/Register/action | Регистрация пользователя в управляемой лаборатории. |
> | Действие | Microsoft.LabServices/users/StartEnvironment/action | Запускает среду, запустив все ресурсы в ней. |
> | Действие | Microsoft.LabServices/users/StopEnvironment/action | Останавливает выполнение среды, остановив все ресурсы в ней. |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.LocationBasedServices/accounts/delete | (Не рекомендуется: используйте /providers/Microsoft.Maps). Удаление учетной записи Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/accounts/listKeys/action | (Не рекомендуется: используйте /providers/Microsoft.Maps). Вывод списка ключей учетной записи Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Не рекомендуется: используйте /providers/Microsoft.Maps). Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Не рекомендуется: используйте /providers/Microsoft.Maps). Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Не рекомендуется: используйте /providers/Microsoft.Maps). Получает доступные метрики для учетных записей Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/accounts/read | (Не рекомендуется: используйте /providers/Microsoft.Maps). Получение учетной записи Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Не рекомендуется: используйте /providers/Microsoft.Maps). Создание нового первичного или вторичного ключа учетной записи Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/accounts/write | (Не рекомендуется: используйте /providers/Microsoft.Maps). Создание или обновление учетной записи Location Based Services. |
> | Действие | Microsoft.LocationBasedServices/register/action | (Не рекомендуется: используйте /providers/Microsoft.Maps). Регистрация поставщика. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Logic/integrationAccounts/agreements/delete | Удаление соглашения в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого соглашения в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/agreements/read | Чтение соглашения в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/agreements/write | Создание или обновление соглашения в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/assemblies/delete | Удаление сборки в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого сборки в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/assemblies/read | Чтение сборки в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/assemblies/write | Создание или обновление сборки в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Удаление конфигурации пакета в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Чтение конфигурации пакета в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Создание или обновление конфигурации пакета в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/certificates/delete | Удаление сертификата в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/certificates/read | Чтение сертификата в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/certificates/write | Создание или обновление сертификата в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/delete | Удаление учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/join/action | Присоединение к учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Получение URL-адреса обратного вызова для учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Получение ключей в хранилище ключей. |
> | Действие | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Регистрация событий отслеживания в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/maps/delete | Удаление сопоставления в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого сопоставления в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/maps/read | Чтение сопоставления в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/maps/write | Создание или обновление сопоставления в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/partners/delete | Удаление партнера в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого партнера в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/partners/read | Чтение партнера в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/partners/write | Создание или обновление партнера в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Чтение определений журналов для учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/read | Чтение учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Повторно создает секреты ключей доступа. |
> | Действие | Microsoft.Logic/integrationAccounts/schemas/delete | Удаление схемы в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Получение URL-адреса обратного вызова для содержимого схемы в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/schemas/read | Чтение схемы в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/schemas/write | Создание или обновление схемы в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/sessions/delete | Удаление сеанса в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/sessions/read | Чтение конфигурации пакета в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/sessions/write | Создание или обновление сеанса в учетной записи интеграции. |
> | Действие | Microsoft.Logic/integrationAccounts/write | Создание или обновление учетной записи интеграции. |
> | Действие | Microsoft.Logic/locations/workflows/validate/action | Проверяет рабочий процесс. |
> | Действие | Microsoft.Logic/operations/read | Получение операции. |
> | Действие | Microsoft.Logic/register/action | Регистрация поставщика ресурсов Microsoft.Logic для заданной подписки. |
> | Действие | Microsoft.Logic/workflows/accessKeys/delete | Удаляет ключ доступа. |
> | Действие | Microsoft.Logic/workflows/accessKeys/list/action | Выводит список секретов ключей доступа. |
> | Действие | Microsoft.Logic/workflows/accessKeys/read | Считывает ключ доступа. |
> | Действие | Microsoft.Logic/workflows/accessKeys/regenerate/action | Повторно создает секреты ключей доступа. |
> | Действие | Microsoft.Logic/workflows/accessKeys/write | Создает или обновляет ключ доступа. |
> | Действие | Microsoft.Logic/workflows/delete | Удаляет рабочий процесс. |
> | Действие | Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
> | Действие | Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
> | Действие | Microsoft.Logic/workflows/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/listSwagger/action | Возвращает определения Swagger рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/move/action | Назначает рабочему процессу, для которого задан идентификатор существующей подписки, группа ресурсов и (или) имя, другой идентификатор подписки, группу ресурсов и (или) имя. |
> | Действие | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Чтение параметров диагностики рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Чтение определений журналов рабочих процессов. |
> | Действие | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Чтение определений метрик рабочих процессов. |
> | Действие | Microsoft.Logic/workflows/read | Считывает рабочий процесс. |
> | Действие | Microsoft.Logic/workflows/regenerateAccessKey/action | Повторно создает секреты ключей доступа. |
> | Действие | Microsoft.Logic/workflows/run/action | Начинает выполнение рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Получение трассировок выражений для действия запуска рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/actions/read | Считывает действие выполнения рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Получение трассировок выражений повторов для действия запуска рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/actions/repetitions/read | Чтение повтора действия запуска рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Чтение повтора области для действия запуска рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/cancel/action | Отменяет выполнение рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/operations/read | Считывает состояние операции выполнения рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/runs/read | Считывает данные о выполнении рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/suspend/action | Приостановка рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/triggers/histories/read | Считывает журналы триггера. |
> | Действие | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Повторяет отправку триггера рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для триггера. |
> | Действие | Microsoft.Logic/workflows/triggers/read | Считывает триггер. |
> | Действие | Microsoft.Logic/workflows/triggers/reset/action | Сброс триггера. |
> | Действие | Microsoft.Logic/workflows/triggers/run/action | Выполняет триггер. |
> | Действие | Microsoft.Logic/workflows/triggers/setState/action | Настройка состояния триггера. |
> | Действие | Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
> | Действие | Microsoft.Logic/workflows/versions/read | Считывает версию рабочего процесса. |
> | Действие | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Возвращает URL-адрес обратного вызова для триггера. |
> | Действие | Microsoft.Logic/workflows/write | Создает или обновляет рабочий процесс. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Перемещает связь плана предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Считывает связь плана предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/commitmentPlans/delete | Удаляет план предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/commitmentPlans/join/action | Присоединяет план предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/commitmentPlans/read | Считывает план предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/commitmentPlans/write | Создает или обновляет план предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/locations/operationresults/read | Получение результата операции машинного обучения. |
> | Действие | Microsoft.MachineLearning/locations/operationsstatus/read | Получение состояния текущей операции машинного обучения. |
> | Действие | Microsoft.MachineLearning/operations/read | Получение операций машинного обучения. |
> | Действие | Microsoft.MachineLearning/register/action | Регистрирует подписку для поставщика ресурсов веб-службы машинного обучения и позволяет создавать веб-службы. |
> | Действие | Microsoft.MachineLearning/skus/read | Получение номеров SKU плана предложения машинного обучения. |
> | Действие | Microsoft.MachineLearning/webServices/action | Создает свойства региональной веб-службы для поддерживаемых регионов. |
> | Действие | Microsoft.MachineLearning/webServices/delete | Удаляет веб-службу машинного обучения. |
> | Действие | Microsoft.MachineLearning/webServices/listkeys/read | Получает ключи к веб-службе машинного обучения. |
> | Действие | Microsoft.MachineLearning/webServices/read | Считывает веб-службу машинного обучения. |
> | Действие | Microsoft.MachineLearning/webServices/write | Создает или обновляет веб-службу машинного обучения. |
> | Действие | Microsoft.MachineLearning/Workspaces/delete | Удаляет рабочую область машинного обучения. |
> | Действие | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Выводит список ключей для рабочей области машинного обучения. |
> | Действие | Microsoft.MachineLearning/Workspaces/read | Считывает рабочую область машинного обучения. |
> | Действие | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Повторно синхронизирует ключи учетной записи хранения, настроенной для рабочей области машинного обучения. |
> | Действие | Microsoft.MachineLearning/Workspaces/write | Создает или обновляет рабочую область машинного обучения. |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Проверка наличия обновлений для системных служб кластера операционализации. |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Удаление учетной записи размещения. |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Вывод списка ключей, связанных с кластером операционализации. |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/read | Чтение учетной записи размещения. |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Обновление системных служб в кластере операционализации. |
> | Действие | Microsoft.MachineLearningCompute/operationalizationClusters/write | Создание или обновление учетной записи размещения. |
> | Действие | Microsoft.MachineLearningCompute/register/action | Регистрация идентификатора подписки в поставщике ресурсов и разрешение создания вычислительных ресурсов машинного обучения. |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MachineLearningModelManagement/accounts/delete | Удаление учетной записи размещения. |
> | Действие | Microsoft.MachineLearningModelManagement/accounts/read | Чтение учетной записи размещения. |
> | Действие | Microsoft.MachineLearningModelManagement/accounts/write | Создание или обновление учетной записи размещения. |
> | Действие | Microsoft.MachineLearningModelManagement/register/action | Регистрация идентификатора подписки в поставщике ресурсов и разрешение создания учетной записи размещения. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MachineLearningServices/register/action | Регистрирует подписку для поставщика ресурсов служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/computes/delete | Удаляет вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Выводит список секретов для вычислительных ресурсов в рабочей области служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/computes/read | Получает вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/computes/write | Создает или обновляет вычислительные ресурсы в рабочих областях служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/delete | Удаляет рабочие области служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/listKeys/action | Выводит список секретов для рабочей области служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/read | Получает рабочие области служб машинного обучения. |
> | Действие | Microsoft.MachineLearningServices/workspaces/write | Создает или обновляет рабочие области служб машинного обучения. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Действие RBAC для назначения существующего пользовательского удостоверения для ресурса. |
> | Действие | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Удаление существующего пользовательского удостоверения. |
> | Действие | Microsoft.ManagedIdentity/userAssignedIdentities/read | Получение существующего пользовательского удостоверения. |
> | Действие | Microsoft.ManagedIdentity/userAssignedIdentities/write | Создание существующего пользовательского удостоверения или обновление связанных с ним тегов. |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ManagedLab/labAccounts/CreateLab/action | Создание лаборатории в учетной записи лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/delete | Удаление учетных записей лабораторий. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/delete | Удаляет лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Удаление параметра среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Удаляет среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Считывает среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Добавляет или изменяет среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Чтение параметра среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Добавление или изменение параметра среды. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Удаление виртуальных машин лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Чтение виртуальных машин лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Добавление или изменение виртуальных машин лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/read | Считывает лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/labs/write | Добавляет или изменяет лаборатории. |
> | Действие | Microsoft.ManagedLab/labAccounts/read | Чтение учетных записей лабораторий. |
> | Действие | Microsoft.ManagedLab/labAccounts/write | Добавление или изменение учетных записей лабораторий. |
> | Действие | Microsoft.ManagedLab/locations/operations/read | Считывает операции. |
> | Действие | Microsoft.ManagedLab/register/action | Регистрирует подписку. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Management/checkNameAvailability/action | Проверка того, является ли имя указанной группы управления допустимым и уникальным. |
> | Действие | Microsoft.Management/getEntities/action | Вывод списка всех сущностей (групп управления, подписок и т. д.) для прошедшего проверку подлинности пользователя. |
> | Действие | Microsoft.Management/managementGroups/delete | Удаление группы управления. |
> | Действие | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Действие | Microsoft.Management/managementGroups/subscriptions/delete | Отмена связывания подписки с группой управления. |
> | Действие | Microsoft.Management/managementGroups/subscriptions/write | Связывание существующей подписки с группой управления. |
> | Действие | Microsoft.Management/managementGroups/write | Создание или обновление группы управления. |
> | Действие | Microsoft.Management/register/action | Регистрация указанной подписки в Microsoft.Management. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Maps/accounts/delete | Удаление учетной записи Maps. |
> | Действие | Microsoft.Maps/accounts/listKeys/action | Вывод списка ключей учетной записи Maps. |
> | Действие | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для учетных записей Maps. |
> | Действие | Microsoft.Maps/accounts/read | Получение учетной записи Maps. |
> | Действие | Microsoft.Maps/accounts/regenerateKey/action | Создание нового первичного или вторичного ключа учетной записи Maps. |
> | Действие | Microsoft.Maps/accounts/write | Создание или обновление учетной записи Maps. |
> | Действие | Microsoft.Maps/register/action | Регистрация поставщика. |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Возвращает соглашение. |
> | Действие | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Принимает подписанное соглашение. |
> | Действие | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Импортирует образ в запись контроля доступа конечного пользователя. |
> | Действие | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Возвращает файл конфигурации. |
> | Действие | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Сохраняет файл конфигурации. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MarketplaceApps/ClassicDevServices/delete | Выполнение операции DELETE в ресурсе классической службы разработки. |
> | Действие | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Получение ключей управления для ресурса классической службы разработки. |
> | Действие | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Получение URL-адреса единого входа для классической службы разработки. |
> | Действие | Microsoft.MarketplaceApps/ClassicDevServices/read | Выполнение операции GET в классической службе разработки. |
> | Действие | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Создание ключей управления для ресурса классической службы разработки. |
> | Действие | Microsoft.MarketplaceApps/Operations/read | Чтение операций для всех типов ресурсов. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Отменяет соглашение для заданного элемента Marketplace. |
> | Действие | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Возвращает соглашение для заданного элемента Marketplace. |
> | Действие | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Подписывает соглашение для заданного элемента Marketplace. |
> | Действие | Microsoft.MarketplaceOrdering/agreements/read | Возвращение всех соглашений в указанной подписке. |
> | Действие | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Получение соглашения для заданного элемента виртуальной машины Marketplace. |
> | Действие | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Подписывание соглашения или его отмена для заданного элемента виртуальной машины Marketplace. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Media/checknameavailability/action | Проверка доступности имени учетной записи служб мультимедиа. |
> | Действие | Microsoft.Media/locations/checkNameAvailability/action | Проверка доступности имени учетной записи служб мультимедиа. |
> | Действие | Microsoft.Media/mediaservices/assets/delete | Удаление ресурса. |
> | Действие | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Получение ключа шифрования ресурса. |
> | Действие | Microsoft.Media/mediaservices/assets/listContainerSas/action | Вывод списка подписанных URL-адресов контейнера ресурсов. |
> | Действие | Microsoft.Media/mediaservices/assets/read | Чтение ресурсов. |
> | Действие | Microsoft.Media/mediaservices/assets/write | Создание или обновление ресурсов. |
> | Действие | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Удаление политики ключей содержимого. |
> | Действие | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Получение свойств политики с секретами. |
> | Действие | Microsoft.Media/mediaservices/contentKeyPolicies/read | Чтение политики ключей содержимого. |
> | Действие | Microsoft.Media/mediaservices/contentKeyPolicies/write | Создание или обновление политики ключей содержимого. |
> | Действие | Microsoft.Media/mediaservices/delete | Удаление учетной записи служб мультимедиа. |
> | Действие | Microsoft.Media/mediaservices/eventGridFilters/delete | Удаление фильтра сетки событий. |
> | Действие | Microsoft.Media/mediaservices/eventGridFilters/read | Чтение фильтра сетки событий. |
> | Действие | Microsoft.Media/mediaservices/eventGridFilters/write | Создание или обновление фильтра сетки событий. |
> | Действие | Microsoft.Media/mediaservices/liveEventOperations/read | Чтение операции динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/delete | Удаление динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Удаление выходных данных динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Чтение выходных данных динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Создание или обновление выходных данных динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/read | Чтение динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/reset/action | Сброс операции динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/start/action | Запуск операции динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/stop/action | Остановка выполнения операции динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveEvents/write | Создание или обновление динамического события. |
> | Действие | Microsoft.Media/mediaservices/liveOutputOperations/read | Чтение операции с выходными данными динамического события. |
> | Действие | Microsoft.Media/mediaservices/read | Удаление учетной записи служб мультимедиа. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Чтение операции конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/delete | Удаление конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/read | Чтение конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Масштабирование операции конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Запуск операции конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Остановка выполнения операции конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingEndpoints/write | Создание или обновление конечной точки потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingLocators/delete | Удаление потокового указателя. |
> | Действие | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Вывод списка ключей содержимого. |
> | Действие | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Вывод списка путей. |
> | Действие | Microsoft.Media/mediaservices/streamingLocators/read | Считывание потокового указателя. |
> | Действие | Microsoft.Media/mediaservices/streamingLocators/write | Создание или обновление потокового указателя. |
> | Действие | Microsoft.Media/mediaservices/streamingPolicies/delete | Удаление политики потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingPolicies/read | Чтение политики потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/streamingPolicies/write | Создание или обновление политики потоковой передачи. |
> | Действие | Microsoft.Media/mediaservices/syncStorageKeys/action | Синхронизация ключей хранилища для присоединенной учетной записи хранения Azure. |
> | Действие | Microsoft.Media/mediaservices/transforms/delete | Удаление преобразования. |
> | Действие | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Отменяет задание. |
> | Действие | Microsoft.Media/mediaservices/transforms/jobs/delete | Удаление задания. |
> | Действие | Microsoft.Media/mediaservices/transforms/jobs/read | Считывание задания. |
> | Действие | Microsoft.Media/mediaservices/transforms/jobs/write | Создание или обновление задания. |
> | Действие | Microsoft.Media/mediaservices/transforms/read | Чтение преобразования. |
> | Действие | Microsoft.Media/mediaservices/transforms/write | Создание или обновление преобразования. |
> | Действие | Microsoft.Media/mediaservices/write | Создание или обновление учетной записи служб мультимедиа. |
> | Действие | Microsoft.Media/operations/read | Возвращает доступные операции. |
> | Действие | Microsoft.Media/register/action | Регистрация подписки для поставщика ресурсов служб мультимедиа и разрешение создания учетных записей служб мультимедиа. |
> | Действие | Microsoft.Media/unregister/action | Отмена регистрации подписки для поставщика ресурсов Media Services. |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Migrate/Operations/read | Чтение предоставленных операций. |

## <a name="microsoftnetwork"></a>Microsoft.Network.

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Предопределенная политика SSL шлюза приложений. |
> | Действие | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Доступные параметры SSL шлюза приложений. |
> | Действие | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Возвращает доступные наборы правил WAF шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/backendhealth/action | Возвращает сведения о работоспособности серверной части шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/delete | Удаляет шлюз приложений. |
> | Действие | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Получение таблицы маршрутов, настроенной в шлюзе приложений. |
> | Действие | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Получение таблицы маршрутов, настроенной в шлюзе приложений. |
> | Действие | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Получение событий для шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/read | Возвращает шлюз приложений. |
> | Действие | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Настройка конфигурации центра безопасности шлюза приложений. |
> | Действие | Microsoft.Network/applicationGateways/start/action | Запускает шлюз приложений. |
> | Действие | Microsoft.Network/applicationGateways/stop/action | Останавливает шлюз приложений. |
> | Действие | Microsoft.Network/applicationGateways/write | Создает новый шлюз приложений или обновляет существующий. |
> | Действие | Microsoft.Network/applicationSecurityGroups/delete | Удаление группы безопасности приложений. |
> | Действие | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Присоединение конфигурации IP-адреса к группам безопасности приложений. |
> | Действие | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Присоединение правила безопасности к группам безопасности приложений. |
> | Действие | Microsoft.Network/applicationSecurityGroups/read | Получение идентификатора группы безопасности приложений. |
> | Действие | Microsoft.Network/applicationSecurityGroups/write | Создание группы безопасности приложений или обновление существующей группы. |
> | Действие | Microsoft.Network/bgpServiceCommunities/read | Возвращает сообщества службы BGP. |
> | Действие | Microsoft.Network/checkTrafficManagerNameAvailability/action | Проверяет доступность относительного DNS-имени диспетчера трафика. |
> | Действие | Microsoft.Network/connections/delete | Удаляет подключение шлюза виртуальной сети. |
> | Действие | Microsoft.Network/connections/read | Возвращает подключение шлюза виртуальной сети. |
> | Действие | Microsoft.Network/connections/sharedkey/action | Получение общего ключа для подключения шлюза виртуальной сети. |
> | Действие | Microsoft.Network/connections/sharedKey/read | Возвращает общий ключ для подключения шлюза виртуальной сети. |
> | Действие | Microsoft.Network/connections/sharedKey/write | Создает новый общий ключ для подключения шлюза виртуальной сети или обновляет существующий. |
> | Действие | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Получение конфигурации VPN-устройства для подключения шлюза виртуальной сети. |
> | Действие | Microsoft.Network/connections/write | Создает новое подключение шлюза виртуальной сети или обновляет существующее. |
> | Действие | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Удаление прокси-сервера плана защиты от атак DDoS. |
> | Действие | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Получение определения прокси-сервера плана защиты от атак DDoS. |
> | Действие | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Создание прокси-сервера плана защиты от атак DDoS или обновление существующего. |
> | Действие | Microsoft.Network/ddosProtectionPlans/delete | Удаление плана защиты от атак DDoS. |
> | Действие | Microsoft.Network/ddosProtectionPlans/join/action | Присоединение плана защиты от атак DDoS. |
> | Действие | Microsoft.Network/ddosProtectionPlans/read | Получение плана защиты от атак DDoS. |
> | Действие | Microsoft.Network/ddosProtectionPlans/write | Создание или обновление плана защиты от атак DDoS.  |
> | Действие | Microsoft.Network/dnsoperationresults/read | Возвращает результаты операции DNS. |
> | Действие | Microsoft.Network/dnsoperationstatuses/read | Возвращает состояние операции DNS.  |
> | Действие | Microsoft.Network/dnszones/A/delete | Удаляет из зоны DNS набор записей типа A с заданным именем. |
> | Действие | Microsoft.Network/dnszones/A/read | Возвращает набор записей типа A в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/A/write | Создает или обновляет набора записей типа A в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/AAAA/delete | Удаляет из зоны DNS набор записей типа AAAA с заданным именем. |
> | Действие | Microsoft.Network/dnszones/AAAA/read | Возвращает набор записей типа AAAA в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/AAAA/write | Создает или обновляет набора записей типа AAAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/all/read | Возвращает набор записей DNS разного типа. |
> | Действие | Microsoft.Network/dnszones/CAA/delete | Удаление из зоны DNS набора записей типа CAA с заданным именем. |
> | Действие | Microsoft.Network/dnszones/CAA/read | Получение набора записей типа CAA в формате JSON. Набор записей содержит срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/CAA/write | Создание или обновление набора записей типа CAA в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/CNAME/delete | Удаляет из зоны DNS набор записей типа CNAME с заданным именем. |
> | Действие | Microsoft.Network/dnszones/CNAME/read | Возвращает набор записей типа CNAME в формате JSON. Набор записей содержит срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/CNAME/write | Создает или обновляет набора записей типа CNAME в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/delete | Удаляет зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. |
> | Действие | Microsoft.Network/dnszones/MX/delete | Удаляет из зоны DNS набор записей типа MX с заданным именем. |
> | Действие | Microsoft.Network/dnszones/MX/read | Возвращает набор записей типа MX в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/MX/write | Создает или обновляет набора записей типа MX в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/NS/delete | Удаляет набор записей DNS типа NS. |
> | Действие | Microsoft.Network/dnszones/NS/read | Возвращает набор записей DNS типа NS. |
> | Действие | Microsoft.Network/dnszones/NS/write | Создает или обновляет набор записей DNS типа NS. |
> | Действие | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики зоны DNS. |
> | Действие | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики зоны DNS. |
> | Действие | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Получение определений метрик зоны DNS. |
> | Действие | Microsoft.Network/dnszones/PTR/delete | Удаляет из зоны DNS набор записей типа PTR с заданным именем. |
> | Действие | Microsoft.Network/dnszones/PTR/read | Возвращает набор записей типа PTR в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/PTR/write | Создает или обновляет набора записей типа PTR в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/read | Возвращает зону DNS в формате JSON. К свойствам зоны относятся tags, etag, numberOfRecordSets и maxNumberOfRecordSets. Обратите внимание, что эта команда не извлекает наборы записей, содержащиеся в зоне. |
> | Действие | Microsoft.Network/dnszones/recordsets/read | Возвращает набор записей DNS разного типа. |
> | Действие | Microsoft.Network/dnszones/SOA/read | Возвращает набор записей DNS типа SOA. |
> | Действие | Microsoft.Network/dnszones/SOA/write | Создает или обновляет набор записей DNS типа SOA. |
> | Действие | Microsoft.Network/dnszones/SRV/delete | Удаляет из зоны DNS набор записей типа SRV с заданным именем. |
> | Действие | Microsoft.Network/dnszones/SRV/read | Возвращает набор записей типа SRV в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/SRV/write | Создает или обновляет набор записей типа SRV. |
> | Действие | Microsoft.Network/dnszones/TXT/delete | Удаляет из зоны DNS набор записей типа TXT с заданным именем. |
> | Действие | Microsoft.Network/dnszones/TXT/read | Возвращает набор записей типа TXT в формате JSON. Набор записей содержит список записей, а также срок жизни, теги и ETag. |
> | Действие | Microsoft.Network/dnszones/TXT/write | Создает или обновляет набора записей типа TXT в зоне DNS. Указанные записи заменят текущие записи в наборе записей. |
> | Действие | Microsoft.Network/dnszones/write | Создает или обновляет зону DNS в группе ресурсов.  Используется для обновления тегов в ресурсе зоны DNS. Обратите внимание, что эту команду невозможно использовать для создания или обновления наборов записей в зоне. |
> | Действие | Microsoft.Network/expressRouteCircuits/authorizations/delete | Удаляет данные авторизации канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/authorizations/read | Возвращает данные авторизации канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/authorizations/write | Создает новые данные авторизации канала ExpressRoute или обновляет существующие. |
> | Действие | Microsoft.Network/expressRouteCircuits/delete | Удаляет канал ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/join/action | Подключает канал Express Route. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Возвращает таблицу ARP для пиринга канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Удаление подключения канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Получение подключения канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Создание нового ресурса подключения канала ExpressRoute или обновление существующего. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/delete | Удаляет пиринг канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/read | Возвращает пиринг канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Возвращает таблицу маршрутов для пиринга канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Возвращает сводку по таблице маршрутов для пиринга канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Возвращает статистику пиринга канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/peerings/write | Создает новый пиринг канала ExpressRoute или обновляет существующий. |
> | Действие | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики для каналов ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики для каналов ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Получение событий для каналов ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Получение определений метрик для каналов ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/read | Возвращает канал ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/stats/read | Возвращает статистику канала ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCircuits/write | Создает новый канал ExpressRoute или обновляет существующий. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/delete | Удаление перекрестного подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/join/action | Присоединение перекрестного подключения Express Route. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Получение таблицы ARP перекрестного пирингового подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Удаление перекрестного пирингового подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/read | Получение перекрестного пирингового подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Получение таблицы маршрутов перекрестного пирингового подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Получение сводки таблицы маршрутов перекрестного пирингового подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/peerings/write | Создание перекрестного пирингового подключения ExpressRoute или обновление существующего. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/read | Получение перекрестного подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteCrossConnections/write | Создание или обновление перекрестного подключения ExpressRoute. |
> | Действие | Microsoft.Network/expressRouteServiceProviders/read | Возвращает поставщики службы ExpressRoute. |
> | Действие | Microsoft.Network/intendedPolicies/delete | Удаляет назначенную политику. |
> | Действие | Microsoft.Network/intendedPolicies/read | Получает описание назначенной политики. |
> | Действие | Microsoft.Network/intendedPolicies/write | Создает назначенную политику или обновляет имеющуюся. |
> | Действие | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/backendAddressPools/read | Возвращает определение внутреннего пула адресов подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/delete | Удаляет подсистему балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Возвращает определение внешней IP-конфигурации подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяет пул входящих подключений NAT подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatPools/read | Возвращает определение пула входящих подключений NAT подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatRules/delete | Удаляет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatRules/read | Возвращает определение правила NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/inboundNatRules/write | Создает новое правило NAT для входящего трафика подсистемы балансировки нагрузки или обновляет существующее. |
> | Действие | Microsoft.Network/loadBalancers/loadBalancingRules/read | Возвращает определение правила подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/networkInterfaces/read | Возвращает ссылки на все сетевые интерфейсы в подсистеме балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/outboundNatRules/read | Возвращает определение правила пула исходящих подключений NAT подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/probes/join/action | Позволяет использовать зонды подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. |
> | Действие | Microsoft.Network/loadBalancers/probes/read | Возвращает пробу подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Возвращение параметров диагностики подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Возвращение событий для подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/virtualMachines/read | Возвращает ссылки на все виртуальные машины в подсистеме балансировки нагрузки. |
> | Действие | Microsoft.Network/loadBalancers/write | Создает новую подсистему балансировки нагрузки или обновляет существующую. |
> | Действие | Microsoft.Network/localnetworkgateways/delete | Удаляет шлюз локальной сети. |
> | Действие | Microsoft.Network/localnetworkgateways/read | Возвращает шлюз локальной сети. |
> | Действие | Microsoft.Network/localnetworkgateways/write | Создает новый шлюз локальной сети или обновляет существующий. |
> | Действие | Microsoft.Network/locations/availableDelegations/read | Получает доступные делегирования. |
> | Действие | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Проверяет поддержку ускоренной сети. |
> | Действие | Microsoft.Network/locations/checkDnsNameAvailability/read | Проверяет, доступна ли метка DNS в указанном расположении. |
> | Действие | Microsoft.Network/locations/operationResults/read | Возвращает результат асинхронной операции POST или DELETE. |
> | Действие | Microsoft.Network/locations/operations/read | Возвращает ресурс операции, представляющий состояние асинхронной операции. |
> | Действие | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Возвращает поддерживаемые размеры виртуальных машин. |
> | Действие | Microsoft.Network/locations/usages/read | Возвращает метрики использования ресурсов. |
> | Действие | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Получение списка доступных служб конечных точек виртуальной сети. |
> | Действие | Microsoft.Network/networkInterfaces/delete | Удаляет сетевой интерфейс. |
> | Действие | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Получение удостоверения диагностики ресурса. |
> | Действие | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Возвращает группы безопасности сети, настроенные для сетевого интерфейса виртуальной машины. |
> | Действие | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Возвращает таблицу маршрутов, настроенную для сетевого интерфейса виртуальной машины. |
> | Действие | Microsoft.Network/networkInterfaces/ipconfigurations/read | Возвращает определение IP-конфигурации сетевого интерфейса.  |
> | Действие | Microsoft.Network/networkInterfaces/join/action | Подключает виртуальную машину к сетевому интерфейсу. |
> | Действие | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Подключает ресурс к сетевому интерфейсу с помощью ассоциации службы. |
> | Действие | Microsoft.Network/networkInterfaces/loadBalancers/read | Возвращает все подсистемы балансировки нагрузки, в которых используется сетевой интерфейс. |
> | Действие | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для сетевого интерфейса. |
> | Действие | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Действие | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Удаляет ассоциацию службы. |
> | Действие | Microsoft.Network/networkInterfaces/serviceAssociations/read | Возвращает определение ассоциации службы. |
> | Действие | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Проверяет ассоциацию службы. |
> | Действие | Microsoft.Network/networkInterfaces/serviceAssociations/write | Создает новую ассоциацию службы или изменяет имеющуюся. |
> | Действие | Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
> | Действие | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Возвращает определение правила безопасности по умолчанию. |
> | Действие | Microsoft.Network/networkSecurityGroups/delete | Удаляет группу безопасности сети. |
> | Действие | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. |
> | Действие | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики групп безопасности сети. |
> | Действие | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики групп безопасности сети. Эта операция осуществляется при задействовании поставщика ресурсов аналитики. |
> | Действие | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Получение событий группы безопасности сети. |
> | Действие | Microsoft.Network/networkSecurityGroups/read | Возвращает определение группы безопасности сети. |
> | Действие | Microsoft.Network/networkSecurityGroups/securityRules/delete | Удаляет правило безопасности. |
> | Действие | Microsoft.Network/networkSecurityGroups/securityRules/read | Возвращает определение правила безопасности. |
> | Действие | Microsoft.Network/networkSecurityGroups/securityRules/write | Создает новое правило безопасности или обновляет существующее. |
> | Действие | Microsoft.Network/networkSecurityGroups/write | Создает новую группу безопасности сети или обновляет существующую. |
> | Действие | Microsoft.Network/networkWatchers/availableProvidersList/action | Получение всех доступных поставщиков услуг Интернета в заданном регионе Azure. |
> | Действие | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Получение оценки относительной задержки для поставщиков услуг Интернета из указанного расположения в регионы Azure. |
> | Действие | Microsoft.Network/networkWatchers/configureFlowLog/action | Настраивает ведение журнала потоков для целевого ресурса. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/delete | Удаление монитора подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики монитора подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики монитора подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для монитора подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Запрос мониторинга подключения между указанными конечными точками. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/read | Получение сведений о мониторе подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Запуск мониторинга подключения между указанными конечными точками. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Остановка или приостановка мониторинга подключения между указанными конечными точками. |
> | Действие | Microsoft.Network/networkWatchers/connectionMonitors/write | Создание монитора подключения. |
> | Действие | Microsoft.Network/networkWatchers/connectivityCheck/action | Проверка возможности прямого подключения TCP между виртуальной машиной и определенной конечной точкой, при этом используется другая виртуальная машина или произвольный удаленный сервер. |
> | Действие | Microsoft.Network/networkWatchers/delete | Удаляет Наблюдатель за сетями. |
> | Действие | Microsoft.Network/networkWatchers/ipFlowVerify/action | Проверяет, разрешена ли передача или прием пакета для определенного места назначения. |
> | Действие | Microsoft.Network/networkWatchers/lenses/delete | Удаление группы связанных элементов. |
> | Действие | Microsoft.Network/networkWatchers/lenses/query/action | Запрос мониторинга трафика в указанной конечной точке. |
> | Действие | Microsoft.Network/networkWatchers/lenses/read | Получение сведений о группе связанных элементов. |
> | Действие | Microsoft.Network/networkWatchers/lenses/start/action | Запуск мониторинга трафика в указанной конечной точке. |
> | Действие | Microsoft.Network/networkWatchers/lenses/stop/action | Остановка или приостановка мониторинга трафика в указанной конечной точке. |
> | Действие | Microsoft.Network/networkWatchers/lenses/write | Создание группы связанных элементов. |
> | Действие | Microsoft.Network/networkWatchers/nextHop/action | Для указанного целевого объекта и конечного IP-адреса возвращает тип следующего прыжка и его IP-адрес. |
> | Действие | Microsoft.Network/networkWatchers/packetCaptures/delete | Удаляет запись пакетов. |
> | Действие | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Возвращает сведения о свойствах и состоянии ресурса записи пакетов. |
> | Действие | Microsoft.Network/networkWatchers/packetCaptures/read | Возвращает определение записи пакетов. |
> | Действие | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Останавливает выполняемый сеанс записи пакетов. |
> | Действие | Microsoft.Network/networkWatchers/packetCaptures/write | Создает запись пакетов. |
> | Действие | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Возвращает состояние ведения журнала потоков для ресурса. |
> | Действие | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Возвращает результат устранения неполадок предыдущей или текущей операции по устранению неполадок. |
> | Действие | Microsoft.Network/networkWatchers/read | Возвращает определение Наблюдателя за сетями. |
> | Действие | Microsoft.Network/networkWatchers/securityGroupView/action | Отображает настроенные и действующие правила группы безопасности сети для виртуальной машины. |
> | Действие | Microsoft.Network/networkWatchers/topology/action | Возвращает представление уровня сети для ресурсов и их связей в группе ресурсов. |
> | Действие | Microsoft.Network/networkWatchers/troubleshoot/action | Начинает устранение неполадок сетевого ресурса в Azure. |
> | Действие | Microsoft.Network/networkWatchers/write | Создает новый Наблюдатель за сетями или обновляет существующий. |
> | Действие | Microsoft.Network/operations/read | Возвращает доступные операции. |
> | Действие | Microsoft.Network/publicIPAddresses/delete | Удаляет общедоступный IP-адрес. |
> | Действие | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. |
> | Действие | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Удаляет серверный пул подсистемы балансировки нагрузки с общедоступным IP-адресом. |
> | Действие | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Подключает серверный пул подсистемы балансировки нагрузки с общедоступным IP-адресом. |
> | Действие | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Возвращает определение серверного пула подсистемы балансировки нагрузки с общедоступным IP-адресом. |
> | Действие | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Создает серверный пул подсистемы балансировки нагрузки с общедоступным IP-адресом или обновляет имеющийся. |
> | Действие | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики общедоступного IP-адреса. |
> | Действие | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики общедоступного IP-адреса. |
> | Действие | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Получение определений журнала общедоступного IP-адреса. |
> | Действие | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Получение определений метрик общедоступного IP-адреса. |
> | Действие | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Действие | Microsoft.Network/publicIPAddresses/write | Создает новый общедоступный IP-адрес или обновляет существующий.  |
> | Действие | Microsoft.Network/register/action | Регистрирует подписку. |
> | Действие | Microsoft.Network/routeFilters/delete | Удаляет определение фильтра маршрутов. |
> | Действие | Microsoft.Network/routeFilters/join/action | Присоединяет фильтр маршрутов. |
> | Действие | Microsoft.Network/routeFilters/read | Возвращает определение фильтра маршрутов. |
> | Действие | Microsoft.Network/routeFilters/routeFilterRules/delete | Удаляет определение правила фильтра маршрутов. |
> | Действие | Microsoft.Network/routeFilters/routeFilterRules/read | Возвращает определение правила фильтра маршрутов. |
> | Действие | Microsoft.Network/routeFilters/routeFilterRules/write | Создает новое правило фильтра маршрутов или обновляет существующее. |
> | Действие | Microsoft.Network/routeFilters/write | Создает новый фильтр маршрутов или обновляет существующий. |
> | Действие | Microsoft.Network/routeTables/delete | Удаляет определение таблицы маршрутов. |
> | Действие | Microsoft.Network/routeTables/join/action | Присоединяет таблицу маршрутов. |
> | Действие | Microsoft.Network/routeTables/read | Возвращает определение таблицы маршрутов. |
> | Действие | Microsoft.Network/routeTables/routes/delete | Удаляет определение маршрута. |
> | Действие | Microsoft.Network/routeTables/routes/read | Возвращает определение маршрута. |
> | Действие | Microsoft.Network/routeTables/routes/write | Создает новый маршрут или обновляет существующий. |
> | Действие | Microsoft.Network/routeTables/write | Создает новую таблицу маршрутов или обновляет существующую. |
> | Действие | Microsoft.Network/securegateways/applicationRuleCollections/delete | Удаление коллекции правил приложения для защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/applicationRuleCollections/read | Получение коллекции правил приложения для данного защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/applicationRuleCollections/write | Создание или обновление коллекции правил приложения для защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/delete | Удаление защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/networkRuleCollections/delete | Удаление коллекции правил сети для защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/networkRuleCollections/read | Получение коллекции правил сети для данного защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/networkRuleCollections/write | Создание или обновление коллекции правил сети для защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/read | Получение защищенного шлюза. |
> | Действие | Microsoft.Network/securegateways/write | Создание или обновление защищенного шлюза. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/delete | Удаление политики конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/join/action | Присоединение политики конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Присоединение подсети к политикам конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/read | Получение описания политики конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Удаление определения политики конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Получение описания определения политики конечной точки службы. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Создание определения политики конечной точки службы или обновление существующего. |
> | Действие | Microsoft.Network/serviceEndpointPolicies/write | Создание политики конечной точки службы или обновление существующей. |
> | Действие | Microsoft.Network/trafficManagerGeographicHierarchies/read | Возвращает географическую иерархию диспетчера трафика, содержащую регионы, которые могут быть использованы для метода географической маршрутизации трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Удаление конечной точки Azure из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную конечную точку Azure. |
> | Действие | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Получение конечной точки Azure, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действие | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Добавление новой конечной точки Azure в существующий профиль диспетчера трафика или обновление свойств существующей конечной точки Azure в этом профиле. |
> | Действие | Microsoft.Network/trafficManagerProfiles/delete | Удаляет профиль диспетчера трафика. Будут утрачены все параметры, связанные с профилем диспетчера трафика, и профиль больше не сможет использоваться для маршрутизации трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Удаление внешней конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную внешнюю конечную точку. |
> | Действие | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Получение внешней конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действие | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Добавление новой внешней конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей внешней конечной точки в этом профиле. |
> | Действие | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Получение тепловой карты диспетчера трафика для данного профиля диспетчера трафика, которая содержит количество запросов и данные задержек по исходному IP-адресу и расположению. |
> | Действие | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Удаление вложенной конечной точки из существующего профиля диспетчера трафика. Диспетчер трафика остановит маршрутизацию трафика в удаленную вложенную конечную точку. |
> | Действие | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Получение вложенной конечной точки, которая относится к профилю диспетчера трафика, а также свойств этой конечной точки. |
> | Действие | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Добавление новой вложенной конечной точки в существующий профиль диспетчера трафика или обновление свойств существующей вложенной конечной точки в этом профиле. |
> | Действие | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики диспетчера трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики диспетчера трафика. Эта операция осуществляется при задействовании поставщика ресурсов аналитики. |
> | Действие | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Получение событий для диспетчера трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для диспетчера трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/read | Возвращает конфигурацию профиля диспетчера трафика. Он содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек, маршрутизируемых данным профилем диспетчера трафика. |
> | Действие | Microsoft.Network/trafficManagerProfiles/write | Создает профиль диспетчера трафика или изменяет конфигурацию существующего профиля диспетчера трафика.<br>Сюда входит включение или отключение профиля и изменение параметров DNS, параметров маршрутизации трафика или параметров мониторинга конечных точек.<br>Конечные точки, маршрутизируемые с помощью профиля диспетчера трафика, можно добавлять, удалять, включать и отключать. |
> | Действие | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Удаление ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действие | Microsoft.Network/trafficManagerUserMetricsKeys/read | Получение ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действие | Microsoft.Network/trafficManagerUserMetricsKeys/write | Создание нового ключа уровня подписки, используемого для сбора метрик пользователя в реальном времени. |
> | Действие | Microsoft.Network/unregister/action | Отменяет регистрацию подписки. |
> | Действие | Microsoft.Network/virtualHubs/delete | Удаление виртуального концентратора. |
> | Действие | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Удаление подключения к виртуальной сети концентратора. |
> | Действие | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Получение подключения к виртуальной сети концентратора. |
> | Действие | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Создание или обновление подключения к виртуальной сети концентратора. |
> | Действие | Microsoft.Network/virtualHubs/read | Получение виртуального концентратора. |
> | Действие | Microsoft.Network/virtualHubs/write | Создание или обновление виртуального концентратора. |
> | Действие | microsoft.network/virtualnetworkgateways/connections/read | Получение подключения к шлюзу виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkGateways/delete | Удаление шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Создание пакета VPN-клиента для шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Создание пакета VPN-профиля для шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Получение объявленных маршрутов шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Получение состояния кэширующего узла BGP шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Получение обнаруженных маршрутов шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Получение параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Получение URL-адреса предварительно созданного пакета профиля VPN-клиента. |
> | Действие | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики для шлюза виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики шлюза виртуальной сети. Эта операция осуществляется при задействовании поставщика ресурсов аналитики. |
> | Действие | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Получение событий шлюза виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик шлюза виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkGateways/read | Получение шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/reset/action | Сброс шлюза виртуальной сети. |
> | Действие | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Настройка параметров IPsec VPN-клиента для клиента P2S шлюза виртуальной сети. |
> | Действие | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Получение списка поддерживаемых VPN-устройств. |
> | Действие | Microsoft.Network/virtualNetworkGateways/write | Создание или обновление шлюза виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Проверяет, доступен ли IP-адрес в указанной виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/customViews/get/action | Получение содержимого пользовательского представления виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/customViews/read | Получение определения пользовательского представления виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/delete | Удаляет виртуальную сеть. |
> | Действие | Microsoft.Network/virtualNetworks/peer/action | Создает пиринг между виртуальными сетями. |
> | Действие | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметров диагностики виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметров диагностики виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Получение определений журналов виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Удаление прокси-сервера пиринга виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Получение определения прокси-сервера пиринга виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Создание прокси-сервера пиринга виртуальной сети или обновление существующего. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/delete | Удаляет подсеть виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/read | Возвращает определение подсети виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Удаление ссылки для перехода к ресурсу. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Получение определения ссылки для перехода к ресурсу. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Создание ссылки для перехода к ресурсу или обновление существующей ссылки. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Удаляет ссылку на ассоциацию службы. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Возвращает подробное определение ссылки на ассоциацию службы. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Возвращает определение ссылки на ассоциацию службы. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Проверяет ссылку на ассоциацию службы. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Создает ссылку на ассоциацию службы или обновляет имеющуюся. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Возвращает ссылки на все виртуальные машины в подсети виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/subnets/write | Создает новую подсеть пиринг виртуальной сети или обновляет существующую. |
> | Действие | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Удаление помеченного объекта-получателя трафика. |
> | Действие | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Получение определения помеченного объекта-получателя трафика. |
> | Действие | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Проверка помеченного объекта-получателя трафика. |
> | Действие | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Создание помеченного объекта-получателя трафика или обновление существующего. |
> | Действие | Microsoft.Network/virtualNetworks/usages/read | Получение сведения об использовании IP-адресов для каждой подсети виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/virtualMachines/read | Возвращает ссылки на все виртуальные машины в виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Удаляет пиринг виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Возвращает определение пиринга виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Создает новый пиринг виртуальной сети или обновляет существующий. |
> | Действие | Microsoft.Network/virtualNetworks/write | Создает новую виртуальную сеть или обновляет существующую. |
> | Действие | Microsoft.Network/virtualNetworkTaps/delete | Удаление устройства перехватчика трафика (TAP) виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkTaps/join/action | Присоединение TAP виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkTaps/read | Получение TAP виртуальной сети. |
> | Действие | Microsoft.Network/virtualNetworkTaps/write | Создание или обновление TAP виртуальной сети. |
> | Действие | Microsoft.Network/virtualWans/delete | Удаление виртуальной глобальной сети. |
> | Действие | Microsoft.Network/virtualWans/read | Получение виртуальной глобальной сети. |
> | Действие | Microsoft.Network/virtualWans/virtualHubProxies/delete | Удаление прокси-сервера виртуального концентратора. |
> | Действие | Microsoft.Network/virtualWans/virtualHubProxies/read | Получение определения прокси-сервера виртуального концентратора. |
> | Действие | Microsoft.Network/virtualWans/virtualHubProxies/write | Создание или обновление прокси-сервера виртуального концентратора. |
> | Действие | Microsoft.Network/virtualWans/virtualHubs/read | Получение всех виртуальных концентраторов, ссылающихся на виртуальную глобальную сеть. |
> | Действие | Microsoft.Network/virtualwans/vpnconfiguration/action | Получение конфигурации VPN. |
> | Действие | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Удаление прокси-сервера сети VPN. |
> | Действие | Microsoft.Network/virtualWans/vpnSiteProxies/read | Получение определения прокси-сервера сети VPN. |
> | Действие | Microsoft.Network/virtualWans/vpnSiteProxies/write | Создание или обновление прокси-сервера сети VPN. |
> | Действие | Microsoft.Network/virtualWans/vpnSites/read | Получение всех сетей VPN, ссылающихся на виртуальную глобальную сеть. |
> | Действие | Microsoft.Network/virtualWans/write | Создание или обновление виртуальной глобальной сети. |
> | Действие | Microsoft.Network/vpnGateways/read | Получение шлюза VPN. |
> | Действие | microsoft.network/vpnGateways/vpnConnections/read | Получение VPN-подключения. |
> | Действие | microsoft.network/vpnGateways/vpnConnections/write | Установка VPN-подключения. |
> | Действие | Microsoft.Network/vpnGateways/write | Установка VPN-шлюза. |
> | Действие | Microsoft.Network/vpnsites/delete | Удаление ресурса сети VPN. |
> | Действие | Microsoft.Network/vpnsites/read | Получение ресурса сети VPN. |
> | Действие | Microsoft.Network/vpnsites/write | Создание или обновление ресурса сети VPN. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Проверяет, доступно ли имя заданного ресурса пространства имен в службе центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Возвращает список описаний правил авторизации пространства имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Правило авторизации пространства имен повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно. |
> | Действие | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действие | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Проверяет, доступно ли заданное имя центра уведомлений в пространстве имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Возвращает список правил авторизации центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Удаляет правила авторизации центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Возвращает строку подключения к центру уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Возвращает список правил авторизации центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Правило авторизации центра уведомлений повторно создает первичный или вторичный ключ. Укажите ключ, который должен быть создан повторно. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Создает правила авторизации центра уведомлений и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Отправляет тестовое push-уведомление. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Удаляет ресурс центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Возвращает все учетные данные PNS центра уведомлений. Сюда входят учетные данные WNS, MPNS, APNS, GCM и Baidu. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Возвращает список описаний ресурсов центра уведомлений. |
> | Действие | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Создает центр уведомлений и обновляет его свойства. К этим свойствам в основном относятся учетные данные PNS. Правила авторизации и срок жизни. |
> | Действие | Microsoft.NotificationHubs/Namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действие | Microsoft.NotificationHubs/Namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действие | Microsoft.NotificationHubs/register/action | Регистрирует подписку для поставщика ресурсов центров уведомлений и позволяет создавать пространства имен и центры уведомлений. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.OperationalInsights/linkTargets/read | Выводит список существующих учетных записей, которые не связаны с подпиской Azure. Чтобы связать эту подписку Azure с рабочей областью, укажите идентификатор клиента, возвращаемый этой операцией, в свойстве идентификатора клиента в операции создания рабочей области. |
> | Действие | Microsoft.OperationalInsights/register/action | Регистрирует подписку для поставщика ресурсов. |
> | Действие | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Действие | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Получение схемы поиска версии 2. |
> | Действие | Microsoft.OperationalInsights/workspaces/api/query/action | Поиск с помощью нового механизма. |
> | Действие | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Получение схемы поиска версии 2. |
> | Действие | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Удаляет область конфигурации. |
> | Действие | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Возвращает область конфигурации. |
> | Действие | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Задает область конфигурации. |
> | Действие | Microsoft.OperationalInsights/workspaces/datasources/delete | Удаляет источники данных в рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/datasources/read | Возвращает источники данных в рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/datasources/write | Создает или обновляет источники данных в рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/delete | Удаляет рабочую область. Если при создании рабочая область была связана с существующей рабочей областью, то эта связанная рабочая область не удаляется. |
> | Действие | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Создает сертификат регистрации для рабочей области. Этот сертификат используется для подключения Microsoft System Center Operations Manager к рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Отключает пакет аналитики для заданной рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Включает пакет аналитики для заданной рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Выводит список всех пакетов аналитики, которые являются видимыми для заданной рабочей области, а также сведения о том, включен или отключен пакет для этой рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Удаление связанной службы в заданной рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/linkedServices/read | Получение связанных служб в заданной рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/linkedServices/write | Создание или обновление связанных служб в заданной рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/listKeys/action | Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/listKeys/read | Получение списка ключей для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/managementGroups/read | Возвращает имена и метаданные для групп управления System Center Operations Manager, подключенных к этой рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Получение определений метрик в рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Удаление параметров уведомления пользователя для рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Получение параметров уведомления пользователя для рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Настройка параметров уведомления пользователя для рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/purge/action | Удаление указанных данных из рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/read | Возвращает существующую рабочую область. |
> | Действие | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Удаляет сохраненный поисковый запрос. |
> | Действие | Microsoft.OperationalInsights/workspaces/savedSearches/read | Возвращает сохраненный поисковый запрос. |
> | Действие | Microsoft.OperationalInsights/workspaces/savedSearches/write | Создает сохраненный поисковый запрос. |
> | Действие | Microsoft.OperationalInsights/workspaces/schema/read | Возвращает схему поиска для рабочей области.  Схема поиска содержит предоставленные поля и их типы. |
> | Действие | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Действие | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Действие | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Удаляет конфигурацию хранилища. Это остановит чтение данных из учетной записи хранения, выполняемое Microsoft Operational Insights. |
> | Действие | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Возвращает конфигурацию хранилища. |
> | Действие | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Создает конфигурацию учетной записи хранения. Эти конфигурации используются для извлечения данных из расположения в существующей учетной записи хранения. |
> | Действие | Microsoft.OperationalInsights/workspaces/usages/read | Возвращает данные об использовании для рабочей области, включая объем данных, считываемых рабочей областью. |
> | Действие | Microsoft.OperationalInsights/workspaces/write | Создает новую рабочую область или устанавливает связь с существующей рабочей областью с помощью указанного идентификатора клиента, полученного из нее. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.OperationsManagement/managementAssociations/delete | Удаление существующей связи управления. |
> | Действие | Microsoft.OperationsManagement/managementAssociations/read | Получение существующей связи управления. |
> | Действие | Microsoft.OperationsManagement/managementAssociations/write | Создание новой связи управления. |
> | Действие | Microsoft.OperationsManagement/managementConfigurations/delete | Удаление существующей конфигурации управления. |
> | Действие | Microsoft.OperationsManagement/managementConfigurations/read | Получение существующей конфигурации управления. |
> | Действие | Microsoft.OperationsManagement/managementConfigurations/write | Создание новой конфигурации управления. |
> | Действие | Microsoft.OperationsManagement/register/action | Регистрирует подписку для поставщика ресурсов. |
> | Действие | Microsoft.OperationsManagement/solutions/delete | Удаляет существующее решение OMS. |
> | Действие | Microsoft.OperationsManagement/solutions/read | Возвращает существующее решение OMS. |
> | Действие | Microsoft.OperationsManagement/solutions/write | Создает решение OMS. |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.PolicyInsights/policyEvents/queryResults/action | Запрашивает сведения о событиях политики. |
> | Действие | Microsoft.PolicyInsights/policyStates/queryResults/action | Запрашивает сведения о состояниях политики. |
> | Действие | Microsoft.PolicyInsights/policyStates/summarize/action | Запрашивает сводную информацию о последних состояниях политики. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Portal/dashboards/delete | Удаление панели мониторинга из подписки. |
> | Действие | Microsoft.Portal/dashboards/read | Чтение панели мониторинга для подписки. |
> | Действие | Microsoft.Portal/dashboards/write | Добавление или изменение панели мониторинга для подписки. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Проверка того, что имя выделенной емкости Power BI является допустимым и не используется. |
> | Действие | Microsoft.PowerBIDedicated/capacities/delete | Удаление выделенной емкости Power BI. |
> | Действие | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Получает доступные журналы для выделенной емкости Power BI. |
> | Действие | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для выделенной емкости Power BI. |
> | Действие | Microsoft.PowerBIDedicated/capacities/read | Извлечение сведений об указанной выделенной емкости Power BI. |
> | Действие | Microsoft.PowerBIDedicated/capacities/resume/action | Возобновляет использование емкости. |
> | Действие | Microsoft.PowerBIDedicated/capacities/skus/read | Получение доступных сведений о номере SKU для емкости. |
> | Действие | Microsoft.PowerBIDedicated/capacities/suspend/action | Приостановка использования емкости. |
> | Действие | Microsoft.PowerBIDedicated/capacities/write | Создание или обновление указанной выделенной емкости Power BI. |
> | Действие | Microsoft.PowerBIDedicated/locations/operationresults/read | Получение сведений о результате указанной операции. |
> | Действие | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Извлечение сведений о состоянии указанной операции. |
> | Действие | Microsoft.PowerBIDedicated/operations/read | Извлечение сведений об операциях. |
> | Действие | Microsoft.PowerBIDedicated/register/action | Регистрация выделенного поставщика ресурсов Power BI. |
> | Действие | Microsoft.PowerBIDedicated/skus/read | Извлечение сведений о номерах SKU. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Действие | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Действие | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Действие | Microsoft.RecoveryServices/locations/backupStatus/action | Проверка состояния архивации хранилищ служб восстановления. |
> | Действие | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Проверка компонентов. |
> | Действие | Microsoft.RecoveryServices/operations/read | Получение списка операций для поставщика ресурсов. |
> | Действие | Microsoft.RecoveryServices/register/action | Регистрация подписки для заданного поставщика ресурсов. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupconfig/read | Возвращает конфигурацию хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupconfig/write | Обновляет конфигурацию хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupEngines/read | Возвращение всех серверов управления архивацией, зарегистрированных в хранилище. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Получение всех защищаемых контейнеров. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Удаление зарегистрированного контейнера. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Запрос рабочих нагрузок в контейнере. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Получение всех элементов в контейнере. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Архивирует защищенный элемент. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Удаляет защищенный элемент. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Восстанавливает точки восстановления для защищенных элементов. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Создание зарегистрированного контейнера. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Обновляет список контейнеров. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Отменяет задание. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Возвращает метаданные управления архивацией для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возвращает результат операции архивации для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupOperations/read | Возвращает состояние операции архивации для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Удаляет политику защиты. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Возвращает состояние операции политики. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Возвращает список всех защищаемых элементов. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Возвращает данные ПИН-кода безопасности для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Возвращает конфигурацию службы хранилища для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Обновляет конфигурацию службы хранилища для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Действие | Microsoft.RecoveryServices/Vaults/delete | Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище". |
> | Действие | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Разрешение оповещения. |
> | Действие | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Возвращает конфигурацию уведомлений хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Настройка уведомлений по электронной почте в хранилище служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Диагностика в службе Azure Backup. |
> | Действие | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Диагностика в службе Azure Backup. |
> | Действие | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Журналы службы Azure Backup. |
> | Действие | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Метрики службы Azure Backup. |
> | Действие | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Действие | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Операцию отмены регистрации контейнера можно использовать для отмены регистрации контейнера. |
> | Действие | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Действие | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Действие | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывает параметры оповещений. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Создает или обновляет параметры оповещений. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Удаляет структуры. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Развертывает образ сервера обработки. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывает структуры. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Удаляет структуру. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывает сети. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Удаляет сетевые сопоставления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывает сетевые сопоставления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Создает или обновляет сетевые сопоставления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Обнаруживает защищаемый элемент. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывает контейнеры защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Удаляет контейнер защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Удаляет защищенные элементы. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Выполняет плановую отработку отказа. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывает защищенные элементы. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Удаляет защищенный элемент. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестирование отработки отказа |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Отработка отказа |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновляет службу Mobility Service. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Создает или обновляет защищенные элементы. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Удаляет сопоставления контейнера защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывает сопоставления контейнера защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Удаляет сопоставление контейнера защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Создает или обновляет сопоставления контейнера защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Переключает контейнер защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Создает или обновляет контейнеры защиты. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Удаляет поставщики служб восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывает поставщики служб восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Обновляет поставщик. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Удаляет поставщик служб восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Удаление или обновление поставщиков служб восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывает классификации хранилища. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Удаляет сопоставления классификаций хранилища. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывает сопоставления классификаций хранилища. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Создает или обновляет сопоставления классификаций хранилища. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Удаляет задания. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Считывает задания. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Создает или обновляет задания. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Создает или обновляет структуры. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Отменяет задание. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationJobs/read | Считывает задания. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Перезапускает задание. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Возобновляет выполнение задания. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Удаляет политики. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывает политики. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Создает или обновляет политики. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Удаляет планы восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Выполняет отработку отказа с фиксацией для плана восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Выполняет плановую отработку отказа для плана восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывает планы восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Повторно защищает план восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Тестирует отработку отказа для плана восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Тестирует очистку отработки отказа для плана восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Выполняет отработку отказа для плана восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Создает или обновляет планы восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/vaults/usages/read | Считывает данные об использовании хранилищ. |
> | Действие | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Действие | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Действие | Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Relay/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действие | Microsoft.Relay/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability. |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.Relay/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действие | Microsoft.Relay/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действие | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Операция обновления гибридного подключения. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Операция удаления правил авторизации гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Возвращает строку подключения для гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Получение списка правил авторизации гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Создает правила авторизации гибридного подключения и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/Delete | Операция удаления ресурса гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/read | Возвращает список описаний ресурсов гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/HybridConnections/write | Создает или обновляет свойства гибридного подключения. |
> | Действие | Microsoft.Relay/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.Relay/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.Relay/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действие | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действие | Microsoft.Relay/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действие | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Операция обновления ретранслятора WCF. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Операция удаления правил авторизации ретранслятора WCF. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Возвращает строку подключения к ретранслятору WCF. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Получение списка правил авторизации ретранслятора WCF. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Создает правила авторизации ретранслятора WCF и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/Delete | Операция удаления ресурса ретранслятора WCF. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/read | Возвращает список описаний ресурсов ретранслятора WCF. |
> | Действие | Microsoft.Relay/namespaces/WcfRelays/write | Создает или обновляет свойства ретранслятора WCF. |
> | Действие | Microsoft.Relay/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действие | Microsoft.Relay/operations/read | Получение операций. |
> | Действие | Microsoft.Relay/register/action | Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора. |
> | Действие | Microsoft.Relay/unregister/action | Регистрирует подписку для поставщика ресурсов ретранслятора и позволяет создавать ресурсы ретранслятора. |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Возвращает состояние доступности для указанного ресурса. |
> | Действие | Microsoft.ResourceHealth/AvailabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Действие | Microsoft.ResourceHealth/events/read | Получение событий работоспособности служб для данной подписки. |
> | Действие | Microsoft.Resourcehealth/healthevent/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.Resourcehealth/healthevent/Activated/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.Resourcehealth/healthevent/InProgress/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.Resourcehealth/healthevent/Pending/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.Resourcehealth/healthevent/Resolved/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.Resourcehealth/healthevent/Updated/action | Обозначение изменения состояния работоспособности для указанного ресурса. |
> | Действие | Microsoft.ResourceHealth/impactedResources/read | Получение затронутых ресурсов для данной подписки. |
> | Действие | Microsoft.ResourceHealth/register/action | Регистрация подписки на службу "Работоспособность ресурсов Microsoft". |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Resources/checkPolicyCompliance/action | Проверка состояния соответствия политикам для указанного ресурса. |
> | Действие | Microsoft.Resources/checkResourceName/action | Проверяет допустимость имени ресурса. |
> | Действие | Microsoft.Resources/deployments/cancel/action | Отменяет развертывание. |
> | Действие | Microsoft.Resources/deployments/delete | Удаляет развертывание. |
> | Действие | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Действие | Microsoft.Resources/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Действие | Microsoft.Resources/deployments/validate/action | Проверяет развертывание. |
> | Действие | Microsoft.Resources/deployments/write | Создает или обновляет развертывание. |
> | Действие | Microsoft.Resources/links/delete | Удаляет ссылку на ресурс. |
> | Действие | Microsoft.Resources/links/read | Возвращает ссылки на ресурсы или выводит их список. |
> | Действие | Microsoft.Resources/links/write | Создает или обновляет ссылку на ресурс. |
> | Действие | Microsoft.Resources/marketplace/purchase/action | Покупка ресурса на Marketplace. |
> | Действие | Microsoft.Resources/providers/read | Возвращает список поставщиков. |
> | Действие | Microsoft.Resources/resources/read | Возвращает список ресурсов на основе фильтров. |
> | Действие | Microsoft.Resources/subscriptions/locations/read | Возвращает список поддерживаемых расположений. |
> | Действие | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Действие | Microsoft.Resources/subscriptions/providers/read | Возвращает поставщики ресурсов или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/read | Возвращает список подписок. |
> | Действие | Microsoft.Resources/subscriptions/resourceGroups/delete | Удаляет группу ресурсов со всеми ресурсами. |
> | Действие | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Возвращает состояния операций развертывания или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Создает или обновляет развертывание. |
> | Действие | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Перемещает ресурсы из одной группы ресурсов в другую. |
> | Действие | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Возвращает ресурсы группы ресурсов. |
> | Действие | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Проверяет перемещение ресурсов из одной группы ресурсов в другую. |
> | Действие | Microsoft.Resources/subscriptions/resourceGroups/write | Создает или обновляет группу ресурсов. |
> | Действие | Microsoft.Resources/subscriptions/resources/read | Возвращает ресурсы подписки. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/delete | Удаляет тег подписки. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/read | Возвращает теги подписки или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Удаляет значение тега подписки. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Возвращает значения тегов подписки или выводит их список. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Добавляет значение тега подписки. |
> | Действие | Microsoft.Resources/subscriptions/tagNames/write | Добавляет тег подписки. |
> | Действие | Microsoft.Resources/tenants/read | Возвращает список клиентов. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Scheduler/jobcollections/delete | Удаляет коллекцию заданий. |
> | Действие | Microsoft.Scheduler/jobcollections/disable/action | Отключает коллекцию заданий. |
> | Действие | Microsoft.Scheduler/jobcollections/enable/action | Включает коллекцию заданий. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/delete | Удаляет задание. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Создает определение приложения логики на основе задания планировщика. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Получает журнал заданий. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/read | Возвращает задание. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/run/action | Выполняет задание. |
> | Действие | Microsoft.Scheduler/jobcollections/jobs/write | Создает или обновляет задание. |
> | Действие | Microsoft.Scheduler/jobcollections/read | Возвращает коллекцию заданий. |
> | Действие | Microsoft.Scheduler/jobcollections/write | Создает или обновляет коллекцию заданий. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Search/checkNameAvailability/action | Проверяет доступность имени службы. |
> | Действие | Microsoft.Search/operations/read | Выводит список всех доступных операций поставщика Microsoft.Search. |
> | Действие | Microsoft.Search/register/action | Регистрирует подписку для поставщика ресурсов службы поиска и позволяет создавать службы поиска. |
> | Действие | Microsoft.Search/searchServices/createQueryKey/action | Создает ключ запроса. |
> | Действие | Microsoft.Search/searchServices/delete | Удаляет службу поиска. |
> | Действие | Microsoft.Search/searchServices/deleteQueryKey/delete | Удаляет ключ запроса. |
> | Действие | Microsoft.Search/searchServices/diagnosticSettings/read | Чтение параметра диагностики для ресурса. |
> | Действие | Microsoft.Search/searchServices/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Search/searchServices/listAdminKeys/action | Считывает ключи администратора. |
> | Действие | Microsoft.Search/searchServices/listQueryKeys/read | Возвращает список ключей API запроса для данной службы поиска Azure. |
> | Действие | Microsoft.Search/searchServices/logDefinitions/read | Получение доступных журналов службы поиска. |
> | Действие | Microsoft.Search/searchServices/metricDefinitions/read | Получение доступных метрик службы поиска. |
> | Действие | Microsoft.Search/searchServices/read | Считывает службу поиска. |
> | Действие | Microsoft.Search/searchServices/regenerateAdminKey/action | Повторно создает ключ администратора. |
> | Действие | Microsoft.Search/searchServices/start/action | Запускает службу поиска. |
> | Действие | Microsoft.Search/searchServices/stop/action | Останавливает службу поиска. |
> | Действие | Microsoft.Search/searchServices/write | Создает или обновляет службу поиска. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Security/alerts/read | Получение всех доступных оповещений системы безопасности. |
> | Действие | Microsoft.Security/applicationWhitelistings/read | Возвращает список разрешений приложения. |
> | Действие | Microsoft.Security/applicationWhitelistings/write | Создает новый список разрешений приложения или обновляет существующий. |
> | Действие | Microsoft.Security/complianceResults/read | Получение результатов соответствия для ресурса. |
> | Действие | Microsoft.Security/locations/alerts/activate/action | Активация оповещения системы безопасности. |
> | Действие | Microsoft.Security/locations/alerts/dismiss/action | Отклонение оповещения системы безопасности. |
> | Действие | Microsoft.Security/locations/alerts/read | Получение всех доступных оповещений системы безопасности. |
> | Действие | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Инициирует политику JIT-доступа к сети. |
> | Действие | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Возвращает политики JIT-доступа к сети. |
> | Действие | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Создает новую политику JIT-доступа к сети или обновляет существующую. |
> | Действие | Microsoft.Security/locations/read | Получение расположения данных безопасности. |
> | Действие | Microsoft.Security/locations/tasks/activate/action | Активирует рекомендацию по безопасности. |
> | Действие | Microsoft.Security/locations/tasks/dismiss/action | Закрывает рекомендацию по безопасности. |
> | Действие | Microsoft.Security/locations/tasks/read | Возвращает все доступные рекомендации по безопасности. |
> | Действие | Microsoft.Security/locations/tasks/resolve/action | Разрешение рекомендации по безопасности. |
> | Действие | Microsoft.Security/locations/tasks/start/action | Запуск рекомендации по безопасности. |
> | Действие | Microsoft.Security/policies/read | Возвращает политику безопасности. |
> | Действие | Microsoft.Security/policies/write | Обновляет политику безопасности. |
> | Действие | Microsoft.Security/pricings/delete | Удаление параметров ценообразования для области. |
> | Действие | Microsoft.Security/pricings/read | Получение параметров ценообразования для области. |
> | Действие | Microsoft.Security/pricings/write | Обновление параметров ценообразования для области. |
> | Действие | Microsoft.Security/register/action | Регистрация подписки в центре безопасности Azure. |
> | Действие | Microsoft.Security/securityContacts/delete | Удаление контактного лица по вопросам безопасности. |
> | Действие | Microsoft.Security/securityContacts/read | Получение контактного лица по вопросам безопасности. |
> | Действие | Microsoft.Security/securityContacts/write | Обновление контактного лица по вопросам безопасности. |
> | Действие | Microsoft.Security/securitySolutions/delete | Удаляет решение безопасности. |
> | Действие | Microsoft.Security/securitySolutions/read | Возвращает решения безопасности. |
> | Действие | Microsoft.Security/securitySolutions/write | Создает новое решение безопасности или обновляет существующее. |
> | Действие | Microsoft.Security/securitySolutionsReferenceData/read | Возвращает эталонные данные решений безопасности. |
> | Действие | Microsoft.Security/securityStatuses/read | Возвращает состояния работоспособности ресурсов Azure. |
> | Действие | Microsoft.Security/securityStatusesSummaries/read | Получение сводок состояния безопасности для области. |
> | Действие | Microsoft.Security/tasks/read | Возвращает все доступные рекомендации по безопасности. |
> | Действие | Microsoft.Security/webApplicationFirewalls/delete | Удаляет брандмауэр веб-приложения. |
> | Действие | Microsoft.Security/webApplicationFirewalls/read | Возвращает брандмауэры веб-приложения. |
> | Действие | Microsoft.Security/webApplicationFirewalls/write | Создает новый брандмауэр веб-приложения или обновляет существующий. |
> | Действие | Microsoft.Security/workspaceSettings/connect/action | Изменение параметров повторного подключения к рабочей области. |
> | Действие | Microsoft.Security/workspaceSettings/delete | Удаление параметров рабочей области. |
> | Действие | Microsoft.Security/workspaceSettings/read | Получение параметров рабочей области. |
> | Действие | Microsoft.Security/workspaceSettings/write | Обновление параметров рабочей области. |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.SecurityGraph/diagnosticsettings/delete | Удаление параметра диагностики. |
> | Действие | Microsoft.SecurityGraph/diagnosticsettings/read | Чтение параметра диагностики. |
> | Действие | Microsoft.SecurityGraph/diagnosticsettings/write | Запись параметра диагностики. |
> | Действие | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Чтение категорий параметра диагностики. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ServiceBus/checkNameAvailability/action | Проверяет доступность пространства имен в заданной подписке. |
> | Действие | Microsoft.ServiceBus/checkNamespaceAvailability/action | Проверяет доступность пространства имен в заданной подписке. Этот API устарел. Вместо него используйте CheckNameAvailability. |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/action | Обновление правила авторизации для пространства имен. Этот API больше не поддерживается. Чтобы обновить правило авторизации для пространства имен, используйте вызов PUT. Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Удаляет правило авторизации для пространства имен. Невозможно удалить правило авторизации пространства имен по умолчанию.  |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Возвращает строку подключения к пространству имен. |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/read | Возвращает список описаний правил авторизации пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.ServiceBus/namespaces/authorizationRules/write | Создает правила авторизации уровня пространства имен и обновляет его свойства. Можно обновить права доступа, первичный ключ и вторичный ключ для правил авторизации. |
> | Действие | Microsoft.ServiceBus/namespaces/Delete | Удаляет ресурс пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Получение ключей правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Получение правил авторизации основного пространства имен для аварийного восстановления. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Отключение аварийного восстановления и остановка репликации изменений из основного пространства имен в дополнительное. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Проверка доступности псевдонима пространства имен в заданной подписке. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Удаление конфигурации аварийного восстановления, связанной с пространством имен. Эта операция может быть вызвана только через основное пространство имен. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Вызов отработки отказа географического аварийного восстановления и перенастройка псевдонима пространства имен для указания на дополнительное пространство. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Получение конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Создание или обновление конфигурации аварийного восстановления, связанной с пространством имен. |
> | Действие | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Удаление фильтра сетки событий, связанного с пространством имен. |
> | Действие | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Получение фильтра сетки событий, связанного с пространством имен. |
> | Действие | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Создание или обновление фильтра сетки событий, связанного с пространством имен. |
> | Действие | Microsoft.ServiceBus/namespaces/eventhubs/read | Возвращает список описаний ресурсов концентратора событий. |
> | Действие | Microsoft.ServiceBus/namespaces/messagingPlan/read | Получение плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.ServiceBus/namespaces/messagingPlan/write | Обновление плана обмена сообщениями для пространства имен.<br>Это нерекомендуемый API.<br>Свойства, предоставляемые через ресурс MessagingPlan, перемещены в ресурс (родительского) пространства имен в более поздних версиях API.<br>Эта операция не поддерживается для API версии 2017-04-01. |
> | Действие | Microsoft.ServiceBus/namespaces/migrate/action | Операция переноса пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/operationresults/read | Получение состояния операции пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает список описаний параметров диагностики пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Возвращает список описаний параметров диагностики пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Возвращает список описаний ресурсов журналов пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Возвращает список описаний ресурсов метрик пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Операция обновления очереди. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Операция удаления правил авторизации очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Возвращает строку подключения к очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Возвращает список правил авторизации очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Создает правила авторизации очереди и обновляет ее свойства. Можно обновить права доступа к правилам авторизации. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/Delete | Операция удаления ресурса очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/read | Возвращает список описаний ресурсов очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/queues/write | Создает или обновляет свойства очереди. |
> | Действие | Microsoft.ServiceBus/namespaces/read | Возвращает список описаний ресурсов пространства имен. |
> | Действие | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Удаляет пространство имен ACS. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Операция обновления раздела. Эта операция не поддерживается для API версии 2017-04-01. Правила авторизации. Чтобы обновить правило авторизации, используйте вызов PUT. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Операция удаления правил авторизации раздела. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Возвращает строку подключения к разделу. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Возвращает список правил авторизации раздела. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Повторно создает первичный или вторичный ключ для ресурса. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Создает правила авторизации раздела и обновляет его свойства. Можно обновить права доступа к правилам авторизации. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/Delete | Операция удаления ресурса раздела. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/read | Возвращает список описаний ресурсов раздела. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Операция удаления ресурса подписки на раздел. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Возвращает список описаний ресурсов подписки на раздел. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Операция удаления ресурса правила. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Возвращает список описаний ресурсов правила. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Создает или обновляет свойства правила. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Создает или обновляет свойства подписки на раздел. |
> | Действие | Microsoft.ServiceBus/namespaces/topics/write | Создает или обновляет свойства раздела. |
> | Действие | Microsoft.ServiceBus/namespaces/write | Создает ресурс пространства имен и обновляет его свойства. К этим свойствам относятся теги и пространство имен. |
> | Действие | Microsoft.ServiceBus/operations/read | Получение операций. |
> | Действие | Microsoft.ServiceBus/register/action | Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины. |
> | Действие | Microsoft.ServiceBus/sku/read | Получение списка описаний ресурсов номеров SKU. |
> | Действие | Microsoft.ServiceBus/sku/regions/read | Получение списка описаний ресурсов регионов для номеров SKU. |
> | Действие | Microsoft.ServiceBus/unregister/action | Регистрирует подписку для поставщика ресурсов служебной шины и позволяет создавать ресурсы служебной шины. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.ServiceFabric/clusters/applications/delete | Удаление любого приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/read | Чтение любого приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/delete | Удаление любой службы. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Чтение любого раздела. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Чтение любой реплики. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/read | Чтение любой службы. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Чтение состояния любой службы. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/services/write | Создание или обновление любой службы. |
> | Действие | Microsoft.ServiceFabric/clusters/applications/write | Создание или обновление любого приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Удаление любого типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/read | Чтение любого типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Удаление любой версии типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Чтение любой версии типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Создание или обновление любой версии типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/applicationTypes/write | Создание или обновление любого типа приложения. |
> | Действие | Microsoft.ServiceFabric/clusters/delete | Удаление любого кластера. |
> | Действие | Microsoft.ServiceFabric/clusters/nodes/read | Чтение любого узла. |
> | Действие | Microsoft.ServiceFabric/clusters/read | Чтение любого кластера. |
> | Действие | Microsoft.ServiceFabric/clusters/statuses/read | Чтение состояния любого кластера. |
> | Действие | Microsoft.ServiceFabric/clusters/write | Создание или обновление любого кластера. |
> | Действие | Microsoft.ServiceFabric/locations/clusterVersions/read | Чтение любой версии кластера. |
> | Действие | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Чтение любой версии кластера для конкретной среды. |
> | Действие | Microsoft.ServiceFabric/locations/operationresults/read | Чтение результатов операций. |
> | Действие | Microsoft.ServiceFabric/locations/operations/read | Чтение операций по расположению. |
> | Действие | Microsoft.ServiceFabric/operations/read | Чтение доступных операций. |
> | Действие | Microsoft.ServiceFabric/register/action | Регистрация любого действия. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.SignalRService/checknameavailability/action | Проверяет, доступно ли имя для новой службы SignalR. |
> | Действие | Microsoft.SignalRService/register/action | Регистрирует поставщика ресурсов Microsoft.SignalRService в подписке. |
> | Действие | Microsoft.SignalRService/SignalR/delete | Удаляет всю службу SignalR. |
> | Действие | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Получает доступные метрики для SignalR. |
> | Действие | Microsoft.SignalRService/SignalR/read | Просмотр параметров и конфигураций SignalR на портале управления или с помощью API. |
> | Действие | Microsoft.SignalRService/SignalR/write | Изменяет параметры и конфигурации SignalR на портале управления или с помощью API. |
> | Действие | Microsoft.SignalRService/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.SignalRService в подписке. |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Solutions/applicationDefinitions/delete | Удаление определения приложения. |
> | Действие | Microsoft.Solutions/applicationDefinitions/read | Извлечение списка определений приложений. |
> | Действие | Microsoft.Solutions/applicationDefinitions/write | Добавление или изменение определения приложения. |
> | Действие | Microsoft.Solutions/applications/delete | Удаление приложения. |
> | Действие | Microsoft.Solutions/applications/read | Извлечение списка приложений. |
> | Действие | Microsoft.Solutions/applications/write | Создает приложение. |
> | Действие | Microsoft.Solutions/locations/operationStatuses/read | Считывает состояние операции ресурса. |
> | Действие | Microsoft.Solutions/register/action | Регистрация в решениях. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Sql/checkNameAvailability/action | Проверка того, доступно ли данное имя сервера для подготовки по всему миру для данной подписки. |
> | Действие | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действие | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере. |
> | Действие | Microsoft.Sql/locations/capabilities/read | Получение сведений о возможностях этой подписки в данном расположении. |
> | Действие | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Получение состояния операции базы данных. |
> | Действие | Microsoft.Sql/locations/databaseOperationResults/read | Получение состояния операции базы данных. |
> | Действие | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Возвращение выполняемых операций на удаленном сервере. |
> | Действие | Microsoft.Sql/locations/deletedServerOperationResults/read | Возвращение выполняемых операций на удаленном сервере. |
> | Действие | Microsoft.Sql/locations/deletedServers/read | Возвращение списка удаленных серверов или получение свойств для указанного удаленного сервера. |
> | Действие | Microsoft.Sql/locations/deletedServers/recover/action | Восстановление удаленного сервера. |
> | Действие | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Удаление правил виртуальной сети, связанных с виртуальной сетью или подсетью. |
> | Действие | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Возвращение асинхронной операции Azure для эластичного пула. |
> | Действие | Microsoft.Sql/locations/elasticPoolOperationResults/read | Возвращение результата операции эластичного пула. |
> | Действие | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действие | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Извлечение результата операции настройки расширенной политики аудита больших двоичных объектов на сервере. |
> | Действие | Microsoft.Sql/locations/instanceFailoverGroups/delete | Удаление имеющейся группы отработки отказа экземпляра. |
> | Действие | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Выполнение плановой отработки отказа в имеющейся группе отработки отказа экземпляра. |
> | Действие | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Выполнение принудительной отработки отказа в имеющейся группе отработки отказа экземпляра. |
> | Действие | Microsoft.Sql/locations/instanceFailoverGroups/read | Возвращение списка групп отработки отказа экземпляра или свойств для указанной группы экземпляра. |
> | Действие | Microsoft.Sql/locations/instanceFailoverGroups/write | Создание группы отработки отказа экземпляра с указанными параметрами либо обновление свойств или тегов указанной группы экземпляра. |
> | Действие | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Завершение операции восстановления управляемой базы данных. |
> | Действие | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных. |
> | Действие | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Возвращение выполняемых операций прозрачного шифрования данных в управляемой базе данных. |
> | Действие | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Возвращение сведений об асинхронной операции для указанного сетевого интерфейса Azure. |
> | Действие | Microsoft.Sql/locations/networkInterfaceOperationResults/read | Возвращение сведений об операции для указанного сетевого интерфейса. |
> | Действие | Microsoft.Sql/locations/read | Получение расположений, доступных для данной подписки. |
> | Действие | Microsoft.Sql/locations/syncAgentOperationResults/read | Получение результата операции ресурса агента синхронизации. |
> | Действие | Microsoft.Sql/locations/syncDatabaseIds/read | Получение идентификаторов базы данных синхронизации для конкретного региона и подписки. |
> | Действие | Microsoft.Sql/locations/syncGroupOperationResults/read | Получение результата операции синхронизации группы. |
> | Действие | Microsoft.Sql/locations/syncMemberOperationResults/read | Получение результата операции синхронизации элемента. |
> | Действие | Microsoft.Sql/locations/usages/read | Получение коллекции метрик использования для этой подписки в расположении. |
> | Действие | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Возвращение сведений об асинхронной операции Azure для указанных правил виртуальной сети.  |
> | Действие | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Возвращение сведений о правилах операции для указанных правил виртуальной сети.  |
> | Действие | Microsoft.Sql/managedInstances/administrators/delete | Удаление существующего администратора управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/administrators/read | Получение списка администраторов управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/administrators/write | Создание или обновление администратора управляемого экземпляра с указанными параметрами. |
> | Действие | Microsoft.Sql/managedInstances/databases/delete | Удаление существующей управляемой базы данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/read | Получение существующей управляемой базы данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Извлечение сведений о политике обнаружения угроз для базы данных, настроенной для заданной управляемой базы данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданной управляемой базы данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/securityEvents/read | Получение событий безопасности управляемой базы данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Получение сведений о прозрачном шифровании данных в данной управляемой базе данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Изменение прозрачного шифрования данных в данной управляемой базе данных. |
> | Действие | Microsoft.Sql/managedInstances/databases/write | Создает новую базу данных или обновляет имеющуюся. |
> | Действие | Microsoft.Sql/managedInstances/delete | Удаление существующего управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/metricDefinitions/read | Получение определений метрик управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/metrics/read | Получение метрик управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/read | Возвращение списка управляемых экземпляров или получение свойств указанного управляемого экземпляра. |
> | Действие | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Извлечение сведений о политике обнаружения угроз, настроенной на данном управляемом сервере. |
> | Действие | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Изменение политики обнаружения угроз на данном управляемом сервере. |
> | Действие | Microsoft.Sql/managedInstances/write | Создание управляемого экземпляра с указанными параметрами либо обновление свойств или тегов указанного управляемого экземпляра. |
> | Действие | Microsoft.Sql/operations/read | Получение доступных операций REST. |
> | Действие | Microsoft.Sql/register/action | Регистрация подписки для поставщика ресурсов базы данных SQL Azure и разрешение создания баз данных Microsoft SQL. |
> | Действие | Microsoft.Sql/servers/administratorOperationResults/read | Возвращение выполняемых операций администраторами серверов. |
> | Действие | Microsoft.Sql/servers/administrators/delete | Удаление администратора сервера. |
> | Действие | Microsoft.Sql/servers/administrators/read | Извлекает сведения об администраторе сервера. |
> | Действие | Microsoft.Sql/servers/administrators/write | Создает или обновляет учетные данные администратора сервера. |
> | Действие | Microsoft.Sql/servers/advisors/read | Возвращает список доступных помощников для сервера. |
> | Действие | Microsoft.Sql/servers/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для сервера. |
> | Действие | Microsoft.Sql/servers/advisors/recommendedActions/write | Применяет рекомендуемое действие к серверу. |
> | Действие | Microsoft.Sql/servers/advisors/write | Обновляет состояние автовыполнения Помощника на уровне сервера. |
> | Действие | Microsoft.Sql/servers/auditingPolicies/read | Извлекает сведения о политике аудита таблиц по умолчанию, настроенной на заданном сервере. |
> | Действие | Microsoft.Sql/servers/auditingPolicies/write | Изменяет политику аудита таблиц по умолчанию для заданного сервера. |
> | Действие | Microsoft.Sql/servers/auditingSettings/operationResults/read | Извлекает результат операции настройки политики аудита больших двоичных объектов на сервере. |
> | Действие | Microsoft.Sql/servers/auditingSettings/read | Извлекает сведения о политике аудита больших двоичных объектов, настроенной на заданном сервере. |
> | Действие | Microsoft.Sql/servers/auditingSettings/write | Изменяет политику аудита больших двоичных объектов для заданного сервера. |
> | Действие | Microsoft.Sql/servers/automaticTuning/read | Возвращение параметров автоматической настройки для сервера. |
> | Действие | Microsoft.Sql/servers/automaticTuning/write | Обновление параметров автоматической настройки для сервера и возвращение обновленных параметров. |
> | Действие | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Удаление существующего архивного хранилища резервных копий. |
> | Действие | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | С помощью этой операции можно получить хранилище резервных копий долгосрочного хранения. Возвращает сведения о хранилище, зарегистрированном на данном сервере. |
> | Действие | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | С помощью этой операции можно зарегистрировать на сервере хранилище резервных копий долгосрочного хранения. |
> | Действие | Microsoft.Sql/servers/communicationLinks/delete | Удаление существующего подключения сервера. |
> | Действие | Microsoft.Sql/servers/communicationLinks/read | Возвращение списка подключений для указанного сервера. |
> | Действие | Microsoft.Sql/servers/communicationLinks/write | Создание или обновление подключения сервера. |
> | Действие | Microsoft.Sql/servers/connectionPolicies/read | Возвращение списка политик подключений для указанного сервера. |
> | Действие | Microsoft.Sql/servers/connectionPolicies/write | Создание или обновление политики подключения сервера. |
> | Действие | Microsoft.Sql/servers/databases/advisors/read | Возвращает список доступных помощников для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Применяет рекомендуемое действие к базе данных. |
> | Действие | Microsoft.Sql/servers/databases/advisors/write | Обновляет состояние автовыполнения Помощника на уровне базы данных. |
> | Действие | Microsoft.Sql/servers/databases/auditingPolicies/read | Извлекает сведения о политике аудита таблиц, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/auditingPolicies/write | Изменяет политику аудита таблиц для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/auditingSettings/read | Извлекает сведения о политике аудита больших двоичных объектов, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/auditingSettings/write | Изменяет политику аудита больших двоичных объектов для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/automaticTuning/read | Возвращение параметров автоматической настройки базы данных. |
> | Действие | Microsoft.Sql/servers/databases/automaticTuning/write | Обновление параметров автоматической настройки базы данных и возвращение обновленных параметров. |
> | Действие | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Получение состояния операции базы данных. |
> | Действие | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Возвращение списка политик хранения резервных копий для указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Создание или обновление политики хранения резервных копий базы данных. |
> | Действие | Microsoft.Sql/servers/databases/connectionPolicies/read | Извлекает сведения о политике подключений, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/connectionPolicies/write | Изменяет политику подключений для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Получение списка политик маскировки данных базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Удаление правила политики маскировки данных для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Извлекает сведения о правиле политики маскирования данных, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Изменяет правило политики маскирования данных для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Изменяет политику маскирования данных для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Возвращает сведения о шаге распределенного запроса к хранилищу данных для выбранного идентификатора шага. |
> | Действие | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Возвращает данные запроса на распределение к хранилищу данных для выбранного идентификатора запроса. |
> | Действие | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Получение действий пользователя в экземпляре хранилища данных SQL, включая выполняемые и приостановленные запросы. |
> | Действие | Microsoft.Sql/servers/databases/delete | Удаление имеющейся базы данных. |
> | Действие | Microsoft.Sql/servers/databases/export/action | Экспорт базы данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Изменение расширенной политики аудита больших двоичных объектов для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/extensions/read | Получение коллекции расширений для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/extensions/write | Изменение расширения для конкретной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Получение политик геоизбыточного резервного копирования для конкретной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Создание или обновление политики геоизбыточного резервного копирования базы данных. |
> | Действие | Microsoft.Sql/servers/databases/importExportOperationResults/read | Получение выполняемых операций импорта и экспорта. |
> | Действие | Microsoft.Sql/servers/databases/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действие | Microsoft.Sql/servers/databases/metrics/read | Получение метрик для баз данных. |
> | Действие | Microsoft.Sql/servers/databases/move/action | Переименование базы данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/operationResults/read | Получение состояния операции базы данных. |
> | Действие | Microsoft.Sql/servers/databases/operations/cancel/action | Отмена приостановленной асинхронной операции базы данных SQL Azure, которая еще не завершена. |
> | Действие | Microsoft.Sql/servers/databases/operations/read | Возвращение списка операций, выполняемых в базе данных. |
> | Действие | Microsoft.Sql/servers/databases/pause/action | Приостановка работы базы данных хранилища данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Возвращает доступные журналы для баз данных. |
> | Действие | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для баз данных. |
> | Действие | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Возвращение коллекции текстов запросов, которые соответствуют заданным параметрам. |
> | Действие | Microsoft.Sql/servers/databases/queryStore/read | Возвращение текущих значений параметров хранилища запросов для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/queryStore/write | Обновляет параметр хранилища запросов для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/read | Возвращение списка баз данных или возвращение свойств указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/delete | Принудительно удаляет отношение репликации. При этом возможна потеря данных. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Выполнение отработки отказа после синхронизации всех изменений из базы данных-источника, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Выполнение немедленной отработки отказа с потенциальной потерей данных, превращая заданную базу данных в базу данных-источник для отношения репликации, а удаленную базу данных-источник — в базу данных-приемник. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/read | Возвращает сведения о каналах репликации, установленных для определенной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Удаляет отношение репликации в принудительном порядке или после синхронизации с партнером. |
> | Действие | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Обновляет режим репликации для ссылки на синхронный или асинхронный режим. |
> | Действие | Microsoft.Sql/servers/databases/restorePoints/action | Создает точку восстановления. |
> | Действие | Microsoft.Sql/servers/databases/restorePoints/delete | Удаляет точку восстановления для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/restorePoints/read | Возвращение списка точек восстановления для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/resume/action | Возобновление работы базы данных хранилища данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/schemas/read | Получение списка схем базы данных. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Извлекает список столбцов таблицы. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Удаление метки конфиденциальности данного столбца. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Получение метки конфиденциальности данного столбца. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Создание или обновление метки конфиденциальности данного столбца. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/read | Получение списка таблиц базы данных. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Извлекает список рекомендаций по индексам для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Применяет рекомендацию по индексу. |
> | Действие | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Изменяет политику обнаружения угроз для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/securityMetrics/read | Возвращение коллекции метрик безопасности базы данных. |
> | Действие | Microsoft.Sql/servers/databases/sensitivityLabels/read | Получение списка меток конфиденциальности для конкретной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Возвращает предложение по масштабированию базы данных на основе статистики выполнения запросов для повышения производительности или снижения затрат. |
> | Действие | Microsoft.Sql/servers/databases/skus/read | Возвращение коллекции доступных номеров SKU для базы данных. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Отмена синхронизации группы синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/delete | Удаление существующей группы синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Возвращение списка схем баз данных концентратора синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/logs/read | Возвращение списка журналов группы синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/read | Возвращение списка групп синхронизации или свойств для указанной группы. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Обновление схемы базы данных концентратора синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Получение результата операции обновления схемы концентратора синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Удаление существующего элемента синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Возвращение списка элементов синхронизации или свойств для указанного элемента. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Обновление схемы элемента синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Получение результата операции обновления схемы элемента синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Возвращение списка схем баз данных элемента синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Создание элемента синхронизации с указанными параметрами либо обновление свойств указанного элемента. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Активация синхронизации группы синхронизации. |
> | Действие | Microsoft.Sql/servers/databases/syncGroups/write | Создание группы синхронизации с указанными параметрами либо обновление свойств указанной группы. |
> | Действие | Microsoft.Sql/servers/databases/topQueries/queryText/action | Возвращает текст Transact-SQL для выбранного идентификатора запроса. |
> | Действие | Microsoft.Sql/servers/databases/topQueries/read | Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период. |
> | Действие | Microsoft.Sql/servers/databases/topQueries/statistics/read | Возвращает статистические данные среды выполнения для выбранного запроса в указанный временной период. |
> | Действие | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Возвращение выполняемых операций прозрачного шифрования данных. |
> | Действие | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Извлекает состояние и сведения о функции безопасности "прозрачное шифрование данных" для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Изменение состояния прозрачного шифрования данных. |
> | Действие | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Обновление работы базы данных хранилища данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/usages/read | Возвращение сведений об использовании базы данных SQL Azure. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Удаление оценки уязвимости указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Извлечение сведений об оценке уязвимости, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Удаление базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Получение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Изменение базовых показателей правила оценки уязвимости для указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Преобразование существующего результата проверки в удобочитаемый формат. Если он уже существует, ничего не происходит. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Выполняет поиск уязвимостей базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Возвращение списка записей сканирования оценки уязвимости базы данных или получение записи для указанного идентификатора сканирования. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Изменение оценки уязвимости указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Выполняет поиск уязвимостей базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Получение результата выполнения сканирования оценки уязвимости базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Извлечение сведений об оценке уязвимости, настроенной для заданной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Изменение оценки уязвимости указанной базы данных. |
> | Действие | Microsoft.Sql/servers/databases/write | Создание базы данных с указанными параметрами либо обновление свойств или тегов для указанной базы данных. |
> | Действие | Microsoft.Sql/servers/delete | Удаление существующего сервера. |
> | Действие | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Удаление существующих конфигураций аварийного восстановления для указанного сервера. |
> | Действие | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Отработка отказа конфигурации аварийного восстановления. |
> | Действие | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Принудительная отработка отказа конфигурации аварийного восстановления. |
> | Действие | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Получение коллекции конфигураций аварийного восстановления, в которых содержится данный сервер. |
> | Действие | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Изменение конфигурации аварийного восстановления сервера. |
> | Действие | Microsoft.Sql/servers/elasticPoolEstimates/read | Возвращает список оценок эластичных пулов, уже созданных на этом сервере. |
> | Действие | Microsoft.Sql/servers/elasticPoolEstimates/write | Создает оценку эластичного пула для указанных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/advisors/read | Возвращает список доступных помощников для эластичного пула. |
> | Действие | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Возвращает список рекомендуемых действий указанного помощника для эластичного пула. |
> | Действие | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Применяет рекомендуемое действие к эластичному пулу. |
> | Действие | Microsoft.Sql/servers/elasticPools/advisors/write | Обновляет состояние автовыполнения Помощника на уровне эластичного пула. |
> | Действие | Microsoft.Sql/servers/elasticPools/databases/read | Получение списка баз данных в эластичном пуле. |
> | Действие | Microsoft.Sql/servers/elasticPools/delete | Удаление имеющегося эластичного пула. |
> | Действие | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Извлекает действия и сведения о заданном пуле эластичных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Извлекает действия заданной базы данных, входящей в пул эластичных баз данных, а также сведения о ней. |
> | Действие | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Возвращает типы метрик, доступных для пулов эластичных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/metrics/read | Возвращение метрик для пулов эластичных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Отмена приостановленной асинхронной операции эластичного пула SQL Azure, которая еще не завершена. |
> | Действие | Microsoft.Sql/servers/elasticPools/operations/read | Возвращение списка операций, выполняемых в эластичном пуле. |
> | Действие | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Возвращает типы метрик, доступных для пулов эластичных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/read | Извлечение сведений о пуле эластичных баз данных для заданного сервера. |
> | Действие | Microsoft.Sql/servers/elasticPools/skus/read | Возвращение коллекции доступных номеров SKU для пула эластичных баз данных. |
> | Действие | Microsoft.Sql/servers/elasticPools/write | Создание эластичного пула или изменение свойств существующего. |
> | Действие | Microsoft.Sql/servers/encryptionProtector/read | Возвращение списка предохранителей шифрования сервера или получение свойств для указанного предохранителя. |
> | Действие | Microsoft.Sql/servers/encryptionProtector/write | Обновление свойств указанного предохранителя шифрования сервера. |
> | Действие | Microsoft.Sql/servers/extendedAuditingSettings/read | Извлечение сведений о расширенной политике аудита больших двоичных объектов, настроенной на заданном сервере. |
> | Действие | Microsoft.Sql/servers/extendedAuditingSettings/write | Изменение расширенного аудита больших двоичных объектов для заданного сервера. |
> | Действие | Microsoft.Sql/servers/failoverGroups/delete | Удаление существующей группы отработки отказа. |
> | Действие | Microsoft.Sql/servers/failoverGroups/failover/action | Выполнение плановой отработки отказа в существующей группе отработки отказа. |
> | Действие | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Выполнение принудительной отработки отказа в существующей группе отработки отказа. |
> | Действие | Microsoft.Sql/servers/failoverGroups/read | Возвращение списка групп отработки отказа или свойств для указанной группы. |
> | Действие | Microsoft.Sql/servers/failoverGroups/write | Создание группы отработки отказа с указанными параметрами либо обновление свойств или тегов указанной группы. |
> | Действие | Microsoft.Sql/servers/firewallRules/delete | Удаление существующего правила брандмауэра сервера. |
> | Действие | Microsoft.Sql/servers/firewallRules/read | Возвращение списка правил брандмауэра сервера или получение свойства для указанного правила. |
> | Действие | Microsoft.Sql/servers/firewallRules/write | Создание правила брандмауэра сервера с указанными параметрами, обновление свойств указанного правила или перезапись всех существующих правил новыми правилами брандмауэра сервера. |
> | Действие | Microsoft.Sql/servers/import/action | Создает базу данных на сервере и развертывает схему и данные из пакета DACPAC. |
> | Действие | Microsoft.Sql/servers/importExportOperationResults/read | Получение выполняемых операций импорта и экспорта. |
> | Действие | Microsoft.Sql/servers/keys/delete | Удаление существующего ключа сервера. |
> | Действие | Microsoft.Sql/servers/keys/read | Возвращение списка ключей сервера или возвращение свойств указанного ключа сервера. |
> | Действие | Microsoft.Sql/servers/keys/write | Создание ключа с указанными параметрами либо обновление свойств или тегов указанного ключа сервера. |
> | Действие | Microsoft.Sql/servers/networkInterfaces/read | Возвращение свойств для указанного сетевого интерфейса. |
> | Действие | Microsoft.Sql/servers/networkInterfaces/write | Создание сетевого интерфейса с указанными параметрами либо обновление свойств или тегов указанного сетевого интерфейса. |
> | Действие | Microsoft.Sql/servers/operationResults/read | Возвращение выполняемых операций сервера. |
> | Действие | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Возвращение типов метрик, доступных для серверов. |
> | Действие | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Действие | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Извлекает метрики для рекомендуемых пулов эластичных баз данных для заданного сервера. |
> | Действие | Microsoft.Sql/servers/recommendedElasticPools/read | Извлекает рекомендацию для пулов эластичных баз данных, позволяющую снизить затраты или повысить производительность, на основе журнала использования ресурсов. |
> | Действие | Microsoft.Sql/servers/recoverableDatabases/read | Эта операция используется для аварийного восстановления динамической базы данных. Она позволяет восстановить базу данных до последней известной корректной точки. Возвращение сведений о последней корректной резервной копии, но без восстановления базы данных. |
> | Действие | Microsoft.Sql/servers/restorableDroppedDatabases/read | Получение списка баз данных, которые были удалены на заданном сервере, но остались в политике хранения. |
> | Действие | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Извлечение результатов операции записи политики обнаружения угроз на сервере. |
> | Действие | Microsoft.Sql/servers/securityAlertPolicies/read | Извлекает сведения о политике обнаружения угроз, настроенной на данном сервере. |
> | Действие | Microsoft.Sql/servers/securityAlertPolicies/write | Изменение политики обнаружения угроз для заданного сервера. |
> | Действие | Microsoft.Sql/servers/serviceObjectives/read | Извлекает список целей уровня обслуживания (также известных как уровни производительности), доступных на данном сервере. |
> | Действие | Microsoft.Sql/servers/syncAgents/delete | Удаление существующего агента синхронизации. |
> | Действие | Microsoft.Sql/servers/syncAgents/generateKey/action | Создание ключа регистрации агента синхронизации. |
> | Действие | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Получение списка баз данных, связанных с агентом синхронизации. |
> | Действие | Microsoft.Sql/servers/syncAgents/read | Возвращение списка агентов синхронизации или свойств для указанного агента. |
> | Действие | Microsoft.Sql/servers/syncAgents/write | Создание агента синхронизации с указанными параметрами либо обновление свойств указанного агента. |
> | Действие | Microsoft.Sql/servers/usages/read | Возвращает квоту DTU сервера и текущие данные об использовании DTU для всех баз данных на сервере. |
> | Действие | Microsoft.Sql/servers/virtualNetworkRules/delete | Удаление существующего правила виртуальной сети. |
> | Действие | Microsoft.Sql/servers/virtualNetworkRules/read | Возвращение списка правил виртуальной сети или свойств указанного правила виртуальной сети. |
> | Действие | Microsoft.Sql/servers/virtualNetworkRules/write | Создание правила виртуальной сети с указанными параметрами либо обновление свойств или тегов указанного правила виртуальной сети. |
> | Действие | Microsoft.Sql/servers/write | Создание сервера с указанными параметрами либо обновление свойств или тегов указанного сервера. |
> | Действие | Microsoft.Sql/unregister/action | Отмена регистрации подписки для поставщика ресурсов базы данных SQL Azure с возможностью создания баз данных Microsoft SQL. |
> | Действие | Microsoft.Sql/virtualClusters/read | Возвращение списка виртуальных кластеров или свойств указанного виртуального кластера. |
> | Действие | Microsoft.Sql/virtualClusters/write | Обновление тегов виртуального кластера. |

## <a name="microsoftstorage"></a>Microsoft.Storage;

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Storage/checknameavailability/read | Проверяет, является ли имя учетной записи допустимым и неиспользуемым. |
> | Действие | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Уведомляет поставщик ресурсов Microsoft.Storage о том, что удаляется виртуальная сеть или подсеть. |
> | Действие | Microsoft.Storage/operations/read | Опрашивает состояние асинхронной операции. |
> | Действие | Microsoft.Storage/register/action | Регистрирует подписку для поставщика ресурсов службы хранилища и позволяет создавать учетные записи хранения. |
> | Действие | Microsoft.Storage/skus/read | Выводит список номеров SKU, поддерживаемых Microsoft.Storage. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Снятие с контейнера больших двоичных объектов удержания по юридическим причинам. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Возвращение результата удаления контейнера. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Удаление политики неизменяемости контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Расширение политики неизменяемости контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Блокировка политики неизменяемости контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Получение политики неизменяемости контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Размещение политики неизменяемости контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/read | Возвращение контейнера или списка контейнеров. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Постановка контейнера больших двоичных объектов на удержание по юридическим причинам. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/containers/write | Возвращение результата размещения или аренды контейнера больших двоичных объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Получение списка определений метрик хранилища Майкрософт. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/read | Возвращение свойств или статистики службы BLOB-объектов. |
> | Действие | Microsoft.Storage/storageAccounts/blobServices/write | Возвращение результата настройки свойств службы BLOB-объектов. |
> | Действие | Microsoft.Storage/storageAccounts/delete | Удаляет существующую учетную запись хранения. |
> | Действие | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Получение списка определений метрик хранилища Майкрософт. |
> | Действие | Microsoft.Storage/storageAccounts/listAccountSas/action | Возвращает маркер SAS для указанной учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/listServiceSas/action | Возвращение токена SAS службы для указанной учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Получение списка определений метрик хранилища Майкрософт. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Получение списка определений метрик хранилища Майкрософт. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Возвращение результата удаления очереди. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/queues/read | Возвращение очереди или списка очередей. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/queues/write | Возвращение результата записи очереди. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/read | Возвращение свойств или статистики службы очередей. |
> | Действие | Microsoft.Storage/storageAccounts/queueServices/write | Возвращение результата настройки свойств службы очередей. |
> | Действие | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Создает или обновляет параметры диагностики учетной записи хранения. |
> | Действие | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Получение параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Создание или обновление параметра диагностики для ресурса. |
> | Действие | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Получение списка определений метрик хранилища Майкрософт. |
> | Действие | Microsoft.Storage/storageAccounts/write | Создает новую учетную запись хранения с указанными параметрами, обновляет свойства или теги указанной существующей учетной записи хранения или добавляет в нее личный домен. |
> | Действие | Microsoft.Storage/usages/read | Возвращает предельное и текущее число используемых ресурсов для указанной подписки. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | microsoft.storagesync/storageSyncServices/delete | Удаление служб синхронизации хранилища. |
> | Действие | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для служб синхронизации хранилища. |
> | Действие | microsoft.storagesync/storageSyncServices/read | Чтение служб синхронизации хранилища. |
> | Действие | microsoft.storagesync/storageSyncServices/registeredServers/delete | Удаление любого зарегистрированного сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/registeredServers/read | Чтение зарегистрированного сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/registeredServers/write | Создание или обновление любого зарегистрированного сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Удаление конечных точек облака. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | API расположения для асинхронных вызовов резервного копирования. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Это действие следует вызывать после резервного копирования. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Это действие следует вызывать после восстановления. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Это действие следует вызывать до резервного копирования. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Это действие следует вызывать до восстановления. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Чтение конечных точек облака. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Восстановление пакетов пульса. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Создание или обновление конечных точек облака. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/delete | Удаление любой группы синхронизации. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/read | Чтение всех групп синхронизации. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Удаление любой конечной точки сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Чтение конечных точек сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Действие для отзыва файлов на сервере. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Создание или обновление конечных точек сервера. |
> | Действие | microsoft.storagesync/storageSyncServices/syncGroups/write | Создание или обновление групп синхронизации. |
> | Действие | microsoft.storagesync/storageSyncServices/write | Создание или обновление служб синхронизации хранилища. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.StorSimple/managers/accessControlRecords/delete | Удаляет записи контроля доступа. |
> | Действие | Microsoft.StorSimple/managers/accessControlRecords/read | Выводит список записей контроля доступа или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/accessControlRecords/write | Создает или обновляет записи контроля доступа. |
> | Действие | Microsoft.StorSimple/managers/alerts/read | Выводит список оповещений или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/bandwidthSettings/delete | Удаляет существующие параметры пропускной способности (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/bandwidthSettings/read | Выводит список параметров пропускной способности (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/bandwidthSettings/write | Создает или обновляет параметры пропускной способности (только для серии 8000). |
> | Действие | Microsoft.StorSimple/Managers/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Действие | Microsoft.StorSimple/managers/clearAlerts/action | Очищает все оповещения, связанные с диспетчером устройств. |
> | Действие | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Выводит список конфигураций, поддерживаемых облачным устройством. |
> | Действие | Microsoft.StorSimple/managers/configureDevice/action | Настраивает устройство. |
> | Действие | Microsoft.StorSimple/managers/delete | Удаляет диспетчеры устройств. |
> | Действие | Microsoft.StorSimple/Managers/delete | Операция удаления хранилища удаляет указанный ресурс Azure типа "хранилище". |
> | Действие | Microsoft.StorSimple/managers/devices/alertSettings/read | Возвращает параметры оповещения или выводит их список. |
> | Действие | Microsoft.StorSimple/managers/devices/alertSettings/write | Создает или обновляет параметры оповещения. |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Выполняет ручную архивацию всех томов, защищенных с помощью политики архивации, для создания их резервной копии по запросу. |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Удаляет существующие политики архивации (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/read | Выводит список политик архивации (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Удаляет существующие расписания. |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Выводит список расписаний. |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Создает или обновляет расписания. |
> | Действие | Microsoft.StorSimple/managers/devices/backupPolicies/write | Создает или обновляет политики архивации (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/backups/delete | Удаляет набор архивации. |
> | Действие | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Клонирует общедоступный ресурс или том с помощью элемента архива. |
> | Действие | Microsoft.StorSimple/managers/devices/backups/read | Возвращает набор архивации или выводит его. |
> | Действие | Microsoft.StorSimple/managers/devices/backups/restore/action | Восстанавливает все тома из набора архивации. |
> | Действие | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Удаляет группы расписаний архивации. |
> | Действие | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Выводит список групп расписаний архивации или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Создает или обновляет группы расписаний архивации. |
> | Действие | Microsoft.StorSimple/managers/devices/chapSettings/delete | Удаляет параметры CHAP. |
> | Действие | Microsoft.StorSimple/managers/devices/chapSettings/read | Возвращает параметры CHAP или выводит их список. |
> | Действие | Microsoft.StorSimple/managers/devices/chapSettings/write | Создает или обновляет параметры CHAP. |
> | Действие | Microsoft.StorSimple/managers/devices/deactivate/action | Деактивирует устройство. |
> | Действие | Microsoft.StorSimple/managers/devices/delete | Удаляет устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/download/action | Скачивает обновления для устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/failover/action | Выполняет отработку отказа устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Архивирует файловый сервер. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/delete | Удаляет файловые серверы. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/read | Выводит список файловых серверов или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Удаляет общедоступные ресурсы. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Выводит список общедоступных ресурсов или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Создает или обновляет общедоступные ресурсы. |
> | Действие | Microsoft.StorSimple/managers/devices/fileservers/write | Создает или обновляет файловые серверы. |
> | Действие | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Изменяет состояние питания контроллера для групп компонентов оборудования. |
> | Действие | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Выводит список групп компонентов оборудования. |
> | Действие | Microsoft.StorSimple/managers/devices/install/action | Устанавливает обновления на устройство. |
> | Действие | Microsoft.StorSimple/managers/devices/installUpdates/action | Устанавливает обновления на устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Архивирует сервер iSCSI. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Удаляет серверы iSCSI. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Удаляет диски. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Выводит список дисков или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Создает или обновляет диски. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/read | Выводит список серверов iSCSI или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/iscsiservers/write | Создает или обновляет серверы iSCSI. |
> | Действие | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Отменяет выполнение задания. |
> | Действие | Microsoft.StorSimple/managers/devices/jobs/read | Выводит список заданий или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Выводит список групп перехода на другой ресурс для существующего устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Выводит список целей отработки отказа для устройств. |
> | Действие | Microsoft.StorSimple/managers/devices/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Подтверждает успешный перенос и фиксирует его. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Извлекает состояние подтверждения переноса. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Извлекает состояние задания оценки переноса. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Извлекает состояние переноса. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Импортирует конфигурации источников для переноса. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Начинает перенос с использованием конфигураций источников. |
> | Действие | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Запускает задание для оценки длительности процесса переноса. |
> | Действие | Microsoft.StorSimple/managers/devices/networkSettings/read | Возвращает параметры сети или выводит их список. |
> | Действие | Microsoft.StorSimple/managers/devices/networkSettings/write | Создает или обновляет параметры сети. |
> | Действие | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Отображает открытый ключ шифрования для диспетчера устройств. |
> | Действие | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Публикует пакет поддержки устройства для устранения неполадок службой поддержки Майкрософт. |
> | Действие | Microsoft.StorSimple/managers/devices/read | Выводит список устройств или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Выполняет поиск обновлений в устройстве. |
> | Действие | Microsoft.StorSimple/managers/devices/securitySettings/read | Выводит список параметров безопасности. |
> | Действие | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Синхронизирует сертификат удаленного управления для устройства. |
> | Действие | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Обновляет параметры безопасности. |
> | Действие | Microsoft.StorSimple/managers/devices/securitySettings/write | Создает или обновляет параметры безопасности. |
> | Действие | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Отправляет тестовое сообщение оповещения настроенным получателям электронной почты. |
> | Действие | Microsoft.StorSimple/managers/devices/timeSettings/read | Возвращает параметры времени или выводит их список. |
> | Действие | Microsoft.StorSimple/managers/devices/timeSettings/write | Создает или обновляет параметры времени. |
> | Действие | Microsoft.StorSimple/managers/devices/updateSummary/read | Возвращает сводку обновлений или выводит их список. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Удаляет существующие контейнеры томов (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Выводит список ключей шифрования контейнеров томов. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Выводит список метрик. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Выводит список определений метрик. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/read | Выводит список контейнеров томов (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Переключает ключи шифрования контейнеров томов. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Удаляет существующие тома. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Выводит список метрик. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Выводит список определений метрик. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Выводит список томов. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Создает или обновляет тома. |
> | Действие | Microsoft.StorSimple/managers/devices/volumeContainers/write | Создает или обновляет контейнеры томов (только для серии 8000). |
> | Действие | Microsoft.StorSimple/managers/devices/write | Создает или обновляет устройства. |
> | Действие | Microsoft.StorSimple/managers/encryptionSettings/read | Возвращает параметры шифрования или выводит их список. |
> | Действие | Microsoft.StorSimple/Managers/extendedInformation/delete | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.StorSimple/Managers/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.StorSimple/Managers/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Действие | Microsoft.StorSimple/managers/getActivationKey/action | Возвращает ключ активации для диспетчера устройств. |
> | Действие | Microsoft.StorSimple/managers/getEncryptionKey/action | Возвращает ключ шифрования для диспетчера устройств. |
> | Действие | Microsoft.StorSimple/managers/listActivationKey/action | Возвращает ключ активации диспетчера устройств StorSimple. |
> | Действие | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Возвращает ключ шифрования для диспетчера устройств StorSimple. |
> | Действие | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Выводит список открытых ключей шифрования для диспетчера устройств StorSimple. |
> | Действие | Microsoft.StorSimple/managers/metrics/read | Выводит список метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/metricsDefinitions/read | Выводит список определений метрик или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Создает облачное устройство. |
> | Действие | Microsoft.StorSimple/managers/read | Выводит список диспетчеров устройств или возвращает их. |
> | Действие | Microsoft.StorSimple/Managers/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Действие | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Повторно создает ключ шифрования для диспетчеров устройств. |
> | Действие | Microsoft.StorSimple/managers/regenerateActivationKey/action | Повторно создает ключ активации для диспетчера устройств. |
> | Действие | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Удаляет данные учетной записи хранения. |
> | Действие | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | Выводит список ключей доступа для данных учетной записи хранения. |
> | Действие | Microsoft.StorSimple/managers/storageAccountCredentials/read | Выводит список данных учетной записи хранения или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/storageAccountCredentials/write | Создает или обновляет данные учетной записи хранения. |
> | Действие | Microsoft.StorSimple/managers/storageDomains/delete | Удаляет домены хранилища. |
> | Действие | Microsoft.StorSimple/managers/storageDomains/read | Выводит список доменов хранилища или возвращает их. |
> | Действие | Microsoft.StorSimple/managers/storageDomains/write | Создает или обновляет домены хранилища. |
> | Действие | Microsoft.StorSimple/managers/write | Создает или обновляет диспетчеры устройств. |
> | Действие | Microsoft.StorSimple/Managers/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.StreamAnalytics/locations/quotas/Read | Чтение квоты подписки Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/operations/Read | Чтение операций Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/Register/action | Регистрация подписки в поставщике ресурсов Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/Delete | Удаляет задание Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Удаление функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Чтение результатов операции функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Чтение функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Получение определения по умолчанию для функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Тестирование функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Запись функции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Удаляет входные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Чтение результатов операции для получения входных данных задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Считывает входные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Пример входных данных задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Тестирование входных данных задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Записывает входные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Чтение определений метрик. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Чтение результатов операции задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Удаляет выходные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Чтение результатов операции для получения выходных данных задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Считывает выходные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Тестирование выходных данных задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Записывает выходные данные задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Считывает параметр диагностики. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Записывает параметр диагностики. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Возвращает доступные журналы для заданий потоковой передачи. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Возвращает доступные метрики для заданий потоковой передачи. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/Read | Считывает задание Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/Start/action | Запускает задание Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Останавливает задание Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Удаляет преобразование задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Считывает преобразование задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Записывает преобразование задания Stream Analytics. |
> | Действие | Microsoft.StreamAnalytics/streamingjobs/Write | Записывает задание Stream Analytics. |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Subscription/CreateSubscription/action | Создание подписки Azure. |
> | Действие | Microsoft.Subscription/SubscriptionDefinitions/read | Получение определения подписки Azure в группе управления. |
> | Действие | Microsoft.Subscription/SubscriptionDefinitions/write | Создание определения подписки Azure. |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.Support/register/action | Выполняет регистрацию в поставщике ресурсов поддержки. |
> | Действие | Microsoft.Support/supportTickets/read | Возвращает подробные сведения о запросе в службу поддержки (включая состояние, серьезность, контактную информацию и сообщения) или возвращает список запросов в службу поддержки в подписках. |
> | Действие | Microsoft.Support/supportTickets/write | Создает или обновляет запрос в службу поддержки. Можно создавать запросы в службу поддержки по техническим проблемам, а также проблемам, связанным с выставлением счетов, квотами или управлением подписками. Вы можете изменить уровень серьезности, контактную информацию и сообщения для существующих запросов в службу поддержки. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Удаление политики доступа. |
> | Действие | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Получение свойств политики доступа. |
> | Действие | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Создание новой политики доступа для среды или обновление существующей политики. |
> | Действие | Microsoft.TimeSeriesInsights/environments/delete | Удаление среды. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Удаление источника события. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для источников событий. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/read | Получение свойств источника событий. |
> | Действие | Microsoft.TimeSeriesInsights/environments/eventsources/write | Создание нового источника событий для среды или обновление существующего. |
> | Действие | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Получение доступных метрик для сред. |
> | Действие | Microsoft.TimeSeriesInsights/environments/read | Получение свойств среды. |
> | Действие | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Удаление эталонного набора данных. |
> | Действие | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Получение свойств эталонного набора данных. |
> | Действие | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Создание нового эталонного набора данных для среды или обновление существующего. |
> | Действие | Microsoft.TimeSeriesInsights/environments/status/read | Получение состояния среды и связанных с ней операций, таких как получение входящего трафика. |
> | Действие | Microsoft.TimeSeriesInsights/environments/write | Создание новой среды или обновление существующей. |
> | Действие | Microsoft.TimeSeriesInsights/register/action | Регистрация подписки для поставщика ресурсов службы "Аналитика временных рядов" с возможностью создания сред данной службы. |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.VisualStudio/Account/Delete | Удаление учетной записи. |
> | Действие | Microsoft.VisualStudio/Account/Read | Чтение учетной записи. |
> | Действие | Microsoft.VisualStudio/Account/Write | Настройка учетной записи. |
> | Действие | Microsoft.VisualStudio/Extension/Delete | Удаление расширения. |
> | Действие | Microsoft.VisualStudio/Extension/Read | Чтение расширения. |
> | Действие | Microsoft.VisualStudio/Extension/Write | Установка расширения. |
> | Действие | Microsoft.VisualStudio/Project/Delete | Удаление проекта. |
> | Действие | Microsoft.VisualStudio/Project/Read | Чтение проекта. |
> | Действие | Microsoft.VisualStudio/Project/Write | Настройка проекта. |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | microsoft.web/apimanagementaccounts/apiacls/read | Получение списков ACL для учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Удаление списков ACL для API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/apiacls/read | Получение списков ACL для API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/apiacls/write | Обновление списков ACL для API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Получение списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Подтверждение кода согласия на подключение для API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Удаление списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Получение списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Обновление списков ACL для подключения API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/delete | Удаление подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Получение ссылок на согласие для подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Получение ключей подключения для подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Получение списка секретов для подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/read | Получение подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/connections/write | Обновление подключений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/delete | Удаление API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Удаление локализованных определений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Получение локализованных определений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Обновление локализованных определений API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/read | Получение API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/apis/write | Обновление API-интерфейсов учетных записей управления API. |
> | Действие | microsoft.web/apimanagementaccounts/connectionacls/read | Получение списков ACL для подключения учетных записей управления API. |
> | Действие | microsoft.web/availablestacks/read | Возвращает доступные стеки. |
> | Действие | microsoft.web/billingmeters/read | Получение списка счетчиков цен. |
> | Действие | Microsoft.Web/certificates/Delete | Удаляет существующий сертификат. |
> | Действие | Microsoft.Web/certificates/Read | Возвращает список сертификатов. |
> | Действие | Microsoft.Web/certificates/Write | Добавляет новый сертификат или обновляет существующий. |
> | Действие | microsoft.web/checknameavailability/read | Проверяет, доступно ли имя ресурса. |
> | Действие | microsoft.web/classicmobileservices/read | Возвращает классические мобильные службы. |
> | Действие | Microsoft.Web/connectionGateways/Delete | Удаляет шлюз подключения. |
> | Действие | Microsoft.Web/connectionGateways/Join/Action | Присоединяет шлюз подключения. |
> | Действие | microsoft.web/connectiongateways/liststatus/action | Получение списка состояний шлюзов подключения. |
> | Действие | Microsoft.Web/connectionGateways/ListStatus/Action | Получение состояния шлюза подключения. |
> | Действие | Microsoft.Web/connectionGateways/Move/Action | Перемещение шлюза подключения. |
> | Действие | Microsoft.Web/connectionGateways/Read | Возвращает список шлюзов подключений. |
> | Действие | Microsoft.Web/connectionGateways/Write | Создает или обновляет шлюз для подключения. |
> | Действие | microsoft.web/connections/confirmconsentcode/action | Подтверждает код согласия на подключение. |
> | Действие | Microsoft.Web/connections/Delete | Удаляет подключение. |
> | Действие | Microsoft.Web/connections/Join/Action | Присоединяет подключение. |
> | Действие | microsoft.web/connections/listconsentlinks/action | Выводит список ссылок на согласие для подключений. |
> | Действие | Microsoft.Web/connections/Move/Action | Перемещение подключения. |
> | Действие | Microsoft.Web/connections/Read | Возвращает список подключений. |
> | Действие | Microsoft.Web/connections/Write | Создает или обновляет подключение. |
> | Действие | Microsoft.Web/customApis/Delete | Удаление пользовательского API. |
> | Действие | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Извлечение определения API из формата WSDL. |
> | Действие | Microsoft.Web/customApis/Join/Action | Присоединение пользовательского API. |
> | Действие | Microsoft.Web/customApis/listWsdlInterfaces/Action | Получение списка интерфейсов языка WSDL для пользовательского API. |
> | Действие | Microsoft.Web/customApis/Move/Action | Перемещение пользовательского API. |
> | Действие | Microsoft.Web/customApis/Read | Получение списка пользовательских API. |
> | Действие | Microsoft.Web/customApis/Write | Создание или обновление пользовательского API. |
> | Действие | microsoft.web/deploymentlocations/read | Возвращает расположения развертываний. |
> | Действие | Microsoft.Web/geoRegions/Read | Возвращает список географических регионов. |
> | Действие | microsoft.web/hostingenvironments/capacities/read | Возвращает емкости сред внешнего размещения. |
> | Действие | Microsoft.Web/hostingEnvironments/Delete | Удаляет среду службы приложений. |
> | Действие | microsoft.web/hostingenvironments/diagnostics/read | Возвращает данные диагностики сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Получение конечных точек сети всех входящих зависимостей. |
> | Действие | microsoft.web/hostingenvironments/metricdefinitions/read | Возвращает определения метрик сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Возвращает определения метрик пулов множественных ролей в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/multirolepools/metrics/read | Возвращает метрики пулов множественных ролей в средах внешнего размещения. |
> | Действие | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Получение доступных метрик для множественных ролей среды службы приложений. |
> | Действие | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Возвращает свойства интерфейсного пула в среде службы приложений. |
> | Действие | microsoft.web/hostingenvironments/multirolepools/skus/read | Возвращает номера SKU пулов множественных ролей в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/multirolepools/usages/read | Возвращает данные об использовании пулов множественных ролей в средах внешнего размещения. |
> | Действие | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Создает новый интерфейсный пул в среде службы приложений или обновляет существующий. |
> | Действие | microsoft.web/hostingenvironments/operations/read | Возвращает операции сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Получение конечных точек сети всех исходящих зависимостей. |
> | Действие | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Web/hostingEnvironments/Read | Возвращает свойства среды службы приложений. |
> | Действие | Microsoft.Web/hostingEnvironments/reboot/Action | Перезагружает все компьютеры в среде службы приложений. |
> | Действие | microsoft.web/hostingenvironments/resume/action | Возобновляет работу сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/serverfarms/read | Возвращает планы службы приложений в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/sites/read | Возвращает веб-приложения в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/suspend/action | Приостанавливает работу сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/usages/read | Возвращает данные об использовании сред внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Возвращает определения метрик рабочих пулов в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/workerpools/metrics/read | Возвращает метрики рабочих пулов в средах внешнего размещения. |
> | Действие | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Получение доступных метрик для пула рабочих ролей среды службы приложений. |
> | Действие | Microsoft.Web/hostingEnvironments/workerPools/Read | Возвращает свойства рабочего пула в среде службы приложений. |
> | Действие | microsoft.web/hostingenvironments/workerpools/skus/read | Возвращает номера SKU рабочих пулов в средах внешнего размещения. |
> | Действие | microsoft.web/hostingenvironments/workerpools/usages/read | Возвращает данные об использовании рабочих пулов в средах внешнего размещения. |
> | Действие | Microsoft.Web/hostingEnvironments/workerPools/Write | Создает новый рабочий пул в среде службы приложений или обновляет существующий. |
> | Действие | Microsoft.Web/hostingEnvironments/Write | Создает новую среду службы приложений или обновляет существующую. |
> | Действие | microsoft.web/ishostingenvironmentnameavailable/read | Проверяет, доступно ли имя среды внешнего размещения. |
> | Действие | microsoft.web/ishostnameavailable/read | Проверяет, доступно ли имя узла. |
> | Действие | microsoft.web/isusernameavailable/read | Проверяет, доступно ли имя пользователя. |
> | Действие | Microsoft.Web/listSitesAssignedToHostName/Read | Возвращает имена сайтов, назначенные имени узла. |
> | Действие | microsoft.web/locations/apioperations/read | Возвращает операции API расположений. |
> | Действие | microsoft.web/locations/connectiongatewayinstallations/read | Возвращает данные об установленных шлюзах подключений в расположениях. |
> | Действие | microsoft.web/locations/extractapidefinitionfromwsdl/action | Извлечение определения API для расположений из синтаксиса WSDL. |
> | Действие | microsoft.web/locations/listwsdlinterfaces/action | Получение списка интерфейсов языка WSDL для расположений. |
> | Действие | microsoft.web/locations/managedapis/apioperations/read | Получение операций с управляемыми API для расположений. |
> | Действие | Microsoft.Web/locations/managedapis/Join/Action | Присоединение управляемого API. |
> | Действие | microsoft.web/locations/managedapis/read | Возвращает расположения управляемых интерфейсов API. |
> | Действие | microsoft.web/operations/read | Возвращает операции. |
> | Действие | microsoft.web/publishingusers/read | Возвращает публикующих пользователей. |
> | Действие | microsoft.web/publishingusers/write | Обновляет публикующих пользователей. |
> | Действие | Microsoft.Web/recommendations/Read | Возвращает список рекомендаций для подписок. |
> | Действие | microsoft.web/register/action | Регистрирует поставщик ресурсов Microsoft.Web для подписки. |
> | Действие | microsoft.web/resourcehealthmetadata/read | Получение метаданных работоспособности ресурса. |
> | Действие | microsoft.web/serverfarms/capabilities/read | Возвращает возможности планов службы приложений. |
> | Действие | Microsoft.Web/serverfarms/Delete | Удаление существующего плана службы приложений |
> | Действие | microsoft.web/serverfarms/firstpartyapps/settings/delete | Удаляет параметры основных приложений в планах службы приложений. |
> | Действие | microsoft.web/serverfarms/firstpartyapps/settings/read | Возвращает параметры основных приложений в планах службы приложений. |
> | Действие | microsoft.web/serverfarms/firstpartyapps/settings/write | Обновляет параметры основных приложений в планах службы приложений. |
> | Действие | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действие | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Получение ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действие | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Возвращает веб-приложения ретрансляторов в пространствах имен гибридных подключений для плана службы приложений. |
> | Действие | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Возвращает ограничения плана гибридных подключений для плана службы приложений. |
> | Действие | microsoft.web/serverfarms/hybridconnectionrelays/read | Возвращает ретрансляторы гибридных подключений для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/metricdefinitions/read | Возвращает определения метрик планов службы приложений. |
> | Действие | microsoft.web/serverfarms/metrics/read | Возвращает метрики планов службы приложений. |
> | Действие | microsoft.web/serverfarms/operationresults/read | Возвращает результаты операций планов службы приложений. |
> | Действие | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Получение доступных метрик для плана службы приложений. |
> | Действие | Microsoft.Web/serverfarms/Read | Возвращает свойства плана службы приложений. |
> | Действие | Microsoft.Web/serverfarms/restartSites/Action | Перезапустите все веб-приложения в плане службы приложений. |
> | Действие | microsoft.web/serverfarms/sites/read | Возвращает веб-приложения в планах службы приложений. |
> | Действие | microsoft.web/serverfarms/skus/read | Возвращает номера SKU в планах службы приложений. |
> | Действие | microsoft.web/serverfarms/usages/read | Возвращает данные об использовании для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальной сети для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/virtualnetworkconnections/read | Возвращает подключения виртуальной сети для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Удаляет маршруты подключений виртуальной сети для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Возвращает маршруты подключений виртуальной сети для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Обновляет маршруты подключений виртуальной сети для планов службы приложений. |
> | Действие | microsoft.web/serverfarms/workers/reboot/action | Перезапускает рабочие роли планов службы приложений. |
> | Действие | Microsoft.Web/serverfarms/Write | Создает новый план службы приложений или обновляет существующий. |
> | Действие | microsoft.web/sites/analyzecustomhostname/read | Анализирует пользовательское имя узла. |
> | Действие | Microsoft.Web/sites/applySlotConfig/Action | Применяет конфигурацию слота веб-приложения из целевого слота к текущему веб-приложению. |
> | Действие | Microsoft.Web/sites/backup/Action | Создает резервную копию веб-приложения. |
> | Действие | microsoft.web/sites/backup/read | Возвращает резервную копию веб-приложений. |
> | Действие | microsoft.web/sites/backup/write | Обновляет резервную копию веб-приложений. |
> | Действие | microsoft.web/sites/backups/delete | Удаление резервных копий веб-приложений. |
> | Действие | microsoft.web/sites/backups/list/action | Выводит список резервных копий веб-приложений. |
> | Действие | Microsoft.Web/sites/backups/Read | Возвращает свойства резервной копии веб-приложения. |
> | Действие | microsoft.web/sites/backups/restore/action | Восстанавливает резервные копии веб-приложений. |
> | Действие | microsoft.web/sites/config/delete | Удаляет конфигурацию веб-приложений. |
> | Действие | Microsoft.Web/sites/config/list/Action | Отображает влияющие на безопасность параметры веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения. |
> | Действие | Microsoft.Web/sites/config/Read | Возвращает параметры конфигурации веб-приложения. |
> | Действие | Microsoft.Web/sites/config/Write | Обновляет параметры конфигурации веб-приложения. |
> | Действие | microsoft.web/sites/continuouswebjobs/delete | Удаляет непрерывные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/continuouswebjobs/read | Возвращает непрерывные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/continuouswebjobs/start/action | Запускает непрерывные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/continuouswebjobs/stop/action | Останавливает непрерывные веб-задания веб-приложений. |
> | Действие | Microsoft.Web/sites/Delete | Удаление существующего веб-приложения |
> | Действие | microsoft.web/sites/deployments/delete | Удаляет развертывания веб-приложений. |
> | Действие | microsoft.web/sites/deployments/log/read | Возвращает журнал развертываний веб-приложений. |
> | Действие | microsoft.web/sites/deployments/read | Возвращает развертывания веб-приложений. |
> | Действие | microsoft.web/sites/deployments/write | Обновляет развертывания веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/analyses/execute/Action | Запуск анализа данных диагностики веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/analyses/read | Получение анализа данных диагностики веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/aspnetcore/read | Получение данных диагностики веб-приложений для приложения ASP.NET Core. |
> | Действие | microsoft.web/sites/diagnostics/autoheal/read | Получение данных диагностики о функции Autoheal для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/deployment/read | Получение данных диагностики о развертывании для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/deployments/read | Получение данных диагностики о развертываниях для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/detectors/execute/Action | Запуск средства обнаружения диагностики веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/detectors/read | Получение данных средства обнаружения диагностики веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Получение данных диагностики веб-приложений о неудачных запросах для каждого URI. |
> | Действие | microsoft.web/sites/diagnostics/frebanalysis/read | Возвращает данные диагностики об анализе FREB для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/loganalyzer/read | Получение данных диагностики веб-приложений об анализаторе журналов. |
> | Действие | microsoft.web/sites/diagnostics/read | Получение категорий данных диагностики веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/runtimeavailability/read | Возвращает данные диагностики о доступности среды выполнения для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/servicehealth/read | Возвращает данные диагностики о работоспособности служб для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Получение данных диагностики веб-приложений об анализе ЦП для сайта. |
> | Действие | microsoft.web/sites/diagnostics/sitecrashes/read | Получение данных диагностики веб-приложений о сбоях сайта. |
> | Действие | microsoft.web/sites/diagnostics/sitelatency/read | Получение данных диагностики веб-приложений о задержках сайта. |
> | Действие | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Получение данных диагностики веб-приложений об анализе памяти для сайта. |
> | Действие | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Получение данных диагностики веб-приложений об обновлении параметра перезапуска сайта. |
> | Действие | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Получение данных диагностики веб-приложений об инициированном пользователем перезапуске сайта. |
> | Действие | microsoft.web/sites/diagnostics/siteswap/read | Получение данных диагностики веб-приложений о переключении сайта. |
> | Действие | microsoft.web/sites/diagnostics/threadcount/read | Получение данных диагностики веб-приложений о количестве потоков. |
> | Действие | microsoft.web/sites/diagnostics/workeravailability/read | Возвращает данные диагностики о доступности рабочих процессов для веб-приложений. |
> | Действие | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Возвращает данные диагностики перезапуска рабочих процессов для веб-приложений. |
> | Действие | microsoft.web/sites/domainownershipidentifiers/read | Возвращает идентификаторы владения доменом веб-приложений. |
> | Действие | microsoft.web/sites/domainownershipidentifiers/write | Обновляет идентификаторы владения доменом веб-приложений. |
> | Действие | microsoft.web/sites/functions/action | Выполняет функции веб-приложений. |
> | Действие | microsoft.web/sites/functions/delete | Удаляет функции веб-приложений. |
> | Действие | microsoft.web/sites/functions/listsecrets/action | Выводит список секретов для функций веб-приложений. |
> | Действие | microsoft.web/sites/functions/masterkey/read | Получение главного ключа функций веб-приложений. |
> | Действие | microsoft.web/sites/functions/read | Возвращает функции веб-приложений. |
> | Действие | microsoft.web/sites/functions/token/read | Получение токена функций веб-приложений. |
> | Действие | microsoft.web/sites/functions/write | Обновляет функции веб-приложений. |
> | Действие | microsoft.web/sites/hostnamebindings/delete | Удаляет привязки имен узлов для веб-приложений. |
> | Действие | microsoft.web/sites/hostnamebindings/read | Возвращает привязки имен узлов для веб-приложений. |
> | Действие | microsoft.web/sites/hostnamebindings/write | Обновляет привязки имен узлов для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnection/delete | Удаляет гибридное подключение для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnection/read | Возвращает гибридное подключение для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnection/write | Обновляет гибридное подключение для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Получение списка ключей ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Получение ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Обновление ретрансляторов в пространствах имен гибридных подключений для веб-приложений. |
> | Действие | microsoft.web/sites/hybridconnectionrelays/read | Возвращает ретрансляторы гибридных подключений для веб-приложений. |
> | Действие | microsoft.web/sites/instances/deployments/delete | Удаление развертываний экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/deployments/read | Возвращает развертывания экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/extensions/log/read | Получение журнала расширений экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/extensions/read | Получение расширений экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/processes/delete | Удаляет процессы экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/processes/read | Возвращает процессы экземпляров веб-приложений. |
> | Действие | microsoft.web/sites/instances/read | Возвращает экземпляры веб-приложений. |
> | Действие | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Отображает состояние триггера функции синхронизации для веб-приложений. |
> | Действие | microsoft.web/sites/metricdefinitions/read | Возвращает определения метрик веб-приложений. |
> | Действие | microsoft.web/sites/metrics/read | Возвращает метрики веб-приложений. |
> | Действие | microsoft.web/sites/metricsdefinitions/read | Получение определений метрик веб-приложений. |
> | Действие | microsoft.web/sites/migratemysql/action | Перенос веб-приложений MySQL. |
> | Действие | microsoft.web/sites/migratemysql/read | Получение сведений о переносе веб-приложений MySQL. |
> | Действие | microsoft.web/sites/networktrace/action | Трассирует сеть для веб-приложений. |
> | Действие | microsoft.web/sites/newpassword/action | Задает новый пароль для веб-приложений. |
> | Действие | microsoft.web/sites/operationresults/read | Возвращает результаты операций веб-приложений. |
> | Действие | microsoft.web/sites/operations/read | Получение операций веб-приложений. |
> | Действие | microsoft.web/sites/perfcounters/read | Возвращает счетчики производительности веб-приложений. |
> | Действие | microsoft.web/sites/premieraddons/delete | Удаляет надстройки Premier для веб-приложений. |
> | Действие | microsoft.web/sites/premieraddons/read | Возвращает надстройки Premier для веб-приложений. |
> | Действие | microsoft.web/sites/premieraddons/write | Обновляет надстройки Premier для веб-приложений. |
> | Действие | microsoft.web/sites/processes/read | Получение процессов веб-приложений. |
> | Действие | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Получение доступных метрик для веб-приложений. |
> | Действие | microsoft.web/sites/publiccertificates/delete | Удаление открытых сертификатов веб-приложений. |
> | Действие | microsoft.web/sites/publiccertificates/read | Получение открытых сертификатов веб-приложений. |
> | Действие | microsoft.web/sites/publiccertificates/write | Обновление открытых сертификатов веб-приложений. |
> | Действие | Microsoft.Web/sites/publish/Action | Публикует веб-приложение. |
> | Действие | Microsoft.Web/sites/publishxml/Action | Возвращает XML-файл профиля публикации для веб-приложения. |
> | Действие | microsoft.web/sites/publishxml/read | Возвращает XML-файл профиля публикации для веб-приложений. |
> | Действие | Microsoft.Web/sites/Read | Возвращает свойства веб-приложения. |
> | Действие | microsoft.web/sites/recommendationhistory/read | Возвращает журнал рекомендаций для веб-приложения. |
> | Действие | microsoft.web/sites/recommendations/disable/action | Отключает рекомендации для веб-приложений. |
> | Действие | Microsoft.Web/sites/recommendations/Read | Возвращает список рекомендаций для веб-приложения. |
> | Действие | microsoft.web/sites/recover/action | Восстановление веб-приложений. |
> | Действие | Microsoft.Web/sites/resetSlotConfig/Action | Сбрасывает конфигурацию веб-приложения. |
> | Действие | microsoft.web/sites/resourcehealthmetadata/read | Получение метаданных работоспособности ресурсов веб-приложений. |
> | Действие | Microsoft.Web/sites/restart/Action | Перезапускает веб-приложение. |
> | Действие | microsoft.web/sites/restore/read | Возвращает данные о восстановлении веб-приложений. |
> | Действие | microsoft.web/sites/restore/write | Восстановление веб-приложений. |
> | Действие | microsoft.web/sites/restoresnapshot/action | Восстановление моментальных снимков веб-приложений. |
> | Действие | microsoft.web/sites/siteextensions/delete | Удаление расширений сайта веб-приложений. |
> | Действие | microsoft.web/sites/siteextensions/read | Получение расширений сайта веб-приложений. |
> | Действие | microsoft.web/sites/siteextensions/write | Обновление расширений сайта веб-приложений. |
> | Действие | microsoft.web/sites/slots/analyzecustomhostname/read | Возвращает результаты анализа пользовательского имени узла для слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/applySlotConfig/Action | Применяет конфигурацию слота веб-приложения из целевого слота к текущему слоту. |
> | Действие | Microsoft.Web/sites/slots/backup/Action | Создает резервную копию слота веб-приложения. |
> | Действие | microsoft.web/sites/slots/backup/read | Получение резервной копии слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/backup/write | Обновляет резервную копию слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/backups/delete | Удаление резервной копии слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/backups/list/action | Выводит список резервных копий слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/backups/Read | Возвращает свойства резервной копии слотов веб-приложения. |
> | Действие | microsoft.web/sites/slots/backups/restore/action | Восстанавливает резервные копии слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/config/delete | Удаляет конфигурацию слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/config/list/Action | Отображает влияющие на безопасность параметры слота веб-приложения, такие как учетные данные для публикации, параметры приложения и строки подключения. |
> | Действие | Microsoft.Web/sites/slots/config/Read | Возвращает параметры конфигурации слота веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/config/Write | Обновляет параметры конфигурации слота веб-приложения. |
> | Действие | microsoft.web/sites/slots/continuouswebjobs/delete | Удаляет непрерывные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/continuouswebjobs/read | Возвращает непрерывные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/continuouswebjobs/start/action | Запускает непрерывные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/continuouswebjobs/stop/action | Останавливает непрерывные веб-задания слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/Delete | Удаляет существующий слот веб-приложения. |
> | Действие | microsoft.web/sites/slots/deployments/delete | Удаляет развертывания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/deployments/log/read | Возвращает журнал развертываний слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/deployments/read | Возвращает развертывания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/deployments/write | Обновляет развертывания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Запуск анализа данных диагностики слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/analyses/read | Получение анализа данных диагностики слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Получение данных диагностики слотов для веб-приложений для приложения ASP.NET Core. |
> | Действие | microsoft.web/sites/slots/diagnostics/autoheal/read | Получение данных диагностики слотов о функции Autoheal для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/deployment/read | Получение данных диагностики слотов о развертывании для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/deployments/read | Получение данных диагностики слотов о развертываниях для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Запуск средства обнаружения данных диагностики слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/detectors/read | Получение сведений средства обнаружения данных диагностики слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Возвращение данных диагностики об анализе FREB для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Получение данных диагностики слотов для веб-приложений об анализаторе журналов. |
> | Действие | microsoft.web/sites/slots/diagnostics/read | Получение данных диагностики слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Получение данных диагностики слотов о доступности среды выполнения для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/servicehealth/read | Получение данных диагностики слотов о работоспособности служб для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Получение данных диагностики слотов об анализе ЦП сайта для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Получение данных диагностики слотов о сбоях сайта для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/sitelatency/read | Получение данных диагностики слотов о задержке сайта для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Получение данных диагностики слотов об анализе памяти сайта для веб-приложений. |
> | Действие | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Получение данных диагностики слотов для веб-приложений об обновлении параметра перезапуска сайта. |
> | Действие | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Получение данных диагностики слотов для веб-приложений об инициированном пользователем перезапуске сайта. |
> | Действие | microsoft.web/sites/slots/diagnostics/siteswap/read | Получение данных диагностики слотов для веб-приложений о переключении сайта. |
> | Действие | microsoft.web/sites/slots/diagnostics/threadcount/read | Получение данных диагностики слотов для веб-приложений о количестве потоков. |
> | Действие | microsoft.web/sites/slots/diagnostics/workeravailability/read | Получение данных диагностики слотов для веб-приложений о доступности рабочих процессов. |
> | Действие | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Получение данных диагностики слотов для веб-приложений о перезапуске рабочих процессов. |
> | Действие | microsoft.web/sites/slots/domainownershipidentifiers/read | Получение идентификаторов владения доменом слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/hostnamebindings/delete | Удаляет привязки имен узлов для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hostnamebindings/read | Возвращает привязки имен узлов для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hostnamebindings/write | Обновляет привязки имен узлов для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnection/delete | Удаляет гибридное подключение для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnection/read | Возвращает гибридное подключение для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnection/write | Обновляет гибридное подключение для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Удаление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Обновление ретрансляторов в пространствах имен гибридных подключений слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/hybridconnectionrelays/read | Получение ретрансляторов гибридных подключений слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/instances/deployments/read | Возвращает развертывания экземпляры слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/instances/processes/delete | Удаление процессов экземпляров слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/instances/processes/read | Возвращает процессы экземпляры слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/instances/read | Возвращает экземпляры слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/metricdefinitions/read | Возвращает определения метрик слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/metrics/read | Возвращает метрики слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/migratemysql/read | Получение сведений о переносе слотов для веб-приложений MySQL. |
> | Действие | microsoft.web/sites/slots/networktrace/action | Трассировка сети слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/newpassword/action | Задает новый пароль для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/operationresults/read | Возвращает результаты операций слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/operations/read | Получение операций слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/perfcounters/read | Получение счетчиков производительности слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/phplogging/read | Возвращает сведения о ведении журнала PHP для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/premieraddons/delete | Удаляет надстройки Premier для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/premieraddons/read | Возвращает надстройки Premier для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/premieraddons/write | Обновляет надстройки Premier для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Возвращает параметр диагностики для ресурса. |
> | Действие | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Создает или обновляет параметр диагностики для ресурса. |
> | Действие | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Получение доступных метрик слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/publiccertificates/read | Получение открытых сертификатов слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/publiccertificates/write | Создание или обновление открытых сертификатов слотов для веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/publish/Action | Публикует слот веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/publishxml/Action | Возвращает XML-файл профиля публикации для слота веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/Read | Возвращает свойства слота развертывания веб-приложения. |
> | Действие | microsoft.web/sites/slots/recover/action | Восстановление слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/resetSlotConfig/Action | Сбрасывает конфигурацию слота веб-приложения. |
> | Действие | microsoft.web/sites/slots/resourcehealthmetadata/read | Получение метаданных работоспособности ресурса слотов для веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/restart/Action | Перезапускает слот веб-приложения. |
> | Действие | microsoft.web/sites/slots/restore/read | Возвращает данные о восстановлении слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/restore/write | Восстановление слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/restoresnapshot/action | Восстановление моментальных снимков слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/siteextensions/delete | Удаление расширений сайта слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/siteextensions/read | Получение расширений сайта слотов для веб-приложений. |
> | Действие | microsoft.web/sites/slots/siteextensions/write | Обновление расширений сайта слотов для веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/slotsdiffs/Action | Возвращает различия конфигураций веб-приложения и слотов. |
> | Действие | Microsoft.Web/sites/slots/slotsswap/Action | Переключает слоты развертывания веб-приложения. |
> | Действие | microsoft.web/sites/slots/snapshots/read | Получение моментальных снимков слотов для веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/sourcecontrols/Delete | Удаляет параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/sourcecontrols/Read | Возвращает параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/sourcecontrols/Write | Обновляет параметры конфигурации системы управления версиями для слота веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/start/Action | Запускает слот веб-приложения. |
> | Действие | Microsoft.Web/sites/slots/stop/Action | Останавливает слот веб-приложения. |
> | Действие | microsoft.web/sites/slots/sync/action | Синхронизирует слоты веб-приложений. |
> | Действие | microsoft.web/sites/slots/triggeredwebjobs/delete | Удаляет активированные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/triggeredwebjobs/read | Возвращает активированные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/triggeredwebjobs/run/action | Выполняет активированные веб-задания слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/usages/read | Возвращает данные об использовании слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/virtualnetworkconnections/delete | Удаляет подключения виртуальных сетей для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальных сетей для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/virtualnetworkconnections/read | Возвращает подключения виртуальных сетей для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/virtualnetworkconnections/write | Обновляет подключения виртуальных сетей для слотов веб-приложений. |
> | Действие | microsoft.web/sites/slots/webjobs/read | Возвращает веб-задания слотов веб-приложений. |
> | Действие | Microsoft.Web/sites/slots/Write | Создает новый слот веб-приложения или обновляет существующий. |
> | Действие | Microsoft.Web/sites/slotsdiffs/Action | Возвращает различия конфигураций веб-приложения и слотов. |
> | Действие | Microsoft.Web/sites/slotsswap/Action | Переключает слоты развертывания веб-приложения. |
> | Действие | microsoft.web/sites/snapshots/read | Возвращает моментальные снимки веб-приложений. |
> | Действие | Microsoft.Web/sites/sourcecontrols/Delete | Удаляет параметры конфигурации системы управления версиями веб-приложения. |
> | Действие | Microsoft.Web/sites/sourcecontrols/Read | Возвращает параметры конфигурации системы управления версиями веб-приложения. |
> | Действие | Microsoft.Web/sites/sourcecontrols/Write | Обновляет параметры конфигурации системы управления версиями веб-приложения. |
> | Действие | Microsoft.Web/sites/start/Action | Запускает веб-приложение. |
> | Действие | Microsoft.Web/sites/stop/Action | Останавливает веб-приложение. |
> | Действие | microsoft.web/sites/sync/action | Синхронизирует веб-приложения. |
> | Действие | microsoft.web/sites/syncfunctiontriggers/action | Активация функции синхронизации для веб-приложений. |
> | Действие | microsoft.web/sites/triggeredwebjobs/delete | Удаляет активированные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/triggeredwebjobs/history/read | Получение истории активированных веб-заданий для веб-приложений. |
> | Действие | microsoft.web/sites/triggeredwebjobs/read | Возвращает активированные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/triggeredwebjobs/run/action | Выполняет активированные веб-задания веб-приложений. |
> | Действие | microsoft.web/sites/usages/read | Возвращает данные об использовании веб-приложений. |
> | Действие | microsoft.web/sites/virtualnetworkconnections/delete | Удаляет подключения виртуальных сетей веб-приложений. |
> | Действие | microsoft.web/sites/virtualnetworkconnections/gateways/read | Возвращает шлюзы подключений виртуальных сетей веб-приложений. |
> | Действие | microsoft.web/sites/virtualnetworkconnections/gateways/write | Обновляет шлюзы подключений виртуальных сетей веб-приложений. |
> | Действие | microsoft.web/sites/virtualnetworkconnections/read | Возвращает подключения виртуальных сетей веб-приложений. |
> | Действие | microsoft.web/sites/virtualnetworkconnections/write | Обновляет подключения виртуальных сетей веб-приложений. |
> | Действие | microsoft.web/sites/webjobs/read | Возвращает веб-задания веб-приложений. |
> | Действие | Microsoft.Web/sites/Write | Создает новое веб-приложение или обновляет существующее. |
> | Действие | microsoft.web/skus/read | Возвращает номера SKU. |
> | Действие | microsoft.web/sourcecontrols/read | Возвращает систему управления версиями. |
> | Действие | microsoft.web/sourcecontrols/write | Обновляет системы управления версиями. |
> | Действие | microsoft.web/unregister/action | Отменяет регистрацию поставщика ресурсов Microsoft.Web для подписки. |
> | Действие | microsoft.web/validate/action | Выполняет проверку. |
> | Действие | microsoft.web/verifyhostingenvironmentvnet/action | Проверка виртуальной сети среды размещения. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Тип действия | Операция | ОПИСАНИЕ |
> | --- | --- | --- |
> | Действие | Microsoft.WorkloadMonitor/components/read | Чтение ресурсов операций. |
> | Действие | Microsoft.WorkloadMonitor/healthInstances/read | Чтение ресурсов операций. |
> | Действие | Microsoft.WorkloadMonitor/Operations/read | Чтение ресурсов операций. |
> | Действие | Microsoft.WorkloadMonitor/workloads/delete | Удаление ресурса рабочей нагрузки. |
> | Действие | Microsoft.WorkloadMonitor/workloads/read | Чтение ресурса рабочей нагрузки. |
> | Действие | Microsoft.WorkloadMonitor/workloads/write | Запись ресурса рабочей нагрузки. |

## <a name="next-steps"></a>Дополнительная информация

- [Пользовательские роли](custom-roles.md)
- [Встроенные роли](built-in-roles.md)
