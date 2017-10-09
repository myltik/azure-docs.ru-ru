---
title: "Использование Interactive Query в Azure HDInsight | Документы Майкрософт"
description: "Сведения об использовании Interactive Query (Hive LLAP) в HDInsight."
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 17fac0327c3c454a8ea5c4d91b2d3e23de71f4ce
ms.contentlocale: ru-ru
ms.lasthandoff: 09/27/2017

---
# <a name="use-interactive-query-with-hdinsight"></a>Использование Interactive Query в HDInsight
Interactive Query (также Hive LLAP, или [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) — это новый [тип кластера](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. Interactive Query поддерживает кэширование в памяти, благодаря чему запросы Hive становятся более быстрыми и интерактивными. 

Кластер Interactive Query отличается от кластера Hadoop. Он содержит только службу Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie и другие службы скоро будут удалены из кластера этого типа.
> Получить доступ к службе Hive в кластере Interactive Query можно только с помощью представления Ambari Hive, Beeline и драйвера Microsoft Hive ODBC. Служба недоступна при использовании консоли Hive, Templeton, программы командной строки Azure (Azure CLI) или Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Создание кластера Interactive Query
Дополнительные сведения о создании кластера HDInsight см. в статье [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Выберите тип кластера Interactive Query.

## <a name="execute-hive-queries-from-interactive-query"></a>Выполнение запросов Hive из кластеров Interactive Query
Есть несколько способов выполнять запросы Hive:

* С помощью Power BI

    См. раздел [Визуализация больших данных с помощью Power BI в Azure HDInsight](./hdinsight-connect-hive-power-bi.md).

* Использование Zeppelin

    См. раздел [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./hdinsight-connect-hive-zeppelin.md).

* Использование Visual Studio

    См. раздел [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).

* С помощью Visual Studio Code

    См. раздел [Использование Visual Studio Code для Hive, LLAP и pySpark](hdinsight-for-vscode.md).
* Запуск Hive с помощью представления Ambari Hive.
  
    См. раздел [Использование представления Hive с Hadoop в Azure HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Запуск Hive с помощью Beeline.
  
    См. раздел [Использование Hive с Hadoop в HDInsight с помощью Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Вы можете использовать Beeline на головном узле или пустом граничном узле. Рекомендуем использовать Beeline на пустом граничном узле. Дополнительные сведения о создании кластера HDInsight с пустым граничным узлом см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](hdinsight-apps-use-edge-node.md).
* Запуск Hive с помощью Hive ODBC.
  
    См. раздел [Подключение Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).

Чтобы найти строку подключения Java Database Connectivity (JDBC):

1. Войдите в Ambari, используя следующий URL-адрес: https://\<имя_кластера\>.AzureHDInsight.net.
2. В меню слева выберите **Hive**.
3. Чтобы скопировать URL-адрес, щелкните значок буфера обмена:
   
   ![JDBC для кластеров HDInsight Hadoop типа Interactive Query на LLAP](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создавать кластеры Interactive Query в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Узнайте, как [визуализировать большие данные с помощью Power BI в Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* Узнайте, как [выполнять запросы Hive в Azure HDInsight с помощью Zeppelin](./hdinsight-connect-hive-zeppelin.md).
* Узнайте, как [выполнять запросы Hive с помощью Средств Data Lake для Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md#run-a-hive-query).
* Узнайте, как [использовать средствами HDInsight для Visual Studio Code](hdinsight-for-vscode.md).
* Узнайте, как [использовать представление Hive с Hadoop в HDInsight](hdinsight-hadoop-use-hive-ambari-view.md)
* Узнайте, как [использовать Beeline для отправки запросов Hive в HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Узнайте, как [подключить Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).


