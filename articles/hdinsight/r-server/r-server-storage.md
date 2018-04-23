---
title: Решения службы хранилища Azure для R Server в HDInsight — Azure | Документы Майкрософт
description: Сведения о разных вариантах хранения, доступных для R Server в кластере HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 23e32a913fb73d2207f7cf37ce6230e428fbe95c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Решения службы хранилища Azure для R Server в Azure HDInsight

R Server в HDInsight поддерживает множество решений для хранения данных, кода и объектов с результатами анализа. К ним относятся следующие решения:

- [Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Хранилище озера данных Azure](https://azure.microsoft.com/services/data-lake-store/)
- [Хранилище файлов Azure](https://azure.microsoft.com/services/storage/files/)

Кроме того, из кластера HDInsight вы можете обращаться к нескольким учетным записям хранения или контейнерам Azure. Хранилище файлов Azure — это удобный способ хранения данных на граничном узле, который позволяет подключить общую папку службы хранилища Azure, например, к файловой системе Linux. Общие папки Azure можно подключить и использовать на любом устройстве с поддерживаемой операционной системой, например Windows или Linux. 

При создании кластера Hadoop в HDInsight нужно указать **учетную запись хранения Azure** или **Data Lake Store**. Определенный контейнер хранилища в этой учетной записи используется для хранения файловой системы создаваемого кластера, например распределенной файловой системы Hadoop (HDFS). Дополнительные сведения и рекомендации см. в следующих статьях:

- [Использование службы хранилища Azure с HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Использование Data Lake Store с кластерами Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Использование учетных записей хранения BLOB-объектов Azure с кластером R Server

Ниже описано, как получить доступ к данным и операциям кластера R Server с помощью дополнительной учетной записи, если при создании кластера R Server вы указали несколько учетных записей хранения. Предположим, что у вас есть две учетные записи хранения (**storage1** для контейнера по умолчанию **container1** и **storage2** для **container2**).

> [!WARNING]
> Для повышения производительности кластер HDInsight создается в том же центре обработки данных, где находится заданная вами основная учетная запись хранения. Использование учетной записи хранения, расположение которой отличается от расположения кластера HDInsight, не поддерживается.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Использование хранилища по умолчанию для R Server в HDInsight

1. С помощью клиента SSH подключитесь к граничному узлу кластера. Сведения об использовании протокола SSH с кластерами HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Скопируйте пример файла mysamplefile.csv в каталог /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

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
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Все ссылки на каталоги и файлы указывают на учетную запись хранения `wasb://container1@storage1.blob.core.windows.net`. Это **учетная запись хранения по умолчанию**, связанная с кластером HDInsight.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Использование дополнительного хранилища для R Server в HDInsight

Теперь мы рассмотрим ситуацию, в которой вам нужно обработать файл с именем mysamplefile1.csv, расположенный в каталоге /private контейнера **container2** в учетной записи хранения **storage2**.

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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Теперь все ссылки на каталоги и файлы указывают на учетную запись хранения `wasb://container2@storage2.blob.core.windows.net`. Это указанный вами **узел имени**.

В учетной записи хранения **storage2** следует настроить каталог /user/RevoShare/<SSH username> указанным ниже образом.


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Использование хранилища Azure Data Lake Store для кластера R Server 

Чтобы использовать Data Lake Store в кластере HDInsight, следует предоставить кластеру доступ к каждому из хранилищ Azure Data Lake Store, которые вы хотите использовать. Инструкции по созданию кластера HDInsight с учетной записью Azure Data Lake Store в качестве хранилища по умолчанию или дополнительного хранилища на портале Azure см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Затем вы можете использовать такое хранилище в скрипте R примерно так же, как дополнительную учетную запись хранения Azure, как описано в предыдущей процедуре.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Предоставление кластеру доступа к хранилищам озера данных Azure
Доступ к хранилищу озера данных Azure осуществляется с помощью субъекта-службы Azure Active Directory (AAD), связанного с кластером HDInsight.

1. При создании кластера HDInsight выберите на вкладке **Источник данных** вариант **Удостоверение кластера AAD**.

2. В диалоговом окне **Удостоверение кластера AAD** в разделе **Выбрать субъект-службу AD** выберите **Создать**.

Присвоив имя субъекту-службе и создав для него пароль, щелкните **Управление доступом ADLS**, чтобы связать субъект-службу с Data Lake Store.

Вы также можете настроить доступ кластера к одному или нескольким учетным записям Data Lake Store после создания кластера. На портале Azure откройте запись Data Lake Store и выберите пункты **Обозреватель данных > Доступ > Добавить**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Доступ к Data Lake Store из кластера R Server в HDInsight

Когда вы получите доступ к Data Lake Store, вы сможете применить это хранилище для кластера R Server в HDInsight так же, как дополнительную учетную запись хранения Azure. Единственное отличие состоит в том, что вместо префикса **wasb://** используется **adl://**.


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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Приведенные ниже команды позволяют настроить учетную запись Data Lake Store для каталога RevoShare и добавить CSV-файл из приведенного выше примера.


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Использование хранилища файлов Azure для R Server в HDInsight

Для граничных узлов есть еще один удобный вариант хранения — [служба файлов Azure]((https://azure.microsoft.com/services/storage/files/). Они позволяют подключить общую папку службы хранения Azure к файловой системе Linux. Этот вариант может быть удобен для хранения файлов данных, скриптов R и объектов результатов, которые могут понадобиться позже, особенно если на граничном узле есть смысл использовать собственную файловую систему, а не HDFS. 

Основное преимущество файлов Azure заключается в том, что общие папки можно подключить и использовать на любом устройстве с поддерживаемой операционной системой, например Windows или Linux. Например, их можно использовать в другом кластере HDInsight, принадлежащем вам или коллеге, в виртуальной машине Azure, или даже в локальной системе. Дополнительные сведения можно найти в разделе 

- [Использование хранилища файлов Azure в Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Использование хранилища файлов Azure в Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Дополнительная информация

* [Основные сведения об R Server и возможностях открытого кода R в HDInsight](r-server-overview.md).
* [Начало работы с кластером R Server в Azure HDInsight](r-server-get-started.md)
* [Варианты контекста вычислений для R Server в HDInsight](r-server-compute-contexts.md)

