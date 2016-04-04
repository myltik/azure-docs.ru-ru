<properties 
   pageTitle="Управление группами безопасности сети с помощью интерфейса командной строки Azure в Resource Manager | Microsoft Azure"
   description="Дополнительные сведения об управлении существующими группами безопасности сети с помощью интерфейса командной строки Azure в Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="telmos" />

# Управление группами безопасности сети с помощью интерфейса командной строки Azure

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Извлечение информации

Вы можете просмотреть существующие группы безопасности сети, получить правила для существующей группы и узнать, с какими ресурсами она связана.

### Просмотр существующих групп безопасности сети

Чтобы просмотреть список групп безопасности сети в определенной группе ресурсов, выполните команду `azure network nsg list`, как показано ниже.

	azure network nsg list --resource-group RG-NSG

Ожидаемые выходные данные:

	info:    Executing command network nsg list
	+ Getting the network security groups
	data:    Name          Location
	data:    ------------  --------
	data:    NSG-BackEnd   westus
	data:    NSG-FrontEnd  westus
	info:    network nsg list command OK
		 
### Перечисление всех правил для группы безопасности сети

Чтобы просмотреть правила группы безопасности сети с именем **NSG-FrontEnd**, выполните команду `azure network nsg show`, как показано ниже.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

Ожидаемые выходные данные:
	
	info:    Executing command network nsg show
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Name                            : NSG-FrontEnd
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    Tags                            : displayName=NSG - Front End
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
	data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

>[AZURE.NOTE] Можно также использовать `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` для получения списка правил из группы безопасности сети **NSG-FrontEnd**.

### Просмотр связей для группы безопасности сети

Чтобы просмотреть, с какими ресурсами связана группа безопасности сети **NSG-FrontEnd**, выполните команду `azure network nsg show`, как показано ниже. Обратите внимание, что единственная разница — использование параметра **--json**.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

Найдите свойства **networkInterfaces** и **subnets**, как показано ниже:

	"networkInterfaces": [],
	...
	"subnets": [
		{
			"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
		}
	],
	...

В приведенном выше примере группа безопасности сети не связана с сетевыми адаптерами, но связана с подсетью **FrontEnd**.

## Управление правилами

Можно добавлять правила для существующей группы безопасности сети, изменять существующие правила и удалять их.

### Добавление правила

Чтобы добавить правило, разрешающее **входящий** трафик через порт **443** с любого компьютера в группу безопасности сети **NSG-FrontEnd**, выполните команду `azure network nsg rule create`, как показано ниже.

	azure network nsg rule create --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--description "Allow access to port 443 for HTTPS" \
		--protocol Tcp \
		--source-address-prefix * \
		--source-port-range * \
		--destination-address-prefix * \
		--destination-port-range 443 \
		--access Allow \
		--priority 102 \
		--direction Inbound		

Ожидаемые выходные данные:

	info:    Executing command network nsg rule create
	+ Looking up the network security rule "allow-https"
	+ Creating a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : *
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule create command OK

### Изменение правила

Чтобы изменить ранее созданное правило, разрешающее входящий трафик только из **Интернета**, выполните команду `azure network nsg rule set`, как показано ниже.

	azure network nsg rule set --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--source-address-prefix Internet

Ожидаемые выходные данные:

	info:    Executing command network nsg rule set
	+ Looking up the network security group "NSG-FrontEnd"
	+ Setting a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : Internet
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule set command OK

### Удаление правила

Чтобы удалить созданное выше правило, выполните команду `azure network nsg rule delete`, как показано ниже.

	azure network nsg rule delete --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--quiet

>[AZURE.NOTE] Параметр **--quiet** гарантирует, что удаление не потребуется подтверждать.

Ожидаемые выходные данные:

	info:    Executing command network nsg rule delete
	+ Looking up the network security group "NSG-FrontEnd"
	+ Deleting network security rule "allow-https"
	info:    network nsg rule delete command OK

## Управление связями

Группу безопасности сети можно связать с сетевыми адаптерами и подсетями. Можно также отменить связь группы безопасности сети с любым ресурсом.

### Связывание группы безопасности сети с сетевым адаптером

Чтобы связать группу безопасности сети **NSG-FrontEnd** с сетевым адаптером **TestNICWeb1**, выполните команду `azure network nic set`, как показано ниже.

	azure network nic set --resource-group RG-NSG \
		--name TestNICWeb1 \
		--network-security-group-name NSG-FrontEnd

Ожидаемые выходные данные:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Looking up the network security group "NSG-FrontEnd"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Отмена связи с сетевым адаптером для группы безопасности сети

Чтобы отвязать группу безопасности сети **NSG-FrontEnd** от сетевого адаптера **TestNICWeb1**, выполните команду `azure network nic set`, как показано ниже.

	azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] Обратите внимание на значение "" (пустое) в параметре **network-security-group-id**. Это позволяет удалить связь с группой. Сделать то же самое с помощью параметра **network-security-group-name** невозможно.

Ожидаемый результат:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Отмена связи с подсетью для группы безопасности сети

Чтобы отвязать группу безопасности сети **NSG-FrontEnd** от подсети **FrontEnd**, выполните команду `azure network vnet subnet set`, как показано ниже.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-id ""

Ожидаемые выходные данные:

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "FrontEnd"
	+ Setting subnet "FrontEnd"
	+ Looking up the subnet "FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:    Type                            : Microsoft.Network/virtualNetworks/subnets
	data:    ProvisioningState               : Succeeded
	data:    Name                            : FrontEnd
	data:    Address prefix                  : 192.168.1.0/24
	data:    IP configurations:
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
	data:
	info:    network vnet subnet set command OK

### Связывание группы NSG с подсетью

Чтобы снова связать группу безопасности сети **NSG-FrontEnd** с подсетью **FrontEnd**, выполните команду `azure network vnet subnet set`, как показано ниже.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-name NSG-FronEnd

>[AZURE.NOTE] Приведенная выше команда работает только потому, что группа безопасности сети **NSG-FrontEnd** находится в той же группе ресурсов, что и виртуальная сеть **TestVNet**. Если группа безопасности сети находится в другой группе ресурсов, необходимо использовать вместо этого параметр **--network-security-group-id** и указать полный идентификатор для группы безопасности сети. Идентификатор можно получить, выполнив команду **azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json** и найдя свойство **id**.

Ожидаемые выходные данные:

		info:    Executing command network vnet subnet set
		+ Looking up the subnet "FrontEnd"
		+ Looking up the network security group "NSG-FrontEnd"
		+ Setting subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
		data:
		info:    network vnet subnet set command OK

## Удаление группы NSG

Группу безопасности сети можно удалить только в том случае, если она не связана с ресурсами. Чтобы удалить группу безопасности сети, выполните следующие действия.

1. Чтобы проверить ресурсы, связанные с группой, выполните команду `azure network nsg show`, как показано в разделе [Просмотр связей для группы безопасности сети](#View-NSGs-associations).
2. Если группа связана с сетевыми адаптерами, выполните `azure network nic set`, как показано в разделе [Отмена связи с сетевым адаптером для группы безопасности сети](#Dissociate-an-NSG-from-a-NIC), для каждого сетевого адаптера. 
3. Если группа связана с подсетью, выполните `azure network vnet subnet set`, как показано в разделе [Отмена связи с подсетью для группы безопасности сети](#Dissociate-an-NSG-from-a-subnet), для каждой подсети.
4. Чтобы удалить группу безопасности сети, выполните команду `azure network nsg delete`, как показано ниже.

		azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

	Ожидаемые выходные данные:

		info:    Executing command network nsg delete
		+ Looking up the network security group "NSG-FrontEnd"
		+ Deleting network security group "NSG-FrontEnd"
		info:    network nsg delete command OK

## Дальнейшие действия

- [Включите ведение журнала](virtual-network-nsg-manage-log.md) для групп безопасности сети.

<!---HONumber=AcomDC_0323_2016-->