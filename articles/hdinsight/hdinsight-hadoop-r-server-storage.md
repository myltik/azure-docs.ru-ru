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
   ms.date="06/01/2016"
   ms.author="jeffstok"
/>

# Параметры службы хранилища Azure для R Server в HDInsight (предварительная версия)

R Server в кластере HDInsight (предварительная версия) имеет доступ к хранилищу BLOB-объектов Azure и [хранилищу озера данных Azure](https://azure.microsoft.com/services/data-lake-store/), используя их для сохранения данных, кода, результирующих объектов после анализа и т. д.

При создании кластера Hadoop в HDInsight нужно указать учетную запись хранения Azure. Конкретный контейнер хранилища BLOB-объектов из этой учетной записи назначается для хранения файловой системы создаваемого кластера, например распределенной файловой системы Hadoop (HDFS). Для повышения производительности кластер HDInsight создается в том же центре обработки данных, где находится заданная вами основная учетная запись хранения. Дополнительную информацию см. в статье [Использование хранилища больших двоичных объектов Azure с HDInsight](hdinsight-hadoop-use-blob-storage.md "Использование хранилища BLOB-объектов Azure с HDInsight").


## Использование нескольких учетных записей хранения BLOB-объектов Azure

При необходимости с помощью кластера можно получить доступ к нескольким учетным записям хранения Azure или контейнерам. Для этого необходимо указать дополнительные учетные записи хранения при создании кластера и выполнить приведенные ниже действия, чтобы использовать их в R.

1.	Предположим, вы создаете кластер HDInsight с учетной записью хранения "storage1" и контейнером по умолчанию "container1". Вы также указываете дополнительную учетную запись хранения "storage2".  
2.	Теперь вы скопировали файл "mycsv.csv" в каталог "/share" и хотите проанализировать его.  

````
hadoop fs –mkdir /share
hadoop fs –copyFromLocal myscsv.scv /share  
````

3.	В коде R вы задаете имя узла "default" и выбираете каталог и файл для обработки.  

````
myNameNode <- "default"
myPort <- 0
````

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

Обратите внимание, что в учетной записи хранения storage2 потребуется настроить каталог /user/RevoShare/<SSH username>.

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## Использование хранилища озера данных Azure

Чтобы использовать хранилища озера данных Azure с учетной записью HDInsight, понадобится предоставить своему кластеру доступ к каждому хранилищу озера данных Azure, которое вы хотите использовать, а затем указать их в сценарии R так, как вы делали ранее, чтобы использовать дополнительную учетную запись хранения Azure.

## Добавление доступа кластера к хранилищам озера данных Azure

Доступ к хранилищу озера данных Azure устанавливается с помощью субъекта-службы Azure Active Directory (AAD), связанного с кластером HDInsight. Чтобы добавить субъект-службу при создании кластера HDInsight, щелкните параметр "Удостоверение кластера AAD" на вкладке "Источник данных", а затем щелкните "Создать" для субъекта-службы. После того, как вы укажете его имя и пароль, откроется новая вкладка. На ней можно будет связать субъект-службу с хранилищами озера данных Azure.

Обратите внимание, что доступ к хранилищу озера данных Azure можно добавить и позже, открыв хранилище озера данных Azure на портале Azure и выбрав "Обозреватель данных > Доступ". Ниже приведен пример диалогового окна, показывающего создание субъекта-службы и его связывание с хранилищем озера данных Azure rkadl11.

![Создание субъекта-службы хранилища озера данных Azure 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Создание субъекта-службы хранилища озера данных Azure 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## Использование хранилища озера данных Azure с R Server
Предоставив доступ к хранилищу озера данных Azure с помощью субъекта-службы кластера, его можно использовать в Server R в HDInsight практически так же, как дополнительную учетную запись хранения Azure. Единственное отличие состоит в том, что вместо префикса wasb:/ / используется adl:/ /.

````
# point to the ADL store (e.g. ADLtest) 
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account) 
bigDataDirRoot <- "/share"  

# define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE)

# set compute context
rxSetComputeContext(mySparkCluster)

# define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# create Factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# define the data source 
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

> [AZURE.NOTE] Ниже представлены команды, используемые для настройки каталога RevoShare для учетной записи хранилища озера данных Azure и добавления примера CSV-файла для приведенного выше примера.

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare 
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## Использование файлов Azure на граничном узле 

Существует также удобный способ хранения данных для граничного узла, называемый [файлами Azure](../storage/storage-how-to-use-files-linux.md "Файлы Azure"). Он позволяет подключить файловый ресурс службы хранилища Azure к файловой системе Linux. Это подходит для хранения файлов данных, R-сценариев и объектов результатов, которые могут понадобиться позже, когда имеет смысл использовать на граничном узле собственную файловую систему вместо HDFS. Значительным преимуществом службы файлов Azure является то, что общие папки можно подключить и использовать в любой системе с поддерживаемой ОС (Win, Linux), например в другом кластере HDInsight, виртуальной машине Azure и даже локальной системе.


## Дальнейшие действия

Теперь, когда вы знаете, как создать кластер HDInsight, который включает R Server, а также основные принципы применения консоли R в сеансе SSH, ознакомьтесь с другими способами работы с R Server в HDInsight, описанными в следующих статьях.

- [Overview of R Server on Hadoop (Общие сведения об R Server в Hadoop)](hdinsight-hadoop-r-server-overview.md)
- [Get started with R server on Hadoop (Начало работы с R Server в Hadoop)](hdinsight-hadoop-r-server-get-started.md)
- [Add RStudio Server to HDInsight Premium (Добавление RStudio Server в HDInsight "Премиум")](hdinsight-hadoop-r-server-install-r-studio.md)
- [Compute context options for R Server on HDInsight Premium (Параметры контекста вычислений для R Server в HDInsight "Премиум")](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0608_2016-->