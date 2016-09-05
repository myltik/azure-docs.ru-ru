<properties
	pageTitle="Добавление диска к виртуальной машине Linux | Microsoft Azure"
	description="Узнайте, как добавить постоянный диск к виртуальной машине Linux."
	keywords="виртуальная машина Linux, добавление диска ресурсов"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.topic="article"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.date="04/29/2016"
	ms.author="rclaus"/>

# Добавление диска к виртуальной машине Linux

Из этой статьи вы узнаете, как добавить в виртуальную машину постоянный диск, на котором можно хранить данные. Эти данные сохранятся даже после повторной подготовки виртуальной машины (например, в ходе обслуживания или изменения размера). Для добавления диска вам потребуется использовать [интерфейс командной строки Azure](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

## Быстрые команды

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## Добавление диска

Присоединение нового диска не займет много времени. Чтобы создать и присоединить новый диск (ГБ) для своей виртуальной машины, введите `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`. Если учетная запись хранения не определяется явным образом, любой создаваемый диск помещается в ту же учетную запись хранения, что и диск операционной системы. Это выглядит примерно так:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Выходные данные

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## Подключение к виртуальной машине Linux для подключения нового диска

> [AZURE.NOTE] В этом разделе для подключения к виртуальной машине используются имена пользователей и пароли. Чтобы использовать пары открытых и закрытых ключей для взаимодействия с виртуальной машиной, см. статью [Использование SSH с Linux в Azure](virtual-machines-linux-mac-create-ssh-keys.md). Вы можете изменить подключение **SSH** виртуальных машин, созданных с помощью команды `azure vm quick-create`, используя команду `azure vm reset-access` для полного сброса доступа **SSH**, добавления или удаления пользователей или добавления файлов открытого ключа для обеспечения безопасного доступа.

Чтобы разбить диск на разделы, отформатировать и подключить его к виртуальной машине Linux, к ВМ Azure нужно подключиться по протоколу SSH. Если вы не знакомы с подключением по протоколу **ssh**, команда имеет вид `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`. В конечном итоге она выглядит вот так:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Выходные данные

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

После того как вы подключились к своей виртуальной машине, вы можете присоединить диск. Сначала найдите диск, используя `dmesg | grep SCSI` (для обнаружения нового диска можно использовать и другой способ). В этом случае отобразится примерно следующее:

```bash
dmesg | grep SCSI
```

Выходные данные

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

а в контексте этого раздела диск `sdc` является именно тем диском, который мы ищем. Теперь разобьем диск с помощью `sudo fdisk /dev/sdc`, предположив, что в нашем случае был диск `sdc`, установим его как основной диск в разделе 1 и примем остальные значения по умолчанию.

```bash
sudo fdisk /dev/sdc
```

Выходные данные

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

Создайте раздел, введя в командной строке `p`:

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

Запишите файловую систему на раздел с помощью команды **mkfs**, указав тип вашей файловой системы и имя устройства. В этом разделе мы будем использовать указанные `ext4` и `/dev/sdc1`:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Выходные данные

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

Теперь создадим каталог для подключения файловой системы, используя `mkdir`:

```bash
sudo mkdir /datadrive
```

А для подключения каталога используйте `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Теперь диск данных готов для использования как `/datadrive`.

```bash
ls
```

Выходные данные

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Чтобы обеспечить автоматическое повторное подключение диска после перезагрузки, его необходимо добавить в файл /etc/fstab. Кроме того, для ссылки на диск настоятельно рекомендуется использовать в файле /etc/fstab идентификатор UUID (глобальный уникальный идентификатор), а не просто имя устройства (например, `/dev/sdc1`). Если операционная система обнаруживает ошибку диска во время загрузки, использование UUID позволяет избежать подключения ошибочного диска в это расположение. Остальные диски с данными затем получают те же идентификаторы устройств. Чтобы найти UUID нового диска, используйте программу **blkid**:

```bash
sudo -i blkid
```

Результат будет выглядеть примерно так:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Некорректное изменение файла **/etc/fstab** может привести к невозможности загрузить систему. Если у вас есть сомнения, см. инструкции по правильному изменению этого файла в документации дистрибутива. Также рекомендуется перед внесением изменений создать резервную копию файла /etc/fstab.

Затем откройте файл **/etc/fstab** в текстовом редакторе:

```bash
sudo vi /etc/fstab
```

В этом примере мы используем значение UUID для нового устройства **/dev/sdc1**, созданного на предыдущих шагах, и точку подключения **/datadrive**. Добавьте следующую строку в конец файла **/etc/fstab**:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
```

>[AZURE.NOTE] Если вы позднее удалите диск данных без редактирования файла fstab, виртуальная машина может не загрузиться. Большинство дистрибутивов содержат `nofail` или `nobootwait` параметры fstab. Эти параметры позволяют системе загружаться, даже если диск не подключится во время загрузки. Дополнительные сведения об этих параметрах см. в документации дистрибутива.


### Поддержка операций TRIM и UNMAP для Linux в Azure
Некоторые ядра Linux поддерживают операции TRIM и UNMAP для отмены неиспользуемых блоков на диске. Это особенно удобно использовать в хранилище уровня "Стандартный", чтобы сообщать Azure о том, что удаленные страницы больше не действительны и могут быть удалены. Это позволит сократить затраты, если вы создаете большие файлы, а затем удаляете их.

Существует два способа включить поддержку операций TRIM в виртуальной машине Linux. Как обычно, обратитесь к документации дистрибутива, чтобы выбрать рекомендуемый метод.

- Используйте параметр подключения `discard` в `/etc/fstab`. Ниже приведен пример.

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Кроме того, вы можете вручную выполнить команду `fstrim` из командной строки или добавить ее в crontab для регулярного выполнения.

	**Ubuntu**

		# sudo apt-get install util-linux
		# sudo fstrim /datadrive

	**RHEL или CentOS**

		# sudo yum install util-linux
		# sudo fstrim /datadrive

## Устранение неполадок
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## Дальнейшие действия

- Помните, для того чтобы после перезапуска виртуальная машина получила доступ к новому диску, информацию о нем необходимо прописать в файле [fstab](http://en.wikipedia.org/wiki/Fstab).
- Ознакомьтесь с рекомендациями по [оптимизации производительности виртуальной машины Linux](virtual-machines-linux-optimization.md), чтобы правильно настроить виртуальную машину Linux.
- Увеличьте емкость хранилища, добавив дополнительные диски, и [настройте RAID](virtual-machines-linux-configure-raid.md) для повышения производительности.

<!---HONumber=AcomDC_0824_2016-->