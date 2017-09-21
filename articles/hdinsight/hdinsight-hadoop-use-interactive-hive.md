---
title: "Использование Interactive Hive с Azure HDInsight | Документация Майкрософт"
description: "Сведения об использовании Interactive Hive (Hive LLAP) с HDInsight."
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>Использование Interactive Hive с HDInsight (предварительная версия)
Interactive Hive (также называется Hive LLAP, [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) — это новый [тип кластера](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. Interactive Hive обеспечивает кэширование в памяти, благодаря чему запросы Hive становятся более быстрыми и интерактивными. 

Кластер Interactive Hive отличается от кластера Hadoop. Он содержит только службу Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie и другие службы скоро будут удалены из кластера этого типа.
> Получить доступ к службе Hive в кластере Interactive Hive можно только с помощью представления Ambari Hive, Beeline и драйвера Microsoft Hive ODBC. Служба недоступна при использовании консоли Hive, Templeton, программы командной строки Azure (Azure CLI) или Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Создание кластера Interactive Hive
Вы можете использовать кластеры Interactive Hive только в кластерах под управлением Linux. Дополнительные сведения о создании кластеров HDInsight см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-queries-from-interactive-hive"></a>Выполнение запросов Hive из Interactive Hive
Есть несколько способов выполнять запросы Hive:

* Запуск Hive с помощью представления Ambari Hive.
  
    Сведения об использовании представления Hive см. в статье [Использование представления Hive с Hadoop в HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Запуск Hive с помощью Beeline.
  
    Дополнительные сведения об использовании Beeline в HDInsight см. в статье [Использование клиента Beeline с Apache Hive](hdinsight-hadoop-use-hive-beeline.md).
  
    Вы можете использовать Beeline на головном узле или пустом граничном узле. Рекомендуем использовать Beeline на пустом граничном узле. Дополнительные сведения о создании кластера HDInsight с пустым граничным узлом см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](hdinsight-apps-use-edge-node.md).
* Запуск Hive с помощью Hive ODBC.
  
    Дополнительные сведения об использовании Hive ODBC см. в статье [Подключение Excel к Hadoop в Azure HDInsight с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).

Чтобы найти строку подключения Java Database Connectivity (JDBC):

1. Войдите в Ambari, используя следующий URL-адрес: https://\<имя_кластера\>.AzureHDInsight.net.
2. В меню слева выберите **Hive**.
3. Чтобы скопировать URL-адрес, щелкните значок буфера обмена:
   
   ![JDBC для HDInsight Hadoop типа Interactive Hive на LLAP](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [создавать кластеры Interactive Hive в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Узнайте, как [использовать Beeline для отправки запросов Hive в HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Узнайте, как [подключить Excel к Hadoop с помощью драйвера Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).


