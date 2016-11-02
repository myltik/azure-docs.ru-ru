<properties
    pageTitle="Решение для анализа сетей Azure в Log Analytics | Microsoft Azure"
    description="Решение для анализа сетей Azure можно использовать в Log Analytics для просмотра журналов групп безопасности сети Azure и шлюза приложений Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>


# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Решение для анализа сетей Azure (предварительная версия) в Log Analytics

>[AZURE.NOTE] Это [предварительная версия решения](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Решения для анализа сетей Azure можно использовать в Log Analytics для просмотра журналов шлюза приложений Azure и групп безопасности сети Azure.

Для журналов шлюза приложений Azure и сетевых групп безопасности Azure можно включить ведение журналов. Эти журналы записываются в хранилище BLOB-объектов, где их может проиндексировать Log Analytics для поиска и анализа.

Шлюзы приложений поддерживают следующие журналы:

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

Группы безопасности сети поддерживают следующие журналы:

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Установка и настройка решения

Установите и настройте решение для анализа сетей Azure, выполнив следующие указания:

1.  Включите ведение журнала диагностики для ресурсов, для которых требуется выполнять мониторинг:
  + [Шлюз приложений](../application-gateway/application-gateway-diagnostics.md)
  + [группа безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md).
2.  Настройте чтение журналов из хранилища BLOB-объектов в Log Analytics, как описано в [JSON-файлах в хранилище BLOB-объектов ](../log-analytics/log-analytics-azure-storage-json.md).
3.  Включите решение для анализа сетей Azure, как описано в статье [Добавление решений Log Analytics из каталога решений](log-analytics-add-solutions.md).  

Если не включить ведение журналов диагностики для определенного типа ресурсов, колонки панели мониторинга для этого ресурса будут пустыми.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Просмотр сведений о сборе данных анализа сетей Azure

Решение для анализа сетей Azure собирает журналы диагностики из хранилища BLOB-объектов для шлюзов приложений и групп безопасности сети Azure.
Для сбора данных агент не требуется.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для анализа сетей Azure.

| Платформа | Direct Agent | Агент Systems Center Operations Manager (SCOM) | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | Частота сбора |
|---|---|---|---|---|---|---|
|Таблицы Azure|![Нет](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Нет](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Да](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![Нет](./media/log-analytics-azure-networking/oms-bullet-red.png)|![Нет](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10 минут|

## <a name="use-azure-networking-analytics"></a>Использование анализа сетей Azure

После установки решения можно просмотреть сводку ошибок клиента и сервера для отслеживаемых шлюзов приложений на плитке **Azure Networking Analytics** (Анализ сетей Azure) на странице **Обзор** в Log Analytics.

![изображение плитки Azure Networking Analytics (Анализ сетей Azure)](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Выбрав плитку **Обзор**, можно просмотреть сводные данные журналов и подробные сведения по следующим категориям:

+ Журналы доступа к шлюзу приложений:
  - ошибки клиента и сервера для журналов доступа шлюза приложений;
  - количество запросов в час для каждого шлюза приложений;
  - количество неудачных запросов в час для каждого шлюза приложений;
  - ошибки агентов пользователя для шлюзов приложений.
+ Производительность шлюза приложений:
  - работоспособность узла для шлюза приложений;
  - максимальное количество и 95-й процентиль для неудачных запросов шлюза приложений.
+ Заблокированные потоки группы безопасности сети:
  - правила группы безопасности сети с заблокированными потоками;
  - MAC-адреса c заблокированными потоками.
+ Разрешенные потоки группы безопасности сети:
  - правила группы безопасности сети с разрешенными потоками;
  - MAC-адреса c разрешенными потоками.


![изображение панели мониторинга анализа сетей Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![изображение панели мониторинга анализа сетей Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![изображение панели мониторинга анализа сетей Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![изображение панели мониторинга анализа сетей Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Просмотр сведений по сводке журнала

1. На странице **Обзор** щелкните плитку **Azure Networking Analytics** (Анализ сетей Azure).
2. На панели мониторинга **Azure Networking Analytics** (Анализ сетей Azure) просмотрите сводные данные в одной из колонок, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.

    На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.

## <a name="next-steps"></a>Дальнейшие действия

- Используйте [поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных анализа сетей Azure.



<!--HONumber=Oct16_HO2-->


