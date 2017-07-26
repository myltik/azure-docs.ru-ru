---
title: "Потоковая передача данных Apache Spark из концентраторов событий в Azure HDInsight | Документация Майкрософт"
description: "Сведения о разработке примера приложения потоковой передачи Apache Spark для отправки потока данных в концентратор событий Azure и последующего получения этих событий в кластере HDInsight Spark с помощью приложения Scala."
keywords: "потоковая передача apache spark, потоковая передача spark, пример приложения spark, пример приложения потоковой передачи apache spark, пример концентратора событий azure, пример приложения spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 41019b4ae022602d2688399d1fc309151174e157
ms.contentlocale: ru-ru
ms.lasthandoff: 06/07/2017


---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Потоковая передача Apache Spark. Обработка данных из концентраторов событий Azure с помощью кластера Spark в HDInsight

В этой статье вы создадите пример приложения потоковой передачи Apache Spark, выполняющий следующие действия:

1. Автономное приложение принимает сообщения в концентратор событий Azure.

2. С помощью двух разных подходов сообщения извлекаются из концентратора событий в режиме реального времени с помощью приложения, работающего в кластере Spark в Azure HDInsight.

3. Вы создаете конвейеры потоковой передачи данных аналитики для сохранения данных в разных системах хранения или анализируете данные оперативно.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Основные понятия потоковой передачи Spark

Подробное описание потоковой передачи Spark см. в [этом разделе](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight предоставляет аналогичные функции потоковой передачи для кластера Spark в Azure.  

## <a name="what-does-this-solution-do"></a>Каково предназначение этого решения?

Чтобы создать пример приложения потоковой передачи Spark, сделайте следующее:

1. Создание концентратора событий Azure, который будет принимать поток событий.

2. Запуск локального автономного приложения, которое создает события и передает их в концентратор событий Azure. Пример такого приложения опубликован по адресу [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Удаленно запустите в кластере Spark приложение потоковой передачи, которое считывает потоковые события из концентратора событий Azure и выполняет различные задачи обработки и анализа данных.

## <a name="create-an-azure-event-hub"></a>Создание концентратора событий Azure

1. Войдите на [портал Azure](https://ms.portal.azure.com) и щелкните **Создать** вверху слева.

2. Последовательно выберите **Интернет вещей** и **Концентраторы событий**.

    ![Создание концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Создание концентратора событий для примера приложения потоковой передачи Spark")

3. В колонке **Создание пространства имен** укажите имя пространства имен. выберите ценовую категорию ("Базовый" или "Стандартный"). Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса. Щелкните **Создать** , чтобы создать пространство имен.

      ![Указание имени концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Указание имени концентратора событий для примера приложения потоковой передачи Spark")

    > [!NOTE]
    > Для сокращения задержек и затрат в поле **Расположение** следует выбрать то же расположение, в котором находится кластер Apache Spark в HDInsight.
    >
    >

4. В списке пространств имен концентраторов событий щелкните созданное пространство имен.      


5. В колонке пространства имен щелкните **Концентраторы событий**, а затем **+Концентратор событий**, чтобы создать новый концентратор событий.
   
    ![Создание концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Создание концентратора событий для примера приложения потоковой передачи Spark")

6. Введите имя для концентратора событий; для параметра с числом разделов установите значение 10, а для хранения сообщений — 1. Мы не будем сохранять сообщения в этом решении, поэтому остальные параметры можно не изменять. Просто нажмите кнопку **Создать**.
   
    ![Указание сведений о концентраторе событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Указание сведений о концентраторе событий для примера приложения потоковой передачи Spark")

7. Только что созданный концентратор событий отобразится в колонке "Концентратор событий".
    
     ![Просмотр концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Просмотр концентратора событий для примера приложения потоковой передачи Spark")

8. В колонке пространства имен (не в конкретной колонке концентратора событий) щелкните **Политики общего доступа**, а затем нажмите щелкните **RootManageSharedAccessKey**.
    
     ![Определение политик концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Определение политик концентратора событий для примера приложения потоковой передачи Spark")

9. Нажмите кнопку копирования, чтобы скопировать первичный ключ и строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните их, чтобы использовать на следующих этапах работы с этим руководством.
    
     ![Просмотр ключей политик концентратора событий для примера приложения потоковой передачи Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Просмотр ключей политик концентратора событий для примера приложения потоковой передачи Spark")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Отправка сообщений в концентратор событий с помощью примера приложения Scala

В этом разделе вы примените локальное автономное приложение Scala для создания потока событий и отправки его в созданный ранее концентратор событий Azure. Это приложение доступно на сайте GitHub по адресу [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Здесь предполагается, что в репозитории GitHub уже созданы разветвления.

1. Убедитесь, что на компьютере, где выполняется это приложение, установлены следующие компоненты:

    * Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. Скачать эту версию можно [здесь](https://maven.apache.org/download.cgi). Инструкции по установке Maven доступны [здесь](https://maven.apache.org/install.html).

2. Откройте командную строку и перейдите к расположению, в которое вы скопировали образец приложения Scala из репозитория GitHub, а затем выполните приведенную ниже команду для сборки приложения.

        mvn package

3. Выходной JAR-файл приложения (**com-microsoft-azure-eventhubs-client-example-0.2.0.jar**) создается в каталоге **/target**. Этот JAR-файл будет использоваться позднее в этой статье для тестирования готового решения.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Создание приложения для получения сообщений от концентратора событий в кластере Spark 

Существует два подхода к установлению подключения между Spark Streaming и концентраторами событий Azure: подключение на основе приемника и подключение на основе Direct DStream. Подключение на основе Direct DStream появилось в январе 2017 г. в версии 2.0.3. Оно предназначено для замены прежних подключений на основе приемника, так как оно обеспечивает более высокую производительность и эффективность использования ресурсов. Дополнительные сведения см. на странице [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). Direct DStream поддерживает только Spark 2.0 и более поздние версии.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Создание приложений с зависимостью от соединителя spark-eventhubs

Мы также опубликуем промежуточную версию Spark-EventHubs в GitHub. Для использования промежуточной версии Spark-EventHubs сначала необходимо указать GitHub в качестве исходного репозитория, добавив в файл pom.xml следующую запись:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

Далее можно добавить в проект указанную ниже зависимость для использования предварительной версии.

Зависимость Maven

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Зависимость SBT

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Подключение Direct DStream

Предварительно собранный JAR-файл, содержащий примеры использования Direct DStream, можно скачать по адресу [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar).

JAR-файл содержит три примера, исходный код которых доступен на странице по адресу [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Рассмотрим [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) в качестве примера.

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

В приведенном выше примере `eventhubParameters` — это параметры, относящиеся к одному экземпляру EventHubs. Их необходимо передать в интерфейс API `createDirectStreams`, который создает объект Direct DStream, сопоставленный с пространством имен концентраторов событий. Посредством объекта Direct DStream можно вызвать любой интерфейс API DStream, предоставляемый платформой API Spark Streaming. В этом примере мы вычисляем частоту встречаемости каждого слова за последние 3 интервала микропакетов.

### <a name="receiver-based-connection"></a>Подключение на основе получателя

Пример приложения потоковой передачи Spark, написанного на языке Scala, которое получает события и перенаправляет их в различные расположения, доступен по адресу [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Выполните действия ниже, чтобы обновить приложение для конфигурации концентратора событий и создать выходной JAR-файл.

1. Запустите IntelliJ IDEA и на экране запуска щелкните **Check out from Version Control** (Извлечь из системы управления версиями) и выберите пункт **Git**.
   
    ![Пример приложения потоковой передачи Apache Spark. Получение источников из Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Пример приложения потоковой передачи Apache Spark. Получение источников из Git")

2. В диалоговом окне **Clone Repository** (Клонирование репозитория) введите URL-адрес репозитория Git, который нужно клонировать, укажите каталог, в который будет выполняться клонирование, а затем нажмите кнопку **Clone** (Клонировать).
   
    ![Пример приложения потоковой передачи Apache Spark. Клонирование из Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Пример приложения потоковой передачи Apache Spark. Клонирование из Git")
3. Следуйте инструкциям на экране, пока проект полностью не клонируется. Нажмите клавиши **ALT+1**, чтобы открыть **представление проекта**. Оно должно выглядеть так:
   
    ![Пример приложения потоковой передачи Apache Spark. Представление проекта](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Пример приложения потоковой передачи Apache Spark. Представление проекта")
4. Скомпилируйте код приложения с помощью Java 8. Чтобы сделать это, выберите **File** (Файл), **Project Structure** (Структура проекта) и на вкладке **Project** (Проект) в поле Project language level (Уровень языка проекта) установите значение **8 - Lambdas, type annotations, etc.** (8 — лямбды, аннотации типа и т. д.).
   
    ![Пример приложения потоковой передачи Apache Spark. Настройка компилятора](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Пример приложения потоковой передачи Apache Spark. Настройка компилятора")
5. Откройте узел **pom.xml** , чтобы убедиться, что используется правильная версия Spark. В узле `<properties>` найдите фрагмент кода ниже и проверьте версию Spark.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. Приложению требуется JAR-файл зависимостей, а точнее **JAR-файл драйвера JDBC**. Этот файл необходим для записи сообщений, полученных из концентратора событий, в базу данных SQL Azure. Этот JAR-файл (версии 4.1 или более поздней) можно скачать [здесь](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Добавьте ссылку на этот JAR-файл в библиотеке проекта. Выполните следующие действия:
     
     1. В окне IntelliJ IDEA, где открыто приложение, щелкните **File** (Файл), выберите **Project Structure** (Структура проекта) и щелкните **Libraries** (Библиотеки). 
     2. Щелкните значок "Добавить" (![добавление значка](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), выберите **Java**и перейдите в папку, куда вы скачали JAR-файл драйвера JDBC. Следуйте инструкциям, чтобы добавить JAR-файл в библиотеку проектов.

         ![добавление отсутствующих зависимостей](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Добавление отсутствующих JAR-файлов зависимостей")
     3. Нажмите кнопку **Применить**.

7. Создайте выходной JAR-файл. Выполните следующие действия.

   1. В диалоговом окне **Project Structure** (Структура проекта) выберите **Artifacts** (Артефакты) и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем выберите **From modules with dependencies** (На основе модулей с зависимостями).      
       
       ![Пример приложения потоковой передачи Apache Spark. Создание JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Пример приложения потоковой передачи Apache Spark. Создание JAR-файла")
   2. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) нажмите кнопку с многоточием (![многоточие](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) возле пункта **Main Class** (Основной класс).
   3. В диалоговом окне **Select Main Class** (Выбор основного класса) выберите любой из доступных классов и нажмите кнопку **ОК**.
      
       ![Пример приложения потоковой передачи Apache Spark. Выбор класса JAR-файла](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Пример приложения потоковой передачи Apache Spark. Выбор класса JAR-файла")
   4. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) установите переключатель **extract to the target JAR** (Извлечь в целевой JAR-файл) и нажмите кнопку **ОК**. В результате будет создан один JAR-файл, содержащий все зависимости.
      
       ![Пример приложения потоковой передачи Apache Spark. Создание JAR-файла на основе модулей](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Пример приложения потоковой передачи Apache Spark. Создание JAR-файла на основе модулей")
   5. На вкладке **Макет выходных данных** содержится список всех JAR-файлов, которые включены в проект Maven. Здесь можно выбрать и удалить файлы, от которых не зависит работа приложения Scala. Для создаваемого приложения можно удалить все файлы, кроме последнего (**spark-streaming-data-persistence-examples compile output**). Выберите JAR-файлы, которые нужно удалить, и щелкните значок **удаления** (![значок удаления](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Пример приложения потоковой передачи Apache Spark. Удаление извлеченных JAR-файлов](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Пример приложения потоковой передачи Apache Spark. Удаление извлеченных JAR-файлов")
      
       Установите флажок **Build on make** («Сборка на основе созданного»), чтобы JAR-файл создавался при каждом создании и обновлении проекта. Нажмите кнопку **Применить**.
   6. На вкладке **Output Layout** (Макет выходных данных) под полем **Available Elements** (Доступные элементы) отображается JAR-файл SQL JDBC, ранее добавленный в библиотеку проекта. Его необходимо добавить на вкладку **Output Layout** (Макет выходных данных). Щелкните JAR-файл правой кнопкой мыши и выберите пункт **Extract Into Output Root**(Извлечь в корень выходных данных).
      
       ![Пример приложения потоковой передачи Apache Spark. Извлечение JAR-файла зависимостей](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Пример приложения потоковой передачи Apache Spark. Извлечение JAR-файла зависимостей")  
      
       Вкладка **макета выходных данных** теперь должна выглядеть следующим образом:
      
       ![Пример приложения потоковой передачи Apache Spark. Вкладка окончательных выходных данных](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Пример приложения потоковой передачи Apache Spark. Вкладка окончательных выходных данных")        
      
       В диалоговом окне **Project Structure** (Структура проекта) нажмите кнопку **Применить**, а затем — **ОК**.    
   7. В строке меню щелкните **Build** (Сборка) и выберите **Make Project** (Создать проект). Кроме того, можно щелкнуть **Build Artifacts** («Сборка артефактов»), чтобы создать JAR-файл. Выходной JAR-файл создается в разделе **\classes\artifacts**.
      
       ![Пример приложения потоковой передачи Apache Spark. Выходной JAR-файл](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Пример приложения потоковой передачи Apache Spark. Выходной JAR-файл")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Удаленный запуск приложений с помощью Livy в кластере Spark

В этой статье для удаленного запуска приложения потоковой передачи Apache Spark на кластере Spark используется Livy. Подробное описание использования Livy с кластером HDInsight Spark см. в статье [Удаленная отправка заданий Spark в кластер Apache Spark в HDInsight на платформе Linux с помощью Livy](hdinsight-apache-spark-livy-rest-interface.md). Перед запуском приложения потоковой передачи Apache Spark сделайте следующее:

1. Запустите локальное автономное приложение для создания и отправки событий в концентратор событий. Используйте следующую команду:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Скопируйте JAR-файл потоковой передачи (**spark-streaming-data-persistence-examples.jar**) в хранилище BLOB-объектов Azure, связанное с кластером. Таким образом JAR-файл станет доступным для Livy. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../storage/storage-use-azcopy.md). Кроме того, для отправки данных вы можете использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Hadoop в HDInsight](hdinsight-upload-data.md).
3. Установите служебную программу cURL на компьютере, где выполняются эти приложения. Эта программа понадобится нам, чтобы вызывать конечные точки Livy для удаленного выполнения заданий.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Запуск приложения потоковой передачи Spark для отправки событий в Azure Storage Blob в виде текста

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

Приложение потоковой передачи Spark будет продолжать работу, пока вы не остановите его. Используйте для этого следующую команду:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Запуск приложений для отправки событий в большой двоичный объект службы хранилища Azure в виде JSON-файла
Откройте командную строку, перейдите в каталог, где установлена программа cURL, и выполните следующую команду (введите соответствующие имя пользователя, пароль и имя кластера):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Параметры в файле **inputJSON.txt** определяются следующим образом:

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Параметры аналогичны тем, которые вы указали для выходного текстового файла на предыдущем шаге. Опять же, вам не нужно создавать выходные папки (EventCheckpoint, EventCount или EventCount10), используемые в качестве параметров. Приложение потоковой передачи создаст их самостоятельно.

 После выполнения команды зайдите в учетную запись хранения Azure, связанную с кластером. Там должна быть создана папка **EventStore10**. Откройте любой файл с префиксом **part-**. Там будут содержаться события, обработанные в формате JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Запуск приложений для отправки событий в таблицу Hive
Для запуска приложения потоковой передачи Spark, которое передает события потоком в таблицу Hive, требуются некоторые дополнительные компоненты, а именно:

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
Прежде чем выполнять этот шаг, убедитесь, что у вас есть созданная база данных SQL Azure. Инструкции см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md). Чтобы завершить работу с этим разделом, укажите в качестве параметров имя базы данных, имя сервера базы данных и учетные данные администратора базы данных. Однако создавать таблицу базы данных не нужно. Приложение потоковой передачи Spark создаст ее самостоятельно.

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
* [Проектирование подключения на основе приемника и Direct DStream](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

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
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
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
[azure-create-storageaccount]: ../storage-create-storage-account/

