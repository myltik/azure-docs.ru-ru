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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 42d4f53aab9229890ddfadd64a7122cda9411686
ms.openlocfilehash: a1e85a3525d164d55aaaaf8c09cdf752e22e75d8


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight"></a>Использование хранилища озера данных Azure с помощью Apache Storm в HDInsight
Хранилище озера данных Azure — это служба облачного хранилища, совместимого с HDFS, которая предоставляет высокую пропускную способность, доступность, устойчивость и надежность данных. В этом документе вы узнаете, как использовать топологию Storm на языке Java для записи данных в хранилище озера данных Azure с помощью компонента [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) , который предоставляется как часть Apache Storm.

> [!IMPORTANT]
> Пример топологии, используемый в данном документе, зависит от компонентов, включенных в Storm в кластерах HDInsight, и может потребовать изменений для работы с хранилищем озера данных Azure при применении с другими кластерами Apache Storm.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или выше
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Подписка Azure
* Storm в кластере HDInsight версии 3.2. Чтобы создать новый Storm в кластере HDInsight, следуйте инструкциям в документе [Использование HDInsight с хранилищем озера данных с помощью Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) . Действия, описанные в этом документе, помогут вам создать новый кластер HDInsight и хранилище озера данных Azure.  
  
  > [!IMPORTANT]
  > При создании кластера HDInsight необходимо выбрать тип кластера **Storm** и версию **3.2**. Операционной системой может быть Windows или Linux.  
  > 
  > 

### <a name="configure-environment-variables"></a>Настройка переменных среды
Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* **JAVA_HOME** — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`.
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

### <a name="understanding-adlstorebolt"></a>Основные сведения о ADLStoreBolt
ADLStoreBolt — имя, используемое для экземпляра HdfsBolt в топологии, которая записывает в озеро данных Azure. Это не специальная версия HdfsBolt, созданная корпорацией Майкрософт. Однако оно полагается на значения конфигурации основного узла, а также на компоненты Hadoop, включенные в Azure HDInsight для связи с озером данных.

В частности, при создании кластера HDInsight можно связать его с хранилищем озера данных Azure. При этом в основной узел выбранного хранилища озера данных вводятся записи, используемые компонентами, такими как hadoop-client and hadoop-hdfs, для обеспечения взаимодействия с хранилищем озера данных.

> [!NOTE]
> Корпорация Майкрософт добавила код в проекты Apache Hadoop и Storm, обеспечивающие обмен данными с хранилищем озера данных Azure и хранилища BLOB-объектов Azure, но эта функция не может быть включена по умолчанию в другие дистрибутивы Hadoop и Storm.
> 
> 

Конфигурация для HdfsBolt в топологии выглядит следующим образом:

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

Если вы знакомы с использованием HdfsBolt, вы заметите, что конфигурация довольно стандартная, за исключением URL-адреса. URL-адрес предоставляет путь к корню хранилища озера данных Azure.

Поскольку для записи в хранилище озера данных применяется HdfsBolt и изменен только URL-адрес, можно воспользоваться любой существующей топологией, которая записывает данные в HDFS или WASB с помощью HdfsBolt, и легко изменить ее для использования хранилища озера данных Azure.

## <a name="build-and-package-the-topology"></a>Сборка и упаковка топологии
1. Скачайте пример проекта на странице [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) в среду разработки.
2. Откройте файл `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` в редакторе и найдите строку, которая содержит `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Замените **MYDATALAKE** именем Azure Data Lake Store, используемым при создании сервера HDInsight.
3. С помощью командной строки, терминала или сеанса работы с оболочкой измените каталоги на корень загруженного проекта и выполните следующие команды для сборки и упаковки топологии.
   
        mvn compile
        mvn package
   
    По завершении сборки и упаковки будет создан каталог `target`, который содержит файл `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Этот файл содержит скомпилированную топологию.

## <a name="deploy-and-run-on-linux-based-hdinsight"></a>Развертывание и запуск в HDInsight на базе Linux 
Если вы создали Storm под управлением Linux в кластере HDInsight, необходимо выполнить следующие действия для развертывания и запуска топологии.

1. Для копирования топологии в кластер HDInsight воспользуйтесь следующей командой. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   > [!NOTE]
   > При использовании клиента Windows для разработки у вас может не быть команды `scp`. В этом случае вы можете применить служебную программу `pscp`, доступную на странице [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   > 
   > 
2. После завершения отправки используйте следующую команду для подключения к кластеру HDInsight с помощью SSH. Замените **USER** именем пользователя SSH, используемым при создании кластера. Замените **CLUSTERNAME** именем кластера.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
   
   > [!NOTE]
   > Если вы используете клиент Windows для разработки, следуйте указаниям в статье о [подключении к HDInsight под управлением Linux с помощью SSH из Windows](hdinsight-hadoop-linux-use-ssh-windows.md) , чтобы использовать клиент PuTTY для подключения к кластеру.
   > 
   > 
3. После подключения запустите топологию следующей командой:
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
   
    Это приведет к запуску топологии с понятным именем `datalakewriter`.

## <a name="deploy-and-run-on-windows-based-hdinsight"></a>Развертывание и запуск в HDInsight на базе Windows 
1. Откройте веб-браузер и перейдите по адресу HTTPS://CLUSTERNAME.azurehdinsight.net, где **CLUSTERNAME** — это имя вашего кластера HDInsight. При появлении запроса укажите имя пользователя-администратора (`admin`) и пароль, использованный для этой учетной записи во время создания кластера.
2. На панели мониторинга Storm в раскрывающемся списке **JAR-файл** щелкните **Обзор**, а затем выберите файл StormToDataLakeStore-1.0-SNAPSHOT.jar из каталога `target`. Примените следующие значения для других операций в форме:
   
   * Имя класса: com.microsoft.example.StormToDataLakeStore
   * Дополнительные параметры: datalakewriter
     
     ![изображение панели мониторинга Storm](./media/hdinsight-storm-write-data-lake-store/submit.png)
3. Нажмите кнопку **Отправить**, чтобы передать и запустить топологию. После запуска топологии в поле результатов под кнопкой **Отправить** будет выведена информация, аналогичная приведенной ниже.
   
        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

## <a name="view-output-data"></a>Просмотр выходных данных
Существует несколько способов просмотреть данные. В этом разделе мы используем портал Azure и команду `hdfs` , чтобы просмотреть данные.

> [!NOTE]
> Следует разрешить топологиям поработать несколько минут перед проверкой выходных данных, чтобы синхронизировать данные с несколькими файлами в хранилище озера данных Azure.
> 
> 

* **На [портале Azure.](https://portal.azure.com)** Выберите Azure Data Lake Store, используемое с HDInsight.
  
  > [!NOTE]
  > Если вы не закрепили Data Lake Store на панели мониторинга портала Azure, его можно найти, щелкнув **Обзор** в нижней части списка, расположенного слева, а затем — **Data Lake Store** и, наконец, выбрав нужное хранилище.
  > 
  > 
  
    Среди значков в верхней части Data Lake Store выберите **Обозреватель данных**.
  
    ![значок просмотра данных](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
  
    Затем выберите папку **stormdata**. Должен отобразиться список текстовых файлов.
  
    ![текстовые файлы](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
  
    Выберите один из файлов, чтобы просмотреть его содержимое.
* **Из кластера.** Если вы подключились к кластеру HDInsight с использованием SSH (кластер Linux) или удаленного рабочего стола (кластер Windows), данные можно просмотреть следующим образом. Замените **DATALAKE** именем Data Lake Store.
  
        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt
  
    При этом будут объединены текстовые файлы, сохраненные в каталоге, и выведены сведения, аналогичные приведенным ниже:
  
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000

## <a name="stop-the-topology"></a>Остановка топологии
Топологии Storm будут выполняться до остановки или удаления кластера. Чтобы остановить топологии, используйте следующую информацию:

**Для HDInsight под управлением Linux**

Из сеанса SSH в кластер примените следующую команду:

    storm kill datalakewriter

**Для HDInsight под управлением Windows**

1. На панели мониторинга Storm (https://CLUSTERNAME.azurehdinsight.net) щелкните ссылку **Storm UI** (Пользовательский интерфейс Storm) в верхней части страницы.
2. После загрузки пользовательского интерфейса Storm щелкните ссылку **datalakewriter**.
   
    ![ссылка на datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)
3. В разделе **Topology Actions** (Действия с топологией) выберите **Удалить** и щелкните "ОК" в появившемся диалоговом окне.
   
    ![действия с топологией](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## <a name="delete-your-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы узнали, как применять Storm для записи в хранилище озера данных Azure, изучите другие [примеры Storm для HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Nov16_HO5-->


