<properties
pageTitle="Использование хранилища озера данных Azure с помощью Apache Storm в HDInsight Azure"
description="Узнайте, как записывать данные в хранилище данных озера Azure из топологии Apache Storm в HDInsight. В этом документе и связанном примере демонстрируется, как компонент HdfsBolt можно использовать для записи в хранилище данных озера."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="06/17/2016"
ms.author="larryfr"/>

#Использование хранилища озера данных Azure с помощью Apache Storm в HDInsight

Хранилище озера данных Azure — это служба облачного хранилища, совместимого с HDFS, которая предоставляет высокую пропускную способность, доступность, устойчивость и надежность данных. В этом документе вы узнаете, как использовать топологию Storm на языке Java для записи данных в хранилище озера данных Azure с помощью компонента [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), который предоставляется как часть Apache Storm.

> [AZURE.IMPORTANT] Пример топологии, используемый в данном документе, зависит от компонентов, включенных в Storm в кластерах HDInsight, и может потребовать изменений для работы с хранилищем озера данных Azure при применении с другими кластерами Apache Storm.

##Предварительные требования

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или выше
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Подписка Azure
* Storm в кластере HDInsight версии 3.2. Чтобы создать новый Storm в кластере HDInsight, следуйте инструкциям в документе [Использование HDInsight с хранилищем озера данных с помощью Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Действия, описанные в этом документе, помогут вам создать новый кластер HDInsight и хранилище озера данных Azure.  

    > [AZURE.IMPORTANT] При создании кластера HDInsight необходимо выбрать тип кластера __Storm__ и версию __3.2__. Операционной системой может быть Windows или Linux.

###Настройка переменных среды

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* __JAVA\_HOME__ — эта переменная должна указывать на каталог, в который установлена среда выполнения Java (JRE). Например, в дистрибутиве Unix или Linux она должна иметь примерно такое значение: `/usr/lib/jvm/java-7-oracle` В Windows значение будет приблизительно таким: `c:\Program Files (x86)\Java\jre1.7`.

* __PATH__ — эта переменная должна содержать следующие пути:

    * __JAVA\_HOME__ или эквивалентный путь;
    
    * __JAVA\_HOME\\bin__ или эквивалентный путь.
    
    * Каталог, в который установлено ПО Maven.

##Реализация топологии

Пример, используемый в этом документе, написан на Java и использует следующие компоненты:

* __TickSpout__: создает данные, используемые другими компонентами в топологии.

* __PartialCount__: подсчитывает количество событий, созданных TickSpout.

* __FinalCount__: агрегирует данные подсчета PartialCount.

* __ADLStoreBolt__: записывает данные в хранилище озера данных Azure с помощью компонента [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Проект, содержащий эту топологию, можно скачать на странице [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

###Основные сведения о ADLStoreBolt

ADLStoreBolt — имя, используемое для экземпляра HdfsBolt в топологии, которая записывает в озеро данных Azure. Это не специальная версия HdfsBolt, созданная корпорацией Майкрософт. Однако оно полагается на значения конфигурации основного узла, а также на компоненты Hadoop, включенные в Azure HDInsight для связи с озером данных.

В частности, при создании кластера HDInsight можно связать его с хранилищем озера данных Azure. При этом в основной узел выбранного хранилища озера данных вводятся записи, используемые компонентами, такими как hadoop-client and hadoop-hdfs, для обеспечения взаимодействия с хранилищем озера данных.

> [AZURE.NOTE] Корпорация Майкрософт добавила код в проекты Apache Hadoop и Storm, обеспечивающие обмен данными с хранилищем озера данных Azure и хранилища BLOB-объектов Azure, но эта функция не может быть включена по умолчанию в другие дистрибутивы Hadoop и Storm.

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

##Сборка и упаковка топологии

1. Скачайте пример проекта на странице [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) в среду разработки.

2. Откройте файл `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` в редакторе и найдите строку, которая содержит `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Замените __MYDATALAKE__ именем хранилища озера данных Azure, которое вы использовали при создании сервера HDInsight.

3. С помощью командной строки, терминала или сеанса работы с оболочкой измените каталоги на корень загруженного проекта и выполните следующие команды для сборки и упаковки топологии.

        mvn compile
        mvn package
    
    По завершении сборки и упаковки будет создан каталог `target`, который содержит файл `StormToDataLakeStore-1.0-SNAPSHOT.jar`. Этот файл содержит скомпилированную топологию.

##Развертывание и запуск в HDInsight на базе Linux

Если вы создали Storm под управлением Linux в кластере HDInsight, необходимо выполнить следующие действия для развертывания и запуска топологии.

1. Для копирования топологии в кластер HDInsight воспользуйтесь следующей командой. Замените __USER__ именем пользователя SSH, которое вы использовали при создании кластера. Замените __CLUSTERNAME__ именем кластера.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
    
    > [AZURE.NOTE] При использовании клиента Windows для разработки у вас может не быть команды `scp`. В этом случае вы можете применить служебную программу `pscp`, доступную на странице [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. После завершения отправки используйте следующую команду для подключения к кластеру HDInsight с помощью SSH. Замените __USER__ именем пользователя SSH, которое вы использовали при создании кластера. Замените __CLUSTERNAME__ именем кластера.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    При появлении запроса введите пароль, который применялся при создании пользователя SSH для кластера. Если вместо пароля используется открытый ключ, может потребоваться использовать параметр `-i` и указать путь к соответствующему закрытому ключу.
    
    > [AZURE.NOTE] Если вы используете клиент Windows для разработки, следуйте указаниям в статье о [подключении к HDInsight под управлением Linux с помощью SSH из Windows](hdinsight-hadoop-linux-use-ssh-windows.md), чтобы использовать клиент PuTTY для подключения к кластеру.
    
3. После подключения запустите топологию следующей командой:

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    Это приведет к запуску топологии с понятным именем `datalakewriter`.

##Развертывание и запуск в HDInsight на базе Windows

1. Откройте веб-браузер и перейдите по адресу HTTPS://CLUSTERNAME.azurehdinsight.net, где __CLUSTERNAME__ — это имя вашего кластера HDInsight. При появлении запроса укажите имя пользователя-администратора (`admin`) и пароль, использованный для этой учетной записи во время создания кластера.

2. На панели мониторинга Storm в раскрывающемся списке __Jar-файл__ выберите __Обзор__, а затем выберите файл StormToDataLakeStore-1.0-SNAPSHOT.jar из каталога `target`. Примените следующие значения для других операций в форме:

    * Имя класса: com.microsoft.example.StormToDataLakeStore
    * Дополнительные параметры: datalakewriter
    
    ![изображение панели мониторинга Storm](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. Нажмите кнопку __Отправить__, чтобы передать и запустить топологию. После запуска топологии в поле результатов под кнопкой __Отправить__ будет выведена информация, аналогичная приведенной ниже.

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##Просмотр выходных данных

Существует несколько способов просмотреть данные. В этом разделе мы используем портал Azure и команду `hdfs`, чтобы просмотреть данные.

> [AZURE.NOTE] Следует разрешить топологиям поработать несколько минут перед проверкой выходных данных, чтобы синхронизировать данные с несколькими файлами в хранилище озера данных Azure.

* __На [портале Azure](https://portal.azure.com)__: выберите хранилище озера данных Azure, которое используется с HDInsight.

    > [AZURE.NOTE] Если вы не закрепили хранилище озера данных Azure на панели мониторинга портала, его можно найти, выбрав __Обзор__ в нижней части списка, расположенного слева, затем — __Хранилище озера данных__ и, наконец, щелкнув нужное хранилище.
    
    Среди значков в верхней части хранилища озера данных выберите __Обозреватель данных__.
    
    ![значок просмотра данных](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    Затем выберите папку __stormdata__. Должен отобразиться список текстовых файлов.
    
    ![текстовые файлы](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    Выберите один из файлов, чтобы просмотреть его содержимое.

* __Из кластера__: если вы подключились к кластеру HDInsight с использованием SSH (кластер Linux) или удаленного рабочего стола (кластер Windows), данные можно просмотреть следующим образом. Замените __DATALAKE__ именем хранилища озера данных.

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
        
##Остановка топологии

Топологии Storm будут выполняться до остановки или удаления кластера. Чтобы остановить топологии, используйте следующую информацию:

__Для HDInsight под управлением Linux__

Из сеанса SSH в кластер примените следующую команду:

    storm kill datalakewriter

__Для HDInsight под управлением Windows__

1. На панели мониторинга Storm (https://CLUSTERNAME.azurehdinsight.net,) выберите ссылку __Storm UI__ (Пользовательский интерфейс Storm) в верхней части страницы.

2. После загрузки пользовательского интерфейса Storm выберите ссылку __datalakewriter__.

    ![ссылка на datalakewriter](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. В разделе __Topology Actions__ (Действия с топологией) выберите __Удалить__ и нажмите кнопку "ОК" в появившемся диалоговом окне.

    ![действия с топологией](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## Удаление кластера

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Дальнейшие действия

Теперь, когда вы узнали, как применять Storm для записи в хранилище озера данных Azure, изучите другие [примеры Storm для HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0622_2016-->