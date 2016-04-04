<properties 
   pageTitle="Создание групп безопасности сети с помощью интерфейса командной строки Azure | Microsoft Azure"
   description="Узнайте, как создавать и развертывать группы безопасности сети в режиме ARM с помощью интерфейса командной строки Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Создание групп безопасности сети с помощью интерфейса командной строки Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье описывается модель развертывания с использованием менеджера ресурсов. Вы также можете [создавать группы безопасности сети с помощью классической модели развертывания](virtual-networks-create-nsg-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Для приведенных ниже примеров команд интерфейса командной строки Azure (Azure CLI) требуется уже созданная простая среда по описанному выше сценарию. Чтобы выполнять команды в том виде, в котором они представлены в этом документе, сначала создайте тестовую среду, развернув [этот шаблон](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd). Затем щелкните **Развернуть в Azure**, при необходимости измените значения параметров по умолчанию и следуйте указаниям на портале.

## Создание группы безопасности для подсети переднего плана
Чтобы создать сетевую группу безопасности под названием *NSG-FrontEnd* по описанному выше сценарию, выполните указанные ниже действия.

1. Если вы еще не пользовались интерфейсом командной строки Azure, см. статью [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

2. Выполните команду **azure config mode**, чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

		azure config mode arm

	Ожидаемые выходные данные:

		info:    New mode is arm

3. Выполните команду **azure network nsg create**, чтобы создать сетевую группу безопасности.

		azure network nsg create -g TestRG -l westus -n NSG-FrontEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:    Name                            : NSG-FrontEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

	Параметры
	- **-g (или --resource-group)**. Имя группы ресурсов, в которой будет создана группа безопасности сети. В данном сценарии это *TestRG*.
	- **-l (или --location)**. Регион Azure, в котором будет создана сетевая группа безопасности. В нашем случае это *westus*.
	- **-n (или --name)**. Имя для новой сетевой группы безопасности. В данном сценарии это *NSG-FrontEnd*.

4. Выполните команду **azure network nsg rule create**, чтобы создать правило, которое разрешает доступ к точке 3389 (RDP) из Интернета.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		warn:    Using default direction: Inbound
		info:    Looking up the network security rule "rdp-rule"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp
		-rule
		data:    Name                            : rdp-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 3389
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Параметры

	- **-a (или --nsg-name)**. Имя сетевой группы безопасности, в которой будет создано правило. В данном сценарии это *NSG-FrontEnd*.
	- **-n (или --name)**. Имя нового правила. В нашем случае это *rdp-rule*.
	- **-c (или --access)**. Уровень доступа для правила (Deny или Allow).
	- **-p (или --protocol)**. Протокол для правила (TCP, UDP или «*»).
- **-r (or --direction)**. Направление подключения (Inbound или Outbound).
	- **-y (или --priority)**. Приоритет правила.
	- **-f (или --source-address-prefix)**. Префикс адреса источника в CIDR или использование тегов по умолчанию.
	- **-o (или --source-port-range)**. Исходный порт или диапазон портов.
	- **-e (или --destination-address-prefix)**. Префикс адреса назначения в CIDR или использование тегов по умолчанию.
	- **-u (или --destination-port-range)**. Конечный порт или диапазон портов.	

5. Выполните команду **azure network nsg rule create**, чтобы создать правило, которое разрешает доступ к порту 80 (HTTP) из Интернета.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 80
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Выполните команду **azure network vnet subnet set**, чтобы связать группу безопасности сети с подсетью переднего плана.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -o NSG-FrontEnd

	Ожидаемые выходные данные:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

## Как создавать сетевую группу безопасности для внутренней подсети
Чтобы создать сетевую группу безопасности под названием *NSG-BackEnd* по описанному выше сценарию, выполните следующие действия.

3. Выполните команду **azure network nsg create**, чтобы создать сетевую группу безопасности.

		azure network nsg create -g TestRG -l westus -n NSG-BackEnd

	Ожидаемые выходные данные:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd
		data:    Name                            : NSG-BackEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

4. Выполните команду **azure network nsg rule create**, чтобы создать правило, которое разрешает доступ к порту 1433 (SQL) из подсети переднего плана.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "sql-rule"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule
		data:    Name                            : sql-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 1433
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

5. Выполните команду **azure network nsg rule create**, чтобы создать правило, которое запрещает доступ к Интернету.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n web-rule -c Deny -p * -r Outbound -y 200 -f * -o * -e Internet -u *

	Ожидаемые выходные данные:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : *
		data:    Source Port                     : *
		data:    Destination IP                  : Internet
		data:    Destination Port                : *
		data:    Protocol                        : *
		data:    Direction                       : Outbound
		data:    Access                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Выполните команду **azure network vnet subnet set**, чтобы связать группу безопасности сети с внутренней подсетью.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -o NSG-BackEnd

	Ожидаемые выходные данные:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Setting subnet "BackEnd"
		info:    Looking up the subnet "BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/BackEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : BackEnd
		data:    Address prefix                  : 192.168.2.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

<!---HONumber=AcomDC_0323_2016-->