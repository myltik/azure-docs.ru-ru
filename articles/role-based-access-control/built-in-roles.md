---
title: Встроенные роли для управления доступом на основе ролей (RBAC) в Azure | Документация Майкрософт
description: В этой статье описаны встроенные роли для управления доступом на основе ролей (RBAC) в Azure. Список actions и notActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 91f721f5508191c7530e57b6dd96cad3301542a7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203511"
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Встроенные роли управления доступом на основе ролей в Azure
[Управление доступом на основе ролей (RBAC)](overview.md) имеет несколько определений встроенной роли, которую можно назначить для пользователей, групп и субъектов-служб. Назначение ролей является способом управления доступом к ресурсам в Azure. Изменить встроенные роли не возможно, однако вы можете создать собственные [настраиваемые роли](custom-roles.md) в соответствии с потребностями вашей организации.

Встроенные роли всегда развиваются. Чтобы получить последние определения ролей, используйте команду [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) или [az role definition list](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Описание встроенных ролей
В таблице ниже содержатся краткие описания встроенных ролей. Щелкните имя роли, чтобы просмотреть список `actions` и `notActions` для каждой роли.


| Встроенные роли | ОПИСАНИЕ |
| --- | --- |
| [Владелец](#owner) | Позволяет управлять всем, в том числе доступом к ресурсам. |
| [Участник](#contributor) | Позволяет управлять всем, кроме доступа к ресурсам. |
| [Читатель](#reader) | Может просматривать все элементы, но не вносить изменения. |
| [AcrImageSigner](#acrimagesigner) | Средство подписывания образов ACR. |
| [AcrQuarantineReader](#acrquarantinereader) | Модуль чтения данных карантина ACR. |
| [AcrQuarantineWriter](#acrquarantinewriter) | Модуль записи данных карантина ACR. |
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
| [Средство очистки данных](#data-purger) | Может выполнить очистку данных аналитики. |
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
| [Модуль чтения и доступ к данным](#reader-and-data-access) | Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения. |
| [Участник кэша Redis](#redis-cache-contributor) | Позволяет управлять кэшем Redis, но не доступом к нему. |
| [Участник коллекции заданий планировщика](#scheduler-job-collections-contributor) | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. |
| [Участник службы поиска](#search-service-contributor) | Позволяет управлять службами поиска, но не доступом к ним. |
| [Администратор безопасности](#security-admin) | Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации |
| [Диспетчер безопасности (устаревший)](#security-manager-legacy) | Это устаревшая роль. Используйте роль администратора безопасности |
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


## <a name="owner"></a>Владелец.
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять всем, в том числе доступом к ресурсам. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |

## <a name="contributor"></a>участник;
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять всем, кроме доступа к ресурсам. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Действия** |  |
> | * | Создание ресурсов всех типов и управление ими |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Не может удалять роли и назначения ролей. |
> | Microsoft.Authorization/*/Write | Не может создавать роли и назначения ролей. |
> | Microsoft.Authorization/elevateAccess/Action | Предоставляет вызывающему доступ с правами администратора для области действия клиента. |

## <a name="reader"></a>Читатель
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать все элементы, но не вносить изменения. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Средство подписывания образов ACR. |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Модуль чтения данных карантина ACR. |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Модуль записи данных карантина ACR. |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Действия** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>Участник службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять службой и интерфейсами API. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/* | Создание службы управления API и управление ею |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="api-management-service-operator-role"></a>Роль оператора службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять службой, но не интерфейсами API. |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/backup/action | Архивирует службу управления API в указанный контейнер в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/delete | Удаляет экземпляр службы управления API. |
> | Microsoft.ApiManagement/service/managedeployments/action | Позволяет изменить номер SKU или единицы, а также добавить или удалить региональные развертывания службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.ApiManagement/service/restore/action | Восстановление службы управления API из указанного контейнера в предоставленной пользователем учетной записи хранения. |
> | Microsoft.ApiManagement/service/updatecertificate/action | Передает SSL-сертификата для службы управления API. |
> | Microsoft.ApiManagement/service/updatehostname/action | Позволяет настроить, обновить или удалить имена личных доменов для службы управления API. |
> | Microsoft.ApiManagement/service/write | Создание экземпляра службы управления API. |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Возвращает список ключей пользователя. |

## <a name="api-management-service-reader-role"></a>Роль читателя данных службы управления API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Доступ к службе и интерфейсам API в режиме "только для чтения". |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Действия** |  |
> | Microsoft.ApiManagement/service/*/read | Чтение экземпляров службы управления API. |
> | Microsoft.ApiManagement/service/read | Чтение метаданных для экземпляра службы управления API. |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Возвращает список ключей пользователя. |

## <a name="application-insights-component-contributor"></a>Участник компонента Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять компонентами Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.Insights/webtests/* | Создание веб-тестов и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="application-insights-snapshot-debugger"></a>Отладчик моментальных снимков Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Предоставляет пользователю разрешение на использование функций отладчика моментальных снимков Application Insights. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-job-operator"></a>Оператор заданий службы автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание заданий и управление ими с помощью модулей Runbook службы автоматизации. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-operator"></a>Оператор службы автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Операторы автоматизации могут запускать, останавливать, приостанавливать и возобновлять задания. |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Считывает ресурсы гибридной рабочей роли Runbook. |
> | Microsoft.Automation/automationAccounts/jobs/read | Возвращает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Возобновляет выполнение задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Останавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Возвращает поток задания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Приостанавливает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobs/write | Создает задание службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Возвращает расписание заданий службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Создает расписание заданий службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Получает рабочую область, связанную с учетной записью службы автоматизации. |
> | Microsoft.Automation/automationAccounts/read | Возвращает учетную запись службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/schedules/read | Возвращает ресурс расписания службы автоматизации Azure. |
> | Microsoft.Automation/automationAccounts/schedules/write | Создает или обновляет ресурс расписания службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Возвращает выходные данные задания. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="automation-runbook-operator"></a>Оператор Runbook службы автоматизации
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение свойств Runbook, позволяющее создавать задания Runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Automation/automationAccounts/runbooks/read | Возвращает runbook службы автоматизации Azure. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="azure-stack-registration-owner"></a>Владелец регистрации Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять регистрациями Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Действия** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Возвращает подробные сведения о продукте из Azure Stack Marketplace. |
> | Microsoft.AzureStack/registrations/products/read | Получает свойства продукта из Azure Stack Marketplace. |
> | Microsoft.AzureStack/registrations/read | Получает свойства регистрации Azure Stack. |

## <a name="backup-contributor"></a>Участник резервного копирования
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службой архивации, но не разрешает создавать хранилища и предоставлять доступ другим пользователям |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Управление результатами операций управления резервным копированием |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Создание контейнеров резервных копий внутри структуры резервного копирования хранилища служб восстановления и управление этими контейнерами |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Создание метаданных, связанных с управлением резервным копированием, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Создание политик резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Создание элементов, включаемых в резервную копию, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Создание контейнеров с элементами, включаемыми в резервную копию, и управление такими контейнерами |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Создание сертификатов, связанных с резервной копией в хранилище служб восстановления, и управление ими |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
> | Microsoft.RecoveryServices/Vaults/usages/* | Создание хранилища служб восстановления и управление его использованием |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="backup-operator"></a>Оператор резервного копирования
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами архивации, но не удалять архивные копии, создавать хранилища или предоставлять доступ другим пользователям |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Архивирует защищенный элемент. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Восстанавливает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Создание заданий резервного копирования и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Отменяет задание. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Возвращает метаданные управления архивацией для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Создание результатов операций управления резервным копированием и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Создание элементов, для которых можно создавать резервные копии, и управление ими |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Возвращает список всех защищаемых элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Управление операциями обнаружения для получения новых контейнеров |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Операцию регистрации контейнера в службе можно использовать для регистрации контейнера в службе восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Подготавливает мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Отменяет мгновенное восстановление для защищенного элемента. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="backup-reader"></a>Читатель резервных копий
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать службы резервного копирования, но не может вносить изменения. |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Возвращает состояние операции. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Возвращает результат операции, выполненной с контейнером защиты. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Возвращает результат операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Возвращает состояние операции, выполненной с защищенными элементами. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Возвращает все зарегистрированные контейнеры. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Возвращает результат операции задания. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Возвращает все объекты заданий. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Экспортирует задания. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Возвращает метаданные управления архивацией для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Возвращает результат операции архивации для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Возвращает результаты операции политики. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Возвращает список всех защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Возвращает все контейнеры, принадлежащие подписке. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Возвращает сводки по защищенным элементам и защищенным серверам для служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Возвращает точки восстановления для защищенных элементов. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Возвращает результат операции задания экспорта. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |

## <a name="billing-reader"></a>Читатель счетов
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Разрешает читать данные выставления счетов. |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Billing/*/read | Чтение сведений о выставлении счетов |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | Вывод списка групп управления для пользователя, прошедшего проверку подлинности. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="biztalk-contributor"></a>Участник BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами BizTalk, но не доступом к ним. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BizTalkServices/BizTalk/* | Создание служб BizTalk и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-endpoint-contributor"></a>Участник конечных точек CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять конечными точками CDN, но не может предоставлять доступ другим пользователям. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-endpoint-reader"></a>Читатель конечной точки CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать конечные точки CDN, но не может вносить изменения. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-profile-contributor"></a>Участник профиля CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять профилями CDN и их конечными точками, но не может предоставлять доступ другим пользователям. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cdn-profile-reader"></a>Читатель данных профиля CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может просматривать профили CDN и их конечные точки, но не может вносить изменения. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-network-contributor"></a>Участник классической сети
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими сетями, но не доступом к ним. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicNetwork/* | Создание классических сетей и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-storage-account-contributor"></a>Участник классической учетной записи хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими учетными записями хранения, но не предоставлять доступ к ним. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicStorage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="classic-storage-account-key-operator-service-role"></a>Роль службы оператора ключей классических учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Операторы ключей классических учетных записей хранения могут перечислять и повторно создавать ключи в классических учетных записях хранения. |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Действия** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Повторно создает существующие ключи доступа для учетной записи хранения. |

## <a name="classic-virtual-machine-contributor"></a>Участник классической виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять классическими виртуальными машинами, но не доступом к ним и не учетной записью виртуальной сети или хранения, к которой они подключены. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.ClassicCompute/domainNames/* | Создание доменных имен для классических вычислений и управление ими |
> | Microsoft.ClassicCompute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Привязывает зарезервированный IP-адрес. |
> | Microsoft.ClassicNetwork/reservedIps/read | Возвращает зарезервированные IP-адреса. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Подключает виртуальную сеть. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Возвращает виртуальную сеть. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Возвращает диск в учетной записи хранения. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Возвращает образ в учетной записи хранения. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="cleardb-mysql-db-contributor"></a>Участник ClearDB MySQL DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять базами данных ClearDB MySQL, но не доступом к ним. |
> | **Id** | 9106cda0-8a86-4e81-b686-29a22c54effe |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | successbricks.cleardb/databases/* | Создание баз данных ClearDB MySQL и управление ими |

## <a name="cosmos-db-account-reader-role"></a>Роль читателя учетных записей Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет считывать данные учетных записей Azure Cosmos DB. Сведения об управлении учетными записями Azure Cosmos DB см. в разделе [Участник учетной записи DocumentDB](#documentdb-account-contributor). |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей. Может считывать данные о разрешениях, предоставленных каждому пользователю |
> | Microsoft.DocumentDB/*/read | Чтение любой коллекции |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Считывает ключи только для чтения учетной записи базы данных. |
> | Microsoft.Insights/MetricDefinitions/read | Считывает определения метрик. |
> | Microsoft.Insights/Metrics/read | Считывает метрики. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="data-factory-contributor"></a>Участник фабрики данных
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание фабрик данных и управление ими, а также их дочерними ресурсами. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DataFactory/dataFactories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.DataFactory/factories/* | Создание фабрик данных и дочерних ресурсов внутри их, а также управление ими. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="data-lake-analytics-developer"></a>Разработчик Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет отправлять, отслеживать задания и управлять ими, но не позволяет создавать или удалять учетные записи Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Удаляет учетную запись Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Предоставляет разрешения на отмену заданий, отправленных другими пользователями. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Создание или обновление учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Создание или обновление связанной учетной записи Data Lake Store для учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Отмена связи учетной записи Data Lake Store с учетной записью Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Создание или обновление связанной учетной записи хранения для учетной записи Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Удаление связи учетной записи хранения с учетной записью Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Создает или обновляет правило брандмауэра. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Удаляет правило брандмауэра. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Создание или обновление политики вычислений. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Удаление политики вычислений. |

## <a name="data-purger"></a>Средство очистки данных
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может выполнить очистку данных аналитики. |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Действия** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action |  |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Удаление указанных данных из рабочей области. |

## <a name="devtest-labs-user"></a>Пользователь DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет подключать, запускать, перезапускать виртуальные машины и завершать их работу в Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Compute/availabilitySets/read | Возвращает свойства группы доступности. |
> | Microsoft.Compute/virtualMachines/*/read | Чтение свойств виртуальной машины (размеры виртуальных машин, состояние среды выполнения, расширения виртуальных машин, и т. д.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Завершает работу виртуальной машины и освобождает ее вычислительные ресурсы. |
> | Microsoft.Compute/virtualMachines/read | Возвращает свойства виртуальной машины. |
> | Microsoft.Compute/virtualMachines/restart/action | Перезапускает виртуальную машину. |
> | Microsoft.Compute/virtualMachines/start/action | Запускает виртуальную машину. |
> | Microsoft.DevTestLab/*/read | Чтение свойств лаборатории |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Создает виртуальные машины в лаборатории. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Отправляет заявку на доступ к случайной запрашиваемой виртуальной машине в лаборатории. |
> | Microsoft.DevTestLab/labs/formulas/delete | Удаляет формулы. |
> | Microsoft.DevTestLab/labs/formulas/read | Считывает формулы. |
> | Microsoft.DevTestLab/labs/formulas/write | Добавляет или изменяет формулы. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Оценивает политику лаборатории. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Присваивает владение существующей виртуальной машиной. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/*/read | Чтение свойств сетевого интерфейса (например, всех балансировщиков нагрузки, частью которых является сетевой интерфейс) |
> | Microsoft.Network/networkInterfaces/join/action | Подключает виртуальную машину к сетевому интерфейсу. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Network/networkInterfaces/write | Создает новый сетевой интерфейс или обновляет существующий.  |
> | Microsoft.Network/publicIPAddresses/*/read | Чтение свойств общедоступного IP-адреса |
> | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/deployments/read | Возвращает развернутые службы или выводит их список. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Выводит список доступных размеров для обновления виртуальной машины. |

## <a name="dns-zone-contributor"></a>Участник зоны DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять зонами DNS и наборами записей в Azure DNS, но не тем, кому они будут доступны. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/dnsZones/* | Создание зон и записей DNS и управление ими. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими. |

## <a name="documentdb-account-contributor"></a>Участник учетной записи DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может управлять учетными записями Azure Cosmos DB Служба Azure Cosmos DB раньше называлась DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.DocumentDb/databaseAccounts/* | Создание учетных записей Azure Cosmos DB и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="intelligent-systems-account-contributor"></a>Участник учетной записи интеллектуальных систем
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями интеллектуальных систем, но не доступом к ним. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.IntelligentSystems/accounts/* | Создание учетных записей интеллектуальных систем и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="key-vault-contributor"></a>Участник Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять хранилищами ключей, но не доступом к ним. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Очищает обратимо удаленное хранилище Key Vault. |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Создатель лаборатории
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать, администрировать и удалять управляемые лаборатории в учетных записях Служб лабораторий Azure. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Создание лаборатории в учетной записи лаборатории. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="log-analytics-contributor"></a>участник Log Analytics.
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Участник Log Analytics может считывать все данные мониторинга и изменять параметры мониторинга. Изменение параметров мониторинга подразумевает добавление расширений в виртуальные машины, чтение ключей учетной записи хранения для настройки коллекции журналов в службе хранилища Azure, создание и настройку учетных записей службы автоматизации, добавление решений и настройку диагностики Azure во всех ресурсах Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="log-analytics-reader"></a>читатель Log Analytics;
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Читатель Log Analytics может просматривать все данные мониторинга, выполнять по ним поиск и просматривать параметры мониторинга, в том числе конфигурацию системы диагностики Azure для всех ресурсов Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Поиск с помощью нового механизма. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |

## <a name="logic-app-contributor"></a>Создатель приложений логики
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять приложениями логики, но не доступом к ним. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Отображает ключи доступа для учетных записей хранения. |
> | Microsoft.ClassicStorage/storageAccounts/read | Возвращает учетную запись хранения для заданной учетной записи. |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Создание, обновление или считывание параметра диагностики для сервера анализа данных. |
> | Microsoft.Insights/logdefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/metricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Logic/* | Управляет ресурсами Logic Apps. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/connectionGateways/* | Создает шлюз подключения и управляет им. |
> | Microsoft.Web/connections/* | Создает подключение и управляет им. |
> | Microsoft.Web/customApis/* | Создает настраиваемый API и управляет им. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/functions/listSecrets/action | Выводит список секретов для функций веб-приложений. |

## <a name="logic-app-operator"></a>Оператор приложений логики
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет читать, включать и отключать приложения логики. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/*/read | Считывание правил оповещений Insights. |
> | Microsoft.Insights/diagnosticSettings/*/read | Получает параметры диагностики для Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Получает доступные метрики для Logic Apps. |
> | Microsoft.Logic/*/read | Считывает ресурсы Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Отключает рабочий процесс. |
> | Microsoft.Logic/workflows/enable/action | Включает рабочий процесс. |
> | Microsoft.Logic/workflows/validate/action | Проверяет рабочий процесс. |
> | Microsoft.Resources/deployments/operations/read | Возвращает операции развертывания или выводит их список. |
> | Microsoft.Resources/subscriptions/operationresults/read | Возвращает результаты операции подписки. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/connectionGateways/*/read | Считывает шлюзы подключения. |
> | Microsoft.Web/connections/*/read | Считывает подключения. |
> | Microsoft.Web/customApis/*/read | Считывает настраиваемый API. |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |

## <a name="managed-identity-contributor"></a>Участник управляемого удостоверения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Создание, чтение, обновление и удаление пользовательских удостоверений. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="managed-identity-operator"></a>Оператор управляемого удостоверения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Чтение и назначение пользовательских удостоверений. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Действия** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="monitoring-contributor"></a>Monitoring Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может читать все данные мониторинга и изменять параметры мониторинга. Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/AlertRules/* | Чтение, запись и удаление правила генерации оповещений. |
> | Microsoft.Insights/components/* | Чтение, запись и удаление компонентов Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Чтение, запись и удаление параметров диагностики. |
> | Microsoft.Insights/eventtypes/* | Вывод списка событий журнала действий (событий управления) в подписке. Это разрешение применяется для доступа к журналу действий посредством кода или портала. |
> | Microsoft.Insights/LogDefinitions/* | Это разрешение необходимо пользователям, которым требуется доступ к журналам действия на портале. Получение списка категорий журнала в журнале действий. |
> | Microsoft.Insights/MetricDefinitions/* | Чтение определений метрик (вывод списка доступных типов метрик для ресурса). |
> | Microsoft.Insights/Metrics/* | Чтение метрик для ресурса. |
> | Microsoft.Insights/Register/Action | Регистрирует поставщик Microsoft Insights. |
> | Microsoft.Insights/webtests/* | Чтение, запись и удаление веб-тестов Application Insights. |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Чтение, запись и удаление пакетов решений Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Чтение, запись и удаление сохраненных поисков Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Извлекает открытые ключи для рабочей области. Эти ключи используются для подключения агентов Microsoft Operational Insights к рабочей области. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Чтение, запись и удаление конфигураций подробных данных хранилища Log Analytics. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Роль Monitoring Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Может читать все данные мониторинга (метрики, журналы и т. д.). Ознакомьтесь также со статьей [Приступая к работе с ролями, разрешениями и системой безопасности с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.OperationalInsights/workspaces/search/action | Выполняет поисковый запрос. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="network-contributor"></a>Участник сети
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять сетями, но не доступом к ним. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/* | Создание сетей и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="new-relic-apm-account-contributor"></a>Участник учетной записи New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями и приложениями New Relic Application Performance Management, но не доступом к ним. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Модуль чтения и доступ к данным
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать все данные, но не позволит удалить или создать учетную запись хранения или содержащий ресурс. Он также предоставит доступ на чтение и запись для всех данных, содержащихся в учетной записи хранения через доступ к ключам учетной записи хранения. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |

## <a name="redis-cache-contributor"></a>Участник кэша Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять кэшем Redis, но не доступом к нему. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Cache/redis/* | Создание кэшей Redis и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="scheduler-job-collections-contributor"></a>Участник коллекции заданий планировщика
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять коллекциями заданий планировщика, но не доступом к ним. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Scheduler/jobcollections/* | Создание коллекциями заданий и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="search-service-contributor"></a>Участник службы поиска
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службами поиска, но не доступом к ним. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Search/searchServices/* | Создание служб поиска и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-admin"></a>администратор безопасности;
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Только в центре безопасности: может просматривать политики безопасности и состояния безопасности, изменять политики безопасности, просматривать оповещения и рекомендации, а также закрывать предупреждения и рекомендации |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Authorization/policyAssignments/* | Создание назначений политик и управление ими |
> | Microsoft.Authorization/policyDefinitions/* | Создание определений политик и управление ими |
> | Microsoft.Authorization/policySetDefinitions/* | Создание наборов политик и управление ими |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/*/read | Чтение компонентов и политик безопасности |
> | Microsoft.Security/locations/alerts/dismiss/action | Отклонение оповещения системы безопасности. |
> | Microsoft.Security/locations/alerts/activate/action | Активация оповещения системы безопасности. |
> | Microsoft.Security/locations/tasks/dismiss/action | Закрывает рекомендацию по безопасности. |
> | Microsoft.Security/locations/tasks/activate/action | Активирует рекомендацию по безопасности. |
> | Microsoft.Security/policies/write | Обновляет политику безопасности. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-manager-legacy"></a>Диспетчер безопасности (устаревший)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Это устаревшая роль. Используйте роль администратора безопасности |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.ClassicCompute/*/read | Чтение сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Запись сведений о конфигурации классических виртуальных машин |
> | Microsoft.ClassicNetwork/*/read | Чтение сведений о конфигурации для классической сети |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/* | Создание компонентов и политик безопасности и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="security-reader"></a>Чтение данных безопасности
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Только в центре безопасности: может просматривать оповещения и рекомендации, просматривать политики безопасности и состояния безопасности, но не может вносить изменения |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Действия** |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.operationalInsights/workspaces/*/read | Просмотр данных Log Analytics |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Security/*/read | Чтение компонентов и политик безопасности |

## <a name="site-recovery-contributor"></a>Участник Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять службой Site Recovery, за исключением создания хранилищ и назначения ролей. |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Операция обновления сертификата ресурса обновляет сертификат учетных данных для ресурса или хранилища. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Создание расширенных сведений, связанных с хранилищем, и управление ими |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Создание зарегистрированных удостоверений и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Создание или обновление параметров оповещения репликации |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Создание структур репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Создание политик репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Создание планов восстановления и управление ими |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Создание конфигурации службы хранилища для хранилища служб восстановления и управление ею |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="site-recovery-operator"></a>Оператор Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет выполнять отработку отказа и восстановление размещения, но не другие операции управления Site Recovery. |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывает параметры оповещений. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Проверяет согласованность структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывает структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Повторно задает связь шлюза. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Обновление сертификата для структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывает сети. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывает сетевые сопоставления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывает контейнеры защиты. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Применяет точку восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Выполняет отработку отказа с фиксацией. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Выполняет плановую отработку отказа. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывает защищенные элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Исправляет репликацию. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Повторно защищает защищенный элемент. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Тестирование отработки отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Тестирует очистку отработки отказа. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Отработка отказа |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Обновляет службу Mobility Service. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывает сопоставления контейнера защиты. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывает поставщики служб восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Обновляет поставщик. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывает классификации хранилища. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывает сопоставления классификаций хранилища. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Считывает задания. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Создание заданий репликации и управление ими |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывает политики. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Выполняет отработку отказа с фиксацией для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Выполняет плановую отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывает планы восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Повторно защищает план восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Тестирует отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Тестирует очистку отработки отказа для плана восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Выполняет отработку отказа для плана восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Чтение оповещений для хранилища служб восстановления |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="site-recovery-reader"></a>Читатель Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет просматривать состояние Site Recovery без выполнения других операций управления. |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp является внутренней операцией, используемой службой. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Операция получения расширенных сведений о хранилище возвращает расширенные сведения об объекте, представляющие ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Возвращает оповещения для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Операцию получения результатов операции можно использовать, чтобы получить состояние и результат асинхронно отправленной операции. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Операцию получения контейнеров можно использовать для получения контейнеров, зарегистрированных для ресурса. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Считывает параметры оповещений. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Считывает события. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Считывает структуры. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Считывает сети. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Считывает сетевые сопоставления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Считывает контейнеры защиты. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Считывает защищаемые элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Считывает защищенные элементы. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Считывает точки восстановления для репликации. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Считывает сопоставления контейнера защиты. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Считывает поставщики служб восстановления. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Считывает классификации хранилища. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Считывает сопоставления классификаций хранилища. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Считывает задания. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Считывает задания. |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Считывает политики. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Считывает планы восстановления. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Возвращает сведения о маркере для хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Операцию получения маркера хранилища можно использовать, чтобы получить маркер хранилища для операций серверной части уровня хранилища. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="sql-db-contributor"></a>Участник БД SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять базами данных SQL, но не доступом к ним. Кроме того, не позволяет управлять их политиками безопасности или родительскими серверами SQL Server. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Создание баз данных SQL и управление ими |
> | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита |
> | Microsoft.Sql/servers/databases/auditRecords/read | Извлекает записи аудита больших двоичных объектов для базы данных. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Диспетчер безопасности SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять политиками безопасности серверов SQL Server и баз данных SQL, но не доступом к ним. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение авторизации Майкрософт |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/servers/auditingPolicies/* | Создание политик аудита SQL Server и управление ими |
> | Microsoft.Sql/servers/auditingSettings/* | Создание параметров аудита SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Создание политик аудита баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Создание параметров аудита баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/auditRecords/read | Чтение записей аудита |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Создание политик подключения баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Создание политик маскирования данных баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/read | Возвращение списка баз данных или возвращение свойств указанной базы данных. |
> | Microsoft.Sql/servers/databases/schemas/read | Получение списка схем базы данных. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Извлекает список столбцов таблицы. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Получение списка таблиц базы данных. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Создание политик оповещения системы безопасности баз данных SQL Server и управление ими |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Создание метрик безопасности базы данных SQL и управление ими |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Возвращение списка серверов или получение свойств для указанного сервера. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Создание политик оповещения системы безопасности SQL Server и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="sql-server-contributor"></a>Участник SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять серверами SQL Server и базами данных SQL, но не доступом к ним и их политиками безопасности. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Создание серверов SQL Server и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Не может изменять политики аудита SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Не может изменять параметры аудита SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Не может изменять политики аудита баз данных SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Не может изменять параметры аудита баз данных SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Не может читать записи аудита |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Не может изменять политики подключения баз данных SQL Server |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Не может изменять политики маскирования данных баз данных SQL Server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности баз данных SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Не может изменять метрики безопасности баз данных SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Не может изменять политики оповещения системы безопасности SQL Server |

## <a name="storage-account-contributor"></a>Участник учетной записи хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять учетными записями хранения, но не доступом к ним. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение всех элементов |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/diagnosticSettings/* | Управление параметрами диагностики |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Присоединение к подсети ресурса, например учетной записи хранения или базы данных SQL. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/* | Создание учетных записей хранения и управление ими |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="storage-account-key-operator-service-role"></a>Роль службы оператора ключей учетных записей хранения
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Операторы ключей учетных записей хранения могут перечислять и повторно создавать ключи в учетных записях хранения. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Действия** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Повторно создает ключи доступа для указанной учетной записи хранения. |

## <a name="support-request-contributor"></a>Support Request Contributor (Участник с правом создавать запросы на поддержку)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет создавать запросы в службу поддержки и управлять ими. |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="traffic-manager-contributor"></a>Участник диспетчера трафика
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять профилями диспетчера трафика, но не доступом к ним. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Чтение ролей и назначений ролей |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="user-access-administrator"></a>Администратор доступа пользователей
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять доступом пользователей к ресурсам Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Действия** |  |
> | */чтение | Чтение ресурсов всех типов, кроме секретов. |
> | Microsoft.Authorization/* | Управление авторизацией |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="virtual-machine-administrator-login"></a>Администратор виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Пользователи с этой ролью могут входить на виртуальную машину с правами администратора Windows или привилегированного пользователя Linux. |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |

## <a name="virtual-machine-contributor"></a>Участник виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять виртуальными машинами, но не доступом к ним и не виртуальной сетью или учетной записью хранения, к которой они подключены. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Compute/availabilitySets/* | Создание групп доступности вычислений и управление ими |
> | Microsoft.Compute/locations/* | Создание расположений вычислений и управление ими |
> | Microsoft.Compute/virtualMachines/* | Создание виртуальных машин и управление ими |
> | Microsoft.Compute/virtualMachineScaleSets/* | Создание наборов масштабирования виртуальных машин и управление ими |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов шлюза приложений. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Выполняет присоединение к внутреннему пулу адресов подсистемы балансировки нагрузки. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Присоединяет пул входящих подключений NAT подсистемы балансировки нагрузки. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Присоединяет правило NAT для входящего трафика подсистемы балансировки нагрузки. |
> | Microsoft.Network/loadBalancers/probes/join/action | Позволяет использовать зонды подсистемы балансировки нагрузки. Например, при наличии этого разрешения свойство healthProbe в масштабируемом наборе виртуальных машин может ссылаться на конкретный зонд. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/locations/* | Создание сетевых расположений и управление ими |
> | Microsoft.Network/networkInterfaces/* | Создание сетевых интерфейсов и управление ими |
> | Microsoft.Network/networkSecurityGroups/join/action | Присоединяет группу безопасности сети. |
> | Microsoft.Network/networkSecurityGroups/read | Возвращает определение группы безопасности сети. |
> | Microsoft.Network/publicIPAddresses/join/action | Присоединяет общедоступный IP-адрес. |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Присоединяет виртуальную сеть. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Возвращает сведения об объекте для защищенного элемента. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Создает элемент, защищенный службой архивации. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Создание цели защиты для резервной копии. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Возвращает все политики защиты. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Создает политику защиты. |
> | Microsoft.RecoveryServices/Vaults/read | Операция получения хранилища возвращает объект, представляющий ресурс Azure типа "хранилище". |
> | Microsoft.RecoveryServices/Vaults/usages/read | Возвращает данные об использовании хранилища служб восстановления. |
> | Microsoft.RecoveryServices/Vaults/write | Создает операцию создания хранилища, которая создает ресурс Azure типа "хранилище". |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Возвращает ключи доступа для указанной учетной записи хранения. |
> | Microsoft.Storage/storageAccounts/read | Возвращает список учетных записей хранения или свойства указанной учетной записи хранения. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |

## <a name="virtual-machine-user-login"></a>Пользователь виртуальной машины
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Пользователи с этой ролью могут входить на виртуальную машину как обычные пользователи. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Действия** |  |
> | Microsoft.Network/publicIPAddresses/read | Возвращает определение общедоступного IP-адреса. |
> | Microsoft.Network/virtualNetworks/read | Возвращает определение виртуальной сети. |
> | Microsoft.Network/loadBalancers/read | Возвращает определение подсистемы балансировки нагрузки. |
> | Microsoft.Network/networkInterfaces/read | Возвращает определение сетевого интерфейса.  |
> | Microsoft.Compute/virtualMachines/*/read |  |

## <a name="web-plan-contributor"></a>Участник веб-плана
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять веб-планами для веб-сайтов, но не доступом к ним. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/serverFarms/* | Создание ферм серверов и управление ими |

## <a name="website-contributor"></a>Участник веб-сайта
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Описание** | Позволяет управлять веб-сайтами (не веб-планами), но не доступом к ним. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Действия** |  |
> | Microsoft.Authorization/*/read | Авторизация на чтение |
> | Microsoft.Insights/alertRules/* | Создание правил оповещения Insights и управление ими |
> | Microsoft.Insights/components/* | Создание компонентов Insights и управление ими |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Возвращает состояния доступности для всех ресурсов в указанной области. |
> | Microsoft.Resources/deployments/* | Создание развертываний группы ресурсов и управление ими |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Возвращает группы ресурсов или выводит их список. |
> | Microsoft.Support/* | Создание запросов в службу поддержки и управление ими |
> | Microsoft.Web/certificates/* | Создание сертификатов веб-сайтов и управление ими |
> | Microsoft.Web/listSitesAssignedToHostName/read | Возвращает имена сайтов, назначенные имени узла. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Возвращает свойства плана службы приложений. |
> | Microsoft.Web/sites/* | Создание веб-сайтов и управление ими (создание сайта также требует разрешений на запись к связанному плану службы приложений). |

## <a name="next-steps"></a>Дополнительная информация

- [Пользовательские роли](custom-roles.md)
- [Use Role-Based Access Control to manage access to your Azure subscription resources](role-assignments-portal.md) (Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure)
- [Разрешения в центре безопасности Azure](../security-center/security-center-permissions.md)
