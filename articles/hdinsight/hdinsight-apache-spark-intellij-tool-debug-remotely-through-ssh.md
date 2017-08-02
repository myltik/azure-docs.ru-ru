---
title: "Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ | Документы Майкрософт"
description: "Пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений на кластерах HDInsight через SSH"
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
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: ru-ru
ms.lasthandoff: 07/17/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH

В данной статье представлены пошаговые инструкции по использованию инструментов HDInsight из набора средств Azure для IntelliJ для удаленной отладки приложений на кластере HDInsight. Для отладки проекта можно воспользоваться инструкциями из этого [видео](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ).

**Предварительные требования:**

* **Средства HDInsight из набора средств Azure для IntelliJ**. Эти инструменты входят в состав набора средств Azure для IntelliJ. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation).
* **Набор средств Azure для IntelliJ**. Используется для создания приложений Spark для кластера HDInsight. Дополнительные сведения см. в статье [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Служба SSH HDInsight с функцией управления именем пользователя и паролем.** Дополнительные сведения см. в статьях [Подключение к HDInsight (Hadoop) с помощью SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) и [Использование туннелирования SSH для доступа к пользовательскому веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим пользовательским веб-интерфейсам](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Создание приложения Spark Scala и его настройка для удаленной отладки
1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next**(«Далее»). В этой статье в качестве примера используется **Пример запуска Spark в кластере HDInsight (Scala)**.

     ![Создание проекта отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - В левой области выберите **HDInsight**.
   - В правой области выберите шаблон Java или Scala (по желанию). Выберите один из следующих параметров: 
      - **Spark on HDInsight (Scala)** (Spark в HDInsight [Scala])
      - **Spark on HDInsight (Java)** (Spark в HDInsight [Java])
      - **Spark on HDInsight Cluster Run Sample (Scala)** (Пример запуска Spark в кластере HDInsight [Scala])
   - Средство сборки: мастер создания проекта Scala поддерживает Maven или SBT для управления зависимостями и сборки проекта Scala. Необходимо выбрать один соответствующий вариант.
2. В следующем окне укажите сведения о проекте.

   ![Выбор пакета SDK для Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - Введите имя и расположение проекта.
   - Для параметра **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера **Spark 2.x** или **Java 1.7** для кластера **Spark 1.x**.
   - Для параметра **Spark Version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK Spark и пакета SDK Scala. Если версия кластера Spark меньше, чем 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x.** В этой статье в качестве примера используется **Spark 2.0.2 (Scala 2.11.8)**.
3. Выберите **src** > **main** > **scala**, чтобы открыть код в проекте. В этой статье в качестве примера используется сценарий **SparkCore_wasbloTest**.
4. Чтобы перейти в меню **Edit Configurations** (Изменение конфигураций), щелкните значок в правом верхнем углу. В этом меню можно создавать и редактировать конфигурации для удаленной отладки.

   ![Меню Edit Configurations (Изменение конфигураций)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. В окне **Run/Debug Configurations** (Запуск и отладка конфигураций) щелкните значок плюса (**+**). Выберите **Submit Spark Job** (Отправить задание Spark).

   ![Меню Edit Configurations (Изменение конфигураций)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. Введите сведения в полях **Name** (Имя), **Spark cluster** (Кластер Spark) и **Main class name** (Имя основного класса). Затем выберите **Advanced configuration** (Расширенная конфигурация). 

   ![Запуск конфигураций отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. В диалоговом окне **Spark Submission Advanced Configuration** (Расширенная конфигурация отправки Spark) установите флажок **Enable Spark remote debug** (Разрешить удаленную отладку Spark). Введите имя пользователя или пароль SSH либо воспользуйтесь файлом закрытого ключа. Чтобы сохранить его, нажмите **ОК**.

   ![Включение удаленной отладки Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. Теперь конфигурация сохранена под именем, которое вы указали. Чтобы просмотреть сведения о конфигурации, выберите имя конфигурации. Чтобы внести изменения, выберите **Edit Configurations** (Изменить конфигурации). 
9. Завершив настройку конфигурации, можно запустить проект на удаленном кластере или выполнить удаленную отладку.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>Порядок выполнения удаленной отладки и удаленного запуска
### <a name="scenario-1-perform-remote-run"></a>Сценарий 1. Удаленный запуск
1. Чтобы удаленно запустить проект, щелкните значок **Run** (Запустить).

   ![Щелкните значок запуска](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. В окне **HDInsight Spark Submission** (Отправка HDInsight Spark) отображается состояние выполнения приложения. Сведения в этом окне можно использовать для отслеживания хода выполнения задания Scala.

   ![Щелкните значок запуска](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Сценарий 2. Удаленная отладка
1. Настройте точку останова, а затем щелкните значок **Debug** (Отладка).

    ![Щелкните значок отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. Когда выполнение программы достигнет точки останова, на нижней панели отобразится вкладка **Debugger** (Отладчик). В окне **Variable** (Переменная) также можно просмотреть сведения о параметре и переменной. Щелкните значок **Step Over** (Перешагнуть), чтобы перейти к следующей строке кода. Затем можно продолжить пошаговую отладку кода. Щелкните значок **Resume Program** (Возобновить программу), чтобы продолжить выполнение кода. Просмотреть состояние выполнения можно в окне **HDInsight Spark Submission** (Отправка HDInsight Spark). 

   ![Вкладка отладки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Сценарий 3. Удаленная отладка и исправление ошибок
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

2. Выполнение кода будет приостановлено в первой точке останова, а в окне **Variable** (Переменная) отобразятся сведения о параметре и переменной. 

3. Щелкните значок **Resume Program** (Возобновить программу), чтобы продолжить. Выполнение кода будет приостановлено во второй точке останова. Как и ожидалось, возникнет исключение.

  ![Ошибка](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Снова щелкните значок **Resume Program** (Возобновить программу). В окне **HDInsight Spark Submission** (HDInsight Spark) будет указано, что во время выполнения задания произошла ошибка.

  ![Ошибка отправки](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Чтобы динамически обновить значение переменной с помощью функции отладки IntelliJ, еще раз щелкните значок **Debug** (Отладка). Снова отобразится окно переменной. 

6. Щелкните правой кнопкой мыши целевой объект на вкладке **Debug** (Отладка), а затем выберите **Set Value** (Задать значение). Затем введите новое значение для переменной. Нажмите клавишу **ВВОД**, чтобы сохранить значение. 

  ![Задание значения](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Щелкните значок **Resume Program** (Возобновить программу), чтобы продолжить выполнение программы. На этот раз исключение не возникает. Проект выполняется успешно без исключений.

  ![Отладка без исключений](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование средств HDInsight в наборе средств Azure для IntelliJ для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений в HDInsight Spark с помощью набора средств Azure для IntelliJ через VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)
 
