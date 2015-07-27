<properties
	pageTitle="Создание виртуальной машины под управлением Linux в Azure"
	description="Узнайте, как создать виртуальную машину Azure с ОС Linux с помощью образа из Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Создание виртуальной машины с ОС Linux

> [AZURE.SELECTOR]
- [Azure Portal](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

Вы можете легко создать виртуальную машину (VM) Azure, работающую под управлением ОС Linux, с помощью командной строки или из портала. В этом учебнике показано, как использовать интерфейс командной строки Azure для Mac, Linux и Windows (CLI Azure), чтобы быстро создать виртуальную машину Ubuntu Server в Azure, подключиться к ней с помощью **ssh**, а также создать и подключить новый диск. (В этом разделе описывается виртуальная машина Ubuntu Server, но вы можете также создавать виртуальные машины Linux, используя [собственные образы в качестве шаблонов](virtual-machines-linux-create-upload-vhd.md).)

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Установка Azure CLI

Первым делом [установите Azure CLI](../xplat-cli-install.md).

Хорошо. Теперь убедитесь, что вы находитесь в режиме управления ресурсами, введя `azure config mode arm`.

Отлично. Теперь войдите с использованием своего рабочего идентификатора, введя `azure login`, и следуйте подсказкам.

> [AZURE.NOTE]Если вы увидите ошибку входа в систему, может потребоваться [создать рабочий идентификатор из личной учетной записи Майкрософт](resource-group-create-work-id-from-personal.md).

## Создание виртуальной машины Azure

Введите `azure group create <my-group-name> westus`, где вместо _&lt;my-group-name&gt;_ введите уникальное для вас имя группы (при необходимости можно указать другой регион). Вы должны увидеть нечто вроде этого:

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Теперь создайте свою виртуальную машину, введя `azure vm quick-create`, и следуйте появляющимся подсказкам для ввода остальных параметров. Используйте имя только что созданной группы ресурсов, а в качестве значения **ImageURN** используйте `canonical:ubuntuserver:14.04.2-LTS:latest`. В итоге вы должны получить примерно следующее:

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_D1
	data:
	data:    Storage Profile:
	data:      Image reference:
	data:        Publisher                   :canonical
	data:        Offer                       :ubuntuserver
	data:        Sku                         :14.04.2-LTS
	data:        Version                     :latest
	data:
	data:      OS Disk:
	data:        OSType                      :Linux
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

Ваша виртуальная машина запущена и готова к подключению.

## Подключение к виртуальной машине

Виртуальные машины Linux обычно подключаются с помощью протокола **ssh**. В этом разделе для подключения к виртуальной машине используются имена пользователей и пароли; чтобы использовать пары открытых и закрытых ключей для взаимодействия со своей виртуальной машиной, см. [Использование SSH с Linux в Azure](virtual-machines-linux-use-ssh-key.md).

Если вы еще не знакомы с подключением с помощью **ssh**, команда имеет вид `ssh <username>@<publicdnsaddress> -p <the ssh port>`. В этом случае используются имя пользователя и пароль, которые использовались на предыдущем шаге, и порт 22, который по умолчанию является портом **ssh**.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
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

После того как вы подключились к своей виртуальной машине, вы можете присоединить диск.

## Присоединение и подключение диска

Присоединение нового диска не займет много времени. Чтобы создать и присоединить новый диск (ГБ) для своей виртуальной машины, просто введите `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`. Должно отобразиться примерно следующее:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


Теперь найдите диск, используя `dmesg | grep SCSI` (для поиска нового диска можно использовать другой способ). В этом случае отобразится примерно следующее:

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

а в контексте этого раздела диск `sdc` является именно тем диском, который мы ищем. Теперь разобьем диск с помощью `sudo fdisk /dev/sdc`, предположив, что в нашем случае был диск `sdc`, установим его как основной диск в разделе 1 и примем остальные значения по умолчанию.

	sudo fdisk /dev/sdc
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

Создайте раздел, введя в командной строке `p`:

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

Запишите файловую систему на раздел с помощью команды **mkfs**, указав тип вашей файловой системы и имя устройства. В этом разделе мы будем использовать указанные `ext4` и `/dev/sdc1`:

	sudo mkfs -t ext4 /dev/sdc1
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

Теперь создадим каталог для подключения файловой системы, используя `mkdir`:

	sudo mkdir /datadrive

А для подключения каталога используйте `mount`:

	sudo mount /dev/sdc1 /datadrive

Теперь диск данных готов для использования как `/datadrive`.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]Вы также можете подключиться к виртуальной машине Linux, использовав ключ SSH для идентификации. Дополнительную информацию см. в разделе [Использование SSH с Linux в Azure](virtual-machines-linux-use-ssh-key.md).

## Дальнейшие действия

Помните, для того чтобы после перезапуска виртуальная машина получила доступ к вашему новому диску, информацию о нем необходимо прописать в файле [fstab](http://en.wikipedia.org/wiki/Fstab).

Чтобы узнать больше о Linux в Azure, см. следующие статьи:

- [Linux и вычисления с открытым кодом в Azure](virtual-machines-linux-opensource.md)

- [Использование интерфейса командной строки Azure](../virtual-machines-command-line-tools.md)

- [Развертывание приложения LAMP с помощью расширения Azure CustomScript для Linux](virtual-machines-linux-script-lamp.md)

- [О параметрах конфигурации виртуальной машины Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Расширение виртуальных машин Docker для Linux в Azure](virtual-machines-docker-vm-extension.md)
 

<!---HONumber=July15_HO3-->