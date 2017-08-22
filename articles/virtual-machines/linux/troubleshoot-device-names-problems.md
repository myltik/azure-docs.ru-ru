---
title: "Изменение имен устройств виртуальных машин Linux в Azure | Документация Майкрософт"
description: "В этом руководстве объясняется, почему изменяются имена устройств и предоставляются решения этой проблемы."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>Устранение неполадок. Изменение имен устройств виртуальных машин Linux

В этом руководстве описывается, почему изменяются имена устройств после перезапуска виртуальной машины Linux или после повторного подключения дисков. В нем также предоставлены решения этой проблемы.

## <a name="symptom"></a>Симптом

При выполнении виртуальных машин Linux в Microsoft Azure могут возникнуть следующие проблемы.

- После перезапуска виртуальная машина не загружается.

- Если диски с данными отсоединить и присоединить повторно, имена дисков изменяются.

- Приложение или скрипт, который ссылается на диск с использованием имени устройства, не работает. Вы обнаружите, что имя диска изменилось.

## <a name="cause"></a>Причина:

Пути устройств в Linux не обязательно будут согласованы после перезагрузок. Имена устройств состоят из основного (буква) и дополнительного номеров.  Если драйвер устройства хранения Linux обнаруживает новое устройство, назначается основной и дополнительный номера из доступного диапазона. При удалении устройства номера устройства освобождаются для последующего использования.

Проблема возникает, так как сканирование устройств в Linux, назначаемое подсистемой SCSI, происходит асинхронно. Окончательное именование пути устройства может различаться после перезагрузок. 

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, используйте постоянное именование. Существует четыре метода постоянного именования — метки файловой системы, UUID, идентификатор и путь. Для виртуальных машин Linux в Azure рекомендуется использовать методы меток файловой системы и UUID. 

Большинство дистрибутивов также поддерживает параметры fstab **nofail** или **nobootwait**. Эти параметры позволяют системе загружаться, даже если диск не подключится во время загрузки. Дополнительные сведения об этих параметрах см. в документации дистрибутива. Дополнительные сведения о настройке виртуальных машин Linux для использования UUID см. в разделе [Подключение к виртуальной машине Linux для подключения нового диска](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

После установки агента Azure Linux на виртуальной машине он использует правила Udev для формирования набора символических ссылок в каталоге **/dev/disk/azure**. Эти правила Udev могут использоваться приложениями и скриптами для идентификации дисков, подключенных к виртуальной машине, а также их типа и логического номера устройства (LUN).

## <a name="more-information"></a>Дополнительные сведения

### <a name="identify-disk-luns"></a>Определение LUN диска

Приложение может использовать LUN, чтобы найти все присоединенные диски и создать символические ссылки. Агент Azure Linux содержит правила udev, с помощью которых настраиваются символические ссылки из LUN на устройства.

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

Также сведения о LUN можно получить с помощью гостевой виртуальной машины Linux, используя lsscsi или аналогичное средство следующим образом.

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Эти сведения о LUN гостевой машины можно использовать с метаданными подписки Azure, чтобы определить расположение виртуального жесткого диска, на котором хранятся данные раздела, в хранилище Azure. Например, используйте командную строку Azure:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Обнаружение UUID файловой системы с помощью служебной программы blkid

Выходные данные blkid или аналогичные источники данных можно прочитать с помощью скрипта либо приложения, чтобы сформировать символические ссылки в каталоге **/dev**. Выходные данные будут показывать UUID всех дисков, подключенных к виртуальной машине, и файл устройства, с которым они связаны:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Правила udev waagent формируют набор символических ссылок в каталоге **/dev/disk/azure**:


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


Приложение может использовать эти данные для идентификации загрузочного диска устройства и диска ресурсов (временного). В Azure для обнаружения этих разделов приложения должны ссылаться на **/dev/disk/azure/root-part1** или **/dev/disk/azure-resource-part1**.

Если в списке blkid имеются дополнительные разделы, они находятся на диске данных. Приложения могут обрабатывать UUID для этих разделов и использовать путь, как например ниже, чтобы получить имя устройства в среде выполнения:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Получение последних правил хранилища Azure

Чтобы получить последние правила хранилища Azure, выполните следующие команды:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


Дополнительные сведения см. в следующих статьях:

- [Для Ubuntu: UsingUUID](https://help.ubuntu.com/community/UsingUUID) (Использование UUID)

- [Для Red Hat: Persistent Naming](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html) (Постоянные именования)

- [Для Linux: What UUIDs can do for you](https://www.linux.com/news/what-uuids-can-do-you) (Чем полезны идентификаторы UUID)

- [Udev: Introduction to Device Management In Modern Linux System](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) (Udev. Руководство по управлению устройствами в современной системе Linux)


