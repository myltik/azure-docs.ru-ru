---
title: Отправка данных для заданий Hadoop в HDInsight | Документация Майкрософт
description: Сведения об отправке данных и получении доступа к ним для заданий Hadoop в HDInsight с помощью интерфейса командной строки Azure, обозревателя хранилищ Azure, Azure PowerShell, командной строки Hadoop или Sqoop.
keywords: etl hadoop, вставка данных в hadoop, загрузка данных hadoop
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 1734e9f0002ab7f33a8a67e44811352cb5c45fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202464"
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Отправка данных для заданий Hadoop в HDInsight

Служба Azure HDInsight — это полнофункциональная распределенная файловая система Hadoop (HDFS), в основе которой лежит служба хранилища Azure и Azure Data Lake Store. Служба хранилища Azure и Azure Data Lake Store разработаны в качестве дополнения для HDFS. Они обеспечивают клиентам высочайшее удобство работы. Благодаря им все компоненты экосистемы Hadoop работают непосредственно с данными, управляемыми службой. Служба хранилища Azure и Azure Data Lake — это разные файловые системы, оптимизированные для хранения и обработки данных. Сведения о преимуществах использования службы хранилища Azure см. в статьях [Использование службы хранилища Azure с кластерами Azure HDInsight][hdinsight-storage] и [Использование Data Lake Store с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы необходимо ознакомиться со следующими требованиями:

* Кластер Azure HDInsight. Инструкции см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight][hdinsight-get-started] или [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](hdinsight-hadoop-provision-linux-clusters.md).
* Знание двух следующих статей:

    - [Использование службы хранилища Azure с HDInsight][hdinsight-storage]
    - [Использование Data Lake Store с кластерами Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Отправка данных в службу хранилища Azure

### <a name="command-line-utilities"></a>Служебные программы командной строки
Корпорация Майкрософт предоставляет следующие служебные программы для работы со службой хранилища Azure:

| Средство | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Интерфейс командной строки Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Команда Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Хотя интерфейс командной строки Azure (CLI), Azure PowerShell и AzCopy можно использовать за пределами Azure, команда Hadoop доступна только в кластере HDInsight. И она допускает загрузку данных только из локальной файловой системы в службу хранилища Azure.
>
>

#### <a id="xplatcli"></a>Интерфейс командной строки Azure
Интерфейс командной строки Azure (CLI) представляет собой кроссплатформенное средство, с помощью которого можно управлять службами Azure. Чтобы отправить данные в службу хранилища Azure, сделайте следующее:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Установите и настройте интерфейс командной строки Azure для Mac, Linux и Windows](../cli-install-nodejs.md).
2. Откройте командную строку, например bash или другую оболочку, и выполните приведенную далее команду для аутентификации в подписке Azure.

    ```cli
    azure login
    ```

    При появлении запроса введите имя пользователя и пароль для своей подписки.
3. Используйте следующую команду для отображения учетных записей хранения, связанных с вашей подпиской:

    ```cli
    azure storage account list
    ```

4. Выберите учетную запись хранения, которая содержит нужный для работы BLOB-объект, а затем с помощью следующей команды получите ключ для этой учетной записи.

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Эта команда возвращает **первичный** и **вторичный** ключи. Скопируйте значение **первичного** ключа, так как оно понадобится позже.
5. С помощью следующей команды получите список контейнеров BLOB-объектов в учетной записи хранения.

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. С помощью следующих команд отправьте и скачайте файлы в BLOB-объект.

   * Для отправки файла:

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Для скачивания файла:

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> При постоянной работе с одной и той же учетной записью хранения не указывайте учетную запись и ключ для каждой команды. Вместо этого задайте следующие переменные среды:
>
> * **AZURE\_STORAGE\_ACCOUNT** — имя учетной записи хранения.
> * **AZURE\_STORAGE\_ACCESS\_KEY** — ключ учетной записи хранения.
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. Сведения о настройке Azure PowerShell на рабочей станции см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Отправка локального файла в службу хранилища Azure**

1. Откройте консоль Azure PowerShell, как описано в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).
2. Задайте значения первых пяти переменных в следующем сценарии:

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Вставьте сценарий в консоль Azure PowerShell, чтобы выполнить его для копирования файла.

Примеры сценариев PowerShell, созданных для работы с HDInsight, см. в разделе о [средствах HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy — это программа командной строки, которая упрощает перенос данных в учетную запись хранения Azure и из нее. Ее можно использовать отдельно или добавить в существующее приложение. [Скачайте AzCopy][azure-azcopy-download].

Синтаксис AzCopy:

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Дополнительные сведения см. в статье [Приступая к работе со служебной программой командной строки AzCopy][azure-azcopy].

Доступна предварительная версия AzCopy для Linux.  Ознакомьтесь со статьей [Announcing AzCopy on Linux Preview](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/) (Объявление о выпуске предварительной версии AzCopy для Linux).

#### <a id="commandline"></a>Командная строка Hadoop
Командная строка Hadoop полезна только для хранения данных в большом двоичном объекте службы хранилища Azure, когда данные уже присутствуют на головном узле кластера.

Чтобы использовать команду Hadoop, необходимо сначала подключиться к головному узлу с помощью одного из следующих методов:

* **HDInsight под управлением Windows**: [подключение с помощью удаленного рабочего стола](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight под управлением Linux:** подключение с использованием [SSH или PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md).

После подключения можно использовать следующий синтаксис для отправки файла в хранилище.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Например, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Так как файловая система по умолчанию для HDInsight находится в службе хранилища Azure, файл /example/data.txt фактически располагается там же. Можно также использовать следующую ссылку на файл:

    wasb:///example/data/data.txt

или

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Список других команд Hadoop, которые работают с файлами, см. здесь: [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> В кластерах HBase при записи данных используется размер блока по умолчанию (256 КБ). Это подходит, когда используются API HBase или REST API. Но при попытке записать более 12 ГБ данных с помощью команды `hadoop` или `hdfs dfs` возникнет ошибка. Подробные сведения см. в разделе [Исключение хранилища для записи большого двоичного объекта](#storageexception) этой статьи.
>
>

### <a name="graphical-clients"></a>Графические клиенты
Существуют также несколько приложений, которые предоставляют графический интерфейс для работы с хранилищем Azure. В следующей таблице приведен список некоторых из таких приложений:

| Клиент | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools для HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer;](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2;](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer;](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer;](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools для HDInsight
Подробные сведения см. в разделе [Переход на связанные ресурсы](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Azure Storage Explorer;
*Обозреватель хранилищ Azure* — это полезное средство для проверки и изменения данных в больших двоичных объектах. Это бесплатный инструмент с открытым кодом. Его можно скачать на сайте[http://storageexplorer.com/](http://storageexplorer.com/). Исходный код доступен также по ссылке.

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в статье "Об учетных записях хранения Azure" в разделах о [создании и удалении учетной записи хранения, а также об управлении ею][azure-create-storage-account].

1. Запустите обозреватель хранилищ Azure. Если вы впервые запускаете Storage Explorer, вам будет предложено ввести **_имя учетной записи хранения** и **ключ учетной записи хранения**. Если Storage Explorer запускался раньше, нажмите кнопку **Добавить**, чтобы добавить новые имя и ключ учетной записи хранения.

    Введите имя и ключ учетной записи хранения, которые использует кластер HDInsight, а затем нажмите кнопку **Save & open** (Сохранить и открыть).

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. В списке контейнеров в левой части интерфейса, щелкните имя контейнера, который связан с вашим кластером HDInsight. По умолчанию это имя кластера HDInsight, однако оно может отличаться, если вы ввели конкретное имя при создании кластера.
3. На панели инструментов выберите значок отправки.

    ![Панель инструментов с выделенным значком отправки](./media/hdinsight-upload-data/toolbar.png)
4. Укажите файл для отправки и щелкните **Открыть**. При появлении запроса выберите **Отправить** и отправьте файл в корневой каталог контейнера хранилища. Если нужно передать файл по конкретному пути, введите путь в поле **Место назначения**, а затем щелкните **Отправить**.

    ![Диалоговое окно отправки файла](./media/hdinsight-upload-data/fileupload.png)

    После завершения передачи файла вы можете использовать его из заданий в кластере HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Подключение службы хранилища Azure в качестве локального диска
Подробные сведения см. в статье [Mount Azure Blob Storage as Local Drive](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx) (Подключение хранилища BLOB-объектов Azure в качестве локального диска).

### <a name="upload-using-services"></a>Отправка с помощью служб
#### <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure является полностью управляемой системой для создания служб хранения, обработки и перемещения данных и превращения этих служб в упрощенные, масштабируемые и надежные конвейеры производства данных.

С помощью фабрики данных Azure можно перемещать данные в службу хранилища Azure или создавать конвейеры данных, непосредственно использующие функции HDInsight, такие как Hive и Pig.

Подробные сведения см. в [документации по фабрике данных Azure](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД.

Дополнительные сведения см. в разделе [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Пакеты SDK для разработки
Доступ к службе хранилища Azure также можно получить с помощью пакета Azure SDK со следующих языков программирования:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Подробные сведения об установке пакетов SDK для Azure см. на странице [Загрузки](https://azure.microsoft.com/downloads/).

### <a name="troubleshooting"></a>Устранение неполадок
#### <a id="storageexception"></a>Исключение хранилища для записи в большой двоичный объект
**Проблемы.** Если при использовании команд `hadoop` или `hdfs dfs` в кластер HBase записываются файлы размером более 12 ГБ, может возникнуть такая ошибка:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Причина.** В HBase в кластерах HDInsight размер блока по умолчанию при записи в службу хранилища Azure равен 256 КБ. Несмотря на то что это подходит при использовании API HBase или REST API, применение служебных программ командной строки `hadoop` или `hdfs dfs` вызовет ошибку.

**Решение.** Задайте больший размер блока с помощью свойства `fs.azure.write.request.size`. Его можно указывать для каждого отдельного случая, используя параметр `-D`. Далее приведен пример использования параметра с командой `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Можно также глобально увеличить значение `fs.azure.write.request.size` с помощью Ambari. Чтобы изменить значение в веб-интерфейсе Ambari, сделайте следующее:

1. В браузере перейдите к веб-интерфейсу Ambari для кластера Это https://CLUSTERNAME.azurehdinsight.net, где **ИМЯ_КЛАСТЕРА** — имя кластера.

    При появлении запроса введите имя и пароль администратора для кластера.
2. В левой части экрана выберите **HDFS**, а затем перейдите на вкладку **Configs** (Конфигурации).
3. В поле **Фильтр...** введите `fs.azure.write.request.size`. Посредине страницы отобразится поле и его текущее значение.
4. Измените значение с 262144 (256 КБ) на новое. Например, 4194304 (4 МБ).

![Изображение смены значения через веб-интерфейс Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Подробные сведения об использовании Ambari см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы знаете, как передавать данные в HDInsight, узнайте, как их можно анализировать.

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
