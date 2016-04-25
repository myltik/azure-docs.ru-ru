
1. Войдите в подписку Azure, выполнив действия, перечисленные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure \(Azure CLI\)](../articles/xplat-cli-connect.md).

2. Убедитесь, что вы находитесь в режиме управления службами, выполнив следующую команду.

        azure config mode asm

3. Среди доступных образов найдите образ Linux, который нужно загрузить.

        azure vm image list | grep "Linux"

   В окне командной строки Windows вместо оператора grep используйте **find**.

4. С помощью команды `azure vm create` создайте виртуальную машину из образа Linux из приведенного выше списка. На этом шаге создается облачная служба, а также учетная запись хранения. Вы также можете подключить эту виртуальную машину к существующей облачной службе с помощью параметра `-c`. С помощью параметра `-e` также создается конечная точка SSH для входа на виртуальную машину Linux.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160314-en-us-30GB "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160314-en-us-30GB
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Для виртуальной машины Linux в команде `vm create` необходимо указать параметр `-e`; SSH невозможно включить после создания виртуальной машины. Дополнительную информацию см. в статье [Использование SSH с Linux в Azure](virtual-machines-linux-ssh-from-linux.md).

    Обратите внимание, что *b39f27a8b8c64d52b05eac6a62ebad85\_\_Ubuntu-14\_04\_4-LTS-amd64-server-20160314-en-us-30GB* — это образ, который мы выбрали из списка образов на предыдущем шаге. *MyTestVM* — имя нашей новой виртуальной машины, а *adminUser* — имя пользователя, которое будет использоваться для подключения к виртуальной машине по протоколу SSH. При необходимости вы можете заменить эти переменные. Дополнительные сведения об этой команде см. в статье [Управление службами Azure с помощью интерфейса командной строки Azure](virtual-machines-command-line-tools.md).

5. Чтобы отобразить созданную виртуальную машину Linux в списке, выполните следующую команду.

        azure vm list

6. Вы можете проверить атрибуты виртуальной машины с помощью следующей команды.

        azure vm show MyTestVM

7. Созданную виртуальную машину можно запустить, выполнив команду `azure vm start`.

Подробные сведения об этих командах для управления виртуальной машиной с помощью CLI Azure см. в статье [об использовании интерфейса командной строки Azure с API управления службами](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0413_2016-->