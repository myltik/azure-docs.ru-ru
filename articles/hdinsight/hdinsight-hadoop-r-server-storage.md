<properties
   pageTitle="Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия) | Azure"
   description="Сведения о различных вариантах хранения, доступных для пользователей R Server в кластере HDInsight (предварительная версия)."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)

R Server в кластере HDInsight (предварительная версия) имеет доступ к обоим хранилищам BLOB-объектов Azure (а скоро получит доступ и к озеру данных Azure), используя их для сохранения данных, кода, объектов результатов после анализа и т. д.

При создании кластера Hadoop в HDInsight нужно указать учетную запись хранения Azure. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается для хранения файловой системы создаваемого кластера, например распределенной файловой системы Hadoop (HDFS). Для повышения производительности кластер HDInsight создается в том же центре обработки данных, где находится заданная вами основная учетная запись хранения. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md "Использование хранилища BLOB-объектов Azure с HDInsight").


## Использование нескольких учетных записей хранения BLOB-объектов Azure

При необходимости с помощью кластера можно получить доступ к нескольким учетным записям хранения Azure или контейнерам. Для этого необходимо указать дополнительные учетные записи хранения при создании кластера и выполнить приведенные ниже действия, чтобы использовать их в R.

1.	Предположим, вы создаете кластер HDInsight с учетной записью хранения "storage1" и контейнером по умолчанию "container1". Вы также указываете дополнительную учетную запись хранения "storage2".  
2.	Теперь вы скопировали файл "mycsv.csv" в каталог "/share" и хотите проанализировать его.  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	В коде R вы задаете имя узла "default" и выбираете каталог и файл для обработки.

    myNameNode <- "default" myPort <- 0

  Расположение данных

    bigDataDirRoot <- "/share"  

  Определение контекста вычислений Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Задание контекста вычислений

    rxSetComputeContext(mySparkCluster)

  Определение файловой системы HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Указание входного файла в HDFS для анализа

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
Все ссылки на каталог и файл указывают на учетную запись хранения wasb://container1@storage1.blob.core.windows.net, так как это **учетная запись хранения по умолчанию**, связанная с кластером HDInsight.

Теперь предположим, что вы хотите обработать файл с именем "mySpecial.csv", находящийся в каталоге "/private" контейнера "container2" в учетной записи хранения "storage2".

В коде R вы изменяете ссылку на узел имени, указав в ней учетную запись хранения "storage2".

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Расположение данных

    bigDataDirRoot <- "/private"

  Определение контекста вычислений Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Задание контекста вычислений

    rxSetComputeContext(mySparkCluster)

  Определение файловой системы HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Указание входного файла в HDFS для анализа

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
Теперь все ссылки на каталог и файл указывают на учетную запись хранения wasb://container2@storage2.blob.core.windows.net, так как это указанный вами **узел имени**.

Обратите внимание, что в учетной записи хранения "storage2" потребуется настроить каталог /user/RevoShare/<SSH username>:

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## Использование файлов Azure на граничном узле 

Существует также удобный способ хранения данных для граничного узла, называемый [служба файлов Azure](../storage/storage-how-to-use-files-linux.md "Файлы Azure"). Он позволяет подключить общую папку службы хранилища Azure к файловой системе Linux. Это подходит для хранения файлов данных, R-сценариев и объектов результатов, которые могут понадобиться позже, когда имеет смысл использовать на граничном узле собственную файловую систему вместо HDFS. Значительным преимуществом службы файлов Azure является то, что общие папки можно подключить и использовать в любой системе с поддерживаемой ОС (Win, Linux), например в другом кластере HDInsight, виртуальной машине Azure и даже локальной системе.


## Дальнейшие действия

Теперь, когда вы знаете, как создать кластер HDInsight, который включает R Server, а также основные принципы применения консоли R в сеансе SSH, ознакомьтесь с другими способами работы с R Server в HDInsight, описанными в следующих статьях.

- [Overview of R Server on Hadoop](hdinsight-hadoop-r-server-overview.md) (Общие сведения об R Server в Hadoop)
- [Get started with R server on Hadoop](hdinsight-hadoop-r-server-get-started.md) (Начало работы с R Server в Hadoop)
- [Add RStudio Server to HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md) (Добавление RStudio Server в HDInsight "Премиум")
- [Compute context options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md) (Параметры контекста вычислений для R Server в HDInsight "Премиум")

<!---HONumber=AcomDC_0420_2016-->