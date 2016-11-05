---
title: Обзор сбора данных службы хранилища Azure в Log Analytics | Microsoft Docs
description: Ресурсы Azure могут записывать данные журналов и метрик в учетную запись хранения Azure, используя систему диагностики Azure. Служба Log Analytics может индексировать эти данные и делать их доступными для поиска.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="collecting-azure-storage-data-in-log-analytics-overview"></a>Обзор сбора данных службы хранилища Azure в Log Analytics
Многие ресурсы Azure могут записывать данные журналов и метрик в учетную запись хранения Azure. Log Analytics может использовать эти данные и упрощать мониторинг ресурсов Azure.

Для записи данных в службу хранилища Azure ресурс может использовать систему диагностики Azure или собственный способ. Эти данные могут быть записаны в различных форматах в одно из следующих расположений:

* таблицу Azure;
* большой двоичный объект Azure;
* концентратор событий.

Log Analytics поддерживает службы Azure, которые записывают данные с помощью [журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Кроме того, Log Analytics поддерживает другие службы, которые выводят данные журналов и метрик в различных форматах и расположениях.  

> [!NOTE]
> За хранение и выполнение операций взимается стандартная плата Azure, если вы отправляете данные диагностики в учетную запись хранения, и служба Log Analytics считывает данные из вашей учетной записи хранения.
> 
> 

![Схема службы хранилища Azure](media/log-analytics-azure-storage/azure-storage-diagram.png)

## <a name="supported-azure-resources"></a>Поддерживаемые ресурсы Azure
Log Analytics может собирать данные для следующих ресурсов Azure:

| Тип ресурса | Журналы (категории диагностики) | Решение Log Analytics |
| --- | --- | --- |
| Application Insights |Доступность <br> Настраиваемые события <br> Исключения <br> Запросы <br> |Application Insights (предварительная версия) |
| Управление API | |*Нет* (предварительная версия) |
| Автоматизация <br> Microsoft.Automation/AutomationAccounts |JobLogs <br> JobStreams |Azure Automation (предварительная версия) |
| хранилище ключей; <br> Microsoft.KeyVault/Vaults |AuditEvent |Хранилище ключей (предварительная версия) |
| Шлюз приложений <br> Microsoft.Network/ApplicationGateways |ApplicationGatewayAccessLog <br> ApplicationGatewayPerformanceLog |Сети Azure (предварительная версия) |
| Группа безопасности сети <br> Microsoft.Network/NetworkSecurityGroups |NetworkSecurityGroupEvent <br> NetworkSecurityGroupRuleCounter |Сети Azure (предварительная версия) |
| Service Fabric |Событие трассировки событий Windows <br> Операционное событие <br> Событие субъектов Reliable Actors <br> Событие надежных служб |Service Fabric (предварительная версия) |
| Виртуальные машины |Системный журнал Linux <br> Событие Windows <br> Журнал IIS <br> Событие трассировки событий Windows |*Нет* |
| Веб-роли <br> Рабочие роли |Системный журнал Linux <br> Событие Windows <br> Журнал IIS <br> Событие трассировки событий Windows |*Нет* |

> [!NOTE]
> Для мониторинга виртуальных машин Azure (Linux и Windows) рекомендуется установить [расширение виртуальной машины Analytics журнала](log-analytics-azure-vm-extension.md). Агент позволяет получить более подробные сведения о виртуальных машинах, чем при использовании диагностики, записанной в хранилище.
> 
> 

Вы можете помочь нам определить, какие дополнительные журналы должны анализироваться службой OMS в первую очередь, проголосовав на [странице отзывов](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).

* Дополнительные сведения о том, как Log Analytics считывает данные журналов из служб Azure, поддерживающих [журналы диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) см. в статье [Анализ журналов диагностики Azure с помощью Log Analytics](log-analytics-azure-storage-json.md).
  * хранилищем ключей Azure
  * Служба автоматизации Azure
  * Шлюз приложений
  * Группы безопасности сети
* Дополнительные сведения о том, как Log Analytics считывает данные журналов служб Azure, которые записывают диагностические данные в хранилище таблиц, или журналы IIS, записанные в хранилище BLOB-объектов, включая следующие, см. статью [Использование хранилища BLOB-объектов для IIS и хранилища таблиц для событий](log-analytics-azure-storage-iis-table.md):
  * Service Fabric
  * веб-роли;
  * рабочие роли;
  * Виртуальные машины

Служба Application Insights находится на стадии личной предварительной версии и она использует непрерывный экспорт в хранилище BLOB-объектов. Чтобы начать использовать личную предварительную версию, обратитесь в службу технической поддержки учетных записей Майкрософт или см. подробные сведения на [сайте отзывов](https://feedback.azure.com/forums/267889-log-analytics/suggestions/6519248-integration-with-app-insights).

## <a name="next-steps"></a>Дальнейшие действия
* [Анализируйте журналы диагностики Azure с помощью Log Analytics](log-analytics-azure-storage-json.md), чтобы считывать данные журналов из служб Azure, которые записывают журналы диагностики Azure в хранилище BLOB-объектов в формате JSON.
* [Используйте хранилище BLOB-объектов для IIS и хранилище таблиц для событий](log-analytics-azure-storage-iis-table.md), чтобы считывать журналы служб Azure, которые записывают диагностические данные в табличное хранилище, или журналы IIS, записанные в хранилище BLOB-объектов.
* [Включите решения](log-analytics-add-solutions.md) , чтобы обеспечить глубокое понимание данных.
* [Воспользуйтесь запросами поиска](log-analytics-log-searches.md) для анализа данных.

<!--HONumber=Oct16_HO2-->


