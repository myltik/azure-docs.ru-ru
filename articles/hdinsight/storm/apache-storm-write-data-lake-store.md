---
title: Запись данных Apache Storm в хранилище Azure или Data Lake Store для Azure HDInsight | Документация Майкрософт
description: Сведения об использовании Apache Storm для записи данных в HDFS-совместимое хранилище для HDInsight. Хранилище Azure или Azure Data Lake Store предоставляет HDFS-совместимое хранилище для HDInsight. В этом документе и связанном примере демонстрируется, как компонент HdfsBolt можно использовать для записи в хранилище Storm по умолчанию в кластере HDInsight.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 0c870b0c8de648ac65bec6857bf850c2913e7aeb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Запись данных в HDFS из Apache Storm в HDInsight

Узнайте об использовании Storm для записи данных в HDFS-совместимое хранилище, используемое Apache Storm в HDInsight. HDInsight может использовать службу хранилища Azure и Azure Data Lake Store в качестве HDFS-совместимого хранилища. Storm предоставляет компонент [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), который записывает данные в HDFS. В этой статье предоставляются сведения о записи данных в хранилища обоих типов из HdfsBolt. 

> [!IMPORTANT]
> Пример топологии, используемый в этом документе, зависит от компонентов, которые входят в состав Storm в HDInsight. Может потребоваться изменить его для работы с Azure Data Lake Store при использовании с другими кластерами Apache Storm.

## <a name="get-the-code"></a>Получение кода

Проект, содержащий эту топологию, можно загрузить отсюда: [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Чтобы скомпилировать этот проект, требуется следующая конфигурация среды разработки:

* Пакет [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 1.8 или более поздней версии. Для HDInsight 3.5 или более поздней версии требуется Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* `JAVA_HOME`. Эта переменная должна указывать на каталог, в который установлен JDK.
* `PATH`. Эта переменная должна содержать следующие пути:
  
    * `JAVA_HOME` или эквивалентный путь.
    * `JAVA_HOME\bin` или эквивалентный путь.
    * Каталог, в который установлено ПО Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Как использовать HdfsBolt с HDInsight

> [!IMPORTANT]
> Перед использованием HdfsBolt Storm в HDInsight необходимо выполнить действие скрипта, чтобы скопировать необходимые JAR-файлы в `extpath` для Storm. Дополнительные сведения см. в статье [Использование Azure Data Lake Store с помощью Apache Storm в HDInsight (Java)](#configure).

HdfsBolt использует предоставленную схему файла, чтобы понять, как выполнять запись данных в HDFS. Для HDInsight используйте одну из следующих схем:

* `wasb://` — используется с учетной записью хранения Azure.
* `adl://` — используется с Azure Data Lake Store.

Следующая таблица содержит примеры использования схемы файлов для разных сценариев.

| Схема | Заметки |
| ----- | ----- |
| `wasb:///` | Учетная запись хранения по умолчанию — это контейнер больших двоичных объектов в учетной записи хранения Azure. |
| `adl:///` | Учетная запись хранения по умолчанию представляет собой каталог в Azure Data Lake Store. Во время создания кластера укажите каталог в Data Lake Store, который является корнем системы HDFS кластера. Например, каталог `/clusters/myclustername/`. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Учетная запись хранения Azure не по умолчанию (дополнительная), связанная с кластером. |
| `adl://STORENAME/` | Корень Data Lake Store, используемый кластером. Эта схема позволяет получить доступ к данным, размещенным вне каталога, в котором содержится файловая система кластера. |

Дополнительные сведения см. в [справочнике HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) на сайте Apache.org.

### <a name="example-configuration"></a>Пример конфигурации

Следующий YAML-файл является фрагментом файла `resources/writetohdfs.yaml`, который включен в пример. Этот файл определяет топологию Storm с помощью платформы [Flux](https://storm.apache.org/releases/1.1.0/flux.html) для Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Это YAML-файл определяет следующие элементы:

* `syncPolicy` — определяет, когда файлы синхронизируются или записываются в файловую систему. В этом примере это происходит после каждой 1000 кортежей.
* `fileNameFormat` — определяет шаблон пути и имени файла, используемый при записи файлов. В этом примере путь предоставляется во время выполнения с помощью фильтра, а файл имеет расширение `.txt`.
* `recordFormat` — определяет внутренний формат записанных файлов. В этом примере поля разделяются с помощью символа `|`.
* `rotationPolicy` — определяет, когда чередуются файлы. В этом примере чередование не выполняется.
* `hdfs-bolt` — использует предыдущие компоненты в качестве параметров конфигурации для класса `HdfsBolt`.

Дополнительные сведения о платформе Flux см. здесь: [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Настройка кластера

По умолчанию Storm в HDInsight не содержит компоненты, которые HdfsBolt использует для взаимодействия с хранилищем Azure или Data Lake Store в пути к классу Storm. Используйте следующее действие скрипта, чтобы добавить эти компоненты в каталог `extlib` для Storm в кластере:

* URI-адрес сценария: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Узлы, к которым применяется: Nimbus, Supervisor
* Параметры: нет

Сведения об использовании этого скрипта с кластером см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Сборка и упаковка топологии

1. Загрузите пример проекта отсюда [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) в среду разработки.

2. С помощью командной строки, терминала или сеанса работы с оболочкой измените каталоги на корень загруженного проекта. Для сборки и создания пакета топологии выполните следующую команду:
   
        mvn compile package
   
    По завершении сборки и упаковки будет создан каталог `target`, который содержит файл `StormToHdfs-1.0-SNAPSHOT.jar`. Этот файл содержит скомпилированную топологию.

## <a name="deploy-and-run-the-topology"></a>Развертывание и запуск топологии

1. Для копирования топологии в кластер HDInsight воспользуйтесь следующей командой. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   > [!NOTE]
   > Дополнительные сведения об использовании `scp` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. После завершения отправки используйте следующую команду для подключения к кластеру HDInsight с помощью SSH. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. После подключения создайте файл `dev.properties` с помощью следующей команды.

        nano dev.properties

4. Добавьте в файл `dev.properties` следующее содержимое:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > В этом примере предполагается, что кластер использует учетную запись хранения Azure в качестве хранилища по умолчанию. Если ваш кластер использует Azure Data Lake Store, используйте `hdfs.url: adl:///`.
    
    Чтобы сохранить этот файл, нажмите клавиши __CTRL+X__, введите __Y__ и нажмите клавишу __ВВОД__. Значения в этом файле позволяют задать URL-адрес Data Lake Store и имя каталога, в который записываются данные.

3. Запустите топологию, используя следующую команду.
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Эта команда запускает топологию с помощью платформы Flux и отправляет ее в узел Nimbus кластера. Топология определяется в файле `writetohdfs.yaml`, включенном в JAR-файл. Файл `dev.properties` передается в качестве фильтра, и топология считывает содержащиеся в нем значения.

## <a name="view-output-data"></a>Просмотр выходных данных

Для просмотра данных используйте следующую команду.

    hdfs dfs -ls /stormdata/

Отобразится список файлов, созданных с помощью этой топологии.

Ниже приведен пример списка, возвращаемого предыдущими командами.

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Остановка топологии

Топологии Storm будут выполняться до остановки или удаления кластера. Для остановки топологии введите следующую команду:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как применять Storm для записи в хранилище Azure и Azure Data Lake Store, изучите другие [примеры Storm для HDInsight](apache-storm-example-topology.md).

