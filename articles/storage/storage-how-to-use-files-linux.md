<properties
	pageTitle="Использование хранилища файлов Azure в Linux | Microsoft Azure"
        description="Создайте файловый ресурс Azure в облаке с помощью этого пошагового руководства. Управляйте содержимым файлового ресурса и подключайте файловый ресурс из виртуальной машины Azure под управлением Linux или из локального приложения с поддержкой SMB 3.0."
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="tysonn" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="02/15/2016"
      ms.author="jutang" />


# Использование хранилища файлов Azure в Linux

## Обзор

Хранилище файлов Azure предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Благодаря хранилищу файлов Azure можно переносить корпоративные приложения с файловых серверов в Azure. Приложения, работающие в Azure, могут легко подключать общие папки из виртуальных машин Azure под управлением Linux. Кроме того, новая версия хранилища файлов позволяет подключить общую папку из локального приложения, которое поддерживает протокол SMB 3.0.

Создавать общие папки Azure можно с помощью [портала Azure](https://portal.azure.com), командлетов PowerShell службы хранилища Azure, клиентских библиотек службы хранилища Azure и REST API службы хранилища Azure. А так как эти общие папки используют протокол SMB, вы можете обращаться к ним с помощью стандартных API-интерфейсов файловой системы.

Хранилище файлов Azure основано на той же технологии, что и хранилища BLOB-объектов, таблиц и очередей, поэтому оно предлагает все средства обеспечения доступности, надежности, масштабируемости и геоизбыточности, встроенные в платформу службы хранения Azure. Дополнительные сведения о целевых показателях производительности и ограничениях хранилища файлов см. в статье [Целевые показатели производительности и масштабируемости для хранилища Azure](storage-scalability-targets.md).

Хранилище файлов теперь стало общедоступным и поддерживает протоколы SMB 2.1 и SMB 3.0. Дополнительные сведения о хранилище файлов см. в статье [REST API файловой службы](https://msdn.microsoft.com/library/azure/dn167006.aspx).

>[AZURE.NOTE] Так как клиент SMB Linux пока не поддерживает шифрование, то для подключения общей папки из Linux по-прежнему требуется, чтобы клиент находился в одном регионе Azure с этой общей папкой. Однако реализация поддержки шифрования для Linux уже запланирована разработчиками Linux, отвечающими за функциональность SMB. Дистрибутивы Linux, поддерживающие шифрование, в будущем также смогут подключать общую папку Azure откуда угодно.

## Видео. Использование хранилища файлов Azure с Linux

В этом видео показано создание и использование общих папок Azure в ОС Linux.

> [AZURE.VIDEO azure-file-storage-with-linux]

## Выбор используемого дистрибутива Linux ##

При создании виртуальной машины Linux в Azure можно указать образ Linux, который поддерживает SMB 2.1 или более поздней версии, из коллекции образов Azure. Ниже приведен список рекомендуемых образов Linux:

- Ubuntu Server 14.04
- Ubuntu Server 15.04
- CentOS 7.1
- Open SUSE 13.2
- SUSE Linux Enterprise Server 12
- SUSE Linux Enterprise Server 12 (образ Premium)

## Подключение общей папки ##

Чтобы подключить общую папку из виртуальной машины под управлением Linux, необходимо установить клиент SMB/CIFS, если в используемом дистрибутиве отсутствует встроенный клиент. Вот команда из Ubuntu для установки одного cifs-utils:

    sudo apt-get install cifs-utils

Далее необходимо создать точку подключения (mkdir mymountpoint), а затем выполнить команду подключения, подобную следующей:

     sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename ./mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Для подключения общей папки можно также добавить параметры в свой /etc/fstab.

Обратите внимание на то, что 0777 здесь представляет код разрешения файла или каталога, который предоставляет всем пользователям разрешения на выполнение, чтение или запись. Его можно заменить другим кодом разрешения файла, согласно документации по разрешениям файлов Linux.

Чтобы сохранить подключение общей папки после перезагрузки, можно добавить в /etc/fstab параметр следующего вида:

    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username= myaccountname,password= StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777

Например, если вы создали виртуальную машину Azure с помощью образа Linux Ubuntu Server 15.04 (доступен в галерее образов Azure), файл можно подключить следующим образом.

    azureuser@azureconubuntu:~$ sudo apt-get install cifs-utils
    azureuser@azureconubuntu:~$ sudo mkdir /mnt/mountpoint
    azureuser@azureconubuntu:~$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@azureconubuntu:~$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

При использовании CentOS 7.1 файл можно подключить следующим образом:

    [azureuser@AzureconCent ~]$ sudo yum install samba-client samba-common cifs-utils
    [azureuser@AzureconCent ~]$ sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    [azureuser@AzureconCent ~]$ df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

При использовании Open SUSE 13.2 файл можно подключить следующим образом:

    azureuser@AzureconSuse:~> sudo zypper install samba*  
    azureuser@AzureconSuse:~> sudo mkdir /mnt/mountpoint
    azureuser@AzureconSuse:~> sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mountpoint -o vers=3.0,user=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    azureuser@AzureconSuse:~> df -h /mnt/mountpoint
    Filesystem  Size  Used Avail Use% Mounted on
    //myaccountname.file.core.windows.net/mysharename  5.0T   64K  5.0T   1% /mnt/mountpoint

## Управление общей папкой ##

[Портал Azure](https://portal.azure.com) предоставляет пользовательский интерфейс для управления хранилищем файлов Azure. Следующие действия можно выполнять в браузере:

- передавать файлы в общую папку и скачивать файлы из нее;
- отслеживать фактическое использование каждой общей папки;
- изменять квоты на размер общей папки;
- копировать команду `net use`, которая позволит подключить общую папку с клиентского компьютера Windows.

Для управления общей папкой можно также использовать межплатформенный интерфейс командной строки Azure (Azure CLI) в Linux. Azure CLI предоставляет набор межплатформенных команд с открытым кодом для работы с хранилищем Azure, включая хранилища файлов. Он предоставляет практически те же функции, что и портал Azure, а также различные возможности доступа к данным. Примеры см. в статье [Использование интерфейса командной строки CLI Azure со службой хранилища Azure](storage-azure-cli.md).

## Разработка с использованием хранилища файлов ##

Разработчики могут построить приложение с хранилищем файлов, используя [клиентскую библиотеку хранилища Azure для Java](https://github.com/azure/azure-storage-java). Примеры кода см. в статье [Использование хранилища файлов из Java](storage-java-how-to-use-file-storage.md).

Для разработки хранилища файлов можно также использовать [клиентскую библиотеку хранилища Azure для Node.js](https://github.com/Azure/azure-storage-node).

## Обратная связь и дополнительные сведения ##

Пользователи Linux, нам очень интересно ваше мнение!

Хранилище файлов Azure для группы пользователей Linux включает форум, где можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи хранилища файлов Azure в Linux](mailto:azurefileslinuxusers@microsoft.com).

## Дальнейшие действия

Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.

### Тематические статьи и видео

- [Хранилище файлов Azure: удобная облачная файловая система SMB для Windows и Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Приступая к работе с хранилищем файлов Azure в Windows](storage-dotnet-how-to-use-files.md)

### Средства для работы с хранилищем файлов

- [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)
- [Создание общих папок и управление ими](storage-azure-cli.md#create-and-manage-file-shares) с помощью интерфейса командной строки Azure.

### Справочные материалы

- [Справочник по REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Записи блога

- [Хранилище файлов Azure стало общедоступным](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
- [Inside Azure File Storage (Хранилище файлов Azure: взгляд изнутри)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
- [Введение в службы файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Сохраняемые подключения к файлам Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=AcomDC_0218_2016-->