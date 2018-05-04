---
title: Поддерживаемые типы ресурсов в службе работоспособности ресурсов Azure | Документация Майкрософт
description: Поддерживаемые типы ресурсов в службе работоспособности ресурсов Azure
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 3b4d99fe883cf52ca7f1ef98e70b7f3a1bccd5ae
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Типы ресурсов и проверки работоспособности в службе работоспособности ресурсов Azure
Ниже приведен полный список проверок, выполняемых в системе работоспособности ресурсов для разных типов ресурсов.

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Выполняемые проверки|
|---|
|<ul><li>Служба Api Management запущена и работает?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Выполняемые проверки|
|---|
|<ul><li>Все ли узлы кэша запущены и работают?</li><li>Доступен ли кэш из центра обработки данных?</li><li>Достигнуто ли для кэша максимальное число подключений?</li><li> Исчерпан ли лимит доступной памяти кэша? </li><li>Возникает ли в кэше большое число ошибок страниц?</li><li>Работает ли кэш с повышенной загрузкой?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Выполняемые проверки|
|---|
|<ul> <li>Какие-то конечные точки остановлены, удалены или неправильно сконфигурированы?</li><li>Доступен ли дополнительный портал для операций настройки CDN?</li><li>Существуют ли проблемы с доставкой между конечными точками CDN?</li><li>Могут ли пользователи изменять настройки ресурсов CDN?</li><li>Изменения конфигурации публикуются с ожидаемой скоростью?</li><li>Могут ли пользователи управлять конфигурацией CDN с помощью портала Azure, PowerShell или API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Завершена ли загрузка ОС на узле?</li><li>Контейнер виртуальной машины подготовлен и включен?</li><li>Есть ли сетевое подключение между узлом и учетной записи хранения?</li><li>Завершена ли загрузка гостевой операционной системы?</li><li>Есть ли назначенные работы по техническому обслуживанию?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Выполняемые проверки|
|---|
|<ul><li>Доступна ли учетная запись из центра обработки данных?</li><li>Доступен ли поставщик ресурсов Cognitive Services?</li><li>Доступна ли служба Cognitive Services из соответствующего региона?</li><li>Успешно ли выполняются операции чтения для учетной записи хранения, в которой хранятся метаданные ресурсов?</li><li>Достигнута ли квота на вызовы API?</li><li>Достигнут ли лимит чтения на вызовы API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Выполняемые проверки|
|---|
|<ul><li>Сервер, размещающий эту виртуальную машину, включен и работает?</li><li>Завершена ли загрузка ОС на узле?</li><li>Контейнер виртуальной машины подготовлен и включен?</li><li>Есть ли сетевое подключение между узлом и учетной записи хранения?</li><li>Завершена ли загрузка гостевой операционной системы?</li><li>Есть ли назначенные работы по техническому обслуживанию?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Выполняемые проверки|
|---|
|<ul><li>Могут ли пользователи отправлять задания в Data Lake Analytics этого региона?</li><li>Успешно ли запускаются и выполняются основные задания в этом регионе?</li><li>Могут ли пользователи перечислять элементы каталога в этом регионе?</li>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Выполняемые проверки|
|---|
|<ul><li>Могут ли пользователи отправлять данные в Data Lake Store из этого региона?</li><li>Могут ли пользователи скачивать данные из Data Lake Store из этого региона?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Выполняемые проверки|
|---|
|<ul><li>Работает ли Центр Интернета вещей?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Выполняемые проверки|
|---|
|<ul><li>Существуют ли запросы к базам данных или коллекциям, которые не были выполнены из-за недоступности службы Azure Cosmos DB?</li><li>Существуют ли запросы к документам, которые не были выполнены из-за недоступности службы Azure Cosmos DB?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Выполняемые проверки|
|---|
|<ul><li>Подключен ли VPN-туннель?</li><li>Существуют ли конфликты конфигурации для соединения?</li><li>Правильно ли настроены предварительные общие ключи?</li><li>Доступно ли VPN-устройство в локальной среде?</li><li>Есть ли несоответствия в политике безопасности IPSec/IKE?</li><li>Правильно ли подготовлено подключение S2S VPN или оно находится в состоянии сбоя?</li><li>Правильно ли подготовлено подключение между виртуальными сетями или оно находится в состоянии сбоя?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Выполняемые проверки|
|---|
|<ul><li>Доступен ли VPN-шлюз из Интернета?</li><li>Находится ли VPN-шлюз в режиме ожидания?</li><li>Запущена ли служба VPN на шлюзе?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Выполняемые проверки|
|---|
|<ul><li> Успешно ли выполняются для пространства имен операции времени выполнения, такие как регистрации, установки и отправки?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Выполняемые проверки|
|---|
|<ul><li>Работает ли операционная система узла?</li><li>Доступна ли коллекция workspaceCollection из-за пределов центра обработки данных?</li><li>Доступен ли поставщик ресурсов PowerBI?</li><li>Доступна ли служба PowerBI для соответствующего региона?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Выполняемые проверки|
|---|
|<ul><li>Успешно ли выполняются операции диагностики для кластера?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Выполняемые проверки|
|---|
|<ul><li> Выполнялись ли входы в базу данных?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Выполняемые проверки|
|---|
|<ul><li>Все ли узлы, на которых выполняются задания, успешно работают?</li><li>Существуют ли задания, которые не удалось запустить?</li><li>Существуют ли запланированные обновления среды выполнения?</li><li>Находится ли задание в запланированном состоянии (запущено или остановлено клиентом)?</li><li>Возникали ли исключения нехватки памяти при выполнении задания?</li><li>Существуют ли запланированные обновления вычислительной среды?</li><li>Доступен ли диспетчер выполнения (план управления)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Работает ли служба Internet Information Services?</li><li>Работает ли подсистема балансировки нагрузки?</li><li>Доступен ли план службы приложений из центра обработки данных?</li><li>Доступна ли учетная запись хранилища, в которой размещается содержимое узлов для фермы серверов?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Работает ли сервер Internet Information?</li><li>Работает ли подсистема балансировки нагрузки?</li><li>Доступно ли веб-приложение из центра обработки данных?</li><li>Доступна ли учетная запись хранения, в которой размещается контент сайта?</li></ul>|

# <a name="next-steps"></a>Дальнейшие действия
-  Дополнительные сведения см. в [общих сведениях о панели мониторинга работоспособности службы Azure](service-health-overview.md) и [общих сведениях о службе "Работоспособность ресурсов Azure"](resource-health-overview.md). 
-  [Azure Resource Health FAQ](resource-health-faq.md) (Часто задаваемые вопросы о службе работоспособности ресурсов Azure)
- Настройте оповещения о проблемах, связанных с работоспособностью. Дополнительные сведения см. в статье [Создание оповещений журнала действий для уведомлений службы](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
