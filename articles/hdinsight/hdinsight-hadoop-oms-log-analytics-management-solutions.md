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
ms.date: 11/08/2017
ms.author: nitinme
ms.openlocfilehash: dc959f763e9a84199130bae845cb62c493676977
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics"></a>Добавление решений по управлению кластерами HDInsight в Log Analytics

HDInsight предоставляет решения по управлению для кластеров, которые можно добавлять в Azure Log Analytics. [Решения по управлению](../log-analytics/log-analytics-add-solutions.md) добавляют функциональные возможности в OMS, предоставляя тем самым дополнительные средства анализа и работы с данными в Log Analytics. Эти решения собирают важные метрики производительности из кластеров HDInsight и предоставляют средства для поиска метрик. Кроме того, они позволяют использовать визуализации и панели мониторинга для большинства типов кластеров, которые поддерживаются в HDInsight. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения. 

Из этой статьи вы узнаете, как добавлять решения по управлению для кластеров в рабочую область OMS.

## <a name="prerequisites"></a>Предварительные требования

* Для использования Log Analytics необходимо настроить кластер HDInsight. Инструкции см. в статье [Use Azure Log Analytics to monitor HDInsight clusters (Preview)](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)).

## <a name="add-cluster-specific-management-solutions"></a>Добавление решений по управлению кластерами

В этом разделе мы добавим решение по управлению кластерами HBase в существующую рабочую область OMS.

1. Откройте кластер HDInsigt на портале Azure, выберите **Мониторинг**, затем нажмите кнопку **Открыть панель мониторинга OMS**.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Открытие панели мониторинга OMS")

1. На панели мониторинга OMS щелкните **Коллекции решений** или значок **конструктора представлений** в области слева.

    ![Добавление решения по управлению в OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Добавление решения по управлению в OMS")

2. В коллекции решений выберите одну из плиток:

    - Мониторинг HDInsight Hadoop
    - Мониторинг HDInsight HBase (предварительная версия)
    - Мониторинг Kafka HDInsight
    - Мониторинг HDInsight Storm
    - Мониторинг HDInsight Spark

3. На следующем экране нажмите кнопку **Добавить**.  На следующем рисунке показана кнопка "Добавить" для мониторинга HBase.

     ![Добавление решения по управлению HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Добавление решения по управлению HBase")

4. На панели мониторинга OMS должна отобразиться плитка решения по управлению HBase. Если кластер, связанный с OMS (в рамках предварительных требований для этой статьи), является кластером HBase, на плитке отобразятся имя кластера и число связанных узлов.

    ![Добавленное решение по управлению HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Добавленное решение по управлению HBase")

## <a name="next-steps"></a>Дальнейшие действия

* [Запрос в Azure Log Analytics для мониторинга кластеров HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>См. также

* [Работа с OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Работа с правилами генерации оповещений в Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
