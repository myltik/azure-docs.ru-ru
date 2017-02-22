---
title: "Отправка данных для заданий Hadoop в HDInsight | Документация Майкрософт"
description: "Сведения об отправке данных и получении доступа к ним для заданий Hadoop в HDInsight с помощью интерфейса командной строки Azure, обозревателя хранилищ Azure, Azure PowerShell, командной строки Hadoop или Sqoop."
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: f9b191a68fe19f30aa157fd01f33afb0a4f1e279
ms.openlocfilehash: 6e6f7793e03930cc002183172ccfbed6305378bd


---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Отправка данных для заданий Hadoop в HDInsight
Служба Azure HDInsight — это полнофункциональная распределенная файловая система Hadoop (HDFS), в основе которой лежит хранилище BLOB-объектов Azure. Разработанная как дополнение для HDFS, она обеспечивает клиентам высочайшее удобство работы. Все компоненты экосистемы Hadoop могут непосредственно работать с данными, которыми управляет служба. Хранилище BLOB-объектов Azure и HDFS — это разные файловые системы, оптимизированные для хранения и обработки данных. Сведения о преимуществах хранилища BLOB-объектов Azure см.в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

**Предварительные требования**

Перед началом работы необходимо иметь следующее:

* Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].

## <a name="why-blob-storage"></a>Зачем нужно хранилище BLOB-объектов?
Кластеры Azure HDInsight обычно развертываются для выполнения заданий MapReduce, а после завершения этих заданий удаляются. Хранение данных в кластерах HDFS после выполнения расчетов было бы дорогостоящим способом хранения информации. Хранилище BLOB-объектов Azure — это доступное, высокомасштабируемое, экономичное и общедоступное решение для хранения данных, которые должны обрабатываться с помощью HDInsight. Хранение информации в виде больших двоичных объектов дает возможность удалять кластеры HDInsight после завершения расчетов без угрозы потери данных.

### <a name="directories"></a>Каталоги
В контейнерах хранилища BLOB-объектов данные хранятся в виде пар «ключ — значение». Иерархия каталогов отсутствует. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. HDInsight видит эти каталоги так, как если бы они были реальными.

Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога "input" не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу.

Поэтому, если вы пользуетесь средствами Azure Explorer, вы можете заметить, что некоторые файлы имеют размер 0 байт. Эти файлы служат двум целям.

* Если есть пустые папки, эти файлы обозначают существование таких папок. Хранилище BLOB-объектов понимает, что если существует BLOB-объект с названием foo/bar, то существует и папка с именем **foo**. Но единственный способ обозначить пустую папку **foo** — это хранить в ней специальный файл размером 0 байт.
* Эти файлы содержат специальные метаданные, которые нужны файловой системе Hadoop, в частности сведения о разрешениях и имена владельцев папок.

## <a name="command-line-utilities"></a>Служебные программы командной строки
Корпорация Майкрософт предоставляет следующие служебные программы для работы с хранилищем больших двоичных объектов Azure:

| Средство | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Интерфейс командной строки Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] | | |✔ |
| [Команда Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Хотя интерфейс командной строки Azure (CLI), Azure PowerShell и AzCopy можно использовать за пределами Azure, команда Hadoop доступна только в кластере HDInsight и допускает загрузку данных только из локальной файловой системы в хранилище BLOB-объектов Azure.
>
>

### <a name="a-idxplatcliaazure-cli"></a><a id="xplatcli"></a>Интерфейс командной строки Azure
Интерфейс командной строки Azure (CLI) представляет собой кроссплатформенное средство, с помощью которого можно управлять службами Azure. Чтобы отправить данные в хранилище BLOB-объектов Azure, выполните следующие действия.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Установите и настройте интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli-install.md).
2. Откройте командную строку, например bash или другую оболочку, и выполните приведенную далее команду для аутентификации в подписке Azure.

        azure login

    При появлении запроса введите имя пользователя и пароль для своей подписки.
3. Используйте следующую команду для отображения учетных записей хранения, связанных с вашей подпиской:

        azure storage account list
4. Выберите учетную запись хранения, которая содержит нужный для работы BLOB-объект, а затем с помощью следующей команды получите ключ для этой учетной записи.

        azure storage account keys list <storage-account-name>

    Команда должна вернуть **первичный** и **вторичный** ключи. Скопируйте значение **первичного** ключа, так как оно понадобится позже.
5. С помощью следующей команды получите список контейнеров BLOB-объектов в учетной записи хранения.

        azure storage container list -a <storage-account-name> -k <primary-key>
6. С помощью следующих команд отправьте и скачайте файлы в BLOB-объект.

   * Для отправки файла:

           azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
   * Для скачивания файла:

           azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [!NOTE]
> Если вы всегда будете работать с одной и той же учетной записью хранения, не указывайте учетную запись и ключ для каждой команды. Вместо этого задайте следующие переменные среды.
>
> * **AZURE\_STORAGE\_ACCOUNT** — имя учетной записи хранения.
> * **AZURE\_STORAGE\_ACCESS\_KEY** — ключ учетной записи хранения.
>
>

### <a name="a-idpowershellaazure-powershell"></a><a id="powershell"></a>Azure PowerShell
Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. Сведения о настройке Azure PowerShell на рабочей станции см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Отправка локального файла в хранилище BLOB-объектов Azure**

1. Откройте консоль Azure PowerShell, как описано в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
2. Задайте значения первых пяти переменных в следующем сценарии:

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
3. Вставьте сценарий в консоль Azure PowerShell, чтобы выполнить его для копирования файла.

Примеры сценариев PowerShell, созданных для работы с HDInsight, см. в разделе о [средствах HDInsight](https://github.com/blackmist/hdinsight-tools).

### <a name="a-idazcopyaazcopy"></a><a id="azcopy"></a>AzCopy
AzCopy — это программа командной строки, которая упрощает перенос данных в учетную запись хранения Azure и из нее. Ее можно использовать отдельно или добавить в существующее приложение. [Скачайте AzCopy][azure-azcopy-download].

Синтаксис AzCopy:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Дополнительные сведения см. в статье [Приступая к работе со служебной программой командной строки AzCopy][azure-azcopy].

### <a name="a-idcommandlineahadoop-command-line"></a><a id="commandline"></a>Командная строка Hadoop
Командная строка Hadoop полезна только для хранения данных в хранилище BLOB-объектов, когда данные уже присутствуют на головном узле кластера.

Чтобы использовать команду Hadoop, необходимо сначала подключиться к головному узлу с помощью одного из следующих методов:

* **HDInsight под управлением Windows**: [подключение с помощью удаленного рабочего стола](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight под управлением Linux:** подключение с использованием SSH ([команды SSH](hdinsight-hadoop-linux-use-ssh-unix.md) или [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md)).

После подключения можно использовать следующий синтаксис для отправки файла в хранилище.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Например, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Поскольку файловая система по умолчанию для HDInsight находится в хранилище BLOB-объектов Azure, файл /example/data.txt фактически располагается там же. Можно также использовать следующую ссылку на файл:

    wasbs:///example/data/data.txt

или

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Список других команд Hadoop, которые работают с файлами, см. на странице [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html).

> [!WARNING]
> В кластерах HBase при записи данных используется размер блока по умолчанию — 256 КБ. Это подходит, когда используются API HBase или REST API. Но при попытке записать более 12 ГБ данных с помощью команды `hadoop` или `hdfs dfs` возникнет ошибка. Подробные сведения см. в разделе [Исключение хранилища для записи большого двоичного объекта](#storageexception) ниже.
>
>

## <a name="graphical-clients"></a>Графические клиенты
Существуют также несколько приложений, которые предоставляют графический интерфейс для работы с хранилищем Azure. Ниже приведен список некоторых из таких приложений:

| Клиент | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools для HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer;](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2;](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer;](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer;](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

### <a name="visual-studio-tools-for-hdinsight"></a>Visual Studio Tools для HDInsight
Подробные сведения см. в разделе [Переход на связанные ресурсы](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

### <a name="a-idstorageexploreraazure-storage-explorer"></a><a id="storageexplorer"></a>Azure Storage Explorer;
*Обозреватель хранилищ Azure* — это полезное средство для проверки и изменения данных в больших двоичных объектах. Это бесплатный инструмент с открытым кодом. Его можно скачать на сайте [http://storageexplorer.com/](http://storageexplorer.com/). Исходный код доступен также по ссылке.

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в статье "Об учетных записях хранения Azure" в разделах о [создании и удалении учетной записи хранения, а также об управлении ею][azure-create-storage-account].

1. Запустите обозреватель хранилищ Azure. Если вы запускаете Storage Explorer в первый раз, вам будет предложено ввести **_имя учетной записи хранения** и **ключ учетной записи хранения**. Если Storage Explorer запускался раньше, нажмите кнопку **Добавить**, чтобы добавить новые имя и ключ учетной записи хранения.

    Введите имя и ключ учетной записи хранения, которые использует кластер HDinsight, а затем нажмите кнопку **Save & open** (Сохранить и открыть).

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. В списке контейнеров в левой части интерфейса, щелкните имя контейнера, который связан с вашим кластером HDInsight. По умолчанию это имя кластера HDInsight, однако оно может отличаться, если вы ввели конкретное имя при создании кластера.
3. На панели инструментов выберите значок отправки.

    ![Панель инструментов с выделенным значком отправки](./media/hdinsight-upload-data/toolbar.png)
4. Укажите файл для отправки и щелкните **Открыть**. При появлении запроса выберите **Отправить** и отправьте файл в корневой каталог контейнера хранилища. Если нужно передать файл по конкретному пути, введите путь в поле **Место назначения**, а затем щелкните **Отправить**.

    ![Диалоговое окно отправки файла](./media/hdinsight-upload-data/fileupload.png)

    После завершения передачи файла вы можете использовать его из заданий в кластере HDInsight.

## <a name="mount-azure-blob-storage-as-local-drive"></a>Подключение хранилища больших двоичных объектов как локального диска
Ознакомьтесь с разделом [Подключение хранилища больших двоичных объектов как локального диска](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="services"></a>Службы
### <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure является полностью управляемой системой для создания служб хранения, обработки и перемещения данных и превращения этих служб в упрощенные, масштабируемые и надежные конвейеры производства данных.

С помощью фабрики данных Azure можно перемещать данные в хранилище BLOB-объектов Azure или создавать конвейеры данных, которые непосредственно используют функции HDInsight, такие как Hive и Pig.

Подробные сведения см. в [документации по фабрике данных Azure](https://azure.microsoft.com/documentation/services/data-factory/).

### <a name="a-idsqoopaapache-sqoop"></a><a id="sqoop"></a>Apache Sqoop
Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД.

Дополнительные сведения см. в разделе [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop].

## <a name="development-sdks"></a>Пакеты SDK для разработки
Доступ к хранилищу BLOB-объектов Azure также можно получить с помощью пакета Azure SDK из следующих языков программирования:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Подробные сведения об установке пакетов SDK для Azure см. на странице [Загрузки](https://azure.microsoft.com/downloads/).

## <a name="troubleshooting"></a>Устранение неполадок
### <a name="a-idstorageexceptionastorage-exception-for-write-on-blob"></a><a id="storageexception"></a>Исключение хранилища для записи в большой двоичный объект
**Проблемы.** Когда с помощью команд `hadoop` или `hdfs dfs` в кластер HBase записываются файлы размером более 12 ГБ, может возникнуть такая ошибка:

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

**Причина.** В HBase в кластерах HDInsight размер блока по умолчанию при записи в службу хранилища Azure равен 256 КБ. Несмотря на то, что это подходит при использовании API HBase или REST API, применение служебных программ командной строки `hadoop` или `hdfs dfs` вызовет ошибку.

**Решение.** Задайте больший размер блока с помощью свойства `fs.azure.write.request.size`. Его можно указывать для каждого отдельного случая, используя параметр `-D`. Ниже приведен пример использования параметра с командой `hadoop`.

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Можно также глобально увеличить значение `fs.azure.write.request.size` с помощью Ambari. Чтобы изменить значение в веб-интерфейсе Ambari, сделайте следующее:

1. В браузере перейдите к веб-интерфейсу Ambari для кластера по адресу https://CLUSTERNAME.azurehdinsight.net, где **CLUSTERNAME** — это имя кластера.

    При появлении запроса введите имя и пароль администратора для кластера.
2. В левой части экрана выберите **HDFS**, а затем перейдите на вкладку **Configs** (Конфигурации).
3. В поле **Фильтр...** введите `fs.azure.write.request.size`. В середине страницы отобразится поле и его текущее значение.
4. Измените значение с 262144 (256 КБ) на новое. Например, 4194304 (4 МБ).

![Изображение смены значения через веб-интерфейс Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Подробные сведения об использовании Ambari см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете, как передавать данные в HDInsight, узнайте, как их можно анализировать.

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png



<!--HONumber=Dec16_HO2-->


