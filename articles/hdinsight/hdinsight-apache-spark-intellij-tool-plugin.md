---
title: "Создание приложений Scala для Spark с помощью набора средств Azure для IntelliJ | Документация Майкрософт"
description: "Узнайте, как создать автономное приложение Spark для работы в кластерах HDInsight Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 6c513def2abc8c99b84b2eb48848de76e12e69c7


---
# <a name="use-hdinsight-tools-in-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-spark-linux-cluster"></a>Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ
В этой статье приводятся пошаговые инструкции по разработке приложений Spark, написанных на языке Scala, и их отправке в кластер HDInsight Spark с помощью средств HDInsight в наборе средств Azure для IntelliJ.  Эти средства можно использовать различным образом:

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

Чтобы приступить к работе, посмотрите видео [здесь](https://mix.office.com/watch/1nqkqjt5xonza) .

> [!IMPORTANT]
> Это средство позволяет создавать и отправлять приложения только в кластер HDInsight Spark на Linux.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. В этой статье используется версия 15.0.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Установка средств HDInsight в наборе средств Azure для IntelliJ
Средства HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. Инструкции по установке набора средств Azure см. в статье [Установка набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="log-into-your-azure-subscription"></a>Вход в подписку Azure
1. Запустите интегрированную среду разработки IntelliJ и откройте Azure Explorer. В меню **View** (Вид) интегрированной среды разработки щелкните **Tool Windows** (Окна инструментов) и выберите **Azure Explorer**.
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. В **Azure Explorer** щелкните правой кнопкой мыши узел **Azure**, а затем выберите пункт **Управление подписками**.
3. В диалоговом окне **Управление подписками** нажмите кнопку **Вход** и введите свои учетные данные Azure.
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. После входа в диалоговом окне **Управление подписками** будут перечислены все подписки Azure, связанные с указанными учетными данными. Нажмите кнопку **Закрыть** в этом диалоговом окне.
5. На вкладке **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Запуск приложения Spark Scala в кластере HDInsight Spark
1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next**(«Далее»).
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * В левой области выберите **HDInsight**.
   * В правой области выберите **Spark on HDInsight (Scala)**(Spark в HDInsight (Scala)).
   * Нажмите кнопку **Далее**.
2. В следующем окне укажите сведения о проекте.
   
   * Введите имя и расположение проекта.
   * Для **пакета SDK для Project**, Java 1.7 или более поздней версии для кластера Spark 1.6 и Java 1.8 для кластера Spark 2.0.
   * Для **пакета SDK для Scala** щелкните **Download** (Создать), а затем **Download** (Скачать), после чего выберите версию Scala.
   * * Выберите **JDK 1.8 и Scala 2.11.x**, если вы хотите отправить задание в кластер Spark 2.0.
   * * Выберите **JDK 1.7 или более позднюю версию и Scala 2.10.x**, если вы хотите отправить задание в кластер Spark 1.6.

        ![](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Можно скачать и использовать **пакет SDK для Spark** [отсюда](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) (spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar для кластера Spark 2.0 и spark-assembly-x.jar для кластера Spark 1.6). Кроме того, можно использовать [репозиторий Spark Maven](http://mvnrepository.com/search?q=spark), однако убедитесь, что у вас установлен нужный репозиторий Maven для разработки приложений Spark. (Например, при использовании потоковой передачи Spark необходимо убедиться, что установлена часть Spark Streaming. Кроме того, убедитесь, что для кластера Spark 1.6 используется репозиторий, помеченный как Scala 2.10, а для кластера Spark 2.0 — репозиторий, помеченный как Scala 2.11).
     
       ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
   * Нажмите кнопку **Готово**
3. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, выполните описанные ниже действия.
   
   1. В меню **File** (Файл) выберите пункт **Project Structure** (Структура проекта).
   2. В диалоговом окне **Project Structure** (Структура проекта) щелкните **Артефакты**, чтобы просмотреть созданный по умолчанию артефакт.
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
      Кроме того, можно создать свой собственный артефакт, щелкнув значок **+** , выделенный на рисунке выше.
4. В диалоговом окне **Project Structure** (Структура проекта) щелкните **Проект**. Если настроен **пакет SDK для Project** версии 1.8, установите для параметра **Project language level** (Уровень языка проекта) значение **7 - Diamonds, ARM, multi-catch, etc** (7 — Diamonds, ARM, множественный перехват и т. п.). Для кластера Spark 2.0 делать это необязательно.
   
    ![Настройка уровня языка проекта](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Добавьте исходный код своего приложения.
   
   1. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **Создать** и щелкните **Scala Class** (Класс Scala).
      
       ![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Вид** выберите **Объект** и нажмите кнопку **ОК**.
      
       ![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. Вставьте в файл **MyClusterApp.scala** следующий код. Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в используемом по умолчанию контейнере хранилища для кластера.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

1. Запустите приложение в кластере HDInsight Spark.
   
   1. В **обозревателе проектов** щелкните имя проекта правой кнопкой мыши и выберите пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).
      
       ![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Вам будет предложено ввести учетные данные подписки Azure. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения.
      
      * В поле **Spark clusters (Linux only)**(Кластеры Spark (только для Linux)) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.
      * Необходимо выбрать артефакт из проекта IntelliJ или с жесткого диска.
      * В текстовом поле **Main class name** (Имя класса Main) щелкните многоточие (![многоточие](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), выберите класс Main в исходном коде приложения и нажмите кнопку **ОК**.
        
          ![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Поскольку для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.
      * После ввода всех входных данных диалоговое окно должно выглядеть следующим образом.
        
          ![Отправить приложение Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      * Нажмите кнопку **Submit**(Отправить).
   3. На вкладке **Spark Submission** (Отправка в Spark) в нижней части окна начнет отображаться ход выполнения. Также можно остановить приложение, щелкнув красную кнопку в окне отправки в Spark.
      
       ![Результат приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      В следующем разделе вы узнаете, как получить доступ к выходным данным задачи, используя средства HDInsight в наборе средств Azure для IntelliJ.

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью средств HDInsight в наборе средств Azure для IntelliJ
С помощью средств HDInsight, которые являются частью набора средств Azure для IntelliJ, можно выполнять различные операции.

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>Доступ к представлению заданий непосредственно из средств HDInsight
1. В **Azure Explorer** разверните **HDInsight**, а затем выберите имя кластера Spark, после чего щелкните **Задания**.
2. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Щелкните имя приложения, дополнительные сведения о котором вы хотите просмотреть.
   
    ![Доступ к представлению заданий](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. Поля **Сообщение об ошибке**, **Job Output** (Выходные данные задания), **Livy Job Logs** (Журналы заданий Livy) и **Spark Driver Logs** (Журналы драйвера Spark) заполняются в соответствии с выбранным приложением.
4. Кроме того, можно открыть **пользовательский интерфейс журнала Spark** и **пользовательский интерфейс YARN** (на уровне приложения), нажав соответствующие кнопки в верхней части экрана.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark
1. В **Azure Explorer** разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.
2. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называлось **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Запуск портала Ambari
В **Azure Explorer** разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure
По умолчанию средства HDInsight содержат список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры из которых вас интересуют. В **Azure Explorer** щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите пункт **Управление подписками**. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, нажмите кнопку **Выйти** .

## <a name="run-a-spark-scala-application-locally"></a>Запуск приложения Spark Scala на локальном компьютере
Средства HDInsight в наборе средств Azure для IntelliJ позволяют запускать приложения Spark Scala локально на рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### <a name="prerequisite"></a>Предварительные требования
При запуске локального приложения Spark Scala на компьютере под управлением Windows может возникнуть исключение, описанное в [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) и связанное с отсутствием в Windows файла WinUtils.exe. Чтобы обойти эту ошибку, [скачайте этот исполняемый файл отсюда](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например в папку **C:\WinUtils\bin**. После этого добавьте переменную среды **HADOOP_HOME** и присвойте ей значение **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Запуск локального приложения Spark Scala
1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next**(«Далее»).
   
    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
   
   * В левой области выберите **HDInsight**.
   * В правой области выберите **Spark on HDInsight Local Run Sample (Scala)**(Пример локального запуска Spark в HDInsight (Scala)).
   * Нажмите кнопку **Далее**.
2. В следующем окне укажите сведения о проекте.
   
   * Введите имя и расположение проекта.
   * Для **пакета SDK проекта**требуется версия Java выше седьмой.
   * Для **пакета SDK для Scala** щелкните **Create** (Создать), а затем **Download** (Скачать), после чего выберите версию Scala. **Версия Scala 2.11.x предназначена для Spark 2.0, а Scala 2.10.x — для Spark 1.6**.
     
       ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * Для **пакета SDK для Spark** скачайте и используйте пакет SDK [отсюда](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Кроме того, можно использовать [репозиторий Spark Maven](http://mvnrepository.com/search?q=spark), однако убедитесь, что у вас установлен нужный репозиторий Maven для разработки приложений Spark. (Например, при использовании потоковой передачи Spark необходимо убедиться, что установлена часть Spark Streaming. Кроме того, убедитесь, что для кластера Spark 1.6 используется репозиторий, помеченный как Scala 2.10, а для кластера Spark 2.0 — репозиторий, помеченный как Scala 2.11).
     
       ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
   * Нажмите кнопку **Готово**
3. Шаблон добавляет пример кода (**LogQuery**) в папку **src**, который можно запустить локально на компьютере.
   
    ![Локальное приложение Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Щелкните правой кнопкой мыши приложение **LogQuery** и выберите пункт **Run LogQuery** (Запустить LogQuery). В нижней части вкладки **Запуск** вы увидите выходные данные следующего вида.
   
   ![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Преобразование существующих приложений IntelliJ IDEA для использования средств HDInsight в наборе средств Azure для IntelliJ
Существующие приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми со средствами HDInsight в наборе средств Azure для IntelliJ. В результате вы сможете использовать средство для отправки приложений в кластер HDInsight Spark. Для этого выполните следующие действия.

1. Для существующего приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий файл IML.
2. На корневом уровне вы увидите элемент **module** следующего вида.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
3. Измените элемент, добавив `UniqueKey="HDInsightTool"` , чтобы элемент **module** выглядел следующим образом.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
4. Сохраните изменения. Ваше приложение станет совместимым со средствами HDInsight в наборе средств Azure для IntelliJ. Это можно проверить, щелкнув имя проекта в обозревателе проектов правой кнопкой мыши. Во всплывающем меню должен появиться пункт **Submit Spark Application to HDInsight**(Отправить приложение Spark в HDInsight).

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>Ошибка "Используйте больший размер кучи" в локальной среде
В Spark 1.6 при использовании 32-разрядного пакета SDK для Java в локальной среде выполнения могут возникать следующие ошибки:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Это связано с тем, что размер кучи недостаточно велик для запуска Spark, так как требуется не меньше 471 МБ (дополнительные сведения см. в статье о [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)). Простое решение – это использовать 64-разрядный пакет SDK для Java. Кроме того, можно изменить параметры виртуальной машины Java в IntelliJ, добавив следующие параметры:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>Отзывы и известные проблемы
В настоящее время просмотр выходных данных Spark напрямую не поддерживается, и мы работаем над этим.

С любыми отзывами и предложениями (а также в случае возникновения проблем при работе с этим средством) обращайтесь по адресу электронной почты hdivstool@microsoft.com.

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

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
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Jan17_HO4-->


