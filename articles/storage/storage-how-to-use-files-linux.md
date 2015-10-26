<properties
	pageTitle="Использование хранилища файлов Azure в Linux | Microsoft Azure"
        description="Создайте общую папку в облаке и подключите ее из виртуальной машины Azure или из локального приложения, запущенного в Linux."
        services="storage"
        documentationCenter="na"
        authors="jasontang501"
        manager="jahogg"
        editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="na"
      ms.topic="article"
      ms.date="10/06/2015"
      ms.author="jutang;tamram" />


# Использование хранилища файлов Azure в Linux 

## Обзор

Хранилище файлов Azure предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Хранилище файлов теперь стало общедоступным и поддерживает как SMB 3.0, так и SMB 2.1.

Вы можете создавать общие папки Azure с помощью портала предварительной версии Azure, командлетов PowerShell службы хранилища Azure, клиентских библиотек службы хранилища Azure и REST API службы хранилища Azure. А так как эти общие папки используют протокол SMB, к ним можно обращаться и через привычные стандартные API-интерфейсы файловой системы.

Приложения, работающие в Azure, могут легко подключать общие папки из виртуальных машин Azure. Новая версия хранилища файлов позволяет также подключить общую папку из локального приложения, которое поддерживает протокол SMB 3.0.

Помните, поскольку клиент SMB Linux пока не поддерживает шифрование, для подключения общей папки из Linux по-прежнему требуется, чтобы клиент находился в одном регионе Azure с этой общей папкой. Однако реализация поддержки шифрования для Linux уже запланирована разработчиками Linux, отвечающими за функциональность SMB. Дистрибутивы Linux, поддерживающие шифрование, в будущем также смогут подключать общую папку Azure откуда угодно.

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

    azureuser@azureconubuntu:~$ sudo apt-get install apt-file
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

Теперь [портал предварительной версии Azure](https://portal.azure.com/) предоставляет пользовательский интерфейс для управления хранилищем файлов Azure. Следующие действия можно выполнять в браузере:

- передавать файлы в общую папку и скачивать файлы из нее;
- отслеживать фактическое использование каждой общей папки;
- изменять квоты на размер общей папки;
- копировать команду `net use`, которая позволит подключить общую папку с клиентского компьютера Windows. 

Для управления общей папкой можно также использовать межплатформенный интерфейс командной строки Azure (Azure CLI) в Linux. Azure CLI предоставляет набор межплатформенных команд с открытым кодом для работы с хранилищем Azure, включая хранилища файлов. Он предоставляет практически те же функции, что и портал Azure, а также различные возможности доступа к данным. Примеры см. в статье [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](storage-azure-cli.md).

## Разработка с использованием хранилища файлов ##

Разработчики могут построить приложение с хранилищем файлов, используя [клиентскую библиотеку хранилища Azure для Java](https://github.com/azure/azure-storage-java). Примеры см. в статье [Использование табличного хранилища из Java](storage-java-how-to-use-file-storage.md).

Для разработки хранилища файлов можно также использовать [клиентскую библиотеку хранилища Azure для Node.js](https://github.com/Azure/azure-storage-node).

## Обратная связь и дополнительные сведения ##

Пользователи Linux, нам очень интересно ваше мнение!

Хранилище файлов Azure для группы пользователей Linux включает форум, где можно поделиться своим мнением и опытом адаптации хранилища файлов в Linux. Чтобы вступить в группу пользователей, отправьте электронное письмо в группу [Пользователи хранилища файлов Azure в Linux](mailto:azurefileslinuxusers@microsoft.com).

## Дальнейшие действия

Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.

### Тематические статьи

- [Использование хранилища файлов Azure в Windows](storage-dotnet-how-to-use-files.md)

### Средства для работы с хранилищем файлов

- [Использование AzCopy со службой хранилища Microsoft Azure](storage-use-azcopy.md)
- [Использование интерфейса командной строки (CLI) Azure со службой хранилища Azure](storage-azure-cli.md#create-and-manage-file-shares)

### Справочные материалы

- [Справочник по REST API службы файлов](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### Записи блога

- [Хранилище файлов Azure стало общедоступным](http://go.microsoft.com/fwlink/?LinkID=626728&clcid=0x409)
- [Подробный обзор хранилища файлов Azure](http://go.microsoft.com/fwlink/?LinkID=626729&clcid=0x409) 
- [Введение в службы файлов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Сохраняемые подключения к файлам Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=Oct15_HO3-->