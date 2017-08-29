---
title: "Копирование или перемещение данных в службу хранилища Azure с помощью AzCopy для Linux | Документация Майкрософт"
description: "Служебная программа AzCopy для Linux позволяет копировать и перемещать данные в BLOB-объект или файл и из него. Копируйте данные в хранилище Azure из локальных файлов, а также внутри учетной записи хранения и из одной такой учетной записи в другую. Легко переносите данные в хранилище Azure."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: seguler
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 441227d84b9c1ec721ae36fdc423ba797654f128
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="transfer-data-with-azcopy-on-linux"></a>Перенос данных с помощью AzCopy для Linux
AzCopy для Linux — это служебная программа командной строки. Она предназначена для копирования данных из хранилища BLOB-объектов и хранилища файлов Microsoft Azure (и обратно) с помощью простых команд, обеспечивающих оптимальную производительность. Кроме того, она позволяет копировать данные из одного объекта в другой в пределах одной учетной записи хранения или из одной такой записи в другую.

Существуют две версии AzCopy, которые можно скачать. Служебная программа AzCopy для Linux основана на платформе .NET Core, которая нацелена на платформы Linux, использующие параметры командной строки в формате POSIX. Служебная программа [AzCopy для Windows](../storage-use-azcopy.md) основана на платформе .NET Framework и использует параметры командной строки в формате Windows. В этой статье рассматривается AzCopy для Linux.

## <a name="download-and-install-azcopy"></a>Скачивание и установка AzCopy
### <a name="installation-on-linux"></a>Установка в Linux

Для работы AzCopy для Linux на платформе должен быть установлен компонент .NET Core. Ознакомьтесь с инструкциями по установке на странице [.NET Core](https://www.microsoft.com/net/core#linuxubuntu).

Для примера давайте установим .NET Core в Ubuntu 16.10. Последняя версия руководства доступна на странице установки [.NET Core для Linux](https://www.microsoft.com/net/core#linuxubuntu).


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.3
```

После установки .NET Core скачайте и установите AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

После установки AzCopy для Linux извлеченные файлы можно будет удалить. Если у вас нет привилегий суперпользователя, AzCopy можно также выполнить с помощью сценария оболочки azcopy в извлеченной папке. 

### <a name="alternative-installation-on-ubuntu"></a>Альтернативная установка в Ubuntu

**Ubuntu 14.04**

Добавьте источник apt для .NET Core.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Добавьте источник apt для репозитория продуктов Linux от корпорации Майкрософт и установите AzCopy.

```bash
curl https://packages.microsoft.com/config/ubuntu/14.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Добавьте источник apt для .NET Core.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Добавьте источник apt для репозитория продуктов Linux от корпорации Майкрософт и установите AzCopy.

```bash
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.10**

Добавьте источник apt для .NET Core.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
```

Добавьте источник apt для репозитория продуктов Linux от корпорации Майкрософт и установите AzCopy.

```bash
curl https://packages.microsoft.com/config/ubuntu/16.10/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Написание первой команды AzCopy
В командах AzCopy используется следующий базовый синтаксис:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

В приведенных ниже примерах описаны разные сценарии копирования данных в большие двоичные объекты и файлы Microsoft Azure (и обратно). Чтобы получить подробное объяснение параметров, используемых в каждом примере, воспользуйтесь меню `azcopy --help`.

## <a name="blob-download"></a>Большой двоичный объект: скачивание
### <a name="download-single-blob"></a>Скачивание одного большого двоичного объекта

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Если папка `/mnt/myfiles` не существует, AzCopy создаст ее и скачает `abc.txt ` в эту новую папку.

### <a name="download-single-blob-from-secondary-region"></a>Скачивание большого двоичного объекта из дополнительного региона

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Обратите внимание: должно быть включено геоизбыточное хранилище с доступом только для чтения.

### <a name="download-all-blobs"></a>Скачивание всех больших двоичных объектов

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Предположим, что в указанном контейнере находятся следующие BLOB-объекты:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

После скачивания в каталог `/mnt/myfiles` будут помещены следующие файлы:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Если не задать параметр `--recursive`, большие двоичные объекты не будут скачаны.

### <a name="download-blobs-with-specified-prefix"></a>Скачивание больших двоичных объектов с указанным префиксом

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Предположим, что в указанном контейнере находятся следующие BLOB-объекты: Будут скачаны все большие двоичные объекты, имя которых начинается с префикса `a`.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

После скачивания в папку `/mnt/myfiles` будут помещены следующие файлы:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Префикс применяется к виртуальному каталогу, который формирует первую часть имени большого двоичного объекта. В указанном выше примере виртуальный каталог не соответствует заданному префиксу, поэтому ни один большой двоичный объект не скачивается. Кроме того, если не задан параметр `--recursive`, то AzCopy не скачивает большие двоичные объекты.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Установка одинакового времени последнего изменения для экспортированных файлов и исходных больших двоичных объектов

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Исключить большие двоичные объекты из операции скачивания вы можете также на основе времени их последнего изменения. Например, если нужно исключить большие двоичные объекты, измененные в то же время, что и конечный файл, или позднее, добавьте параметр `--exclude-newer`:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

А если нужно исключить большие двоичные объекты, измененные в то же время, что и конечный файл, или раньше, добавьте параметр `--exclude-older`:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Большой двоичный объект: отправка
### <a name="upload-single-file"></a>Отправка одного файла

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Если указанный контейнер назначения не существует, AzCopy создаст его и передаст в него файл.

### <a name="upload-single-file-to-virtual-directory"></a>Отправка одного файла в виртуальный каталог

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "abc.txt"
```

Если указанный виртуальный каталог не существует, AzCopy передаст файл и включит имя виртуального каталога в имя большого двоичного объекта (*например*, `vd/abc.txt` в указанном выше примере).

### <a name="upload-all-files"></a>Отправка всех файлов

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Выбор параметра `--recursive` обеспечивает рекурсивную передачу указанного каталога в хранилище BLOB-объектов. Это означает, что передаются также все вложенные папки и файлы. Например, предположим, что следующие файлы находятся в папке `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Когда передача завершится, в контейнер будут помещены следующие файлы:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Если параметр `--recursive` не указать, будут переданы только следующие три файла:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Отправка файлов, соответствующих указанному шаблону

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Предположим, что следующие файлы размещены в папке `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Когда передача завершится, в контейнер будут помещены следующие файлы:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Если параметр `--recursive` не указан, AzCopy пропускает файлы, находящиеся во вложенных каталогах.

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Задание типа содержимого MIME целевого большого двоичного объекта
По умолчанию AzCopy задает для типа содержимого целевого большого двоичного объекта значение `application/octet-stream`. Однако вы можете явно задать тип содержимого с помощью параметра `--set-content-type [content-type]`. Этот синтаксис задает тип содержимого для всех больших двоичных объектов в операции отправки.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Если вы задали параметр `--set-content-type`, не указав значение, AzCopy задаст тип содержимого для каждого большого двоичного объекта или файла в соответствии с расширением файла.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Большой двоичный объект: копирование
### <a name="copy-single-blob-within-storage-account"></a>Копирование большого двоичного объекта в пределах учетной записи хранения

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key> \
    --dest-key <key> \
    --include "abc.txt"
```

При копировании большого двоичного объекта без параметра --sync-copy выполняется [операция копирования на стороне сервера](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-single-blob-across-storage-accounts"></a>Копирование большого двоичного объекта между различными учетными записями хранения

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

При копировании большого двоичного объекта без параметра --sync-copy выполняется [операция копирования на стороне сервера](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Копирование одного большого двоичного объекта из дополнительного региона в основной регион

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt"
```

Обратите внимание: должно быть включено геоизбыточное хранилище с доступом только для чтения.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Копирование большого двоичного объекта и его моментальных снимков между различными учетными записями хранения

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1 \
    --destination https://destaccount.blob.core.windows.net/mycontainer2 \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

После выполнения копирования целевой контейнер будет содержать большой двоичный объект и его моментальные снимки. Контейнер будет содержать следующий большой двоичный объект и его моментальные снимки:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Синхронное копирование больших двоичных объектов между учетными записями хранения
По умолчанию AzCopy выполняет асинхронное копирование данных между двумя конечными точками хранилища. Следовательно, операция копирования выполняется в фоновом режиме, используя запасную пропускную способность, для которой не установлено соглашение об уровне обслуживания, регулирующее скорость, с которой копируется большой двоичный объект. 

Параметр `--sync-copy` обеспечивает постоянную скорость операции копирования. AzCopy выполняет синхронное копирование, при котором большие двоичные объекты копируются из указанного источника в локальную память путем скачивания, а затем загружаются в целевое хранилище больших двоичных объектов.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

Параметр `--sync-copy` может повлечь дополнительные затраты на исходящий трафик по сравнению с асинхронным копированием. Во избежание таких затрат мы советуем использовать данный режим в виртуальных машинах Azure, которые находятся в одном регионе с вашей исходной учетной записью хранения.

## <a name="file-download"></a>Файл: скачивание
### <a name="download-single-file"></a>Скачивание одного файла

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "abc.txt"
```

Если исходный файл для копирования находится в общей папке Azure, необходимо указать либо точное имя файла (*например*, `abc.txt`) для копирования одного файла, либо параметр `--recursive` для рекурсивного копирования всех файлов в общей папке. Попытка одновременно задать шаблон файла и параметр `--recursive` приводит к ошибке.

### <a name="download-all-files"></a>Скачивание всех файлов

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Обратите внимание на то, что пустые папки не скачиваются.

## <a name="file-upload"></a>Файл: отправка
### <a name="upload-single-file"></a>Отправка одного файла

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include abc.txt
```

### <a name="upload-all-files"></a>Отправка всех файлов

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Обратите внимание на то, что пустые папки не передаются.

### <a name="upload-files-matching-specified-pattern"></a>Отправка файлов, соответствующих указанному шаблону

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Файл: копирование
### <a name="copy-across-file-shares"></a>Копирование общих файловых ресурсов

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
При копировании файла между файловыми ресурсами выполняется [операция копирования на стороне сервера](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-file-share-to-blob"></a>Копирование из общего файлового ресурса в BLOB-объект

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
При копировании файла из файлового ресурса в большой двоичный объект выполняется [операция копирования на стороне сервера](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-blob-to-file-share"></a>Копирование из большого двоичного объекта в общий файловый ресурс

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
При копировании файла из большого двоичного объекта в файловый ресурс выполняется [операция копирования на стороне сервера](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="synchronously-copy-files"></a>Синхронное копирование файлов
Вы можете указать параметр `--sync-copy` для синхронного копирования данных из хранилища файлов в хранилище файлов, из хранилища файлов в хранилище BLOB-объектов, а также из хранилища BLOB-объектов в хранилище файлов. AzCopy выполняет эту операцию, скачивая исходные данные в локальную память и передавая их в место назначения. В этом случае взимается стандартная плата за исходящий трафик.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

При копировании из хранилища файлов в хранилище BLOB-объектов пользователь может указать параметр `/BlobType:page` для изменения типа большого двоичного объекта назначения.

Обратите внимание на то, что параметр `--sync-copy` может повлечь дополнительные затраты на исходящий трафик по сравнению с асинхронным копированием. Во избежание таких затрат мы советуем использовать данный режим в виртуальных машинах Azure, которые находятся в одном регионе с вашей исходной учетной записью хранения.

## <a name="other-azcopy-features"></a>Другие функции AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Копирование только тех данных, которых нет в целевой папке
Предотвратить копирование старого или нового ресурса позволяют параметры `--exclude-older` и `--exclude-newer` соответственно. Если нужно скопировать только те исходные ресурсы, которых нет в целевой папке, в команде AzCopy вы можете указать оба параметра:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Использование файла конфигурации для задания параметров командной строки

```azcopy
azcopy --config-file "azcopy-config.ini"
```

В файл конфигурации можно добавить параметры командной строки AzCopy. AzCopy обрабатывает параметры в файле, как если бы он были заданы в командной строке, выполняя прямую замену содержимого файла.

Предположим, у нас имеется файл конфигурации `copyoperation`, содержащий приведенные ниже строки. Каждый параметр AzCopy можно указать как в одной строке,

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

так и в разных:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

При выполнении AzCopy возникнет ошибка, если разделить параметр на две строки, как показано ниже на примере параметра `--source-key`.

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Указание подписи общего доступа (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer2 \
    --source-sas <SAS1> \
    --dest-sas <SAS2> \
    --include abc.txt
```

Кроме того, вы можете указать SAS в коде URI контейнера:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

Обратите внимание на то, что в настоящее время AzCopy поддерживает только [подписанный URL-адрес учетной записи](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1).

### <a name="journal-file-folder"></a>Папка файлов журнала
При каждом вводе команды для AzCopy выполняется проверка на наличие файла журнала в папке по умолчанию или в папке, которая была задана с помощью данного параметра. Если в обоих местах файл журнала отсутствует, AzCopy воспринимает операцию как новую и создает новый файл журнала.

Если файл журнала существует, AzCopy проверяет, соответствует ли введенная командная строка командной строке в файле журнала. Если две командные строки совпадают, AzCopy возобновляет незавершенную операцию. Если они не совпадают, AzCopy предложит пользователю либо перезаписать файл журнала, чтобы начать новую операцию, либо отменить текущую операцию.

Использование расположения по умолчанию для файла журнала.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Если опустить параметр`--resume` или задать параметр `--resume` без пути папки, как указано выше, AzCopy создает файл журнала в расположении по умолчанию: `~\Microsoft\Azure\AzCopy`. Если файл журнала уже существует, то AzCopy возобновляет операцию на основе файла журнала.

Использование настраиваемого расположения для файла журнала.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

В этом примере создается файл журнала, если он еще не существует. Если такой файл существует, то AzCopy возобновляет операцию на основе файла журнала.

Если вы хотите возобновить операцию AzCopy, повторите ту же самую команду. AzCopy для Linux запросит подтверждение.

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Вывод подробных журналов

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Задание количества одновременных операций для запуска
Параметр `--parallel-level` задает количество одновременных операций копирования. По умолчанию AzCopy запускает несколько одновременных операций для увеличения скорости передачи данных. Количество одновременных операций равно восьмикратному количеству процессоров в вашем распоряжении. При выполнении AzCopy в сети с низкой пропускной способностью можно задать меньшее значение для параметра --parallel-level, чтобы избежать сбоев, вызванных соревнованием за ресурсы.

[!TIP]
>Чтобы просмотреть полный список параметров AzCopy, ознакомьтесь с меню azcopy --help.

## <a name="known-issues-and-best-practices"></a>Известные проблемы и рекомендации
### <a name="error-net-core-is-not-found-in-the-system"></a>Ошибка: компонент .NET Core не найден в системе.
Если отображается сообщение об ошибке, в котором говорится, что в системе не установлен компонент .NET Core, возможно, отсутствует переменная PATH, указывающая путь к двоичному файлу `dotnet` .NET Core.

Чтобы устранить эту проблему, найдите данный двоичный файл .NET Core в системе.
```bash
sudo find / -name dotnet
```

Эта команда возвращает путь к двоичному файлу dotnet. 

    /opt/rh/rh-dotnetcore11/root/usr/bin/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/dotnet
    /opt/rh/rh-dotnetcore11/root/usr/lib64/dotnetcore/shared/Microsoft.NETCore.App/1.1.2/dotnet

Теперь добавьте этот путь в переменную PATH. В случае использования sudo измените secure_path, добавив путь к двоичному файлу dotnet.
```bash 
sudo visudo
### Append the path found in the preceding example to 'secure_path' variable
```

В этом примере переменная secure_path имеет следующий вид.

```
secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/opt/rh/rh-dotnetcore11/root/usr/bin/
```

Измените .bash_profile/.profile для текущего пользователя, добавив путь к двоичному файлу dotnet в переменную PATH. 
```bash
vi ~/.bash_profile
### Append the path found in the preceding example to 'PATH' variable
```

Убедитесь, что теперь компонент .NET Core указан в переменной PATH.
```bash
which dotnet
sudo which dotnet
```

### <a name="error-installing-azcopy"></a>Ошибка при установке AzCopy
В случае возникновения проблем при установке AzCopy можно попытаться запустить AzCopy с помощью сценария bash в извлеченной папке `azcopy`.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Ограничение одновременных операций записи при копировании данных
При копировании BLOB-объектов или файлов с помощью AzCopy следует иметь в виду, что другое приложение может изменять данные в то время, когда они копируются. Если это возможно, обеспечьте, чтобы во время операции копирования не происходило изменение копируемых данных. Например, при копировании VHD, связанного с виртуальной машиной Azure, убедитесь в том, что никакое другое приложение в это время не записывает данные на VHD. Для этого рекомендуется сдать ресурс, который нужно скопировать, в аренду. В качестве альтернативы можно сначала создать моментальный снимок VHD, а затем скопировать его.

Если не удается предотвратить запись в BLOB-объекты или файлы во время их копирования со стороны других приложений, следует иметь в виду, что к моменту завершения задания скопированные ресурсы могут больше не иметь полного соответствия с исходными ресурсами.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Запускайте один экземпляр AzCopy на одном компьютере.
AzCopy предназначен для максимального использования ресурсов компьютера для ускорения передачи данных. Рекомендуется запускать только один экземпляр AzCopy на одном компьютере и указать параметр `--parallel-level`, если требуется несколько параллельных операций. Для получения более подробной информации введите в командной строке `AzCopy --help parallel-level` .

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной информации о службе хранилища Azure и AzCopy ознакомьтесь со следующими ресурсами.

### <a name="azure-storage-documentation"></a>Документация по хранилищу Azure:
* [Введение в хранилище Azure](../storage-introduction.md)
* [создать учетную запись хранения;](../storage-create-storage-account.md)
* [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ (предварительная версия)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs)
* [Использование Azure CLI 2.0 со службой хранилища Azure](../storage-azure-cli.md)
* [Использование хранилища BLOB-объектов из C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Использование хранилища BLOB-объектов из Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Использование хранилища больших двоичных объектов из Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Использование хранилища больших двоичных объектов из Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Записи блога по хранилищу Azure:
* [Announcing AzCopy on Linux Preview](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/) (Объявление о выпуске предварительной версии AzCopy для Linux)
* [Введение в предварительную версию библиотеки движения данных в хранилище Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: введение в синхронное копирование и настраиваемый тип содержимого](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: выпуск общедоступной версии AzCopy 3.0 и предварительной версии AzCopy 4.0 с поддержкой таблиц и файлов](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: оптимизированные сценарии для крупномасштабного копирования](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: поддержка геоизбыточного хранилища для доступа с правом чтения](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy – Transfer data with re-startable mode and SAS Token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx) (AzCopy: передача данных с использованием перезапускаемого режима и маркера SAS)
* [AzCopy: использование копирования больших двоичных объектов между разными учетными записями](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: отправка и скачивание файлов для больших двоичных объектов Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


