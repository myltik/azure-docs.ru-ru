---
title: Использование Caffe в кластере Azure HDInsight Spark для распределенного глубокого обучения | Документация Майкрософт
description: Сведения об использовании Caffe в кластере Azure HDInsight Spark для выполнения распределенного глубокого обучения.
services: hdinsight
documentationcenter: ''
author: xiaoyongzhu
manager: asadk
editor: cgronlun
tags: azure-portal
ms.assetid: 71dcd1ad-4cad-47ad-8a9d-dcb7fa3c2ff9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: xiaoyzhu
ms.openlocfilehash: 95fd9efdcd7f93d8011808af943f52d4db13de8b
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808627"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Использование Caffe в кластере Azure HDInsight Spark для распределенного глубокого обучения


## <a name="introduction"></a>Введение

Глубокое обучение используется во всех отраслях, начиная от здравоохранения и заканчивая сферой транспортировки и производством. Компании используют глубокое обучение для решения сложных проблем, таких так [классификация образов](http://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [распознавание речи](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), распознавание объектов и машинный перевод. 

Существует [множество популярных платформ](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), в том числе [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), MXNet, Theano и т. д. Caffe — это самая известная платформа изучения нейронных сетей, работающая на уровне машинных команд. Она широко используется во многих областях, в том числе в компьютерном зрении. Кроме того, [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) объединяет Caffe с Apache Spark. В этом случае глубокое обучение можно легко использовать в существующем кластере Hadoop. Глубокое обучение можно использовать с конвейерами Spark ETL, чтобы уменьшить сложность системы и задержку для полного обучения решения.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) — это облачное предложение Hadoop, предоставляющее оптимизированные аналитические кластеры с открытым кодом для Spark, Hive, MapReduce, HBase, Storm, Kafka и R Server. Платформа HDInsight поддерживает соглашение об уровне обслуживания на 99,9 %. Каждую из этих технологий больших данных и каждое из этих приложений от независимых поставщиков программного обеспечения можно с легкостью развернуть в качестве управляемых кластеров, обеспечив при этом безопасность и мониторинг для предприятий.

В этой статье показано, как установить [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark) для кластера HDInsight. В этой статье также используется встроенная демонстрационная версия MNIST, на примере которой показано, как использовать распределенное глубокое обучение с помощью HDInsight Spark на ЦП.

Для выполнения этой задачи нужно пройти четыре этапа.

1. Установка необходимых зависимостей на всех узлах.
2. Создание CaffeOnSpark для HDInsight на головном узле.
3. Развертывание необходимых библиотек на всех рабочих узлах.
4. Разработка модели Caffe и ее распределенный запуск.

HDInsight — это платформа PaaS, предоставляющая расширенные функции, которые упрощают выполнение некоторых заданий. Одна из функций, используемая при выполнении действий, описанных в этой записи блога, называется [Script Action](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) (Действие скрипта). Она позволяет выполнять команды оболочки, необходимые для настройки узлов кластера (головного, рабочего или граничного).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Этап 1. Установка необходимых зависимостей на всех узлах

Чтобы начать работу, нужно установить необходимые зависимости. На сайте Caffe и [сайте CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) предлагается несколько полезных вики-сайтов об установке зависимостей для Spark в режиме YARN. HDInsight также использует Spark в режиме YARN. Тем не менее необходимо добавить несколько дополнительных зависимостей для платформы HDInsight. Для этого воспользуйтесь приведенным ниже действием сценария и выполните его на всех головных и рабочих узлах. Его выполнение занимает примерно 20 минут, так как эти зависимости также зависят от других пакетов. Это действие скрипта следует разместить в доступном для кластера HDInsight расположении, например в репозитории GitHub или в учетной записи хранилища BLOB-объектов по умолчанию.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


Приведенное выше действие сценария выполняется в два этапа. Первый этап — установка всех необходимых библиотек. Это библиотеки, необходимые для компиляции (например, gflags, glog) и запуска Caffe (например, numpy). Для оптимизации ЦП необходимо использовать библиотеку libatlas, но вы можете установить другие библиотеки, например MKL или CUDA (для графического процессора), следуя указаниям, приведенным на вики-сайте CaffeOnSpark.

Второй этап — скачивание, компиляция и установка ProtoBuf 2.5.0 для Caffe во время выполнения. ProtoBuf 2.5.0 — это [обязательный](https://github.com/yahoo/CaffeOnSpark/issues/87) элемент, но эта версия недоступна в виде пакета для Ubuntu 16. Поэтому вам нужно ее скомпилировать из исходного кода. В Интернете можно также найти несколько ресурсов по компиляции этой версии. Дополнительные сведения см. [здесь](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Чтобы приступить к работе, вы можете выполнить это действие сценария в кластере для всех рабочих и головных узлов (для HDInsight 3.5). Можно выполнить действия сценария в существующем кластере или использовать их во время создания кластера. Дополнительные сведения о действиях сценария см. в [этой документации](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions).

![Действия скриптов для установки зависимостей](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-spark-for-hdinsight-on-the-head-node"></a>Этап 2. Создание CaffeOnSpark для HDInsight на головном узле

Второй этап заключается в создании Caffe на головном узле и развертывании скомпилированных библиотек на всех рабочих узлах. На этом шаге необходимо [установить SSH-подключение к головному узлу](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). После этого необходимо следовать [процедуре сборки CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Ниже приведен сценарий, который можно использовать для выполнения сборки CaffeOnSpark с несколькими дополнительными действиями. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Вам может потребоваться выполнить больше действий, чем описано в документации по CaffeOnSpark. К этим дополнительным действиям относятся:
- Переход к конфигурации ЦП и настройка использования библиотеки libatlas именно для этого.
- Отправка наборов данных в хранилище BLOB-объектов, которое находится в общем расположении, доступном для всех рабочих узлов (для последующего использования).
- Отправка скомпилированных библиотек Caffe в хранилище BLOB-объектов и копирование этих библиотек во все узлы с помощью действий сценария, чтобы сократить время компиляции.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Устранение неполадок, связанных с ошибкой "An Ant BuildException has occured: exec returned: 2".

При первом создании CaffeOnSpark может отобразиться следующее сообщение об ошибке:

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Чтобы устранить эту проблему, очистите репозиторий кода, используя команду make clean, а затем выполните команду make build (при условии, что вы установили правильные зависимости).

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Устранение неполадок, связанных с ошибкой времени ожидания подключения к репозиторию Maven

Иногда при подключении к Maven возникает ошибка времени ожидания подключения. Пример фрагмента этой ошибки представлен ниже.

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Необходимо повторить попытку через несколько минут.


### <a name="troubleshooting-test-failure-for-caffe"></a>Устранение неполадок, связанных со сбоем тестирования Caffe

При выполнении окончательной проверки CaffeOnSpark может возникнуть ошибка тестирования. Скорее всего, это связано с кодировкой UTF-8, но эта ошибка не влияет на использование Caffe.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Этап 3. Развертывание необходимых библиотек на всех рабочих узлах.

Следующий этап заключается в развертывании библиотек (в основном они расположены в каталоге CaffeOnSpark/caffe-public/distribute/lib/ и CaffeOnSpark/caffe-distri/distribute/lib/) на всех узлах. На этапе 2 вы поместили эти библиотеки в хранилище BLOB-объектов. Теперь с помощью действий сценария это хранилище необходимо скопировать на все головные и рабочие узлы.

Чтобы сделать это, выполните действие сценария, как показано в следующем фрагменте.

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Обязательно укажите правильное расположение кластера.

На этапе 2 вы поместили это действие скрипта в хранилище BLOB-объектов, доступное для всех узлов, поэтому сейчас его нужно скопировать на все узлы.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Шаг 4. Разработка модели Caffe и ее распределенный запуск

Компонент Caffe устанавливается после выполнения предыдущих шагов. Следующий этап заключается в написании модели Caffe. 

Caffe использует "выразительную" архитектуру, где для разработки модели необходимо просто определить файл конфигурации, не выполняя работу с кодом (в большинстве случаев). Давайте рассмотрим этот процесс более подробно. 

Выбранная вами модель является примером модели для обучения MNIST. База данных образцов рукописного написания цифр MNIST содержит 60 000 образцов наборов данных для обучения и тестовый набор из 10 000 образцов. Это подмножество более широкого набора из базы данных NIST. Цифры были нормализованы по размеру и расположены в центре изображения фиксированного размера. CaffeOnSpark содержит некоторые скрипты, которые позволяют скачать набор данных и преобразовать его в правильный формат.

Эта система предоставляет несколько образцов сетевых топологий для обучения MNIST. Она предоставляет эффективную модель разбиения и оптимизации сетевой инфраструктуры (топология сети). В этом случае вам потребуется два указанных ниже файла. 

Файл "алгоритма решения" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) используется для отслеживания оптимизаций и создания обновлений параметров. Например, он определяет, следует ли использовать ЦП или GPU, а также определяет динамику, число итераций и т. д. Кроме того, он определяет, какую топологию нейронных сетей должна использовать программа (в этом случае вам нужен второй файл). Дополнительные сведения об алгоритме решения см. в [документации по Caffe](http://caffe.berkeleyvision.org/tutorial/solver.html).

Так как вы используете ЦП, а не графический процессор, измените последнюю строку следующим образом:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Конфигурация Caffe](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

При необходимости вы можете изменить и другие строки.

Второй файл (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) определяет, как выглядит нейронная сеть, а также соответствующий входной и выходной файл. Этот файл также необходимо обновить в соответствии с расположением данных для обучения. Измените в файле lenet_memory_train_test.prototxt следующие части (укажите расположение в соответствии с используемым кластером):

- Замените file:/Users/mridul/bigml/demodl/mnist_train_lmdb на wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb.
- Замените file:/Users/mridul/bigml/demodl/mnist_test_lmdb/ на wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb.

![Конфигурация Caffe](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Дополнительные сведения об определении сети см. в [документации Caffe по использованию набора данных MNIST](http://caffe.berkeleyvision.org/gathered/examples/mnist.html).

В этой статье вы используете данный пример MNIST. На головном узле выполните следующие команды.

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Предыдущая команда распределяет необходимые файлы (lenet_memory_solver.prototxt и lenet_memory_train_test.prototxt) во все контейнеры YARN. Она также задает относительный путь LD_LIBRARY_PATH для каждого драйвера или исполнителя Spark. Значение LD_LIBRARY_PATH определено в предыдущем фрагменте кода, оно указывает на расположение с библиотеками CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Мониторинг и устранение неполадок

Так как вы используете режим кластера YARN, время, когда драйвер Spark будет выполняться в случайном контейнере (и случайном рабочем узле), можно узнать, просто просмотрев выходные данные консоли примерно такого содержания:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Если вы хотите узнать, что произошло, просмотрите эти сведения в журнале драйвера Spark. В этом случае, чтобы найти соответствующие журналы YARN, вам потребуется перейти к пользовательскому интерфейсу YARN. Для этого перейдите по следующему URL-адресу: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![Пользовательский интерфейс YARN](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Здесь вы можете просмотреть сведения о количестве выделенных ресурсов для этого конкретного приложения. Щелкнув ссылку "Планировщик", вы увидите, что для этого приложения запущено девять контейнеров. Восемь из них используются в качестве исполнителей, а один для обработки драйвера. 

![Планировщик YARN](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

При сбое можно проверить журналы драйвера или контейнера. Чтобы просмотреть журналы драйвера, в пользовательском интерфейсе YARN щелкните идентификатор приложения, а затем нажмите кнопку "Журналы". Журналы драйвера записываются в поток stderr.

![Пользовательский интерфейс YARN 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Например, в некоторых журналах вы можете увидеть указанные ниже сообщения об ошибках, указывающие на наличие слишком большого числа исполнителей.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Иногда проблемы возникают с исполнителями, а не с драйверами. В этом случае необходимо проверить журналы контейнера. Вы всегда можете получить журналы контейнера, чтобы определить контейнер со сбоем. Например, этот сбой может произойти при запуске Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

В этом случае необходимо получить идентификатор контейнера, в котором произошел сбой (в приведенном выше примере это container_1485916338528_0008_05_000005). Затем на головном узле необходимо выполнить следующую команду: 

    yarn logs -containerId container_1485916338528_0008_03_000005

Проверив сведения о сбое контейнера, вы определите, что он произошел из-за использования режима графического процессора в файле lenet_memory_solver.prototxt (вместо режима ЦП).

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Получение результатов

Так как вы выделили 8 исполнителей и используете простую топологию сети, получение результатов занимает около 30 минут. В окне командной строки вы можете видеть, что модель была помещена в папку wasb:///mnist.model, а результаты— в папку wasb:///mnist_features_result.

Чтобы получить результаты, выполните следующую команду:

    hadoop fs -cat hdfs:///mnist_features_result/*

Результат должен выглядеть следующим образом:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

Параметр SampleID представляет идентификатор в наборе данных MNIST, а параметр label — это номер, определенный моделью.


## <a name="conclusion"></a>Заключение

В этой статье представлены сведения об установке CaffeOnSpark с использованием простого образца данных. HDInsight — это полностью управляемая облачная распределенная вычислительная платформа, оптимизированная для выполнения рабочих нагрузок машинного обучения и расширенной аналитики с использованием большого набора данных, а также для выполнения заданий распределенного глубокого обучения с помощью Caffe в HDInsight Spark.


## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)

