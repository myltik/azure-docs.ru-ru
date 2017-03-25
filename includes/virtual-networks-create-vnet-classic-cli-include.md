## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Создание классической виртуальной сети с помощью интерфейса командной строки Azure
Интерфейс командной строки Azure можно использовать для управления ресурсами Azure из командной строки с любого компьютера под управлением Windows, Linux или OSX. Чтобы создать виртуальную сеть с помощью интерфейса командной строки Azure, выполните указанные ниже действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../articles/cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure network vnet create** , чтобы создать виртуальную сеть и подсеть, как показано ниже. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Ожидаемые выходные данные:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Имя виртуальной сети, которую нужно будет создать. В данном сценарии это *TestVNet*
   * **-e (или --address-space)**. Адресное пространство виртуальной сети. В данном сценарии это *192.168.0.0*
   * **-i (или -cidr)**. Маска подсети в формате CIDR. В данном сценарии это *16*.
   * **-n (или --subnet-name**). Имя первой подсети. В данном сценарии это *FrontEnd*.
   * **-p (или --subnet-start-ip)**. Начальный IP-адрес для подсети или адресное пространство подсети. В нашем сценарии это *192.168.1.0*.
   * **-r (или --subnet-cidr)**. Маска подсети в формате CIDR для подсети. В данном сценарии это *24*.
   * **-l (или --location)**. Регион Azure, в котором будет создана виртуальная сеть. В данном сценарии это *Central US*.
3. Выполните команду **azure network vnet subnet create** , чтобы создать подсеть, как показано ниже. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Вот результат, ожидаемый для указанной выше команды:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (или --vnet-name**. Имя виртуальной сети, в которой будет создана подсеть. В данном сценарии это *TestVNet*.
   * **-n (или --name)**. Имя новой подсети. В нашем сценарии это *BackEnd*.
   * **-a (или --address-prefix)**. Блок подсети CIDR. В данном сценарии это *192.168.2.0/24*.
4. Выполните команду **azure network vnet show** , как показано ниже, чтобы просмотреть свойства новой виртуальной сети.
   
            azure network vnet show
   
    Вот результат, ожидаемый для указанной выше команды:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

