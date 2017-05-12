---
title: "Потоковая передача данных из концентраторов событий с помощью Apache Spark в Azure HDInsight | Документация Майкрософт"
description: "Пошаговые инструкции по отправке потока данных в концентратор событий Azure и последующего получения этих событий в HDInsight Spark с помощью приложения Scala"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 91c60e944dd3b72f5bf1137d93ba2ae70537b2f7
ms.contentlocale: ru-ru
ms.lasthandoff: 03/29/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Потоковая передача Spark. Обработка событий из концентраторов событий Azure с помощью кластера Apache Spark в HDInsight

В этой статье вы ознакомитесь с некоторыми концепциями потоковой передачи с помощью Apache Spark и создадите решение для потоковой передачи, выполняющее следующие действия.

1. Автономное приложение принимает сообщения в концентратор событий Azure.

2. Сообщения извлекаются из концентратора событий в режиме реального времени с помощью приложения, работающего в кластере Spark в Azure HDInsight.

3. Полученные данные передаются в несколько назначений, например в хранилище больших двоичных объектов Azure, в таблицу Hive или таблицу SQL. 

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Основные понятия потоковой передачи Spark

Подробное описание обработки потоковой передачи в Apache Spark см. [в этой статье](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight предоставляет аналогичные функции потоковой передачи для кластера Spark в Azure.  

## <a name="what-does-this-solution-do"></a>Каково предназначение этого решения?

В этой статье вы выполните следующие действия, чтобы создать решение для потоковой передачи.

1. Создание концентратора событий Azure, который будет принимать поток событий.

2. Запуск локального автономного приложения, которое создает события и передает их в концентратор событий Azure. Пример такого приложения опубликован по адресу [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Удаленный запуск приложения потоковой передачи в кластере Spark. Это приложение считывает события потоковой передачи из концентратора событий Azure и отправляет их в различные расположения (большой двоичный объект службы хранилища Azure, таблицу Hive и таблицу базы данных SQL). 

## <a name="create-azure-event-hub"></a>Создание концентратора событий Azure

1. Войдите на [портал Azure](https://manage.windowsazure.com) и щелкните **Создать** вверху слева.

2. Последовательно выберите **Интернет вещей** и **Концентраторы событий**.
   
    ![Создание концентратора событий](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub9.png)

3. В колонке **Создание пространства имен** укажите имя пространства имен. выберите ценовую категорию ("Базовый" или "Стандартный"). Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса. Щелкните **Создать** , чтобы создать пространство имен.
   
    ![Создание концентратора событий](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub1.png)

    > [!NOTE]
       > Для сокращения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится кластер Apache Spark в HDInsight.
       > 
       > 

4. В списке пространств имен концентраторов событий щелкните созданное пространство имен.      
   
    
5. В колонке пространства имен щелкните **Концентраторы событий**, а затем **+Концентратор событий**, чтобы создать новый концентратор событий.
   
    ![Создание концентратора событий](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub3.png)

6. Введите имя для концентратора событий; для параметра с числом разделов установите значение 10, а для хранения сообщений — 1. Мы не будем сохранять сообщения в этом решении, поэтому остальные параметры можно не изменять. Просто нажмите кнопку **Создать**.
   
    ![Создание концентратора событий](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub5.png)

7. Только что созданный концентратор событий отобразится в колонке "Концентратор событий".
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub6.png)

8. В колонке пространства имен (не в конкретной колонке концентратора событий) щелкните **Политики общего доступа**, а затем нажмите щелкните **RootManageSharedAccessKey**.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub7.png)

9. Нажмите кнопку копирования, чтобы скопировать первичный ключ и строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните их, чтобы использовать на следующих этапах работы с этим руководством.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub8.png)

## <a name="send-messages-to-an-azure-event-hub-using-a-scala-application"></a>Отправка сообщений в концентратор событий с помощью приложения Scala

В этом разделе вы примените локальное автономное приложение Scala для создания потока событий и отправки его в концентратор событий Azure, созданный на предыдущем шаге. Это приложение доступно на сайте GitHub по адресу [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Здесь предполагается, что в репозитории GitHub уже созданы разветвления.

1. Убедитесь, что на компьютере, где выполняется это приложение, установлены следующие компоненты.

    * Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Java IDE. В этой статье используется среда IntelliJ IDEA 15.0.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).


2. Откройте приложение **EventhubsSampleEventProducer**в IntelliJ IDEA.

3. Создайте проект. В меню **Сборка** выберите пункт **Make Project** (Создать проект). В зависимости от конфигурации IntelliJ IDEA выходной JAR-файл будет создан в разделе **\classes\artifacts**.

    > [!TIP]
    > Можно создать проект непосредственно из репозитория GitHub с помощью параметра в IntelliJ IDEA. Чтобы понять, как использовать этот подход, следуйте инструкциям в следующем разделе. Обратите внимание, что большинство действий, описанных в следующем разделе, не применимы для приложения Scala, создаваемого на этом шаге. Например:
    > 
    > * Не требуется обновлять модель объекта проекта, чтобы добавить в нее версию Spark. Это связано с тем, что при создании этого приложения не используется Spark.
    > * Не требуется добавлять JAR-файлы зависимостей в библиотеку проекта. Такие JAR-файлы не нужны для этого проекта.
    > 
    > 

## <a name="receive-messages-from-the-event-hub-using-a-streaming-application-running-on-spark-cluster"></a>Получение сообщений от концентратора событий с помощью приложения потоковой передачи, работающего в кластере Spark

Пример приложения Scala для приема событий и их перенаправления в различные места назначения доступен по адресу [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Выполните действия ниже, чтобы обновить приложение и создать выходной JAR-файл.

1. Запустите IntelliJ IDEA и на экране запуска щелкните **Check out from Version Control** (Извлечь из системы управления версиями) и выберите пункт **Git**.
   
    ![Получение источников из Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. В диалоговом окне **Clone Repository** (Клонирование репозитория) введите URL-адрес репозитория Git, который нужно клонировать, укажите каталог, в который будет выполняться клонирование, а затем нажмите кнопку **Clone** (Клонировать).
   
    ![Клонирование из Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. Следуйте инструкциям на экране, пока проект полностью не клонируется. Нажмите клавиши **ALT+1**, чтобы открыть **представление проекта**. Оно должно выглядеть так:
   
    ![представление проекта](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. Скомпилируйте код приложения с помощью Java 8. Чтобы сделать это, выберите **File** (Файл), **Project Structure** (Структура проекта) и на вкладке **Project** (Проект) в поле Project language level (Уровень языка проекта) установите значение **8 - Lambdas, type annotations, etc.** (8 — лямбды, аннотации типа и т. д.).
   
    ![Структура проекта](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. Откройте узел **pom.xml** , чтобы убедиться, что используется правильная версия Spark. В узле `<properties>` найдите фрагмент кода ниже и проверьте версию Spark.
   
        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. Приложению требуется JAR-файл зависимостей, а точнее **JAR-файл драйвера JDBC**. Этот файл необходим для записи сообщений, полученных из концентратора событий, в базу данных SQL Azure. Этот JAR-файл версии 4.1 или более поздней можно скачать [здесь](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Добавьте ссылку на этот JAR-файл в библиотеке проекта. Выполните следующие действия:
     
     1. В окне IntelliJ IDEA, где открыто приложение, щелкните **File** (Файл), выберите **Project Structure** (Структура проекта) и щелкните **Libraries** (Библиотеки). 
     2. Щелкните значок "Добавить" (![добавление значка](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), выберите **Java**и перейдите в папку, куда вы скачали JAR-файл драйвера JDBC. Следуйте инструкциям, чтобы добавить JAR-файл в библиотеку проектов.
        
         ![добавление отсутствующих зависимостей](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Добавление отсутствующих JAR-файлов зависимостей")
     3. Нажмите кнопку **Применить**.
7. Создайте выходной JAR-файл. Выполните следующие действия.
   
   1. В диалоговом окне **Project Structure** (Структура проекта) выберите **Artifacts** (Артефакты) и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем выберите **From modules with dependencies** (На основе модулей с зависимостями).
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) нажмите кнопку с многоточием (![многоточие](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) возле пункта **Main Class** (Основной класс).
   3. В диалоговом окне **Select Main Class** (Выбор основного класса) выберите любой из доступных классов и нажмите кнопку **ОК**.
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) установите переключатель **extract to the target JAR** (Извлечь в целевой JAR-файл) и нажмите кнопку **ОК**. В результате будет создан один JAR-файл, содержащий все зависимости.
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. На вкладке **Макет выходных данных** содержится список всех JAR-файлов, которые включены в проект Maven. Здесь можно выбрать и удалить файлы, от которых не зависит работа приложения Scala. Для создаваемого приложения можно удалить все файлы, кроме последнего (**microsoft-spark-streaming-examples compile output**(«Выходные данные компиляции microsoft-spark-streaming-examples»)). Выберите JAR-файлы, которые нужно удалить, и щелкните значок **удаления** (![значок удаления](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       Установите флажок **Build on make** («Сборка на основе созданного»), чтобы JAR-файл создавался при каждом создании и обновлении проекта. Нажмите кнопку **Применить**.
   6. На вкладке **Output Layout** (Макет выходных данных) под полем **Available Elements** (Доступные элементы) отображается JAR-файл SQL JDBC, ранее добавленный в библиотеку проекта. Его необходимо добавить на вкладку **Output Layout** (Макет выходных данных). Щелкните JAR-файл правой кнопкой мыши и выберите пункт **Extract Into Output Root**(Извлечь в корень выходных данных).
      
       ![Извлечение JAR-файла зависимостей](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       Вкладка **макета выходных данных** теперь должна выглядеть следующим образом:
      
       ![Вкладка окончательных выходных данных](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       В диалоговом окне **Project Structure** (Структура проекта) нажмите кнопку **Применить**, а затем — **ОК**.    
   7. В строке меню щелкните **Build** (Сборка) и выберите **Make Project** (Создать проект). Кроме того, можно щелкнуть **Build Artifacts** («Сборка артефактов»), чтобы создать JAR-файл. Выходной JAR-файл создается в разделе **\classes\artifacts**.
      
       ![Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Удаленный запуск приложений с помощью Livy в кластере Spark

Для удаленного запуска приложения потоковой передачи на кластере Spark мы используем Livy. Подробное описание использования Livy с кластером HDInsight Spark см. в статье [Удаленная отправка заданий Spark в кластер Apache Spark в HDInsight на платформе Linux с помощью Livy](hdinsight-apache-spark-livy-rest-interface.md). Прежде чем начать выполнение удаленных заданий для потоковой передачи событий с помощью Spark, нужно выполнить несколько дополнительных действий.

1. Запустите локальное автономное приложение для создания и отправки событий в концентратор событий. Используйте следующую команду:
   
        java -cp com-microsoft-azure-eventhubs-client-example.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Скопируйте JAR-файл потоковой передачи (**spark-streaming-data-persistence-examples.jar**) в хранилище BLOB-объектов Azure, связанное с кластером. Таким образом JAR-файл станет доступным для Livy. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../storage/storage-use-azcopy.md). Кроме того, для отправки данных вы можете использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).
3. Установите служебную программу cURL на компьютере, где выполняются эти приложения. Эта программа понадобится нам, чтобы вызывать конечные точки Livy для удаленного выполнения заданий.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Запуск приложений для отправки событий в большой двоичный объект службы хранилища Azure в виде текста

Откройте командную строку, перейдите в каталог, где установлена программа cURL, и выполните следующую команду (введите соответствующие имя пользователя, пароль и имя кластера):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Параметры в файле **inputBlob.txt** определяются следующим образом:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Ниже приведено описание параметров во входном файле.

* **file** — путь к JAR-файлу приложения в учетной записи хранения Azure, связанной с кластером.
* **className** — имя класса в JAR-файле.
* **args** — список аргументов, требуемых для класса.
* **numExecutors** — количество ядер, используемых Spark для запуска приложения потоковой передачи. Это количество должно быть по крайней мере в два раза больше числа разделов в концентраторе событий.
* **executorMemory**, **executorCores**, **driverMemory** — параметры, используемые для назначения необходимых ресурсов приложению потоковой передачи.

> [!NOTE]
> Вам не нужно создавать выходные папки (EventCheckpoint, EventCount или EventCount10), используемые в качестве параметров. Приложение потоковой передачи создаст их самостоятельно.
> 
> 

После выполнения команды вы должны увидеть следующие выходные данные:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Запишите идентификатор пакетной службы, указанный в последней строке выходных данных (в данном примере — 1). Чтобы убедиться, что приложение выполняется успешно, зайдите в учетную запись хранения Azure, связанную с кластером. Там должна быть создана папка **/EventCount/EventCount10**. В этой папке должны содержаться большие двоичные объекты, которые записывают число событий, обработанных в течение периода времени, заданного для параметра **batch-interval-in-seconds**.

Приложение будет продолжать работу, пока вы не остановите его. Используйте для этого следующую команду:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Запуск приложений для отправки событий в большой двоичный объект службы хранилища Azure в виде JSON-файла
Откройте командную строку, перейдите в каталог, где установлена программа cURL, и выполните следующую команду (введите соответствующие имя пользователя, пароль и имя кластера):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Параметры в файле **inputJSON.txt** определяются следующим образом:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Параметры аналогичны тем, которые вы указали для выходного текстового файла на предыдущем шаге. Опять же, вам не нужно создавать выходные папки (EventCheckpoint, EventCount или EventCount10), используемые в качестве параметров. Приложение потоковой передачи создаст их самостоятельно.

 После выполнения команды зайдите в учетную запись хранения Azure, связанную с кластером. Там должна быть создана папка **EventStore10**. Откройте любой файл с префиксом **part-**. Там будут содержаться события, обработанные в формате JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Запуск приложений для отправки событий в таблицу Hive
Для запуска приложения, которое передает события потоком в таблицу Hive, требуются некоторые дополнительные компоненты, а именно:

* datanucleus-api-jdo-3.2.6.jar;
* datanucleus-rdbms-3.2.9.jar;
* datanucleus-core-3.2.10.jar;
* hive-site.xml

**JAR**-файлы доступны в кластере HDInsight Spark в каталоге `/usr/hdp/current/spark-client/lib`. Файл **hive-site.xml** доступен в каталоге `/usr/hdp/current/spark-client/conf`.

С помощью клиента [WinScp](http://winscp.net/eng/download.php) можно копировать эти файлы из кластера на локальный компьютер. Затем их можно копировать в учетную запись хранения, связанную с кластером, используя соответствующие инструменты. Дополнительные сведения об отправке файлов в учетную запись хранения см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).

Скопировав файлы в учетную запись хранения Azure, откройте командную строку, перейдите в каталог, где установлена программа cURL, и выполните следующую команду (введите соответствующие имя пользователя, пароль и имя кластера):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Параметры в файле **inputHive.txt** определяются следующим образом:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Параметры аналогичны тем, которые вы указали для выходного текстового файла на предыдущих шагах. Опять же, вам не нужно создавать выходные папки (EventCheckpoint, EventCount или EventCount10) или выходную таблицу (Hive EventHiveTable10), используемые в качестве параметров. Приложение потоковой передачи создаст их самостоятельно. Обратите внимание, что в параметрах **jars** и **files** содержатся пути к JAR-файлам и файлу hive-site.xml, скопированным в учетную запись хранения.

Чтобы убедиться, что таблица Hive успешно создана, подключитесь к кластеру по SSH и выполните запросы Hive. Инструкции см. в статье [Использование Hive с Hadoop в HDInsight с применением протокола SSH](hdinsight-hadoop-use-hive-ssh.md). Подключившись с использованием SSH, можно выполнить следующую команду, чтобы убедиться, что таблица Hive, **EventHiveTable10**, создана.

    show tables;

Должен отобразиться результат, аналогичный приведенному ниже:

    OK
    eventhivetable10
    hivesampletable

Можно также выполнить запрос SELECT, чтобы просмотреть содержимое таблицы.

    SELECT * FROM eventhivetable10 LIMIT 10;

Вы должны увидеть подобные выходные данные:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Запуск приложений для отправки событий в таблицу базы данных SQL Azure
Прежде чем выполнять этот шаг, убедитесь, что у вас есть созданная база данных SQL Azure. Инструкции см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md). Чтобы завершить работу с этим разделом, укажите в качестве параметров имя базы данных, имя сервера базы данных и учетные данные администратора базы данных. Однако создавать таблицу базы данных не нужно. Приложение потоковой передачи создаст ее самостоятельно.

Откройте командную строку, перейдите в каталог, где установлена программа cURL, и выполните следующую команду:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Параметры в файле **inputSQL.txt** определяются следующим образом:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Чтобы убедиться, что приложение выполняется успешно, подключитесь к базе данных SQL Azure с помощью SQL Server Management Studio. Соответствующие инструкции см. в статье [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](../sql-database/sql-database-connect-query-ssms.md). После подключения к базе данных можно перейти в таблицу **EventContent**, созданную с помощью приложения потоковой передачи. Для получения данных из таблицы используется быстрый запрос. Выполните следующий запрос:

    SELECT * FROM EventCount

Вы должны увидеть результат, аналогичный приведенному ниже:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

