---
title: "Документация по Azure HDInsight. Руководство по устранению неполадок | Документация Майкрософт"
description: "В документации с пошаговыми инструкциями по устранению неполадок рабочих нагрузок Hadoop в HDInsight показано, как устранять распространенные неполадки с Hive, Spark, HBase, Storm и Kafka в HDInsight."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 9313f0713818f80bae99c32a438d97ba544a7a6b
ms.contentlocale: ru-ru
ms.lasthandoff: 08/17/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Устранение неполадок Azure HDInsight

| Рабочая нагрузка Apache | Основные вопросы |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Устранение неполадок с HBase](hdinsight-troubleshoot-hbase.md)|<br>[Как запускать отчеты о командах hbck с несколькими неназначенными регионами?](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Как устранить проблемы с временем ожидания в командах hbck для назначения регионов?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Как принудительно отключить безопасный режим HDFS в кластере?](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[Как устранить проблемы с подключением JDBC или sqlline к Apache Phoenix?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Что вызывает сбой запуска главного сервера?](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Что вызывает сбой перезапуска на региональном сервере?](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Устранение неполадок с HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Как получить доступ к локальной системе HDFS изнутри кластера?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Как принудительно отключить безопасный режим HDFS в кластере?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Устранение неполадок с Hive](hdinsight-troubleshoot-hive.md)|<br>[Как экспортировать хранилище метаданных Hive и импортировать его в другой кластер?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Как найти журналы Hive в кластере?](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[Как запустить оболочку Hive с определенными конфигурациями в кластере?](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Как анализировать данные направленного ациклического графа Tez по критическому пути кластера?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Как скачать данные направленного ациклического графа Tez из кластера?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Устранение неполадок со Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Как настроить приложение Spark с помощью Ambari в кластерах?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[Как настроить приложение Spark с помощью записной книжки Jupyter в кластерах?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[Как настроить приложение Spark с помощью LIVY в кластерах?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[Как настроить приложение Spark с помощью spark-submit в кластерах?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[Что вызывает в приложении Spark исключение OutOfMemoryError?](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Устранение неполадок со Storm](hdinsight-troubleshoot-STORM.md)|<br>[Как получить доступ к пользовательскому интерфейсу Storm в кластере?](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[Как передать сведения о контрольной точке spout концентратора событий Storm из одной топологии в другую?](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Как найти двоичные файлы Storm в кластере?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Как определить топологию развертывания кластера Storm?](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Как найти двоичные файлы Storm-EventHub-Spout для разработки?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[Как найти файлы конфигурации Storm Log4J в кластерах?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Устранение неполадок с YARN](hdinsight-troubleshoot-YARN.md)|<br>[Как создать новую очередь Yarn в кластере?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Как скачать журналы Yarn из кластера?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Ресурсы по устранению неполадок HDInsight
Дополнительную справочную информацию для устранения неполадок см. в следующих статьях.

| Тема | Справочные статьи |
| --- | --- |
| HDInsight в Linux и оптимизация | [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md)<br>[Hadoop: память и производительность](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Производительность запросов Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Журналы и дампы | [Доступ к журналам приложений YARN Hadoop в Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[Включение дампов кучи для служб Hadoop](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| Ошибки | [Понимание и устранение ошибок WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[Исправление ошибки "нехватка памяти" с помощью параметров Hive](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Средства | [Отладка заданий Tez с помощью представлений Ambari](hdinsight-debug-ambari-tez-view.md)<br>[Оптимизация запросов Hive](hdinsight-hadoop-optimize-hive-query.md) |



