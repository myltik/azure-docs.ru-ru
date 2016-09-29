<properties
   pageTitle="Использование компонентов Python в топологии Storm на HDinsight | Microsoft Azure"
   description="Узнайте, как можно использовать компоненты Python с Apache Storm на Azure HDInsight. Вы узнаете, как использовать компоненты Python из топологии Storm как на основе Java, так и на основе Clojure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/11/2016"
   ms.author="larryfr"/>

#Разработка топологий Apache Storm с помощью Python в HDInsight

Apache Storm поддерживает несколько языков и даже позволяет объединять компоненты из нескольких языков в одной топологии. Из этого документа вы узнаете, как использовать компоненты Python в топологиях Storm на базе Java и Clojure в HDInsight.

##Предварительные требования

* Python 2.7 или выше;

* Java JDK 1.7 или выше.

* [Leiningen](http://leiningen.org/)

##Многоязыковая поддержка Storm

Storm предназначен для работы с компонентами, написанными на любом языке программирования. Однако эти компоненты должны уметь работать с [определением Thrift для Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). В рамках проекта Apache Storm предоставляется модуль для Python, который позволяет легко взаимодействовать со Storm. Этот модуль можно найти по адресу [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Так как Apache Storm представляет собой процесс Java, выполняемый на виртуальной машине Java (JVM), компоненты, написанные на других языках, выполняются как подпроцессы. Биты Storm, работающие в JVM, взаимодействуют с этими подпроцессами с помощью сообщений JSON, отправляемых через стандартный ввод-вывод. Дополнительные сведения о связи между компонентами можно найти в документации по [многоязыковому протоколу](https://storm.apache.org/documentation/Multilang-protocol.html).

###Модуль Storm

Модуль storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) предоставляет элементы, необходимые для создания компонентов Python, которые работают со Storm.

Предоставляются такие элементы, как `storm.emit` для выдачи кортежей и `storm.logInfo` для записи в журналы. Рекомендуется прочитать этот файл и понять, какие элементы он предоставляет.

##Сложности

С помощью модуля __storm.py__ можно создавать воронки Python, которые потребляют данные, и сита, которые используют данные. Однако общее определение топологии Storm, обеспечивающее взаимодействие между компонентами, все равно пишется на языке Java или Clojure. Кроме того, если вы используете Java, необходимо создать также компоненты Java, служащие интерфейсом для компонентов Python.

Кроме того, так как кластеры Storm выполняются распределенно, необходимо обеспечить на всех рабочих узлах кластера доступность любых модулей, необходимых для компонентов Python. У Storm нет простого способа это сделать для многоязыковых ресурсов. Необходимо либо включить все зависимости в JAR-файл для топологии, либо вручную установить зависимости на каждом рабочем узле в кластере.

###Определение топологии: Java против Clojure

Из двух способов определения топологии Clojure — несомненно самый простой и четкий, так как он позволяет непосредственно обращаться к компонентам Python в определении топологии. Для определений топологии на основе Java необходимо определить также компоненты Java, обрабатывающие такие действия, как объявление полей в кортежах, возвращаемых из компонентов Python.

В этом документе описаны оба метода вместе с примерами проектов.

##Компоненты Python с топологией Java

> [AZURE.NOTE] Этот пример доступен по адресу [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) в каталоге __JavaTopology__. Это проект на основе Maven. Если вы незнакомы с Maven, см. статью [Разработка топологий на основе Java с помощью Apache Storm в HDInsight](hdinsight-storm-develop-java-topology.md) для получения дополнительных сведений о создании проекта Maven для топологии Storm.

Топология на основе Java, использующая Python (или другие языковые компоненты JVM), использует, на первый взгляд, компоненты Java. Однако, если присмотреться к каждой из воронок или сит Java, вы увидите код, подобный следующему:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Здесь Java вызывает Python и запускает сценарий, содержащий фактическую логику сита. Воронки и сита Java (в данном примере) просто объявляют поля в кортеже, которые выдаст базовый компонент Python.

Фактические файлы Python в этом примере хранятся в каталоге `/multilang/resources`. Каталог `/multilang` указывается в файле __pom.xml__:

<ресурсы> <ресурс> <!-- Where the Python bits are kept --> <каталог>${basedir}/multilang</каталог> </ресурс> </ресурсы>

Сюда входят все файлы в папке `/multilang` JAR-файла, который будет построен из этого проекта.

> [AZURE.IMPORTANT] Обратите внимание, что здесь указывается только каталог `/multilang`, а не `/multilang/resources`. Storm ожидает не относящиеся к JVM ресурсы в каталоге `resources`, поэтому внутри него уже производится поиск. Помещение компонентов в эту папку позволяет просто обращаться к ним по имени в коде Java. Пример: `super("python", "countbolt.py");`. Можно представить это иначе: Storm воспринимает каталог `resources` как корневой (/) при доступе к многоязыковым ресурсам.
>
> В этом примере проекта модуль `storm.py` включен в каталог `/multilang/resources`.

###Построение и запуск проекта

Чтобы запустить этот проект локально, просто выполните следующую команду Maven для построения и запуска в локальном режиме:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Остановить процесс можно сочетанием клавиш CTRL + C.

Чтобы развернуть проект в кластере HDInsight под управлением Apache Storm, выполните следующие действия:

1. Постройте uber jar:

        mvn package

    При этом будет создан файл с именем __WordCount--1.0-SNAPSHOT.jar__ в каталоге `/target` данного проекта.

2. Загрузите JAR-файл в кластер Hadoop с помощью одного из следующих методов.

    * Для кластеров HDInsight __под управлением Linux__: используйте `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` для копирования JAR-файла в кластер, заменив USERNAME именем пользователя SSH, а CLUSTERNAME именем кластера HDInsight.

        После завершения передачи файла подключитесь к кластеру с помощью SSH и запустите топологию с помощью `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`.

    * Для кластеров HDInsight __под управлением Windows__: подключитесь к панели мониторинга Storm, перейдя по адресу HTTPS://CLUSTERNAME.azurehdinsight.net/ в браузере. Замените CLUSTERNAME именем кластера HDInsight и укажите имя администратора и пароль в ответ на запрос.

        Используя форму, выполните следующие действия.

        * __JAR-файл__: щелкните __Обзор__, а затем выберите файл __WordCount-1.0-SNAPSHOT.jar__.
        * __Имя класса__: введите `com.microsoft.example.WordCount`.
        * __Дополнительные параметры__: введите понятное имя, например `wordcount`, для определения топологии.

        Наконец, нажмите кнопку __Отправить__ для запуска топологии.

> [AZURE.NOTE] После запуска топология Storm выполняется до тех пор, пока она не будет остановлена (завершена). Чтобы остановить топологию, используйте либо команду `storm kill TOPOLOGYNAME` из командной строки (например, сеанса SSH для кластера Linux), либо с помощью пользовательского интерфейса Storm выберите топологию, а затем нажмите кнопку __Kill__ (Удалить).

##Компоненты Python с топологией Clojure

> [AZURE.NOTE] Этот пример доступен по адресу [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) в каталоге __ClojureTopology__.

Эта топология создана с помощью [Leiningen](http://leiningen.org) для [создания нового проекта Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). После этого в шаблонный проект были внесены следующие изменения.

* __project.clj__: добавлены зависимости для Storm и исключения для элементов, которые могут вызвать проблемы при развертывании на сервере HDInsight.
* __resources/resources__: Leiningen создает каталог по умолчанию `resources`, однако хранящиеся в нем файлы добавляются в корень JAR-файла, созданного из этого проекта, а Storm ожидает файлы во вложенном каталоге с именем `resources`. Поэтому добавлен вложенный каталог, и файлы Python хранятся в `resources/resources`. Во время выполнения этот каталог будет считаться корневым (/) для доступа к компонентам Python.
* __src/wordcount/core.clj__: этот файл содержит определение топологии. Кроме того, на него ссылается файл __project.clj__. Дополнительные сведения об использовании Clojure для определения топологии Storm см. в статье [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Построение и запуск проекта

__Чтобы построить и запустить проект локально__, выполните следующую команду:

    lein clean, run

Чтобы остановить топологию, нажмите сочетание клавиш __CTRL + C__.

__Чтобы построить uberjar и развернуть в HDInsight__, выполните следующие действия:

1. Создайте uberjar, содержащий топологию и необходимые зависимости:

        lein uberjar

    Будет создан новый файл с именем `wordcount-1.0-SNAPSHOT.jar` в каталоге `target\uberjar+uberjar`.
    
2. С помощью одного из следующих методов разверните и запустите топологию в кластер HDInsight:

    * __HDInsight под управлением Linux__
    
        1. Скопируйте файл в головной узел кластера HDInsight с помощью `scp`. Например:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Замените USERNAME именем пользователя SSH для вашего кластера, а CLUSTERNAME — именем кластера HDInsight.
            
        2. После копирования файла в кластер подключитесь к кластеру с помощью SSH и отправьте задание. Сведения об использовании SSH с HDInsight см. в одной из следующих статей.
        
            * [Использование SSH с HDInsight под управлением Linux в Linux, Unix или OS X.](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Использование SSH с HDInsight под управлением Linux в Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. После подключения запустите топологию следующей командой:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight под управлением Windows__
    
        1. Подключитесь к панели мониторинга Storm, перейдя по адресу HTTPS://CLUSTERNAME.azurehdinsight.net/ в браузере. Замените CLUSTERNAME именем кластера HDInsight и укажите имя администратора и пароль в ответ на запрос.

        2. Используя форму, выполните следующие действия.

            * __JAR-файл__: щелкните __Обзор__, а затем выберите файл __wordcount-1.0-SNAPSHOT.jar__.
            * __Имя класса__: введите `wordcount.core`.
            * __Дополнительные параметры__: введите понятное имя, например `wordcount`, для определения топологии.

            Наконец, нажмите кнопку __Отправить__ для запуска топологии.

> [AZURE.NOTE] После запуска топология Storm выполняется до тех пор, пока она не будет остановлена (завершена). Чтобы остановить топологию, используйте либо команду `storm kill TOPOLOGYNAME` из командной строки (сеанса SSH для кластера Linux), либо с помощью пользовательского интерфейса Storm выберите топологию, а затем нажмите кнопку __Kill__ (Удалить).

##Дальнейшие действия

В этом документе рассмотрено, как использовать компоненты Python из топологии Storm. Чтобы узнать о других способах использования Python с HDInsight, см. следующие документы.

* [Использование Python для потоковой передачи заданий MapReduce.](hdinsight-hadoop-streaming-python.md)
* [Использование определяемых пользователем функций Python (UDF) в Pig и Hive.](hdinsight-python.md)

<!---HONumber=AcomDC_0914_2016-->