---
title: "Запрос Azure Log Analytics для мониторинга кластеров Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как выполнять запросы в Azure Log Analytics для мониторинга заданий, выполняемых в кластере HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Запрос Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)

В этой статье рассматриваются несколько сценариев использования Azure Log Analytics с кластерами Azure HDInsight. Ниже приводятся три самых распространенных сценария:

* анализ метрик кластера HDInsight в OMS;
* поиск конкретных сообщений журнала для кластеров HDInsight;
* создание оповещений на основе событий, происходящих в кластерах.

## <a name="prerequisites"></a>Предварительные требования

* Для использования Log Analytics необходимо настроить кластер HDInsight. Инструкции см. в статье [Use Azure Log Analytics to monitor HDInsight clusters (Preview)](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)).

* Необходимо добавить решение по управлению, связанное с кластером HDInsight, в рабочую область OMS согласно инструкциям в статье [Add HDInsight cluster management solutions to Log Analytics (Preview)](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Добавление решений по управлению, связанных с кластером HDInsight, в Log Analytics (предварительная версия)).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Анализ метрик кластера HDInsight в OMS

В этом разделе описаны шаги для поиска конкретных метрик в кластере HDInsight.

1. Откройте панель мониторинга OMS. На портале Azure откройте колонку кластера HDInsight, которая связана с Azure Log Analytics, выберите вкладку "Мониторинг" и щелкните **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. На панели мониторинга OMS на начальном экране щелкните **Log Search** (Поиск по журналам).

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. В окне поиска по журналам в текстовом поле **Begin search here** (Начать поиск) введите `*`, чтобы найти все доступные метрики для всех кластеров HDInsight, настроенных для использования Azure Log Analytics. Нажмите клавишу ВВОД.

    ![Поиск всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Поиск всех метрик")

4. Вы должны увидеть подобные выходные данные.

    ![Выходные данные поиска всех метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Выходные данные поиска всех метрик")

5. В левой области в категории **Тип** найдите метрику, которую необходимо изучить подробнее. В данном руководстве мы выберем `metrics_resourcemanager_queue_root_default_CL`. Установите флажок нужной метрики и нажмите кнопку **Применить**.

    > [!NOTE]
    > Чтобы найти необходимую метрику, может потребоваться щелкнуть **[+] Дополнительно**. Кроме того, кнопка **Применить** находится в нижней части списка, поэтому нужно прокрутить вниз, чтобы увидеть ее.
    > 
    >    
    Обратите внимание, что запрос в текстовом поле теперь изменился на тот, который показан в выделенном поле на следующем снимке экрана:

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Поиск конкретных метрик")

6. Теперь можно получить более подробную информацию о конкретной метрике. Например, можно уточнить имеющиеся выходные данные по среднему количеству ресурсов, использованных за 10-минутный интервал, классифицировав их по имени кластера. Введите следующий запрос в текстовом поле запроса.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Поиск конкретных метрик")

7. Вместо уточнения по среднему количеству используемых ресурсов можно использовать следующий запрос, чтобы уточнить результаты на основе максимального (а также 90-го и 95-го процентиля) использования ресурсов в 10-минутном интервале.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Поиск конкретных метрик](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Поиск конкретных метрик")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Поиск конкретных сообщений журнала в кластерах HDInsight

В этом разделе описаны шаги для поиска сообщений об ошибках в определенном временном окне. Эти шаги являются всего лишь одним примером того, как можно получить сообщение об ошибке, которое вас интересует. Для поиска нужных ошибок можно использовать любое доступное свойство.

1. Откройте панель мониторинга OMS. На портале Azure откройте колонку кластера HDInsight, которая связана с Azure Log Analytics, выберите вкладку "Мониторинг" и щелкните **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. На панели мониторинга OMS на начальном экране щелкните **Log Search** (Поиск по журналам).

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. В окне поиска по журналам в текстовом поле **Begin search here** (Начать поиск) введите `"Error"` (с кавычками), чтобы найти все сообщения об ошибках для всех кластеров HDInsight, настроенных для использования Azure Log Analytics. Нажмите клавишу ВВОД.

    ![Поиск всех ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Поиск всех ошибок")

4. Вы должны увидеть подобные выходные данные.

    ![Выходные данные поиска всех ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Выходные данные поиска всех ошибок")

5. В левой области в категории **Тип** найдите тип ошибки, который необходимо изучить подробнее. В данном руководстве мы выберем `log_sparkappsexecutors_CL`. Установите флажок нужной метрики и нажмите кнопку **Применить**.

    ![Поиск конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Поиск конкретных ошибок")

        
6. Обратите внимание, что запрос в текстовом поле теперь изменился на тот, который показан в выделенном поле ниже, а результаты уточнены и показывают только ошибку выбранного типа.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Выходные данные поиска конкретных ошибок")

7. Теперь можно получить более подробную информацию о конкретном списке ошибок, используя параметры, доступные на панели слева. Например, можно уточнить запрос, чтобы отобразить только сообщения об ошибках конкретного рабочего узла.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Выходные данные поиска конкретных ошибок")

8. Можно также указать момент времени, когда предположительно произошла ошибка, выбрав соответствующее значение времени на левой панели.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Выходные данные поиска конкретных ошибок")

9. Теперь выберите конкретную ошибку, которую вы ищете. Щелкните **[+]show more** ([+] больше), чтобы просмотреть фактическое сообщение об ошибке.

    ![Выходные данные поиска конкретных ошибок](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Выходные данные поиска конкретных ошибок")

## <a name="create-alerts-to-track-events"></a>Создание оповещений для отслеживания событий

Первым шагом создания оповещения является получение запроса, на основе которого активируется оповещение. Для простоты воспользуемся следующим запросом, который предоставляет список приложений, в которых возник сбой, запущенных на кластерах HDInsight.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

Для создания оповещения можно использовать любой запрос.

1. Откройте панель мониторинга OMS. На портале Azure откройте колонку кластера HDInsight, которая связана с Azure Log Analytics, выберите вкладку "Мониторинг" и щелкните **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

2. На панели мониторинга OMS на начальном экране щелкните **Log Search** (Поиск по журналам).

    ![Открытие поиска по журналам](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Открытие поиска по журналам")

3. В окне поиска по журналам в текстовом поле **Begin search here** (Начать поиск) вставьте запрос, на основе которого нужно создать оповещение, нажмите клавишу ВВОД, а затем щелкните **Оповещение**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Ввод запроса для создания оповещения")

4. В окне **Добавить правило оповещения** введите запрос и другие сведения, чтобы создать оповещение, а затем щелкните **Сохранить**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Ввод запроса для создания оповещения")

    В соответствии с настройками на этом снимке экрана электронное уведомление отправляется, только если запрос на оповещение получает выходные данные.

5. Имеющееся оповещение можно изменить или удалить. Для этого на любой странице портала OMS щелкните значок **Параметры**.

    ![Ввод запроса для создания оповещения](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Ввод запроса для создания оповещения")

6. На странице **Параметры** щелкните **Оповещения**, чтобы просмотреть созданные оповещения. Оповещения также можно отключить, изменить или удалить. Дополнительные сведения см. в статье [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>См. также

* [Работа с OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
