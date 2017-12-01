---
title: "Использование Log Analytics для мониторинга кластеров Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как использовать Azure Log Analytics для мониторинга заданий, выполняемых в кластере HDInsight."
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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6677b0b3ed047ce011bfbb72c25e45195859830a
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Использование Azure Log Analytics для мониторинга кластеров HDInsight

Узнайте, как использовать Azure Log Analytics для мониторинга операций кластера Hadoop в HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) — это служба в [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников. 

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь со страницей [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free).

* **Кластер Azure HDInsight**. Сейчас Azure Operations Management Suite можно использовать со следующими типами кластеров HDInsight:

    * Hadoop
    * HBase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Рабочая область Log Analytics**. Рабочую область можно представить как уникальную среду Log Analytics с собственным репозиторием данных, источниками данных и решениями. У вас должна быть создана одна такая рабочая область, которую можно связать с кластерами Azure HDInsight. Инструкции см. в руководстве по [созданию рабочей области Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-log-analytics"></a>Настройка кластера HDInsight для использования Log Analytics

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. Откройте кластер HDInsight на портале Azure.
2. В области слева выберите **Мониторинг**.
3. В области справа щелкните **Включить** и выберите существующую рабочую область Log Analytics. Затем нажмите кнопку **Сохранить**.

    ![Включение мониторинга кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Включение мониторинга кластеров HDInsight")

    Параметр сохраняется в течение нескольких секунд.  Затем вы увидите кнопку **Открыть панель мониторинга OMS** в верхней части страницы. 

    ![Открытие панели мониторинга Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Open OMS dashboard")

5. Нажмите кнопку **Открыть панель мониторинга OMS**.
6. При появлении запроса введите свои учетные данные Azure.

    ![Портал Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Портал Operations Management Suite")

## <a name="next-steps"></a>Дальнейшие действия
* [Добавление решений по управлению кластерами HDInsight в Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
