
Дополнительную информацию о дисках см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин](../articles/virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Практическое руководство. Присоединение пустого диска
Подключение пустого диска — это более простой способ добавления диска данных, так как Azure автоматически создает VHD-файл и сохраняет его в учетной записи хранения.

1.  Откройте Azure CLI для Windows, Linux и Mac и подключитесь к подписке Azure. Дополнительные сведения см. в разделе [Подключение к Azure из Azure CLI](../articles/xplat-cli-connect.md).

2.  Убедитесь, что используется режим управления службами Azure (режим по умолчанию), введя команду `azure config
 	mode asm`.

3.  Создайте и подключите новый диск с помощью команды `azure vm disk attach-new`, как показано ниже. Обратите внимание, что имя _ubuntuVMasm_ будет заменено именем виртуальной машины Linux, созданной в подписке. В этом примере число 30 — это размер диска в ГБ.

        azure vm disk attach-new ubuntuVMasm 30

4.	После создания и присоединения диска с данными он отображается в результатах команды `azure vm disk list
    <virtual-machine-name>`:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Практическое руководство. Присоединение существующего диска

Для подключения существующего диска требуется VHD-файл в учетной записи хранения.

1. 	Откройте Azure CLI для Windows, Linux и Mac и подключитесь к подписке Azure. Дополнительные сведения см. в разделе [Подключение к Azure из Azure CLI](../articles/xplat-cli-connect.md).

2.  Убедитесь, что используется режим управления службами Azure (режим по умолчанию). Если вы перешли в режим управления ресурсами, вернитесь, введя команду `azure config mode asm`.

3.	Узнайте, не добавлен ли уже нужный виртуальный жесткий диск в подписку Azure, выполнив следующую команду:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  Если вам не удается найти нужный диск, вы можете добавить его в подписку с помощью команды `azure vm disk create` или `azure vm disk upload`. Например:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	С помощью команды `azure vm disk upload` вы также можете добавить виртуальный жесткий диск в конкретную учетную запись хранения. Дополнительные сведения о командах для управления дисками с данными виртуальных машин Azure см. [здесь](../virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

5.  Введите следующую команду, чтобы присоединить нужный добавленный виртуальный жесткий диск к виртуальной машине:

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Обязательно замените _ubuntuVMasm_ именем своей виртуальной машины, а _myTestVhd_ — именем своего виртуального жесткого диска.

6.	Проверить, присоединен ли диск к виртуальной машине, можно с помощью команды `azure vm disk list
 	<virtual-machine-name>`, например:

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


> [AZURE.NOTE]После добавления диска данных необходимо войти в систему на виртуальной машине и инициализировать диск, чтобы виртуальная машина могла использовать его для хранения данных.

<!---HONumber=Oct15_HO3-->