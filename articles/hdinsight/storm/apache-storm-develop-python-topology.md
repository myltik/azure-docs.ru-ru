---
title: Apache Storm с компонентами Python. Azure HDInsight | Документация Майкрософт
description: Сведения о создании топологии Apache Storm, использующей компоненты Python.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
keywords: apache storm python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 0a8930da2d44714573155477d791dd9cb78d872a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Разработка топологий Apache Storm с помощью Python в HDInsight

В этой статье приведены сведения о создании топологии Apache Storm, использующей компоненты Python. Apache Storm поддерживает несколько языков и даже позволяет объединять компоненты из нескольких языков в одной топологии. Платформа Flux (впервые появилась в Storm 0.10.0) позволяет легко создавать решения, использующие компоненты Python.

> [!IMPORTANT]
> Сведения в этом документе были проверены с использованием Storm в HDInsight 3.6. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Код для этого проекта можно найти по адресу [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>предварительным требованиям

* Python 2.7 или выше;

* Пакет Java JDK 1.8 или более поздней версии.

* Maven 3.

* (Необязательно.) Локальная среда разработки Storm. Локальная среда разработки Storm требуется только в том случае, если вы хотите запускать топологию локально. Дополнительные сведения см. в разделе [Setting up a development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) (Настройка среды разработки).

## <a name="storm-multi-language-support"></a>Многоязыковая поддержка Storm

Среда разработки Apache Storm была разработана для работы с компонентами, написанными на любом языке программирования. Компоненты должны "понимать", как работать с [определением Thrift для Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). В рамках проекта Apache Storm предоставляется модуль для Python, который позволяет легко взаимодействовать со Storm. Этот модуль можно найти по адресу [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm является процессом Java, который работает на виртуальной машине Java (JVM). Компоненты, написанные на других языках, выполняются как подпроцессы. Storm взаимодействуют с этими подпроцессами с помощью сообщений JSON, отправляемых через стандартные потоки stdin и stdout. Дополнительные сведения о связи между компонентами можно найти в документации по [многоязыковому протоколу](https://storm.apache.org/documentation/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python с платформой Flux

Платформа Flux позволяет определять топологии Storm отдельно от компонентов. Она использует YAML для определения топологии Storm. Ниже приведен пример указания ссылки на компонент Python в документе YAML.

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Класс `FluxShellSpout` используется для сценария `sentencespout.py`, который реализует spout.

Flux ожидает, что сценарии Python находятся в каталоге `/resources` в JAR-файле, содержащем топологию. Поэтому в этом примере сценарии Python хранятся в каталоге `/multilang/resources`. В `pom.xml` этот файл указан с помощью приведенного ниже кода XML.

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Как упоминалось ранее, существует файл `storm.py`, который реализует определение Thrift для Storm. Платформа Flux добавляет `storm.py` автоматически при выполнении сборки проекта, поэтому не нужно беспокоиться о его добавлении.

## <a name="build-the-project"></a>Сборка проекта

В корневом каталоге проекта выполите следующую команду.

```bash
mvn clean compile package
```

Она создает файл `target/WordCount-1.0-SNAPSHOT.jar`, содержащий скомпилированную топологию.

## <a name="run-the-topology-locally"></a>Локальный запуск топологии

Для локального запуска топологии введите следующую команду.

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Для ее выполнения нужна локальная среда разработки Storm. Дополнительные сведения см. в разделе [Setting up a development environment](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html) (Настройка среды разработки).

После запуска топология выдает в локальную консоль информацию следующего вида.


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Чтобы остановить топологию, нажмите сочетание клавиш __CTRL+C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Запуск топологии Storm в HDInsight

1. Воспользуйтесь приведенной ниже командой, чтобы скопировать файл `WordCount-1.0-SNAPSHOT.jar` в кластер Storm в HDInsight.

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Замените `sshuser` именем пользователя SSH для кластера. Замените `mycluster` именем кластера. При появлении запроса введите пароль пользователя SSH.

    Дополнительные сведения об использовании SSH и SCP см. в разделе [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. После завершения передачи файла подключитесь к кластеру с помощью протокола SSH.

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. В сеансе SSH используйте следующую команду, чтобы запустить топологию на кластере.

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Для просмотра топологии в кластере можно использовать пользовательский интерфейс Storm. Пользовательский интерфейс Storm находится по адресу https://mycluster.azurehdinsight.net/stormui. Замените `mycluster` именем кластера.

> [!NOTE]
> После запуска топология Storm выполняется до тех пор, пока она не будет остановлена. Чтобы остановить топологию, используйте один из следующих методов:
>
> * выполните команду `storm kill TOPOLOGYNAME` из командной строки;
> * нажмите кнопку **Kill** (Завершить) в пользовательском интерфейсе Storm.


## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать о других способах использования Python с HDInsight, см. следующие документы.

* [Использование Python для потоковой передачи заданий MapReduce.](../hadoop/apache-hadoop-streaming-python.md)
* [Использование определяемых пользователем функций Python (UDF) в Pig и Hive.](../hadoop/python-udf-hdinsight.md)
