1. Войдите в подписку Azure, выполнив действия, перечисленные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../articles/xplat-cli-connect.md).
2. Убедитесь, что вы находитесь в режиме управления службами, выполнив следующую команду.

        azure config mode asm

3. Среди доступных образов найдите образ Linux, который нужно загрузить.

        azure vm image list | grep "Linux"

4. С помощью команды `azure vm create` создайте виртуальную машину из образа Linux из приведенного выше списка. На этом шаге создается облачная служба, а также учетная запись хранения. Вы также можете подключить эту виртуальную машину к существующей облачной службе с помощью параметра `-c`. С помощью параметра `-e` также создается конечная точку SSH для входа на виртуальную машину Linux.

        ~$ azure vm create "MyTestVM" b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64 "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b4590d9e3ed742e4a1d46e5424aa335e__suse-opensuse-13.1-20141216-x86-64
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE]Для виртуальной машины Linux в команде `vm create` необходимо указать параметр `-e`; SSH невозможно включить после создания виртуальной машины. Дополнительную информацию см. в статье [Использование SSH с Linux в Azure](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md).

    Обратите внимание, что именно образ *b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-opensuse-13.1-20141216-x86-64* мы выбрали в списке образов на предыдущем шаге. *MyTestVM* — имя нашей новой виртуальной машины, а *adminUser* — имя пользователя, которое будет использоваться для подключения к виртуальной машине по протоколу SSH. При необходимости вы можете заменить эти переменные. Дополнительные сведения об этой команде см. в статье [Управление службами Azure с помощью интерфейса командной строки Azure](../articles/virtual-machines/virtual-machines-command-line-tools.md).

5. Чтобы отобразить созданную виртуальную машину Linux в списке, выполните следующую команду.

        azure vm list

6. Вы можете проверить атрибуты виртуальной машины с помощью следующей команды.

        azure vm show MyTestVM

7. Созданную виртуальную машину можно запустить, выполнив команду `azure vm start`.

Подробные сведения обо всех этих командах для управления виртуальной машиной с помощью CLI Azure см. в статье [Управление службами Azure с помощью интерфейса командной строки Azure](../articles/virtual-machines/virtual-machines-command-line-tools.md).

<!---HONumber=July15_HO5-->