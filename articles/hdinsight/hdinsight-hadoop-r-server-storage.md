
<properties
   pageTitle="Возможности службы хранилища Azure для R Server в HDInsight (предварительная версия) | Microsoft Azure"
   description="Сведения о разных вариантах хранения, доступных для пользователей R Server в кластере HDInsight (предварительная версия)."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="jeffstok"
/>

# Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)

Microsoft R Server в кластере HDInsight (предварительная версия) имеет доступ к хранилищу BLOB-объектов Azure и [хранилищу озера данных Azure](https://azure.microsoft.com/services/data-lake-store/), используя их для сохранения данных, кода, результирующих объектов после анализа и т. д.

При создании кластера Hadoop в HDInsight нужно указать учетную запись хранения Azure. Из этой учетной записи назначается конкретный контейнер хранилища BLOB-объектов для хранения файловой системы создаваемого кластера, например распределенной файловой системы Hadoop (HDFS). Для повышения производительности кластер HDInsight создается в том же центре обработки данных, где находится заданная вами основная учетная запись хранения. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md "Использование хранилища BLOB-объектов Azure с HDInsight").


## Использование нескольких учетных записей хранения BLOB-объектов Azure

При необходимости вы можете осуществлять доступ из кластера HDI к нескольким учетным записям хранения Azure или контейнерам. Для этого при создании кластера следует указать дополнительные учетные записи хранения и выполнить описанный здесь процесс их использования в R.

1.	Создайте кластер HDInsight с учетной записью хранения **storage1** и контейнером по умолчанию **container1**.
2. Укажите также дополнительную учетную запись хранения с именем **storage2**.
3. Скопируйте файл mycsv.csv в каталог /share и выполните анализ этого файла.

    ````
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal myscsv.scv /share  
    ````

3.	В коде R задайте узел имени **default** и определите каталог и файл для обработки.

    ````
    myNameNode <- "default"
    myPort <- 0
    ````

  Расположение данных

    bigDataDirRoot <- "/share"  

  Определение контекста вычислений Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Определение контекста вычислений

    rxSetComputeContext(mySparkCluster)

  Определите распределенную файловую систему Hadoop (HDFS).

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Укажите входной файл в HDFS для анализа.

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Все ссылки на каталоги и файлы указывают на учетную запись хранения wasbs://container1@storage1.blob.core.windows.net. Это **учетная запись хранения по умолчанию**, связанная с кластером HDInsight.

Теперь предположим, что вы хотите обработать файл с именем mySpecial.csv, расположенный в каталоге /private контейнера **container2** в учетной записи хранения **storage2**.

В коде R измените ссылку на узел имени так, чтобы она указывала на учетную запись хранения **storage2**.

    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Расположение данных

    bigDataDirRoot <- "/private"

  Определение контекста вычислений Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

  Определение контекста вычислений

    rxSetComputeContext(mySparkCluster)

  Определение файловой системы HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Укажите входной файл в HDFS для анализа.

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Теперь все ссылки на каталоги и файлы указывают на учетную запись хранения wasbs://container2@storage2.blob.core.windows.net. Это указанный вами **узел имени**.

Обратите внимание, что в учетной записи хранения **storage2** следует настроить каталог /user/RevoShare/<имя пользователя SSH>.

    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Использование хранилища озера данных Azure

Чтобы использовать в учетной записи HDInsight хранилища озера данных, следует предоставить кластеру доступ к каждому из хранилищ озера данных Azure, которые вы хотите использовать. Вы можете использовать такое хранилище в скрипте R примерно так же, как дополнительную учетную запись хранения (как описано в предыдущей процедуре).

## Предоставление кластеру доступа к хранилищам озера данных Azure

Доступ к хранилищу озера данных Azure осуществляется с помощью субъекта-службы Azure Active Directory (AAD), связанного с кластером HDInsight.

### Добавление субъекта-службы
1. Когда вы создаете кластер HDInsight, выберите на вкладке **Источник данных** вариант **Удостоверение кластера AAD**.
2. В диалоговом окне **Удостоверение кластера AAD** в разделе **Выбрать субъект-службу AD** выберите **Создать**.

Когда вы присвоите имя и создадите пароль для субъекта-службы, откроется новая вкладка, где можно связать субъект-службу с хранилищами озера данных.

Обратите внимание, что доступ к Data Lake Store можно добавить и позже, открыв Data Lake Store на портале Azure и выбрав **Обозреватель данных** > **Доступ**. На следующем примере диалогового окна показано, как можно создать субъект-службу и связать его с хранилищем озера данных rkadl11.

![Создание субъекта-службы хранилища озера данных 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Создание субъекта-службы хранилища озера данных 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Использование хранилища озера данных с R Server
Когда вы предоставите доступ к хранилищу озера данных, это хранилище можно будет использовать в R Server на HDInsight так же, как дополнительную учетную запись хранения Azure. Единственное отличие состоит в том, что вместо префикса **wasb://** используется **adl://**.

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Ниже представлены команды, которые позволяют настроить учетную запись хранилища озера данных c каталогом RevoShare с использованием примера CSV-файла из приведенного выше примера.

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Использование файлов Azure на граничном узле

Для граничных узлов существует еще один удобный вариант хранения — [файлы Azure](../storage/storage-how-to-use-files-linux.md "Файлы Azure"). Они позволяют подключить общую папку службы хранения Azure к файловой системе Linux. Это удобно для хранения файлов данных, R-сценариев и объектов результатов, которые могут понадобиться позже, если на граничном узле по каким-либо причинам используется собственная файловая система, а не HDFS.

Основное преимущество файлов Azure заключается в том, что общие папки можно подключить и использовать на любом устройстве с поддерживаемой операционной системой, например Windows или Linux. Например, их можно использовать в другом кластере HDInsight, принадлежащем вам или коллеге, в виртуальной машине Azure, или даже в локальной системе.


## Дальнейшие действия

Теперь у вас есть вся базовая информация о том, как использовать R-консоль из сеанса SSH и как создать кластер HDInsight c R Server. Далее рекомендуем ознакомиться с другими способами работы с R Server в HDInsight, которые описаны в следующих статьях.

- [Общие сведения об R Server в HDInsight ](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (Начало работы с R Server в Hadoop)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight Premium (Добавление RStudio Server в HDInsight "Премиум")](hdinsight-hadoop-r-server-install-r-studio.md)
- [Варианты контекста вычислений для R Server в HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0921_2016-->