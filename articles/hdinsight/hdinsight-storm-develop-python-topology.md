---
title: "Использование компонентов Python в топологии Storm на HDinsight | Документация Майкрософт"
description: "Узнайте, как можно использовать компоненты Python с Apache Storm на Azure HDInsight. Вы узнаете, как использовать компоненты Python из топологии Storm как на основе Java, так и на основе Clojure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4b667dda33c61c44090cf89ebeebece0b19c84ea


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Разработка топологий Apache Storm с помощью Python в HDInsight
Apache Storm поддерживает несколько языков и даже позволяет объединять компоненты из нескольких языков в одной топологии. Из этого документа вы узнаете, как использовать компоненты Python в топологиях Storm на базе Java и Clojure в HDInsight.

## <a name="prerequisites"></a>Предварительные требования
* Python 2.7 или выше;
* Java JDK 1.7 или выше.
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Многоязыковая поддержка Storm
Топология Storm предназначена для работы с компонентами, написанными на любом языке программирования. Однако эти компоненты должны уметь работать с [определением Thrift для Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). В рамках проекта Apache Storm предоставляется модуль для Python, который позволяет легко взаимодействовать со Storm. Этот модуль можно найти по адресу [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Так как Apache Storm представляет собой процесс Java, выполняемый на виртуальной машине Java (JVM), компоненты, написанные на других языках, выполняются как подпроцессы. Биты Storm, работающие в JVM, взаимодействуют с этими подпроцессами с помощью сообщений JSON, отправляемых через стандартный ввод-вывод. Дополнительные сведения о связи между компонентами можно найти в документации по [многоязыковому протоколу](https://storm.apache.org/documentation/Multilang-protocol.html) .

### <a name="the-storm-module"></a>Модуль Storm
Модуль Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) предоставляет код, необходимый для создания компонентов Python, работающих с топологией Storm.

Предоставляются такие элементы, как `storm.emit` для выдачи кортежей и `storm.logInfo` для записи в журналы. Рекомендуется прочитать этот файл и понять, какие элементы он предоставляет.

## <a name="challenges"></a>Сложности
С помощью модуля **storm.py** можно создавать воронки Python, которые потребляют данные, и сита, которые обрабатывают данные. Однако общее определение топологии Storm, обеспечивающее взаимодействие между компонентами, все равно пишется на языке Java или Clojure. Кроме того, если вы используете Java, необходимо создать также компоненты Java, служащие интерфейсом для компонентов Python.

Кроме того, так как кластеры Storm выполняются распределенно, необходимо обеспечить на всех рабочих узлах кластера доступность любых модулей, необходимых для компонентов Python. У Storm нет простого способа это сделать для многоязыковых ресурсов. Необходимо либо включить все зависимости в JAR-файл для топологии, либо вручную установить зависимости на каждом рабочем узле в кластере.

### <a name="java-vs-clojure-topology-definition"></a>Определение топологии: Java против Clojure
Из двух способов определения топологии Clojure — несомненно самый простой и четкий, так как он позволяет непосредственно обращаться к компонентам Python в определении топологии. Для определений топологии на основе Java необходимо определить также компоненты Java, обрабатывающие такие действия, как объявление полей в кортежах, возвращаемых из компонентов Python.

В этом документе описаны оба метода вместе с примерами проектов.

## <a name="python-components-with-a-java-topology"></a>Компоненты Python с топологией Java
> [!NOTE]
> Этот пример доступен по адресу [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) в каталоге **JavaTopology**. Это проект на основе Maven. Если вы не знакомы с Maven, ознакомьтесь со статьей [Разработка топологий на основе Java с помощью Apache Storm в HDInsight](hdinsight-storm-develop-java-topology.md), чтобы получить дополнительные сведения о создании проекта Maven для топологии Storm.
> 
> 

Топология на основе Java, использующая Python (или другие языковые компоненты JVM), использует, на первый взгляд, компоненты Java. Однако, если присмотреться к каждой из воронок или сит Java, вы увидите код, подобный следующему:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Здесь Java вызывает Python и запускает сценарий, содержащий фактическую логику сита. Воронки и сита Java (в данном примере) просто объявляют поля в кортеже, которые выдаст базовый компонент Python.

Фактические файлы Python в этом примере хранятся в каталоге `/multilang/resources` . Каталог `/multilang` указывается в файле **pom.xml**:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

Сюда входят все файлы в папке `/multilang` JAR-файла, который будет построен из этого проекта.

> [!IMPORTANT]
> Обратите внимание, что здесь указывается только каталог `/multilang`, а не `/multilang/resources`. Storm ожидает не относящиеся к JVM ресурсы в каталоге `resources` , поэтому внутри него уже производится поиск. Помещение компонентов в эту папку позволяет просто обращаться к ним по имени в коде Java. Пример: `super("python", "countbolt.py");`. Можно представить это иначе: Storm воспринимает каталог `resources` как корневой (/) при доступе к многоязыковым ресурсам.
> 
> В этом примере проекта модуль `storm.py` включен в каталог `/multilang/resources`.
> 
> 

### <a name="build-and-run-the-project"></a>Построение и запуск проекта
Чтобы запустить этот проект локально, просто выполните следующую команду Maven для построения и запуска в локальном режиме:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Остановить процесс можно сочетанием клавиш CTRL + C.

Чтобы развернуть проект в кластере HDInsight под управлением Apache Storm, выполните следующие действия:

1. Постройте uber jar:
   
        mvn package
   
    При этом будет создан файл с именем **WordCount--1.0-SNAPSHOT.jar** в каталоге `/target` данного проекта.
2. Загрузите JAR-файл в кластер Hadoop с помощью одного из следующих методов.
   
   * Для кластеров HDInsight **под управлением Linux**: используйте `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` для копирования JAR-файла в кластер, заменив USERNAME именем пользователя SSH, а CLUSTERNAME — именем кластера HDInsight.
     
       После завершения передачи файла подключитесь к кластеру с помощью SSH и запустите топологию с помощью `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * Для кластеров HDInsight **под управлением Windows**: подключитесь к панели мониторинга Storm, перейдя по адресу HTTPS://<имя_кластера>.azurehdinsight.net/ в браузере. Замените CLUSTERNAME именем кластера HDInsight и укажите имя администратора и пароль в ответ на запрос.
     
       Используя форму, выполните следующие действия.
     
     * **JAR-файл**: щелкните **Обзор**, а затем выберите файл **wordcount-1.0-SNAPSHOT.jar**.
     * **Имя класса**: введите `com.microsoft.example.WordCount`.
     * **Дополнительные параметры**: введите понятное имя, например `wordcount`, для описания топологии.
       
       Наконец, нажмите кнопку **Отправить** для запуска топологии.

> [!NOTE]
> После запуска топология Storm выполняется до тех пор, пока она не будет остановлена (завершена). Чтобы остановить топологию, используйте либо команду `storm kill TOPOLOGYNAME` в командной строке (например, сеанса SSH для кластера Linux), либо с помощью пользовательского интерфейса Storm выберите топологию, а затем нажмите кнопку **Прервать**.
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>Компоненты Python с топологией Clojure
> [!NOTE]
> Этот пример доступен по адресу [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) в каталоге **ClojureTopology** .
> 
> 

Данная топология создана с помощью [Leiningen](http://leiningen.org). Этот инструмент был использован для [создания нового проекта Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). После этого в шаблонный проект были внесены следующие изменения.

* **project.clj**: добавлены зависимости для Storm и исключения для элементов, которые могут вызвать проблемы при развертывании на сервере HDInsight.
* **resources/resources**: Leiningen создает каталог по умолчанию `resources`, однако хранящиеся в нем файлы добавляются в корень JAR-файла, созданного из этого проекта, а Storm ожидает файлы во вложенном каталоге с именем `resources`. Поэтому добавлен вложенный каталог, и файлы Python хранятся в `resources/resources`. Во время выполнения этот каталог будет считаться корневым (/) для доступа к компонентам Python.
* **src/wordcount/core.clj**: этот файл содержит определение топологии. Кроме того, на него ссылается файл **project.clj**. Дополнительные сведения об использовании Clojure для определения топологии Storm см. в статье [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

### <a name="build-and-run-the-project"></a>Построение и запуск проекта
**Чтобы построить и запустить проект локально**, выполните следующую команду:

    lein clean, run

Чтобы остановить топологию, нажмите сочетание клавиш **CTRL + C**.

**Чтобы построить uberjar и развернуть в HDInsight**, выполните следующие действия:

1. Создайте uberjar, содержащий топологию и необходимые зависимости:
   
        lein uberjar
   
    Будет создан новый файл с именем `wordcount-1.0-SNAPSHOT.jar` в каталоге `target\uberjar+uberjar` .
2. С помощью одного из следующих методов разверните и запустите топологию в кластер HDInsight:
   
   * **HDInsight под управлением Linux**
     
     1. Скопируйте файл в головной узел кластера HDInsight с помощью `scp`. Например:
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         Замените USERNAME именем пользователя SSH для вашего кластера, а CLUSTERNAME — именем кластера HDInsight.
     2. После копирования файла в кластер подключитесь к кластеру с помощью SSH и отправьте задание. Сведения об использовании SSH с HDInsight см. в одной из следующих статей.
        
        * [Использование SSH с HDInsight под управлением Linux в Linux, Unix или OS X.](hdinsight-hadoop-linux-use-ssh-unix.md)
        * [Использование SSH с HDInsight под управлением Linux в Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)
     3. После подключения запустите топологию следующей командой:
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **HDInsight под управлением Windows**
     
     1. Подключитесь к панели мониторинга Storm, перейдя по адресу HTTPS://<имя_кластера>.azurehdinsight.net/ в браузере. Замените CLUSTERNAME именем кластера HDInsight и укажите имя администратора и пароль в ответ на запрос.
     2. Используя форму, выполните следующие действия.
        
        * **JAR-файл**: щелкните **Обзор**, а затем выберите файл **wordcount-1.0-SNAPSHOT.jar**.
        * **Имя класса**: введите `wordcount.core`.
        * **Дополнительные параметры**: введите понятное имя, например `wordcount`, для описания топологии.
          
          Наконец, нажмите кнопку **Отправить** для запуска топологии.

> [!NOTE]
> После запуска топология Storm выполняется до тех пор, пока она не будет остановлена (завершена). Чтобы остановить топологию, используйте либо команду `storm kill TOPOLOGYNAME` в командной строке (сеанса SSH для кластера Linux), либо с помощью пользовательского интерфейса Storm выберите топологию, а затем нажмите кнопку **Прервать**.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
В этом документе рассмотрено, как использовать компоненты Python из топологии Storm. Чтобы узнать о других способах использования Python с HDInsight, см. следующие документы.

* [Использование Python для потоковой передачи заданий MapReduce.](hdinsight-hadoop-streaming-python.md)
* [Использование определяемых пользователем функций Python (UDF) в Pig и Hive.](hdinsight-python.md)




<!--HONumber=Nov16_HO3-->


