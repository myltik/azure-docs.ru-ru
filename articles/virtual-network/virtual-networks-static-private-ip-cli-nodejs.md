---
title: "Настройка частных IP-адресов для виртуальных машин (Azure CLI 1.0) | Документация Майкрософт"
description: "Узнайте, как настроить частные IP-адреса для виртуальных машин с помощью интерфейса командной строки Azure (CLI) версии 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9179319095c31d5eb454860e173ffa7c65fc9f73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-10"></a>Настройка частных IP-адресов для виртуальной машины с помощью Azure CLI 1.0


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи 

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки. 

- [Azure CLI 1.0](#how-to-specify-a-static-private-ip-address-when-creating-a-vm) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](virtual-networks-static-private-ip-arm-cli.md) — это интерфейс командной строки нового поколения для модели развертывания Resource Manager. 

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

В этой статье описывается модель развертывания с использованием менеджера ресурсов. Кроме того, вы можете [управлять статическим частным IP-адресом в классической модели развертывания](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Для выполнения приведенных ниже примеров команд Azure CLI требуется созданная простая среда. Для выполнения команд в том виде, в каком они представлены в данном документе, сначала постройте тестовую среду, описанную в разделе [Создание виртуальной сети](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Указание статического частного IP-адреса при создании виртуальной машины
Чтобы создать виртуальную машину с именем *DNS01* в подсети *FrontEnd* виртуальной сети *TestVNet* со статическим частным IP-адресом *192.168.1.101*, выполните следующие действия.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../cli-install-nodejs.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.
2. Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.
   
        azure config mode arm
   
    Ожидаемые выходные данные:
   
        info:    New mode is arm
3. Выполните команду **azure network public-ip create** , чтобы создать общедоступный IP-адрес для виртуальной машины. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
        azure network public-ip create -g TestRG -n TestPIP -l centralus
   
    Ожидаемые выходные данные:
   
        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK
   
   * **-g (или --resource-group)**. Имя группы ресурсов, в которой будет создан новый общедоступный IP-адрес.
   * **-n (или --name)**. Имя общедоступного IP-адреса.
   * **-l (или --location)**. Регион Azure, в котором будет создан общедоступный IP-адрес. В данном случае это — *centralus*.
4. Выполните команду **azure network nic create** , чтобы создать сетевую карту со статическим частным IP-адресом. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd
   
    Ожидаемые выходные данные:
   
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
   
   * **-a (или --private-ip-address)**. Статические частный IP-адрес для сетевой карты.
   * **-m (или --subnet-vnet-name)**. Имя виртуальной сети, в которой будет создана сетевая карта.
   * **-k (или --subnet-name)**. Имя подсети, в которой будет создана сетевая карта.
5. Выполните команду **azure vm create** , чтобы создать виртуальную машину с использованием общедоступного IP-адреса и сетевой карты, созданных ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.
   
        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd
   
    Ожидаемые выходные данные:
   
        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK
   
   * **-y (или --os-type)**. Тип операционной системы для виртуальной машины: *Windows* или *Linux*.
   * **-f (или --nic-name)**. Имя сетевой карты, которая будет использоваться виртуальной машиной.
   * **-i (или --public-ip-name)**. Имя общедоступного IP-адреса, который будет использоваться виртуальной машиной.
   * **-F (или --vnet-name)**. Имя виртуальной сети, в которой будет создана виртуальная машина.
   * **-j (или --vnet-subnet-name)**. Имя подсети, в которой будет создана виртуальная машина.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Получение сведений о статическом частном IP-адресе виртуальной машины
Чтобы просмотреть сведения о статическом частном IP-адресе виртуальной машины, созданной с помощью приведенного выше скрипта, выполните следующую команду Azure CLI. Обратите внимание на значения параметров *Private IP alloc-method* и *Private IP address*.

    azure vm show -g TestRG -n DNS01

Ожидаемые выходные данные:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Удаление статического частного IP-адреса виртуальной машины
Статический частный IP-адрес нельзя удалить из сетевой карты с использованием Azure CLI для диспетчера ресурсов. Вам нужно создать новую сетевую карту, использующую динамический IP-адрес, удалить предыдущую сетевую карту виртуальной машины, а затем добавить новую сетевую карту. Чтобы изменить сетевую карту для виртуальной машины, которая использовалась в приведенных выше командах, выполните действия, описанные выше.

1. Выполните команду **azure network nic create** , чтобы создать сетевую карту с динамическим IP-адресом. Обратите внимание: вам уже не нужно указывать IP-адрес.
   
        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd
   
    Ожидаемые выходные данные:
   
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
2. Выполните команду **azure vm set** , чтобы изменить сетевую карту, используемую виртуальной машиной.
   
        azure vm set -g TestRG -n DNS01 -N TestNIC2
   
    Ожидаемые выходные данные:
   
        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK
3. При необходимости выполните команду **azure network nic delete** , чтобы удалить старую сетевую карту.
   
        azure network nic delete -g TestRG -n TestNIC --quiet
   
    Ожидаемые выходные данные:
   
        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Добавление статического частного IP-адреса для существующей виртуальной машины
Чтобы добавить статический частный IP-адрес в сетевую карту, которая используется виртуальной машиной, созданной с помощью приведенного выше сценария, выполните следующую команду.

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Ожидаемые выходные данные:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с информацией о [зарезервированных общедоступных IP-адресах](virtual-networks-reserved-public-ip.md) .
* Узнайте об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Ознакомьтесь с информацией о [REST API зарезервированных IP-адресов](https://msdn.microsoft.com/library/azure/dn722420.aspx).

