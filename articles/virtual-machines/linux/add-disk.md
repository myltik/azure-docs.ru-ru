---
title: Добавление диска в виртуальную машину Linux с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как добавить постоянный диск в виртуальную машину Linux с помощью Azure CLI 1.0 и Azure CLI 2.0.
keywords: виртуальная машина Linux, добавление диска ресурсов
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3d3e3468b491f366473899f5d073704ea9a95ea
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="add-a-disk-to-a-linux-vm"></a>Добавление диска к виртуальной машине Linux
Из этой статьи вы узнаете, как добавить в виртуальную машину постоянный диск, на котором можно хранить данные. Эти данные сохранятся даже после повторной подготовки виртуальной машины (например, в ходе обслуживания или изменения размера). 


## <a name="use-managed-disks"></a>Использование управляемых дисков
Компонент "Управляемые диски" Azure упрощает управление дисками виртуальных машин Azure. Он управляет учетными записями хранения, связанными с этими дисками. Вам нужно лишь выбрать тип ("Премиум" или "Стандартный") и размер диска, а Azure самостоятельно создаст диск и будет управлять им. Дополнительные сведения см. в разделе [Обзор управляемых дисков Azure](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Подключение нового диска к виртуальной машине
Если вам просто нужен новый диск в виртуальной машине, то используйте команду [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) с параметром `--new`. Если виртуальная машина находится в зоне доступности, то диск автоматически создается в одной зоне с виртуальной машиной. Дополнительные сведения см. в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](../../availability-zones/az-overview.md). В следующем примере создается диск с именем *myDataDisk* размером *50* ГБ:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Подключение существующего диска 
Во многих случаях подключаются созданные диски. Чтобы подключить существующий диск, найдите идентификатор диска и укажите его в команде [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach). В следующем примере выполняется запрос диска *myDataDisk* в группе ресурсов *myResourceGroup*, а затем этот диск подключается к виртуальной машине *myVM*:

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

Результат выглядит примерно следующим образом (для форматирования результатов можно использовать параметр `-o table` для любой команды):

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Использование неуправляемых дисков
Для неуправляемых дисков требуется дополнительная нагрузка, чтобы создать базовые учетные записи хранения и управлять ими. Неуправляемые диски создаются в той же учетной записи хранения, что и диск ОС. Чтобы создать и подключить неуправляемый диск, используйте команду [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach). В следующем примере выполняется подключение неуправляемого диска объемом *50* ГБ к виртуальной машине *myVM*, входящей в группу ресурсов *myResourceGroup*:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Подключение к виртуальной машине Linux для подключения нового диска
Чтобы разбить диск на разделы, отформатировать и подключить новый диск к виртуальной машине Linux, подключитесь к виртуальной машине Azure по протоколу SSH. Дополнительные сведения см. в статье [Как использовать SSH с Linux в Azure](mac-create-ssh-keys.md). В следующем примере выполняется подключение к виртуальной машине с помощью общедоступной записи DNS *mypublicdns.westus.cloudapp.azure.com* и имени пользователя *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

После подключения к виртуальной машине можно подключить диск. Сначала найдите диск, используя `dmesg` (для обнаружения нового диска можно использовать и другой способ). В следующем примере используется команда dmesg для фильтрации по дискам *SCSI*:

```bash
dmesg | grep SCSI
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

В данном примере *sdc* — это диск, который нам нужен. Разобьем диск с помощью `fdisk`, установим его как основной диск в разделе 1 и примем остальные значения по умолчанию. В следующем примере запускается процесс `fdisk` в */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Создайте раздел, введя в командной строке `p` следующий текст:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Теперь запишите файловую систему в раздел, выполнив команду `mkfs`. Укажите тип файловой системы и имя устройства. В следующем примере создается файловая система *ext4* в разделе */dev/sdc1*, созданном на предыдущем шаге:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Теперь создайте каталог для подключения файловой системы, используя `mkdir`. В следующем примере создается каталог в */datadrive*:

```bash
sudo mkdir /datadrive
```

Используйте `mount`, чтобы затем подключить файловую систему. В следующем примере раздел */dev/sdc1* подключается к точке подключения */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Чтобы обеспечить автоматическое повторное подключение диска после перезагрузки, его необходимо добавить в файл */etc/fstab*. Также для ссылки на диск настоятельно рекомендуется использовать в файле */etc/fstab* идентификатор UUID (глобальный уникальный идентификатор), а не просто имя устройства (например, */dev/sdc1*). Если операционная система обнаруживает ошибку диска во время загрузки, использование UUID позволяет избежать подключения ошибочного диска в это расположение. Остальные диски с данными затем получают те же идентификаторы устройств. Чтобы найти UUID нового диска, используйте служебную программу `blkid`:

```bash
sudo -i blkid
```

Результат должен быть аналогичным приведенному ниже:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Некорректное изменение файла **/etc/fstab** может привести к невозможности загрузить систему. Если у вас есть сомнения, см. инструкции по правильному изменению этого файла в документации дистрибутива. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

Затем откройте файл */etc/fstab* в текстовом редакторе, как показано ниже:

```bash
sudo vi /etc/fstab
```

В этом примере мы используем значение UUID для устройства */dev/sdc1*, созданного на предыдущих шагах, и точку подключения */datadrive*. Добавьте следующую строку в конец файла */etc/fstab* :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Если вы позднее удалите диск данных без редактирования файла fstab, виртуальная машина может не загрузиться. Большинство дистрибутивов поддерживает параметры fstab *nofail* и (или) *nobootwait*. Эти параметры позволяют системе загружаться, даже если диск не подключится во время загрузки. Дополнительные сведения об этих параметрах см. в документации дистрибутива.
> 
> Параметр *nofail* обеспечивает запуск виртуальной машины даже в том случае, если файловая система повреждена или отсутствует диск во время загрузки. Без этого параметра может возникнуть ситуация, описанная в записи блога [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Не удается подключиться к виртуальной машине Linux по протоколу SSH из-за ошибок FSTAB).

### <a name="trimunmap-support-for-linux-in-azure"></a>Поддержка операций TRIM и UNMAP для Linux в Azure
Некоторые ядра Linux поддерживают операции TRIM и UNMAP для отмены неиспользуемых блоков на диске. Эту функцию особенно удобно использовать в хранилище уровня "Стандартный", чтобы сообщать Azure о том, что удаленные страницы больше не действительны и могут быть удалены. Это позволяет сократить затраты, если вы создаете большие файлы, а затем удаляете их.

Существует два способа включить поддержку операций TRIM в виртуальной машине Linux. Как обычно, обратитесь к документации дистрибутива, чтобы выбрать рекомендуемый метод.

* Используйте параметр подключения `discard` в */etc/fstab*. Ниже приведен пример.

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* В некоторых случаях параметр `discard` может негативно влиять на производительность. Кроме того, вы можете вручную выполнить команду `fstrim` из командной строки или добавить ее в crontab для регулярного выполнения.
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL или CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Устранение неполадок
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Дополнительная информация
* Помните, для того чтобы после перезапуска виртуальная машина получила доступ к новому диску, информацию о нем необходимо прописать в файле [fstab](http://en.wikipedia.org/wiki/Fstab) .
* Ознакомьтесь с рекомендациями по [оптимизации производительности виртуальной машины Linux](optimization.md) , чтобы правильно настроить виртуальную машину Linux.
* Увеличьте емкость хранилища, добавив дополнительные диски, и [настройте RAID](configure-raid.md) для повышения производительности.

