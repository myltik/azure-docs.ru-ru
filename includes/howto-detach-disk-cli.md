
#Отключение диска данных от виртуальной машины с помощью CLI

Когда диск данных, подключенный к виртуальной машине, больше не нужен, его можно легко отключить. При данной операции происходит удаление диска из виртуальной машины, но не из хранилища. Если вы хотите снова использовать существующие данные на диске, его можно легко повторно подключить как к той же самой, так и к другой виртуальной машине.

> [AZURE.NOTE]Виртуальная машина в Azure использует различные типы дисков: диск с операционной системой, временный локальный диск и дополнительные диски с данными. Диски данных рекомендуется использовать в качестве средства хранения данных для виртуальных машин. Более подробная информация о дисках приводится в разделе [О дисках и образах](http://go.microsoft.com/fwlink/p/?LinkId=263439). В настоящее время отсоединить диск операционной системы невозможно.


1. Получение списка дисков, подключенных к виртуальной машине:

        vm disk list <vm-name>

    Если опустить параметр `<vm-name>`, вы получите список всех дисков в вашей подписке.


2. Отсоединение диска:

        vm disk detach <vm-name> <lun>

    `lun` определяет диск для отсоединения и представляет номер, который можно найти в списке дисков ВМ.

Пошаговое руководство, включая выходные данные терминала:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=62-->