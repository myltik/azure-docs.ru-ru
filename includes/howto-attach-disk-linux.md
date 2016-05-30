
Дополнительную информацию о дисках см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>
## Подключение пустого диска

1.  Откройте Azure CLI и [подключитесь к подписке Azure](../articles/xplat-cli-connect.md). Убедитесь, что вы находитесь в режиме управления службами Azure (`azure config mode asm`).

2.  Введите `azure vm disk attach-new`, чтобы создать и подключить новый диск, как показано в примере ниже. Замените имя _TestVM_ именем своей виртуальной машины Linux и укажите размер диска в гигабайтах. В нашем примере это 100 ГБ:

        azure vm disk attach-new TestVM 100

3.	Созданный и присоединенный диск с данными отображается в выводе команды `azure vm disk list <virtual-machine-name>`, как показано ниже:

        $ azure vm disk list TestVM
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        TestVM-2645b8030676c8f8.vhd  Linux
        data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Подключение существующего диска

Для подключения существующего диска требуется VHD-файл в учетной записи хранения.

1. 	Откройте Azure CLI и [подключитесь к подписке Azure](../articles/xplat-cli-connect.md). Убедитесь, что вы находитесь в режиме управления службами Azure (`azure config mode asm`).

2.	Проверьте, не добавлен ли уже виртуальный жесткий диск, который вы хотите подключить, в подписку Azure.

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
    	data:    TestVM-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  Если вам не удается найти нужный диск, вы можете добавить его в подписку с помощью команды `azure vm disk create` или `azure vm disk upload`. Пример `disk create` показан ниже:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	С помощью команды `azure vm disk upload` вы также можете передать виртуальный жесткий диск в конкретную учетную запись хранения. Дополнительные сведения о командах для управления дисками с данными виртуальных машин Azure см. [здесь](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

4.  Теперь подключите нужный виртуальный жесткий диск к виртуальной машине.

		$azure vm disk attach TestVM myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Обязательно замените _TestVM_ именем своей виртуальной машины, а _myTestVhd_ — именем своего виртуального жесткого диска.

5.	Чтобы проверить, подключен ли диск к виртуальной машине, выполните команду `azure vm disk list <virtual-machine-name>`.

		$azure vm disk list TestVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        TestVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
После добавления диска данных необходимо войти в систему на виртуальной машине и инициализировать диск, чтобы виртуальная машина могла использовать его для хранения данных. Информацию о том, как это сделать, см. в инструкциях ниже.
