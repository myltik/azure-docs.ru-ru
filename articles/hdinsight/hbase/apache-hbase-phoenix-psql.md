---
title: Массовая загрузка данных в Apache Phoenix с помощью psql — Azure HDInsight | Документация Майкрософт
description: Используйте средство psql для массовой загрузки данных в таблицы Phoenix.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 54d3b7ae22162d84ef6287945d4ff95fa1274874
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164744"
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Массовая загрузка данных в Phoenix с помощью psql

[Apache Phoenix](http://phoenix.apache.org/) — это реляционная база данных на основе [HBase](../hbase/apache-hbase-overview.md) с открытым кодом и высоким уровнем параллелизма. Phoenix поддерживает запросы к HBase в стиле SQL. Phoenix использует драйверы JDBC. Это позволяет создавать, удалять и изменять SQL-таблицы, индексы, представления и последовательности, а также вставлять строки по одной или в пакетном режиме. Phoenix использует для сборки запросов компиляцию в машинный код noSQL, а не MapReduce, что позволяет создавать на основе HBase приложения с низким уровнем задержки. Также Phoenix поддерживает сопроцессоры для выполнения пользовательского кода в адресном пространстве сервера, то есть прямо в месте размещения данных. Это сводит к минимуму трафик между клиентом и сервером.  Чтобы применить Phoenix для работы с данными в HDInsight, сначала создайте таблицы и загрузите в них данные.

## <a name="bulk-loading-with-phoenix"></a>Массовая загрузка с помощью Phoenix

Есть много способов загрузить данные в HBase, например клиентские интерфейсы API, задание MapReduce с TableOutputFormat или ввод данных вручную через оболочку HBase. Phoenix предлагает два метода загрузки данных в формате CSV в таблицы Phoenix: клиентское средство загрузки `psql` и средство массовой загрузки на основе MapReduce.

Средство `psql` является однопоточным и хорошо подходит для загрузки нескольких мегабайт или гигабайт данных. Все загружаемые CSV-файлы должны иметь расширение .csv.  Также вы можете указать в командной строке `psql` SQL-файлы скриптов с расширением .sql.

Массовая загрузка с помощью MapReduce применяется для данных существенно большего объема, в том числе в промышленных средах, поскольку в MapReduce используется многопоточная схема работы.

Прежде чем загружать данные, убедитесь, что Phoenix включен и для запросов правильно настроены параметры времени ожидания.  Откройте панель мониторинга Ambari для кластера HDInsight, выберите HBase и откройте вкладку настройки.  Прокрутите вниз и убедитесь, что Apache Phoenix включен, проверив указанный на следующем рисунке параметр `enabled`:

![Параметры кластера HDInsight для Apache Phoenix](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Использование `psql` для массовой загрузки таблиц

1. Создайте таблицу и сохраните запрос в файл с именем `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Скопируйте CSV-файл с нужными данными (в указанном ниже формате) с именем `customers.csv` в каталог `/tmp/`, чтобы загрузить эти данные в созданную таблицу.  С помощью команды `hdfs` скопируйте CSV-файл в требуемое исходное расположение.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Создайте SQL-запрос SELECT для проверки загруженных данных, а затем сохраните его в файл с именем `listCustomers.sql`. Для этого вы можете применить любой SQL-запрос.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Выполните массовую загрузку данных, открыв *новое* окно командной строки Hadoop. Сначала перейдите в каталог выполнения с помощью команды `cd`, а затем запустите средство `psql` (командой Python `psql.py`). 

    Команда в следующем примере ожидает, что файл `customers.csv` скопирован из учетной записи хранилища в локальный каталог temp `hdfs`, как описано выше в шаге 2.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Чтобы определить имя `ZookeeperQuorum`, найдите в файле `/etc/hbase/conf/hbase-site.xml` строку кворума zookeeper с именем свойства `hbase.zookeeper.quorum`.

5. Когда операция `psql` завершится, вы увидите в окне командной строки следующее сообщение:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Использование MapReduce для массовой загрузки таблиц

Чтобы повысить пропускную способность и распределить нагрузку в кластере, используйте средство загрузки MapReduce. Этот загрузчик преобразует все данные в формат HFiles и передает созданные файлы HFiles в HBase.

1. Запустите загрузчик CSV MapReduce с помощью команды `hadoop`, указав клиентский JAR-файл Phoenix:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Создайте новую таблицу с помощью инструкции SQL, например `CreateCustomersTable.sql` из шага 1.

3. Чтобы проверить схему таблицы, запустите `!describe inputTable`.

4. Определите путь расположения входных данных (в нашем примере файла `customers.csv`). Файлы входных данных можно разместить в учетной записи хранилища WASB/ADLS. В нашем примере файлы входных данных сохранены в каталоге `<storage account parent>/inputFolderBulkLoad`.

5. Откройте каталог выполнения команды массовой загрузки MapReduce:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Найдите в файле `/etc/hbase/conf/hbase-site.xml` нужное значение `ZookeeperQuorum` с именем свойства `hbase.zookeeper.quorum`.

7. Настройте пути к классам и выполните команду запуска средства `CsvBulkLoadTool`:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Чтобы использовать MapReduce с ADLS, найдите корневую папку ADLS, которая задана значением `hbase.rootdir` в файле `hbase-site.xml`. В следующей команде корневой каталог ADLS имеет значение `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Передайте при помощи этой команды входной и выходной каталоги ADLS в качестве параметров:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Рекомендации

* Используйте одну среду хранения для входных и выходных папок WASB или ADLS. Чтобы передать данные из WASB в ADLS, можно использовать команду `distcp`:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Используйте рабочие узлы большого размера. Процессы сопоставления в операции массового копирования MapReduce создают большие объемы временных выходных данных, которые занимают все доступное место, кроме DFS. Для массовой загрузки большого объема данных используйте значительное количество рабочих узлов большого размера. Число рабочих узлов, выделенных для кластера, прямо влияет на скорость обработки.

* Разделите входные файлы на фрагменты объемом около 10 ГБ. Для массовой загрузки данных используются большие объемы хранилища. Поэтому разделение входных данных на небольшие фрагменты позволит повысить производительность.

* Избегайте перегрузки региональных серверов. Если значение ключа в строках монотонно возрастает, последовательные операции записи в HBase приводят к перегрузке региональных серверов. Добавив *соль* к ключу строк, вы сможете избежать последовательных операций записи. Phoenix позволяет прозрачно добавлять случайные значения к ключу строк, используя байты соли для конкретных таблиц. Этот механизм описан в статье, ссылка на которую приводится ниже.

## <a name="next-steps"></a>Дополнительная информация

* [Массовая загрузка данных с помощью Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Использование Apache Phoenix с кластерами HBase под управлением Linux в HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Salted Tables](https://phoenix.apache.org/salted.html) (Таблицы с солью)
* [Грамматика Phoenix](http://phoenix.apache.org/language/index.html)
