---
title: "Добавление решений по управлению кластерами HDInsight в Log Analytics | Документация Майкрософт"
description: "Узнайте, как использовать Azure Log Analytics, чтобы создавать пользовательские представления для кластеров HDInsight."
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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Добавление решений по управлению кластерами HDInsight в Log Analytics (предварительная версия)

HDInsight предоставляет решения по управлению для кластеров, которые можно добавлять в Azure Log Analytics. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) добавляют функциональные возможности в OMS, предоставляя тем самым дополнительные средства анализа и работы с данными в Log Analytics. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения. 

Из этой статьи вы узнаете, как добавлять решения по управлению для кластеров в рабочую область OMS.

## <a name="prerequisites"></a>Предварительные требования

* Для использования Log Analytics необходимо настроить кластер HDInsight. Инструкции см. в статье [Use Azure Log Analytics to monitor HDInsight clusters (Preview)](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)).

## <a name="add-cluster-specific-management-solutions"></a>Добавление решений по управлению кластерами

В этом разделе мы добавим решение по управлению кластерами HBase в существующую рабочую область OMS. В ближайшее время будут реализованы аналогичные решения для других типов кластеров HDInsight.

1. Откройте панель мониторинга OMS. На портале Azure откройте колонку кластера HDInsight, которая связана с Azure Log Analytics, выберите вкладку "Мониторинг" и щелкните **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

1. На панели мониторинга OMS щелкните **Коллекции решений** или значок конструктора представлений в области слева.

    ![Добавление решения по управлению в OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Добавление решения по управлению в OMS")

2. В коллекции решений выберите плитку **Мониторинг HDInsight HBase**.

    ![Поиск решения по управлению HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "Поиск решения по управлению HBase")

3. На следующем экране нажмите кнопку **Добавить**.

     ![Добавление решения по управлению HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Добавление решения по управлению HBase")

4. На панели мониторинга OMS должна отобразиться плитка для решения по управлению HBase. Если кластер, связанный с OMS (в рамках предварительных требований для этой статьи), является кластером HBase, на плитке отобразятся имя кластера и число связанных узлов.

    ![Добавленное решение по управлению HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Добавленное решение по управлению HBase")

## <a name="next-steps"></a>Дальнейшие действия

* [Запрос в Azure Log Analytics для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>См. также

* [Работа с OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
