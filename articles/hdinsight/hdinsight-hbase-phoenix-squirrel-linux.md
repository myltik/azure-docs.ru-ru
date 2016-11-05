---
title: Использование Apache Phoenix и SQuirreL в HDInsight | Microsoft Docs
description: Узнайте о том, как использовать Apache Phoenix в HDInsight, а также как установить и настроить SQuirreL на рабочей станции для подключения к кластеру HBase в HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Использование Apache Phoenix с кластерами HBase под управлением Linux в HDInsight
Узнайте, как использовать [Apache Phoenix](http://phoenix.apache.org/) в HDInsight, а также как использовать SQLLine. Дополнительные сведения о Phoenix см. в разделе [Phoenix за 15 минут или меньше](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Для информации по грамматике Phoenix обратитесь к разделу [Грамматика Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Для получения информации о версии Phoenix в HDInsight см. раздел [Новые возможности версий кластера Hadoop, предоставляемых HDInsight][hdinsight-versions].
> 
> 

## Использование SQLLine
[SQLLine](http://sqlline.sourceforge.net/) — это утилита командной строки для выполнения SQL.

### Предварительные требования
Перед использованием SQLLine необходимо иметь следующее:

* **Кластер HBase в HDInsight**. Сведения о подготовке кластера HBase см. в разделе [Начало работы с Apache HBase в HDInsight][hdinsight-hbase-get-started].
* **Подключение к кластеру с помощью протокола удаленного рабочего стола**. Инструкции см. в статье [Управление кластерами Hadoop в HDInsight с помощью классического портала Azure][hdinsight-manage-portal].

При подключении к кластеру HBase необходимо подключиться к одному из Zookeeper. Каждый кластер HDInsight содержит 3 Zookeeper.

**Определение имени узла Zookeeper**

1. Откройте Ambari, перейдя по адресу **https://<имя\_кластера>.azurehdinsight.net/**.
2. Введите имя пользователя (кластера) HTTP и пароль для входа.
3. В меню слева щелкните **Zookeeper**. Вы увидите 3 сервера **ZooKeeper Server**.
4. Выберите один из серверов **ZooKeeper Server**. На панели сводки найдите **Имя узла**. Оно аналогично *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Использование SQLLine**

1. Подключитесь к кластеру с помощью SSH. Инструкции см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md) или [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md). Выбор статьи зависит от операционной системы клиентского компьютера.
2. Из SSH выполните следующие команды для запуска SQLLine.
   
        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure
3. Выполните следующие команды, чтобы создать таблицу HBase и вставить некоторые данные.
   
        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
   
        !tables
   
        UPSERT INTO Company VALUES(1, 'Microsoft');
   
        SELECT * FROM Company;
   
        !quit

Для получения дополнительных сведений обратитесь к разделу [Руководство по SQLLine](http://sqlline.sourceforge.net/#manual) и [Грамматика Phoenix](http://phoenix.apache.org/language/index.html).

## Дальнейшие действия
В этой статье вы изучили использование Apache Phoenix в HDInsight. Дополнительные сведения см. на следующих ресурсах:

* [Обзор HDInsight HBase][hdinsight-hbase-overview]\: HBase — это NoSQL (нереляционная распределенная) база данных с открытым исходным кодом Apache, разработанная в рамках проекта Hadoop, которая обеспечивает произвольный доступ и строгую согласованность больших объемов неструктурированных и полуструктурированных данных.
* [Подготовка кластеров HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet]\: благодаря интеграции виртуальной сети кластеры HBase могут быть развернуты в той же виртуальной сети, что и приложения, поэтому эти приложения могут напрямую обмениваться данными с HBase.
* [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md): узнайте, как настроить репликацию HBase между двумя центрами обработки данных Azure.
* [Анализ мнений Twitter с помощью HBase в HDInsight][hbase-twitter-sentiment]\: узнайте, как выполнять [анализ мнений](http://en.wikipedia.org/wiki/Sentiment_analysis) в режиме реального времени на основе больших данных, используя HBase в кластере Hadoop в HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
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




<!---HONumber=AcomDC_0914_2016-->