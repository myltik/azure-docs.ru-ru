---
title: Подключение хранилища файлов Azure на виртуальных машинах Linux с помощью протокола SMB | Документация Майкрософт
description: Как подключить хранилище файлов Azure на виртуальных машинах Linux, используя протокол SMB и Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: iainfou
ms.openlocfilehash: 01e18103f9e94615357ff3b9c4be7f2473763a57
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Подключение хранилища файлов Azure на виртуальных машинах Linux с помощью протокола SMB

Из этой статье вы узнаете, как работать со службой хранилища файлов Azure на виртуальной машине Linux, используя SMB-подключение и Azure CLI 2.0. Хранилище файлов Azure предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Эти действия можно также выполнить с помощью [Azure CLI 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md). Для этого необходимы следующие компоненты:

- [учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/);
- [файлы открытого и закрытого ключа SSH](mac-create-ssh-keys.md).

## <a name="quick-commands"></a>Быстрые команды

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
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Сохранение подключения после перезагрузки
Для этого добавьте приведенную ниже строку в файл `/etc/fstab`.

```bash
//myaccountname.file.core.windows.net/mysharename /mnt/mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Подробное пошаговое руководство

Хранилище файлов предоставляет общие папки в облаке с доступом по стандартному протоколу SMB. Новая версия хранилища файлов позволяет подключить общую папку из любой ОС, которая поддерживает протокол SMB 3.0. Использование подключения SMB в Linux обеспечивает простое резервное копирование в надежное постоянное место хранения для архивации, которое поддерживается Соглашением об уровне обслуживания.

Перемещение файлов с виртуальной машины в точку подключения SMB, размещенную в хранилище файлов — отличный способ отладки с использованием журналов. Это связано с тем, что этот же общий ресурс SMB можно подключить локально к рабочей станции с ОС Mac, Linux или Windows. SMB — не лучшее решение для потоковой передачи журналов Linux или журналов приложений в режиме реального времени, так этот протокол не предназначен для такого интенсивного ведения журнала. Вместо SMB для сбора выходных данных журналов Linux и приложений лучше использовать специальный единый инструмент, работающий на уровне ведения журналов, например Fluentd.

Для работы с этим подробным пошаговым руководством необходимо сначала создать общую папку хранилища файлов и подключить ее по протоколу SMB к виртуальной машине Linux.

1. С помощью команды [az group create](/cli/azure/group#az_group_create) создайте группу ресурсов для хранения файлового ресурса.

    Чтобы создать группу ресурсов `myResourceGroup` в регионе "Западная часть США", воспользуйтесь следующим примером.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. С помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create) создайте учетную запись хранения для файлов.

    Чтобы создать учетную запись хранения mystorageaccount, использующую номер SKU службы хранилища Standard_LRS, воспользуйтесь следующим примером.

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Отобразите ключи учетной записи хранения.

    При создании учетной записи хранения ключи учетной записи создаются парами, поэтому их можно менять без прерывания работы службы. При смене одного из ключей в паре создается еще одна пара ключей. Так как новые ключи учетной записи хранения всегда создаются парами, у вас всегда будет по крайней мере один неиспользованный ключ учетной записи хранения, на который можно сменить текущий ключ.

    Ключи учетной записи хранения можно просмотреть, выполнив команду [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list). В следующем примере отображаются ключи для учетной записи хранения `mystorageaccount`.

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Чтобы извлечь отдельный ключ, используйте флаг `--query`. Приведенный ниже пример извлекает первый ключ (`[0]`).

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Создайте общий ресурс хранилища файлов.

    Общий ресурс хранилища файлов используется для хранения общего ресурса SMB. Создайте его, выполнив команду [az storage share create](/cli/azure/storage/share#az_storage_share_create). Квота всегда измеряется в гигабайтах (ГБ). Передайте один из ключей из предыдущей команды `az storage account keys list`. Создайте общий ресурс mystorageshare с квотой в 10 ГБ, используя приведенный ниже пример.

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Создайте каталог точек подключения.

    Создайте локальный каталог в файловой системе Linux для подключения общего ресурса SMB. Все данные, записанные в подключенный локальный каталог или считанные из него, перенаправляются в общую папку SMB, размещенную в хранилище файлов. Чтобы создать локальный каталог в /mnt/mymountdirectory, используйте приведенный ниже пример.

    ```bash
    sudo mkdir -p /mnt/mymountpoint
    ```

6. Подключите общий ресурс SMB к локальному каталогу.

    Укажите собственные имя пользователя и ключ учетной записи хранения для подключения, как показано ниже.

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountpoint -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Обеспечьте сохранение подключения SMB после перезагрузок.

    При перезагрузке виртуальной машины Linux подключенный общий ресурс SMB отключается во время завершения работы. Чтобы общий ресурс SMB повторно подключался при загрузке, добавьте строку в файл Linux /etc/fstab. Linux использует файл fstab, чтобы получить список файловых систем, которые следует подключить во время загрузки. Если добавить общий ресурс SMB, общая папка хранилища файлов станет постоянно подключенной файловой системой в виртуальной машине Linux. Общий ресурс SMB в хранилище файлов можно добавить в новую виртуальную машину, используя cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Настройка виртуальной машины Linux во время создания с помощь cloud-init](using-cloud-init.md)
- [Добавление диска к виртуальной машине Linux](add-disk.md)
- [Шифрование дисков на виртуальной машине Linux с помощью интерфейса командной строки Azure](encrypt-disks.md)
