<properties
   pageTitle="Создание внутреннего балансировщика нагрузки в Resource Manager с помощью Azure CLI | Microsoft Azure"
   description="Узнайте, как создать внутренний балансировщик нагрузки в Resource Manager с помощью Azure CLI."
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

# Создание внутреннего балансировщика нагрузки с помощью Azure CLI

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [Классическая модель развертывания](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Развертывание решения с помощью интерфейса командной строки Azure (Azure CLI)

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и интерфейса командной строки. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты:

- **Конфигурация интерфейсных IP-адресов**: содержит общедоступные IP-адреса для входящего сетевого трафика.
- **Пул внутренних адресов**: содержит сетевые интерфейсы (сетевые карты), которые позволяют виртуальным машинам получать трафик от балансировщика нагрузки.
- **Правила балансировки нагрузки**: содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
- **Правила NAT для входящего трафика**: содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
- **Пробы**: содержат пробы работоспособности, которые используются для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье, посвященной [поддержке Azure Resource Manager для балансировщика нагрузки](load-balancer-arm.md).

## Настройка интерфейса командной строки для использования Resource Manager

1. Если вы никогда не использовали Azure CLI, то см. статью [Установка Azure CLI](../../articles/xplat-cli-install.md). Следуйте инструкциям до того момента, где выбираются учетная запись и подписка Azure.

2. Выполните команду **azure config mode**, чтобы переключиться в режим Resource Manager, как показано ниже.

        azure config mode arm

    Ожидаемые выходные данные:

        info:    New mode is arm

## Пошаговая инструкция по созданию внутреннего балансировщика нагрузки

1. Войдите в Azure.

        azure login

    При появлении запроса введите свои учетные данные Azure.

2. Измените командные инструменты на режим Azure Resource Manager.

        azure config mode arm

## Создание группы ресурсов

Все ресурсы в Azure Resource Manager связаны с группой ресурсов. Если вы еще этого не сделали, создайте группу ресурсов.

    azure group create <resource group name> <location>

## Создание набора внутренних балансировщиков нагрузки

1. Создание внутреннего балансировщика нагрузки

    В следующем сценарии группа ресурсов с именем nrprg создается в восточном регионе США.

        azure network lb create -n nrprg -l eastus

    >[AZURE.NOTE] Все ресурсы для внутреннего балансировщика нагрузки, такие как виртуальные сети и подсети виртуальных сетей, должны находиться в той же группе ресурсов и в том же регионе.

2. Создайте интерфейсный IP-адрес для внутреннего балансировщика нагрузки.

    Используемый IP-адрес должен быть в диапазоне подсети виртуальной сети.

        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

    Используемые параметры:

    * **-g**: группа ресурсов;
    * **-l**: имя набора внутреннего балансировщика нагрузки;
    * **-n**: имя интерфейсного IP-адреса;
    * **-a**: частный IP-адрес в диапазоне подсети;
    * **-e**: имя подсети;
    * **-m**: имя виртуальной сети.

3. Создайте пул внутренних адресов.

        azure network lb address-pool create -g nrprg -l ilbset -n beilb

    Используемые параметры:

    * **-g**: группа ресурсов;
    * **-l**: имя набора внутреннего балансировщика нагрузки;
    * **-n**: имя пула внутренних адресов.

    После определения интерфейсного IP-адреса и пула внутренних адресов можно создать правила балансировщика нагрузки, правила NAT для входящего трафика и настроенные пробы работоспособности.

4. Создайте правило балансировщика нагрузки для внутреннего балансировщика нагрузки.

    Во время выполнения описанных выше действий команда создает правило балансировщика нагрузки для прослушивания порта 1433 в интерфейсном пуле и отправки сетевого трафика со сбалансированной нагрузкой во внутренний пул адресов, также используя порт 1433.

        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

    Используемые параметры:

    * **-g**: группа ресурсов;
    * **-l**: имя набора внутреннего балансировщика нагрузки;
    * **-n**: имя правила балансировщика нагрузки;
    * **-p**: протокол, используемый для правила;
    * **-f**: порт в интерфейсе балансировщика нагрузки, который прослушивает входящий сетевой трафик;
    * **-b**: порт во внутреннем пуле адресов, который принимает сетевой трафик.

5. Создайте правило NAT для входящих подключений.

    Правила NAT для входящего трафика используются для создания конечных точек в балансировщике нагрузки, которые будут направлены к конкретному экземпляру виртуальной машины. Предыдущие действия создали два правила NAT для удаленного рабочего стола.

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

    Используемые параметры:

    * **-g**: группа ресурсов;
    * **-l**: имя набора внутреннего балансировщика нагрузки;
    * **-n**: имя правила NAT для входящего трафика;
    * **-p**: протокол, используемый для правила;
    * **-f**: порт в интерфейсе балансировщика нагрузки, который прослушивает входящий сетевой трафик;
    * **-b**: порт во внутреннем пуле адресов, который принимает сетевой трафик.

5. Создайте пробы работоспособности для балансировщика нагрузки.

    Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность.

        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

    Используемые параметры:

    * **-g**: группа ресурсов;
    * **-l**: имя набора внутреннего балансировщика нагрузки;
    * **-n**: имя пробы работоспособности;
    * **-p**: протокол, используемый для пробы работоспособности;
    * **-i**: интервал между выполнением проб в секундах;
    * **-c**: число проверок.


    >[AZURE.NOTE] Для различных сценариев администрирования платформа Microsoft Azure использует статические общедоступные маршрутизируемые IPv4-адреса. IP-адрес — 168.63.129.16. Не блокируйте этот IP-адрес брандмауэрами, поскольку это может привести к непредвиденному поведению. Во внутреннем балансировщике нагрузки Azure этот IP-адрес используется пробами мониторинга для определения состояния работоспособности виртуальных машин в наборе балансировки нагрузки. Если группа безопасности сети используется для ограничения трафика, поступающего на виртуальные машины Azure в наборе внутренней балансировки нагрузки, или если она применяется к подсети виртуальной сети, то убедитесь в наличии правила сетевой безопасности, разрешающего поступление сетевого трафика с адреса 168.63.129.16.

## Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Создайте сетевую карту *lb-nic1-be*, а затем свяжите ее с правилом NAT *rdp1* и внутренним пулом адресов *beilb*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Параметры

    * **-g**: имя группы ресурсов;
    * **-n**: имя для ресурса сетевой карты;
    * **--subnet-name**: имя подсети;
    * **--subnet-vnet-name**: имя виртуальной сети;
    * **-d**: идентификатор ресурса внутреннего пула, который начинается с /subscription/{ИД-подписки/resourcegroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadbalancers/<имя\_балансировщика\_нагрузки>/backendaddresspools/<имя\_внутреннего\_пула>.
    * **-e**: идентификатор правила NAT, который будет связан с ресурсом сетевой карты. Начинается с /subscriptions/####################################/resourceGroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/loadBalancers/<имя\_балансировщика\_нагрузки>/inboundNatRules/<имя\_правила\_NAT>.

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

2. Создайте сетевую карту *lb-nic2-be*, а затем свяжите ее с правилом NAT *rdp2* и внутренним пулом адресов *beilb*.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Создайте виртуальную машину *DB1*, а затем свяжите ее с сетевой картой *lb-nic1-be*. Учетная запись хранения *web1nrp* создается перед выполнением следующей команды:

        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Виртуальные машины в балансировщике нагрузки должны находиться в одной группе доступности. Создайте группу доступности с помощью команды `azure availset create`.

4. Создайте виртуальную машину *DB2*, а затем свяжите ее с сетевой картой *lb-nic2-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды:

        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Удаление балансировщика нагрузки

Чтобы удалить балансировщик нагрузки, используйте следующую команду:

    azure network lb delete -g nrprg -n ilbset

В этом примере **nrprg** — это группа ресурсов, а **ilbset** — имя внутреннего балансировщика нагрузки.


## Дальнейшие действия

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0928_2016-->