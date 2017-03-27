---
title: "Использование хранилища озера данных Azure с помощью Apache Storm в HDInsight Azure"
description: "Узнайте, как записывать данные в хранилище данных озера Azure из топологии Apache Storm в HDInsight. В этом документе и связанном примере демонстрируется, как компонент HdfsBolt можно использовать для записи в хранилище данных озера."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Использование Azure Data Lake Store с помощью Apache Storm в HDInsight (Java)

Хранилище озера данных Azure — это служба облачного хранилища, совместимого с HDFS, которая предоставляет высокую пропускную способность, доступность, устойчивость и надежность данных. В этом документе вы узнаете, как использовать топологию Storm на платформе Java для записи данных в Azure Data Lake Store. Действия, описанные в этом документа, выполняются с помощью компонента [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), который предоставляется в составе Apache Storm.

> [!IMPORTANT]
> Пример топологии, используемый в данном документе, зависит от компонентов, включенных в Storm в кластерах HDInsight, и может потребовать изменений для работы с хранилищем озера данных Azure при применении с другими кластерами Apache Storm.

## <a name="how-to-work-with-azure-data-lake-store"></a>Работа с Azure Data Lake Store

В HDInsight Data Lake Store — это совместимая с HDFS файловая система, поэтому для записи можно использовать элемент bolt Storm-HDFS. При работе с Azure Data Lake из HDInsight можно использовать схему файлов `adl://`.

* Если Data Lake Store является основным хранилищем для кластера, то используйте `adl:///`. Это корень хранилища кластера в Azure Data Lake. Его можно преобразовать в путь /clusters/ИМЯ_КЛАСТЕРА в вашей учетной записи Data Lake Store.
* Если Data Lake Store является дополнительным хранилищем для кластера, то используйте `adl://DATALAKEACCOUNT.azuredatalakestore.net/`. Этот универсальный код ресурса (URI) указывает учетную запись Data Lake Store, в которую записываются данные. Данные записываются, начиная с корня Data Lake Store.

    > [!NOTE]
    > Можно также использовать этот формат универсального кода ресурса (URI) для сохранения данных в учетную запись Data Lake Store, которая содержит основное хранилище для кластера. Это позволяет сохранить данные за пределами каталога, который содержит HDInsight.

В следующем коде Java показано использование элемента bolt Storm-HDFS для записи данных в каталог `/stormdata` учетной записи Data Lake Store `MYDATALAKE`.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

Ниже приведен код YAML, демонстрирующий использование элемента bolt Storm-HDFS из платформы Flux.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

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

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

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
    parallelism: 1
```

> [!NOTE]
> В примерах, приведенных в этом документе, используется платформа Flux.

## <a name="prerequisites"></a>Предварительные требования

* Пакет [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 1.8 или более поздней версии. Для работы HDInsight 3.5 требуется Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Кластер Storm в HDInsight версии 3.5. Чтобы создать новый Storm в кластере HDInsight, следуйте инструкциям в документе [Использование HDInsight с хранилищем озера данных с помощью Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) .

### <a name="configure-environment-variables"></a>Настройка переменных среды

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-8-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.8`.
* **PATH** — эта переменная должна содержать следующие пути:
  
  * **JAVA\_HOME** или эквивалентный путь;
  * **JAVA\_HOME\bin** или эквивалентный путь;
  * Каталог, в который установлено ПО Maven.

## <a name="topology-implementation"></a>Реализация топологии

Пример, используемый в этом документе, написан на Java и использует следующие компоненты:

* **TickSpout** — создает данные, используемые другими компонентами в топологии;
* **PartialCount** — подсчитывает количество событий, созданных TickSpout;
* **FinalCount** — агрегирует данные подсчета PartialCount;
* **ADLStoreBolt** — записывает данные в Azure Data Lake Store с помощью компонента [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Проект, содержащий эту топологию, можно скачать на странице [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

## <a name="build-and-package-the-topology"></a>Сборка и упаковка топологии

1. Скачайте пример проекта на странице [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) в среду разработки.

2. Откройте файл `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` в редакторе и найдите строку, которая содержит `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Замените **MYDATALAKE** именем Azure Data Lake Store, используемым при создании сервера HDInsight.

3. С помощью командной строки, терминала или сеанса работы с оболочкой измените каталоги на корень загруженного проекта и выполните следующие команды для сборки и упаковки топологии.
   
        mvn compile
        mvn package
   
    По завершении сборки и упаковки будет создан каталог `target`, который содержит файл `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Этот файл содержит скомпилированную топологию.

## <a name="deploy-and-run-the-topology"></a>Развертывание и запуск топологии

1. Для копирования топологии в кластер HDInsight воспользуйтесь следующей командой. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   > [!NOTE]
   > При использовании клиента Windows для разработки у вас может не быть команды `scp`. В этом случае вы можете применить служебную программу `pscp`, доступную на странице [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. После завершения отправки используйте следующую команду для подключения к кластеру HDInsight с помощью SSH. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   > [!NOTE]
   > Если вы используете клиент Windows для разработки, следуйте указаниям в статье о [подключении к HDInsight под управлением Linux с помощью SSH из Windows](hdinsight-hadoop-linux-use-ssh-windows.md) , чтобы использовать клиент PuTTY для подключения к кластеру.

3. После подключения создайте файл `dev.properties` с помощью следующей команды.

        nano dev.properties

4. Добавьте в файл `dev.properties` следующее содержимое:

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    Чтобы сохранить этот файл, нажмите клавиши __CTRL+X__, введите __Y__ и нажмите клавишу __ВВОД__. Значения в этом файле позволяют задать URL-адрес Data Lake Store и имя каталога, в который записываются данные.

3. Запустите топологию, используя следующую команду.
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    Эта команда запускает топологию с помощью платформы Flux. Топология определяется в файле `datalakewriter.yaml`, включенном в JAR-файл. Файл `dev.properties` передается в качестве фильтра, и топология считывает содержащиеся в нем значения.

## <a name="view-output-data"></a>Просмотр выходных данных

Для просмотра данных используйте следующую команду.

    hdfs dfs -ls /stormdata/

Она отобразит список файлов, которые были созданы топологией.

Если Data Lake Store не является хранилищем по умолчанию для кластера, выполните следующую команду, чтобы просмотреть данные.

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

В предыдущей команде замените __MYDATALAKE__ учетной записью Data Lake Store.

Ниже приведен пример списка, возвращаемого предыдущими командами.

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>Остановка топологии

Топологии Storm будут выполняться до остановки или удаления кластера. Чтобы остановить топологии, используйте следующую информацию:

**Для HDInsight под управлением Linux**

Из сеанса SSH в кластер примените следующую команду:

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как применять Storm для записи в хранилище озера данных Azure, изучите другие [примеры Storm для HDInsight](hdinsight-storm-example-topology.md).


