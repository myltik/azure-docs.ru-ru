---
title: "Заметки о выпуске для компонентов Hadoop в Azure HDInsight | Документация Майкрософт"
description: "Последние заметки о выпуске и версии компонентов Hadoop в Azure HDInsight. Советы по разработке и подробные сведения о Spark, R Server, Hive и т. д."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 782cab231e1b152c720abebff1fc76ae0559d12a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/12/2017


---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Заметки о выпуске для компонентов Hadoop в Azure HDInsight

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Дополнительные сведения см. в [статье об управлении версиями HDInsight](hdinsight-component-versioning.md).

## <a name="04062017---general-availability-of-hdinsight-36"></a>Общедоступная версия HDInsight 3.6 от 06.04.2017

* В этом выпуске повышена версия Azure HDInsight до 3.6, которая основана на HDP 2.6. Заметки о выпуске HDP 2.6 доступны [здесь](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html), дополнительные сведения о версиях HDInsight доступны [здесь](hdinsight-component-versioning.md). Версия HDInsight 3.6 доступна для следующих рабочих нагрузок:

    * Hadoop 2.7.3
    * HBase 1.1.2
    * Storm 1.1.0
    * Spark 2.1.0
    * Interactive Hive 2.1.0

* **Поддержка Hive View 2.0**. Это должно улучшить взаимодействие с пользователем в Interactive Hive. Дополнительные сведения см. в [документации по Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Повышение производительности с помощью Hive LLAP**. Дополнительные сведения см. в [документации Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Новые функции в Hive**. Дополнительные сведения см. в [документации Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Интерфейс командной строки Hive объявлен нерекомендуемым**: использовать интерфейс командной строки Hive не рекомендуется, вместо него пользователям предлагается использовать Beeline. Дополнительные сведения см. в [документации Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Инструкции по использованию Beeline с HDInsight см. в разделе [Использование Hive с Hadoop в HDInsight с применением Beeline](hdinsight-hadoop-use-hive-beeline.md).

* **Новые возможности в Apache Phoenix и HBase**.
    * Поддержка квоты хранилища: обычно используется в мультитенантных средах для ограничения емкости, выделяемой для хранения данных на уровне таблицы и пространства имен.
    * Улучшение индексирования Phoenix: создание добавочных индексов и перестроение индекса и возобновление индексирования после обнаружения ошибок.
    * Инструмент проверки целостности данных Phoenix: поддерживает проверку схемы, индекса и других метаданных.


* **Проблема с HBase**: при выполнении задания MapReduce для массовой передачи CSV-файлов приведенный ниже синтаксис может привести к ошибке.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Вместо этого используйте следующий синтаксис:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>Выпуск Spark 2.1 в HDInsight 3.6 (предварительная версия) от 28.02.2017
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) устраняет многие проблемы со стабильностью и удобством использования, имевшиеся в предыдущих версиях. Также добавлены новые функции для всех рабочих нагрузок Spark, включая Spark Core, SQL, машинное обучение и потоковую передачу.
* Улучшена масштабируемость структурированного потока и добавлена поддержка водяных знаков событий времени и соединителя Kafka 0.10.
* Секционирование Spark SQL теперь обрабатываются с помощью нового механизма масштабируемой обработки секций. Дополнительные сведения об установке новой версии см. [здесь](http://spark.apache.org/releases/spark-release-2-1-0.html).
* Spark 2.1 в Azure HDInsight 3.6 (предварительная версия) в настоящее время не поддерживает подключение инструментов бизнес-аналитики с помощью драйвера ODBC.
* В этой предварительной версии не поддерживается доступ к Azure Data Lake Store из кластеров Spark 2.1.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>Выпуск Spark 2.0.1 в HDInsight 3.5 от 18.11.2016
Выпуск Spark 2.0.1 теперь доступен в кластерах Spark (HDInsight версии 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>Выпуск R Server 9.0 в HDInsight 3.5 (Spark 2.0) от 16.11.2016
*   Кластеры R Server теперь включают параметры для двух версий: R Server 9.0 в HDI 3.5 (Spark 2.0) и R Server 8.0 в HDI 3.4 (Spark 1.6).
*   R Server 9.0 в HDI 3.5 (Spark 2.0) основан на R 3.3.2 и содержит новые функции источника данных ScaleR под названием RxHiveData и RxParquetData. Они позволяют передавать данные из Hive и Parquet напрямую в таблицы данных Spark для анализа с помощью ScaleR. Дополнительные сведения об этих функциях в R см. в интернет-справке. Ее можно вызвать, выполнив команды **?RxHiveData** и **?RxParquetData**.
*   Теперь выпуск RStudio Server Community Edition устанавливается по умолчанию (с параметром opt-out) в колонке "Настройка кластера" как часть потока подготовки.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>Выпуск Spark 2.0 в HDInsight от 09.11.2016
* Кластеры Spark 2.0 в HDInsight 3.5 теперь поддерживают службы Livy и Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>Выпуск R Server в HDInsight от 26.10.2016
* Универсальный код ресурса (URI), используемый для доступа к граничному узлу, теперь имеет формат **имя_кластера**-ed-ssh.azurehdinsight.net.
* Подготовка кластеров R Server в HDInsight упрощена.
* R Server в HDInsight теперь доступен как обычный тип кластера HDInsight "R Server" и больше не устанавливается как отдельное приложение HDInsight. Теперь граничный узел и двоичные файлы R Server подготавливаются в рамках развертывания кластера R Server. Это ускоряет подготовку и повышает ее надежность. Модель ценообразования для R Server обновлена соответствующим образом.
* Стоимость кластера R Server теперь объединяет цену уровня "Стандартный" и доплату за R Server. Уровень "Премиум" зарезервирован для функций этого уровня, доступных для других типов кластеров, и не используется для кластеров R Server. Это изменение не влияет на действующие цены для R Server. Оно влияет только на то, как платежи будут представлены в счете. Все имеющиеся кластеры R Server по-прежнему поддерживаются, а шаблоны Resource Manager функционируют до уведомления об устаревании. **Мы советуем обновить скрипты развертывания для использования нового шаблона Resource Manager.**

## <a name="08302016---release-of-r-server-on-hdinsight"></a>Выпуск R Server в HDInsight от 30.08.2016
Полные номера версий кластеров HDInsight под управлением Linux для данного выпуска:

| HDI | Версия кластера HDI | HDP | Сборка HDP | Сборка Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Полные номера версий кластеров HDInsight под управлением Windows для данного выпуска:

| HDI | Версия кластера HDI | HDP | Сборка HDP |
| --- | --- | --- | --- |
| 2,1 |2.1.10.1033.2559206 |1,3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2,0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2,1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |






