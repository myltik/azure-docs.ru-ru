---
title: Настройка программного RAID-массива на виртуальной машине под управлением Linux | Документация Майкрософт
description: Узнайте, как использовать mdadm для настройки RAID-массива на платформе Linux в Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.openlocfilehash: d6e831692da37645e264c6674f1ba54bb16d25d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911763"
---
# <a name="configure-software-raid-on-linux"></a>Настройка программного RAID-массива в Linux
Это обычный сценарий для использования программного RAID-массива на виртуальных машинах Linux в Azure, который позволяет представить множество дисков данных в виде одного RAID-устройства. Обычно это делается для повышения производительности и обеспечения возможности увеличения пропускной способности по сравнению с использованием только одного диска.

## <a name="attaching-data-disks"></a>Присоединение дисков данных
Для настройки RAID-устройства требуется не менее двух пустых дисков данных.  Основная причина создания устройства RAID — повышение производительности операций ввода-вывода дисков.  В зависимости от потребностей ввода-вывода, можно подключить диски, которые хранятся в хранилище уровня "Стандартный" и допускают до 500 операций ввода-вывода в секунду на диск, или диски из хранилища уровня "Премиум", поддерживающие до 5000 операций ввода-вывода в секунду. В этой статье мы не останавливаемся подробно на том, как подготовить и подключить диски данных к виртуальной машине Linux.  Подробные указания по подключению пустого диска данных к виртуальной машине Linux в Azure см. в разделе [Добавление диска](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) статьи Microsoft Azure.

## <a name="install-the-mdadm-utility"></a>Установка служебной программы mdadm
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS и Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES и openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>Создание дисковых разделов
В этом примере мы создаем один дисковый раздел в /dev/sdc. Новый дисковый раздел будет назван /dev/sdc1.

1. Запустите `fdisk`, чтобы начать создание разделов:

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. Нажмите «n» в командной строке, чтобы создать новый ( **n**ew) раздел:

    ```bash
    Command (m for help): n
    ```

3. Далее нажмите «p», чтобы создать основной ( **p**rimary) раздел:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Нажмите «1», чтобы выбрать номер раздела 1.

    ```bash
    Partition number (1-4): 1
    ```

5. Выберите начальную точку нового раздела или нажмите клавишу `<enter>` , чтобы принять значения по умолчанию и поместить раздел в начале свободного пространства на диске:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Выберите размер раздела, например, введите «+10G», чтобы создать раздел размером 10 гигабайт. Или нажмите клавишу `<enter>` , чтобы создать один раздел, охватывающий весь диск:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Далее измените идентификатор и тип ( **t**ype) раздела со значения по умолчанию «83» (Linux) на fd (автоматическое RAID-устройство Linux):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Наконец, запишите таблицу разделов на диск и выйдите из программы fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Создание RAID-массива
1. В приведенном ниже примере "чередуются" (RAID уровня 0) три раздела, расположенные на трех отдельных дисках данных (sdc1, sdd1, sde1).  После выполнения этой команды будет создано устройство RAID — **/dev/md127** . Заметьте также, что если эти диски данных были ранее частью другого RAID-массива, который больше не существует, может потребоваться добавить параметр `--force` в команду `mdadm`.

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Создайте файловую систему на новом RAID-устройстве.
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE и Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    Б. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11** — включите boot.md и создайте mdadm.conf.

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > После внесения этих изменений в системах SUSE может потребоваться перезагрузка. Этот шаг *не* является обязательным для SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Добавление новой файловой системы в /etc/fstab
> [!IMPORTANT]
> Некорректное изменение файла /etc/fstab может привести к невозможности загрузить систему. Если у вас есть сомнения, см. инструкции по правильному изменению этого файла в документации дистрибутива. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

1. Создайте нужную точку монтирования для новой файловой системы, например:

    ```bash
    sudo mkdir /data
    ```
2. При изменении файла /etc/fstab следует использовать **UUID** для ссылки на файловую систему, а не имя устройства.  Чтобы определить UUID для новой файловой системы, используйте служебную программу `blkid` .

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Откройте файл /etc/fstab в текстовом редакторе и добавьте запись для новой файловой системы, например:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    **SLES 11**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Затем сохраните и закройте /etc/fstab.

4. Проверьте правильность записи в /etc/fstab:

    ```bash  
    sudo mount -a
    ```

    Если в результате выполнения команды появляется сообщение об ошибке, проверьте синтаксис в файле /etc/fstab.
   
    Теперь выполните команду `mount` для подключения файловой системы:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Необязательно) Параметры загрузки в безопасном режиме
   
    **Конфигурация fstab**
   
    Многие дистрибутивы включают в себя параметры подключения `nobootwait` или `nofail`, которые можно добавить в файл /etc/fstab. Эти параметры в случае сбоя при монтировании конкретной файловой системы позволяют системе Linux продолжить загрузку, даже если ей не удается надлежащим образом смонтировать файловую систему RAID. Дополнительные сведения об этих параметрах см. в документации по вашему дистрибутиву.
   
    Пример (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Параметры загрузки Linux**
   
    Помимо приведенных выше параметров, параметр ядра`bootdegraded=true`позволяет системе загружаться, даже если RAID-устройство воспринимается как поврежденное или с пониженной производительностью (например, если диск данных случайно удален с виртуальной машины). По умолчанию это может также привести к невозможности загрузки системы.
   
    Сведения о том, как правильно изменять параметры ядра, см. в документации по вашему дистрибутиву. Например, во многих дистрибутивах (CentOS, Oracle Linux, SLES 11) эти параметры можно вручную добавить в файл `/boot/grub/menu.lst`.  В Ubuntu этот параметр можно добавить в переменную `GRUB_CMDLINE_LINUX_DEFAULT` в файле /etc/default/grub.


## <a name="trimunmap-support"></a>Поддержка операций TRIM и UNMAP
Некоторые ядра Linux поддерживают операции TRIM и UNMAP для отмены неиспользуемых блоков на диске. Эти операции особенно удобно использовать в хранилище уровня "Стандартный", чтобы сообщать Azure о том, что удаленные страницы больше не действительны и могут быть удалены. Удаление страниц позволит сократить затраты, если вы создаете большие файлы, а затем удаляете их.

> [!NOTE]
> RAID может не выполнять команды отклонения, если значения размера блока для массива меньше заданного значения по умолчанию (512 КБ). Причина в том, что степень детализации программы UNMAP на узле также составляет 512 КБ. Если вы изменили размер блока массива с помощью параметра mdadm `--chunk=`, то ядро может игнорировать запросы операций TRIM и UNMAP.

Существует два способа включить поддержку операций TRIM в виртуальной машине Linux. Как обычно, обратитесь к документации дистрибутива, чтобы выбрать рекомендуемый метод.

- Используйте параметр подключения `discard` в `/etc/fstab`. Ниже приведен пример.

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- В некоторых случаях параметр `discard` может негативно влиять на производительность. Кроме того, вы можете вручную выполнить команду `fstrim` из командной строки или добавить ее в crontab для регулярного выполнения.

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL или CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
