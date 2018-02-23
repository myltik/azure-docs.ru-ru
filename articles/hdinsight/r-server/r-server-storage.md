---
title: "Решения службы хранилища Azure для R Server в HDInsight — Azure | Документы Майкрософт"
description: "Сведения о разных вариантах хранения, доступных для пользователей R Server в кластере HDInsight."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 863277294fc0462e9221edffab1dd4e2001d7493
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Решения службы хранилища Azure для R Server в HDInsight

Microsoft R Server в HDInsight включает в себя множество решений для хранения данных, кода и объектов, содержащих результаты анализа. К ним относятся следующие решения:

- [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Хранилище озера данных Azure](https://azure.microsoft.com/services/data-lake-store/)
- [Хранилище файлов Azure](https://azure.microsoft.com/services/storage/files/)

Кроме того, имеется возможность доступа из кластера HDI к нескольким учетным записям хранения Azure или контейнерам. Хранилище файлов Azure — это удобный способ хранения данных на граничном узле, который позволяет подключить общую папку службы хранилища Azure, например, к файловой системе Linux. Однако общие папки Azure можно подключить и использовать на любом устройстве с поддерживаемой операционной системой, например Windows или Linux. 

При создании кластера Hadoop в HDInsight нужно указать **учетную запись хранения Azure** или **Data Lake Store**. Определенный контейнер хранилища в этой учетной записи используется для хранения файловой системы создаваемого кластера, например распределенной файловой системы Hadoop (HDFS). Дополнительные сведения и рекомендации см. в следующих статьях:

- [Использование службы хранилища Azure с HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Использование Data Lake Store с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) 

Дополнительные сведения о решениях службы хранилища Azure см. в статье [Общие сведения о службе хранилища Microsoft Azure](../../storage/common/storage-introduction.md). 

Рекомендации по выбору наиболее подходящего варианта хранилища для вашего сценария см. в статье [Выбор между большими двоичными объектами, службой файлов и дисками данных Azure](../../storage/common/storage-decide-blobs-files-disks.md). 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Использование учетных записей хранения BLOB-объектов Azure с R Server

Ниже описано, как получить доступ к данным и операциям сервера R Server с помощью дополнительной учетной записи, если при создании кластера R Server вы указали несколько учетных записей хранения. Предположим, что у вас есть две учетные записи хранения (**storage1** и **storage2**) и контейнер по умолчанию (**container1**).

> [!WARNING]
> Для повышения производительности кластер HDInsight создается в том же центре обработки данных, где находится заданная вами основная учетная запись хранения. Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

1. С помощью клиента SSH подключитесь к граничному узлу кластера как удаленный пользователь (remoteuser).  

  + На портале Azure перейдите на страницу службы кластеров HDI, выберите "Обзор", а затем щелкните **Безопасная оболочка (SSH)**.
  + В списке "Имя узла" выберите граничный узел (с *ed-ssh.azurehdinsight.net* в имени).
  + Скопируйте имя узла.
  + Откройте клиент SSH, например PutTY или SmartTY, и введите имя узла.
  + Введите remoteuser в качестве имени пользователя и пароль кластера.
  
2. Скопируйте файл mycsv.csv в каталог /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

3. Подключитесь к R Studio или другой консоли R, введите код R, чтобы задать узлу имени **значение по умолчанию** и расположение файла, к которому нужно получить доступ.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Все ссылки на каталоги и файлы указывают на учетную запись хранения wasb://container1@storage1.blob.core.windows.net. Это **учетная запись хранения по умолчанию**, связанная с кластером HDInsight.

Теперь предположим, что вы хотите обработать файл с именем mySpecial.csv, расположенный в каталоге /private контейнера **container2** в учетной записи хранения **storage2**.

В коде R измените ссылку на узел имени так, чтобы она указывала на учетную запись хранения **storage2** .


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Теперь все ссылки на каталоги и файлы указывают на учетную запись хранения wasb://container2@storage2.blob.core.windows.net. Это указанный вами **узел имени**.

В учетной записи хранения **storage2** следует настроить каталог /user/RevoShare/<SSH username> указанным ниже образом.


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Использование хранилища Azure Data Lake Store с R Server

Чтобы использовать в учетной записи HDInsight хранилища озера данных, следует предоставить кластеру доступ к каждому из хранилищ озера данных Azure, которые вы хотите использовать. Инструкции по созданию кластера HDInsight с учетной записью Azure Data Lake Store в качестве хранилища по умолчанию или дополнительного хранилища на портале Azure см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Затем вы можете использовать такое хранилище в скрипте R примерно так же, как дополнительную учетную запись хранения Azure, как описано в предыдущей процедуре.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Предоставление кластеру доступа к хранилищам озера данных Azure
Доступ к хранилищу озера данных Azure осуществляется с помощью субъекта-службы Azure Active Directory (AAD), связанного с кластером HDInsight.

Чтобы добавить субъект-службу Azure AD, выполните указанные ниже действия.

1. При создании кластера HDInsight выберите на вкладке **Источник данных** вариант **Удостоверение кластера AAD**.

2. В диалоговом окне **Удостоверение кластера AAD** в разделе **Выбрать субъект-службу AD** выберите **Создать**.

Присвоив имя субъекту-службе и создав для него пароль, щелкните **Управление доступом ADLS**, чтобы связать субъект-службу с хранилищами Data Lake Store.

Можно также настроить доступ кластера к одному или нескольким хранилищам Data Lake Store после создания кластера. Откройте запись портала Azure для Data Lake Store и выберите **Обозреватель данных > Доступ > Добавить**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Доступ к Data Lake Store с сервера R Server

Когда вы предоставите доступ к хранилищу озера данных, это хранилище можно будет использовать в R Server на HDInsight так же, как дополнительную учетную запись хранения Azure. Единственное отличие состоит в том, что вместо префикса **wasb://** используется **adl://**.


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


Приведенные ниже команды позволяют настроить учетную запись хранилища Data Lake Store c каталогом RevoShare и добавить образец CSV-файла из приведенного выше примера.


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Использование хранилища файлов Azure с R Server

Для граничных узлов существует еще один удобный вариант хранения — [файлы Azure]((https://azure.microsoft.com/services/storage/files/). Они позволяют подключить общую папку службы хранения Azure к файловой системе Linux. Этот вариант может быть удобен для хранения файлов данных, скриптов R и объектов результатов, которые могут понадобиться позже, особенно если на граничном узле есть смысл использовать собственную файловую систему, а не HDFS. 

Основное преимущество файлов Azure заключается в том, что общие папки можно подключить и использовать на любом устройстве с поддерживаемой операционной системой, например Windows или Linux. Например, их можно использовать в другом кластере HDInsight, принадлежащем вам или коллеге, в виртуальной машине Azure, или даже в локальной системе. Дополнительные сведения можно найти в разделе 

- [Использование хранилища файлов Azure в Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Использование хранилища файлов Azure в Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы ознакомились с возможностями службы хранилища Azure, используйте приведенные ниже ссылки, чтобы изучить другие способы выполнения задач обработки и анализа данных с помощью R Server в HDInsight.

* [Общие сведения об R Server в HDInsight](r-server-overview.md)
* [Get started with R server on Hadoop (Начало работы с R Server в Hadoop)](r-server-get-started.md)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)

