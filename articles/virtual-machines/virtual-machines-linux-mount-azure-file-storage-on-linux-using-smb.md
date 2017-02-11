---
title: "Подключение хранилища файлов Azure на виртуальных машинах Linux с помощью SMB | Документация Майкрософт"
description: "Подключение хранилища файлов Azure на виртуальных машинах Linux с помощью SMB."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 6a3c4b85642a7b6eb4dbd0efc3b046a89328b3f0
ms.openlocfilehash: 711d217841690ee78321a1ae1a56571c49ef74cc


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Подключение хранилища файлов Azure на виртуальных машинах Linux с помощью SMB

В этой статье показано, как использовать службу хранилища файлов Azure на виртуальной машине Linux, используя подключение SMB.  Хранилище файлов Azure предоставляет общие папки в облаке с доступом по стандартному протоколу SMB.  Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);

- [файлы открытого и закрытого ключа SSH](virtual-machines-linux-mac-create-ssh-keys.md).

## <a name="quick-commands"></a>Быстрые команды

Если вам необходимо быстро выполнить задачу, в следующем разделе описаны нужные команды. Более подробные сведения и контекст для каждого этапа можно найти в остальной части документа [начиная отсюда](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Необходимые компоненты: группа ресурсов, виртуальная сеть, группа безопасности сети с входящим трафиком SSH, подсеть, учетная запись хранения Azure и ее ключи, общая папка хранилища файлов Azure и виртуальная машина Linux. Замените все примеры параметров своими параметрами.

Создание каталога для локального подключения

```bash
mkdir -p /mnt/mymountpoint
```

Подключение общего ресурса SMB хранилища файлов Azure к точке подключения

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Чтобы сохранить подключение после перезагрузки, добавьте строку в `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Хранилище файлов Azure предоставляет общие папки в облаке с доступом по стандартному протоколу SMB.  Кроме того, новая версия хранилища файлов позволяет подключить общую папку из любой ОС, которая поддерживает протокол SMB 3.0.  Использование подключения SMB в Linux обеспечивает простое резервное копирование в надежное постоянное место хранения для архивации, которое поддерживается Соглашением об уровне обслуживания.  

Перемещение файлов с виртуальной машины в точку подключения SMB, размещенную в хранилище файлов Azure, — это отличный способ отладки c использованием журналов, поскольку этот же общий ресурс SMB можно подключить локально к рабочей станции с ОС Mac, Linux или Windows.  SMB — это не лучшее решение для потоковой передачи журналов Linux или приложений в режиме реального времени, так как протокол SMB не предназначен для такого интенсивного ведения журналов.  Вместо SMB для сбора выходных данных журналов Linux и приложений лучше использовать специальный единый инструмент, работающий на уровне ведения журналов, например Fluentd.

Для прохождения этого подробного пошагового руководства необходимо сначала создать общую папку хранилища файлов Azure и подключить ее по протоколу SMB к виртуальной машине Linux.

## <a name="create-the-azure-storage-account"></a>Создание учетной записи хранения Azure

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>Отображение ключей учетной записи хранения

Ключи учетной записи хранения Azure создаются парами при создании учетной записи хранения.  Ключи учетной записи хранения создаются парами, поэтому их можно менять без прерывания работы службы.  После смены одного из ключей в паре создается новая пара ключей.  Поскольку новые ключи учетной записи хранения всегда создаются парами, у вас всегда будет по крайней мере один неиспользованный ключ к хранилищу данных, на который можно сменить текущий ключ.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Создание общей папки хранилища файлов Azure

Создайте общую папку хранилища файлов, содержащую общий ресурс SMB.  Квота всегда измеряется в гигабайтах (ГБ).

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Создайте каталог точек подключения.

Для подключения к общему ресурсу SMB требуется локальный каталог в файловой системе Linux.  Все данные, записанные в подключенный локальный каталог или считанные из него, перенаправляются в общую папку SMB, размещенную в хранилище файлов Azure.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Подключение общего ресурса SMB

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Сохранение подключения SMB после перезагрузки

При перезагрузке виртуальной машины Linux подключенный общий ресурс SMB отключается во время завершения работы.  Для его повторного подключения при загрузке необходимо добавить строку в файл Linux `/etc/fstab`.  ОС Linux использует файл `fstab` в качестве списка файловых систем, которые ей следует подключить во время загрузки.  Если добавить общий ресурс SMB, общая папка хранилища файлов Azure станет постоянно подключенной файловой системой в виртуальной машине Linux.  Общий ресурс SMB в хранилище файлов Azure можно добавить в новую виртуальную машину с помощью `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка виртуальной машины Linux во время создания с помощь cloud-init](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Добавление диска к виртуальной машине Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Encrypt disks on a Linux VM using the Azure CLI](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Шифрование дисков на виртуальной машине Linux с помощью интерфейса командной строки Azure)



<!--HONumber=Dec16_HO3-->


