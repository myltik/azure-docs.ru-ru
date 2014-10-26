<properties linkid="manage-linux-howto-configure-raid" urlDisplayName="Configure RAID on Linux" pageTitle="Configure software RAID on avirtual machine running Linux in Azure" metaKeywords="raid in Azure, madadm Azure, stripe disks in Azure" description="Learn how to use mdadm to configure RAID on Linux in Azure." metaCanonical="http://www.windowsazure.com/ru-ru/manage/linux/articles/virtual-machines-linux-configure-raid" services="virtual-machines" documentationCenter="" title="" authors="szark" solutions="" writer="szark" manager="timlt" editor=""  />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark"></tags>

# Настройка программного RAID-массива в Linux

Это обычный сценарий для использования программного RAID-массива на виртуальных машинах Linux в Azure, который позволяет представить множество дисков данных в виде одного RAID-устройства. Обычно это делается для повышения производительности и обеспечения возможности увеличения пропускной способности по сравнению с использованием только одного диска.

## Присоединение дисков данных

Для настройки RAID-устройства обычно требуется не менее двух пустых дисков данных. В этой статье мы не будем подробно останавливаться на том, как присоединить диски данных к виртуальной машине Linux. Подробные инструкции по присоединению пустого диска данных к виртуальной машине Linux в Azure см. в статье Windows Azure [Присоединение диска][Присоединение диска].

> [WACOM.NOTE] На виртуальных машинах сверхмалого размера не поддерживается присоединение более одного диска данных. Подробные сведения о размерах виртуальных машин и поддерживаемом количестве дисков данных см. в разделе [Размеры виртуальных машин и облачных служб в Windows Azure][Размеры виртуальных машин и облачных служб в Windows Azure].

## Установка служебной программы mdadm

-   **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install mdadm

-   **CentOS и Oracle Linux**

        # sudo yum install mdadm

-   **SLES и openSUSE**

        # zypper install mdadm

## Создание дисковых разделов

В этом примере мы создадим один дисковый раздел в /dev/sdc. Новый дисковый раздел затем будет назван /dev/sdc1.

-   Запустите fdisk, чтобы начать создание разделов

        # sudo fdisk /dev/sdc
        Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
        Building a new DOS disklabel with disk identifier 0xa34cb70c.
        Changes will remain in memory only, until you decide to write them.
        After that, of course, the previous content won't be recoverable.

        WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                 switch off the mode (command 'c') and change display units to
                 sectors (command 'u').

-   Нажмите «n» в командной строке, чтобы создать новый (**n**ew) раздел:

        Command (m for help): n

-   Далее нажмите «p», чтобы создать основной (**p**rimary) раздел:

        Command action
            e   extended
            p   primary partition (1-4)
        p

-   Нажмите «1», чтобы выбрать номер раздела 1.

        Partition number (1-4): 1

-   Выберите начальную точку нового раздела или просто нажмите клавишу `<enter>`, чтобы принять значения по умолчанию и поместить раздел в начале свободного пространства на диске:

        First cylinder (1-1305, default 1):
        Using default value 1

-   Выберите размер раздела, например, введите «+10G», чтобы создать раздел размером 10 гигабайт. Или просто нажмите клавишу `<enter>`, чтобы создать один раздел, охватывающий весь диск.

        Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
        Using default value 1305

-   Далее измените идентификатор и тип (**t**ype) раздела со значения по умолчанию «83» (Linux) на «fd» (автоматическое RAID-устройство Linux):

        Command (m for help): t
        Selected partition 1
        Hex code (type L to list codes): fd

-   Наконец, запишите таблицу разделов на диск и выйдите из программы fdisk:

        Command (m for help): w
        The partition table has been altered!

## Создание RAID-массива

1.  В приведенном ниже примере «чередуются» (RAID уровня 0) три раздела, расположенные на трех отдельных дисках данных (sdc1, sdd1, sde1).

        # sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
          /dev/sdc1 /dev/sdd1 /dev/sde1

В примере после выполнения этой команды будет создано новое RAID-устройство с названием **/dev/md127**. Заметьте также, что если эти диски данных были ранее частью другого RAID-массива, который больше не существует, может потребоваться добавить параметр `--force` в команду `mdadm`.

1.  Создайте файловую систему на новом RAID-устройстве.

    **CentOS, Oracle Linux, openSUSE и Ubuntu**

        # sudo mkfs -t ext4 /dev/md127

    **SLES**

        # sudo mkfs -t ext3 /dev/md127

2.  **SLES и openSUSE** — включите boot.md и создайте mdadm.conf.

        # sudo -i chkconfig --add boot.md
        # sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

    > [WACOM.NOTE] После внесения этих изменений в системах SUSE может потребоваться перезагрузка.

## Добавление новой файловой системы в /etc/fstab

**Внимание!** Некорректное изменение файла /etc/fstab может привести к невозможности загрузить систему. Если не уверены, обратитесь к документации дистрибутива за сведениями о том, как правильно изменить этот файл. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

1.  Создайте нужную точку монтирования для новой файловой системы, например:

        # sudo mkdir /data

2.  При изменении файла /etc/fstab следует использовать **UUID** для ссылки на файловую систему, а не имя устройства. Чтобы определить UUID для новой файловой системы, используйте программу `blkid`.

        # sudo /sbin/blkid
        ...........
        /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3.  Откройте файл /etc/fstab в текстовом редакторе и добавьте запись для новой файловой системы, например:

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

    В **SLES и openSUSE**:

        /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

    Затем сохраните и закройте /etc/fstab.

4.  Проверьте правильность записи в /etc/fstab:

        # sudo mount -a

    Если в результате выполнения команды появляется сообщение об ошибке, проверьте синтаксис в файле /etc/fstab.

    Теперь выполните команду `mount` для монтирования файловой системы:

        # mount
        .................
        /dev/md127 on /data type ext4 (rw)

5.  Необязательные параметры

    Многие дистрибутивы включают в себя параметры монтирования `nobootwait` или `nofail`, которые можно добавить в файл /etc/fstab. Эти параметры в случае сбоя при монтировании конкретной файловой системы позволяют системе Linux продолжить загрузку, даже если ей не удается надлежащим образом смонтировать файловую систему RAID. Дополнительные сведения об этих параметрах см. в документации по вашему дистрибутиву.

    Пример (Ubuntu):

        UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

    Помимо приведенных выше параметров, параметр ядра `bootdegraded=true` позволяет системе загружаться, даже если RAID-устройство воспринимается как поврежденное или с пониженной производительностью (например, если диск данных случайно удален с виртуальной машины). По умолчанию это может также привести к невозможности загрузки системы.

    Сведения о том, как правильно изменять параметры ядра, см. в документации по вашему дистрибутиву. Например, во многих дистрибутивах (CentOS, Oracle Linux, SLES 11) эти параметры можно вручную добавить в файл `/boot/grub/menu.lst`. В Ubuntu этот параметр можно добавить в переменную `GRUB_CMDLINE_LINUX_DEFAULT` в файле /etc/default/grub.

  [Присоединение диска]: http://www.windowsazure.com/ru-ru/documentation/articles/storage-windows-attach-disk/#attachempty
  [Размеры виртуальных машин и облачных служб в Windows Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn197896.aspx
