<properties
	pageTitle="Отправка данных для заданий Hadoop в HDInsight | Microsoft Azure"
	description="Сведения об отправке данных и получении доступа к ним для заданий Hadoop в HDInsight с помощью интерфейса командной строки Azure, обозревателя хранилищ Azure, Azure PowerShell, командной строки Hadoop или Sqoop."
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>



#Отправка данных для заданий Hadoop в HDInsight

Служба Azure HDInsight — это полнофункциональная распределенная файловая система Hadoop (HDFS), в основе которой лежит хранилище BLOB-объектов Azure. Разработанная как дополнение для HDFS, она обеспечивает клиентам высочайшее удобство работы. Все компоненты экосистемы Hadoop могут непосредственно работать с данными, которыми управляет служба. Хранилище BLOB-объектов Azure и HDFS — это разные файловые системы, оптимизированные для хранения и обработки данных. Сведения о преимуществах хранилища BLOB-объектов Azure см.в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

**Предварительные требования**

Перед началом работы необходимо иметь следующее:

* Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].

##Зачем нужно хранилище BLOB-объектов?

Кластеры Azure HDInsight обычно развертываются для выполнения заданий MapReduce, а после завершения этих заданий удаляются. Хранение данных в кластерах HDFS после выполнения расчетов было бы дорогостоящим способом хранения информации. Хранилище BLOB-объектов Azure — это доступное, высокомасштабируемое, экономичное и общедоступное решение для хранения данных, которые должны обрабатываться с помощью HDInsight. Хранение информации в виде больших двоичных объектов дает возможность удалять кластеры HDInsight после завершения расчетов без угрозы потери данных.

###Каталоги

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар «ключ — значение». Иерархия каталогов отсутствует. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. HDInsight видит эти каталоги так, как если бы они были реальными.

Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога "input" не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу.

Поэтому, если вы пользуетесь средствами Azure Explorer, вы можете заметить, что некоторые файлы имеют размер 0 байт. Эти файлы служат двум целям.

- Если есть пустые папки, эти файлы обозначают существование таких папок. Хранилище BLOB-объектов понимает, что если существует BLOB-объект с названием foo/bar, то существует и папка с именем **foo**. Но единственный способ обозначить пустую папку **foo** — это хранить в ней специальный файл размером 0 байт.

- Эти файлы содержат специальные метаданные, которые нужны файловой системе Hadoop, в частности сведения о разрешениях и имена владельцев папок.

##Служебные программы командной строки

Корпорация Майкрософт предоставляет следующие служебные программы для работы с хранилищем больших двоичных объектов Azure:

| Средство | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Интерфейс командной строки Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Команда Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Хотя интерфейс командной строки Azure (CLI), Azure PowerShell и AzCopy можно использовать за пределами Azure, команда Hadoop доступна только в кластере HDInsight и допускает загрузку данных только из локальной файловой системы в хранилище BLOB-объектов Azure.

###<a id="xplatcli"></a>Интерфейс командной строки Azure

Интерфейс командной строки Azure (CLI) представляет собой кроссплатформенное средство, с помощью которого можно управлять службами Azure. Чтобы отправить данные в хранилище BLOB-объектов Azure, выполните следующие действия.

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

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

> [AZURE.NOTE] Если вы всегда будете работать с одной и той же учетной записью хранения, не указывайте учетную запись и ключ для каждой команды. Вместо этого задайте следующие переменные среды.
>
> * **AZURE\_STORAGE\_ACCOUNT** — имя учетной записи хранения.
>
> * **AZURE\_STORAGE\_ACCESS\_KEY** — ключ учетной записи хранения.

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. Сведения о настройке Azure PowerShell на рабочей станции см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Отправка локального файла в хранилище BLOB-объектов Azure**

1. Откройте консоль Azure PowerShell, как описано в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
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

Примеры сценариев PowerShell, созданных для работы с HDInsight, см. в разделе [Средства HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy — это программа командной строки, которая упрощает перенос данных в учетную запись хранения Azure и из нее. Ее можно использовать отдельно или добавить в существующее приложение. [Загрузка AzCopy][azure-azcopy-download].

Синтаксис AzCopy:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Дополнительные сведения см. в статье [AzCopy — отправка и скачивание файлов для BLOB-объектов Azure][azure-azcopy].


###<a id="commandline"></a>Командная строка Hadoop

Командная строка Hadoop полезна только для хранения данных в хранилище BLOB-объектов, когда данные уже присутствуют на головном узле кластера.

Чтобы использовать команду Hadoop, необходимо сначала подключиться к головному узлу с помощью одного из следующих методов:

* **HDInsight под управлением Windows**: [подключение с помощью удаленного рабочего стола](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight под управлением Linux**: подключение с использованием SSH ([команды SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) или [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

После подключения можно использовать следующий синтаксис для отправки файла в хранилище.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Например, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Поскольку файловая система по умолчанию для HDInsight находится в хранилище BLOB-объектов Azure, файл /example/data.txt фактически располагается там же. Можно также использовать следующую ссылку на файл:

	wasbs:///example/data/data.txt

или

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Список других команд Hadoop, которые работают с файлами, см. на странице [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##Графические клиенты

Существуют также несколько приложений, которые предоставляют графический интерфейс для работы с хранилищем Azure. Ниже приведен список некоторых из таких приложений:

| Клиент | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Microsoft Visual Studio Tools для HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Azure Storage Explorer;](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2;](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer;](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer;](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###Visual Studio Tools для HDInsight

Дополнительные сведения см. в разделе [Переход на связанные ресурсы](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

###<a id="storageexplorer"></a>Обозреватель хранилищ Azure

*Обозреватель хранилищ Azure* — это полезное средство для проверки и изменения данных в больших двоичных объектах. Это бесплатный инструмент с открытым кодом. Скачать его можно на сайте [http://storageexplorer.com/](http://storageexplorer.com/). Исходный код доступен также по ссылке.

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в статье [Об учетных записях хранения Azure][azure-create-storage-account] в разделе «Просмотр, копирование и повторное создание ключей доступа к хранилищу».

1. Запустите обозреватель хранилищ Azure. Если вы запускаете Storage Explorer в первый раз, вам будет предложено ввести ___имя учетной записи хранения_\_ и __ключ учетной записи хранения__. Если вы уже запускали его раньше, нажмите кнопку __Добавить\_\_, чтобы добавить новые имя и ключ учетной записи хранения.

    Введите имя и ключ учетной записи хранения, используемые вашим кластером HDinsight, а затем нажмите __СОХРАНИТЬ И ОТКРЫТЬ__.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. В списке контейнеров в левой части интерфейса, щелкните имя контейнера, который связан с вашим кластером HDInsight. По умолчанию это имя кластера HDInsight, однако оно может отличаться, если вы ввели конкретное имя при создании кластера.

6. На панели инструментов выберите значок отправки.

    ![Панель инструментов с выделенным значком отправки](./media/hdinsight-upload-data/toolbar.png)

7. Укажите файл для отправки и щелкните **Открыть**. При появлении запроса выберите __Отправить__ и отправьте файл в корневой каталог контейнера хранилища. Если требуется передать файл по конкретному пути, введите путь в поле __Место назначения__, а затем нажмите __Отправить__.

    ![Диалоговое окно отправки файла](./media/hdinsight-upload-data/fileupload.png)
    
    После завершения передачи файла вы можете использовать его из заданий в кластере HDInsight.

##Подключение хранилища больших двоичных объектов как локального диска

Ознакомьтесь с разделом [Подключение хранилища больших двоичных объектов как локального диска](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##Службы

###Фабрика данных Azure

Фабрика данных Azure является полностью управляемой системой для создания служб хранения, обработки и перемещения данных и превращения этих служб в упрощенные, масштабируемые и надежные конвейеры производства данных.

С помощью фабрики данных Azure можно перемещать данные в хранилище BLOB-объектов Azure или создавать конвейеры данных, которые непосредственно используют функции HDInsight, такие как Hive и Pig.

Дополнительные сведения см. в [документации по фабрике данных Azure](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД.

Дополнительные сведения см. в разделе [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

##Пакеты SDK для разработки

Доступ к хранилищу BLOB-объектов Azure также можно получить с помощью пакета Azure SDK из следующих языков программирования:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Дополнительные сведения об установке пакетов SDK для Azure см. в разделе [Загрузки Azure](https://azure.microsoft.com/downloads/)


## Дальнейшие действия
Теперь, когда вы знаете, как передавать данные в HDInsight, узнайте, как их можно анализировать.

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
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

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0914_2016-->
