
Дополнительную информацию о дисках см. в разделе [О дисках виртуальных машин в Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="cliattachempty"></a>Практическое руководство. Подключение пустого диска
Присоединение пустого диска — это простой способ добавления диска данных. Выполните следующую команду, чтобы присоединить новый пустой диск:

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Замените `vm-name` на имя вашей виртуальной машины, а `size-in-gb` — на размер нового диска. В качестве последнего аргумента при желании можно использовать URL-адрес BLOB-объекта, чтобы явно указать целевой BLOB-объект для создания. Если URL-адрес BLOB-объекта не указан, BLOB-объект создается автоматически.

Выполните следующую команду, чтобы убедиться, что диск был создан:

    vm disk list <vm-name>

Ниже приведен пример выполнения указанных выше команд, включая выходные данные терминала:

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=August15_HO6-->