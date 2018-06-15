---
title: Создание общедоступной подсистемы балансировки нагрузки с поддержкой IPv6 с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать общедоступную подсистему балансировки нагрузки с поддержкой IPv6 в Azure Resource Manager с помощью Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: IPv6, Azure Load Balancer, двойной стек, общедоступный IP-адрес, встроенная поддержка Ipv6, мобильное устройство, Интернет вещей
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 62f22ccadfabd2f3d6906beb3c241703d4e6383f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30264036"
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Создание общедоступной подсистемы балансировки нагрузки с поддержкой IPv6 в Azure Resource Manager с помощью Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Интерфейс командной строки Azure](load-balancer-ipv6-internet-cli.md)
> * [Шаблон](load-balancer-ipv6-internet-template.md)


Azure Load Balancer является балансировщиком нагрузки 4-го уровня (TCP, UDP). Подсистема балансировки нагрузки обеспечивает высокий уровень доступности, распределяя входящий трафик между работоспособными экземплярами службы в облачных службах или виртуальных машинах, определенных в наборе подсистемы балансировки нагрузки. Подсистемы балансировки нагрузки могут также представить данные службы на нескольких портах, нескольких IP-адресах или обоими этими способами.

## <a name="example-deployment-scenario"></a>Пример сценария развертывания

На следующей схеме показано решение подсистемы балансировки нагрузки, которое развертывается в этой статье с помощью примера шаблона.

![Сценарий использования балансировщика нагрузки](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

В этом сценарии вы создадите следующие ресурсы Azure:

* две виртуальные машины;
* виртуальный сетевой интерфейс для каждой виртуальной машины с назначенными адресами IPv4 и IPv6;
* общедоступную подсистему балансировки нагрузки с общедоступными адресами IPv4 и IPv6;
* группу доступности, которая содержит две виртуальные машины;
* два правила подсистемы балансировки нагрузки для сопоставления общедоступных виртуальных IP-адресов с частными конечными точками.

## <a name="deploy-the-solution-by-using-azure-cli"></a>Развертывание решения с помощью Azure CLI

Ниже описано, как создать общедоступную подсистему балансировки нагрузки с помощью Azure Resource Manager и Azure CLI. Azure Resource Manager позволяет создать и настроить каждый объект по отдельности, после чего на их основе создается единый ресурс.

Чтобы развернуть подсистему балансировки нагрузки, создайте и настройте следующие объекты:

* **Конфигурация интерфейсных IP-адресов**. Содержит общедоступные IP-адреса для входящего сетевого трафика.
* **Пул внутренних адресов**. Содержит сетевые интерфейсы (сетевые адаптеры (NIC)) для получения виртуальными машинами трафика от подсистемы балансировки нагрузки.
* **Правила подсистемы балансировки нагрузки**. Содержат правила сопоставления общего порта в подсистеме балансировки нагрузки с портом в пуле внутренних адресов.
* **Правила преобразования сетевых адресов для входящего трафика**. Содержат правила преобразования сетевых адресов (NAT) в подсистеме балансировки нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
* **Пробы** Содержат пробы работоспособности, которые используются для проверки доступности экземпляров виртуальной машины в пуле внутренних адресов.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Настройка среды Azure CLI для использования Azure Resource Manager

В этом примере программы Azure CLI выполняются в командном окне PowerShell. Для улучшения удобства чтения и повторного использования примените возможности сценариев PowerShell, а не командлеты Azure PowerShell.

1. Если вы еще не пользовались Azure CLI, ознакомьтесь со статьей [Install the Azure CLI 1.0](../cli-install-nodejs.md) (Установка Azure CLI 1.0) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Чтобы переключиться в режим Resource Manager, выполните команду **azure config mode**.

    ```azurecli
    azure config mode arm
    ```

    Ожидаемые выходные данные:

        info:    New mode is arm

3. Войдите в Azure и получите список подписок.

    ```azurecli
    azure login
    ```

4. Когда появится окно для входа, введите учетные данные Azure.

    ```azurecli
    azure account list
    ```

5. Выберите подписку, которую нужно использовать, и запишите идентификатор подписки, который понадобится на следующем шаге.

6. Настройте переменные PowerShell для использования с командами Azure CLI.

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Создание группы ресурсов, балансировщика нагрузки, виртуальной сети и подсетей

1. Создайте группу ресурсов:

    ```azurecli
    azure group create $rgName $location
    ```

2. Создайте подсистему балансировки нагрузки.

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Создайте виртуальную сеть:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Создайте две подсети в виртуальной сети.

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Создание общедоступных IP-адресов для интерфейсного пула

1. Настройте переменные PowerShell.

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Создайте общедоступный IP-адрес для интерфейсного пула IP-адресов.

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Подсистемы балансировки нагрузки используют метку домена общедоступного IP-адреса в качестве своего полного доменного имени (FQDN). В этом заключается отличие от классического развертывания, при котором в качестве полного доменного имени балансировщика нагрузки используется имя облачной службы.
    >
    > В этом примере используется полное доменное имя *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Создание интерфейсного и внутреннего пулов

В этом разделе вы создадите следующие пулы IP-адресов:
* интерфейсный пул IP-адресов, который получает входящий сетевой трафик в подсистеме балансировки нагрузки;
* внутренний пул IP-адресов, куда интерфейсный пул отправляет сетевой трафик с балансировкой нагрузки.

1. Настройте переменные PowerShell.

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Создайте пул интерфейсных IP-адресов и связывающий с ним общедоступный IP-адрес, который был создан на предыдущем этапе, и подсистему балансировки нагрузки.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Создание пробы, правил преобразования сетевых адресов и правил подсистемы балансировки нагрузки

В этом примере создаются следующие элементы:

* правило пробы для проверки подключения к TCP-порту 80;
* правило преобразования сетевых адресов, которое направляет весь входящий трафик с порта 3389 на порт 3389 для RDP\*;
* правило преобразования сетевых адресов, которое направляет весь входящий трафик с порта 3391 на порт 3389 для протокола удаленного рабочего стола (RDP)\*;
* правило подсистемы балансировки нагрузки, которое балансирует весь входящий трафик на порту 80, перенаправляя трафик на порт 80 других адресов во внутреннем пуле.

\* Правила преобразования сетевых адресов сопоставлены с конкретным экземпляром виртуальной машины, находящимся в зоне действия подсистемы балансировки нагрузки. Сетевой трафик, который поступает на порт 3389, отправляется на определенную виртуальную машину и порт, которые связанны с правилом преобразования сетевых адресов. Для правила NAT необходимо указать протокол (UDP или TCP). Нельзя назначить оба протокола одному и тому же порту.

1. Настройте переменные PowerShell.

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Создадим пробу.

    В следующем примере создается проба TCP, которая каждые 15 секунд проверяет подключение к внутреннему TCP-порту 80. После двух последовательных неудавшихся подключений она пометит внутренний ресурс как недоступный.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Создайте правила преобразования сетевых адресов для входящего трафика, позволяющие RDP-подключения к внутренним ресурсам.

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Создайте правила подсистемы балансировки нагрузки, которые отправляют трафик на разные внутренние порты в зависимости от полученного запроса интерфейса.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Проверьте параметры.

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    Ожидаемые выходные данные:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Создание сетевых адаптеров

Создайте объединение сетевых адаптеров и свяжите их с правилами преобразования сетевых адресов, правилами подсистемы балансировки нагрузки и пробами.

1. Настройте переменные PowerShell.

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Создайте сетевой адаптер для каждой серверной части и добавьте конфигурацию IPv6.

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Создание внутренних ресурсов виртуальных машин и присоединение каждого сетевого адаптера

Чтобы создать виртуальные машины, необходима учетная запись хранения. Для балансировки нагрузки виртуальные машины должны входить в группу доступности. Дополнительные сведения о создании виртуальных машин см. в статье [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Настройте переменные PowerShell.

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > В этом примере для виртуальных машин используются имя пользователя и пароль в виде открытого текста. Будьте предусмотрительны, используя эти учетные данные в виде открытого текста. Более безопасный способ обработки учетных данных в PowerShell приводится в описании командлета [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx).

2. Создайте учетную запись хранения и группу доступности.

    При создании виртуальных машин можно использовать существующую учетную запись хранения. Создайте учетную запись хранения с помощью следующей команды:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Создайте группу доступности.

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Создайте виртуальные машины со связанными сетевыми адаптерами.

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Дополнительная информация

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-cli.md)  
[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)  
[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
