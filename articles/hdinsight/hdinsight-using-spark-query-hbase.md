---
title: Чтение и запись данных в HBase с помощью Spark в Azure HDInsight | Документация Майкрософт
description: Чтение и запись данных из кластера Spark в кластер HBase с помощью соединителя Spark HBase.
services: hdinsight
documentationcenter: ''
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 7cfc7f586e8a92c29736a7c4cff0b12796be430a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161181"
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Чтение и запись данных в HBase с помощью Spark

Обычно для запроса Apache HBase применяется низкоуровневый API (сканирует, получает и помещает) или синтаксис SQL, использующий Phoenix. Apache также предоставляет соединитель Spark HBase, который является удобной и эффективной альтернативой запросу и изменению данных, хранящихся в HBase.

## <a name="prerequisites"></a>предварительным требованиям

* Установлены отдельные кластеры HDInsight: HBase и Spark (Spark 2.1 (HDInsight 3.6)).
* Кластер Spark должен напрямую связываться с кластером HBase с минимальной задержкой, поэтому рекомендуем развертывать оба кластера в одной и той же виртуальной сети. Дополнительные сведения см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Доступ по протоколу SSH к каждому кластеру.
* Доступ к хранилищу по умолчанию для каждого кластера.

## <a name="overall-process"></a>Общий процесс

Ниже приведен общий процесс, чтобы позволить кластеру Spark запрашивать кластер HDInsight.

1. Подготовка демонстрационных данных в HBase.
2. Извлечение файла hbase-site.xml из папки конфигурации кластера HBase (/etc/hbase/conf).
3. Помещение копии файла hbase-site.xml в папку конфигурации Spark 2 (/etc/spark2/conf).
4. Запуск `spark-shell` со ссылкой на соединитель Spark HBase по его координатам Maven в параметре `packages`.
5. Определение каталога, который сопоставляет схему из Spark с HBase.
6. Взаимодействие с данными HBase с помощью API RDD или таблицы данных.

## <a name="prepare-sample-data-in-hbase"></a>Подготовка демонстрационных данных в HBase

На этом этапе вы создаете и заполняете простую таблицу в HBase, которую затем можно запросить с помощью Spark.

1. Подключитесь к головному узлу кластера HBase с помощью SSH. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Запустите оболочку HBase:

        hbase shell

3. Создайте таблицу `Contacts` с семействами столбцов `Personal` и `Office`:

        create 'Contacts', 'Personal', 'Office'

4. Загрузите несколько выборочных строк данных:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Извлечение файла hbase-site.xml из кластера HBase

1. Подключитесь к головному узлу кластера HBase с помощью SSH.
2. Скопируйте файл hbase-site.xml из локального хранилища в корень хранилища по умолчанию кластера HBase:

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Перейдите в кластер HBase с помощью [портала Azure](https://portal.azure.com).
4. Выберите "Учетные записи хранения". 

    ![учетные записи хранения;](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Выберите учетную запись хранения в списке с галочкой в столбце "По умолчанию".

    ![Учетная запись хранения по умолчанию](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. На панели "Учетная запись хранения" выберите плитку "Большие двоичные объекты".

    ![Плитка "Большие двоичные объекты"](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. В списке контейнеров выберите контейнер, используемый кластером HBase.
8. В списке файлов выберите `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. На панели свойств большого двоичного объекта выберите "Загрузить" и сохраните `hbase-site.xml` в расположении на вашем локальном компьютере.

    ![Загрузка](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Помещение файла hbase-site.xml в кластер Spark

1. Перейдите в кластер Spark с помощью [портала Azure](https://portal.azure.com).
2. Выберите "Учетные записи хранения".

    ![учетные записи хранения;](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Выберите учетную запись хранения в списке с галочкой в столбце "По умолчанию".

    ![Учетная запись хранения по умолчанию](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. На панели "Учетная запись хранения" выберите плитку "Большие двоичные объекты".

    ![Плитка "Большие двоичные объекты"](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. В списке контейнеров выберите контейнер, используемый кластером Spark.
6. Выберите "Передать".

    ![Передать](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Выберите файл `hbase-site.xml`, который вы ранее загрузили на локальный компьютер.

    ![Передача файла hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Выберите "Передать".
9. Подключитесь к головному узлу кластера Spark с помощью SSH.
10. Скопируйте `hbase-site.xml` из хранилища по умолчанию кластера Spark в папку конфигурации Spark 2 в локальном хранилище кластера:

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Запуск оболочки Shell со ссылкой на соединитель Spark HBase

1. Подключитесь к головному узлу кластера Spark с помощью SSH.
2. Запустите оболочку Spark, указав пакет соединителя Spark HBase:

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11 --repositories http://repo.hortonworks.com/coroups/public/

3. Оставьте открытым экземпляр оболочки Spark и перейдите к следующему шагу.

## <a name="define-a-catalog-and-query"></a>Определение каталога и отправка запроса

На этом этапе вы определяете объект каталога, который сопоставляет схему из Spark с HBase. 

1. В открытой оболочке Spark выполните следующие инструкции `import`:

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Определите каталог для таблицы контактов, созданной в HBase.
    1. Определите схему каталога для таблицы HBase с именем `Contacts`.
    2. Определите rowkey как `key` и сопоставьте имена столбцов, используемые в Spark, с семейством столбцов, именем столбца и типом столбца, используемыми в HBase.
    3. rowkey также должен быть определен как именованный столбец (`rowkey`), который содержит определенное семейство столбцов `cf` из `rowkey`.

            def catalog = s"""{
                |"table":{"namespace":"default", "name":"Contacts"},
                |"rowkey":"key",
                |"columns":{
                |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
                |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
                |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
                |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
                |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
                |}
            |}""".stripMargin

3. Определите метод, который предоставляет таблицу данных для вашей таблицы `Contacts` в HBase:

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Создайте экземпляр таблицы данных:

        val df = withCatalog(catalog)

5. Выполните запрос таблицы данных:

        df.show()

6. Вы должны увидеть две строки данных:

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Зарегистрируйте временную таблицу, чтобы запрашивать таблицу HBase с помощью Spark SQL:

        df.registerTempTable("contacts")

8. Выполните SQL-запрос к таблице `contacts`:

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Вы должны увидеть примерно такой результат:

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Вставка новых данных

1. Чтобы вставить новую запись о контакте, определите класс `ContactRecord`:

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Создайте экземпляр `ContactRecord` и поместите его в массив:

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Сохраните массив новых данных в HBase:

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Изучите результаты:
    
        df.show()

5. Вы должны увидеть примерно такой результат:

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Дополнительная информация

* [Соединитель Spark HBase](https://github.com/hortonworks-spark/shc)
