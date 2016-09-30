<properties 
   pageTitle="Создание балансировщика нагрузки для Интернета в диспетчере ресурсов с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Узнайте, как создать балансировщик нагрузки для Интернета в диспетчере ресурсов с помощью интерфейса командной строки Azure."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Начало работы по созданию внутреннего балансировщика нагрузки с помощью интерфейса командной строки Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета, используя классическое развертывание](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Развертывание решения с помощью интерфейса командной строки Azure (Azure CLI)

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и интерфейса командной строки. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты.

- Конфигурация интерфейсных IP-адресов. Содержит общедоступные IP-адреса для входящего сетевого трафика.
- Пул внутренних адресов. Содержит сетевые интерфейсы (сетевые карты) для получения виртуальными машинами трафика от балансировщика нагрузки.
- Правила балансировки нагрузки. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
- Правила NAT для входящего трафика. Содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
- Пробы. Содержат пробы работоспособности, с помощью которых можно проверить доступность экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для балансировщика нагрузки](load-balancer-arm.md).

## Настройка интерфейса командной строки для использования Resource Manager

1. Если вы еще не пользовались Azure CLI, см. статью [Установка и настройка CLI Azure](../../articles/xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

        azure config mode arm

    Ожидаемые выходные данные:

        info:    New mode is arm

## Создание виртуальной сети и общедоступного IP-адреса для пула IP-адресов клиентской части

1. Создайте виртуальную сеть *NRPVnet* в регионе "Восток США" с помощью группы ресурсов *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Создайте подсеть *NRPVnetSubnet* с блоком CIDR 10.0.0.0/24 в виртуальной сети *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Создайте общедоступный IP-адрес *NRPPublicIP*, который будет использоваться пулом интерфейсных IP-адресов, с DNS-именем *loadbalancernrp.eastus.cloudapp.azure.com*. В команде ниже используется статическое выделение и время ожидания при простое 4 минуты.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT] Балансировщик нагрузки будет использовать метку домена общедоступного IP-адреса в качестве своего полного доменного имени (FQDN). В этом заключается отличие от классического развертывания, при котором в качестве полного доменного имени балансировщика нагрузки используется облачная служба. В этом примере используется полное доменное имя *loadbalancernrp.eastus.cloudapp.azure.com*.

## Создание балансировщика нагрузки

Приведенная ниже команда создает балансировщик нагрузки *NRPlb* в группе ресурсов *NRPRG*, размещенной в регионе Azure *Восточная часть США*.

    azure network lb create NRPRG NRPlb eastus

## Создание пула интерфейсных IP-адресов и пула внутренних адресов

Этот примере создает пул интерфейсных IP-адресов, который будет принимать входящий сетевой трафик для балансировщика нагрузки, а также пул внутренних IP-адресов, который будет отправлять сетевой трафик с балансировкой нагрузки.

1. Создайте пул интерфейсных IP-адресов, связывающий общедоступный IP-адрес, созданный на предыдущем этапе, и балансировщик нагрузки.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Настройте пул внутренних адресов для приема входящего трафика из пула интерфейсных IP-адресов.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Создание правил балансировки нагрузки, правил NAT и пробы

В этом примере создаются следующие элементы:

- правило NAT, которое направляет весь входящий трафик с порта 21 на порт 22<sup>1</sup>;
- правило NAT, которое направляет весь входящий трафик с порта 23 на порт 22;
- правило балансировщика нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов во внутреннем пуле;
- правило пробы, согласно которому будет проверяться состояние работоспособности на странице *HealthProbe.aspx*.

<sup>1</sup> Правила NAT сопоставлены с конкретным экземпляром виртуальной машины, находящимся в зоне действия балансировщика нагрузки. Сетевой трафик, поступающий на порт 21, отправляется в определенную виртуальную машину на порту 22, связанным с этим правилом NAT. Для правила NAT необходимо указать протокол (UDP или TCP). Нельзя назначить оба протокола одному и тому же порту.

1. Создайте правила NAT.

        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

    Параметры
    * **-g** — имя группы ресурсов;
    * **-l** — имя балансировщика нагрузки;
    * **-n** — имя ресурса (правило NAT, правило пробы или правило балансировки нагрузки);
    * **-p** — протокол (TCP или UDP);
    * **-f** — интерфейсный порт, который следует использовать (команда probe использует параметр -f для определения пути пробы);
    * **-b** — внутренний порт, который следует использовать.

2. Создайте правило балансировщика нагрузки.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Создайте пробу работоспособности.

        azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

    Параметры
    * **-g** — группа ресурсов;
    * **-l** — имя набора балансировщика нагрузки;
    * **-n** — имя пробы работоспособности;
    * **-p** — протокол, используемый для пробы работоспособности;
    * **-i** — интервал между выполнением проб в секундах;
    * **-c** — число проверок.

4. Проверьте параметры.

        azure network lb show nrprg nrplb

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

## Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Создайте сетевую карту *lb-nic1-be* и свяжите ее с правилом NAT *rdp1*, а также с пулом внутренних адресов *NRPbackendpool*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Параметры

    * **-g** — имя группы ресурсов;
    * **-n** — имя для ресурса сетевого адаптера;
    * **--subnet-name** — имя подсети;
    * **--subnet-vnet-name** — имя виртуальной сети;
    * **-d** — идентификатор ресурса внутреннего пула (начинается с /subscription/{ИД\_подписки/resourcegroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadbalancers/<имя\_балансировщика\_нагрузки>/backendaddresspools/<имя\_внутреннего\_пула>).
    * **-e** — идентификатор правила NAT, который будет связан с ресурсом сетевой карты (начинается с /subscriptions/####################################/resourceGroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadBalancers/<имя\_балансировщика\_нагрузки>/inboundNatRules/<имя\_правила\_NAT>).

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

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Создайте виртуальную машину *web1* и свяжите ее с сетевым адаптером *lb-nic1-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Виртуальные машины в балансировщике нагрузки должны находиться в одной группе доступности. Создайте группу доступности с помощью команды `azure availset create`.

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

    >[AZURE.NOTE] Информационное сообщение **В этой сетевой карте не настроен параметр publicIP** является ожидаемым, так как сетевая карта, созданная для балансировщика нагрузки, будет подключаться к Интернету через общедоступный IP-адрес балансировщика нагрузки.

    Так как сетевой адаптер *lb-nic1-be* связан с правилом NAT *rdp1*, вы можете подключиться к виртуальной машине *web1* с помощью RDP через порт 3441 в балансировщике нагрузки.

4. Создайте виртуальную машину *web2* и свяжите ее с сетевым адаптером *lb-nic2-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Обновление существующего балансировщика нагрузки

Вы можете добавить правила, ссылающиеся на существующий балансировщик нагрузки. В следующем примере новое правило балансировщика нагрузки добавляется в существующий балансировщик нагрузки **NRPlb**.

    azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Параметры

* **-g** — имя группы ресурсов;
* **-l** — имя балансировщика нагрузки;
* **-n** — имя правила балансировщика нагрузки;
* **-p** — протокол;
* **-f** — интерфейсный порт;
* **-b** — внутренний порт;
* **-t** — имя интерфейсного пула;
* **-b** — имя внутреннего пула.

## Удаление балансировщика нагрузки

Чтобы удалить балансировщик нагрузки, используйте следующую команду.

    azure network lb delete -g nrprg -n nrplb

Где **nrprg** — это группа ресурсов, а **nrplb** — имя балансировщика нагрузки.

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-cli.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!----HONumber=AcomDC_0914_2016-->
