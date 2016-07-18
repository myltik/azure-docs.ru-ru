Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При этом происходит удаление диска из виртуальной машины, но не из хранилища. Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

> [AZURE.NOTE] Виртуальная машина в Azure использует различные типы дисков, такие как диск с операционной системой, локальный временный диск и дополнительные диски данных. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Диск операционной системы отключить невозможно, если также не удаляется виртуальная машина.


## Поиск диска

Прежде чем отключить диск от виртуальной машины, необходимо узнать номер LUN (идентификатор диска, который необходимо отключить). Для этого выполните следующие действия:

1. 	Откройте Azure CLI и [подключитесь к подписке Azure](../articles/xplat-cli-connect.md). Убедитесь, что вы находитесь в режиме управления службами Azure (`azure config mode asm`).

2. 	Узнайте, какие диски подключены к вашей виртуальной машине, с помощью команды `azure vm disk list
	<virtual-machine-name>`.

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	Запишите номер LUN (**логический номер устройства**) диска, который необходимо отсоединить.

## Удаление ссылок операционной системы на диск

Прежде чем отключить диск от гостевой виртуальной машины Linux, необходимо определить все разделы на диске, которые не используются, и убедиться, что операционная система не будет пытаться повторно подключить их после перезагрузки. Выполнив эти инструкции, вы отмените конфигурацию, созданную при [подключении](../articles/virtual-machines-linux-classic-attach-disk.md) диска.

1. Используйте команду `lsscsi`, чтобы узнать идентификатор устройства. Вы можете установить `lsscsi` с помощью `yum install lsscsi` (в дистрибутивах на основе Red Hat) или `apt-get install lsscsi` (в дистрибутивах на основе Debian). Вы также можете найти идентификатор диска, используя приведенный выше логический номер устройства (LUN). Последний номер в кортеже в каждой строке и есть номер LUN. В следующем примере LUN 0 сопоставляется с _/dev/sdc_.

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd

2. Используйте `fdisk -l <disk>`, чтобы найти разделы, связанные с диском, который должен быть отключен.
3. 
			$ sudo fdisk -l /dev/sdc
			Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
			Units = sectors of 1 * 512 = 512 bytes
			Sector size (logical/physical): 512 bytes / 512 bytes
			I/O size (minimum/optimal): 512 bytes / 512 bytes
			Disk label type: dos
			Disk identifier: 0x5a1d2a1a

			   Device Boot      Start         End      Blocks   Id  System
			/dev/sdc1            2048  2145386495  1072692224   83  Linux

3. Отключите каждый раздел, указанный для диска. В этом примере — `$ sudo umount /dev/sdc1`.
4. Используйте `blkid`, чтобы найти UUID для всех разделов.

			$ sudo blkid
			/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
			/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
			/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
			/dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
			
5. Удалите записи из файла **/etc/fstab**, связанные с путями к устройству или идентификаторами UUID для всех разделов диска, который должен быть отключен. Записи в этом примере могут быть такими:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
или

		/dev/sdc1   /datadrive   ext4   defaults   1   2


## Отсоединить диск

Когда вы найдете номер LUN диска и удалите ссылки операционной системы, диск можно будет отключить.

1. 	Отключите выбранный диск от виртуальной машины, выполнив команду `azure vm disk detach
 	<virtual-machine-name> <LUN>`.

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	Проверить, отсоединен ли диск, можно, выполнив следующую команду:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

Отсоединенный диск остается в хранилище, но более не присоединен к виртуальной машине.

<!---HONumber=AcomDC_0706_2016-->