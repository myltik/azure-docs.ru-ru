---
title: Запрос Azure Log Analytics для мониторинга кластеров Azure HDInsight | Документация Майкрософт
description: Узнайте, как выполнять запросы в Azure Log Analytics для мониторинга заданий, выполняемых в кластере HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 61467d702f3123085fd7e067a8d56c30331c5bc6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401104"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Запрос в Azure Log Analytics для мониторинга кластеров HDInsight

Ознакомьтесь с несколькими базовыми сценариями использования Azure Log Analytics для мониторинга кластеров Azure HDInsight:

* [Анализ метрик кластера HDInsight](#analyze-hdinsight-cluster-metrics).
* [Поиск определенных сообщений журнала](#search-for-specific-log-messages).
* [Создание оповещений о событиях](#create-alerts-for-tracking-events).

## <a name="prerequisites"></a>предварительным требованиям

* Для использования Log Analytics необходимо настроить кластер HDInsight. Инструкции см. в статье [Use Azure Log Analytics to monitor HDInsight clusters (Preview)](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)).

* Необходимо добавить решение по управлению, связанное с кластером HDInsight, в рабочую область [Log Analytics](../operations-management-suite/operations-management-suite-overview.md) согласно инструкциям в статье [Добавление решений по управлению кластерами HDInsight в Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Анализ метрик кластера HDInsight

Узнайте, как выполнить поиск определенных метрик для кластера HDInsight.

1. Откройте кластер HDInsight, связанный с Azure Log Analytics, на портале Azure.
2. Щелкните **Мониторинг**, а затем — **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. Выберите **Поиск по журналу** в меню слева.

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. В поле поиска всех метрик введите следующий запрос для поиска всех доступных метрик для всех кластеров HDInsight, настроенных для использования Azure Log Analytics, а затем нажмите клавишу **ВВОД**.

        `search *` 

    ![Поиск всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск всех метрик")

    Выходные данные должны выглядеть так:

    ![Выходные данные поиска всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Выходные данные поиска всех метрик")

5. В левой области в категории **Тип** выберите метрику, которую необходимо изучить подробнее, и нажмите кнопку **Применить**. На следующем снимке экрана показано, что выбран тип `metrics_resourcemanager_queue_root_default_CL`. 

    > [!NOTE]
    > Чтобы найти необходимую метрику, может потребоваться нажать кнопку **[+] Дополнительно**. Кроме того, кнопка **Применить** находится в нижней части списка, поэтому нужно прокрутить вниз, чтобы увидеть ее.
    > 
    >    

    Обратите внимание, что запрос в текстовом поле изменяется на тот, который показан в выделенном поле на следующем снимке экрана:

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

6. Можно получить более подробную информацию о конкретной метрике. Например, можно уточнить существующие выходные данные по среднему количеству ресурсов, использованных за 10-минутный интервал, классифицировав их по имени кластера с помощью следующего запроса:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального использования ресурсов в 10-минутном интервале (а также 90-го и 95-го процентиля):

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Поиск определенных сообщений журнала

Узнайте, как искать сообщения об ошибках за определенный интервал времени. Эти шаги являются всего лишь одним примером того, как можно получить сообщение об ошибке, которое вас интересует. Для поиска нужных ошибок можно использовать любое доступное свойство.

1. Откройте кластер HDInsight, связанный с Azure Log Analytics, на портале Azure.
2. Щелкните **Мониторинг**, а затем **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. На начальном экране портала OMS щелкните **Поиск по журналам**.

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. Введите следующий запрос для поиска всех сообщений об ошибках для всех кластеров HDInsight, настроенных для использования Azure Log Analytics, а затем нажмите клавишу **ВВОД**. 

         search "Error"

    Должны отобразиться выходные данные, аналогичные указанным ниже.

    ![Выходные данные поиска всех ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Выходные данные поиска всех ошибок")

5. В левой области в категории **Тип** найдите тип ошибки, который необходимо изучить подробнее, и нажмите кнопку **Применить**.  Обратите внимание, что результаты уточнены и показывают только ошибки выбранного типа.
7. Можно получить более подробную информацию о конкретном списке ошибок, используя параметры, доступные на панели слева. Например, 

    - чтобы просмотреть сообщения об ошибках из определенного рабочего узла, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Выходные данные поиска конкретных ошибок")

    - Чтобы просмотреть ошибку, которая возникла в определенное время, сделайте следующее:

        ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Выходные данные поиска конкретных ошибок")

9. Чтобы просмотреть определенную ошибку, сделайте следующее. Щелкните **[+]show more** ([+] больше), чтобы просмотреть фактическое сообщение об ошибке.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Выходные данные поиска конкретных ошибок")

## <a name="create-alerts-for-tracking-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для простоты воспользуемся следующим запросом, который предоставляет список приложений, в которых возник сбой, запущенных на кластерах HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Для создания оповещения можно использовать любой запрос.

1. Откройте кластер HDInsight, связанный с Azure Log Analytics, на портале Azure.
2. Щелкните **Мониторинг**, а затем — **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. На начальном экране портала OMS щелкните **Поиск по журналам**.

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. Выполните следующий запрос, на основе которого требуется создать оповещение, а затем нажмите клавишу **ВВОД**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. В верхней части страницы щелкните **Оповещение**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ввод запроса для создания оповещения")

4. В окне **Добавить правило оповещения** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Сохранить**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ввод запроса для создания оповещения")

    На снимке экрана показана конфигурация для отправки уведомления по электронной почте, когда запрос оповещения вернет выходные данные.

5. Имеющееся оповещение можно изменить или удалить. Для этого на любой странице портала OMS щелкните значок **Параметры**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Ввод запроса для создания оповещения")

6. На странице **Параметры** щелкните **Оповещения**, чтобы просмотреть созданные оповещения. Оповещения также можно отключить, изменить или удалить. Дополнительные сведения см. в статье [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>См. также

* [Работа с Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
