
Дополнительные сведения о дисках см. в разделе [О дисках и виртуальных жестких дисках для виртуальных машин Azure](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Подключение пустого диска
1. Откройте Azure CLI 1.0 и [подключитесь к подписке Azure](../articles/xplat-cli-connect.md). Убедитесь, что вы находитесь в режиме управления службами Azure (`azure config mode asm`).
2. Введите `azure vm disk attach-new`, чтобы создать и подключить новый диск, как показано в примере ниже. Замените имя *myVM* именем своей виртуальной машины Linux и укажите размер диска в гигабайтах. В нашем примере это *100 ГБ*.

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Созданный и присоединенный диск с данными отображается в выводе команды `azure vm disk list <virtual-machine-name>`, как показано в примере ниже.
   
    ```azurecli
    azure vm disk list TestVM
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Подключение существующего диска
Для подключения существующего диска требуется VHD-файл в учетной записи хранения.

1. Откройте Azure CLI 1.0 и [подключитесь к подписке Azure](../articles/xplat-cli-connect.md). Убедитесь, что вы находитесь в режиме управления службами Azure (`azure config mode asm`).
2. Проверьте, не добавлен ли уже виртуальный жесткий диск, который вы хотите подключить, в подписку Azure.
   
    ```azurecli
    azure vm disk list
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Если в подписке нет нужного диска, вы можете передать локальный виртуальный жесткий диск в подписку с помощью команды `azure vm disk create` или `azure vm disk upload`. Пример использования команды `disk create` показан ниже.
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   С помощью команды `azure vm disk upload` вы также можете передать виртуальный жесткий диск в конкретную учетную запись хранения. Дополнительные сведения о командах для управления дисками с данными виртуальных машин Azure см. [здесь](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Теперь подключите нужный виртуальный жесткий диск к виртуальной машине.
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Обязательно замените *myVM* именем своей виртуальной машины, а *myVHD* — именем своего виртуального жесткого диска.

5. Чтобы проверить, подключен ли диск к виртуальной машине, выполните команду `azure vm disk list <virtual-machine-name>`.
   
    ```azurecli
    azure vm disk list myVM
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> После добавления диска данных необходимо войти в систему на виртуальной машине и инициализировать диск, чтобы виртуальная машина могла использовать его для хранения данных. Сведения об инициализации диска см. в инструкциях ниже.
> 
> 



<!--HONumber=Feb17_HO2-->


