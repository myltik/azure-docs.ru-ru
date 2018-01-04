---
title: "Создайте внешняя Подсистема балансировки нагрузки с IPv6 - Azure CLI | Документы Microsoft"
description: "Узнайте, как создать внешняя Подсистема балансировки нагрузки с IPv6 в диспетчере ресурсов Azure с помощью Azure CLI."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: "IPv6, Azure Load Balancer, двойной стек, общедоступный IP-адрес, встроенная поддержка Ipv6, мобильное устройство, Интернет вещей"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3abd47460999f7b059469a58a59a3e297e88effb
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Создание внешняя Подсистема балансировки нагрузки с IPv6 в диспетчере ресурсов Azure с помощью Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Интерфейс командной строки Azure](load-balancer-ipv6-internet-cli.md)
> * [Шаблон](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer является балансировщиком нагрузки 4-го уровня (TCP, UDP). Подсистемы балансировки нагрузки обеспечивают высокую доступность путем распределения входящего трафика между экземплярами службы работоспособности в облачных службах или виртуальных машин в набор балансировки нагрузки. Подсистемы балансировки нагрузки могут также предоставлять эти службы на несколько портов или несколько IP-адресов.

## <a name="example-deployment-scenario"></a>Пример сценария развертывания

На следующей схеме показана решение, которое развертывается с помощью шаблона, описанных в этой статье по балансировке нагрузки.

![Сценарий использования балансировщика нагрузки](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

В этом случае создайте следующие ресурсы Azure:

* Две виртуальные машины (VM)
* Для каждой виртуальной Машины с адресами IPv4 и IPv6, назначенный виртуальном сетевом интерфейсе
* Внешняя Подсистема балансировки нагрузки с IPv4 и IPv6 общедоступный IP-адрес
* Группу доступности, который содержит две виртуальные машины
* Два загрузить балансировки правила сопоставления общедоступные VIP-адреса в частной конечные точки

## <a name="deploy-the-solution-by-using-azure-cli"></a>Развертывание решения с помощью Azure CLI

Ниже показано, как создать внешняя Подсистема балансировки нагрузки с помощью диспетчера ресурсов Azure с помощью Azure CLI. С помощью диспетчера ресурсов Azure, создания и настройки каждого объекта отдельно и поместите их друг с другом для создания ресурса.

Чтобы развернуть подсистему балансировки нагрузки, создайте и настройте следующие объекты:

* **Интерфейсный IP-конфигурации**: содержит общие IP-адреса для входящего сетевого трафика.
* **Пул адресов серверной части**: содержит сетевых интерфейсов (NIC) для виртуальных машин для получения сетевого трафика из подсистемы балансировки нагрузки.
* **Правила подсистемы балансировки нагрузки**: содержит правила, которые сопоставляют открытый порт в подсистеме балансировки нагрузки с портом в пул адресов серверной части.
* **Правила NAT для входящих подключений**: содержит сетевой адрес преобразования Сетевых адресов правила, которые сопоставляют открытый порт в подсистеме балансировки нагрузки к порту для конкретной виртуальной машины из пула адресов серверной части.
* **Проверяет**: содержит проверках работоспособности, которые используются для проверки доступности экземпляров виртуальных машин из пула адресов серверной части.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Настройка среды Azure CLI для использования диспетчера ресурсов Azure

В этом примере вы запускаете средства Azure CLI в окне команд PowerShell. Чтобы повысить удобочитаемость и повторного использования, использовать возможности для создания сценариев PowerShell, не командлетов Azure PowerShell.

1. Если ранее не пользовались Azure CLI, см. раздел [установить и настроить Azure CLI](../cli-install-nodejs.md) и следуйте инструкциям до того момента, где выбирается учетная запись Azure и подписки.

2. Чтобы переключиться в режим диспетчера ресурсов, запустите **azure конфигурации режима** команды:

    ```azurecli
    azure config mode arm
    ```

    Ожидаемые выходные данные:

        info:    New mode is arm

3. Войдите в Azure и получить список подписок:

    ```azurecli
    azure login
    ```

4. В командной строке введите свои учетные данные Azure:

    ```azurecli
    azure account list
    ```

5. Выберите подписку, которую требуется использовать и запишите идентификатор подписки для использования на следующем шаге.

6. Настройка переменных PowerShell для использования с командами Azure CLI.

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

2. Создайте подсистемы балансировки нагрузки:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Создайте виртуальную сеть:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. В этой виртуальной сети создайте две подсети:

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Создание общедоступных IP-адресов для интерфейсного пула

1. Настройка переменных PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Создайте общедоступный IP-адрес пула IP-интерфейса:

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Подсистемы балансировки нагрузки использует общедоступный IP-адрес домена метку в качестве его полное доменное имя (FQDN). В этом заключается отличие от классического развертывания, при котором в качестве полного доменного имени балансировщика нагрузки используется имя облачной службы.
    >
    > В этом примере используется полное доменное имя *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Создание интерфейсного и внутреннего пулов

В этом разделе создайте следующие пулы IP-адресов:
* Внешний пул IP-, получающий входящего сетевого трафика в подсистеме балансировки нагрузки.
* Пул IP-серверной части, где переднего плана пула отправляет трафик с балансировкой нагрузки.

1. Настройка переменных PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Создание интерфейса пула IP- и связать его с общедоступный IP-адрес, созданный в предыдущем шаге и Подсистема балансировки нагрузки.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Создайте пробы, правил NAT и правила подсистемы балансировки нагрузки

В этом примере создаются следующие элементы:

* Правило проверки на наличие подключения к TCP-порт 80.
* Правило NAT для перевода весь входящий трафик через порт 3389 на порт 3389 для протокола удаленного рабочего СТОЛА.\*
* Правило NAT для перевода весь входящий трафик через порт 3391 на порт 3389 для протокола удаленного рабочего стола (RDP).\*
* Правило подсистемы балансировки нагрузки для балансировки весь входящий трафик через порт 80 для порта 80 на адреса из пула серверной части.

\*Правила преобразования сетевых адресов связаны с определенным экземпляром виртуальной машины за подсистемой балансировки нагрузки. Сетевой трафик, поступающий в порт 3389, отправляется для конкретной виртуальной машины и порта, связанный с правилом NAT. Для правила NAT необходимо указать протокол (UDP или TCP). Оба эти протокола нельзя назначить тот же порт.

1. Настройка переменных PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Создадим пробу.

    В следующем примере создается проверки зонда TCP для подключения к серверной части TCP-порт 80 каждые 15 секунд. После двух последовательных сбоев она помечает внутренний ресурс как недоступные.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Создание правила для входящих подключений NAT, поддерживающих соединения протокола удаленного рабочего СТОЛА к серверным ресурсам:

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Создайте правила подсистемы балансировки нагрузки, отправлять трафик к различным портам серверной части, в зависимости от внешнего интерфейса, получившего запрос.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Проверьте параметры:

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

Создать сетевые адаптеры и связать их с правила NAT, правила подсистемы балансировки нагрузки и зонды.

1. Настройка переменных PowerShell:

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

2. Создайте сетевую КАРТУ для каждой серверной части и добавьте конфигурации IPv6:

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Создать ресурсы виртуальной Машины сервера и присоединить каждый сетевой Адаптер

Чтобы создать виртуальные машины, необходима учетная запись хранения. Для балансировки нагрузки виртуальные машины должны входить в группу доступности. Дополнительные сведения о создании виртуальных машин см. в разделе [Создание ВМ Azure с помощью PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Настройка переменных PowerShell:

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
    > В этом примере используется имя пользователя и пароль для виртуальных машин в виде открытого текста. Были предусмотрительны при использовании эти учетные данные в виде открытого текста. Это более безопасный способ обработки учетных данных в PowerShell, в разделе [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) командлета.

2. Создайте набор учетной записи и доступности хранилища.

    При создании виртуальных машин, можно использовать существующую учетную запись хранения. Создайте новую учетную запись хранения с помощью следующей команды:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Создайте группу доступности.

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Создание виртуальных машин с связанные сетевые адаптеры:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-cli.md)  
[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)  
[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
