---
title: Руководства по устранению неполадок с HDInsight | Документация Майкрософт
description: Устранение неполадок рабочих нагрузок Hadoop с помощью Azure HDInsight. В документации с пошаговыми инструкциями показано, как с помощью HDInsight устранять распространенные неполадки с Hive, Spark, YARN, HBase, HDFS и Storm.
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: ''
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 785884b7fd03339d195fbd7853b17300effecc6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984734"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Устранение неполадок при помощи Azure HDInsight

| Рабочая нагрузка Apache | Основные вопросы |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Устранение неполадок с HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Как запускать отчеты о командах hbck с несколькими неназначенными регионами?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Как устранить проблемы со временем ожидания в командах hbck для назначения регионов?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Как принудительно отключить безопасный режим HDFS в кластере?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Как устранить проблемы с подключением JDBC или sqlline к Apache Phoenix?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Что вызывает сбой запуска главного сервера?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Что вызывает сбой перезапуска на сервере региона?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Устранение неполадок c HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Как получить доступ к локальной системе HDFS в пределах кластера?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Как принудительно отключить безопасный режим HDFS в кластере?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Устранение неполадок с HBase](hdinsight-troubleshoot-hive.md)|<br>[Как экспортировать хранилище метаданных Hive и импортировать его в другой кластер?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Как найти журналы Hive в кластере?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Как запустить оболочку Hive с определенными конфигурациями в кластере?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Как анализировать данные направленного ациклического графа Tez по критическому пути кластера?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Как скачать данные направленного ациклического графа Tez из кластера?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Устранение неполадок со Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Как настроить приложение Spark с помощью Ambari в кластерах?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Как настроить приложение Spark с помощью Jupyter Notebook в кластерах?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Как настроить приложение Spark с помощью Livy в кластерах?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Как настроить приложение Spark с помощью spark-submit в кластерах?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[Что вызывает в приложении Spark исключение OutOfMemoryError?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Устранение неполадок со Storm](hdinsight-troubleshoot-STORM.md)|<br>[Как получить доступ к пользовательскому интерфейсу Storm в кластере](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Как передать сведения о контрольной точке spout концентратора событий Storm из одной топологии в другую](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Как найти двоичные файлы Storm в кластере](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Как определить топологию развертывания кластера Storm?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Как найти двоичные файлы объекта spout концентратора событий Storm для разработки](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Как найти файлы конфигурации Storm Log4J в кластерах?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Устранение неполадок с YARN](hdinsight-troubleshoot-YARN.md)|<br>[Как создать очередь YARN в кластере?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Как скачать журналы YARN из кластера?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Ресурсы по устранению неполадок HDInsight

| Тема | Справочные материалы |
| --- | --- |
| HDInsight в Linux и оптимизация | - [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md)<br>- [Указатель статей об устранении неполадок Hadoop в HDInsight](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Troubleshooting Hive query performance in HDInsight Hadoop cluster](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Устранение неполадок с производительностью запросов Hive в кластере HDInsight Hadoop) |
| Журналы и дампы | - [Доступ к журналам приложений YARN в HDInsight под управлением Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Включение дампов кучи для служб Hadoop в HDInsight под управлением Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Сведения об анализе журналов Azure HDInsight в этой статье](hdinsight-debug-jobs.md)|
| Errors | - [Понимание и устранение ошибок, полученных из WebHCat в HDInsight](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Устранение ошибки нехватки памяти Hive в Azure HDInsight](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Средства | - [Отладка заданий Tez в HDInsight с помощью представлений Ambari](hdinsight-debug-ambari-tez-view.md)<br>- [Оптимизация запросов Hive в Azure HDInsight](hdinsight-hadoop-optimize-hive-query.md) |
