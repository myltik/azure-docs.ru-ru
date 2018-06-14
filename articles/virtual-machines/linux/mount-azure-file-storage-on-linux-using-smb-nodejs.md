---
title: Подключение хранилища файлов Azure на виртуальных машинах Linux по протоколу SMB с помощью Azure CLI 1.0 | Документация Майкрософт
description: Подключение хранилища файлов Azure на виртуальных машинах Linux по протоколу SMB
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30904837"
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Подключение хранилища файлов Azure на виртуальных машинах Linux по протоколу SMB с помощью Azure CLI 1.0

В этой статье описано, как подключить хранилище файлов Azure на виртуальной машине Linux по протоколу SMB. Хранилище файлов предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Для этого необходимы следующие компоненты:

* [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
* [файлы открытого и закрытого ключей Secure Shell (SSH)](mac-create-ssh-keys.md).

## <a name="cli-versions-to-use"></a>Версии интерфейса командной строки
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки:

- [Azure CLI 1.0](#quick-commands) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — это интерфейс командной строки нового поколения для модели развертывания с помощью Resource Manager.


## <a name="quick-commands"></a>Быстрые команды
Чтобы быстро выполнить задачу, следуйте указаниям в этом разделе. Подробные сведения и контекст см. в разделе [Подробное пошаговое руководство](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

### <a name="prerequisites"></a>предварительным требованиям
* Группа ресурсов.
* Виртуальная сеть Azure.
* Группа безопасности сети с входящим трафиком SSH.
* Подсеть.
* Учетная запись хранения Azure.
* Ключи учетной записи хранения Azure.
* Общая папка хранилища файлов Azure.
* Виртуальная машина Linux.

Замените все примеры параметров своими параметрами.

### <a name="create-a-directory-for-the-local-mount"></a>Создание каталога для локального подключения

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Подключение общего ресурса SMB хранилища файлов к точке подключения

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Сохранение подключения после перезагрузки
Добавьте следующую строку после `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Хранилище файлов предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Новая версия хранилища файлов позволяет подключить общую папку из любой ОС, которая поддерживает протокол SMB 3.0. Использование подключения SMB в Linux обеспечивает простое резервное копирование в надежное постоянное место хранения для архивации, которое поддерживается Соглашением об уровне обслуживания.

Перемещение файлов с виртуальной машины в точку подключения SMB, размещенную в хранилище файлов — отличный способ отладки с использованием журналов. Это связано с тем, что этот же общий ресурс SMB можно подключить локально к рабочей станции с ОС Mac, Linux или Windows. SMB — не лучшее решение для потоковой передачи журналов Linux или журналов приложений в режиме реального времени, так этот протокол не предназначен для такого интенсивного ведения журнала. Вместо SMB для сбора выходных данных журналов Linux и приложений лучше использовать специальный единый инструмент, работающий на уровне ведения журналов, например Fluentd.

Для работы с этим подробным пошаговым руководством необходимо сначала создать общую папку хранилища файлов и подключить ее по протоколу SMB к виртуальной машине Linux.

1. Создайте учетную запись хранения Azure, используя следующий код:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Отображение ключей учетной записи хранения.

    При создании учетной записи хранения ключи учетной записи создаются парами, поэтому их можно менять без прерывания работы службы. При смене одного из ключей в паре создается еще одна пара ключей. Так как новые ключи учетной записи хранения всегда создаются парами, у вас всегда будет по крайней мере один неиспользованный ключ к хранилищу данных, на который можно сменить текущий ключ. Чтобы отобразить ключи учетной записи хранения, используйте следующий код:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Создание общей папки хранилища файлов.

    Общая папка хранилища файлов содержит общий ресурс SMB. Квота всегда измеряется в гигабайтах (ГБ). Чтобы создать общую папку хранилища файлов, используйте следующий код:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Создайте каталог точек подключения.

    Для подключения к общему ресурсу SMB требуется создать локальный каталог в файловой системе Linux. Все данные, записанные в подключенный локальный каталог или считанные из него, перенаправляются в общую папку SMB, размещенную в хранилище файлов. Чтобы создать каталог, используйте следующий код:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Подключите общий ресурс SMB, используя следующий код:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Сохраните подключение SMB после перезагрузки.

    При перезагрузке виртуальной машины Linux подключенный общий ресурс SMB отключается во время завершения работы. Для его повторного подключения при загрузке необходимо добавить строку в файл Linux /etc/fstab. Linux использует файл fstab, чтобы получить список файловых систем, которые следует подключить во время загрузки. Если добавить общий ресурс SMB, общая папка хранилища файлов станет постоянно подключенной файловой системой в виртуальной машине Linux. Общий ресурс SMB в хранилище файлов можно добавить в новую виртуальную машину, используя cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Настройка виртуальной машины Linux во время создания с помощь cloud-init](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Добавление диска к виртуальной машине Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Шифрование дисков на виртуальной машине Linux с помощью интерфейса командной строки Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
