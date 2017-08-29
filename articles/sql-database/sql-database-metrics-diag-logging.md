---
title: "Метрики и журналы диагностики базы данных SQL Azure | Документация Майкрософт"
description: "Узнайте больше о настройке ресурсов базы данных SQL Azure для хранения данных статистики об использовании ресурсов, подключении и выполнении запросов."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bf41aa530c68ea0e94a09d1dab63237c6f42bce7
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Метрики и журналы диагностики базы данных SQL Azure 
База данных SQL Azure может выдавать значения метрик и журналы диагностики для упрощения мониторинга. Вы можете настроить базу данных SQL Azure для хранения сведений об использовании ресурсов, о рабочих ролях и сеансах, а также настроить подключение к одному из этих ресурсов Azure:
- **Служба хранилища Azure**: для архивации больших объемов телеметрии по оптимальной стоимости.
- **Концентратор событий Azure**: для интеграции телеметрии базы данных SQL Azure с настраиваемым решением для мониторинга или горячими конвейерами.
- **Azure Log Analytics**: для встроенного решения для мониторинга с возможностями предоставления отчетов, предупреждений и выполнения исправлений. 

    ![архитектура](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Включение ведения журналов

Метрики и журналы диагностики не включены по умолчанию. Вы можете включить их и управлять ими, используя один из следующих методов:
- Портал Azure
- PowerShell
- Инфраструктура CLI Azure
- Интерфейс REST API 
- Шаблон Resource Manager

При включении метрик и журналов диагностики необходимо указать ресурс Azure, где собираются выбранные данные. Доступны следующие варианты:
- Служба Log Analytics
- Концентратор событий
- Хранилище Azure 

Вы можете подготовить новый ресурс Azure или выбрать имеющийся. После выбора ресурса хранилища необходимо указать, какие данные нужно собирать. Доступны следующие варианты.

- **[Метрики 1 минуты](sql-database-metrics-diag-logging.md#1-minute-metrics)** содержат сведения о проценте использования DTU, ограничении DTU, проценте использования ЦП, проценте чтения физических данных, проценте записей в журнал, проценте успешных, неудачных или заблокированных подключений брандмауэра, проценте сеансов, проценте рабочих ролей, хранилище, проценте хранилища, проценте хранилища XTP.

При указании учетной записи концентратора событий или AzureStorage можно выбрать политику хранения данных, чтобы сообщить об удалении данных, хранящихся дольше выбранного периода времени. При указании Log Analytics политика хранения определяется на основании выбранной ценовой категории. Узнайте больше о [ценах на Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Мы рекомендуем полностью прочитать статьи [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) и [Сбор и использование диагностических данных из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), чтобы не только научиться включать ведение журнала, но и узнать, какие категории журналов поддерживаются различными службами Azure.

### <a name="azure-portal"></a>Портал Azure

Чтобы включить сбор метрик и журналов диагностики на портале Azure, перейдите на страницу базы данных Azure SQL или эластичного пула и выберите **Параметры диагностики**.

   ![включение на портале Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

Чтобы включить метрики и журналы диагностики с помощью PowerShell, используйте следующие команды:

- Эта команда включает отправку журналов диагностики в учетную запись хранения:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   StorageAccountID — это идентификатор ресурса учетной записи хранения, в которую будут отправляться журналы.

- Эта команда включает потоковую передачу журналов диагностики в концентратор событий:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ServiceBusRuleID — это строка в таком формате:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду.

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Идентификатор ресурса рабочей области Log Analytics можно получить с помощью следующей команды.

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="cli"></a>Интерфейс командной строки

Чтобы включить метрики и журналы диагностики с помощью Azure CLI, используйте следующие команды:

- Эта команда включает отправку журналов диагностики в учетную запись хранения:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   StorageAccountID — это идентификатор ресурса учетной записи хранения, в которую будут отправляться журналы.

- Эта команда включает потоковую передачу журналов диагностики в концентратор событий:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   ServiceBusRuleID — это строка в таком формате:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="rest-api"></a>Интерфейс REST API

Сведения об изменении параметров диагностики с помощью REST API Azure Monitor см. в [этом документе](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Шаблон Resource Manager

Сведения о включении параметров диагностики при создании ресурса из шаблона Resource Manager см. [здесь](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Поток в службе Log Analytics 
Метрики и журналы диагностики базы данных SQL Azure можно передавать в службу Log Analytics с помощью встроенного параметра Send to Log Analytics (Отправка в Log Analytics) на портале или путем включения Log Analytics в параметре диагностики через командлеты Azure PowerShell, Azure CLI или REST API Azure Monitor.

### <a name="installation-overview"></a>Обзор установки

Мониторинг транспортной карты базы данных SQL Azure упрощен благодаря службе Log Analytics. Необходимо выполнить три шага:

1.  Создать ресурс Log Analytics.
2.  Настроить базы данных для записи метрик и журналов диагностики в созданную службу Log Analytics.
3.  Установить решение **Аналитика SQL Azure** из коллекции в Log Analytics.

### <a name="create-log-analytics-resource"></a>Создание ресурса Log Analytics

1. В меню слева щелкните **Создать**.
2. Щелкните **Monitoring + Management** (Мониторинг и управление).
3. Щелкните **Log Analytics**.
4. В форме Log Analytics введите необходимые дополнительные сведения: имя рабочей области, подписки, группы ресурсов, расположения и ценовую категорию.

   ![Служба Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Настройка баз данных для записи метрик и журналов диагностики

Проще всего настроить расположение, в которое базы данных будут записывать метрики, на портале Azure. На портале Azure перейдите к ресурсу базы данных SQL Azure и щелкните **Параметры диагностики**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Установка решения "Аналитика SQL Azure" из коллекции  

1. После создания ресурса Log Analytics и переноса данных в него установите решение "Аналитика SQL Azure". Это можно сделать с помощью **коллекции решений**, которую можно найти на домашней странице OMS, а также в боковом меню. В коллекции найдите и щелкните решение **Аналитика SQL Azure** и нажмите кнопку **Добавить**.

   ![решение мониторинга](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. На домашней странице OMS появится новая плитка под названием **Аналитика SQL Azure**. При выборе этой плитки открывается панель мониторинга решения "Аналитика SQL Azure".

### <a name="using-azure-sql-analytics-solution"></a>Использование решения "Аналитика SQL Azure"

Решение "Аналитика SQL Azure" — это иерархическая панель мониторинга, которая позволяет перемещаться по иерархии ресурсов базы данных SQL Azure. Эта возможность позволяет выполнять мониторинг высокого уровня, а также выполнять мониторинг только некоторых необходимых ресурсов.
Панель мониторинга содержит списки различных ресурсов в рамках выбранного ресурса. Например, для выбранной подписки можно просмотреть все принадлежащие ей серверы, эластичные пулы и базы данных. Кроме того, для эластичных пулов и баз данных можно просмотреть метрики использования ресурсов этого ресурса. Сюда входят диаграммы для DTU, ЦП, операций ввода-вывода, операций записи в журнал, сеансов, рабочих ролей, подключений и объема хранилища в ГБ.

## <a name="stream-into-azure-event-hub"></a>Потоковая передача в концентратор событий Azure

Метрики и журналы диагностики базы данных SQL Azure можно передавать в концентратор событий с помощью встроенного параметра Stream to an event hub (Потоковая передача в концентратор событий) на портале или путем включения идентификатора правила служебной шины в параметре диагностики через командлеты Azure PowerShell, Azure CLI или REST API Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>Что делать с метриками и журналами диагностики в концентраторе событий?
Выполнив потоковую передачу выбранных данных в концентратор событий, вы становитесь на один шаг ближе к включению дополнительных сценариев мониторинга. Концентраторы событий — это аналог парадного входа для событий конвейера. Когда данные поступают в концентратор событий, они могут быть преобразованы и сохранены с использованием любого поставщика средств оперативной аналитики либо адаптера пакетной обработки или хранения. Концентраторы событий отделяют создание потока событий от потребления этих событий, чтобы потребители событий могли обращаться к событиям по собственному расписанию. Дополнительные сведения о концентраторе событий см. в статьях:

- [Что такое концентраторы событий Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Начало работы с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Мы приведем лишь несколько примеров использования потоковой передачи.

-   Проверка работоспособности службы путем потоковой передачи данных критического пути в PowerBI. С помощью концентраторов событий, Stream Analytics и PowerBI можно в близком к реальному времени получать аналитическую информацию о службах Azure на основе метрик и диагностических данных. Обзор настройки концентраторов событий, обработки данных в Stream Analytics и вывода информации через PowerBI см. в статье [Stream Analytics и Power BI. Панель мониторинга для анализа потоковой передачи данных](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-   Потоковая передача журналов в сторонние потоки ведения журналов и сбора телеметрии. С помощью потоковой передачи концентраторов событий вы можете передать метрики и журналы диагностики в другие сторонние решения мониторинга и Log Analytics. 
-   Создание пользовательской платформы для телеметрии и ведения журнала. Если у вас уже есть пользовательская платформа для телеметрии и ведения журнала или вы только планируете ее создать, высокая масштабируемость публикации и подписки концентраторов событий позволит вам гибко настраивать прием журналов диагностики. Ознакомьтесь с [руководством Дэна Росановы (Dan Rosanova) по использованию концентраторов событий для глобальной платформы телеметрии](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Потоковая передача в службу хранилища Azure

Метрики и журналы диагностики базы данных SQL Azure можно хранить в службе хранилища Azure с помощью встроенного параметра Archive to a storage account (Архивировать в учетной записи хранения) на портале Azure или путем включения службы хранилища Azure в параметре диагностики через командлеты Azure PowerShell, Azure CLI или REST API Azure Monitor.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Схема метрик и журналов диагностики в учетной записи хранения

После настройки сбора метрик и журналов диагностики в выбранной учетной записи хранения создается контейнер хранилища, как только первые строки данных станут доступными. Вот как выглядит структура большого двоичного объекта:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Или даже еще проще:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Например, большой двоичный объект метрики 1 минуты может иметь такое имя:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Если требуется записывать данные из эластичного пула, имя большого двоичного объекта немного отличается:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Загрузка метрик и журналов из службы хранилища Azure

Ознакомьтесь с разделом [Скачивание больших двоичных объектов](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="1-minute-metrics"></a>Метрики 1 минуты

| |  |
|---|---|
|**Ресурс**|**Метрики**|
|База данных|Сведения о проценте использования DTU, используемых единицах DTU, ограничении DTU, проценте использования ЦП, проценте чтения физических данных, проценте записей в журнал, проценте успешных, неудачных или заблокированных подключений брандмауэра, проценте сеансов, проценте рабочих ролей, хранилище, проценте хранилища, проценте хранилища XTP, взаимоблокировках |
|Эластичный пул|Сведения о проценте использования DTU, используемых единицах DTU, ограничении DTU, проценте использования ЦП, проценте чтения физических данных, проценте записей в журнал, проценте сеансов, проценте рабочих ролей, хранилище, проценте хранилища, ограничении хранилища, проценте хранилища XTP |
|||

## <a name="next-steps"></a>Дальнейшие действия

- Прочитайте статьи [Обзор метрик в Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) и [Сбор и использование диагностических данных из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), чтобы не только научиться включать ведение журнала, но и узнать, какие категории журналов поддерживаются различными службами Azure.
- Прочтите эти статьи, чтобы узнать о концентраторах событий:
   - [Что такое концентраторы событий Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Начало работы с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Ознакомьтесь с разделом [Скачивание больших двоичных объектов](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

