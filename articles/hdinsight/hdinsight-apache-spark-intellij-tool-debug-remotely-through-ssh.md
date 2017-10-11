---
title: "Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ | Документы Майкрософт"
description: "Пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений на кластерах HDInsight через SSH."
keywords: "удаленная отладка intellij, ssh, intellij, hdinsight, отладка intellij, отладка"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH

В данной статье представлены пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений в кластере HDInsight. Для отладки проекта можно также просмотреть видео [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Отладка приложений HDInsight Spark с помощью набора средств Azure для IntelliJ).

**Предварительные требования**

* **Средства HDInsight из набора средств Azure для IntelliJ**. Этот инструмент входит в состав набора средств Azure для IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Набор средств Azure для IntelliJ**. Этот набор средств используется для создания приложений Spark для кластера HDInsight. Дополнительные сведения см. в статье [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Служба SSH HDInsight с функцией управления именем пользователя и паролем.** Дополнительные сведения см. в статьях [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) и [Использование туннелирования SSH для доступа к пользовательскому веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим пользовательским веб-интерфейсам](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Создание приложения Spark Scala и его настройка для удаленной отладки

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне **Новый проект** сделайте следующее:

   а. Выберите **HDInsight**. 

   b. Выберите шаблон Java или Scala (на свой выбор). Выберите один из следующих вариантов:

      - **Spark on HDInsight (Scala)** (Spark в HDInsight [Scala])

      - **Spark on HDInsight (Java)** (Spark в HDInsight [Java])

      - **Spark on HDInsight Cluster Run Sample (Scala)** (Пример запуска Spark в кластере HDInsight [Scala])

      В этом примере используется шаблон **Пример запуска Spark в кластере HDInsight (Scala)**.

   c. В списке **средств сборки** выберите один из следующих вариантов:

      - **Maven.** Для поддержки мастера создания проекта Scala.

      -  **SBT.** Для управления зависимостями и создания проекта Scala. 

      ![Создание проекта отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. Щелкните **Далее**.     
 
3. В следующем окне **New Project** (Новый проект) сделайте следующее.

   ![Выбор пакета SDK для Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   а. Введите имя и расположение проекта.

   b. Из раскрывающегося списка **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера **Spark 2.x** или **Java 1.7** для кластера **Spark 1.x**.

   c. В раскрывающемся списке **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x.** В этом примере используется **Spark 2.0.2 (Scala 2.11.8)**.

   d. Выберите **Готово**.

4. Выберите **src** > **main** > **scala**, чтобы открыть код в проекте. В этом примере используется сценарий **SparkCore_wasbloTest**.

5. Чтобы перейти в меню **Edit Configurations** (Изменение конфигураций), щелкните значок в правом верхнем углу. В этом меню можно создавать и редактировать конфигурации для удаленной отладки.

   ![Меню Edit Configurations (Изменение конфигураций)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. В диалоговом окне **Run/Debug Configurations** (Конфигурации выполнения и отладки) щелкните знак "плюс" (**+**). Выберите **Submit Spark Job** (Отправить задание Spark).

   ![Добавление новой конфигурации](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. Введите сведения в полях **Name** (Имя), **Spark cluster** (Кластер Spark) и **Main class name** (Имя основного класса). Затем выберите **Advanced configuration** (Расширенная конфигурация). 

   ![Запуск конфигураций отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. В диалоговом окне **Spark Submission Advanced Configuration** (Расширенная конфигурация отправки Spark) установите флажок **Enable Spark remote debug** (Разрешить удаленную отладку Spark). Введите имя пользователя SSH, затем введите пароль либо воспользуйтесь файлом закрытого ключа. Нажмите кнопку **ОК**, чтобы сохранить конфигурацию.

   ![Включение удаленной отладки Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации). 

10. Завершив настройку конфигурации, можно запустить проект на удаленном кластере или выполнить удаленную отладку.

## <a name="learn-how-to-perform-remote-debugging"></a>Порядок выполнения удаленной отладки
### <a name="scenario-1-perform-remote-run"></a>Сценарий 1. Удаленный запуск

В этом разделе мы покажем, как выполнить отладку драйверов и исполнителей.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. Настройте точки останова, а затем щелкните значок **Debug** (Отладка).

   ![Щелчок значка отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. Когда выполнение программы достигнет точки останова, в области **Debugger** (Отладчик) вы увидите вкладку **Driver** (Драйвер) и две вкладки **Executor** (Исполнитель). Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение кода, который затем достигнет следующей точки останова и отобразится на соответствующей вкладке **Executor** (Исполнитель). Вы можете просмотреть журналы выполнения на соответствующей вкладке **Console** (Консоль).

   ![Вкладка отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>Сценарий 2. Удаленная отладка и исправление ошибок
В этом разделе мы покажем, как выполнять динамическое обновление значения переменной с помощью возможностей отладки IntelliJ для исправления простых ошибок. В следующем примере кода возникает исключение из-за того, что целевой файл уже существует.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Удаленная отладка и исправление ошибок
1. Настройте две точки останова и щелкните значок **Debug** (Отладка), чтобы приступить к удаленной отладке.

2. Выполнение кода будет приостановлено в первой точке останова, а в области **Variables** (Переменные) отобразятся сведения о параметре и переменной. 

3. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить. Выполнение кода будет приостановлено во второй точке останова. Как и ожидалось, возникнет исключение.

  ![Ошибка](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Снова щелкните значок **Resume Program** (Возобновить выполнение программы). В окне **HDInsight Spark Submission** (Отправка HDInsight Spark) будет указано, что во время выполнения задания произошла ошибка.

  ![Ошибка отправки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Чтобы динамически обновить значение переменной с помощью функции отладки IntelliJ, еще раз щелкните значок **Debug** (Отладка). Снова появится область **Variables** (Переменные). 

6. Щелкните правой кнопкой мыши целевой объект на вкладке **Debug** (Отладка), а затем выберите **Set Value** (Задать значение). Затем введите новое значение переменной. Нажмите клавишу **ВВОД**, чтобы сохранить значение. 

  ![Задание значения](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Щелкните значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение программы. На этот раз исключение не возникает. Проект выполняется успешно без исключений.

  ![Отладка без исключений](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Дальнейшие действия
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Создание приложений машинного обучения Apache Spark в Azure HDInsight](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Apache Spark. Обработка данных из концентраторов событий Azure с помощью кластера Spark в HDInsight](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)
