---
title: Набор средств Azure для IntelliJ. Создание приложений Spark для кластера HDInsight | Документация Майкрософт
description: Сведения о разработке приложений Spark на языке Scala и их отправке в кластер HDInsight Spark с помощью набора средств Azure для IntelliJ.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/25/2017
ms.author: maxluk,jejiang
ms.openlocfilehash: cb78808b515bb3385f7cf56725441a2b228f0aba
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164896"
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ

Подключаемый модуль из набора средств Azure для IntelliJ позволяет разрабатывать приложения Spark на языке Scala и отправлять их в кластер HDInsight Spark непосредственно из интегрированной среды разработки IntelliJ (IDE). С помощью подключаемого модуля можно выполнять следующие действия:

* разрабатывать и отправлять приложения Scala Spark в кластер HDInsight Spark;
* получать доступ к ресурсам кластера Azure HDInsight Spark;
* разрабатывать и запускать приложения Scala Spark в локальной среде.

Чтобы создать проект, просмотрите видео о [создании приложений Spark с помощью набора средств Azure для IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ).

> [!IMPORTANT]
> Вы можете использовать этот подключаемый модуль, чтобы создавать и отправлять приложения только в кластер HDInsight Spark на Linux.
> 

## <a name="prerequisites"></a>предварительным требованиям

- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
- Комплект разработчика Oracle Java. Его можно установить с [веб-сайта Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. В этой статье используется версия 2017.1. Его можно установить с [веб-сайта JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Установка набора средств Azure для IntelliJ
Инструкции по установке см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="get-started"></a>Начало работы
Пользователь может [войти в подписку Azure](#sign-in-to-your-azure-subscription) или [связать кластер HDInsight](#link-a-cluster) с помощью имени пользователя и пароля Ambari или учетных данных присоединения к домену.


## <a name="sign-in-to-your-azure-subscription"></a>Войдите в свою подписку Azure.

1. Запустите IntelliJ IDE и откройте Azure Explorer. В меню **View** (Вид) выберите **Tool Windows** (Окна инструментов) и выберите **Azure Explorer**.
       
   ![Ссылка на Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Щелкните правой кнопкой мыши узел **Azure**, а затем выберите **Sign In** (Войти).

3. В диалоговом окне **Azure Sign In** (Вход в Azure) нажмите кнопку **Sign in** (Войти), а затем введите свои учетные данные Azure.

    ![Диалоговое окно входа в Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Нажмите кнопку **Select** (Выбрать).

    ![Диалоговое окно выбора подписок](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. На вкладке **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.
   
    ![Кластеры HDInsight Spark в Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.
   
    ![Развернутый узел имени кластера](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">Связывание кластера</h2>
Вы можете связать обычный кластер с помощью управляемого имени пользователя Ambari, а кластер безопасности — с помощью имени пользователя домена (например, user1@contoso.com). 
1. Щелкните **Link a cluster** (Связывание кластера) в **обозревателе Azure**.

   ![контекстное меню связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Введите **имя кластера**, **имя пользователя** и **пароль**. В случае сбоя аутентификации проверьте имя пользователя и пароль. При необходимости добавьте учетную запись хранения, ключ хранилища, затем выберите контейнер из контейнера хранилища. Информация о хранилище предназначена для обозревателя хранилищ, который находится в дереве слева
   
   ![диалоговое окно связывания кластера](./media/apache-spark-intellij-tool-plugin/link-a-cluster-dialog.png)

   > [!NOTE]
   > Если кластер зарегистрирован в подписке Azure и связан, используется ключ к хранилищу данных, имя пользователя и пароль для связывания.
   > ![обозреватель хранилищ в IntelliJ](./media/apache-spark-intellij-tool-plugin/storage-explorer-in-IntelliJ.png)

   
3. Если данные введены правильно, связанный кластер отобразится в узле **HDInsight**. Теперь в этот связанный кластер можно отправить приложение.

   ![связанный кластер](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. В **обозревателе Azure** также можно удалить связь кластера.
   
   ![кластер с удаленной связью](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Запуск приложения Spark Scala в кластере HDInsight Spark

1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне **Новый проект** сделайте следующее: 

   a. Выберите **HDInsight** > **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).

   Б. В списке **средств сборки** выберите один из следующих вариантов:

      * **Maven.** Для поддержки мастера создания проекта Scala.
      * **SBT.** Для управления зависимостями и создания проекта Scala.

    ![Диалоговое окно нового проекта](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Щелкните **Далее**.

3. Мастер создания проектов Scala автоматически определяет, установлен ли подключаемый модуль Scala. Щелкните **Установить**.

   ![Проверка подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Чтобы скачать подключаемый модуль Scala, нажмите кнопку **ОК**. Следуйте инструкциям, чтобы перезапустить IntelliJ. 

   ![Диалоговое окно установки подключаемого модуля Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. В окне **нового проекта** сделайте следующее:  

    ![Выбор пакета SDK для Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Введите имя и расположение проекта.

   Б. В раскрывающемся списке для параметра **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера Spark 2.x или **Java 1.7** для кластера Spark 1.x.

   c. В раскрывающемся списке для параметра **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.0.2 (Scala 2.11.8)**.

6. Выберите **Готово**.

7. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, сделайте следующее:

   a. В меню **File** (Файл) выберите **Project Structure** (Структура проекта).

   Б. В диалоговом окне **Project Structure** (Структура проекта) щелкните **артефакты**, чтобы просмотреть созданный по умолчанию артефакт. Вы также можете создать собственный артефакт, щелкнув значок "плюс" (**+**).

      ![Сведения об артефакте в диалоговом окне](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Добавьте исходный код приложения, сделав следующее:

   a. В обозревателе проектов щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать) и выберите **Scala Class** (Класс Scala).
      
      ![Команды для создания класса Scala в обозревателе проектов](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   Б. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Kind** (Вид) выберите **Object** (Объект) и нажмите кнопку **ОК**.
      
      ![Диалоговое окно создания класса Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. Вставьте в файл **MyClusterApp.scala** следующий код. Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в используемом по умолчанию контейнере хранилища для кластера.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. Запустите приложение в кластере HDInsight Spark следующим образом:

   a. В обозревателе проектов щелкните имя проекта правой кнопкой мыши и выберите **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).
      
      ![Команда Submit Spark Application to HDInsight (Отправить приложение Spark в HDInsight)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   Б. Вам будет предложено ввести учетные данные подписки Azure. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения и нажмите кнопку **Submit** (Отправить).
      
      * В поле **Spark clusters (Linux only)**(Кластеры Spark (только для Linux)) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.

      * Выберите артефакт из проекта IntelliJ или с жесткого диска.

      * В текстовом поле **Main class name** (Имя основного класса) щелкните многоточие (**...**), выберите основной класс в исходном коде приложения и нажмите кнопку **ОК**.

        ![Диалоговое окно выбора основного класса](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Так как для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять. После ввода необходимой информации диалоговое окно должно выглядеть как на следующем изображении.
        
        ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. На вкладке **Spark Submission** (Отправка в Spark) в нижней части окна начнет отображаться ход выполнения. Приложение также можно остановить, выбрав красную кнопку в окне **Spark Submission** (Отправка в Spark).
      
     ![Окно Spark Submission (Отправка в Spark)](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      В разделе "Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ" ниже в этой статье показано, как получить доступ к выходным данным задачи.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Локальная и удаленная отладка приложений Spark в кластере HDInsight 
Мы также рекомендуем еще один способ отправки приложения Spark в кластер. Он заключается в задании параметров **конфигураций запуска и отладки** в интегрированной среде разработки. Дополнительные сведения см. в статье [Отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью набора средств Azure для IntelliJ
При помощи набора средств Azure для IntelliJ можно выполнять разные операции.

### <a name="access-the-job-view"></a>Доступ к представлению задания
1. В Azure Explorer разверните **HDInsight**, а затем выберите имя кластера Spark, после чего щелкните **Задания**.  

    ![Узел представления задания](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Выберите имя приложения, дополнительные сведения о котором вы хотите просмотреть.

    ![Сведения о приложении](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >Примечание
    >

3. Чтобы отобразить базовые сведения о выполняющемся задании, наведите указатель мыши на граф задания. Чтобы просмотреть этапы графа и сведения, создаваемые каждым заданием, выберите узел на графе задания.

    ![Сведения об этапе задания](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Выберите вкладку **Журнал**, чтобы просмотреть часто используемые журналы, включая *Driver Stderr*, *Driver Stdout* и *Directory Info*.

    ![Подробные сведения журнала](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Вы также можете просмотреть пользовательский интерфейс журнала Spark и пользовательский интерфейс YARN (на уровне приложения), щелкнув ссылку в верхней части окна.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark
1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). 

2. При появлении запроса введите учетные данные администратора для кластера, указанные при настройке кластера.

3. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено, по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называется **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Запуск портала Ambari
1. В Azure Explorer разверните **HDInsight**, щелкните имя кластера Spark правой кнопкой мыши и выберите **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). 

2. При появлении запроса введите учетные данные администратора для кластера. Вы указали эти учетные данные во время установки кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure
По умолчанию набор средств Azure для IntelliJ содержит список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, к которым необходимо получить доступ. 

1. В Azure Explorer щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите **Управление подписками**. 

2. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и выберите **Закрыть**. Если вы хотите выйти из своей подписки Azure, выберите **Выйти**.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Преобразование имеющихся приложений IntelliJ IDEA для использования набора средств Azure для IntelliJ
Имеющиеся приложения Spark Scala, созданные в IntelliJ IDEA, можно преобразовать и сделать совместимыми с набором средств Azure для IntelliJ. Затем вы сможете использовать подключаемый модуль для отправки приложений в кластер HDInsight Spark.

1. Для имеющегося приложения Spark Scala, созданного с помощью IntelliJ IDEA, откройте соответствующий IML-файл.

2. На корневом уровне вы увидите элемент **module** следующего вида:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Измените элемент, добавив `UniqueKey="HDInsightTool"` , чтобы элемент **module** выглядел следующим образом.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Сохраните изменения. Ваше приложение станет совместимым с набором средств Azure для IntelliJ. Это можно проверить, щелкнув имя проекта в обозревателе проектов правой кнопкой мыши. Во всплывающем меню должен появиться пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Ошибка *Please use a larger heap size* (Используйте больший размер кучи) в локальной среде
В Spark 1.6 при использовании 32-разрядного пакета SDK для Java в локальной среде выполнения могут возникать следующие ошибки:

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

Это связано с тем, что размер кучи недостаточно велик для запуска Spark. Для Spark требуется не меньше 471 МБ. (Дополнительные сведения см. в статье о [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081).) Простое решение – это использовать 64-разрядный пакет SDK для Java. Кроме того, можно изменить параметры виртуальной машины Java в IntelliJ, добавив следующие параметры:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Добавление параметров в поле VM options (Параметры виртуальной машины) в IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>Часто задаваемые вопросы
Рекомендуем при связывании кластера предоставить учетные данные хранилища.

![Связка кластера, предоставление учетных данных хранилища](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

Существует два режима отправки заданий. Если учетные данные хранилища указаны, для отправки задания будет использоваться пакетный режим. В противном случае будет использоваться интерактивный режим. Если кластер занят, может появиться приведенная ниже ошибка.

![IntelliJ получает сообщение об ошибке, если кластер занят](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![IntelliJ получает сообщение об ошибке, если кластер занят](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы
Сейчас просмотр выходных данных Spark напрямую не поддерживается.

С любыми отзывами и предложениями, а также в случае возникновения проблем при работе с этим подключаемым модулем вы можете отправить сообщение по электронному адресу hdivstool@microsoft.com.

## <a name="seealso"></a>Дальнейшие действия
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight](apache-spark-use-bi-tools.md)
* [Создание приложений машинного обучения Apache Spark в Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
