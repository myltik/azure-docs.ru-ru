---
title: "Использование Apache Phoenix и SQuirreL c HBase в Azure HDInsight | Документы Майкрософт"
description: "Узнайте о том, как использовать Apache Phoenix в HDInsight, а также как установить и настроить SQuirreL на рабочей станции для подключения к кластеру HBase в HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/26/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 13d17083bbe26fa9745ce4c5fef9f56859243c2e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/10/2017


---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Использование Apache Phoenix с кластерами HBase под управлением Linux в HDInsight
Узнайте, как использовать [Apache Phoenix](http://phoenix.apache.org/) в HDInsight, а также как использовать SQLLine. Дополнительные сведения о Phoenix см. в статье [Phoenix in 15 minutes or less](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html) (Phoenix за 15 минут или меньше). Информацию по грамматике Phoenix см. в разделе [Phoenix Grammar](http://phoenix.apache.org/language/index.html) (Грамматика Phoenix).

> [!NOTE]
> Сведения о версии Phoenix в HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Использование SQLLine
[SQLLine](http://sqlline.sourceforge.net/) — это программа командной строки для выполнения SQL.

### <a name="prerequisites"></a>Предварительные требования
Перед использованием SQLLine необходимо иметь следующее:

* **Кластер HBase в HDInsight.** Сведения о подготовке кластера HBase см. в статье [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Windows в HDInsight][hdinsight-hbase-get-started].
* **Подключение к кластеру с помощью протокола удаленного рабочего стола.** Инструкции см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure][hdinsight-manage-portal].

При подключении к кластеру HBase необходимо подключиться к одному из Zookeeper. Каждый кластер HDInsight содержит три узла Zookeeper.

**Определение имени узла Zookeeper**

1. Откройте Ambari, перейдя по адресу **https://<ClusterName>.azurehdinsight.net**.
2. Введите имя пользователя (кластера) HTTP и пароль для входа.
3. В меню слева щелкните **Zookeeper** . В списке будет три сервера **ZooKeeper Server**.
4. Выберите один из серверов **ZooKeeper Server** . На панели сводки найдите **Имя узла**. Оно аналогично *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Использование SQLLine**

1. Подключитесь к кластеру с помощью SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Из SSH выполните следующие команды для запуска SQLLine.

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure
3. Выполните следующие команды, чтобы создать таблицу HBase и вставить некоторые данные.

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Дополнительные сведения см. в разделе [SQLLine manual](http://sqlline.sourceforge.net/#manual) (Руководство по SQLLine) и [Phoenix Grammar](http://phoenix.apache.org/language/index.html) (Грамматика Phoenix).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы изучили использование Apache Phoenix в HDInsight.  Дополнительные сведения см. на следующих ресурсах:

* [Что такое HBase в HDInsight][hdinsight-hbase-overview]. HBase — это база данных NoSQL с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и частично структурированных данных.
* [Создание кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]. Благодаря интеграции виртуальной сети кластеры HBase можно развернуть в той же виртуальной сети, что и приложения, поэтому эти приложения могут напрямую обмениваться данными с HBase.
* [Настройка репликации HBase в HDInsight](hdinsight-hbase-replication.md): узнайте, как настроить репликацию HBase между двумя центрами обработки данных Azure.
* [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight][hbase-twitter-sentiment]. Узнайте, как выполнять [анализ тональности](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе больших данных, используя HBase в кластере Hadoop в HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png

