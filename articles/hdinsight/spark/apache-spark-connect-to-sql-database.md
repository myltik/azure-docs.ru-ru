---
title: Чтение данных из базы данных SQL Azure и запись в нее с использованием Apache Spark | Документация Майкрософт
description: Узнайте, как настроить подключение между кластером HDInsight Spark и базой данных SQL Azure для чтения данных из базы данных SQL, а также записи и потоковой передачи данных в нее.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: nitinme
ms.openlocfilehash: 63bf7d5a0ad988ff7a6b498b4e91e90de97b507b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775648"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Чтение данных из базы данных SQL Azure и запись в нее с использованием кластера HDInsight Spark

Узнайте, как подключить кластер Apache Spark в Azure HDInsight с помощью базы данных SQL Azure, а также как выполнять чтение из базы данных SQL, запись и потоковую передачу данных в нее. В приведенных здесь инструкциях для выполнения фрагментов кода Scala используется записная книжка Jupyter. Тем не менее вы можете создать изолированное приложение на языке Scala или Python и выполнять такие же задачи. 

## <a name="prerequisites"></a>предварительным требованиям

* **Кластер Azure HDInsight Spark**.  Инструкции см. в статье [Создание кластера Apache Spark в Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* **База данных SQL Azure**. Инструкции см. в статье [Создание базы данных SQL Azure на портале Azure](../../sql-database/sql-database-get-started-portal.md). Создайте базу данных с примером схемы и данными **AdventureWorksLT**. Создайте также правило брандмауэра на уровне сервера, чтобы предоставить клиентским IP-адресам доступ к базе данных SQL на сервере. Инструкции по добавлению правила брандмауэра находятся в той же статье. После создания базы данных SQL Azure сохраните следующие значения. Они понадобятся вам для подключения к базе данных из кластера Spark.

    * Имя сервера, на котором размещена база данных SQL Azure.
    * Имя базы данных SQL Azure
    * Имя и пароль администратора базы данных SQL Azure.

* **SQL Server Management Studio.** Инструкции см. в статье [Подключайтесь к базе данных Azure SQL и создавайте запросы к ней с помощью SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Создание записной книжки Jupyter

Сначала создайте записную книжку Jupyter, связанную с кластером Spark. Она понадобится для выполнения фрагментов кода, используемых в этой статье. 

1. Откройте кластер на [портале Azure](https://portal.azure.com/). 

2. В разделе **Быстрые ссылки** щелкните **Панели мониторинга кластера**, чтобы открыть представление **Панели мониторинга кластера**.  Если раздел **Быстрые ссылки** не отображается, в колонке в меню слева щелкните **Обзор**.

    ![Панель мониторинга кластера в Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "Панель мониторинга кластера в Spark") 

3. Щелкните **Записная книжка Jupyter**. При появлении запроса введите учетные данные администратора для кластера.

    ![Записная книжка Jupyter в Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Записная книжка Jupyter в Spark")
   
   > [!NOTE]
   > Вы также можете получить доступ к записной книжке Jupyter в кластере Spark, открыв следующий URL-адрес в браузере. Замените **CLUSTERNAME** именем кластера:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. Чтобы создать записную книжку Scala, в записной книжке Jupyter в правом верхнем углу щелкните **New** (Создать), а затем — **Spark**. Записные книжки Jupyter в кластере HDInsight Spark предоставляют ядро **PySpark** для приложений Python2 и ядро **PySpark3** для приложений Python3. В этой статье мы создаем записную книжку Scala.
   
    ![Ядра для записной книжки Jupyter в Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Ядра для записной книжки Jupyter в Spark")

    Дополнительные сведения о ядрах см. в статье [Ядра для записных книжек Jupyter с кластерами Apache Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > В этой статье используется ядро Spark (Scala), так как в настоящее время потоковая передача данных из Spark в базу данных SQL поддерживается только на Scala и Java. Несмотря на то что чтение и запись в SQL можно выполнить с помощью Python, для обеспечения согласованности в этой статье мы используем Scala для всех трех операций.
   >

5. После этого откроется новая записная книжка с именем по умолчанию **Untitled** (Без имени). Щелкните это имя и введите имя по своему усмотрению.

    ![Указание имени для записной книжки](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Указание имени для записной книжки")

Теперь можно приступить к созданию приложения.
    
## <a name="read-data-from-azure-sql-database"></a>Cчитывание данных из базы данных SQL Azure

В этом разделе вы выполняете операцию чтения данных из таблицы (например, **SalesLT.Address**), которая находится в базе данных AdventureWorks.

1. В новой записной книжке Jupyter в ячейку кода вставьте следующий фрагмент кода и замените значения заполнителей значениями для базы данных SQL Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.  

2. С помощью приведенного ниже фрагмента кода создается URL-адрес JDBC, который можно передать в API кадров данных Spark, а также объект `Properties` для хранения параметров. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. С помощью приведенного ниже фрагмента кода создается кадр данных с данными из таблицы в базе данных SQL Azure. Для этого фрагмента используется таблица **SalesLT.Address**. Она доступна как часть базы данных **AdventureWorksLT**. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Теперь вы можете выполнять операции над кадром данных (например, получение схемы данных).

       sqlTableDF.printSchema
   
    Должен отобразиться следующий результат:

    ![Указание имени для записной книжки](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Указание имени для записной книжки")

5. Вы также можете выполнять такие операции, как извлечение первых 10 строк.

       sqlTableDF.show(10)

6. Можно также извлечь определенные столбцы из набора данных.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Запись данных в базу данных SQL Azure

В этом разделе для создания таблицы в базе данных SQL Azure и заполнения ее данными используется пример CSV-файла, доступный в кластере. Пример CSV-файла (**HVAC.csv**) содержится во всех кластерах HDInsight в папке `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. В новой записной книжке Jupyter в ячейку кода вставьте следующий фрагмент кода и замените значения заполнителей значениями для базы данных SQL Azure.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.  

2. Следующий фрагмент кода создает URL-адрес JDBC, который можно передать в API кадров данных Spark, а также объект `Properties` для хранения параметров. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. В приведенном ниже фрагменте кода извлекается схема данных в HVAC.csv и с ее помощью данные загружаются из CSV-файла в кадр данных `readDf`. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Используя кадр данных `readDf`, создайте временную таблицу `temphvactable`. Затем с ее помощью создайте таблицу hive `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Наконец, с помощью таблицы hive создайте таблицу в базе данных SQL Azure. Следующий фрагмент кода создает `hvactable` в базе данных SQL Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Подключитесь к базе данных SQL Azure с помощью SSMS и убедитесь, что вы видите таблицу `dbo.hvactable`.

    a. Запустите SSMS и подключитесь к базе данных SQL Azure, указав сведения о подключении, как показано на снимке экрана.

    ![Подключение к базе данных SQL с помощью SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Подключение к базе данных SQL с помощью SSMS")

    Б. В обозревателе объектов разверните узел "База данных SQL Azure" и "Таблица", чтобы увидеть созданную таблицу **dbo.hvactable**.

    ![Подключение к базе данных SQL с помощью SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Подключение к базе данных SQL с помощью SSMS")

7. Выполните запрос в SSMS для просмотра всех столбцов в таблице.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Потоковая передача данных в базу данных SQL Azure

В этом разделе мы выполним потоковую передачу данных в таблицу **hvactable**, которую вы ранее создали в базе данных SQL Azure.

1. Сначала проверьте, что таблица **hvactable** пустая. С помощью SSMS выполните следующий запрос к таблице.

       DELETE FROM [dbo].[hvactable]

2. Создайте записную книжку Jupyter в кластере HDInsight Spark. Вставьте следующий фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**.

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Мы выполняем потоковую передачу данных из **HVAC.csv** в hvactable. Файл HVAC.csv находится в кластере в папке */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. В следующем фрагменте кода мы сначала получаем схему данных для потоковой передачи. Затем создаем кадр данных потоковой передачи с помощью этой схемы. Вставьте фрагмент кода в ячейку кода и нажмите клавиши **SHIFT+ВВОД**, чтобы выполнить код.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. Выходные данные будут содержать схему файла **HVAC.csv**. В таблице **hvactable** также есть схема. В выходных данных перечислены столбцы в таблице.

    ![Схема таблицы](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Схема таблицы")

5. Наконец, используйте следующий фрагмент кода, чтобы считать данные из HVAC.csv и передать их в **hvactable** в базе данных SQL Azure. Вставьте фрагмент кода в ячейку кода, замените значения заполнителей значениями для базы данных SQL Azure, а затем нажмите клавиши **SHIFT+ENTER**, чтобы выполнить код.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

6. Убедитесь, что данные передаются в таблицу **hvactable**, выполнив следующий запрос в SQL Server Management Studio. При каждом выполнении запроса отображется большее число строк в таблице.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>Дополнительная информация

* [Использование кластера HDInsight Spark для анализа данных в Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Структурированная потоковая передача Apache Spark в HDInsight для обработки событий из концентраторов событий](apache-spark-eventhub-structured-streaming.md)
* [Использование структурированной потоковой передачи Spark с Kafka в HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
