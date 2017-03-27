## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Создание виртуальной сети с помощью интерфейса командной строки Azure
Интерфейс командной строки Azure можно использовать для управления ресурсами Azure из командной строки с любого компьютера под управлением Windows, Linux или OSX. Чтобы создать виртуальную сеть с помощью интерфейса командной строки Azure, выполните указанные ниже действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../articles/cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.
   
        azure config mode arm
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    New mode is arm
3. При необходимости выполните команду **azure group create**, как показано ниже, чтобы создать группу ресурсов. Обратите внимание на результат выполнения команды. В списке, который откроется после выполнения команды, будут указаны используемые параметры. Дополнительные сведения о группах ресурсов см. в разделе "Группы ресурсов" [обзора Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
        azure group create -n TestRG -l centralus
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
   
   * **-n (или --name)**. Имя для новой группы ресурсов. В данном сценарии это *TestRG*.
   * **-l (или --location)**. Регион Azure, в котором будет создана новая группа ресурсов. В данном сценарии это *centralus*.
4. Выполните команду **azure network vnet create** , чтобы создать виртуальную сеть и подсеть, как показано ниже. 
   
        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK
   
   * **-g (или --resource-group)**. Имя группы ресурсов, в которой будет создана виртуальная сеть. В данном сценарии это *TestRG*.
   * **-n (или --name)**. Имя виртуальной сети, которую нужно будет создать. В данном сценарии это *TestVNet*
   * **-a (или --address-prefixes)**. Список блоков CIDR для адресного пространства виртуальной сети. В данном сценарии это *192.168.0.0/16*
   * **-l (или --location)**. Регион Azure, в котором будет создана виртуальная сеть. В данном сценарии это *centralus*.
5. Выполните команду **azure network vnet subnet create** , чтобы создать подсеть, как показано ниже. Обратите внимание на результат выполнения команды. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK
   
   * **-e (или --vnet-name**. Имя виртуальной сети, в которой будет создана подсеть. В данном сценарии это *TestVNet*.
   * **-n (или --name)**. Имя новой подсети. В данном сценарии это *FrontEnd*.
   * **-a (или --address-prefix)**. Блок подсети CIDR. В данном сценарии это *192.168.1.0/24*.
6. При необходимости повторите описанный выше шаг 5, чтобы создать другие подсети. В данном сценарии выполните указанную ниже команду для создания подсети *BackEnd* .
   
        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24
7. Выполните команду **azure network vnet show** , как показано ниже, чтобы просмотреть свойства новой виртуальной сети.
   
        azure network vnet show -g TestRG -n TestVNet
   
    Вот результат, ожидаемый для указанной выше команды:
   
        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK

