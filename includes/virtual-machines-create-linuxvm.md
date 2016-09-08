
1. Войдите в подписку Azure, выполнив действия, перечисленные в статье [Подключение к среде Azure с использованием интерфейса командной строки Azure (Azure CLI)](../articles/xplat-cli-connect.md).

2. Убедитесь, что вы находитесь в режиме классического развертывания, выполнив следующую команду.

        azure config mode asm

3. Среди доступных образов найдите образ Linux, который нужно загрузить.

        azure vm image list | grep "Linux"

   В окне командной строки Windows вместо оператора grep используйте **find**.

4. С помощью команды `azure vm create` создайте виртуальную машину на основе образа Linux из приведенного выше списка. На этом шаге создаются облачная служба и учетная запись хранения. Вы также можете подключить эту виртуальную машину к существующей облачной службе с помощью параметра `-c`. С помощью параметра `-e` также создается конечная точка SSH для входа на виртуальную машину Linux.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ru-RU-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ru-RU-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Для виртуальной машины Linux в `vm create` необходимо указать параметр `-e`. После создания виртуальной машины включить протокол SSH невозможно. Дополнительную информацию см. в статье [Использование SSH с Linux в Azure](virtual-machines-linux-mac-create-ssh-keys.md).

    *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-ru-RU-30GB* — это образ, который мы выбрали из списка образов на предыдущем шаге. *MyTestVM* — имя нашей новой виртуальной машины, а *adminUser* — имя пользователя для подключения к виртуальной машине по протоколу SSH. При необходимости вы можете заменить эти переменные. Дополнительные сведения об этой команде см. в статье [Команды Azure CLI в режиме управления службами Azure (ASM)](virtual-machines-command-line-tools.md).

5. Чтобы отобразить созданную виртуальную машину Linux в списке, выполните следующую команду.

        azure vm list

6. Вы можете проверить атрибуты виртуальной машины с помощью следующей команды.

        azure vm show MyTestVM

7. Созданную виртуальную машину можно запустить, выполнив команду `azure vm start`.

## Дальнейшие действия
Дополнительные сведения о всех командах Azure CLI для управления виртуальной машиной см. в статье [Команды Azure CLI в режиме управления службами Azure (ASM)](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0824_2016-->