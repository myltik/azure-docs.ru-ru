
Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища. Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

> [AZURE.NOTE]Виртуальные машины в Azure используют различные типы дисков, такие как диск с операционной системой, локальный временный диск и дополнительные диски данных. Диски данных рекомендуется использовать в качестве средства хранения данных для виртуальных машин. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](../../virtual-machines-disks-vhds.md). Диск операционной системы отключить невозможно, если также не удаляется виртуальная машина.

## Поиск диска

Прежде чем отсоединить диск от виртуальной машины, необходимо узнать номер LUN (идентификатор диска, который необходимо отсоединить). Для этого выполните следующие действия:

1. 	Откройте Azure CLI для Windows, Linux и Mac и подключитесь к подписке Azure. Дополнительные сведения см. в разделе [Подключение к Azure из Azure CLI](../articles/xplat-cli-connect.md).

2.  Убедитесь, что используется режим управления службами Azure (режим по умолчанию), введя команду `azure config
 	mode asm`.

3. 	Узнайте, какие диски присоединены к виртуальной машине, с помощью команды `azure vm disk list
	<virtual-machine-name>`:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

4. 	Запишите номер LUN (**логический номер устройства**) диска, который необходимо отсоединить.


## Отсоединить диск

Найдя номер LUN диска, его можно отсоединить.

1. 	Отсоедините выбранный диск от виртуальной машины, выполнив команду `azure vm disk detach
 	<virtual-machine-name> <LUN>`:

		$azure vm disk detach ubuntuVMasm 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	Проверить, отсоединен ли диск, можно, выполнив следующую команду:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

Отсоединенный диск остается в хранилище, но более не присоединен к виртуальной машине.

<!---HONumber=August15_HO7-->