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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: dbd3d0ed4337d4fe86465c5c59bf20c0a50a87b4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Использование Azure Log Analytics для мониторинга кластеров HDInsight (предварительная версия)

Из этой статьи вы узнаете, как использовать Azure Log Analytics для мониторинга операций в кластерах HDInsight Hadoop.

Log Analytics — это служба в [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), которая отслеживает облачные и локальные среды, чтобы обеспечивать их доступность и производительность. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников. 

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Прежде чем приступать к изучению этого руководства, необходимо оформить подписку Azure. Ознакомьтесь со страницей [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/free).

* **Кластер Azure HDInsight**. Сейчас вы можете использовать Azure OMS со следующими типами кластеров HDInsight:
    * Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Interactive Hive

    Инструкции по созданию кластера HDInsight см. в руководстве по [началу работы с Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).


* **Рабочая область Log Analytics**. Рабочую область можно представить как уникальную среду Log Analytics с собственным репозиторием данных, источниками данных и решениями. У вас должна быть создана одна такая рабочая область, которую можно связать с кластерами Azure HDInsight. Инструкции см. в руководстве по [созданию рабочей области Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Настройка кластера HDInsight для использования Azure Log Analytics

В этом разделе мы настроим существующий кластер HDInsight Hadoop, чтобы использовать рабочую область Azure Log Analytics для мониторинга заданий, журналов отладки и пр.

1. На портале Azure в области слева щелкните **Кластеры HDInsight** и выберите имя кластера, который нужно настроить с использованием Azure Log Analytics.

2. В колонке кластера в области слева щелкните **Мониторинг**.

3. В области справа щелкните **Включить** и выберите существующую рабочую область Log Analytics. Щелкните **Сохранить**.

    ![Включение мониторинга кластеров HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Включение мониторинга кластеров HDInsight")

4. Когда кластер будет настроен для мониторинга в Log Analytics, в верхней части вкладки отобразится параметр **Открыть панель мониторинга OMS**. Нажмите кнопку.

    ![Открытие панели мониторинга OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Открытие панели мониторинга OMS")

5. При появлении запроса введите свои учетные данные Azure. Откроется панель мониторинга Microsoft OMS.

    ![Портал Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Портал Operations Management Suite")

## <a name="next-steps"></a>Дальнейшие действия
* [Добавление решений по управлению кластерами HDInsight в Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
