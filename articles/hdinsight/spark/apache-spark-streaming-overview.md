---
title: Потоковая передача Spark в Azure HDInsight | Документация Майкрософт
description: Инструкции по использованию приложений потоковой передачи Spark в кластерах HDInsight Spark.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 2f521df81e5153affa95248cda2aa001bc5d6484
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164787"
---
# <a name="overview-of-spark-streaming"></a>Общие сведения о потоковой передаче Spark

Потоковая передача Spark обеспечивает обработку потока данных в кластерах HDInsight Spark. При этом гарантируется, что любое входящее событие обрабатывается всего один раз, даже если происходит сбой узла. Потоковая передача Spark — это долго выполняющееся задание, которое получает входные данные из разнообразных источников, включая концентраторы событий Azure, Центр Интернета вещей, Kafka, Flume, Twitter, ZeroMQ, незащищенные сокеты TCP, либо из файловых систем мониторинга HDFS. В отличие от процесса на основе исключительно событий потоковая передача Spark подразумевает объединение входных данных в пакеты, упорядоченное по временным промежуткам, например, 2 секунды, а затем преобразует каждый пакет данных с помощью операций сопоставления, уменьшения, соединения и извлечения. Затем поток Spark записывает преобразованные данные в файловые системы, базы данных, панели мониторинга и консоль.

![Потоковая обработка с помощью HDInsight и потоковой передачи Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Приложениям потоковой передачи Spark необходимо подождать долю секунды, чтобы собрать каждый *микропакет* событий, прежде чем отправить пакет на обработку. В свою очередь, приложение на основе событий обрабатывает каждое событие сразу же. Задержка при потоковой передаче Spark, как правило, не превышает несколько секунд. Преимущества подхода с использованием микропакетов заключаются в более эффективной обработке данных и более простых статистических вычислениях.

## <a name="introducing-the-dstream"></a>Общие сведения о DStream

Потоковая передача Spark представляет непрерывный поток входящих данных с использованием *дискретизированного потока* под названием DStream. Поток DStream можно создать из источников входных данных, таких как концентраторы событий или Kafka, либо путем применения преобразований в другом потоке DStream.

Поток DStream обеспечивает уровень абстракции поверх необработанных данных событий. 

Начните с одиночного события, например с чтения температуры с подключенного термостата. При получении этого события в приложении потоковой передачи Spark оно сохраняется надежным способом, то есть реплицируется на нескольких узлах. Такая отказоустойчивость гарантирует, что сбой какого-либо узла не приведет к потере события. В ядре Spark используется структура данных, в которой данные распределяются по нескольким узлам кластера, где каждый узел обычно хранит свои данные в памяти для повышения производительности. Эта структура данных называется *устойчивым распределенным набором данных* (RDD).

Каждый набор RDD содержит события, собранные за определяемый пользователем период времени, называемый *интервалом пакетной обработки*. По окончании каждого интервала пакетов создается новый набор RDD, содержащий все данные из этого интервала. Непрерывный набор RDD собирается в поток DStream. Например, если интервал пакетов составляет одну секунду, ваш поток DStream каждую секунду выдает пакет, содержащий один набор RDD со всеми данными, полученными на протяжении этой секунды. При обработке потока DStream событие температуры появляется в одном из этих пакетов. Приложение потоковой передачи Spark обрабатывает пакеты, содержащие события, и в конечном счете работает с данными, хранящимися в каждом наборе RDD.

![Пример потока DStream с событиями температуры ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Структура приложения потоковой передачи Spark

Приложение потоковой передачи Spark — это приложение продолжительного действия, которое получает данные из источников приема, применяет преобразования для обработки данных, а затем отправляет данные в один или несколько пунктов назначения. Структура приложения потоковой передачи Spark состоит из статической и динамической частей. Статическая часть определяет источник данных, способ обработки данных и пункт назначения результатов. Динамическая часть запускает приложение на неопределенный срок, ожидая сигнал об остановке.

Например, следующее простое приложение получает строку текста через TCP-сокет и подсчитывает количество появлений каждого слова.

### <a name="define-the-application"></a>Определение приложения

Определение логики приложения состоит из четырех шагов.

1. Создание объекта StreamingContext.
2. Создание потока DStream из StreamingContext.
3. Применение преобразований к потоку DStream.
4. Вывод результатов.

Это определение является статическим, обработка данных начинается только после запуска приложения.

#### <a name="create-a-streamingcontext"></a>Создание объекта StreamingContext

Создайте объект StreamingContext из объекта SparkContext, указывающего на ваш кластер. При создании объекта StreamingContext необходимо указать размер пакета в секундах, например:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Создание потока DStream

С помощью экземпляра StreamingContext создайте входной поток DStream для источника входных данных. В этом случае приложение наблюдает за появлением новых файлов в хранилище по умолчанию, подключенном к кластеру HDInsight.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Применение преобразований

Обработка реализовывается путем применения преобразований к потоку DStream. Это приложение получает одну строку текста за раз из файла, разбивает каждую строку на слова, а затем использует шаблон MapReduce для подсчета количества появлений каждого слова.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Вывод результатов

Отправьте результаты преобразования в системы назначения, применив операции вывода. В этом случае результат каждого выполнения путем вычисления выводится в выходных данных консоли.

    wordCounts.print()

### <a name="run-the-application"></a>Выполнение приложения

Запустите приложение потоковой передачи и продолжайте его работу, пока не будет получен сигнал завершения.

    ssc.start()            
    ssc.awaitTermination()

Дополнительные сведения об API-интерфейсе потоковой передачи Spark, а также источниках событий, преобразованиях и операциях вывода, которые он поддерживает, см. в [руководстве по программированию потоковой передачи Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Следующий пример приложения является автономным, поэтому его можно запустить в [блокноте Jupyter](apache-spark-jupyter-notebook-kernels.md). В этом примере создается макет источника данных в классе DummySource, который выводит значение счетчика и текущее время в миллисекундах каждые пять секунд. Для нового объекта StreamingContext интервал пакетной обработки составляет 30 секунд. Каждый раз при создании пакета приложение потоковой передачи проверяет полученный набор RDD, преобразует его в кадр данных Spark и создает для этого кадра данных временную таблицу.

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
                store(Iterator((counter, System.currentTimeMillis)))
                counter += 1
                Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Затем можно периодически запрашивать кадр данных, чтобы просмотреть текущий набор значений в пакете, например с помощью такого SQL-запроса:

    %%sql
    SELECT * FROM demo_numbers

Результат выглядит примерно так:

| value | Twitter в режиме реального |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Выводятся шесть значений, так как DummySource создает значение каждые 5 секунд, а пакет выпускается приложением каждые 30 секунд.

## <a name="sliding-windows"></a>Скользящие окна

Чтобы выполнить статистические вычисления в потоке DStream за определенный период времени (например, чтобы получить значение средней температуры за последние две секунды), можно использовать операции *скользящего окна*, входящие в состав потоковой передачи Spark. Скользящее окно имеет продолжительность и интервал, во время которого вычисляется содержимое окна (интервал скольжения).

Скользящие окна могут перекрывать друг друга. Например, можно определить окно с продолжительностью две секунды и интервалом скольжения в одну секунду. Это означает, что каждый раз при выполнении статического вычисления в окне будут содержаться данные последней секунды предыдущего окна, а также новые данные следующей секунды.

![Пример начального окна с событиями температуры](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Пример окна с событиями температуры после изменения слайда](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

В следующем примере обновляется код, который использует DummySource для сбора пакетов в окно длительностью в одну минуту и интервалом скольжения в одну минуту.

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

После истечения первой минуты будут получены 12 записей — по шесть записей из каждого из двух пакетов, собранных в окне.

| value | Twitter в режиме реального |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4. | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

В функции скользящего окна, доступные в API потоковой передачи Spark, входит window, countByWindow, reduceByWindow и countByValueAndWindow. Дополнительные сведения об этих функциях см. в разделе [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams) (Преобразования в потоках DStream).

## <a name="checkpointing"></a>Контрольные точки

Для обеспечения отказоустойчивости в потоковой передаче Spark используются контрольные точки, позволяющие гарантировать, что потоки обрабатываются непрерывно даже при сбоях узлов. В HDInsight Spark создает контрольные точки в долговременном хранилище (служба хранилища Azure или Data Lake Store). В этих контрольных точках хранятся метаданные о приложении потоковой передачи, такие как данные о конфигурации, данные об операциях, определенных приложением, и любые пакеты, поставленные в очередь, но еще не обработанные. В некоторых случаях контрольные точки также включают сохранение данных в наборах RDD. Это позволяет быстрее перестраивать состояние данных из содержимого в пакетах RDD, управляемых с помощью Spark.

## <a name="deploying-spark-streaming-applications"></a>Развертывание приложений потоковой передачи Spark

Как правило, приложение потоковой передачи Spark создается локально в JAR-файле, а затем развертывается в Spark в HDInsight путем копирования JAR-файла в хранилище по умолчанию, подключенное к кластеру HDInsight. Можно запустить приложение с использованием интерфейсов REST API LIVY, к которым можно получить доступ в кластере с помощью операции POST. Текст операции POST содержит документ JSON, предоставляющий путь к JAR-файлу, имя класса, основной метод которого определяет и выполняет приложение потоковой передачи, а также (необязательно) требования к ресурсам задания (например, количество исполнителей, ядер и объем памяти) и настройки конфигурации, необходимые для кода приложения.

![Развертывание приложения потоковой передачи Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Состояние всех приложений можно также проверить с помощью запроса GET к конечной точке LIVY. Наконец, можно закрыть работающее приложение, выполнив запрос DELETE к конечной точке LIVY. Дополнительные сведения об API LIVY см. в статье [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](apache-spark-livy-rest-interface.md).

## <a name="next-steps"></a>Дополнительная информация

* [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Руководство по программированию потоковой передачи Spark](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Удаленная отправка заданий Spark в кластер Azure HDInsight с помощью Apache Spark REST API](apache-spark-livy-rest-interface.md)
