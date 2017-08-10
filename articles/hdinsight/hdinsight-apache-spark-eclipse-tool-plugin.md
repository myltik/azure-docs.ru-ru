---
title: "Создание приложений Scala для HDInsight Spark с помощью набора средств Azure для Eclipse | Документация Майкрософт"
description: "Использование средств HDInsight из набора средств Azure для Eclipse для разработки приложений Spark на языке Scala и их отправки в кластер HDInsight Spark непосредственно из интегрированной среды разработки Eclipse."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: a921eeabe0df6dbc32ab62f74fe585ac2eaf9d42
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для Eclipse

Использование средств HDInsight из набора средств Azure для Eclipse для разработки приложений Spark на языке Scala и их отправки в кластер Azure HDInsight Spark непосредственно из интегрированной среды разработки Eclipse. Подключаемый модуль средств HDInsight можно использовать по-разному.

* Для разработки и отправки приложений Scala Spark в кластер HDInsight Spark.
* Для доступа к ресурсам кластера Azure HDInsight Spark.
* Для разработки и локального запуска приложений Scala Spark.

> [!IMPORTANT]
> Это средство позволяет создавать и отправлять приложения только в кластер HDInsight Spark на Linux.
> 
> 

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java версии 8, который используется для среды выполнения интегрированной среды разработки Eclipse. Его можно скачать с [веб-сайта Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Интегрированная среда разработки Eclipse. В этой статье используется Eclipse Neon. Его можно установить с [веб-сайта Eclipse](https://www.eclipse.org/downloads/).
* Интегрированная среда разработки Scala для Eclipse. 
  
  * **Если у вас установлена интегрированная среда разработки Eclipse**, то можно добавить подключаемый модуль интегрированной среды разработки Scala, выбрав **Help** (Справка) > **Install New SoftWare** (Установить новое программное обеспечение) и указав [http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site) в качестве источника для скачивания подключаемого модуля Scala для Eclipse. 
  * **Если у вас не установлена интегрированная среда разработки Eclipse**, то ее можно установить непосредственно с [веб-сайта Scala](http://scala-ide.org/download/sdk.html). Скачайте ZIP-файл, извлеките его содержимое, перейдите в папку **/eclipse** и выполните файл **eclipse.exe**.
    
    > [!NOTE]
    > В этой статье описаны действия для интегрированной среды разработки Eclipse с установленным подключаемым модулем Scala.
    > 
    > 
* Пакет SDK для Spark. Скачать эту версию можно с [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
* e(fx)clipse. Его можно установить с [веб-сайта Eclipse](https://www.eclipse.org/efxclipse/install.html).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Установка средств HDInsight в наборе средств Azure для Eclipse
Средства HDInsight для Eclipse доступны в составе набора средств Azure для Eclipse. Инструкции по установке см. в статье [Установка набора средств Azure для Eclipse](../azure-toolkit-for-eclipse-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Войдите в свою подписку Azure.
1. Запустите интегрированную среду разработки Eclipse и откройте Azure Explorer. В меню **Window** (Окно) выберите пункт **Show View** (Показать представление) и щелкните **Other** (Другие). В открывшемся диалоговом окне разверните **Azure** и щелкните **Azure Explorer**, а затем нажмите кнопку **ОК**.

    ![Диалоговое окно Show View (Показать представление)](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Щелкните правой кнопкой мыши узел **Azure**, а затем выберите пункт **Sign in** (Войти).
3. В диалоговом окне **Azure Sign In** (Вход в Azure) выберите режим проверки подлинности, нажмите кнопку **Sign in** (Войти) и введите свои учетные данные Azure.
   
    ![Диалоговое окно входа в Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. После входа в диалоговом окне **Select Subscriptions** (Выбор подписок) будут перечислены все подписки Azure, связанные с указанными учетными данными. Щелкните **Select** (Выбрать), чтобы закрыть диалоговое окно.

    ![Диалоговое окно выбора подписок](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. На вкладке **Azure Explorer** разверните **HDInsight**, чтобы просмотреть кластеры HDInsight Spark в своей подписке.
   
    ![Кластеры HDInsight Spark в Azure Explorer](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Далее можно развернуть узел имени кластера, чтобы увидеть ресурсы (например, учетные записи хранения), связанные с ним.
   
    ![Развертывание имени кластера для просмотра ресурсов](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Настройка проекта Spark Scala для кластера HDInsight Spark

1. В рабочей области интегрированной среды разработки Eclipse щелкните **File** (Файл), **New** (Создать), **Project** (Проект). 
2. В мастере New Project (Новый проект) разверните **HDInsight**, выберите **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)) и нажмите кнопку **Далее**.

    ![Выбор проекта Spark в HDInsight (Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. В диалоговом окне **New HDInsight Scala Project** (Новый проект Scala HDInsight) введите следующие значения, после чего нажмите кнопку **Далее**.
   * Введите имя проекта.
   * Убедитесь, что в поле **JRE** параметр **Use an execution environment JRE** (Использовать среду выполнения JRE) имеет значение **JavaSE-1.7** или более позднюю версию.
   * Убедитесь, что для пакета SDK для Spark задано расположение, в которое вы скачали этот пакет SDK. Ссылка для скачивания указана в разделе [Предварительные требования](#prerequisites) ранее в этой статье. Можно также скачать пакет SDK по ссылке в этом диалоговом окне.

    ![Диалоговое окно нового проекта Scala HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
4.  В следующем диалоговом окне щелкните вкладку **Libraries** (Библиотеки) и оставьте значения по умолчанию, а затем щелкните **Finish**  (Готово). 
   
    ![Вкладка Libraries (Библиотеки)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)


## <a name="run-a-spark-scala-application-on-an-azure-data-lake-store-cluster"></a>Запуск приложения Spark Scala в кластере Azure Data Lake Store
Если вы хотите отправить приложение в Azure Data Lake Store, необходимо выбрать **интерактивный** режим во время входа в Azure. 

   ![Параметр "Интерактивный" при входе](./media/hdinsight-apache-spark-eclipse-tool-plugin/Interactive-Authentication.png)

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Создание приложения Scala для кластера HDInsight Spark

1. В интегрированной среде разработки Eclipse в обозревателе пакетов разверните созданный ранее проект, щелкните правой кнопкой мыши **src**, выберите **New** (Создать) и щелкните **Other** (Другое).
2. В диалоговом окне **Select a wizard** (Выбор мастера) разверните **Scala Wizards** (Мастера Scala), щелкните **Scala Object** (Объект Scala) и нажмите кнопку **Далее**.
   
    ![Диалоговое окно выбора мастера](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. В диалоговом окне **Create New File** (Создание файла) введите имя объекта и нажмите кнопку **Готово**.
   
    ![Диалоговое окно создания нового файла](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Вставьте следующий код в текстовый редактор.
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Запустите приложение в кластере HDInsight Spark.
   
   1. В обозревателе пакетов щелкните имя проекта правой кнопкой мыши и выберите пункт **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).        
   2. В диалоговом окне **Spark Submission** (Отправка в Spark) введите следующие значения и нажмите кнопку **Submit** (Отправить).
      
      * В поле **Cluster Name**(Имя кластера) выберите кластер HDInsight Spark, в котором вы хотите запустить приложение.
      * Выберите артефакт из проекта Eclipse или с жесткого диска. Значение по умолчанию зависит от элемента, который вы щелкнете правой кнопкой мыши в обозревателе пакетов.
      * В раскрывающемся списке **Main class name** (Имя класса main) в мастере отправки отображаются имена всех объектов из выбранного проекта. Выберите или введите имя любого из них, который требуется запустить. При выборе артефакта с жесткого диска необходимо указать имя класса main самостоятельно. 
      * Так как для кода приложения в этом примере не требуются аргументы командной строки, справочные JAR или файлы, остальные текстовые поля можно не заполнять.
        
       ![Диалоговое окно Spark Submission (Отправка в Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
   3. На вкладке **Spark Submission** (Отправка в Spark) начнет отображаться ход выполнения. Вы можете остановить приложение, щелкнув красную кнопку в окне **Spark Submission** (Отправка в Spark). Можно также просмотреть журналы для данного запуска приложения, щелкнув значок глобуса (обозначен на рисунке синей рамкой).
      
       ![Окно Spark Submission (Отправка в Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Доступ к кластерам HDInsight Spark и управление ими с помощью средств HDInsight в наборе средств Azure для Eclipse
С помощью средств HDInsight можно выполнять различные операции, включая доступ к выходным данным заданий.

### <a name="access-the-job-view"></a>Доступ к представлению задания
1. В Azure Explorer разверните **HDInsight**, а затем выберите имя кластера Spark, после чего щелкните **Задания**.  
       ![Узел представления задания](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)
2. В области справа на вкладке **Spark Job View** (Просмотр заданий Spark) отображаются все приложения, запускаемые в кластере. Щелкните имя приложения, дополнительные сведения о котором вы хотите просмотреть.
       ![Сведения о приложении](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. Наведите указатель мыши на граф задания. Отобразятся базовые сведения о выполняющемся задании. Щелкнув граф задания, можно просмотреть этапы графа и сведения, создаваемые каждым заданием.
       ![Сведения об этапе задания](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Часто используемые журналы, включая Driver Stderr, Driver Stdout и Directory Info, приведены на вкладке **Журнал**.
       ![Подробные сведения журнала](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)
5. Кроме того, вы можете открыть пользовательский интерфейс журнала Spark и пользовательский интерфейс YARN (на уровне приложения), щелкнув соответствующую ссылку в верхней части окна.

### <a name="access-the-storage-container-for-the-cluster"></a>Доступ к контейнеру хранилища для кластера
1. В Azure Explorer разверните корневой узел **HDInsight**, чтобы увидеть список доступных кластеров HDInsight Spark.
2. Разверните имя кластера, чтобы увидеть учетную запись хранилища и контейнер хранилища по умолчанию для кластера.
   
    ![Учетная запись хранения и контейнер по умолчанию](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Щелкните имя связанного с кластером контейнера хранилища. В области справа дважды щелкните папку **HVACOut**. Откройте один из файлов **part-** для просмотра выходных данных приложения.

### <a name="access-the-spark-history-server"></a>Доступ к серверу журнала Spark
1. В Azure Explorer щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Spark History UI** (Открыть пользовательский интерфейс журнала Spark). При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.
2. На панели мониторинга сервера журнала Spark вы сможете найти приложение, выполнение которого только что было завершено, по его имени. В приведенном выше коде имя приложения было указано с помощью `val conf = new SparkConf().setAppName("MyClusterApp")`. Следовательно, приложение Spark называлось **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Запуск портала Ambari
1. В Azure Explorer щелкните имя кластера Spark правой кнопкой мыши и выберите пункт **Open Cluster Management Portal (Ambari)** (Открыть портал управления кластерами (Ambari)). 
2. При появлении запроса введите учетные данные администратора для кластера. Вы должны были указать их при подготовке кластера.

### <a name="manage-azure-subscriptions"></a>Управление подписками Azure
По умолчанию средства HDInsight в наборе средств Azure для Eclipse содержат список кластеров Spark из всех ваших подписок Azure. При необходимости можно указать подписки, кластеры из которых вас интересуют. 

1. В Azure Explorer щелкните правой кнопкой мыши корневой узел **Azure**, а затем выберите пункт **Управление подписками**. 
2. В диалоговом окне снимите флажки напротив подписок, доступ к которым вам не требуется, и нажмите кнопку **Закрыть**. Если вы хотите выйти из своей подписки Azure, нажмите кнопку **Выйти**.

## <a name="run-a-spark-scala-application-locally"></a>Запуск приложения Spark Scala на локальном компьютере
Средства HDInsight в наборе средств Azure для Eclipse позволяют запускать приложения Spark Scala локально на рабочей станции. Как правило, такие приложения не требуют доступа к ресурсам кластера, таким как контейнер хранилища, и могут запускаться и тестироваться локально.

### <a name="prerequisite"></a>Предварительные требования
При запуске локального приложения Spark Scala на компьютере с Windows может возникнуть исключение, описанное в [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Это исключение возникает, так как в Windows отсутствует файл **WinUtils.exe**. 

Чтобы устранить эту ошибку, [скачайте этот исполняемый файл](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например в папку **C:\WinUtils\bin**. После этого добавьте переменную среды **HADOOP_HOME** и присвойте ей значение **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Запуск локального приложения Spark Scala
1. Запустите Eclipse и создайте новый проект. В диалоговом окне **нового проекта** установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Далее**.
   
   * В левой области выберите **HDInsight**.
   * В правой области выберите **Spark on HDInsight Local Run Sample (Scala)** (Пример локального запуска Spark в HDInsight (Scala)).

    ![Диалоговое окно "Новый проект"](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
2. Чтобы предоставить сведения о проекте, выполните шаги 3–6, как описано в разделе [Настройка проекта Spark Scala для кластера HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark cluster).
3. Шаблон добавляет пример кода (**LogQuery**) в папку **src**, который можно запустить локально на компьютере.
   
    ![Расположение LogQuery](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. Щелкните правой кнопкой мыши приложение **LogQuery**, выберите пункт **Run As** (Запустить от имени) и щелкните **1 Scala Application** (1. Приложение Scala). В нижней части вкладки **Console** (Консоль) вы увидите выходные данные следующего вида.
   
   ![Результат локального запуска приложения Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback-and-known-issues"></a>Отзывы и известные проблемы
Сейчас просмотр выходных данных Spark напрямую не поддерживается.

С любыми отзывами и предложениями (а также в случае возникновения проблем при работе с этим инструментом) обращайтесь по электронному адресу hdivstool@microsoft.com.

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)


