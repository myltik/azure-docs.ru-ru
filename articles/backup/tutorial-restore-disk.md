---
title: Восстановление диска виртуальной машины с помощью службы архивации Azure | Документы Майкрософт
description: Дополнительные сведения о восстановлении диска и создании восстановленной виртуальной машины в Azure с помощью служб архивации и восстановления.
services: backup
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: ''
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/17/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f2b82725362517d12dd4e7df7b2bb083fa107253
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Восстановление диска и создание восстановленной виртуальной машины в Azure
Служба архивации Azure создает точки восстановления, которые хранятся в геоизбыточных хранилищах восстановления. Используя точку восстановления, можно восстановить всю виртуальную машину или только отдельные файлы. В этой статье описан процесс восстановления всей виртуальной машины с помощью CLI. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Составление списка и выбор точек восстановления
> * Восстановление диска из точки восстановления
> * Создание виртуальной машины на основе восстановленного диска

Сведения об использовании PowerShell для восстановления диска и создания восстановленной виртуальной машины см. в разделе о [резервном копировании и восстановление виртуальных машин Azure с помощью PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.18 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>предварительным требованиям
Для выполнения этого руководства требуется виртуальная машина Linux, защищенная с помощью службы архивации Azure. Для имитации случайного удаления виртуальной машины и процесса восстановления виртуальная машина создается на основе диска в точке восстановления. Если вам требуется виртуальная машина Linux, защищенная с помощью службы архивации Azure, см. раздел [Резервное копирование виртуальной машины в Azure с помощью интерфейса командной строки](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Обзор службы архивации
Когда система Azure запускает архивацию, расширение резервного копирования на виртуальной машине делает моментальный снимок на момент времени. Расширение резервного копирования устанавливается на виртуальной машине при запросе первой резервной копии. Если на момент архивации виртуальная машина не запущена, служба архивации Azure также может создавать моментальный снимок базового хранилища.

По умолчанию служба архивации Azure создает резервную копию, согласованную с файловой системой. После создания моментального снимка службой архивации Azure данные передаются в хранилище служб восстановления. Для повышения эффективности служба архивации Azure анализирует блоки данных и передает только те из них, которые были изменены с момента предыдущего резервного копирования.

После передачи данных служба удаляет моментальный снимок и создает точку восстановления.


## <a name="list-available-recovery-points"></a>Список доступных точек восстановления
Чтобы восстановить диск, выберите точку восстановления в качестве источника данных восстановления. Поскольку политика по умолчанию создает точку восстановления каждый день и хранит все точки в течение 30 дней, вы можете сохранить набор точек восстановления, а затем выбрать определенный момент времени для восстановления. 

Чтобы просмотреть список доступных точек восстановления, используйте команду [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). **Имя** точки восстановления используется для восстановления дисков. В рамках этого руководства нам требуется последняя доступная точка восстановления. Параметр `--query [0].name` выбирает имя самой последней точки восстановления следующим образом:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Восстановление диска виртуальной машины
Чтобы восстановить диск из точки восстановления, сначала необходимо создать учетную запись хранения Azure. Она используется для хранения восстановленного диска. Затем этот восстановленный диск используется для создания виртуальной машины.

1. Создайте учетную запись хранения с помощью команды [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). Имя учетной записи хранения следует указывать в нижнем регистре, оно должно быть глобально уникальным. Замените *mystorageaccount* собственным уникальным именем:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Восстановите диск из точки восстановления с помощью команды [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Замените *mystorageaccount* именем учетной записи хранения, созданной с помощью предыдущей команды. Замените *myRecoveryPointName* именем точки восстановления, полученной в выходных данных предыдущей команды [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Мониторинг задания восстановления
Чтобы отслеживать состояние задания восстановления, используйте команду [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

В результате вы получите приблизительно следующие выходные данные. Они указывают на то, что задание восстановления *выполняется*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Когда *состояние* задания восстановления изменится на *Завершено*, это будет означать, что диск восстановлен в учетной записи хранения.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Преобразование восстановленного диска в управляемый диск
Задание восстановления создает неуправляемый диск. Чтобы создать виртуальную машину на основе диска, его сначала необходимо преобразовать в управляемый диск.

1. Получите сведения о подключении для вашей учетной записи хранения с помощью команды [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Замените *mystorageaccount* именем своей учетной записи хранения следующим образом:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Неуправляемый диск защищен в учетной записи хранения. Следующая команда получает сведения о неуправляемом диске и создает переменную с именем *uri*, которая используется в следующем шаге при создании управляемого диска.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Теперь на основе восстановленного диска можно создать управляемый диск с помощью команды [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). Переменная *uri* из предыдущего шага используется как источник для управляемого диска.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Создав управляемый диск на основе восстановленного диска, выполните очистку неуправляемого диска и учетной записи хранения с помощью команды [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Замените *mystorageaccount* именем своей учетной записи хранения следующим образом:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Создание виртуальной машины на основе восстановленного диска
Последним шагом является создание виртуальной машины на основе управляемого диска.

1. Создайте виртуальную машину на основе управляемого диска с помощью команды [az vm create](/cli/azure/vm?view=azure-cli-latest#az_vm_create) следующим образом:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Чтобы убедиться, что виртуальная машина была создана на основе восстановленного диска, перечислите виртуальные машины в группе ресурсов с помощью команды [az vm list](/cli/azure/vm?view=azure-cli-latest#az_vm_list) следующим образом:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Дополнительная информация
В этом руководстве описывается восстановление диска из точки восстановления, а затем создание виртуальной машины на его основе. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Составление списка и выбор точек восстановления
> * Восстановление диска из точки восстановления
> * Создание виртуальной машины на основе восстановленного диска

Перейдите к следующему руководству, чтобы узнать о восстановлении отдельных файлов из точки восстановления.

> [!div class="nextstepaction"]
> [Восстановление файлов на виртуальной машине в Azure](tutorial-restore-files.md)

