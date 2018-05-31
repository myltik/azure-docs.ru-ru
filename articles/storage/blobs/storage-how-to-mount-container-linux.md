---
title: Как подключить хранилище BLOB-объектов Azure в качестве файловой системы в Linux | Документация Майкрософт
description: Подключение контейнера хранилища BLOB-объектов Azure с FUSE в Linux
services: storage
documentationcenter: linux
author: seguler
manager: jahogg
ms.service: storage
ms.devlang: bash
ms.topic: article
ms.date: 05/10/2018
ms.author: seguler
ms.openlocfilehash: 1098eef15b559c30ef436d8e13bbe02bddb78649
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072098"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Как подключить хранилище BLOB-объектов в качестве файловой системы с использованием blobfuse

## <a name="overview"></a>Обзор
[blobfuse](https://github.com/Azure/azure-storage-fuse) — это виртуальный драйвер файловой системы для хранилища BLOB-объектов Azure, позволяющий получать доступ к имеющимся данным блочного BLOB-объекта в учетной записи хранения в файловой системе Linux. Хранилище BLOB-объектов Azure — это служба хранения объектов, не имеющая иерархического пространства имен. blobfuse предоставляет это пространство имен, используя схему виртуального каталога с прямой косой чертой (/) в качестве разделителя.  

В этом руководстве показано, как использовать blobfuse, подключить контейнер хранилища BLOB-объектов в Linux, а также получить доступ к данным. Дополнительные сведения о blobfuse см. в [репозитории blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> blobfuse не гарантирует полную совместимость с POSIX, он просто преобразовывает запросы в [REST API больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api). Например, операции переименования в POSIX являются атомарными, а в blobfuse — нет.
> Полный список различий между собственной файловой системой и blobfuse см. в [репозитории исходного кода blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Установка blobfuse в Linux
Двоичные файлы blobfuse доступны в [репозиториях программного обеспечения Майкрософт для Linux](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Чтобы установить blobfuse, настройте один из этих репозиториев.

### <a name="configure-the-microsoft-package-repository"></a>Настройка репозитория пакетов Майкрософт
Настройте [репозиторий пакетов Linux для продуктов Майкрософт](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Например, в дистрибутиве Enterprise Linux 6:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Аналогичным образом измените URL-адрес на `.../rhel/7/...`, чтобы указать дистрибутив Enterprise Linux 7.

Еще один пример для Ubuntu 14.04:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Аналогичным образом измените URL-адрес на `.../ubuntu/16.04/...`, чтобы указать дистрибутив Ubuntu 16.04.

### <a name="install-blobfuse"></a>Установка blobfuse

В дистрибутиве Ubuntu или Debian:
```bash
sudo apt-get install blobfuse
```

В дистрибутиве Enterprise Linux:
```bash
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Подготовка к подключению
Для blobfuse требуется временный путь в файловой системе, чтобы поместить все открытые файлы в буфер и кэшировать их, что помогает обеспечить производительность, близкую к исходной. Для этого временного пути выберите самый высокопроизводительный диск или используйте электронный диск для достижения оптимальной производительности. 

> [!NOTE]
> blobfuse хранит все содержимое открытых файлов во временном пути. Убедитесь, что имеется достаточно места для всех открытых файлов. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Использование электронного диска для временного пути (необязательно)
В следующем примере создается электронный диск на 16 ГБ, а также каталог для blobfuse. Выберите размер в соответствии со своими потребностями. Такой электронный диск позволяет blobfuse открывать файлы размером до 16 ГБ. 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-for-temporary-path"></a>Использование диска SSD для временного пути
В Azure можно использовать временные диски (SSD), доступные на виртуальной машине, чтобы обеспечить для blobfuse буфер с малой задержкой. В дистрибутивах Ubuntu этот временный диск подключен к "/mnt", тогда как в дистрибутивах RedHat и CentOS — к "/mnt/resource/".

Для пользователя нужно настроить права доступа к временному пути.
```bash
sudo mkdir /mnt/resource/blobfusetmp
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Настройка учетных данных учетной записи хранения
Для blobfuse требуется, чтобы учетные данные хранились в текстовом файле такого формата: 

```
accountName myaccount
accountKey myaccesskey==
containerName mycontainer
```

После создания этого файла ограничьте к нему доступ, чтобы никто не смог его прочитать.
```bash
chmod 700 fuse_connection.cfg
```

### <a name="create-an-empty-directory-for-mounting"></a>Создание пустого каталога для подключения
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Подключение

> [!NOTE]
> Полный список параметров подключения см. в [репозитории blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Чтобы подключить blobfuse, выполните следующую команду с использованием нужного профиля пользователя. Эта команда подключает контейнер, указанный в "/path/to/fuse_connection.cfg", к расположению "/mycontainer".

```bash
blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Теперь у вас должен быть доступ к блочным BLOB-объектам через обычные API файловой системы. Обратите внимание, что доступ к подключенному каталогу может получить только пользователь, выполнивший это подключение, что обеспечивает безопасный доступ. Если вы хотите разрешить доступ всем пользователям, можно выполнить подключение через параметр ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Дополнительная информация

* [Домашняя страница blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Сообщить о проблемах с blobfuse](https://github.com/Azure/azure-storage-fuse/issues) 

