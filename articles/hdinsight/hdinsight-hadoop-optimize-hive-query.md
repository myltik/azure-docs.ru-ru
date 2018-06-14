---
title: Оптимизация запросов Hive в Azure HDInsight | Документация Майкрософт
description: Узнайте, как оптимизировать запросы Hive для Hadoop в HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: e844b4f1e9898347da6af589dbfe41bf2ad3ab69
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199900"
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Оптимизация запросов Hive в Azure HDInsight

По умолчанию производительность кластеров Hadoop не оптимизирована. В этой статье описывается несколько наиболее распространенных методов оптимизации производительности Hive, которые можно применить к отправке запросов.

## <a name="scale-out-worker-nodes"></a>Горизонтальное масштабирование рабочих узлов

Увеличение числа рабочих узлов в кластере может дать возможность большему числу модулей сопоставления и сжатия работать параллельно. Горизонтальное масштабирование в HDInsight можно выполнить двумя способами:

* Во время подготовки можно указать количество рабочих узлов при помощи портала Azure, Azure PowerShell или кроссплатформенного интерфейса командной строки.  Дополнительные сведения см. в статье [Создание кластеров Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). На следующем снимке экрана показана рабочая конфигурация узла на портале Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* Во время выполнения можно масштабировать кластер без необходимости его повторного создания.

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Дополнительные сведения о разных виртуальных машинах, поддерживаемых HDInsight, см. на странице [цен на HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Включение Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) — это механизм выполнения, альтернативный механизму MapReduce:

![tez_1][image-hdi-optimize-hive-tez_1]

Tez работает быстрее, так как:

* **Выполняет задания, представленные в виде направленных ациклических графов (DAG), как единое целое при работе с платформой MapReduce.** DAG требует, чтобы набору модулей сопоставления соответствовал набор модулей сжатия. Это приводит к созданию множества заданий MapReduce для каждого запроса Hive. Tez не имеет такого ограничения и может обрабатывать сложные DAG как единое задание, тем самым  сводя к минимуму затраты на создание новых заданий.
* **Позволяет избежать ненужных операций записи.** Из-за возникновения многочисленных заданий для каждого запроса Hive при работе на платформе MapReduce, результат каждого задания записывается в HDFS в качестве промежуточных данных. Поскольку Tez сводит к минимуму количество заданий для каждого запроса Hive, это позволяет избежать ненужных операций записи.
* **Сводит к минимуму задержки при загрузке.** Платформа Tez способствует снижению задержки при запуске благодаря уменьшению количества модулей сопоставления, необходимых для запуска, а также путем оптимизации всего процесса.
* **Повторно использует контейнеры.** Каждый раз, когда появляется возможность, Tez повторно использует контейнеры, таким образом снижая задержки при запуске контейнеров.
* **Применяет методы непрерывной оптимизации.** Традиционно оптимизация выполняется на этапе компиляции. Однако по мере поступления дополнительных сведений о входных данных появляется возможность улучшить оптимизацию уже на этапе работы. Tez применяет методы непрерывной оптимизации, что позволяет производить дальнейшую оптимизацию плана на этапе работы.

Дополнительные сведения об этих понятиях см. в документе об [Apache Tez](http://hortonworks.com/hadoop/tez/).

Вы можете выполнить любой запрос Hive, используя платформу Tez, активировав ее с помощью префикса запроса со следующими параметрам:

    set hive.execution.engine=tez;

При подготовке кластеров HDInsight под управлением Linux платформа Tez включена по умолчанию.


## <a name="hive-partitioning"></a>Секционирование данных в Hive

Операция ввода-вывода — это основной ограничивающий фактор производительности при выполнении запросов Hive. Производительность можно повысить, если удастся уменьшить объем данных, которые необходимо считать. По умолчанию запросы Hive просматривают все таблицы Hive. Это очень удобно для запросов, выполняющих сканирование таблиц. Однако для запросов, которым требуется просмотреть только небольшой объем данных (например, для запросов с фильтрацией), это поведение создает излишнюю нагрузку. Секционирование данных Hive предоставляет запросам Hive доступ только к необходимому объему данных в таблицах Hive.

Секционирование данных Hive реализуется путем реорганизации необработанных данных в новые каталоги с присвоением каждой секции своего собственного каталога, где секция определяется пользователем. Следующая схема иллюстрирует секционирование таблицы Hive по столбцу *Год*. Для каждого года создается новый каталог.

![Секционирование][image-hdi-optimize-hive-partitioning_1]

Некоторые рекомендации для выполнения секционирования:

* **Секций не должно быть слишком мало.** Секционирование по столбцам, содержащим только небольшое количество значений, может привести к малому количеству секций. Например, секционирование по половой принадлежности создает только две секции ("Мужская" и "Женская"), тем самым это сокращает задержку только наполовину.
* **Секций не должно быть слишком много.** С другой стороны, создание секции для столбца с уникальным значением (например, содержащим идентификатор пользователя userid) приведет к появлению множества секций, что увеличит нагрузку на узел имен кластера из-за необходимости обрабатывать множество каталогов.
* **Избегайте неравномерного распределения данных** — выбирайте ключ секционирования рационально, таким образом, чтобы все секции были одинакового размера. Например секционирование по *Штату* может привести к тому, что количество записей по Калифорнии будет почти в 30 раз больше, чем для Вермонта, из-за различия в населении штатов.

Чтобы выполнить секционирование таблицы, используйте команду *Секционирована по* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Как только секционированная таблица будет создана, можно выполнить статическое или динамическое секционирование.

* **Статическое секционирование** означает, что вы уже распределили данные по соответствующим каталогам и можете выполнить создание секций Hive вручную, исходя из расположения каталогов. Просмотрите пример в следующем фрагменте кода:
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Динамическое секционирование** означает, что Hive автоматически создаст секции для вас. Так как таблица секционирования уже была создана из промежуточной таблицы, теперь достаточно только выполнить вставку данных в секционированную таблицу:
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Дополнительные сведения см. в разделе о [секционированных таблицах](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Использование формата ORC-файлов
Hive поддерживает различные форматы. Например: 

* **Текст**: это формат файла по умолчанию, работает с большинством сценариев
* **Avro**: хорошо подходит для сценариев взаимодействия
* **ORC/Parquet**: лучше всего подходит для повышения производительности

Формат ORC (Optimized Row Columnar) — это очень эффективный способ хранения данных Hive. По сравнению с другими форматами ORC имеет следующие преимущества:

* поддержка сложных типов, включая даты и время и частично структурированные и сложные типы
* обеспечивает показатель сжатия данных до 70 %
* индексирует каждые 10 000 строк, что позволяет пропускать строки;
* обеспечивает существенное снижение времени выполнения запросов

Чтобы активировать ORC формат, необходимо сначала создать таблицу командой *Хранение в виде ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Далее необходимо выполнить вставку данных в таблицу ORC из промежуточной таблицы. Например: 

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Вы можете прочитать подробнее о формате ORC [здесь](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Векторизация

Векторизация позволяет Hive обрабатывать пакеты из 1024 строк одновременно вместо обработки каждой строки отдельно. Это означает, что простые операции будут выполняться быстрее благодаря меньшему объему внутреннего кода, необходимого для запуска.

Чтобы активировать функцию векторизации, создайте запрос Hive со следующими параметрами:

    set hive.vectorized.execution.enabled = true;

Для получения дополнительных сведений обратитесь к разделу [Выполнение векторизованного запроса](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Другие методы оптимизации
Существуют дополнительные методы оптимизации, которые также можно использовать, например:

* **Группирование Hive** — техника, которая позволяет сегментировать или объединять в кластеры большие наборы данных для оптимизации производительности запросов.
* **Оптимизация объединений** — это оптимизация выполнения запросов Hive с целью повышения эффективности объединений и сокращения действия пользователя. Для получения дополнительных сведений обратитесь к разделу [Оптимизация объединений](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Увеличение модулей сжатия.**

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы узнали некоторые распространенные методы оптимизации запросов Hive. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Использование Apache Hive в HDInsight](hadoop/hdinsight-use-hive.md)
* [Анализ данных о задержке рейсов с помощью Hadoop в HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Анализ данных Twitter с помощью Hive в HDInsight](hdinsight-analyze-twitter-data.md)
* [Анализ данных датчика с помощью консоли запросов Hive с Hadoop в HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Использование Hive в HDInsight для анализа журналов веб-сайтов](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
