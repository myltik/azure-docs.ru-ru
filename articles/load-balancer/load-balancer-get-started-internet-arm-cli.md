---
title: "Создание подсистемы балансировки нагрузки для Интернета в Resource Manager с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Узнайте, как создать балансировщик нагрузки для Интернета в диспетчере ресурсов с помощью интерфейса командной строки Azure."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: c8f29176c8566c94efeecadbc804f459d8f2a6c3

---
# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Создание внутреннего балансировщика нагрузки с помощью Azure CLI

> [!div class="op_single_selector"]
> * [Портал](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета, используя классическое развертывание](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Развертывание решения с помощью интерфейса командной строки Azure (Azure CLI)

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и интерфейса командной строки. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты.

* Конфигурация интерфейсных IP-адресов. Содержит общедоступные IP-адреса для входящего сетевого трафика.
* Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами трафика от балансировщика нагрузки.
* Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
* Правила NAT для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
* Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье о [поддержке Azure Resource Manage для подсистемы балансировки нагрузки](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Настройка интерфейса командной строки для использования Resource Manager

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

    ```azurecli
        azure config mode arm
    ```

    Ожидаемые выходные данные:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

1. Создайте виртуальную сеть *NRPVnet* в регионе "Восток США" с помощью группы ресурсов *NRPRG*.

    ```azurecli
        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16
    ```

    Создайте подсеть *NRPVnetSubnet* с блоком CIDR 10.0.0.0/24 в виртуальной сети *NRPVnet*.

    ```azurecli
        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24
    ```

2. Создайте общедоступный IP-адрес *NRPPublicIP*, который будет использоваться пулом интерфейсных IP-адресов, с DNS-именем *loadbalancernrp.eastus.cloudapp.azure.com*. В команде ниже используется статическое выделение и время ожидания при простое 4 минуты.

    ```azurecli
        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4
    ```

   > [!IMPORTANT]
   > Балансировщик нагрузки будет использовать метку домена общедоступного IP-адреса в качестве своего полного доменного имени (FQDN). В этом заключается отличие от классического развертывания, при котором в качестве полного доменного имени балансировщика нагрузки используется облачная служба.
   > В этом примере используется полное доменное имя *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Создание балансировщика нагрузки

Приведенная ниже команда создает балансировщик нагрузки *NRPlb* в группе ресурсов *NRPRG*, размещенной в регионе Azure *Восточная часть США*.

    ```azurecli
    azure network lb create NRPRG NRPlb eastus
    ```

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Создание пула интерфейсных IP-адресов и пула внутренних адресов
В этом примере показано, как создать пул интерфейсных IP-адресов, который будет принимать входящий сетевой трафик для балансировщика нагрузки, а также пул внутренних IP-адресов, который будет отправлять сетевой трафик с балансировкой нагрузки.

1. Создайте пул интерфейсных IP-адресов, связывающий общедоступный IP-адрес, созданный на предыдущем этапе, и балансировщик нагрузки.

    ```azurecli
        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip
    ```

2. Настройте пул внутренних адресов для приема входящего трафика из пула интерфейсных IP-адресов.

    ```azurecli
        azure network lb address-pool create NRPRG NRPlb NRPbackendpool
    ```

## <a name="create-lb-rules-nat-rules-and-probe"></a>Создание правил балансировки нагрузки, правил NAT и пробы

В этом примере создаются следующие элементы:

* правило NAT, которое направляет весь входящий трафик с порта 21 на порт 22<sup>1</sup>;
* правило NAT, которое направляет весь входящий трафик с порта 23 на порт 22;
* правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов во внутреннем пуле;
* правило пробы, согласно которому будет проверяться состояние работоспособности на странице *HealthProbe.aspx*.

<sup>1</sup> Правила NAT сопоставлены с конкретным экземпляром виртуальной машины, находящимся в зоне действия балансировщика нагрузки. Сетевой трафик, поступающий на порт 21, отправляется в определенную виртуальную машину на порту 22, связанным с этим правилом NAT. Для правила NAT необходимо указать протокол (UDP или TCP). Нельзя назначить оба протокола одному и тому же порту.

1. Создайте правила NAT.

    ```azurecli
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22
    ```

2. Создайте правило балансировщика нагрузки.

    ```azurecli
        azure network lb rule create --resource-group nrprg nrplb --lb-name lbrule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name NRPfrontendpool --backend-address-pool-name NRPbackendpool
    ```

3. Создайте пробу работоспособности.

    ```azurecli
        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4
    ```

4. Проверьте параметры.

    ```azurecli
        azure network lb show nrprg nrplb
    ```

    Ожидаемые выходные данные:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Создайте сетевую карту *lb-nic1-be* и свяжите ее с правилом NAT *rdp1*, а также с пулом внутренних адресов *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
    ```

    Ожидаемые выходные данные:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Создайте сетевую карту *lb-nic2-be* и свяжите ее с правилом NAT *rdp2*, а также с пулом внутренних адресов *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
    ```

3. Создайте виртуальную машину *web1* и свяжите ее с сетевым адаптером *lb-nic1-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

    ```azurecli
        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

    > [!IMPORTANT]
    > Виртуальные машины в балансировщике нагрузки должны находиться в одной группе доступности. Создайте группу доступности с помощью команды `azure availset create` .

    Должен быть получен результат, аналогичный приведенному ниже:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    > [!NOTE]
    > Информационное сообщение **В этой сетевой карте не настроен параметр publicIP** является ожидаемым, так как сетевая карта, созданная для балансировщика нагрузки, будет подключаться к Интернету через общедоступный IP-адрес балансировщика нагрузки.

    Так как сетевой адаптер *lb-nic1-be* связан с правилом NAT *rdp1*, вы можете подключиться к виртуальной машине *web1* с помощью RDP через порт 3441 в балансировщике нагрузки.

4. Создайте виртуальную машину *web2* и свяжите ее с сетевым адаптером *lb-nic2-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

    ```azurecli
        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="update-an-existing-load-balancer"></a>Обновление существующего балансировщика нагрузки
Вы можете добавить правила, ссылающиеся на существующий балансировщик нагрузки. В следующем примере новое правило балансировщика нагрузки добавляется в существующий балансировщик нагрузки **NRPlb**

```azurecli
azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool
```

## <a name="delete-a-load-balancer"></a>Удаление балансировщика нагрузки
Чтобы удалить балансировщик нагрузки, используйте следующую команду.

```azurecli
azure network lb delete --resource-group nrprg --name nrplb
```

## <a name="next-steps"></a>Дальнейшие действия
[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-cli.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO5-->


